---
layout: distill

title: Damage pipeline 
description: Extending Unreal Engine's Gameplay Ability System to implement a damage pipeline capable of supporting Destiny 2's complex damage calculations, weapon perks and abilities.
importance: 2
img: /assets/img/devpost_covers/DamagePipeline.png
category: density

tags: distill formatting
giscus_comments: true
date: 2021-05-22
featured: true

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc: true


---

## Introduction

This article explores how the damage pipeline used in the project works and how it uses the Gameplay Ability System to do so. It also gives some examples of its use to apply weapon and ability damage with some custom perks and effects found in Destiny 2 such as Rampage and Echo of Undermining vortex grenades.

<div class="l-body">
        {% include video.html path="assets/video/DamagePipeline.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
</div>
<div class="caption">
    Example video of using a weapon with the damage perk Rampage and a Vortex grenade that applies a 15% weaken effect to targets <a href="#example-uses-of-the-damage-pipeline">(Detailed explanation below)</a>. Kills with a weapon also give 20% grenade energy back.
</div>



### Buffs, debuffs, weapon perks and damage in Destiny 2

Destiny 2 is a complex first person shooter game that implements many elements from rpg and mmo games. Characters in the game have a wide variety of abilities and weapons and every one of these can have many different perks and modifications that apply effects on the player and enemies from simple stat and damage buffs to crazier effects that heavily alter the players gameplay. 

The damage system in Destiny 2 also supports different types of buffs and debuffs some of which stack and some only use the highest effect applied. Weapons and abilities can also deal elemental damage of which there are 3 in Destiny 2 (_Arc_, _Solar_ and _Void_). Any of the buffs and debuffs in the game can have the possibility of only affecting a specefic damage type from a specific source, eg: a player could have an effect that gives an increase in damage to _Void_ grenade abilities.

As a general rule however Destiny 2 has 4 main types of buffs/debuffs:

- *Empower* effects buff the damage dealt by weapons and only the highest empower buff applied affects the player.

- *Surge* effects buff the damage dealt by weapons and are tied to a specific element and only the highest empower buff applied affects the player.

- *Weaken* effects increase the damage taken by a target from all sources and only the highest weaken effect applied affects the target.

- Any other type of damage increase that doesnt belong to the above such as those originating from weapon perks stacks can be stacked infinately.

In order to  to simulate this behaviour in UE5, a custom damage pipeline that uses the Gameplay Ability System was created.


## GAS Damage pipeline
The damage pipeline is the series of functions and abilities that handle all of the buffs debuffs and effects that can alter the damage dealt by the player's abilities and weapons. The concept of using a damage pipeline to handle damage in a modular and extensible way was inspired by this excellent post: [A Gameplay Framework with GAS based on Risk of Rain 2](https://www.vitorcantao.com/post/gas-gameplay-framework/) by Vitor Cantão in which he reproduces the game Risk of Rain 2's gameplay systems using GAS. 

The damage pipeline created for Density processes all damage using mostly meta-attributes. These are altered by gameplay effects and abilities by responding to gameplay events at different points in the damage pipeline. Each character that can be damaged has the following attributes and meta-attributes (Meta attributes are not replicated and they are manually reset to their base value of 1.0 after each execution of the damage pipeline. See [GAS Documentation](https://github.com/tranek/GASDocumentation#433-meta-attributes) for more info on meta attributes):



| **Attribute**    	| **Description**                                                                                                                            	|
|------------------	|--------------------------------------------------------------------------------------------------------------------------------------------	|
| Health/MaxHealth 	| The characters health. When reaching 0 it activates OnKill and OnDeath effects on the instigator and itself respectively                   	|
| Shield/MaxShield 	| The characters shield. If its > 0 then damage will first apply to the shield before health. On reaching 0 activates OnShieldBroken effects 	|



| **Meta Attribute**       	| **Description**                                                                                                                                                                                                               	|
|--------------------------	|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| OutgoingDamage           	| Base damage of the ability, can be increased by infinitely stacking multipliers in _ModifyOutgoingDamage_ events.                                                                                                             	|
| OutgoingEmpowerBuff      	| Separate damage bucket buff. Only the highest applied EmpowerBuff on a character should modify this value _ModifyOutgoingDamage_ events.                                                                                      	|
| OutgoingSurgeBuff        	| Separate damage bucket buff. Only the highest applied SurgeBuff on a character should modify this value in _ModifyOutgoingDamage_ events.                                                                                     	|
| IncomingDamage           	| Initial incoming damage on the target. Usually equal to `OutgoingDamage` * `OutgoingEmpowerBuff` * `OutgoingSurgeBuff` from the instigator.                                                                                   	|
| IncomingWeakenDebuff     	| Amplifies the damage taken. Only the highest debuff should modify this value in _ModifyIncomingDamage_ events.                                                                                                                	|
| IncomingDamageResistBuff 	| Any damage resistance buffs that the target has should be multiplied to this value in abilities responding to the _ModifyIncomingDamage_ event.                                                                               	|
| DamageTaken              	| Damage applied to target, usually equal to `IncomingDamage` * `IncomingWeakenDebuff` * `IncomingDamageResistBuff`. The targets health and shield is reduced in the _PostGameplayEffectExecute()_ function based on this value 	|



When executing the damage pipeline, the main flowchart of operations is the following:

<div class="l-page-outset">
  {% include figure.html path="assets/img/DamagePipelineDevpost/PipelineFlowchart.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Damage pipeline flowchart
</div>

The main steps explained are the following:

1. The first step is applying the [Damage Container](#gas-damage-container). As explained more in detail below it contains the `BaseDamageEffect`, the `DamageApplicationEffect` and a _GameplayTagContainer_ for the abilities `DamageType`.
2. The `BaseDamageEffect` is applied to the instigator, overriding the `OutgoingDamage` attribute with the abilitiy's base damage.
3. The _ModifyOutgoingDamage_ event is fired on the instigator's `ASC`(Ability System Component). Any ability meant to modify `OutgoingDamage`, `OutgoingEmpowerBuff` or `OutgoingSurgeBuff` should respond to this event and apply its effects based on the abilities `DamageType` tags.
4. Process the `OutgoingDamage`, `OutgoingEmpowerBuff` or `OutgoingSurgeBuff` attributes by multiplying them together, then setting the value as the targets `IncomingDamage`.
5. Modify the incoming damage based on the target data, eg: If the `DamageType` tag is for a weapon and the weapon can critically strike, if the target hit is a weakspot multiply by the weapon's `CritDamageModifier` attribute.
6. The _BeforeDamage_ event is fired on the targets's `ASC`. Any ability meant to apply an effect to the target before damage is calculated should respond to this event and apply its effects based on the abilities `DamageType` tags.
7. The _ModifyIncomingDamage_ event is fired on the targets's `ASC`. Any ability that modifies the `IncomingDamage`, `IncomingWeakenDebuff` or `IncomingDamageResistBuff` should respond to this event and apply its effects based on the abilities `DamageType` tags.
8. The `DamageApplicationEffect` is applied to the target. This `GE` calculates the final damage and sets the target's `DamageTaken` attribute. The _PostGameplayEffectExecute()_ will then handle applying that attribute to the target's `Shield` and  `Health` attributes as well as triggering the _OnKill_, _OnShieldBroken_ and _OnDeath_ events on the instigator, and targets respectively.
9. Send the _OnHitEffect_ event on the instigator's `ASC` so that any on-hit abilities can trigger by activating with this event.
10. If all targets for this container haven't been processed, go back to step 4.


### GAS Damage Container

The concept of damage containers are based on Epic’s Action RPG Sample `FGameplayEffectContainer` and Vitor Cantão' post. A damage container provides some quality of life features such as automatically handling the damage pipeline in its entirety and simplifying adding damage to projectile based abilities. Every Gameplay Ability deriving from `FDensGameplayAbility` has access to the `ApplyDamageContainer` variety of nodes, which should be used to apply the abilities damage to targets. The `FDensDamageContainer` class contains:

- `BaseDamageEffect` and `DamageApplicationEffect` classes which are Gameplay Effects.
- `DamageType` _GameplayTagContainer_ which determines what kind of damage to apply. If for example the damage of a grenade ability has the tag `DamageType.Ability.Grenade`, this abilities damage will not get increased by effects that buff weapon damage with tag `DamageType.Weapon`.
- `bDamageCanCrit` _boolean_ determines wether the ability should ever apply a crit modifier buff to the ability. For example most abilities and some weapons in Destiny 2 such as fusion rifles do not receive a bonus for hitting an enemy's weakspot.

Gameplay abilities derived from `FDensGameplayAbility_Damage` have a default `FDensDamageContainer` that can be edited in the class defaults, that gets applied by when calling the `ApplyDamageContainer` node. However as an example, if one needs to send a damage container to a grenade ability, which applies the damage in a projectile separate from the original ability, a _DamageContainerSpec_ can be created from a container then passed as an argument to the projectile and applied later.

<div class="l-body">
  {% include figure.html path="assets/img/DamagePipelineDevpost/DamageContainerBP_Native.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Native use of damage container from WeaponFire ability
</div>


<div class="l-body-outset">
  {% include figure.html path="assets/img/DamagePipelineDevpost/DamageContainerBP_External.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    Manually creating DamageContainer to use later in a projectile ability. Green comment represents the projectile's separate blueprint.
</div>



### Custom Gameplay Effect Context

In order to send around important data around multiple gamplay abilities such as the abilities `DamageType` tags, a custom Gamplay Effect Context was used. ([Here is a great tutorial on how to do this](https://www.thegames.dev/?p=62)). The fustom `FDensGameplayEffectContext` contains a `GameplayTagContainer` to hold the `DamageType` tags as well as some other variables such as `bIsCriticalHit` and `bIsCriticalHit` which can be used to send information to Gameplay Cues about the damage effect for visual feedback later on. 

When firing any of the events in the damage pipeline above, the custom gameplay effect context is also sent along with it in order to be able to access the `DamageType` tags to check if an effect should apply to a damage type or not.




## Example uses of the Damage Pipeline

### Rampage weapon perk damage buff example

<div class="l-body">
        {% include video.html path="assets/video/RampageExample.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
</div>
<div class="caption">
    Rampage perk gives stacking damage buff when getting kills with a weapon.
</div>


Rampage is a very popular weapon perk inside of Destiny 2. It is a perk that gives a stacking damage buff when the player defeats an enemy with the weapon that has the perk. The buff lasts for 4.5 seconds and if the user gets another kill within that time the Rampage stacks increase up to a maximum of 3. If the buff timer runs out the stacks reset to 0.

- Rampage x1 gives a 10% damage increase
- Rampage x2 gives a 21% damage increase
- Rampage x3 gives a 33% damage increase

To recreate this using the Density damage pipeline we need two `GA`, two `GE` and a curve table to hold the damage increases values for each stack:

<div class="l-body">
  {% include figure.html path="assets/img/DamagePipelineDevpost/RampageBP.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>

The `GA_RampagePerk` is added to the player's `ASC` when the player equips a weapon that has the perk on its `AbiltySet`. (The adding of the ability is handled in C++)

The `GA_RampagePerk` is activated by responding to the _OnKill_ event. Then if the `DamageType` tag matches `DamageType.Weapon`, the `GE_RampagePerk` is applied to the player, the ability then checks the number of stacks of `GE_RampagePerk` on the `ASC` and sets the ability level corresponding to the number of stacks.

`GE_RampagePerk` is an duration based `GE` that stacks up to 3 times, and lasts 4.5 seconds. It also grants the player the `GA_RamageBuff` ability. This is the main ability which responds to the _ModifyOutgoingDamage_ event and modifies the `OutgoingDamage` attribute by applying the `GE_RampageBuff` effect. The amount by which the `OutgoingDamage` is multiplied by is extracted from the curve table based on the level of the gameplay effect, which is set to be the same as the ability level set by `GA_RampagePerk`. 

When the player unequips the weapon with the rampage perk, any stacks of `GE_RampagePerk` remaining on the `ASC` are removed (this is done in C++).

<div class="l-page-outset">
  {% include figure.html path="assets/img/DamagePipelineDevpost/GA_RampagePerk.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    GA_RampagePerk (top) and GA_RampageBuff (bottom)
</div>


<div class="l-page-outset">
  {% include figure.html path="assets/img/DamagePipelineDevpost/RampageGE.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    GE_RampagePerk (left) and GE_RampageBuff (right)
</div>



### Echo of undermining grenade perk example

<div class="l-body">
        {% include video.html path="assets/video/UnderminingExample.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
</div>
<div class="caption">
     Grenade applies a 15% debuff to enemies increasing damage dealt to the target from all sources and making the damage numbers yellow for all sources of damage.
</div>


In Destiny 2, Echo of Undermining is a fragment(ability) that applies a 15% weaken debuff to an enemy for 5 seconds after a grenade deals damage to it. Weaken debuffs are a type of non-stackable debuff which means that only the highest percent debuff applied should affect a target.

To recreate this using the Density damage pipeline we need two `GA` and `GE`. First the ability `GA_EchoOfUndermining` is added to the player's `ASC`. This ability is activated by the _BeforeDamage_ event in the damage pipeline. It is a simple ability, first it checks that the instigator is not the same as the target (so that self damage from a grenade wont apply a weaken effect to the insitgator) and it also checks that the damage being dealth is of tag `DamageType.Ability.Grenade`. If these conditions are met, the `GE_GiveWeaken15Buff` is applied to the target for 5 seconds.

`GE_GiveWeaken15Buff` grants the target the `GA_15WeakenDebuff` ability while it is applied and also applies the `Status.Effects.Weakend` tag so that other parts of the game can respond to the target being weakened (such as the HUD damage numbers).

`GA_15WeakenDebuff` is a data-only gameplay ability that derives from `GA_DamageEffect_NonStack`, a template ability that was created to handle non-stacking buffs and debuffs (Empower, Surge and Weaken) applied to characters. `GA_15WeakenDebuff` has the following inputs and the rest is handled by the parent ability:


<div class="l-body">
  {% include figure.html path="assets/img/DamagePipelineDevpost/GA_15Weaken.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    GA_15WeakenDebuff parameters
</div>



`GA_DamageEffect_NonStack` first checks the current value of the respective buff attribute (weaken in this case) and then only applies the effect value if the _StatusEffectPercent_ variable is higher than the current attribute value. The value of the applied weaken is set using a SetByCaller magnitude on the `GE_WeakenDebuff` effect which overrides the value of the `IncomingWeakenDebuff` attribute.


<div class="l-page-outset">
  {% include figure.html path="assets/img/DamagePipelineDevpost/GANonStack.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>
<div class="caption">
    GA_DamageEffect_NonStack ability
</div>





[<i class="fas fa-arrow-up fa-sm"></i> Back to top](#)


[<i class="fas fa-undo fa-sm"></i> Back to project](/portfolio/density/)     

