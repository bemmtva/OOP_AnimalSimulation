--Hobby Animal Simulation

A C# console application that simulates the daily life of Cathy's hobby animals: fish, birds, and dogs across a series of days with varying moods. Built for  demonstrating the **Visitor** and **Singleton** design patterns.

---

--Problem Description

Cathy owns three types of animals: **fish**, **birds**, and **dogs**. Each animal has an exhilaration level between **0 and 100** (0 = the animal dies). Cathy's daily mood affects every animal's exhilaration level differently:

| Mood | Fish | Bird | Dog |
|------|------|------|-----|
| Good (`g`) | +1 | +2 | +3 |
| Ordinary (`o`) | -3 | -1 | 0 |
| Bad (`b`) | -5 | -3 | -10 |

At the end of the simulation, the program outputs the name(s) of the **alive animal(s) with the lowest exhilaration level**.

---

Architecture & Design

Class Hierarchy

```
Animal  (abstract)
├── Fish
├── Bird
└── Dog

IMood  (interface)
├── Good     (Singleton)
├── Ordinary (Singleton)
└── Bad      (Singleton)
```

--Design Patterns Used:

--Visitor Pattern
The mood classes act as **visitors** over the animal hierarchy. Each `Animal` subclass implements `Traverse(IMood mood)`, which calls back `mood.Change(this)` — achieving **double dispatch** without any type-checking conditionals. This means adding a new animal type or mood requires minimal changes and does not violate the Open/Closed Principle.

```csharp
// Animal calls back into the mood (double dispatch)
protected override IMood Traverse(IMood mood) => mood.Change(this);

// Mood applies the correct effect per animal type
public IMood Change(Dog p)  { p.LevelChange(3);  return this; }
public IMood Change(Fish p) { p.LevelChange(1);  return this; }
public IMood Change(Bird p) { p.LevelChange(2);  return this; }
```

--Singleton Pattern
`Good`, `Ordinary`, and `Bad` are each implemented as Singletons — since mood objects are stateless and shared across all animals, there is no need to instantiate them more than once.

```csharp
public static Good Instance()
{
    if (instance == null) instance = new Good();
    return instance;
}
```

--Why Not Simple Conditionals?

Using `if (mood == "good")` inside each animal class would violate the **Open/Closed Principle** (SOLID): every time a new mood is introduced, all animal classes would need modification. The Visitor pattern eliminates this coupling entirely.

---



--How to Run

Prerequisites
- [.NET 6.0 SDK](https://dotnet.microsoft.com/download) or higher
- Visual Studio 2022 or JetBrains Rider

--Steps:

```bash
# Clone the repository
git clone https://github.com/bemmtva/OOP_AnimalSimulation.git
cd OOP_AnimalSimulation/oop2

# Build and run
dotnet run
```


---Formal Specification

The simulation satisfies the following postcondition:

```
Post = moods = moods_n
     ∧ ∀i ∈ [1..n]: animals[i], moods_i = daycare(animals[i], moods_{i-1})
     ∧ minexlevel = MIN_{i=1..n, animals[i].ExhilarationLevel} < animals[i].name >
```

The core traversal loop:

```
∀j ∈ [1..m]: animals_j[i], days_i[j] = traverse(animals_{j-1}[i], days_{i-1}[j])
             ∧ animals[i] = animals_m[i]
```



