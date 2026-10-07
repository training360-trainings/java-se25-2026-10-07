# Alapfogalmak

* Generational, weak-generational hypothesis
* Young generation és old generation
* Az objektumok a young generationön születnek
* GC után a megmaradtak átkerülnek az old generationbe
* Minor GC, Full GC
* Stop the world
* Parallel: több szálon fut a szemétgyűjtő algoritmus
* Concurrent: az alkalmazás futásával párhuzamosan

# G1

* Garbage-First
* Java SE 7-ben jelent meg
* A memóriát régiókra osztja
* Bizonyos régiókat az eden, survivor vagy az old területhez rendeli
* A legtöbb szemetet tartalmazó régiókat üríti, innen ered a neve is
* Még élő objektumokat más régiókba másolja

# ZGC

* Z Garbage Collector
* 11-ben még csak kísérleti, 15-ben lesz production ready
* Terabyte nagyságú heap-ek kezelésére
* 10 ms alatt a megállások
* Skálázható, memóriaterület nagyságának növekedésével ne nőjön a GC idő
* Max 15%-ot használjon a futási időből
* Nem írja át azonnal a referenciákat
* Színes pointerek, a referencia állapota
* Load barrier, kódrészletek
* Referenciák kijavítása időben eltolva
* Java 21-ben generációs
* Java 23-tól alapértelmezett a generációs
* Java 24-től nincs nem generációs

# Shenandoah

* Java 12-ben experimental, 15-ben végleges
* A Low-Pause-Time Garbage Collector
* Nagyon hasonló a G1 szemétgyűjtőhöz
  * Régiókkal dolgozik
  * Különbség: objektumok átmozgatása másik régiókba is párhuzamosan tud történni
  * Indirection (brooks) pointer: objektum headerben lévő plusz adat, mely az átmozgatott
    objektumra mutat
  * Az eredeti objektumot nem törli, hanem beállítja a pointert, így az alkalmazással
    párhuzamosan képes futni

