---
layout: post
title:      "Learning Object Relationships in Ruby with Pokémons"
date:       2018-11-13 23:07:44 -0500
permalink:  learning_object_relationships_in_ruby_with_pok_mons
---


After spending the better part of my Sunday trying to understand Object Relationship, and still not getting anywhere near a lightbulb moment (not even a dim one), I travelled across the Internet land for the very best explanation, searching far and wide.

That’s when I stumbled upon this short post by [Han Lee](https://medium.com/@tpstar), where he described [object relationship using Pokémon](https://medium.com/@tpstar/object-relationships-in-ruby-explained-by-relationships-in-pokemons-8dd64c8af773). It was so good! But so short! 🤕

So I decided to take it further. In essence, object relationship is a concept used to illustrate how different instances of our classes can interact with one another, as with real-life situations. Our class instances can also be referred to as **models**. There are several basic ways in which models relate to one another—in this case, I’m going to practice the `belongs to` and the `has many` relationships.

In the world of Pokémons, you can `have many` Pokémons (e.g. Pikachu, Eevee, etc.). In a reciprocal manner, those Pokémons `belong to` you. And because you love Pokémons and you want to teach them more, each of your Pokémon also `has many` moves and those moves `belong to` each Pokémon with a specific type (e.g. electric, grass, etc.).

So let’s set up our three separate classes: `Trainer`, `Pokemon`, and `Move`.

As we commit to become the very best trainer, like no one ever was, Prof. Oak called us to his lab and asked for our `trainer_name`. At this point, we still have no Pokémons and our array starts empty. But no worries! We’ll earn our first starter Pokémon in no time!

```
class Trainer
  attr_accessor :trainer_name, :pokemons

  def initialize(trainer_name)
    @trainer_name = trainer_name
    @pokemons = []
  end
end
```

Alright. Now, let’s set up our Pokémons! Since our Pokémons `belong to` us, we are assigning `attr_accessor :trainer` at the beginning of the class. Next, our class Pokemon is responsible for recording all the Pokémons that we are going to encounter, and push them into our global variable `@@pokedex`. When we encounter a new Pokémon, we also need to record its `pokemon_name` and `pokemon_type`, among other things, but we’ll keep it to these two for simplicity! And because we’re still a Lv 1 trainer, most of our Pokémons have not been taught any special moves yet, and our instance variable `pokemon_moves` initiates to an empty array.

```
class Pokemon
  attr_accessor :pokemon_name, :pokemon_type, :pokemon_moves, :trainer
  
    @@pokedex = []

  def initialize(pokemon_name, pokemon_type)
    @pokemon_name = pokemon_name
    @pokemon_type = pokemon_type
    @@pokedex << self
    @pokemon_moves = []
  end
end
```

We’re also going to create a `Move` class, not to complicate things, but because we promised to teach our best friends a few chops and kicks! Since we can only teach certain moves to Pokémons of a corresponding type, we also need to define `pokemon_type` when we’re creating a new instance variable `move`.

```
class Move
  attr_accessor :pokemon_type, :move

  def initialize(pokemon_type, move)
    @pokemon_type = pokemon_type
    @move = move
  end
end
```

Now, we’re done setting up! Let’s go back to our `Trainer` class. We’ve chosen Pikachu as our first starter, and we need a method within our class to add Pikachu onto our slots. Our `#add_pokemon` method will, *well*, add our Pokémon for us. And by assigning `self` to our `pokemon.trainer`, we automatically reciprocates the relationship between new Pokémon and ourselves, the trainer. Lastly, we can also check each Pokémon in our current slots using the `#pokemon_slots` method, which will also helpfully tell us our Pokémon’s type.

```
class Trainer
  attr_accessor :trainer_name, :pokemons

  def initialize(trainer_name)
    @trainer_name = trainer_name
    @pokemons = []
  end

  def add_pokemon(pokemon)
    @pokemons << pokemon
    pokemon.trainer = self        # The added Pokemon belongs to the trainer whom we called #add_pokemon on
  end
  
  def pokemon_slots
    @pokemons.map { |pokemon|
      "#{pokemon.pokemon_name} : #{pokemon.pokemon_type}"
    }
  end
end
```

So, let’s check on our code by calling on our methods!

```
ash = Trainer.new("Ash")
pikachu = Pokemon.new("Pikachu", "electric")
bulbasaur = Pokemon.new("Bulbasaur", "grass")
blastoise = Pokemon.new("Blastoise", "water")

ash.add_pokemon(pikachu)
ash.add_pokemon(bulbasaur)
ash.add_pokemon(blastoise)

ash.pokemon_slots
=> ["Pikachu: electric", "Bulbasaur: grass", "Blastoise: water"]
```

Now, next, after levelling up some, we want to teach our friends some *sick* moves. How do we do that? We already have our `Move` class, which we can use to call `new` moves anytime. What’s next? We probably need to define some methods in our `Pokemon` class so that we can assign a new move to a Pokémon. But our Pokémons can only learn moves that correspond to their types, so we need to code that conditional in as well. And finally, to look at all the moves each Pokémon knows, we use the `known_moves` method.

```
class Pokemon
  attr_accessor :pokemon_name, :pokemon_type, :pokemon_moves, :trainer

    @@pokedex = []

  def initialize(pokemon_name, pokemon_type)
    @pokemon_name = pokemon_name
    @pokemon_type = pokemon_type
    @@pokedex << self
    @pokemon_moves = []
  end

  def assign_move(move)
    if self.pokemon_type == move.pokemon_type
      puts "#{pokemon_name} learned #{move.move}!"
      @pokemon_moves << move
      move.pokemon << self
    else
      puts "#{pokemon_name} can't learn #{move.move}!"
    end
  end

  def known_moves
    @pokemon_moves.map { |move|
      move.move
    }
  end
end
```

Let’s try calling our new methods!

```
pikachu.assign_move(thunderbolt)      # =>  "Pikachu learned Thunderbolt!"
pikachu.assign_move(surf)             # =>  "Pikachu can't learn Surf!"
pikachu.assign_move(frenzy_plant)     # =>  "Pikachu can't learn Frenzy Plant!"
pikachu.assign_move(catastropika)     # =>  "Pikachu learned Catastropika!"

pikachu.known_moves
=> ["Thunderbolt", "Catastropika"]
```

All done! Here, we learn about how each class can use the informations in other classes to its advantage, especially with the help of `attr_accessor`s. We also learned about how to access those information and represent them as strings or array, using Ruby’s built-in `map` method. We also used `self` whenever we want to use objects (data and behaviour) to describe something. We got to practice some conditionals along the way, too. So that’s it! Hope that was as much fun for you as it was for me!

<hr>

*All flub-ups and boo-boos, if there are any, are mine. Any questions, shoot a message anytime! I don’t bite. Follow me on Twitter at @jouissances. Originally published on Medium [here](https://medium.com/@janicedarikho/learning-object-relationship-in-ruby-with-pok%C3%A9mons-c1f7e816fa8a).
*




