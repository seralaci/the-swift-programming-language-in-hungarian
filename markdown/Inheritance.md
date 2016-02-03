# Öröklődés
-----------

Egy osztály *örökölheti* egy másik osztály metódusait, tulajdonságait és egyéb jellemzőit. Amikor az egyik osztály a másikból öröklődik, akkor az öröklő osztályt *gyermek/származtatott osztálynak (subclass)*, míg azt az osztályt amiből öröklődik, *ős/szülőosztályának (superclass)* nevezzük. Az öröklődés egy olyan alapvető tulajdonsága az osztályoknak, ami megkülönbözteti a Swift egyéb típusaitól.

Swift-ben az osztályok meg tudják hívni és elérik a szülőosztály metódusait, tulajdonságait, indexeit és a saját verziókkal felül tudják definiálni ezeket a metódusokat, tulajdonságokat, indexeket annak érdekében, hogy módosítsák azok eredeti viselkedését. A Swift segítséget nyújt a felülbírálások helyességének ellenőrzésében azzal, hogy megvizsgálja a szülő osztályban a felülbírálandó definíció meglétét.

Az osztályokban lehetőség van ún. tulajdonság megfigyelőket hozzárendelni az örökölt tulajdonságokhoz, hogy értesítést kapjon, ha megváltozik a megfigyelt tulajdonság értéke. Ezek a megfigyelők bármilyen tulajdonsághoz hozzáadhatók függetlenül attól, hogy tárolt vagy számolt tulajdonságról van szó.

<a name="defining_a_base_class"></a>
## Egy alaposztály létrehozása

Minden olyan osztályt, ami nem öröklődik egy másik osztályból, *alaposztálynak* nevezzük.

> MEGJEGYZÉS  
A Swift osztályok nem öröklődnek automatikusan semmiféle univerzális alaposztályból. Az osztályok, amelyek ősosztály nélkül vannak definiálva, automatikusan alaposztállyá válnak.

A következő példa egy `Vehicle` nevű alaposztályt definiál. Ez az alaposztály tartalmaz egy `currentSpeed` nevű tárolt tulajdonságot, melynek az alapértéke `0.0` (így a tulajdonság *Double* típusú lesz). Ennek a `currentSpeed` tulajdonságnak az értékét a `String` típusú, csak olvasható, `description` nevű kalkulált tulajdonság használja a jármű szöveges leírásához.

A `Vehicle` alaposztály tartalmaz még egy `makeNoise` nevű metódust is. Ez a metódus jelenleg nem csinál semmit egy alap `Vehicle` példány esetén, de majd később felülbíráljuk a `Vehicle` osztály származtatott osztályaiban:


```swift
class Vehicle {
    var currentSpeed = 0.0
    var description: String {
        return "jelenleg \(currentSpeed) km/h sebességgel halad"
    }
    func makeNoise() {
        // nem csinálunk semmit - nem minden jármű csap zajt
    }
}
```

Új példányt a `Vehicle` osztályból *inicializáló szintaxis* segítségével tudsz létrehozni, ami egy `TípusNév` majd üres zárójelek:

```swift
let someVehicle = Vehicle()
```

Miután létrehoztál egy új példányt a `Vehicle` osztályból, el tudod érni a `description` tulajdonságot hogy ki tudd írni emberek számára is könnyen értelmezhető formában a jármű sebességét:

```swift
print("Jármű: \(someVehicle.description)")
// Jármű: jelenleg 0.0 km/h sebességgel halad
```

A `Vehicle` osztály a járművek általános jellemzőit határozza meg, de ez önmagában nem túl sok. Ahhoz hogy igazán használható legyen, tovább kell finomítani további járművek leírásával.

<a name="subclassing"></a>
## Származtatás

*Származtatásnak* nevezzük, amikor egy új osztályt egy meglévőből örököltetünk. A származtatott osztály örökli a meglévő ősosztály jellemzőit, amiket aztán tovább tudsz finomítani. De természetesen a származtatott osztály tovább bővíthető saját jellemzőkkel.

Ha szeretnél egy osztályt egy meglévőből származtatni, akkor nincs más dolgod, mint az új osztály neve után egy kettősponttal elválasztva megadni a meglévő osztály nevét, amiből származtatni szeretnéd:

```swift
class SomeSubclass: SomeSuperclass {
    // ide jön a származtatott osztály törzse
}
```

A következő példa egy `Bicycle` nevű osztályt definiál, ami a `Vehicle` osztályból van származtatva:

```swift
class Bicycle: Vehicle {
    var hasBasket = false
}
```

Az új `Bicycle` osztály automatikusan örökli a `Vehicle` jellemzőit, azaz a `currentSpeed` és `description` tulajdonságokat és a `makeNoise()` nevű metódust.

Az örökölt dolgokon felül a `Bicycle` osztályban definiálva lett egy új tárolt tulajdonság, a `hasBasket`, aminek alapértelmezett érteke `false` (így a tulajdonság típusa `Bool`).

Alapértelmezetten, egyik újonnan létrehozott `Bicycle` példány sem rendelkezik kosárral. A `hasBasket` tulajdonság `true` értékre állítható egy adott `Bicycle` példány esetén, miután azt létrehoztuk:

```swift
let bicycle = Bicycle()
bicycle.hasBasket = true
```

Ugyanígy lehetőség van a `Bicycle` példány örökölt `currentSpeed` tulajdonságának módosítására is és ki írathatjuk a szintén örökölt `description` tulajdonság értékét is:

```swift
bicycle.currentSpeed = 15.0
print("Bicikli: \(bicycle.description)")
// Bicikli: jelenleg 15.0 km/h sebességgel halad
```

A származtatott osztályokat is tovább lehet származtatni. A következő példában a `Bicycle` osztályt származtatjuk tovább egy "tandem" néven ismert kétüléses bicikli létrehozásához:

```swift
class Tandem: Bicycle {
    var currentNumberOfPassengers = 0
}
```

A `Tandem` örökli a `Bicycle` osztály minden tulajdonságát és metódusát, ami viszont örökölte minden tulajdonságát és metódusát a `Vehicle` osztálynak. A `Tandem` osztály ezeken felül még tartalmaz egy új tárolt tulajdonságot is: `currentNumberOfPassengers`, aminek alapértéke `0`.

Ha létrehozol egy `Tandem` példányt, akkor eléred az összes örökölt és saját tulajdonságot, illetve le tudod kérdezni a csak olvasható `description` tulajdonság értékét is, amit a `Vehicle` osztályból örökölt:

```swift
let tandem = Tandem()
tandem.hasBasket = true
tandem.currentNumberOfPassengers = 2
tandem.currentSpeed = 22.0
print("Tandem: \(tandem.description)")
// Tandem: jelenleg 22.0 km/h sebességgel halad
```

<a name="overriding"></a>
## Felülírás (Overriding)

Egy származtatott osztály tartalmazhat saját példánymetódus, típusmetódus, példány tulajdonság, típus tulajdonság vagy akár index implementációt, amelyeket egyébként az ősosztályból örökölt. Ezt nevezzük *felülírásnak*.

Ahhoz hogy felülírhassunk egy örökölt jellemzőt, az `override` kulcsszót oda kell biggyeszteni az új definíció elé. Ezzel egyértelműsíted a szándékodat, hogy valamit felül szeretnél írni és nem véletlenül adtál meg egyező definíciót. Nem várt következményekkel járhat, ha véletlenül írunk felül valamit és emiatt az `override` kulcsszó használata nélküli felülírást a fordító hibaként fogja jelezni.

Az `override` kulcsszó azt is jelzi a Swift fordító számára, hogy szükség van az ősosztályban (vagy annak valamelyik szülőjében) annak ellenörzésére, hogy a felülírandó jellemző létezik. Így biztosítva vagyunk, hogy a felülírás definíciója helyes.

<a name="accessing_superclass_methods_propertie_and_subscripts"></a>
### A szülőosztály metódusainak, tulajdonságainak és indexeinek elérése

Amikor egy származtatott osztályban felülírsz egy metódust, tulajdonságot vagy indexet, akkor hasznos lehet az ősosztálybeli implementációt is elérni. Például módosítani tudod a meglévő implementációt vagy új értéket adhatsz egy örökölt változónak.

Ahol ez indokolt, el tudod érni az ősosztály metódusát, tulajdonságát vagy indexét a `super` előtag használatával:

* A `someMethod()` nevű felülíró metódusban meg tudod hívni az ősosztály `someMethod()` metódusát a `super.someMethod()` segítségével az új implementáción belül.
* A `someProperty` nevű felülíró tulajdonság getter vagy setter implementációjából az ősosztály `someProperty` tulajdonságát a `super.someProperty` használatával tudod elérni.
* A `someIndex` felülíró index implementációjából pedig a `super[someIndex]` segítségével tudod elérni a szülőosztályben definiált változatot.

<a name="overriding_methods"></a>
### Metódusok felülírása 

Lehetőség van egy öröklött példány- vagy típusmetódus felülírására, hogy saját implementációt tudjuk meghatározni a származtatott osztály számára.

A következő példában a `Vehicle` osztály `Train` nevű származtatott osztályát hozzuk létre, amiben felülírjuk a `makeNoise()` metódust amit a `Train`, a `Vehicle` osztálytól örökölt:

```swift
class Train: Vehicle {
    override func makeNoise() {
        print("Choo Choo")
    }
}
```

Ha létrehozunk egy új `Train` példányt és meghívjuk a `makeNoise()` metódusát, akkor láthatjuk, hogy a `Train` osztályban meghatározott implementációja kerül meghívásra:

```swift
let train = Train()
train.makeNoise()
// Eredmény: "Choo Choo"
```

<a name="overriding_properties"></a>
### Tulajdonságok felülírása

Lehetőség van egy öröklött példány- vagy típus tulajdonságok felülírására is, saját getter és setter definiálható hozzájuk vagy akár tulajdonság megfigyelőt rendelhetünk hozzájuk, hogy a felülíró tulajdonság értesüljön róla, ha a mögöttes tulajdonság értéke megváltozik.

<a name="overriding_property_getters_and_setters"></a>
### Tulajdonság getter és setter felülírása

Saját getter (és setter, ha szükséges) hozható létre hogy felülírjunk *bármilyen* örökölt tulajdonságot, függetlenül attól, hogy tárolt vagy kalkulált tulajdonságként lettek azok implementálva. Az örökölt tulajdonság számított vagy tárolt jellege nem ismert a származtatott osztály számára, csupán a tulajdonság nevét és típusát ismeri. A felülírandó tulajdonság nevét és típusát mindig meg kell határozni, hogy a fordító ellenőrizni tudja azok meglétét az ősosztályban.

Akár arra is lehetőség van, hogy egy csak olvasható tulajdonságot írhatóvá tegyünk a getter és setter implementációójával a származtatott osztályban. Viszont nincs lehetőségünk egy már írható tulajdonságot csak olvashatóvá tenni.

> MEGJEGYZÉS  
Ha egy setter-t definiálunk a felülíró tulajdonsághoz, akkor a getter felülírása is kötelező. Ha nem akarod módosítani az örökölt tulajdonság getter implementációját, akkor egyszerűen csak passzoljuk vissza az értéket a `super.someProperty` használatával a felülíró getter-ből, ahol a `someProperty` a felülírandó tulajdonság neve.

A következő példában egy `Car` nevű osztályt definiálunk, ami a `Vehicle` osztályból származik. A`Car` osztályban bevezetünk egy `gear` nevű új tárolt tulajdonságot, aminek alapértelmezett értéke `1` lesz. Továbbá a `Car` osztályban felülírjuk a Vehicle osztálytól örökölt description tulajdonságot, hogy még specifikusabb leírást biztosítsunk, ami tartalmazza majd az aktuális sebességfokozatot is:

```swift
class Car: Vehicle {
    var gear = 1
    override var description: String {
        return super.description + " \(gear). sebességi fokozatban"
    }
}
```

A `description` felülírt tulajdonság implementációja egy `super.description` hívással kezdődik, ami visszaadja a `Vehicle` osztály `description` tulajdonság implementáció szerinti értékét. A `Car` osztály `description` tulajdonságában ezután még a megkapott leíráshoz hozzáfűzzük a jelenlegi sebességfokozattal kapcsolatos információt.

Ha létrehozunk egy új `Car` példányt és beállítjuk a `gear` és `currentSpeed` tulajdonságok értékét, akkor a `description` tulajdonság a `Car` osztályban meghatározott szöveget fogja visszaadni számunkra:

```swift
let car = Car()
car.currentSpeed = 25.0
car.gear = 3
print("Autó: \(car.description)")
// Autó: jelenleg 25.0 km/h sebességgel halad 3. sebességi fokozatban
```

<a name="overriding_property_observers"></a>
### Tulajdonság megfigyelők felülírása

A tulajdonságok felülírásával megfigyelőt rendelhetünk egy örökölt tulajdonsághoz. Ez lehetőséget biztosít számunkra, hogy értesüljünk, ha egy örökölt tulajdonság értéke megváltozott, függetlenül attól, hogy miként volt eredetileg implementálva a tulajdonság. Tovább információt a tulajdonság megfigyelőkről itt találhatsz: [Tulajdonság megfigyelők](markdown/Properties.md).

> MEGJEGYZÉS  
Nincs lehetőség örökölt konstans tárolt vagy csak olvasható kalkulált tulajdonságokhoz megfigyelőt rendelni. Ezeknek a tulajdonságoknak az értékét nem lehet beállítani és így nincs értelme a `willSet` vagy `didSet` megfigyelőket implementálni.  
Szintén vegyük figyelembe, hogy nincs lehetőség egyszerre felülírni egy setter-t és egy megfigyelőt ugyanazon tulajdonsághoz. Ha mégis szeretnél értesülni egy tulajdonság értékének változásáról, miközben már felülbíráltad a setter-t, akkor figyeljük meg az értékek változását az adott setter-en belül.

A következő példában létrehozunk egy `AutomaticCar` osztály, aminek a `Car` lesz az őse. Az `AutomaticCar` a nevéből következően automata valtós autókat hivatott reprezentálni, ami a jelenlegi sebesség függvényében határozza meg a megfelelő sebességi fokozatot:

```swift
class AutomaticCar: Car {
    override var currentSpeed: Double {
        didSet {
            gear = Int(currentSpeed / 10.0) + 1
        }
    }
}
```

Amikor beállítod az `AutomaticCar` példány `currentSpeed` tulajdonságának értékét, akkor a tulajdonság `didSet` megfigyelője beállítja az adott példány `gear` tulajdonságát az új sebességnek megfelelő sebbességfokozat értékére. A sebességfokozat értékét úgy állapítjuk meg a megfigyelőben, hogy a `currentSpeed` új értékét osztjuk `10`-el, lefelé kerekítve a legközelebbi egész számra és megnöveljük az értéket `1`-el. Így pl. a 35.0 km/h-s sebesség 4-es sebességi fokozatot fog eredményezni:

```swift
let automatic = AutomaticCar()
automatic.currentSpeed = 35.0
print("Automata: \(automatic.description)")
// Automata: jelenleg 35.0 km/h sebességgel halad 4. sebességi fokozatban
```

<a name="preventing_overrides"></a>
### Felülírás megelőzése

A *final* használatával tudjuk megvédeni egy metódus, tulajdonság vagy index felülírását. Ehhez a `final` módosítot rakjuk a metódushoz, tulajdonsághoz vagy indexhez tartozó kulcsszó elé (azaz `final var`, `final func`, `final class func` illetve `final subscript`).

Ha egy `final` módosítóval ellátott metódust, tulajdonságot vagy indexet próbálunk meg felülírni egy származtatott osztályban, akkor már fordításkor hibát fogunk kapni.

De akár egy egész osztály esetén használható a `final` módosító, elég csak a `class` kulcsszó elé írni (`final class`). Ezután minden származtatási kísérlet fordítási hibával fog járni az adott osztály esetén.