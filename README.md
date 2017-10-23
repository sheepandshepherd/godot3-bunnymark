# Godot 3.0 Bunnymark: GDScript vs C#/Mono vs GDNative(C++)

Renders an increasing number of bunny sprites until a stable 60fps is hit.  This is a decent test of real world usage as it combines Godot api usage with raw computation.  I plan to update this README whenever significant performance changes occur or when new languages are added.  Feel free to contribute language implementations or improvements!

GDScript example adapted from: https://github.com/curly-brace/godot-bunnies.  Thanks @curly-brace!

## Benchmark Run - October 22, 2017

*Standard*

| Language           | Bunnies Rendered |
|--------------------|------------------|
| GDScript (Debug)   | 9110             |
| GDScript (Release) | 11410            |
| C#/Mono            | 20510            |
| GDNative (C++)     | 32010            |

*Standard + one get_children() call per _process*

| Language           | Bunnies Rendered |
|--------------------|------------------|
| GDScript (Debug)   | 8310             |
| GDScript (Release) | 11210            |
| C#/Mono            | 15610            |
| GDNative (C++)     | 31510            |

*Standard + five get_children() calls per _process*

| Language           | Bunnies Rendered |
|--------------------|------------------|
| GDScript (Release) | 10710            |
| C#/Mono            | 10810            |
| GDNative (C++)     | 27410            |

C#/Mono rendered 1.80x more bunnies than GDScript.  GDNative (C++) rendered 1.56x more bunnies than C#/Mono and 2.81x more bunnies than GDScript.

As a comparison to another engine, I ran the [Pixi.js bunny-mark](https://pixijs.github.io/bunny-mark/) (which does the same thing) and rendered 35000 bunnies at 60fps.

### Hardware:

* CPU: Intel i7 7700k 4.2GHz
* GPU: Nvidia GeForce GTX 1070
* RAM: 16GB DDR4

### Remarks

These results played out as one would expect:
* GDScript < C#/Mono < GDNative (++).  
* In the second test I added a get\_children() call to test marshalling/unmarshalling performance.  get\_children() had almost no effect on GDNative's performance (-1.56%) and GDScripts performance (-1.75%) as the array was not copied.  But it did have a significant effect on C#'s performance (-15.3%)
* The third test illustrates what a landmine the C# marshalling is.  It only took 5 get\_children() calls to bring C#'s performance down to GDScript's performance.  Use enough calls like this and you could easily get performance that is worse than any of the other options.  Be wary of calling Godot apis from C# that set/return Arrays or Dictionaries.
* In C# the game jumped / framerates dipped significantly (ex: from ~100 fps to ~80fps) after each click of the "add bunnies" button.  That did not happen for any of the other languages.
* Across all languages there was a perceivable "skip" about every 10 seconds.  This was independent of bunny count
