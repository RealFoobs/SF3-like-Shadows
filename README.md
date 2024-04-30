A system to replace Ikemen's default shadows for rounder shadows similar to those present in Capcom games like SFIII. This is based of MFG user [inktrebuchet's mugen code](https://mugenguild.com/forum/topics/round-shadows-based-sf3-198646.0.html).

**To install**

1. Drop the contents of the repository in a new folder named `shadows` in `data\`.
2. Go to `save\` and open `config.json`
3. Near the beginning of the file you should see a block like this:
```
  "CommonFx": [],
```
Followed by this
```
	"CommonStates": [
		"data/action.zss",
		"data/dizzy.zss",
		"data/guardbreak.zss",
		"data/score.zss",
		"data/tag.zss",
		"data/training.zss"
	],
```
You just need to add my files so the engine can read them:


```
  "CommonFx": [
    "data/shadows/shadows.def"
  ],
```
```
	"CommonStates": [
		"data/action.zss",
		"data/dizzy.zss",
		"data/guardbreak.zss",
		"data/score.zss",
		"data/tag.zss",
		"data/training.zss",
		"data/shadows/config.zss",
		"data/shadows/shadows.zss"
	],
  ```
The order isn't very relevant, just make sure to include a comma if either file is NOT the last the last line on the block, and to ommit it if it isn't.

**Config Options**

TBD

**Adjusting Characters**

I tried to make this system work as well as I could, but due to the sandbox nature of mugen content I can't gurantee some things won't look off. The good part is that fixing stuff, if needed, can be relatively painless. Let's look at [Rera by Misao](https://misao-mugen.localinfo.jp/). This is how she looks on a clean download.

![alt text](https://raw.githubusercontent.com/RealFoobs/SF3-like-Shadows/main/Shadows%20Wrong.png)

Let's fix her issues one by one, starting with Rera herself. Her shadow is a bit too small for her stance, we can change this by going to Rera.def and adding a new section:

```[Map]
Shadow_Scale = 1.3
```
1.0 leave the character as it'd look without declaring shadow scale; 1.3 means the shadow is 30% wider and on the flipside 0.7 would mean 30% slimmer.

This only affects the "main body" of the character, we need to fix her helpers individually. By default helpers have much smaller shadows than playable characters and this works fine enough for projectiles, but for human-sized (or larger) helpers we need to give them their own scale.

Using debug mode (ctrl+d) we can check Rera's wolf "Shikuruu" starts on state 6000 (conveniently enough placed is in "Shikuruu.cns").

```;==========================================================================
;                            シクルゥ
;==========================================================================
;立ち
[Statedef 6000]
type = S
physics = S
velset = 0, 0
sprpriority = 5
anim = Ifelse(PrevStateNo=6010,6011,6000)

[State 6000, Shadow]; add this block right here
type = mapset
trigger1 = ishelper; 
map = "Shadow_Scale"
value = 1.5
```
Finally Rera's dagger is called "CHICHIUSHI" and it starts on state 197 (located on "Rera_A.cns"). Its sprite has the quirk of having a shadow drawn directly, so instead of changing the circular one drawn by the system, it'd be better to disable it entirely. You can do it this way:

```
;---------------------------------------------------------------------------
;武器
[Statedef 197]
type    = S
movetype= I
physics = S
ctrl = 0
velset = 0,0
anim = 197
sprpriority = 3

[State 197, No Shadow]; Add this block right here
type = assertspecial
trigger1 = 1
flag = noshadow
```

This is how Rera looks after our minor edits.

![alt text](https://raw.githubusercontent.com/RealFoobs/SF3-like-Shadows/main/Shadows%20Fixed.png)
