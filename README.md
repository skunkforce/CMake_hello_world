[![CMake](https://www.cmake.org/favicon.ico)](https://www.cmake.org)
# _Hello World_ in CMake
Dieses Repository demonstriert die grundlegendste Konfiguration eines Projekts, das `CMake` zur Einrichtung seines Buildsystems verwendet.
Dafür benötigt es nur eine kompilierbare Datei - in unserem Fall `main.cpp` - und eine `CMakeLists.txt`-Datei.
Eine `CMakeLists.txt` wird immer benötigt, wenn du dein Buildsystem mit `CMake` aufsetzen möchtest.

## Das _C_ in CMake
An diesem Punkt solltest du bereits mit Build-Tools wie `GNU Make` vertraut sein.
Diese Tools bieten gleichzeitig zwei sehr wichtige Funktionen:
1) Inkrementelle Builds, um die Kompilierungszeit in größeren Projekten zu beschleunigen, und
2) Dokumentation darüber, wie der Build-Prozess ablaufen soll
Build-Tools können deinen Compiler und andere Programme in deiner Toolchain ausführen, um deinen Quellcode in Programme zu konvertieren.

Wie du vielleicht schon gehört hast, ist `CMake` per se kein Build-System.
`CMake` wird nicht wirklich verwendet, um den Code zu kompilieren, das bleibt weiterhin Aufgabe deines Compilers.
`CMake` ruft auch nicht direkt deinen Compiler auf, das bleibt weiterhin Aufgabe von `GNU Make` oder `Ninja`.
Was macht `CMake` also überhaupt?
Die Antwort ergibt sich für jeden, der versucht hat, ein `Makefile`, das er auf seinem eigenen Rechner geschrieben hat, auf dem Rechner eines Kollegen auszuführen.
Pfade sind anders, Compiler könnten unterschiedlich sein, vielleicht sogar das Betriebssystem ist anders.
Keiner der codierten Parameter im `Makefile` ergibt mehr Sinn.
So wie wir alle, hast du wahrscheinlich etwas Magie zum `Makefile` hinzugefügt, damit es auf beiden Maschinen läuft.

Ich muss wohl nicht erklären, was passiert, sobald du einen dritten Kollegen bekommst, oder?

`C` bedeutet in diesem Fall _crossplattform_.
Es ist die Aufgabe von `CMake`, automatisch eine Build-Datei wie etwa ein `Makefile` für die Maschine zu generieren, auf der es ausgeführt wird.

## Eine weitere Ebene hinzufügen
>  "We can solve any problem by introducing an extra level of indirection." - _David Wheeler_(supposedly)

`CMake` sucht nach einem geeigneten Compiler, sammelt die erforderlichen Pfade und kann sogar zusätzliche Bibliotheken herunterladen, falls sie noch nicht auf dem Host verfügbar sind.
Es fügt - sozusagen - eine weitere Abstraktionsebene zum Buildsystem hinzu.
Indem wir ein Rezept schreiben, das nicht an eine bestimmte Maschine gebunden ist, sondern weiß, wie man verschiedene Maschinen nach korrekten Pfaden, Compilern und Bibliotheken durchsucht, können wir unsere Build-Rezepte maschinenunabhängig machen.
Dies bietet große Vorteile, insbesondere für Projekte in größeren Unternehmen oder für Kompilationsvorgänge in CI/CD-Diensten.
Eine `CMakeLists.txt` wird in jedem Fall benötigt, um `CMake` für ein bestimmtes Projekt auszuführen.
Es beschreibt, was auf der abstraktesten Ebene möglich sein soll, aber immer noch spezifisch genug ist, um deterministisch zu funktionieren.

## Wie man baut
Lass uns dieses Projekt bauen, um mit den tatsächlichen Schritten der Verwendung von `CMake` vertraut zu werden.
Diese Schritte sind typisch für die Verwendung von `CMake` und können offensichtlich modifiziert werden, aber versuche, diese Schritte zu wiederholen, bis sie sich in dein Gehirn eingebrannt haben.
Lerne die Regeln, um sie zu vergessen!
1) Wir beginnen, indem wir ein neues Verzeichnis - genannt build - in unserem Repository erstellen: `mkdir build`
2) Wir navigieren in den Build-Ordner - `cd build` - und führen `cmake ..` aus. Achte dort auf den Parameter `..`.
Er gibt den Pfad an, wo `CMake` die `CMakeLists.txt` finden kann, die quasi das Rezept für `CMake` ist.
Dies wird eine ganze Menge neuer Dateien im Build-Verzeichnis erstellen.
3) Wie bereits erwähnt, richtet `CMake` das Build-System für uns ein.
Daher kannst du ein `Makefile` im `./build/`-Ordner finden. Du könntest es potenziell einzeln ausführen, aber manchmal kann man sich damit selbst ins Bein schießen. Daher solltest du jetzt `cmake --build .` aufrufen.
Dies wird indirekt das richtige Build-Tool aufrufen und dein Programm kompilieren.
4) Der Kompilierungsprozess sollte nun eine Datei namens `HELLO` erzeugt haben. Gute Arbeit! Du hast dein erstes Projekt mit `CMake` kompiliert!

## Was ist in der `CMakeLists.txt`
Um eine einfache _Hello World_-Anwendung zu bauen, benötigen wir lediglich drei Zeilen Code in `CMake`.
Sicherlich ist das viel, da es mehr als 100% der Zeichen unseres ursprünglichen Programms zum Repository hinzufügt, aber je komplexer dein Projekt wird, desto mehr wirst du dich in die Schönheit von `CMake` verlieben.

Die erste Zeile in einer `CMakeLists.txt` gibt immer die minimal erforderliche Version ([Doku](https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html)) an, die benötigt wird, um dieses spezifische Skript auszuführen.
> `cmake_minimum_required(VERSION 3.10)`

Du kannst die aktuelle Version auf der [offiziellen `CMake` Release-Seite](https://cmake.org/download) finden

Die nächste Zeile gibt an, wie dein Projekt genannt werden soll.
In unserem Fall wird dies auch der Name des ausführbaren Programms sein, ([Doku](https://cmake.org/cmake/help/latest/command/project.html)). Die Versionsnummer ist optional.
> `project(HELLO VERSION 1.0)`

Der nächste Befehl ist optional und zeigt nur die Möglichkeit in diesem spezifischen `CMakeLists.txt`.
Ich glaube, du wirst selbst herausfinden, was dies tut
> `message("This is Hello World in CMake")`

Und schließlich definieren wir in der letzten obligatorischen Zeile unseres `CMake`-Codes, welche Dateien verlinkt werden sollen, um unser ausführbares Programm zu werden, und wie es genannt werden soll, indem wir die folgende Funktion aufrufen ([Doku](https://cmake.org/cmake/help/latest/command/add_executable.html))
> `add_executable(${PROJECT_NAME} main.cpp)`

Wie du vielleicht schon vermutet hast, ist `${PROJECT_NAME}` eine Variable, die durch den vorherigen `project()`-Befehl gesetzt wurde.

## Was man _nicht_ in `CMake` machen kann
`CMake` ist tatsächlich eine turing-vollständige Sprache.
Daher kannst du ziemlich alles in `CMake` programmieren.
Aber nur weil du kannst, heißt das nicht immer, dass du solltest.
Verwende `CMake`, um deine Build-Umgebung einzurichten und zu konfigurieren.
Versuche selbst herauszufinden, was sonst noch `CMake` tun kann.

# _Hello World_ in CMake 
This repository demonstrates the most basic configuration of a project, that uses `CMake` to setup its buildsystem.
In order for this to work, it only needs a compilable file - in our case `main.cpp` - and a `CMakeLists.txt`-file.
A `CMakeLists.txt` is always required if you want to setup your buildsystem using `CMake`.

## The _C_ in CMake
At this point, you should be familiar with build-tools like `GNU Make`.
Those tools provide two very important features at once:
1) Incremental Builds, to speed up compilation-time in larger projects, and
2) Documentation about how the build-process is supposed to happen
Buildtools can run your compiler and other programs in your toolchain to convert your source-code into programs.

As you may have heard already, `CMake` isn't a build-system per se. 
`CMake` is not really used to compile the code, this is still your compilers part.
`CMake` also doesn't call your compiler directly, this is still `GNU Make`s or `Ninja`s part.
So what does `CMake` do at all?
The answer comes to everyone that tried to run a `Makefile` they wrote on their own machine on a co-workers machine.
Paths are different, compilers might be different, maybe even the OS is different. 
None of the coded parameters in the `Makefile` make sense anymore. 
So as we all have, you probably added some magic to the `Makefile` to make it run on both your machines.

I probably don't need to explain what happens as soon as you've got a third co-worker, right?

`C` means cross-platform in this case. 
It is `CMake`s job, to automatically generate a build-file, such as a `Makefile` for the machine you are running it on.

## Adding another Layer
>  "We can solve any problem by introducing an extra level of indirection." - _David Wheeler_(supposedly)

`CMake` searches for a suitable compiler, gathers the required paths and can even download additional libraries if they are not yet available on the host.
It adds - so to speak - another layer of abstraction to the buildsystem. 
By writing a recipe, that is not bound to a specific machine, but knows how to query different machines for correct paths, compilers and download libraries, we can make our build recipies machine independent.
This features great benefits, especially for projects in larger companies, or running compilation-jobs in CI/CD services.
A `CMakeLists.txt` is required in any case, to run `Cmake` for a specific project.
It describes, what should be done on the most abstract level possible, but still being specific enough to work deterministically.

## How to Build
Let's build this project to get familiar with the actual steps of using `CMake`.
These steps are idiomatic for using `CMake` and can be obviously modified, but try to repeat these steps until they burned into your brains.
Learn the rules to forget them!
1) We start by creating a new directory - called build - inside our repository: `mkdir build`
2) We navigate into the buildfolder - `cd build` - and run `cmake ..`. Be aware of the parameter `..` there. 
It specifies the path where `CMake` can find the `CMakeLists.txt` which is sort of the recipe for `CMake`. 
This will create a whole plathora of new files in the build-directory. 
3) As we already mentioned, `CMake` sets up the build-system for us. 
Thus you can find a `Makefile` in the `./build/`-folder. You could potentially run it individually, but you can shoot yourself in the foot with this sometimes. Therefor you shall now call `cmake --build .`. 
This will indirectly call the correct build-tool and compile your program. 
1) The compilation-process should now have generated a file called `HELLO`. 

Good Job! You've compiled your first project with `CMake`!

## What's inside the `CMakeLists.txt`
To build a simple _Hello World_ application, we merely need three lines of code in `CMake`.
Surely this is a lot, as it basically adds more than 100% of the characters of our original program to the repository, but the more complex your project gets, the more you'll fall in love with the beauty of `CMake`.

The first line in a `CMakeLists.txt` always specifies the minimal required version ([docu](https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html)) that is needed to run this specific script. 
> `cmake_minimum_required(VERSION 3.10)` 

You can find the current version on the [official `CMake` Release Page](https://cmake.org/download)

The next line specifies, what your project should be called. 
In our case this will also be the name of the executable, ([docu](https://cmake.org/cmake/help/latest/command/project.html)). The Version Number is optional.
> `project(HELLO VERSION 1.0)`

The next command is optional and only demonstrates the possibility in this specific `CMakeLists.txt`.
I believe you will find out for yourselves what this does
> `message("This is Hello World in CMake")`

And finally, in the last mandatory line of our `CMake`-Code, we define, which files should be linked to become our executable, and what it should be called, by calling the following function ([docu](https://cmake.org/cmake/help/latest/command/add_executable.html))
> `add_executable(${PROJECT_NAME} main.cpp)`

As you may have already guessed, `${PROJECT_NAME}` is a variable, that was set by the previous `project()`-command. 

## What you _can't_ do in `CMake`
`CMake` is actually a turing-complete language. 
Therefor you can pretty much program anything in `CMake`. 
But just because you can, doesn't always imply that you should.
Use `CMake` to setup your build-environment and to configure it.
Try for yourselves what else `CMake` can do.
