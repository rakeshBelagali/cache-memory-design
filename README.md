# cache-memory-design
![alt text](https://github.com/rakeshBelagali/cache-memory-design/blob/master/Screen%20Shot%202018-01-02%20at%207.31.32%20PM.png)


# Implementation


● Refer​ ​umon.c​ ​and​ ​umon.h​ ​files.
● Set​ ​up​ ​one​ ​utility​ ​monitor​ ​(Umon)​ ​for​ ​each​ ​core.
● Utility​ ​monitors​ ​are​ ​analogous​ ​to​ ​pseudo​ ​L2​ ​caches​ ​with​ ​entire​ ​ways​ ​for​ ​their​ ​respective
cores.
● Each​ ​utility​ ​monitor​ ​has​ ​-
○ An​ ​Auxiliary​ ​Tag​ ​Directory​ ​(ATD)​ ​implemented​ ​as​ ​a​ ​2D-array​ ​[32][16].
○ 16 ​ ​hit​ ​counters​ ​for​ ​ 16 ​ ​ways​ ​-​ ​a​ ​1D​ ​array​ ​of​ ​ 16 ​ ​elements.
○ partData​ ​-​ ​a​ ​structure​ ​to​ ​populate​ ​data​ ​needed​ ​for​ ​partitioning.
■ Number​ ​of​ ​ways​ ​for​ ​core0.
■ Total​ ​utility.
● Monitoring​ ​data​ ​-
○ One​ ​of​ ​every​ ​ 32 ​ ​sets​ ​of​ ​L2​ ​cache​ ​is​ ​sampled​ ​and​ ​a​ ​total​ ​of​ ​ 32 ​ ​sets​ ​are​ ​monitored
by​ ​Umon.​ ​This​ ​means​ ​on​ ​every​ ​L2​ ​access​ ​if​ ​the​ ​set​ ​is​ ​one​ ​among​ ​the​ ​ 32 ​ ​that​ ​are
sampled,​ ​it​ ​will​ ​also​ ​result​ ​in​ ​Umon​ ​access.​ ​(setIndex%32​ ​==​ ​0)
○ For​ ​each​ ​set,​ ​tags​ ​in​ ​ATD​ ​are​ ​arranged​ ​from​ ​MRU​ ​(most​ ​recently​ ​used)​ ​to​ ​LRU
(least​ ​recently​ ​used)​ ​positions.
○ Whenever​ ​a​ ​tag​ ​in​ ​a​ ​set​ ​of​ ​ATD​ ​is​ ​a​ ​hit,​ ​the​ ​hit​ ​counter​ ​of​ ​that​ ​corresponding​ ​way
is​ ​incremented​ ​and​ ​it​ ​is​ ​moved​ ​to​ ​MRU​ ​position.​ ​This​ ​will​ ​just​ ​result​ ​in​ ​reshuffling
to​ ​move​ ​that​ ​tag​ ​to​ ​MRU,​ ​nothing​ ​is​ ​evicted.
○ But,​ ​if​ ​a​ ​tag​ ​is​ ​a​ ​miss,​ ​it​ ​is​ ​just​ ​inserted​ ​at​ ​MRU​ ​position​ ​of​ ​that​ ​set.​ ​NO​ ​hit
counters​ ​are​ ​incremented.​ ​Tag​ ​in​ ​LRU​ ​position​ ​will​ ​be​ ​evicted.
● Making​ ​the​ ​partitioning​ ​decision​ ​-
○ Decision​ ​is​ ​taken​ ​every​ ​few​ ​million​ ​cycles.
○ Utility​ ​of​ ​a​ ​core​ ​for​ ​x​ ​ways​ ​=​ ​sum​ ​of​ ​first​ ​‘x’​ ​hit​ ​counters​ ​starting​ ​from​ ​MRU.
○ Total​ ​utility​ ​=​ ​​ ​utility​ ​of​ ​core0​ ​for​ ​‘x’​ ​ways​ ​+​ ​utility​ ​of​ ​core1​ ​for​ ​‘16-x’​ ​ways.
○ Total​ ​utility​ ​is​ ​calculated​ ​for​ ​all​ ​combinations​ ​from​ ​‘1-15’​ ​to​ ​‘15-1’.​ ​(Note​ ​that​ ​at
least​ ​one​ ​way​ ​is​ ​given​ ​to​ ​each​ ​core).
○ The​ ​combination​ ​that​ ​results​ ​in​ ​the​ ​highest​ ​total​ ​utility​ ​is​ ​chosen​ ​and​ ​partitioning
is​ ​done​ ​according​ ​to​ ​that​ ​until​ ​the​ ​next​ ​decision​ ​event.
○ Hit​ ​counters​ ​are​ ​halved​ ​at​ ​the​ ​end​ ​of​ ​each​ ​decision.
