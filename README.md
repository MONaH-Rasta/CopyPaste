# CopyPaste

Oxide plugin for Rust. Copy and paste your buildings to save them or move them

## Chat Commands

### Copy

```
/copy NAME options values - Copy a building
Example: /copy home radius 3 method building
Short example: /copy home r 3 m building
```

#### Syntax - Options

* **each true/false** - *default: true* - Check radius from each entity
* **method building/proximity** - *default: proximity* - Choose the type of mechanics to use to copy a building:\
**Building**: Only copy the current building.\
**Proximity**: Copy all blocks close to the building. (Some deployables can be missing with Building also use proximity in these cases)
* **radius XX** - *default: 3* - Sets the radius to search for entities around each building parts & deployables
* **share true/false** - *default: true* - Set to copy data CodeLocks, BuildingPrivileges, SleepingBag
* **tree true/false** - *default: false* - Set to copy trees and resources


### Paste

```
/paste NAME options values - Paste a building
Example: /paste home auth true stability false
Short example: /paste home a true s false
```

#### Syntax - Options

* **auth true/false** - *default: true* - Authorize player in all cupboards
* **blockcollision XX** - *default: 0* - Checks in XX radius if there is something that could collide with the new building, if so, blocks the build. 0 is to deactivate the detection.
* **deployables true/false** - *default: true* - Set to paste the deployables
* **height XX **- *default: 0 *- Adjust height to paste
* **autoheight true/false** - *default: true* - Wether or not to try to find best height for building
* **inventories true/false** - *default: true* - Set to paste the inventories
* **stability true/false** - *default: true* - Set false to ignore stability system
* **vending true/false** - *default: true* - Set to paste sellings, name and broadcasting for Vending Machine
* **entityowner true/false** - *default: true* - Copy entity ownership of building.
* **position x,y,z** - Override a position for spawning
* **rotation x** - *default: 0* - Change rotation correction

### Pasteback

```
/pasteback NAME options values - Paste on old place a building where it was when it was saved
Example: /pasteback home auth true stability false
Short example: /pasteback home a true s false
```

#### Syntax - Options

* **auth true/false** - *default: false* - Authorize player in all cupboards
* **deployables true/false** - *default: true* - Set to paste the deployables
* **inventories true/false **- *default: true* - Set to paste the inventories
* **height XX** - *default: 0* - Adjust height to pasteback
* **stability true/false** - *default: true* - Set false to ignore stability system
* **vending true/false** - *default: false* - Set to paste sellings, name and broadcasting for Vending Machine
* **position x,y,z** - Override a position for spawning
* **rotation x** - *default: 0* - Change rotation correction

### Other

/undo - Removes what you've last pasted
/copylist - List of stuctures (from folder oxide/data/copypaste)

## Permissions

* `copypaste.copy`
* `copypaste.list`
* `copypaste.paste`
* `copypaste.pasteback`
* `copypaste.undo`

## Configuration

```json
{
  "Amount of entities to paste per batch. Use to tweak performance impact of pasting": 15,
  "Amount of entities to copy per batch. Use to tweak performance impact of copying": 100,
  "Amount of entities to undo per batch. Use to tweak performance impact of undoing": 15,
  "Enable data saving feature": true,
  "Copy Options": {
    "Check radius from each entity (true/false)": true,
    "Share (true/false)": true,
    "Tree (true/false)": false
  },
  "Paste Options": {
    "Auth (true/false)": true,
    "Deployables (true/false)": true,
    "Inventories (true/false)": true,
    "Vending Machines (true/false)": true,
    "Stability (true/false)": true
  }
}
```

## Developer API

```csharp
object TryCopyFromSteamId(ulong userID, string filename, string[] args)
object TryPasteFromSteamId(ulong userID, string filename, string[] args)
object TryPasteFromVector3(Vector3 pos, float rotationCorrection, string filename, string[] args)
```

Returns string on failure and true on success

When Copy is done calls `OnCopyFinished(List<object> rawData, string filename, IPlayer player, Vector3 startPos)`

When Pasting is done calls `OnPasteFinished(List<BaseEntity> pastedEntities, string filename, IPlayer player, Vector3 startPos)`

**Example:**

```csharp
bool BuyBuilding(BasePlayer player, string buildingName)
{
    var options = new List<string>{ "blockcollision", "true" };

    var success = CopyPaste.Call("TryPasteFromSteamId", player.userID, buildingName, options.ToArray());

    if(success is string)
    {
        SendReply(player, "Can't place the building here");

        return false;
    }

    SendReply(player, "You've successfully bought this building");

    return true;
}
```

## Credits

* **Reneb**, the original author of this plugin
* **MiRror**, the previous maintainer of this plugin