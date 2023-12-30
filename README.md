
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
		"data/shadows/shadows.zss"
	],
  ```
  The order isn't very relevant, just make sure to include a comma if either file is NOT the last the last line on the block, and to ommit it if it isn't.
