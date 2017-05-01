---
layout: post
title: Game Design Deep Dive into Legend of Zelda Breath of the Wild
subtitle: Examining the cooking system of BotW
---

#### Note: This post contains moderate (mostly mechanical) spoilers for Legend of Zelda: Breath of the Wild, and requires at least general familiarity with the game.

Cooking in Legend of Zelda: Breath of the Wild[^1] was one of my single favorite
mechanics throughout my 70 hour playthrough. In this article, I'll take a deep
dive into cooking, and hopefully help you understand how this mechanic interacts 
with the game's goals and enriches the experience overall.

---
# Cooking: Development of a mental model

I remember the first time I stepped out into the world from the shrine of
resurrection on the Great Plateau. It was the evening of March 6, 2017, and I
had just torn open the Best Buy box which contained my shiny new Nintendo
Switch. I came home (way too) early from work and plugged the console into my
TV, and booted up Breath of the Wild.

Three minutes later, I stepped out onto the cliff right outside the shrine, and
saw the vast expanse of the world. Out in front of me, Death Mountain and Hyrule
Castle stood ominously in the distance. To the East, a dilapidated Temple of
Time and an old man sitting in the small indentation under a hill. And from this
very first moment, cooking was an integral part of the game. I remember walking
up to the old man, picking up the baked apple in front of him to trigger his
first cutscene, and I was off. The man dropped a couple of hints that apples
were not only edible, but cookable, by letting me know that dropping them near a
fire bakes them and makes them more nutritious.

I think the fact that this interaction is literally the first you experience
helps to show just how incredibly important the cooking mechanic is to this
game. Instead of introducing the combat mechanics or even the plot, the
designers of BotW picked *cooking*. The first question I have to ask in this
analysis is why, because at first blush there's no clear reason that cooking is
so vitally important that it trumps everything else in the game for that initial
impression. But as it turns out, the answer is pretty obvious when taken in the
context of BotW as a whole: the game was meant to be a huge departure from the
linear, constrained Zeldas of Christmas Past, into a sort of reinvention of the
first incarnation of the franchise. Rather than limiting you to a thrilling, but
ultimately (nearly) completely linear story[^2], BotW deliberately attempts to
shatter linearity in every possible way, and give the player complete agency in
how they approach every problem in the game. This is reminiscent of the first
NES version of The Legend of Zelda, in which you are completely able to explore
the entire world from the very beginning of the game, and only through
exploration do you learn the optimal order of navigating its myriad dungeons.
Though very different in their scope, 1987 and 2017 have much more in common
than initially meets the eye, and much of that similarity comes down to the
creative ways in which players approached tackling their grand challenge[^3]

It turns out that cooking, as a mechanic that interacts with the rest of the
game, is a perfect avenue for expressing that creative freedom.


## The freedom to choose 
Let me walk you through the thought process of early Gabe-Link throughout the
beginning (first, say, 10 hours) of the game:
1. I need a recipe that will grant me heat resistance.
2. Where can I get ingredients that grant me heat resistance? Hm, since cold is
   the opposite of heat, a cold area might have what I'm looking for! I'll head
   to the mountains.
3. I got this blue Lizalfos tail, and it says in its description that it is icy
   and can provide protection from the heat. I'll try cooking it with a Hylian
   Mushroom (a basic HP restoration item) and see what happens!
4. Whoops, I got junk. Hmmm, maybe that was due to the mixture of monster bits
   and regular food. Lemme try with a Bokoblin Horn.
5. Success! I got a chilly elixir. Okay, so it looks like monster parts are 
   separate from food, and you get elixirs from monster parts which don't
   restore HP.

This illustrates a couple of important points about how cooking interacts with
other systems in Breath of the Wild: first of all, I chose to make a chilling
item because I didn't have any other way to cross the desert at that time; I
would die from the heat had I tried to cross without other preparation. And this
reveals the intentional design decision to make armor special effects and
foodcraft special effects *the same system*. This allows the player to choose
along which avenue they will pursue the desired effect: will they go for the
simpler, but much more temporary, solution in food, or the more difficult but
more permanent effects of armor. Players could even mix and match these effects
by, for example, getting increased defense from their armor, and thunder
resistance from a food effect.

The mix-and-match nature of this system is key to early game flexibility in the
choices players make: since it's not guaranteed that a player might have the
particular armor set demanded for a quest or situation, a suitable food item can
be substituted. And since food is relatively easy to come by (simply through
exploration and foraging), it provides a viable alternative in many situations
early-game. It's also very clear that the designers of the game intend the
player to make these discoveries on their own: on the way to Death Mountain, the
Goron domain, for example, you'll come across a merchant at a stable that gives
you a free fireproof elixir and offers to sell you another one at a rather steep
price. If you've been keeping up on Rupee farming, it's very easy to buy
whatever supplies you need from them, but if you haven't, you can simply examine
the recipe of the elixir (all food items allow this) and collect the same
ingredients to make more. Without these elixirs, you would die very early on in
your quest up the mountain, and indeed the game seems almost designed so that
players will attempt the trek unprepared, die, and be forced to reevaluate.

There are many environmental factors all throughout the game that allow this
discovery, and many of them have a similar guidepost to show you that it is
indeed possible to traverse this challenge, if only you are willing to prepare
properly. So, given that it integrates so incredibly well with the other
mechanics and goals of the game, does it have any faults? Where does it fail?

## It *sucks* as a standalone system.

Despite the fact that I loved the cooking mechanic within BotW, I am also aware
of the fact that it is less complex and interesting than meets the eye, and
ultimately it isn't a design to be emulated.

How can I hold both of these views simultaneously? Through *black-boxing*. I 
appropriate a term often used in system design to describe this mechanic because
it's a great description of how I feel about it as a designer:

#### A black box is a component of a system whose internal structure and operation are hidden from view; the rest of the system is only exposed to what the design of the black box dictates, and all other components are kept secret.

Taken as a black box, the cooking system interacts with the armor system and
buffing[^4] system of the game; it provides an alternative to armor to achieve
buffs to stats and helpful effects. And it does so marvelously insofar as it
provides a balanced interaction which rewards the player for their
experimentation and creativity.

But, the system itself is actually pretty awful. My first realization of this
fact was during the completion of a side quest in which you have to gather the
ingredients for a particular dish that I had never cooked. After experimenting
and searching for a while, I decided that this would be the moment that I went
online to find the answer. Now, this was about 40-45 hours into the game for me,
so I felt as though I had "cracked" most of the systems in the game and I didn't
feel overly upset about "spoiling" the game for myself. After all, I had gotten
all the Divine Beasts, upgraded all my runes, and begun the arduous quest of
preparing for the final fight with Calamity Ganon. Of course, while looking for
this recipe, I stumbled upon a wiki page listing all the recipes in Breath of 
the Wild, which was being compiled by the community. Flipping through this page
to find the recipe I needed, the bottom fell out of the cooking system as I
started to unravel how it *really* worked.

The problem with mental models is that they're purely mental: we build them up
to describe opaque mechanisms around us so that we can make better predictions
about what will happen next. I was aware that my mental model of cooking wasn't
perfect, because my predictions didn't always work, but I had figured that I
understood them well enough to boil them down into a few basic rules:
* Monster parts and food are separate categories (this was true)
* There are different grades of "generic" monster parts and food, such as meat or monster fangs (also true)
* You have to combine some "generic" ingredients with some status effecting ingredients using some complicated formula and some randomness to build the recipe you wanted (this was *dead wrong*)

The last point here is where the game breaks down big time for me. I had assumed
that, for example, in order to make an item that provided maximal defense boost,
I would need a couple of different high quality defensive items mixed with a
high quality meat and maybe an egg or something, and that there was some 
randomness or other "magic" that I didn't yet understand which was causing
unexpected results (oftentimes, I would get only a moderate boost to defense 
instead of a large one). I thought I was pretty close to figuring it all out,
and I was extremely impressed at the complexity and nuance of the system.

As it turns out, heart recovery and effects are completely separate from each
other and have no interactions. The effect you get is dependent strictly on the
number and quality of stat-boosting items you throw in, and the heart
restoration items are simply added on top of whatever heart restoration the stat
items give you. For example, putting 4 warm safflina's in a pot gave you maximum
cold resistance and a certain number of hearts (I think it was 4, but I don't
remember exactly). Then, if you threw a coconut in there, it would add something
like 8 hearts of restoration on top of that. That was it.

This meant a couple of things: first of all, the recipe system was useless.
There was no reason to learn the more complex recipes since simple meat and
mushroom skewers could be optimized to give you the maximal heart restoration
for minimal effort, and high-quality elixirs could be made just with monster
part drops. Making seafood paella or fruitcake is completely pointless since
there are other ways to use those ingredients that get you better restoration.
Second of all, the system is way, *way*, **way**[^5] less complicated than I
imagined, and than my mental model had led me to understand. Now, this is always
a risk in designing opaque systems: once your player "cracks" the system, will
they still appreciate it? Will they still enjoy the game, even when they can
"solve" each and every encounter optimally? In the case of BotW the rest of the
game is strong enough for me, but it took me a few days' wrangling with this new
information to come to that conclusion.

There is also, of course, something to be said for the fact that it took me
going online to figure out how this system works. It's entirely possible that
the inner workings of cooking might have eluded me through my whole playthrough,
and I don't know if I would have responded differently had I cracked the puzzle
myself, but as it stands I do wish the system had as much depth as I imagined it
to.

# Lessons

A game like Legend of Zelda: Breath of the Wild[^6] is a master class in game
design from top to bottom. Every system interlocks perfectly at their
boundaries, even if internally they are less clean and interesting than they
appear externally. It's a testament to the idea that game design is as much a
systems design problem as it is an artistic pursuit. 

In the case of the cooking system specifically, I think the most important
specific takeaway is that there's an inherent danger in developing an opaque
system: you have to ensure that the mechanic is still fun when it's laid bare. I
think a good way to test this in the real world would be to explain a mechanic
in as much detail as you can to one of your play testers and see if they still
enjoy interacting with it, or if they just default to the "optimal" option and
move on, ignoring it completely. On the other hand, opaque systems (or
"black-boxes") are an amazing way to add depth to a game where it didn't
previously exist: simply by hiding a lot of the intricacies of your mechanic,
you allow people to develop their own mental image of how it probably works.
This will almost always be much more interesting than what you design, and so
long as your mechanic is either
1. opaque enough that your players generally won't figure it out, or
2. still fun enough to continue interacting with after the optimal solution has
   been found.

This is an incredibly difficult balance to strike, but I think that one thing
that would have helped Breath of the Wild in this case would have been giving
complex recipes such as pies, stews, paellas, etc. some sort of inherent extra 
value (maybe they could have special hidden status effects not available through 
regular food, or maybe they could restore far more hearts than any of the
alternative recipes). This change would have still incentivized experimentation
after discovering the "guts" of the system.

It is also important to clearly define the boundaries and "interface" of each
mechanic so that if one ends up failing the player for some reason, it doesn't
bring down the rest of the game. Designing modularly will make some games appear
choppy, that is true, but I think that by focusing on making each individual
part good at the boundaries, any one part can fail without taking down the rest
of the experience with it. This shows in BotW since I still enjoy the game, even
with my issues with this core mechanic! Since it's not intertwined intrinsically
with every other system in the game (it only has a one-way interaction to stats;
since armor provides an alternative avenue there isn't as much damage to the
stats mechanic if cooking is broken).

All in all, Breath of the Wild is definitely worth your time, if only as a case
study in modular design and proper construction of a non-linear experience. You
should definitely still play it even if what I mention in this article bothers
you. And for those of you that have made a lot of progress or even finished the
game, try analyzing this or another mechanic closely, and see if you can draw
some conclusions about how it's effective and where it falls short. I find that
this process makes me a much better designer and much more cognizant of the
intentional decisions that go into designing a game.

---
[^1]: Copyright and Trademark and whatnot Nintendo of America, Europe, and/or Japan depending on where you live. I don't like getting sued.
[^2]: Majora's mask notwithstanding. That game is a case study all its own in nonlinear narrative structure, and I can't go into too much detail here. Suffice to say, it's the exception rather than the rule of that era.
[^3]: It's worth pointing out that at the time of its release, The Legend of Zelda (1987) was easily one of the biggest and most ambitious games to date for a home console.
[^4]: a term used in game design to denote a positive improvement to a character's stats.
[^5]: way, way, way
[^6]: (c), TM, etc. Nintendo of America, Europe, and/or Japan
