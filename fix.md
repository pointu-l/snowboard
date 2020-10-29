# Les fixs pour un retro sans bugs
## Par Plume

### Combats

> Corriger les crashs pendant les combats
PacketFight.MoveToCell -> don't reinit pathfindingRetro
```csharp
    // exemple
    while (j <= num2)
	{
        if (this.IsHandToHand(pathfindingRetro.PathcellGone[j]))
        {
            // pathfindingRetro = new PathfindingRetro(...);
            text = Conversions.ToString(pathfindingRetro.pathing(this._mitm.PacketAnalyser._cellsRetro, list, this.GetFighter(this._mitm.PacketAnalyser.idperso).CellId, pathfindingRetro.PathcellGone[j], this._mitm.PacketAnalyser._width, false, true, this.PointsMovement));
```

### Inventaire

> Fix useItem
Inventory.UseItem -> use correct data source
```csharp
    // exemple

    // use retro "inventoryItem"
    foreach(uint num in this._mitm.PacketAnalyser.inventoryitem) {
        if (string.Concat(num) == ObjectIdName)
        {
            // use inventoryItem2 to get GUID
            result = (int)this._mitm.PacketAnalyser.inventoryitem2[num2];
        }
    }
```

### UI

> Mettre à jour jauge quand level UP
PacketAnalyser.OnNewLevel -> add this._mitm.Setlevel(this._level) call;
```csharp
    // exemple
    this._level = Conversions.ToInteger(param1);
    this._mitm.Setlevel(this._level); // add
```

### Map

> Fix la détection des soleils "bottom"
PacketAnalyser.onMapData -> fix double-sun-line on bottom of retro's maps
```csharp                       
    // exemple
    // before ...
    if (xcoord + num2 == 2 * this._height - 3)
    
    // ... after :
    int xRow = xcoord + num2;
    int ySize = 2 * this._height;
    if ((xRow == ySize - 3) || (xRow == ySize - 4))
```

> Fix la détection des soleils "left"
PacketAnalyser.onMapData -> fix double-sun-line on left of retro's maps
```csharp                       
    // exemple
    if (xcoord - 1 == num2 || xcoord - 2 == num2)
    {
	    if (!this._cellsChange.ContainsKey("left"))
		{
		    this._cellsChange.Add("left", i);
```

### Pathfinding

> Ne pas génerer de déplacements "bottom" hors de la map
>  (Pas un fix, juste un bypass. Un bug plus profond dans la gestion des maps est présent -> on ne devrait jamais aller au dela de 478 à la base)
PathfindingRetro.findpath -> wrap in anti-y-cell overflow condition
> Edit : detecté avant lajout du LGN44 dans loadSprites, peut-être plus nécessaire
```csharp
    // exemple
    else if (num3 <= 478) // add
    {
    	this.openlist.Add(num3);
    	this.openlist[this.openlist.Count - 1] = num3;
    	this.Glist[num3] = this.Glist[num2] + 5;
    	this.Hlist[num3] = Conversions.ToInteger(this.goalDistance(A.A(-539478863), num3, cell2));
    	this.Flist[num3] = this.Glist[num3] + this.Hlist[num3];
    	this.Plist[num3] = num2;
    } else if (num3 > 478) { //...
```

PathfindingRetro.loadSprites -> exclude lineOfSight & LGN44
```csharp
    // exemple
    if (mapHandler[num] != null)
    {
    	if (mapHandler[num].movement < 1 & mapHandler[num].active)
    	{
    		this.closelist.Add(num);
    	}
        else if (mapHandler[num].lineOfSight) // add
    	{
    		this.closelist.Add(num);
    	}
	else if (mapHandler[num].layerGroundNum == 44) // add
	{
		this.closelist.Add(num);
	}
```
