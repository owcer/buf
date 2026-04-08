# buf

a Simple list of bloxd utilities provided by `Ocelote`, for free!

> [!NOTE]
> This is all licensable under CC0, hence no need to credit me (you can credit me if you want though :D)
### rcf - RayCast Far

This function works just like the regular function `api.raycastForBlock`, except it can go far (usually till the edge of the render distance).

One thing to be aware of: if a chunk is not loaded then blocks within it will count as `Unloaded`, NOT `Air`, so it will return the block coords at the position of the unloaded blocks.However, you can get around this by checking the block at the returned position and doing the same action as for what you would do if it returned null (i.e. was Air).

Code:

```js
function rcf(r,l){for(let t=0;t<100;t++){let a=api.raycastForBlock(r,l);if(null!=a)return a;r=[r[0]+5*l[0],r[1]+5*l[1],r[2]+5*l[2]]}return null}
```

### scp - Set Custom Pose

Usage: `setCustomPose(playerId, poseDescription)`

How to use: `setCustomPose(myId, "Left arm up")setCustomPose(myId, "Both Legs Forward, Right arm upward")`  
`setCustomPose(myId, "both leg front, both hands up")`

By the way, arms cannot be perpendicular, and same with legs

Code:

```js
setCustomPose=function(e,l){let r=api.getOtherEntitySetting(e,e,"meshScaling"),a,_,t,g,s,o;a=r.HeadMesh??[1,1,1],_=r.TorsoNode??[1,1,1],t=r.ArmLeftMesh??[1,1,1],g=r.ArmRightMesh??[1,1,1],s=r.LegLeftMesh??[1,1,1],o=r.LegRightMesh??[1,1,1];let c=l.toLowerCase().split(",");for(let p=0;p<c.length;p++)c[p]=c[p].trim(),c[p]=c[p].replace(/pointing/g,""),c[p]=c[p].replace(/point/g,""),c[p]=c[p].replace(/wards/g,""),c[p]=c[p].replace(/ward/g,""),c[p]=c[p].replace(/to the/g,""),c[p]=c[p].replace(/in the/g,""),c[p]=c[p].replace(/to/g,""),c[p]=c[p].replace(/\s+/g," "),c[p]=c[p].replace(/hand/g,"arm"),c[p]=c[p].replace(/the sky/g,"up"),c[p]=c[p].replace(/sky/g,"up"),c[p]=c[p].replace(/ground/g,"floor"),c[p]=c[p].replace(/the floor/g,"down"),c[p]=c[p].replace(/floor/g,"down"),c[p]=c[p].replace(/for/g,"front"),c[p]=c[p].replace(/down/g,"0"),c[p]=c[p].replace(/front/g,"1"),c[p]=c[p].replace(/up/g,"2"),c[p]=c[p].replace(/back/g,"3"),c[p]=c[p].replace(/left arm/g,"la"),c[p]=c[p].replace(/right arm/g,"ra"),c[p]=c[p].replace(/both arms/g,"ba"),c[p]=c[p].replace(/both arm/g,"ba"),c[p]=c[p].replace(/left leg/g,"ll"),c[p]=c[p].replace(/right leg/g,"rl"),c[p]=c[p].replace(/both legs/g,"bl"),c[p]=c[p].replace(/both leg/g,"bl");let h={la:0,ra:0,ll:0,rl:0};for(let i=0;i<c.length;i++){let n=c[i].slice(0,2);"ba"===n?(h.la=+c[i].slice(3,4),h.ra=+c[i].slice(3,4)):"bl"===n?(h.ll=+c[i].slice(3,4),h.rl=+c[i].slice(3,4)):h[n]=+c[i].slice(3,4)}if((h.la+h.ra)%2==1)throw"Arms cannot be perpendicular";if((h.ll+h.rl)%2==1)throw"Legs cannot be perpendicular";let b=!!(h.la%2),f=!!(h.ll%2),d=h.la>1?[1,-1,-1]:[1,1,1],$=h.ra>1?[1,-1,-1]:[1,1,1],m=h.ll>1?[1,-1,-1]:[1,1,1],M=h.rl>1?[1,-1,-1]:[1,1,1];t=[Math.abs(t[0])*d[0],Math.abs(t[1])*d[1],Math.abs(t[2])*d[2]],g=[Math.abs(g[0])*$[0],Math.abs(g[1])*$[1],Math.abs(g[2])*$[2]],s=[Math.abs(s[0])*m[0],Math.abs(s[1])*m[1],Math.abs(s[2])*m[2]];let L={HeadMesh:a,TorsoNode:_,ArmLeftMesh:t,ArmRightMesh:g,LegLeftMesh:s,LegRightMesh:o=[Math.abs(o[0])*M[0],Math.abs(o[1])*M[1],Math.abs(o[2])*M[2]]};for(let w of Object.keys(L)){let u=L[w];api.log(u),api.log(r[w]),api.log(w),r[w],1===u[0]&&1===u[1]&&1===u[2]&&delete L[w]}api.setPlayerPose(e,b?f?"driving":"zombie":f?"sitting":"standing"),api.scalePlayerMeshNodes(e,L)};

```

### ulb - Update LeaderBoard

Usage: `ulb(leaderBoard)`.

How to use: The leaderBoard object will contain keys as columns, then each value will be `[columnName, functionThatTakesInPlayerIdAndOutputsAValue,sortPriority]`. Moreover, `color`, `nameTag`, `subTag`, `nameColor`, `subColor` are special keys which won’t be considered a column. Also, smaller number = more priority, hence it is recommended to use negatives, as pfp and name have 1 and 0.

You can test it by putting these in world code right after:

```js
leaderBoard = {
id:["id",(pid)=>(pid),1],
dbId:api.getPlayerDbId,
aura:["Aura Level",plId=>api.getAuraInfo(plId).level,-1],
color:()=>(Math.random()>0.5?"orange":"yellow"),
nameTag:api.getEntityName,
subTag:"cool person",
nameColor:"yellow",
subColor:"yellow"
}
onPlayerJoin=()=>{ulb(leaderBoard)}
```

or even just:

```js
leaderBoard = {
color:"gold",
nameColor:"gold",
subColor:"gold"
}
onPlayerJoin = (playerId) => {
ulb(leaderBoard)
}
```

Code:

```js
ulb=function(e){if("object"!=(objType=typeof e))throw"Error: ulb expected object, but received "+objType+" ('"+e+"')";let{color:t=e=>"default",nameTag:o,nameColor:r,subTag:n,subColor:a,...i}=e;api.getPlayerIds().forEach(e=>{let y={},d={pfp:{displayName:"",sortPriority:1},name:{displayName:"Name",sortPriority:0}},f=Object.keys(i),l=Object.values(i);for(let s=0;s<f.length;s++){let p=l[s];Array.isArray(p)||(p=[""+f[s],p,s]),"function"==typeof p[1]?y[f[s]]=p[1](e):y[f[s]]=p[1],d[f[s]]={displayName:p[0],sortPriority:-p[2]}}void 0!==o&&("function"==typeof o&&(o=o(e)),"object"!=typeof o&&(o=[{str:o+""}])),void 0!==n&&("function"==typeof n&&(n=n(e)),"object"!=typeof n&&(n=[{str:n+""}])),void 0!==r&&("function"==typeof r&&(r=r(e)),"string"!=typeof r&&(r+="")),void 0!==a&&("function"==typeof a&&(a=a(e)),"string"!=typeof a&&(a+="")),api.setTargetedPlayerSettingForEveryone(e,"colorInLobbyLeaderboard","function"==typeof t?t(e):t+"",!0),api.setTargetedPlayerSettingForEveryone(e,"lobbyLeaderboardValues",y,!0),api.setTargetedPlayerSettingForEveryone(e,"nameTagInfo",{content:o,subtitle:n,backgroundColor:r,subtitleBackgroundColor:a},!0),api.setClientOption(e,"lobbyLeaderboardInfo",d)})};
```

### ats - Add To Shop

Instructions:

You need to call the ats function once, in world code, so you can set it up. You can also use this in a Code Block for easier testing.

The first argument is just the category key, which is basically the name of the category. The second argument is an array of "trades".

A "trade" specifies what items are given to the player, and what items are required and taken away. A positive number indicates that an item will be given to the player, and a negative number indicates it will be taken away.

Two numbers indicate a range. For example, 1, 3, "Iron Bar" means the player will get 1-3 Iron Bars (both inclusive). Multiple numbers before an item indicate ranges.

For example, 1, 10, 100, 1000, 10000, 100000, "Orange Tulip" means that the player will get 1-10, 100-1000, or 10000-100000 Orange Tulips, with each range having a 1 in 3 chance of being selected. This means the player can never get, say, 50 Orange Tulips, since that does not fall into one of the ranges.

When an odd number of numbers are provided, the last one will wrap to itself. For example, 1, 10, 100, "Coal" means the player will either get 1-10 Coal, or exactly 100 Coal, both outcomes having a 50/50 probability.

Test Code Block:

```js
ats("Shop",[
[1,"Watermelon",-50,"Gold Coin"],
[-1,"Watermelon",50,"Gold Coin"],
[1,3,"Iron Bar"],
[1,10,100,"Coal"],
//in case of differing signs, only the sign of the first number counts (as seen in "Gold Bar"'s amounts):
[1,10,100,1000,10000,100000,"Orange Tulip",-1,-2,"Diamond",-1,2,"Gold Bar"],
["Apple"], //1 is the default number when none are provided
[1,2,3,"Invalid yay"] //will throw an error due to invalid item name to help catch errors early
])
```

Code:

```js
ats=(e,t)=>{for(let o=0;o<t.length;o++){let n=t[o],r=[],a=[],m=[];for(let i=0;i<n.length;i++)if("number"==typeof n[i])m.push(n[i]);else if("string"==typeof n[i]){let l=n[i];0===m.length&&(m=[1]);let s=m[0]<0,f=m.map(e=>Math.abs(e)).sort((e,t)=>e-t),g=[];for(let u=0;u<f.length;u+=2)u+1<f.length?g.push([f[u],f[u+1]]):g.push([f[u],f[u]]);s?a.push({item:l,ranges:g}):r.push({item:l,ranges:g}),m=[]}let h=e=>e.map(e=>e[0]===e[1]?e[0]:e[0]+"-"+e[1]).join(" or "),$=r.map(e=>h(e.ranges)+" "+e.item).join(", "),p=a.map(e=>h(e.ranges)+" "+e.item).join(", "),c="";$&&p?c=`Trade ${p} for ${$}`:$?c=`Receive ${$} for free`:p&&(c=`Throw away ${p}`);let d=r.length>0?r[0].item:"",v=a.length>0?a[0].item:"",I="Get";a.length>0?I=r.length>0?[{icon:v},{str:h(a[0].ranges)+" Trade"}]:[{icon:v},{str:h(a[0].ranges)+" Discard"}]:r.length>0&&(I=[{icon:d},{str:" Get"}]),r.length+a.length===0&&(I="Okay",c="Do nothing"),api.createShopItem(e,o+"",{image:d||v||"Unknown",canBuy:!0,amount:1,buyButtonText:I,description:c,onBoughtMessage:[{str:"Loading..."},{str:JSON.stringify({gives:r,takes:a}),style:{fontSize:"0px"}}],sell:!1,sortPriority:0,customTitle:"Trade "+(o+1)})}},onPlayerBoughtShopItem=(e,t,o,n,r)=>{let a;try{a=JSON.parse(n.onBoughtMessage[1].str)}catch(m){return api.sendOverShopInfo(e,"Invalid metadata. Please report to owner.")}let i=e=>{let t=e[Math.floor(Math.random()*e.length)];return Math.floor(Math.random()*(t[1]-t[0]+1))+t[0]},l=e=>Math.max.apply(null,e.flat()),s=a.takes.map(e=>({item:e.item,amount:i(e.ranges),max:l(e.ranges)})).filter(e=>e.max>0),f=a.gives.map(e=>({item:e.item,amount:i(e.ranges)})).filter(e=>e.amount>0);for(let g of s){let u=api.getInventoryItemAmount(e,g.item);if(u<g.max)return api.sendOverShopInfo(e,[{str:"You need "+(g.max-u)+" more"},{icon:g.item},{str:g.item+"."}])}for(let h of s)api.removeItemName(e,h.item,h.amount);let $=[],p=!1;for(let c of f){let d=api.giveItem(e,c.item,c.amount);if(d>0&&$.push({item:c.item,amount:d}),d<c.amount){p=!0;break}}if(p){for(let v of $)api.removeItemName(e,v.item,v.amount);for(let I of s)api.giveItem(e,I.item,I.amount);return api.sendOverShopInfo(e,"Inventory too full!")}api.sendOverShopInfo(e,"Purchase Successful!")};
```

### dnt - Do Next Tick

You can call `dnt(()=>api.log(1))` or `dnt(cb1,cb2,cb3,cb4...)` or `dnt([cb1,cb2,cb3...])` etc, and dnt will reliably handle everything for you, including assigning tasks to the next tick, and handling the breakdown of the array also within the tick. Make sure to put `dnt.tick()` at the top of your tick callback.

You can also force load a chunk, by instead of just putting a `callback`, you can use the format `[callback,[x,y,z]]`, and it will make sure the chunk at x,y,z is loaded before executing callback, and it respects your order of tasks, but it looks ahead to the next tasks to load their chunks to save time.

100% interruption safe - as long as the function call itself doesn't get interrupted, your code is perfectly guaranteed to not interrupt.

```js
(dnt=(...e)=>{dnt.u[dnt.u.e++]=[e,"undefined"!=typeof myId?myId:playerId]}).t={s:0,e:0},dnt.u={s:0,e:0,p:0},dnt.p=0,dnt.tick=()=>{for(;dnt.u.s<dnt.u.e;){let e=dnt.u[dnt.u.s];if(!e){dnt.u.s++,dnt.u.p=0;continue}for(;dnt.u.p<e[0].length;){let t=e[0][dnt.u.p];if(Array.isArray(t)&&!("function"==typeof t[0]&&(1==t.length||2==t.length&&"function"!=typeof t[1]))){e[0].splice(dnt.u.p,1,...t);continue}dnt.t[dnt.t.e++]=[t,e[1]],dnt.u.p++}delete dnt.u[dnt.u.s++],dnt.u.p=0}dnt.p<dnt.t.s&&(dnt.p=dnt.t.s);let s=0;for(;dnt.p<dnt.t.e&&!(s++>20);){let $=dnt.t[dnt.p];if($&&Array.isArray($[0])&&2==$[0].length&&Array.isArray($[0][1])&&3==$[0][1].length){let n=$[0][1];api.isBlockInLoadedChunk(n[0],n[1],n[2])||api.getBlockId(n[0],n[1],n[2])}dnt.p++}for(;dnt.t.s<dnt.t.e;){let u=dnt.t[dnt.t.s];if(!u){dnt.t.s++;continue}let i=u[0];if(Array.isArray(i)){if(2==i.length&&Array.isArray(i[1])&&3==i[1].length&&"number"==typeof i[1][0]&&!api.isBlockInLoadedChunk(i[1][0],i[1][1],i[1][2])){api.getBlockId(i[1][0],i[1][1],i[1][2]);return}i=i[0]}let r,f="#2eeb82",l=u[1];try{myId=l,playerId=l,void 0!==(r=i())&&(r=JSON.stringify(r)),r&&(r="Result: "+r)}catch(o){r="dntError: "+o.name+": "+o.message,f="#ff9d87"}if(void 0!==r){if(l)try{api.sendMessage(l,r,{color:f})}catch(p){}else try{("undefined"==typeof error||error)&&api.broadcastMessage(r,{color:f})}catch(_){}}delete dnt.t[dnt.t.s++]}dnt.t.s>=dnt.t.e&&(dnt.t={s:0,e:0},dnt.p=0),dnt.u.s>=dnt.u.e&&(dnt.u={s:0,e:0,p:0})};
```

### fll - Fast Long Log

Usage:
`fll("message")`  
`fll(Object.keys(api))`

Code:
```js
fll=e=>{let f=("Log: "+JSON.stringify(e)).match(/.{1,2000}/g);if(myId||playerId)for(let o of f)api.sendMessage(myId||playerId,o,{color:"#cef3ff"});else for(let s of f)api.broadcastMessage(s,{color:"#cef3ff"})};
```

### pst - Player STorage

Usage:

`setPermKey(playerId,key,val,index)`  
`getPermKey(playerId,key,index)`  
`setPerm(playerId,object,index)`  
`getPerm(playerId,index)`

Note: limited to 36 slots (minimum index is 0, maximum is 35)

Code:
```js
pst={setPerm:function(t=myId,e={},o=0){api.setMoonstoneChestItemSlot(t,o,"Ice",1,{customDescription:JSON.stringify(e)})},getPerm:function(t=myId,e=0){return JSON.parse(api.getMoonstoneChestItemSlot(t,e)?.attributes?.customDescription??"{}")},setPermKey:function(t=myId,e,o,s=0){let n=JSON.parse(api.getMoonstoneChestItemSlot(t,s)?.attributes?.customDescription??"{}");void 0===o?delete n[e]:n[e]=o,api.setMoonstoneChestItemSlot(t,s,"Ice",1,{customDescription:JSON.stringify(n)})},getPermKey:function(t=myId,e,o=0){return JSON.parse(api.getMoonstoneChestItemSlot(t,o)?.attributes?.customDescription??"{}")[e]}};
```

### gfi - Give Fake Item
What does this do? tbh idk. figure it out. for starters, it gives unstackable+uninteractible items.

Code:

```js
gfi=(t,e,l)=>{l=Math.min(l,46),e=api.getInitialItemMetadata(e).id+"";for(let a=0,i=0;a<l&&i<46;i++){let m=api.getItemSlot(t,i);api.log(m),!m&&(api.setItemSlot(t,i,e),a++)}};
```

### lcb - Load Code Blocks

Edit the parts labeled `//EDIT THIS PART.` (there's two, the location of your code blocks, and what callbacks you want to exist. Note that tick and onPlayerJoin will exist even if you don't need them to but this should be fine for the most part).

Use callbacks like `tick=()=>{code}` and not `function tick(){code}`. Why? functions are block scoped.

Supports multiple callbacks of the same name - same as order of blocks provided, if there are multiple callbacks in one code block, it will simply execute them in the order they were defined it.

Code:

```js
//lcb-load code blocks-licensable under CC0-no credit required-made by Ocelote
//note: use callbacks like tick=()=>{code goes here}
//NOT like function tick(){code shouldn't go here}
let lcb={j:[],a:false,d:[],e:0,f:0,g:0,h:0,k:0,l:0,v:{},s:false}
onPlayerJoin=function t(p){return(lcb.a&&(t!==onPlayerJoin))?onPlayerJoin(p):lcb.j[lcb.j.length]=p}
tick=function t(m){
if(lcb.a)return globalThis.tick&&(tick!==t)&&tick(m)
while(lcb.d.length<lcb.b.length){
let pos = lcb.b[lcb.d.length]
let block = api.getBlock(pos)
if(block==="Unloaded")return
lcb.d.push(api.getBlockData(pos[0],pos[1],pos[2])?.persisted?.shared?.text??"")
}
for(;lcb.k<lcb.c.length;lcb.k++){
let n = lcb.c[lcb.k]
let h = {}
h[n]=[]
Object.defineProperty(globalThis, n, {
set: f=>(typeof f==="function"&&h[n].push(f)),
get: () => (...a) => {
let r = lcb.v[n]
let l = h[n].length
let s = h[n]
for (let i = 0; i < l; i++) {
r = s[i](...a)
}
return r
},
configurable: true,
enumerable: true
})
delete lcb.c[lcb.k]
}
for(;lcb.h<lcb.d.length;lcb.h++){
try{
if(!lcb.s){
let s = api.setCallbackValueFallback
api={...api,setCallbackValueFallback: (c,f) => {
lcb.v[c]=f
s(c,f)
}
}
lcb.s = true
}
(0,eval)(lcb.d[lcb.h])
}catch(e){
api.broadcastMessage("Error in block "+lcb.b[lcb.h]+": "+e.name+": "+e.message)
}
delete lcb.d[lcb.h]
}
lcb.f=lcb.f||lcb.j.length
if(typeof onPlayerJoin==='function'){
for(;lcb.g<lcb.f;lcb.g++){
onPlayerJoin(lcb.j[lcb.g])
delete lcb.j[lcb.g]
}
}
lcb.a=true
}
lcb.b=
//EDIT THIS PART.
//put the list of your code block locations here:
[[3,1,4],[1,5,9],[9,2,6]]

lcb.c=
//EDIT THIS PART.
//put the list of callbacks you will be using here (tick and onPlayerJoin will exist regardless for simplicity):
["tick","onPlayerJoin","onPlayerJump","onPlayerChat"]

for(;lcb.l<lcb.c.length;lcb.l++){
let v = lcb.c[lcb.l]
if(v==="tick"||v==="onPlayerJoin")continue
globalThis[v]=function f(...e){let n=globalThis[v];if(n&&(n!==f))return n(...e);else return lcb.v[v]};
}
```

### bim - Big Index Memory

Usage:

`bim.set(1,"bloxd",api.log`  
`bim.get(1,api.log)`

A storage utility object called bim, which let's you store 20 zettabytes of data. How this works is you get ~5 quintillion slots of storage, and each slot can hold 1976 characters of data. Use strings if you need indices greater than 9 quadrillion (because js max safe integer limit). Also it has a built in rate limit which matches the api rate limit. This is a low-level abstraction. You should be able to build higher level stuff on this ( hash tables, checksums, pointers, etc.).

Code:

```js
//"bim" stands for "big index memory"
//20 zetabytes of memory!
//You get 5 quintillion slots of data!
//Use strings for big indices (bigger than 9 quadrillion)
//two easy functions: bim.set(index,value,callback) and bim.get(index,callback)
//uses bloxd's practically only storage option: block data

//probably don't modify this part unless your main world area occupies that space or if players can get there. Also DO NOT MODIFY once the code has gone into prodcution
const BASE = [-3.5e5,-3.5e5,-3.5e5]
const MAX_X = 700000
const MAX_Y = 300000
const MAX_Z = 700000
//probably don't modify this part unless Sticks become stackable again or the Slot count gets bumped (probably not happening anytime soon)
const ITEM = "Stick"
const SLOTS = 36
//modify this if description limit gets bumped (which might happen soon)
const MAX_DESC = 2000-24 //-24 because 2000 is the limit of the whole JSON not just the text
//modify this if rate limit gets bumped
const MAX_OPS = 3
const MAX_CHARS_PER_MIN = 40000 //limit decided by game devs
const LIMIT_WAIT = 30 //in ticks, time to wait when rate limit is hit
//modify this if ticks per second gets change
const TICKS_PER_SECOND = 20
//modify this if max object/array size gets increased
const MAX_CHEST_CACHE_SIZE = 200; //caches the contents of entire chests
const MAX_RAM_CACHE_SIZE = 2000; //caches individual slot values (the 'main' cache)
//do not edit anything below this (unless you know what you are doing)
class LRUCache {
constructor(maxSize) {
this.maxSize = maxSize;
this.cache = new Map();
}
get(key) {
if (!this.cache.has(key)) {
return undefined;
}
const value = this.cache.get(key);
this.cache.delete(key);
this.cache.set(key, value);
return value;
}
set(key, value) {
if (this.cache.has(key)) {
this.cache.delete(key);
} else if (this.cache.size >= this.maxSize) {
const leastRecentlyUsedKey = this.cache.keys().next().value;
this.cache.delete(leastRecentlyUsedKey);
}
this.cache.set(key, value);
}
}
const MAX_CHARS_PER_TICK = MAX_CHARS_PER_MIN/(TICKS_PER_SECOND*60)
// task format: [boolTrueEqualsWriteFalseEqualsRead,indexString,callback,(value)]
RAMtasksQueue = {start:0,end:0}
RAMcache = new LRUCache(MAX_RAM_CACHE_SIZE);
chestCache = new LRUCache(MAX_CHEST_CACHE_SIZE);
setSlot = function(pos,slot,item,amt,pid,att){
api.setStandardChestItemSlot(pos,slot,item,amt,pid,att)
let posStr = pos+''
let chestContents = chestCache.get(posStr);
if(!chestContents){
chestContents = api.getStandardChestItemSlots(pos)
}
chestContents[slot] = {name:item,amount:amt,attributes:att};
chestCache.set(posStr, chestContents);
}
getSlot = function(pos,slot){
let posStr = pos+''
let chestContents = chestCache.get(posStr);
if(!chestContents){
chestContents = api.getStandardChestItems(pos)
chestCache.set(posStr, chestContents);
}
return chestContents[slot]
}
RAMwait = 0
tick=()=>{
if(RAMwait>MAX_CHARS_PER_TICK)return RAMwait-=MAX_CHARS_PER_TICK
RAMwait=0
let trueMax = MAX_OPS*api.getNumPlayers()
mainLoop: for(let i = 0; i < trueMax; i++){
if(RAMtasksQueue.start===RAMtasksQueue.end){
RAMtasksQueue.start = 0
RAMtasksQueue.end = 0
break
}
let task = RAMtasksQueue[RAMtasksQueue.start]
let idxStr = task[1]
let decoded = decodeIndex(idxStr)
let pos = decoded.pos
let slot = decoded.slot
if(!api.isBlockInLoadedChunk(...pos)){
api.getBlock(pos)
break
}
let write = task[0]
let callback = task[2]
let value = ((task[3]??'')+'').slice(0,MAX_DESC)
if(write){
if(api.getBlock(pos)!=="Loot Chest")api.setBlock(pos,"Loot Chest"),RAMwait+=210
if(value===''){
setSlot(pos,slot,"Air")
if(getSlot(pos)===SLOTS){
api.setBlock(pos,"Air")
}
} else {
try{
let item = getSlot(pos,slot)
if((item?.name!==ITEM)||(item?.attributes?.customDescription!==value))
setSlot(pos,slot,ITEM,1,undefined,{customDescription:value})
let cachedValue = RAMcache.get(idxStr) || {val:"",max:0};
RAMwait+=Math.max(0,value.length+68+ITEM.length-cachedValue.max)
RAMcache.set(idxStr, {val:value,max:Math.max(value.length+68+ITEM.length,cachedValue.max)});
if(callback)callback(idxStr,value)
}catch(e){
api.log(e.name+': '+e.message)
if(e.name==="InternalError"||e.name==="ApiError"){
RAMwait = LIMIT_WAIT*MAX_CHARS_PER_TICK
break mainLoop
}
}
}
} else {
try{
let readVal = ""
let cachedValue = RAMcache.get(idxStr);
if(!cachedValue){
if(api.getBlock(pos)==="Loot Chest"){
let item = getSlot(pos,slot)
if(item?.name===ITEM){
readVal = item?.attributes?.customDescription
}
}
RAMcache.set(idxStr, {val:readVal,max:readVal.length+68+ITEM.length});
} else {
readVal=cachedValue.val
}
if(callback)callback(idxStr,readVal)
}catch(e){
api.log(e.name+': '+e.message)
if(e.name==="InternalError"||e.name==="ApiError"){
RAMwait = LIMIT_WAIT*MAX_CHARS_PER_TICK
break mainLoop
}
}
}
delete RAMtasksQueue[RAMtasksQueue.start]
RAMtasksQueue.start++
if(RAMwait>MAX_CHARS_PER_TICK)break mainLoop
}
}
function safeMod(numStr, divisor) {
let remainder = 0;
for (let i = 0; i < numStr.length; i++) {
remainder = (remainder * 10 + parseInt(numStr[i], 10)) % divisor;
}
return remainder;
}
function safeDivide(numStr, divisor) {
let quotient = '';
let remainder = 0;
for (let i = 0; i < numStr.length; i++) {
let currentDigit = parseInt(numStr[i], 10);
let currentNumber = remainder * 10 + currentDigit;
quotient += Math.floor(currentNumber / divisor);
remainder = currentNumber % divisor;
}
return quotient.replace(/^0+/, '');
}
let decodeIndex = function(idxStr) {
let tempIdxStr = String(idxStr)
let slot = safeMod(tempIdxStr, SLOTS)
tempIdxStr = safeDivide(tempIdxStr, SLOTS)
let x = safeMod(tempIdxStr, MAX_X)
tempIdxStr = safeDivide(tempIdxStr, MAX_X)
let y = safeMod(tempIdxStr, MAX_Y)
tempIdxStr = safeDivide(tempIdxStr, MAX_Y)
let z = safeMod(tempIdxStr, MAX_Z)
return {slot:slot, pos:[BASE[0]+x,BASE[1]+y,BASE[2]+z]}
}
bim = {
get:(index,callback)=>{
RAMtasksQueue[RAMtasksQueue.end]=[false,index+'',callback]
RAMtasksQueue.end++
},
set:(index,value,callback)=>{
RAMtasksQueue[RAMtasksQueue.end]=[true,index+'',callback,value]
RAMtasksQueue.end++
}
}
```

### qst - Quick Style Text

Usage:

```js
$_`styledTextInfo`
```

- `$_` right before a template literal to style it  
- `${["iconName"]}` to insert icon  
- `${[styleObj]}` to set a new permanent style  
- `${styledObj}` to temporarily change style of whatever is contained in the `styledObj` till the next style change/override  
Recommendation: stick to only using `${[styledObj]}` or `${styledObj}` for readability, as one changes the default style, and one temporarily overrides it.  
- `${"string"}` to insert a regular string  
- `styledObj` is an object of a format like `{color:"yellow",fontSize:"15px"}`, basically the style object you would pass into a typical custom styling segment

Example:

```js
api.sendMessage(myId,$_`\
${[{color:"#fe0000"}]}\
${{color:"white"}}[\
${{}}${["youtube"]}${{color:"white"}}\
 You${{}}Tube${{color:"white"}}]\
 ${{color:"#fc0"}}\
[\
${["zap"]} Super]\
 ${{color:"lime"}}\
${api.getEntityName(myId)}:\
 ${{color:"white"}}\
Wait I have superrank?`)
```

Example:
```js
api.sendMessage(myId,$_`${[{color:"yellow"}]}yellow text ${[{fontWeight:"bold"}]}yellow bold text`)
```

Code:

```js
$_ = (strings,...styles) => {
currentDefault = {}
styledText = (strings[0])?([{str:strings[0]}]):([])
let currentStyle = {}
let prevStyle = {}
for(let i = 0; i < styles.length; i++){
let mainString = strings[i+1]
let mainStyle = styles[i]
if ((typeof mainStyle) === 'string'){
styledText.push({str:mainStyle,style:currentStyle})
styledText.push({str:mainString,style:currentStyle})
} else {
if(Array.isArray(mainStyle)){
let newDefaultStyle = mainStyle[0]
if ((typeof newDefaultStyle) !== 'object'){
styledText.push({icon:newDefaultStyle,style:currentStyle})
} else {
currentDefault = {...currentDefault,...newDefaultStyle}
currentStyle = currentDefault
}
} else {
currentStyle = {...currentDefault,...mainStyle}
}
prevStyle = {...currentStyle}
if(mainString)styledText.push({str:mainString,style:currentStyle})
}
}
return styledText
}
```

### sbr - Set Block Reliable

Usage (To test, make a flat world and put these in separate code blocks):
```javascript
sbr([
[19,2,6],[40,50,30],"Maple Wood Planks",true,true
])
```
```javascript
sbr([19,2,6],[40,50,30],"Ice")
```
```javascript
sbr(thisPos,"Maple Log")
```
```javascript
sbr(18,1,5,"Stone")
```
```javascript
sbr([[18,1,5],"Pine Plank"]) //intentionally incorrect name to show error logging
```
```javascript
let [x,y,z] = thisPos
sbr([[x+1,y,z+1],[x+6,y+5,z+6],[x+11,y,z+11],[x+16,y+5,z+16]]) //cloning section from one place to another
//to test this one, place a code block, then build something to the positive x positive z direction of it (within 5 blocks), then run the code block.
```
```javascript
let [x,y,z] = thisPos
sbr([x,y-1,z,"Board"],[x,y-1,z,{persisted:{shared:{text:"Hello\nWorld"}}}]) //cloning section from one place to another
//to test this one, place a code block, then build something to the positive x positive z direction of it (within 5 blocks), then run the code block.
```

Code:

```js
(sbr=function(...e){e.length>0&&sbr.rawInputQueue.push(e)}).rawInputQueue=[],sbr.buildQueue=[],sbr.rateLimitCooldown=0,sbr.SPECIAL_BLOCK_IDS=new Set([204,205,206,207,952,953,954,955,1291,1292,1293,1294,1510,2088,2089,2090,2091]),sbr.MAX_RECT_VOLUME=360,sbr.TICK_BUDGET=250,sbr.COST_SCAN_BLOCK=.5,sbr.COST_SCAN_DATA=.7,sbr.COST_BUILD_RECT=10,sbr.COST_BUILD_DATA=1,sbr.COST_SPLIT_RECT=2,sbr.COST_CHUNK_CHECK=8,sbr.free=function(){return 0===sbr.rawInputQueue.length&&0===sbr.buildQueue.length},sbr.kill=function(){sbr.rawInputQueue=[],sbr.buildQueue=[]},sbr.getVol=function(e,t){let l=[t[0]-e[0]+1,t[1]-e[1]+1,t[2]-e[2]+1];return{dims:l,vol:l[0]*l[1]*l[2]}},sbr.deconstructWalls=function(e){let[t,l,$,u,s]=e,n=[],i=Math.min(t[0],l[0]),o=Math.min(t[1],l[1]),a=Math.min(t[2],l[2]),r=Math.max(t[0],l[0]),_=Math.max(t[1],l[1]),c=Math.max(t[2],l[2]);u&&n.push([[i,o,a],[r,o,c],$]),s&&n.push([[i,_,a],[r,_,c],$]),n.push([[i,o,a],[i,_,c],$]),n.push([[r,o,a],[r,_,c],$]);let C=u?o+1:o,T=s?_-1:_;return C<=T&&i+1<=r-1&&(n.push([[i+1,C,a],[r-1,T,a],$]),n.push([[i+1,C,c],[r-1,T,c],$])),n},sbr.checkAndLoadChunks=function(e,t){let l=[Math.min(e[0],t[0]),Math.min(e[1],t[1]),Math.min(e[2],t[2])],$=[Math.max(e[0],t[0]),Math.max(e[1],t[1]),Math.max(e[2],t[2])],u=!0;for(let s=l[0];s<=$[0];s+=32)for(let n=l[1];n<=$[1];n+=32)for(let i=l[2];i<=$[2];i+=32)api.isBlockInLoadedChunk(s,n,i)||(api.getBlockId(s,n,i),u=!1);return u},sbr.getTaskRange=function(e){return e?"gclone"===e[0]||"rect_with_data"===e[0]?[e[1],e[2]]:3===e.length&&Array.isArray(e[0])?[e[0],e[1]]:2===e.length&&Array.isArray(e[0])?[e[0],e[0]]:[e.slice(0,3),e.slice(0,3)]:[[0,0,0],[0,0,0]]},sbr.tick=function(){if(sbr.rateLimitCooldown>0){sbr.rateLimitCooldown--;return}let e=0;for(;sbr.rawInputQueue.length>0&&e<50;){e++;let t=sbr.rawInputQueue.shift();for(;1===t.length&&Array.isArray(t[0]);)t=t[0];t.length>0&&(!Array.isArray(t[0])||Array.isArray(t[0])&&!Array.isArray(t[0][0]))&&(t=[t]);for(let l=0;l<t.length;l++){let $=t[l];if($&&Array.isArray($)){if(5===$.length&&"boolean"==typeof $[3]){let u=sbr.deconstructWalls($);t.splice(l,1,...u),l--}else 4===$.length&&$.every(e=>Array.isArray(e))?sbr.buildQueue.push(["gclone",...$,{scanPos:null,run:null}]):sbr.buildQueue.push($)}}}let s=sbr.TICK_BUDGET,n=0;for(;s>0&&sbr.buildQueue.length>0&&n<1e3;){n++;let i=sbr.buildQueue[0],[o,a]=sbr.getTaskRange(i);if(!sbr.checkAndLoadChunks(o,a)){s-=sbr.COST_CHUNK_CHECK;for(let r=1;r<Math.min(sbr.buildQueue.length,15)&&s>=sbr.COST_CHUNK_CHECK;r++){let[_,c]=sbr.getTaskRange(sbr.buildQueue[r]);sbr.checkAndLoadChunks(_,c),s-=sbr.COST_CHUNK_CHECK}break}try{if("gclone"===i[0]){let[C,T,f,h,d,A]=i,g=[Math.min(T[0],f[0]),Math.min(T[1],f[1]),Math.min(T[2],f[2])],m=[Math.max(T[0],f[0]),Math.max(T[1],f[1]),Math.max(T[2],f[2])],k=[m[0]-g[0]+1,m[1]-g[1]+1,m[2]-g[2]+1],S=[h[0]-T[0],h[1]-T[1],h[2]-T[2]];null===A.scanPos&&(A.scanPos=[0,0,0],A.run={startPos:[0,0,0],blockType:api.getBlockId(g[0],g[1],g[2])});let b=!1;for(;s>=sbr.COST_SCAN_BLOCK;){s-=sbr.COST_SCAN_BLOCK;let[p,L,O]=A.scanPos,B=api.getBlockId(g[0]+p,g[1]+L,g[2]+O);if(B!==A.run.blockType||p===k[0]-1){let I=A.run.startPos,D=B!==A.run.blockType?p-1:p,Q=A.run.blockType;if(Q&&0!==Q){let P=[g[0]+I[0]+S[0],g[1]+L+S[1],g[2]+O+S[2]],y=[g[0]+D+S[0],g[1]+L+S[1],g[2]+O+S[2]];if(sbr.SPECIAL_BLOCK_IDS.has(Q)){let E=[];for(let U=I[0];U<=D&&s>=sbr.COST_SCAN_DATA;U++)s-=sbr.COST_SCAN_DATA,E.push(api.getBlockData(g[0]+U,g[1]+L,g[2]+O));sbr.buildQueue.splice(1,0,["rect_with_data",P,y,String(Q),E,0])}else sbr.buildQueue.splice(1,0,[P,y,String(Q)])}A.run={startPos:[p,L,O],blockType:B}}if(A.scanPos[0]++,A.scanPos[0]>=k[0]&&(A.scanPos[0]=0,A.scanPos[1]++,A.scanPos[1]>=k[1]&&(A.scanPos[1]=0,A.scanPos[2]++),A.scanPos[2]<k[2]&&(A.run={startPos:[...A.scanPos],blockType:api.getBlockId(g[0],g[1]+A.scanPos[1],g[2]+A.scanPos[2])})),A.scanPos[2]>=k[2]){b=!0;break}}b&&sbr.buildQueue.shift()}else if("rect_with_data"===i[0]){let[w,K,x,R,N,H]=i;0===H&&(api.setBlockRect(K,x,R),s-=sbr.COST_BUILD_RECT);let M=H;for(;s>=sbr.COST_BUILD_DATA&&M<N.length;)s-=sbr.COST_BUILD_DATA,null!==N[M]&&api.setBlockData(K[0]+M,K[1],K[2],N[M]),M++;M>=N.length?sbr.buildQueue.shift():i[5]=M}else if(3===i.length&&Array.isArray(i[0])){let[V,v,G]=i,W=[Math.min(V[0],v[0]),Math.min(V[1],v[1]),Math.min(V[2],v[2])],X=[Math.max(V[0],v[0]),Math.max(V[1],v[1]),Math.max(V[2],v[2])],{dims:j,vol:q}=sbr.getVol(W,X);if(q>sbr.MAX_RECT_VOLUME){let z=j.indexOf(Math.max(...j)),F=W[z]+Math.floor(j[z]/2)-1,J=[...X];J[z]=F;let Y=[...W];Y[z]=F+1,sbr.buildQueue.splice(0,1,[W,J,G],[Y,X,G]),s-=sbr.COST_SPLIT_RECT}else api.setBlockRect(W,X,String(G)),sbr.buildQueue.shift(),s-=sbr.COST_BUILD_RECT}else if(2===i.length&&Array.isArray(i[0]))api.setBlockData(i[0][0],i[0][1],i[0][2],i[1]),sbr.buildQueue.shift(),s-=sbr.COST_BUILD_DATA;else if(4!==i.length||Array.isArray(i[0]))sbr.buildQueue.shift();else{let Z=i[3];"object"==typeof Z&&null!==Z?(api.setBlockData(i[0],i[1],i[2],Z),s-=sbr.COST_BUILD_DATA):(api.setBlock(i[0],i[1],i[2],String(Z)),s-=sbr.COST_BUILD_RECT/10),sbr.buildQueue.shift()}}catch(ee){let et=ee.message?ee.message.toLowerCase():"";if(et.includes("rate limit"))sbr.rateLimitCooldown=5;else if(et.includes("interrupted"))break;else api.log("sbr error: "+ee.message),sbr.buildQueue.shift()}}};

//use like this:
tick=()=>{
sbr.tick()
//rest of you code
}
```

### exe = EXEcute Code Block

Takes 3 parameters, `x`, `y`, `z`, and an option 4th one, `myId`, and executes the code block at the found position. Also, when executing the code block, `thisPos` WILL work, and be at the location of being evaled. However, `myId` will be undefined in world code (since no specific person is executing the code), unless the function is called from a code block, where it will be the id of the player. Note: will not be able to load chunks on first player join.

Code:

```js
exe=(x,y,z,i=null)=>(0,eval)(`let thisPos=[+${+x},+${+y},+${+z}];myId=playerId=${i?"+i+":i};undefined;`+(api.getBlockData(x,y,z)?.persisted?.shared?.text??""))
```

### ctb - Color to Blockname

Usage:

`ctb(colorName)`  
`ctb("#ff0000")`

Finds the closest solid block to a given color in the default texture pack

Note: you have to use full hex color names ("red" and "#ff0" won't work)

Code:

```js
ctb=_=>{let e=parseInt(_.slice(1,7),16),a=[e>>16&255,e>>8&255,255&e];return[["Gray Concrete",[55,58,62]],["Light Gray Concrete",[125,125,115]],["Black Concrete",[9,11,16]],["Blue Concrete",[45,48,144]],["Brown Concrete",[97,60,32]],["Cyan Concrete",[21,119,135]],["Light Blue Concrete",[37,138,200]],["Lime Concrete",[95,170,25]],["Magenta Concrete",[169,48,159]],["Orange Concrete",[225,98,1]],["Pink Concrete",[215,102,143]],["Purple Concrete",[101,32,157]],["Red Concrete",[144,33,33]],["White Concrete",[208,214,215]],["Green Concrete",[73,91,36]],["Yellow Concrete",[242,176,21]],["Gray Baked Clay",[58,42,36]],["Light Gray Baked Clay",[135,107,98]],["Black Baked Clay",[36,21,15]],["Blue Baked Clay",[73,60,91]],["Brown Baked Clay",[77,50,35]],["Cyan Baked Clay",[85,90,90]],["Light Blue Baked Clay",[112,109,138]],["Lime Baked Clay",[103,117,53]],["Magenta Baked Clay",[149,87,108]],["Orange Baked Clay",[163,83,38]],["Pink Baked Clay",[163,78,79]],["Purple Baked Clay",[118,70,86]],["Red Baked Clay",[143,61,47]],["White Baked Clay",[210,177,161]],["Green Baked Clay",[75,82,40]],["Yellow Baked Clay",[187,134,36]],["Gray Chalk",[111,128,138]],["Light Gray Chalk",[182,206,204]],["Black Chalk",[43,57,67]],["Blue Chalk",[132,167,222]],["Brown Chalk",[182,164,151]],["Cyan Chalk",[172,219,210]],["Light Blue Chalk",[160,197,231]],["Lime Chalk",[172,211,149]],["Magenta Chalk",[230,163,224]],["Orange Chalk",[243,160,133]],["Pink Chalk",[248,173,198]],["Purple Chalk",[192,155,200]],["Red Chalk",[243,133,135]],["White Chalk",[225,247,248]],["Green Chalk",[158,179,147]],["Yellow Chalk",[253,245,180]]].reduce((_,e)=>{let l=e[1][0]-a[0],C=e[1][1]-a[1],r=e[1][2]-a[2],k=l*l+C*C+r*r;return k>_[2]?_:[e[0],e[1],k]},[,,2e5])[0]}
```

### stp - Simple Timeouts Polyfill

Usage:
- `setTimeout(cb,ms,...args)` returns `id` executes `cb(...args)` after `ms` milliseconds
- `setInterval(cb,ms,...args)` returns `id` executes `cb(...args)` every `ms` milliseconds
- `setImmediate(cb,ms,...args)` returns `id` executes `cb(...args)` the very next tick
- `clearTimeout(id)`
- `clearInterval(id)`
- `clearImmediate(id)`

Note: since this uses tick, timers are only accurate to 50ms, and will be delayed if tick takes longer. However, this is usually desirable.

Code:

```js
globalThis.setTimeout=(e,t,...r)=>{let l=setTimeout.c+(t/50>>>0);setTimeout.d[l]||=[];let d=setTimeout.d[l];return d[d.length]=[()=>e(...r),setTimeout.i],setTimeout.r[setTimeout.i]=[d,d.length-1],setTimeout.i++},globalThis.clearTimeout=e=>{let t=setTimeout.r[e];if(!t)return;let r;delete t[0][t[1]],delete setTimeout.r[e]},globalThis.setInterval=(e,t,...r)=>{let l=t/50>>>0,d=setTimeout.c+(l=l>1?l:1);setTimeout.d[d]||=[];let a=setTimeout.d[d];return a[a.length]=[()=>e(...r),setTimeout.i,l],setTimeout.r[setTimeout.i]=[a,a.length-1],setTimeout.i++},globalThis.clearInterval=e=>{clearTimeout(e)},globalThis.setImmediate=(e,...t)=>setTimeout(e,0,...t),globalThis.clearImmediate=e=>{clearTimeout(e)},setTimeout.d={},setTimeout.r={},setTimeout.t=()=>{let e=setTimeout.d[setTimeout.c];return e&&(e.forEach((t,r)=>{let l=t[0],d=t[1];try{l()}catch(a){api.broadcastMessage((t[2]?"Interval error: ":"Timeout error: ")+a.name+": "+a.message,{color:"#ff9d87"})}if(t[2]){let c=t[2],i=setTimeout.c+c;setTimeout.d[i]||=[];let n=setTimeout.d[i];n[n.length]=[l,d,c],setTimeout.r[d]=[n,n.length-1]}else delete setTimeout.r[d];delete e[r]}),delete setTimeout.d[setTimeout.c]),setTimeout.c++},setTimeout.c=0,setTimeout.i=0

//usage:
//other code and callbacks
tick = () => {
setTimeout.t()
//rest of the code for tick
}
//other code and callbacks
```

### hsi - huge suffixed integers

Usage:

```js
console.log(hsi.add("5000", "4999"))//9999
console.log(hsi.add("5000", "5000"))//10.00k
console.log(hsi.sub("12k", "3k"))//9000
console.log(hsi.mul("100", "50"))//5000
console.log(hsi.mul("100", "100"))//10.00k
console.log(hsi.div("1m", "50"))//20.00k
console.log(hsi.sub("3.24b", "3.23b"))//10.00m
console.log(hsi.add("1.9", "1.1"))//3
```

4 digits of precision. Format like "1234" or "12.34m" or "123.4ab". Arithmetic with hsi.add,hsi.sub,hsi.mul,hsi.div

Code:

```js
hsi={_std:["","k","m","b","t"],_gns:function(t){if(t<=0)return"";if(t<5)return this._std[t];let e=t-5,r=2;for(;e>=Math.pow(26,r);)e-=Math.pow(26,r),r++;let $="";for(let o=0;o<r;o++)$=String.fromCharCode(97+e%26)+$,e=Math.floor(e/26);return $},_ste:function(t){if(!t)return 0;let e=this._std.indexOf(t);if(-1!==e)return e;let r=t.length,$=5;for(let o=2;o<r;o++)$+=Math.pow(26,o);let i=0;for(let s=0;s<r;s++)i=26*i+(t.charCodeAt(s)-97);return $+i},_parse:function(t){if(null==t)return{m:0,e:0};let e=t.toString().toLowerCase().replace(/\s/g,"").match(/^([-+]?\d*\.?\d+)([a-z]*)$/);if(!e)return{m:0,e:0};let r=parseFloat(e[1]),$=this._ste(e[2]);return{m:r,e:$}},_format:function(t){let{m:e,e:r}=t;if(0===e||!isFinite(e))return"0";for(;Math.abs(e)>=1e3;)e/=1e3,r++;for(;Math.abs(e)>0&&1>Math.abs(e)&&r>0;)e*=1e3,r--;if(0===r)return Math.floor(e).toString();if(1===r&&10>Math.abs(e))return Math.floor(1e3*e).toString();let $=Math.abs(e),o;return parseFloat(o=$>=100?e.toFixed(1):$>=10?e.toFixed(2):e.toFixed(3))>=1e3?this._format({m:parseFloat(o),e:r}):o+this._gns(r)},add:function(t,e){let r=this._parse(t),$=this._parse(e),o=Math.max(r.e,$.e),i=r.m*Math.pow(1e3,r.e-o)+$.m*Math.pow(1e3,$.e-o);return this._format({m:i,e:o})},sub:function(t,e){let r=this._parse(t),$=this._parse(e),o=Math.max(r.e,$.e),i=r.m*Math.pow(1e3,r.e-o)-$.m*Math.pow(1e3,$.e-o);return this._format({m:i,e:o})},mul:function(t,e){let r=this._parse(t),$=this._parse(e);return this._format({m:r.m*$.m,e:r.e+$.e})},div:function(t,e){let r=this._parse(t),$=this._parse(e);return 0===$.m?"0":this._format({m:r.m/$.m,e:r.e-$.e})}}
```

### bcf - Bloxd Chat Faker

Usage:

```js
bcf(`
coolperson joined
someone joined
coolperson: hi
Tribe ppl has been created by someone
[ppl] someone: who are you?
Tip: A "chunk" is a cube with 32 block length sides
coolperson: I am a cat
[ppl] someone: ok...
coolperson has joined the tribe
[ppl] coolperson: wait was that an interview???
[ppl] someone: wait i didnt mean to invite u srry
Left tribe ppl
Tribe ppl has been disbanded by someone
Tribe cat has been created by coolperson
Invited someone to join
someone has joined the tribe
The chunk is now yours. Other players cannot build here.
Protectors protect the chunk they are in, with 32 cubes on each side, as well as the chunk above and below.
someone has left the tribe
someone: oops left accidentaly invite me again
Invited someone to join
someone has joined the tribe
[cat] someone: wait kick me from the tribe for a sec
[cat] coolperson: k
someone has been removed from the tribe
Removed someone from the tribe
Arthur joined
[Dev] [10Super] Arthur: hi bye
Arthur left
[cat] [Youtuber] [0Super] coolperson: yo
[cat] [Youtuber] [0Super] coolperson: yay yt and super
[cat] [Youtuber] [0Super] coolperson: lemme choose color...
[cat] [Youtuber] [1Super] coolperson: test
[cat] [Youtuber] [4Super] coolperson: test
[cat] [Youtuber] [7Super] coolperson: test
[cat] [Youtuber] [9Super] coolperson: test
[cat] [Youtuber] [9Super] coolperson: nice
`)
```

1. Create a flat test world
2. Put the world code shown below in world code
3. Put the code block shown above in a code block for test
4. Type /log until all messages have come, and no more are coming
5. Edit it to match your needs once you get it working

Code:

```js
bd=[],bcf=e=>{let t=["#dff8ff","#ffff66","#b3ff66","#66ff66","#66ffb3","#66ffff","#66bcff","#cc66ff","#ff80bb","#ff6666","#ffaa66"],f=e.trim().split("\n");for(let e of f)if(e.includes(":")&&!e.startsWith("Tip")){let f=[],s=e.indexOf(":"),r=e.substring(0,s+1),l=e.substring(s+1),o="",i="#dff8ff";if(r.startsWith("[")){let e=r.indexOf("]"),t=r.substring(0,e+1);t.includes("Youtube")||t.includes("Super")||t.includes("Dev")||(o=t,f.push({str:o,style:{color:"#2eeb82"}}),r=r.slice(o.length))}if(r.includes("[Youtube]")){let e=r.indexOf("[Youtube]");e>0&&f.push({str:r.substring(0,e),style:{color:"#dff8ff"}}),f.push({str:"[",style:{color:"#fff"}},{icon:"youtube",style:{color:"#fe0000"}},{str:" You",style:{color:"#fff"}},{str:"Tube",style:{color:"#fe0000"}},{str:"]",style:{color:"#fff"}}),r=r.slice(e+9)}if(r.includes("[Dev]")){let e=r.indexOf("[Dev]");e>0&&f.push({str:r.substring(0,e),style:{color:"#dff8ff"}}),f.push({str:"[",style:{color:"#cef3ff"}},{icon:"wrench",style:{color:"#a4a4a4"}},{str:" Dev]",style:{color:"#cef3ff"}}),r=r.slice(e+5)}let c=r.match(/\[(\d+)\s?Super\]/);if(c){let e=c[0],s=parseInt(c[1]),l=r.indexOf(e);l>0&&f.push({str:r.substring(0,l),style:{color:"#dff8ff"}}),f.push({str:"[",style:{color:"#fc0"}},{icon:"zap",style:{color:"#fc0"}},{str:" Super]",style:{color:"#fc0"}}),i=t[s]||"#dff8ff",r=r.slice(l+e.length)}f.push({str:r,style:{color:i}});let n="[Tribe]"===o?"#2eeb82":"#ffffff";f.push({str:l,style:{color:n}}),bd.push(f)}else{let t="#cef3ff";(e.endsWith(" has joined the tribe")||"The chunk is now yours. Other players cannot build here."===e||"Protectors protect the chunk they are in, with 32 cubes on each side, as well as the chunk above and below."===e)&&(t="#2eeb82"),(e.endsWith(" has left the tribe")||e.endsWith(" has been removed from the tribe")||e.startsWith("Left tribe ")||e.startsWith("Removed ")&&e.endsWith(" from the tribe"))&&(t="#ff9d87"),bd.push([{str:e,style:{color:t}}])}},playerCommand=(e,t)=>{if("log"===t)for(let t=0;bd.length&&t<16;t++)api.sendMessage(e,bd.shift());return!0};
```

# pmd - Parse MarkDown

Usage:

```
Use **text** or __text__ for bold
Use *text\* or \_text\_for italic
Use <b>text</b> for bold
Use <i>text</i> for italic
Use > text for blockquote
Use <color>text</color> for colored text
Use # text for h1, ## text for h2, ### text for h3 (i.e. the normal font size), so until h89.
Use --- or ___ or === or *** for a horizontal rule (like -------------------------------)
Use :iconname: to insert a bloxd-supported icon (make sure to color them accordingly)
```

Code:

```js
pmd=(e,t)=>{let l=t?[{str:("-"===t[0]?api.getEntityName(t):t)+": ",style:{color:"#dff8ff"}}]:[],i=!1,n=!1,o=[],s={},c=e.split("\n"),f=0;for(let _ of c){f++;if(_.match(/^[-=*_]{2,}$/))l.push({str:_[0].repeat(31),style:{fontWeight:"700"}});else{let h=0;_=_.replace(/^>*\s+/,e=>(h=e.trim().length,"")),l.push({str:"| ".repeat(h),style:{fontWeight:"900",color:"#aaa"}});let r=!1,g=(_=_.replace(/^-# /,"#### ")).match(/^#+ /),u=(g?.[0]?.length||4)-1;g&&(r=!0),r&&(_=_.slice(u));let a=3/u+"rem",$=_.split(/(\*\*?|__?|\\|:.*?:|<.*?>)/g),p=!1,m=!1,b=!1,d=!1,y=!1;for(let W of $){if(!W)continue;if(!y){if("**"===W){p=!p;continue}if("*"===W){b=!b;continue}if("__"===W){m=!m;continue}if("_"===W){d=!d;continue}if("\\"===W){y=!0;continue}if(":"===W[0]&&":"===W[W.length-1]){let E="1rem"===a?{}:{fontSize:a};o.length&&(E.color=o[o.length-1]),l.push({icon:W.slice(1,-1),style:E});continue}if("<b>"===W||"<bold>"===W||"<strong>"===W){i=!0;continue}if("</b>"===W||"</bold>"===W||"</strong>"===W){i=!1;continue}if("<i>"===W||"<italic>"===W||"<italics>"===W||"<em>"===W){n=!0;continue}if("</i>"===W||"</italic>"===W||"</italics>"===W||"</em>"===W){n=!1;continue}if("<"===W[0]&&">"===W[W.length-1]){if("/"===W[1]){let M=W.slice(2,-1);if(s[M]){let N=s[M][s[M].length-1];for(let S=o.length-1;S>=N;S--)s[o[S]].length--;o.length=N}else{let j=o.pop();s[j].pop(),0===s[j].length&&delete s[j]}}else{let k=W.slice(1,-1);s[k]||=[],s[k].push(o.length),o.push(k)}continue}}y=!1;let q="1rem"===a?{}:{fontSize:a};o.length&&(q.color=o[o.length-1]),(p||m||i||r)&&(q.fontWeight="900"),(b||d||n)&&(q.fontStyle="italic"),l.push({str:W,style:q})}}f<c.length&&l.push({str:"\n"})}return l}
```

# cbe - Create Block Explosion

Usage:

`cbe(playerId,position,level)`, where `position` is `[number,number,number]` and `level` is `0`, `1`, `2`, and `3` (`0` being no explosion)

Example:

```js
//explosive pebbles
onPlayerThrowableHitTerrain=(pid,name,eid)=>{
if(name!=="Pebble")return
cbe(pid,api.getPosition(eid),3)
}

api.setCallbackValueFallback("onPlayerChangeBlock","preventDrop")
api.setCallbackValueFallback("onWorldChangeBlock","preventDrop")
```

Code:

```js
cbe=(e,t,l)=>{if(l){let r=[t[0],t[1]-1,t[2]],o="Air"===api.getBlock(r);o&&api.setBlock(r,"Stone"),api.attemptCreateThrowable(e,l<2?"Fireball":l<3?"RPG":"Super RPG",t,[0,-1,0],1,1,0,{}),o&&api.setBlock(r,"Air")}};
```

# bps - Big Player Storage

Uses Moonstone Chests for storage to make it persist through sessions.

Usage:
- put it in world code
- `bps[playerId][key] = value` to set a value for a player
- `bps[playerId][key]` to get a value for a player

Example:

```js
onPlayerJoin=(pid)=>{
 bps[pid].jumps ||= 0 //sets coins to 0 if player joined for the first time
}
onPlayerJump=(pid)=>{
  bps[pid].jumps++ //increment jumpcount
}
onPlayerCommand=(pid,cmd)=>{
 if(cmd === "jumps") {
  let jumps = bps[pid].jumps
  api.sendMessage(pid,jumps.toString()) 
 }
}
```


Also, you might want to do:

```js
onPlayerJoin=pid=>{
 {...bps[pid]}
 //other code
}
onPlayerLeave=pid=>{
 delete bps[pid]
 //other code
}
```

for a slight performance boost, but it's a pretty minor thing and is optional

Code:

```js
bps=new Proxy(Object.create(null),{has:(e,t)=>api.playerIsInGame(t),get:(e,t)=>e[t]||=new Proxy(Object.create(null),{has:(e,o)=>o in e||(Object.assign(e,JSON.parse(api.getMoonstoneChestItemSlot(t,961*o.charCodeAt(0)+31*o.charCodeAt(o.length>>1)+o.charCodeAt(o.length-1)&31)?.attributes?.customDescription??"{}")),o in e),get:(e,o)=>(o in e||Object.assign(e,JSON.parse(api.getMoonstoneChestItemSlot(t,961*o.charCodeAt(0)+31*o.charCodeAt(o.length>>1)+o.charCodeAt(o.length-1)&31)?.attributes?.customDescription??"{}")),e[o]),set(e,o,s){let n=typeof s;if(null!==s&&!["string","number","boolean","undefined"].includes(n))throw TypeError("Unexpected value type: "+n+" (expected string, number, boolean, or undefined)");let r=961*o.charCodeAt(0)+31*o.charCodeAt(o.length>>1)+o.charCodeAt(o.length-1)&31,i=JSON.parse(api.getMoonstoneChestItemSlot(t,r)?.attributes?.customDescription??"{}");i[o]=s,Object.assign(e,i),api.setMoonstoneChestItemSlot(t,r,"Ice",1,{customDescription:JSON.stringify(i)})},deleteProperty(e,o){let s=961*o.charCodeAt(0)+31*o.charCodeAt(o.length>>1)+o.charCodeAt(o.length-1)&31,n=JSON.parse(api.getMoonstoneChestItemSlot(t,s)?.attributes?.customDescription??"{}");delete n[o],Object.assign(e,n),delete e[o],api.setMoonstoneChestItemSlot(t,s,"Ice",1,{customDescription:JSON.stringify(n)})},setPrototypeOf(){},preventExtensions(){},defineProperty(){},ownKeys:e=>(Object.assign(e,...api.getMoonstoneChestItems(t).map(e=>e?.attributes?.customDescription).filter(e=>e).map(e=>JSON.parse(e))),Reflect.ownKeys(e))}),set(){},setPrototypeOf(){},preventExtensions(){},defineProperty(){},ownKeys:()=>api.getPlayerIds()});
```
