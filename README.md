# The Empty Party Scenario Bug
This bug is difficult to explain without spoilers. The best I can do, with very light spoilers is: This bug occurs if you attempt to recruit Riufan while in the middle of the scenario after Euchrisse.

**If you trigger this bug, you will be stuck with an empty party that you are unable to edit or add characters to. Battles auto-game over and is essentially a soft lock.**

Please not the below description INCLUDES SPOILERS.

# Directions
1) Switch to Nowa, save, close your game
2) In your game directory, browse to: EiyudenChronicle_Data\StreamingAssets\aa\StandaloneWindows64
3) Find a file named 3a52e2d360a274202f3dab4a30b75859.bundle. BACK IT UP. DO NOT LOSE THIS FILE.
4) Place the given 3a52e2d360a274202f3dab4a30b75859.bundle in this folder.
5) Load your game, swap to Seign, validate your party is correct and save.
6) Remove the modified 3a52e2d360a274202f3dab4a30b75859.bundle file, and restore your original file.
7) Continue playing

By having a proper party in Seign's group, you can restore the original file, and when the game uses the PartyStock system to save Seign's party, it will work correctly now. (As long as you don't trigger any other event like Riufan's that will erase it again!)

# Technical Details
There is an issue where recruiting a character like Riufan, which has code that backs up your party (into an object called a `StockParty`), sets up the 1v1 party, and then restores the `StockParty` back, causes conflicts with the code that swaps Nowa's and Seign's party. 

When you swap between the two groups, Nowa's party is saved in his `PersonalData` object, which includes a party object. Seign's party is also saved in his `PersonalData`. However, the game uses the StockParty system to snapshot it for some reason. The code removes and re-adds the two guest characters when swapping. Maybe they didn't want those guests to show up in Nowa's party selection screen, since they hadn't showed up yet?

Anyway: The problem is that an event like Riufan's recruitment deletes the `StockParty` object that holds Seign's party. So when you swap to Seign, it swaps to Seign's character and then attempts to restore the `StockParty`. But the `StockParty` is empty, so you end up with an empty party.

However, Seign's `PersonalData` object still has his party. (But, it doesn't hold the two guest characters, since the game removed them before swapping to Nowa.)

So, our tentative fix here is to:

A) Remove the use of the PartyStock system in this swap
B) Remove the "RemoveUnit" command for the guest characters, when swapping to Nowa
C) Add "PartyIn" command to force the two guest characters back into your party, if you happen to be loading an existing save.

Note: While testing, it was discovered that Seign's `PersonalData` object's party data can get messed up/removed if you attempt to send his party into a Guild Mission. To remedy this, an additional change was made:

D) Add "PartyIn" command for all units that are supposed to be in Seign's party. If they are already in the party, the command does nothing.

This bug has been reported to the developers. Hopefully a real fix will be available soon. Until then, should you happen to get stuck, please feel free to use this bug fix mod for the time being.
