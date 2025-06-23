---
title: "Patterns: Flyweight"
categories:
  - Game Programming Patterns
  - Learning Content
tags:
  - GameProgramming Patterns
  - Educational
  - Learning Content
---

# Game Programming Patterns 101: The Flyweight Pattern

> Hello hello! This is Game Programming Patterns 101 and this video is about the **Flyweight Pattern**.
> 

**`*[ Display title slide ]*`**

---

> As always, we can share the definitions from the gang of 4 and Wikipedia to have a broad picture.
> 

**`*[ Display wikipedia and gang slide ]*`**

---

> The official design patterns book said:
> 

> "Use sharing to support large numbers of fine-grained objects efficiently."
> 

**[ Display definition with gang logo ]**

---

> And Wikipedia mentioned it this way:
> 

> "The Flyweight pattern minimizes memory usage by sharing some of its data between similar objects."
> 

**`*[ Display definition with Wiki logo ]*`**

---

> Basically — **it's about sharing heavy data across many objects** instead of duplicating and multiplying it everywhere.
> 

**`*[ Display a meme ]*`**

![68747470733a2f2f73332e616d617a6f6e6177732e636f6d2f776174747061642d6d656469612d736572766963652f53746f7279496d6167652f4e50354b774e59594938413468413d3d2d3236323233303730392e313435356633383338323462653433322e6a.jpg](68747470733a2f2f73332e616d617a6f6e6177732e636f6d2f776174747061642d6d656469612d736572766963652f53746f7279496d6167652f4e50354b774e59594938413468413d3d2d3236323233303730392e313435356633383338323462653433322e6a.jpg)

---

> So picture this: You’re walking through a dense and ancient forest.
> 

> Thousands of leaves floating in the air and trees stretch down the hills getting lost into the misty distance.
> 

> Every game developer dream of creating epic scenes like this.
> 

**`*[ Display video of pretty forest ]*`**

---

> **But** — rendering thousands of leaves and trees can obliterate your memory and computer bus communication.
> 

> Even if your machine could somehow hold all that data, moving it back and forth GPU and CPU would *choke* the performance anyways.
> 

**`*[ Display sad programmer meme ]*`**

![4fd1f0edbcf7fee871e4852970e05628.jpg](4fd1f0edbcf7fee871e4852970e05628.jpg)

---

> Coding a not efficient tree like that might look like this:
> 

```cpp
class Tree {
private:
    Mesh StaticMesh;
    Texture Bark;
    Texture Leaves;
    Vector Location;
    double Height;
    double Thickness;
    Color BarkTint;
    Color LeafTint;
```

> This is a quite a bit of data per tree; and the **mesh** and **textures** are the ones hitting heavier.
> 

> But lucky us, in reality, most trees are probably using **the same mesh and textures, so** this case  sounds like a good candidate for the Flyweight pattern.
> 

**`*[ Display this image ]*`**

![flyweight-trees.png](flyweight-trees.png)

---

> We could split the the class to keep the heavy parts separately.
> 

```cpp
class TreeModel {
private:
    Mesh StaticMesh;
    Texture Bark;
    Texture Leaves;
```

> The rest goes to each individual **Tree.** They just need to hold lightweight unique info — like its position, size, or tint.
> 

> This is the concept of Intrinsic and Extrinsic data for the Flyweight pattern.
> 

> Intrinsic is the static data that could be shared across  many trees. Like the Mesh, Texture. Is the same for all.
> 

```cpp
class Tree {
private:
    TreeModel* IntrinsicTreeData;
    
    Vector Location;
    double Height;
    double Thickness;
    Color BarkTint;
    Color LeafTint;
```

> Extrinsic is the data that cannot be shared, like the position or the state. The amount of wood left if you can loot it, etc.
> 

> Now, thousands of trees can *share* one `TreeModel`, saving a potentially massive amounts of memory using it as the Intrinsic instance of the data.
> 

**`*[ Display this image ]*`**

![flyweight-tree-model.png](flyweight-tree-model.png)

> The Intrinsic Tree Data can be pass to the class upon creation, so its instance is created only once.
> 

```cpp
class Tree {
	Tree(TreeModel* newIntrinsicTreeData) : 
		IntrinsicTreeData(newIntrinsicTreeData) {}
		
	void SetExtrinsicValues(
		Vector Location,
    double Height,
    double Thickness,
    Color BarkTint,
    Color LeafTint);
```

---

> At first glance, this seems like simple asset reuse, kind of a stretch calling it a pattern.
> 

> But the Flyweight pattern becomes *really clever* when we apply it in places without obvious sharable pieces in the objects.
> 

**`*[ Display unimpressed meme ]*`**

![1624536388711-what-its-like-to-be-the-actual-face-of-disappointment.webp](1624536388711-what-its-like-to-be-the-actual-face-of-disappointment.webp)

---

> Let's look at another example: **terrain tiles for a world map**.
> 

> We could model terrain with an enum like this:
> 

```cpp
enum TerrainType {
    TERRAIN_GRASS,
    TERRAIN_HILL,
    TERRAIN_RIVER
```

> And a world map could contain something like this:
> 

```cpp
class World {
private:
    TerrainType Tiles[WIDTH][HEIGHT];
```

> Let’s say we want to check if a tile is water; we could do something like this:
> 

```cpp
bool World::IsWater(int x, int y) {
    switch (Tiles[x][y]) {
        case TERRAIN_GRASS: return false;
        case TERRAIN_HILL:  return false;
        case TERRAIN_RIVER: return true;
```

> **This is fine, It kinda works**... but it's clunky and a little awful to be honest.
> 

> Switches everywhere. Hard to extend. Messy when adding new behaviors.
> 

---

> SO! Instead of that, we create a new class for our Terrain type:
> 

```cpp
class Terrain {
public:
    Terrain(int moveCost, bool isWater, Texture texture)
        : MoveCost(moveCost), bIsWater(isWater), TerrainTexture(texture) {}

    bool isWater() { return bIsWater; }
    int getMoveCost() { return MoveCost; }

private:
    int MoveCost;
    bool bIsWater;
    Texture TerrainTexture;
```

> Attributes like the cost of moving through it or if is water will be stored here.
> 

> Along with the texture and any other element independent from the world.
> 

---

> Now, our world class can store *pointers* to shared Terrain objects instead of dumb enumerators inside an enum.
> 

```cpp
class World {
private:
    Terrain* Tiles[WIDTH][HEIGHT];
```

> Every grass tile points to the **same** `GrassTerrain` instance.
> 

> Every river tile will point to a **shared** `RiverTerrain` instance.
> 

> Much better. Clean. No switch statements.
> 

---

> In a super simple version, we can just create terrains upfront.
> 

> When creating the world, the terrain types can be already created and stored in within the world.
> 

```cpp
class World {
public:
    World() : GrassTerrain(1, false, GRASS_TEXTURE),
          HillTerrain(3, false, HILL_TEXTURE),
          RiverTerrain(2, true, RIVER_TEXTURE) {}

private:
    Terrain GrassTerrain;
    Terrain HillTerrain;
    Terrain RiverTerrain;
    Terrain* Tiles[WIDTH][HEIGHT];
```

> When generating the map, you just assign pointers to these shared terrains.
> 

> This can be done by a function following location instructions or by noise or something like that.
> 

> But that topic goes beyond the intention of this video so let’s avoid it.
> 

**`[ Display blind eye meme ]`**

> If you want even more flexibility, for example, dynamically creating terrains based on data files, you can create terrains **on demand** using a **Factory Method.**
> 

> That's another pattern that we'll explore in future videos so stay tuned.
> 

**`[ Display a subscribe meme ]`**

---

**`*[ Display ANYWAYS meme ]*`**

> Anyways. With this setup, querying terrain properties becomes super clean: Bravo!
> 

> No switch statements or asking stuff to the world. Everything shared and holding its own stuff.
> 

```cpp
int walkingCost = world.GetTile(2, 3).getMoveCost();
```

---

> Now, this is awesome and all but you might worry:
> 

**`*[ Display this image ]*`**

![maxresdefault.jpg](maxresdefault.jpg)

> "Isn't using pointers slower than using enums directly?". And you might sound like you should be right.
> 

> But in the tests from *Game Programming Patterns author*, **Flyweight objects were actually faster** than using enums, thanks to better data layout and fewer branch mispredictions.
> 

**`*[ Display this image ]*`**

![10-00.jpg](10-00.jpg)

> Sounds really convincing, but performance can vary depending on how your memory is organized, so it might not be always the case.
> 

> Always profile if you’re unsure, at least to see for yourself.
> 

**`*[ Insert some image here ]*`**

---

> In summary and to close already. If you find yourself using **enums and switch** statements everywhere, **consider using this pattern. The Flyweight Pattern**.
> 

> It will make your code: Easier to extend. Cleaner. Maybe faster.
> 

> And Much more memory efficient, since the point is sharing.
> 

> This sounds like a sponsorship from this pattern.
> 

> In any case. Thank you for watching and remember to like and subscribe and all that and good bie very good bie.
>
