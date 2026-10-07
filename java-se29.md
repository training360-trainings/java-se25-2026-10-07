## Java 26 újdonságok

- JEP 500: Prepare to Make Final Mean Final
- JEP 504: Remove the Applet API
- JEP 516: Ahead-of-Time Object Caching with Any GC
- JEP 517: HTTP/3 for the HTTP Client API
- JEP 522: G1 GC: Improve Throughput by Reducing Synchronization

## Java 26 újdonságok - preview, experimental

- JEP 524: PEM Encodings of Cryptographic Objects (Second Preview)
- JEP 525: Structured Concurrency (Sixth Preview)
- JEP 526: Lazy Constants (Second Preview)
- JEP 529: Vector API (Eleventh Incubator)
- JEP 530: Primitive Types in Patterns, instanceof, and switch (Fourth Preview)

## JEP 500: Prepare to Make Final Mean Final

* Reflection tudja módosítani
* Warning

`FinalDemo.java`

```java
static class Employee {
    private final String name;

    public Employee(String name) {
        this.name = name;
    }
}

static void main() throws Exception {
    var employee = new Employee("John Doe");
    Field f = Employee.class.getDeclaredField("name");
    f.setAccessible(true);
    System.out.println(employee.name);  // Prints 100

    f.set(employee, "Jack Doe");
    System.out.println(employee.name);  // Prints 200
}
```

## JEP 504: Remove the Applet API

## JEP 516: Ahead-of-Time Object Caching with Any GC

* GC független megoldásra való átállás (ZGC esetén is működjön)
* Az AOT és a GC független legyen egymástól

## JEP 517: HTTP/3 for the HTTP Client API

```java
var client = HttpClient.newBuilder()
        .version(HttpClient.Version.HTTP_3)
        .build();
var request = HttpRequest.newBuilder(URI.create("https://www.jtechlog.hu/"))
        .GET().build();
var response = client.send(request, HttpResponse.BodyHandlers.ofString());
System.out.println(response.statusCode());
var htmlText = response.body();
System.out.println(htmlText);
```

* TCP helyett QUIC (UDP alapú) protokollra épül
* A HTTP/1.1 és HTTP/2 TCP + TLS handshake-et igényel
* Gyorsabban jön meg az első byte, főleg magas latency (mobil, távoli szerver) esetén.
    * Multi region appok
    * Real-time appoknál jól jöhet
* Külön stream-ek függetlenek, egy elveszett packet nem blokkolja a többit
    * Ez jó lehet streamek esetén, nincs globális megakadás
* QUIC-ben be van építve a TLS 1.3
* HTTP/3 mindig titkosított, TLS 1.3 kötelező
* Nem IP-hez kötött, WiFi → mobilnet, egyik cella → másik cella nem kavar be
    * Mobil klienseknél
* Hatékonyabb torlódáskezelés

## JEP 522: G1 GC: Improve Throughput by Reducing Synchronization

* A heap fel van osztva kis fix méretű blokkokra, ez a card
    * 512 byte
* card table: melyik card clean, és melyik dirty
    * dirty: történt referencia módosítás
    * GC-nek foglalkoznia kell vele
* Alkalmazás thread módosítja referencia értékadáskor
* GC is módosítja
* Szinkronizáció
* Trükk: két card table
    * aktív (primary) – ezt írják az alkalmazás threadek
    * másodlagos (secondary) – ezt dolgozzák fel a GC threadek
* Egyszerűsödik a write barrier
    * Injektált kódrészlet, mely minden attribútum írásakor lefut
    * Ez frissíti a card table-t
    * ~50 utasítás helyett kb. ~12 utasítás

## Java 27 újdonságok

- JEP 523:	Make G1 the Default Garbage Collector in All Environments
- JEP 527:	Post-Quantum Hybrid Key Exchange for TLS 1.3
- JEP 534:	Compact Object Headers by Default
- JEP 536:	JFR In-Process Data Redaction

---

## Java 27 újdonságok - preview, experimental

- JEP 531:	Lazy Constants (Third Preview)
- JEP 532:	Primitive Types in Patterns, instanceof, and switch
(Fifth Preview)
- JEP 533:	Structured Concurrency (Seventh Preview)
- JEP 537:	Vector API (Twelfth Incubator)
- JEP 538:	PEM Encodings of Cryptographic Objects (Third Preview)

## Make G1 the Default Garbage Collector in All Environments

Eddig az ún. ergonomics alapján döntötte el, hogy mi legyen a GC. Pl.
kis erőforrású gépeken és konténerekben mást választott.

## Post-Quantum Hybrid Key Exchange for TLS 1.3

## Compact Object Headers by Default

Java 25 újdonság legyen default

## JFR In-Process Data Redaction

* Biztonsági fejlesztés
* A JFR (Java Flight Recorder) mostantól még azelőtt kitakarja a potenciálisan érzékeny adatokat, hogy azok bekerülnének a .jfr fájlba.
* Command-line argument, environment variable, system property, könnyen tartalmazhatnak secret-eket