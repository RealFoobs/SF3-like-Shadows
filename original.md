***Info and Updates***
I've put together several options for round shadows that mimic Street Fighter 3's round shadows. These were all designed to be simple copy paste use, for perfect conversion you would probably want to reference a sprite table and not a universal math formula since some of the characters in SF3 don't stick to the same rules as others.

(Note: the original text had dead links to a precompiled sff and a preview image. The main repo has both of them)

***This goes in the characters Air file***
*If you don't have a blank animation already
```
; Blank
[Begin Action 9741]
-1,0, 0,0, -1
```

***This goes in [statedef -2] for players and in helpers for projectiles***
*for projectiles, AssertSpecial noshadow will need to be in every state the projectile uses.
```
[state -2, shadow] 
type = helper
trigger1 = !numhelper(33333399+ID)
name = "shadow"
ID = 33333399+ID
stateno = 33333399
postype = p1
ownpal = 1
keyctrl = 0
supermovetime = 2147483647
pausemovetime = 2147483647
ignorehitpause = 1
[State shadow] 
type = AssertSpecial
trigger1 = 1
flag = noshadow
ignorehitpause = 1
```

***Your pick goes in CNS or data/common1.cns***
***Version 1 | Scale based round shadows***
[Not how it’s actually done in sf3, this has smoother size transitions and only uses one sprite(8.png in the shadows download). There are some options in the 'scale' parameter of angledraw. You can use it as default and the shadow's size will be calculated based on the width in the character's constant or you can adjust your base shadow size manually. ]
```
;=========================================================================
;Scale based round shadows by Inktrebuchet
;=========================================================================
[Statedef 33333399]
type = A
movetype = I
physics = N
ctrl = 0
velset = 0, 0
anim = 9741 ; Blank Animation

[State 33333399, 1]
type = BindToParent
trigger1 = PlayerIdExist(parent,ID)
time = 1
facing = 1
pos =0,-Parent,Pos Y
ignorehitpause = 1 

[State 33333399, 2] 
type = RemoveExplod
trigger1 = 1
ignorehitpause = 1

[State 33333399, 3];Shadow for player
type = Explod
trigger1 = Time ;This is to correct a 1 frame delay in shadow pos when helper is first spawned
trigger1 = NumExplod(8530) = 0
trigger1 = PlayerIdExist(Parent,ID)
trigger1 = !Parent,IsHelper
id = 8530
anim = 8530
postype = p1
pos = 0,0
;scale = (.47 - (((.47/(96/cond(abs(Parent,Pos Y)+.0001 < 96, abs(Parent,pos Y)+.00001, 96)))))) + .6 + (.0) * Const(size.xscale), (.3 - ( ( (.3/(96/cond(abs(Parent,Pos Y)+.0001 < 96, abs(Parent,Pos Y)+.00001, 96)))))) + .8 + (.0) * Const(size.yscale) ;The last float number in () is to adjust base shadow size. This number should be the same for x and y.  
scale = ((.47 - (((.47/(96/cond(abs(Parent,Pos Y)+.0001 < 96, abs(Parent,pos Y)+.00001, 96)))))) + .6 )/(100.0/(Parent,Const(size.ground.back) + Parent,Const(size.ground.front))) * 2 * Const(size.xscale) ,  ((.3 - ( ( (.3/(96/cond(abs(Parent,Pos Y)+.0001 < 96, abs(Parent,Pos Y)+.00001, 96)))))) + .8)/(100.0/(Parent,Const(size.ground.back) + Parent,Const(size.ground.front))) * 2 * Const(size.yscale) ;Bases shadow size on character width
trans = addalpha
alpha =  128,128
removetime = -1
sprpriority = -4
ignorehitpause = 1

[State 33333399, 4];Shadow for projectile
type = Explod
trigger1 = Time ;This is to correct a 1 frame delay in shadow pos when helper is first spawned
trigger1 = NumExplod(8531) = 0
trigger1 = PlayerIdExist(Parent,ID)
trigger1 = Parent,IsHelper
id = 8530
anim = 8530
postype = p1
pos = 0,0
scale = (.47 - (((.47/(96/cond(abs(Parent,pos y)+.0001 < 96, abs(Parent,Pos Y)+.00001, 96)))))) + .6 + (-.3) * Const(size.xscale), (.3 - ( ( (.3/(96/cond(abs(Parent,Pos Y)+.0001 < 96, abs(Parent,Pos Y)+.00001, 96)))))) + .8 + (-.3) * Const(size.yscale) ;The last float number in () is to adjust base shadow size. This number should be the same for x and y.  
trans = addalpha
alpha =  128,128
removetime = -1
sprpriority = -4
ignorehitpause = 1

[State 33333399, 5] ;*This may need to be adjusted for some projectiles
type = RemoveExplod
trigger1 = !PlayerIdExist(parent,ID)
trigger2 = PlayerIdExist(parent,ID)
trigger2 = Parent, IsHelper
trigger2 = Parent, MoveContact
trigger2 = Parent, Movetype != A
ignorehitpause = 1

[State 33333399, 6]
type = DestroySelf
trigger1 = Time
trigger1 = !NumExplod(8530)
ignorehitpause = 1

;----------------------------
```

***Version 2 | Sprite based round shadows***
[This is the more accurate to sf3 version to use. Base shadow sprite can be changed in the 'anim' parameter of changeanim. ]
```
;=========================================================================
;Sprite based round shadows by Inktrebuchet 
;=========================================================================
[Statedef 33333399]
type = A
movetype = I
physics = N
ctrl = 0
velset = 0, 0
anim = 9741 ; Blank Animation

[State 33333399, 1]
type = BindToParent
trigger1 = PlayerIdExist(parent,ID)
time = 1
facing = 1
pos =0,-Parent,Pos Y
ignorehitpause = 1 

[State 33333399, 2] 
type = RemoveExplod
trigger1 = 1
ignorehitpause = 1

[State 33333399, 3];Shadow for projectile
type = Explod
trigger1 = Time ;This is to correct a 1 frame delay in shadow pos when helper is first spawned
trigger1 = NumExplod(8531) = 0
trigger1 = PlayerIdExist(Parent,ID)
trigger1 = !Parent,IsHelper
id = 8530
anim = 8530 + ceil(cond(abs(Parent,pos y) < 96, abs(Parent,pos y +.00001), 96) / 12)  + (7) ;The last number in () is to adjust base shadow size.
scale = Const(size.xscale), Const(size.yscale)
postype = p1
pos = 0,0
trans = addalpha
alpha =  128,128
shadow = -1
removetime = -1
sprpriority = -4
ignorehitpause = 1

[State 33333399, 4];Shadow for projectile
type = Explod
trigger1 = Time ;This is to correct a 1 frame delay in shadow pos when helper is first spawned
trigger1 = NumExplod(8531) = 0
trigger1 = PlayerIdExist(Parent,ID)
trigger1 = Parent,IsHelper
id = 8530
anim = 8550 ;Projectiles only have one shadow size in sf3
scale = Const(size.xscale), Const(size.yscale)
postype = p1
pos = 0,0
trans = addalpha
alpha =  128,128
shadow = -1
removetime = -1
sprpriority = -4
ignorehitpause = 1

[State 33333399, 5] ;*This may need to be adjusted for some projectiles
type = RemoveExplod
trigger1 = !PlayerIdExist(parent,ID)
trigger2 = PlayerIdExist(parent,ID)
trigger2 = Parent, IsHelper
trigger2 = Parent, MoveContact
trigger2 = Parent, Movetype != A
ignorehitpause = 1

[state 33333399, 6]
type = DestroySelf
trigger1 = Time
trigger1 = !NumExplod(8530)
ignorehitpause = 1

;----------------------------

```

***Old versions you shouldn't care about because compatibility is important:***

helper based, compatible with 1.1b stages but can't use fight.sff sprites so harder to add to all characters

***This goes in [statedef -2] for players and in helpers for projectiles***
```
[state -2, shadow] 
type = helper
trigger1 = !numhelper(33333399+ID)
name = "shadow"
ID = 33333399+ID
stateno = 33333399
postype = p1
ownpal = 1
keyctrl = 0
supermovetime = 2147483647
pausemovetime = 2147483647
ignorehitpause = 1
[State shadow]
type = AssertSpecial
trigger1 = 1
flag = noshadow
ignorehitpause = 1
```

***Your pick goes in CNS***
***Version 1 | Scale based round shadows***
[Not how it’s actually done in sf3, this has smoother size transitions and only uses one sprite(8.png in the shadows download). There are some options in the 'scale' parameter of angledraw. You can use it as default and the shadow's size will be calculated based on the width in the character's constant or you can adjust your base shadow size manually. ]
```
;=========================================================================
;Scale based round shadows by Inktrebuchet
;=========================================================================
[Statedef 33333399]
type = A
movetype = I
physics = N
ctrl = 0
velset = 0, 0
anim = 8537 ;base shadow sprite. This one was chosen because it is between the largest and smallest size. (100px)
sprpriority = -4

[State 33333399, 0] ;This is to correct a 1 frame delay in shadow pos
type = AssertSpecial
trigger1 = !time
flag = invisible
ignorehitpause = 1

[State 33333399, 1]
type = AssertSpecial
trigger1 = 1
flag = noshadow
ignorehitpause = 1

[State 33333399, 2] ;player shadow
type = angledraw
trigger1 = PlayerIdExist(Parent,ID)
trigger1 = !Parent,ishelper 
;scale = (.47 - ( ( (.47/(96/cond(abs(Parent,pos y)+.0001 < 96, abs(Parent,pos y)+.00001, 96)))))) + .6 + (.0), (.3 - ( ( (.3/(96/cond(abs(Parent,pos y)+.0001 < 96, abs(Parent,pos y)+.00001, 96)))))) + .8 + (.0) ;The last float number in () is to adjust base shadow size. This number should be the same for x and y.  
scale = ((.47 - ( ( (.47/(96/cond(abs(Parent,pos y)+.0001 < 96, abs(Parent,pos y)+.00001, 96)))))) + .6 )/(100.0/(Parent,const(size.ground.back) + Parent,const(size.ground.front))) * 2 ,  ((.3 - ( ( (.3/(96/cond(abs(Parent,pos y)+.0001 < 96, abs(Parent,pos y)+.00001, 96)))))) + .8)/(100.0/(Parent,const(size.ground.back) + Parent,const(size.ground.front))) * 2 ; bases shadow size on character width
ignorehitpause = 1

[State 33333399, 3] ;projectile shadow
type = angledraw
trigger1 = PlayerIdExist(Parent,ID)
trigger1 = Parent,ishelper
scale = (.47 - ( ( (.47/(96/cond(abs(Parent,pos y)+.0001 < 96, abs(Parent,pos y)+.00001, 96)))))) + .6 + (-.3), (.3 - ( ( (.3/(96/cond(abs(Parent,pos y)+.0001 < 96, abs(Parent,pos y)+.00001, 96)))))) + .8 + (-.3) ;The last float number in () is to adjust base shadow size. This number should be the same for x and y.  
ignorehitpause = 1

[state 33333399, 4]
type = Trans
trigger1 = 1
trans = addalpha
alpha = 128, 128
ignorehitpause = 1

[state 33333399, 5] ;*this may need to be adjusted for some projectiles
type = destroyself
trigger1 = !PlayerIdExist(parent,ID)
trigger2 = PlayerIdExist(parent,ID)
trigger2 = parent, ishelper
trigger2 = parent, movecontact
trigger2 = parent, movetype != A
ignorehitpause = 1

[State 33333399, 6]
type = BindToParent
trigger1 = PlayerIdExist(Parent,ID)
time = 1
facing = 1
pos =0,-Parent,pos y
ignorehitpause = 1 

;----------------------------
```

***Version 2 | Sprite based round shadows***
[This is the more accurate to sf3 version to use. Base shadow sprite can be changed in the 'anim' parameter of changeanim. ]
```
;=========================================================================
;Sprite based round shadows by Inktrebuchet 
;=========================================================================
[Statedef 33333399]
type = A
movetype = I
physics = N
ctrl = 0
velset = 0, 0
sprpriority = -4

[State 33333399, 0] ;This is to correct a 1 frame delay in shadow pos
type = AssertSpecial
trigger1 = !time
flag = invisible
ignorehitpause = 1

[State 33333399, 1]
type = AssertSpecial
trigger1 = 1
flag = noshadow
ignorehitpause = 1

[State 33333399, 2] ;player shadow
type = changeanim
trigger1 = PlayerIdExist(Parent,ID)
trigger1 = !parent, ishelper
value = 8530 + ceil(cond(abs(Parent,pos y) < 96, abs(Parent,pos y +.00001), 96) / 12)  + (7) ; The last number in () is to adjust base shadow size.
ignorehitpause = 1

[State 33333399, 3] ;projectile shadow
type = changeanim
trigger1 = PlayerIdExist(Parent,ID)
trigger1 = parent, ishelper
value = 8550 ; projectiles only have one shadow size in sf3
ignorehitpause = 1

[state 33333399, 4]
type = Trans
trigger1 = 1
trans = addalpha
alpha = 128, 128
ignorehitpause = 1

[state 33333399, 5] ;*this may need to be adjusted for some projectiles
type = destroyself
trigger1 = !PlayerIdExist(parent,ID)
trigger2 = PlayerIdExist(parent,ID)
trigger2 = parent, ishelper
trigger2 = parent, movecontact
trigger2 = parent, movetype != A
ignorehitpause = 1

[State 33333399, 6]
type = BindToParent
trigger1 = PlayerIdExist(Parent,ID)
time = 1
facing = 1
pos =0,-Parent,pos y
ignorehitpause = 1 

;----------------------------
```

[no helper, only explod. no projectile support and not compatible with 1.1b stages with zoom!]

***Your pick goes in [statedef -2]***
***Version 1 | Scale based round shadows***
[Not how it’s actually done in sf3, this has smoother size transitions and only uses one sprite(8.png in the shadows download). There are some options in the 'scale' parameter of modifyexplod. You can use it as default and the shadow's size will be calculated based on the width in the character's constant or you can adjust your base shadow size manually. ]

```
;Scale based round shadows by Inktrebuchet
[State shadow 1]
type = AssertSpecial
trigger1 = 1
flag = noshadow
ignorehitpause = 1

[state shadow 2]
type = explod
trigger1 = numexplod(8530) = 0
id = 8530
anim = (8530 + 07) ;<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< this sprite is 100px wide
postype = left
pos = ceil(screenpos x), ceil(screenpos y) - ceil(pos y)
removetime = -1
bindtime = -1
sprpriority = -4
ignorehitpause = 1

[state shadow 3]
type = modifyexplod
trigger1 = numexplod(8530) = 1
id = 8530
postype = left
pos = ceil(screenpos x), ceil(screenpos y - pos y)
;scale = (.47 - ( ( (.47/(96/cond(abs(pos y)+.0001 < 96, abs(pos y)+.00001, 96)))))) + .6 + (.0), 1 ;The last float number in () is to adjust base shadow size.  
scale = ((.47 - ( ( (.47/(96/cond(abs(pos y)+.0001 < 96, abs(pos y)+.00001, 96)))))) + .6 )/(100.0/(const(size.ground.back) + const(size.ground.front))) * 2 , 1 ; base shadow on character's width
trans = addalpha
alpha = 128,128
sprpriority = -4
ignorehitpause = 1
```

***Version 2 | Sprite based round shadows***
[This is the more accurate to sf3 version to use. Base shadow sprite can be changed in the 'anim' parameter of the explod. ]
```
;Sprite based round shadows by Inktrebuchet 
[State 2000, Assert]
type = AssertSpecial
trigger1 = 1
flag = noshadow
ignorehitpause = 1

[state shadow]; 
type = removeexplod
trigger1 = numexplod(8530)
id = 8530
ignorehitpause = 1

[state 6000, Effect]
type = explod
trigger1 = numexplod(8530) = 0
ID = 8530
anim = 8530 + (7) + ceil(cond(abs(pos y) < 96, abs(pos y +.00001), 96) / 12) ; (7) is the base shadow size
postype = left
pos = ceil(screenpos x), ceil(screenpos y - pos y)
bindtime = 1
removetime = -1
ownpal = 1
sprpriority = -4
ignorehitpause = 1
```
