🇬🇧 [en](BRANCHING_MODEL.md)&nbsp;&nbsp;|&nbsp;&nbsp;🇭🇺 
[hu](BRANCHING_MODEL.hu.md)

# Egyszerűsített Git-flow

```
                    RELEASE TAG
o---------------------------o-----------------o------------o------> MAIN
\                          / \                 \----------/ HOTFIX
 \                        /   \                           \
  \----------------------/     \--------------------o-----o------> DEVELOP
                                \                  /
                                 \----------------/ FEATURE
```

- **`main`**: a legstabilabb, kiadásra kész kódot tartalmazza. Másképp 
fogalmazva: a `main` ágról bármikor lehúzhatjuk a kódot, lefordíthatjuk és éles 
környezetbe telepíthetjük.

- **`develop`**: itt zajlik a fejlesztés nagy része. Amikor a `develop` állapota 
elég stabil, beolvasztható a `main` ágba. Minden kiadást címkével (taggel) kell 
ellátni (például: `v2.1.3`).

- **`feature`**: itt történik az egyes funkciók fejlesztése. Mindig a `develop` 
ágból kell indítani, és csak a `develop` ágba szabad mergelni. Miután teljesen 
visszakerült a `develop` ágba, az adott `feature` ág törölhető.

- **`hotfix`**: az aktuális kiadás hibajavítására szolgál. A javítás befejezése 
után a `hotfix` ágat mind a `main`, mind a `develop` ágba vissza kell mergelni, 
majd az ág törölhető.

## Opcionális `release` ág

Ez a branching modell elsősorban kis létszámú (1–5 fős) fejlesztőcsapatok 
számára ideális. Ha a `develop` ág állapota eléri azt a pontot, ahol 
részletesebb tesztelésre, hibajavításokra vagy teljes QA-folyamatra van szükség, 
problémát okozhat, hogy a csapat hogyan folytassa a fejlesztést anélkül, hogy a 
tesztelést megzavarnák az új beolvasztások.

Ilyenkor két megközelítés létezik:

1. **FEATURE FREEZE a `develop` ágon**  
   A `develop` ág a kiadás elkészültéig nem fogad új `feature` beolvasztásokat.

2. **`release` ág létrehozása a `develop` ágból**  
   A kiadás előtti véglegesítés, hibajavítás és QA-folyamat a `release` ágban
   történik. Eközben a `develop` ág továbbra is fogadhat új `feature`
   beolvasztásokat, így a fejlesztés nem áll le a stabilizáció idejére.

   A `release` ágban végzett minden módosítást vissza kell mergelni a
   `develop` ágba is. Ezen felül azokat a `feature` ágakat, amelyeket a
   `release` ág létrehozása előtt hoztak létre, szintén frissíteni kell a
   `release` ág változtatásaival, különben regresszió (a már kijavított hibák
   visszacsúszása) léphet fel.

**Amennyiben külön nem jelezzük, minden esetben a FEATURE FREEZE megoldást 
használjuk.**

## Elnevezési konvenciók

- **`feature`**: `feature/` előtaggal kezdődjön, majd egy rövid, leíró név 
következzen  
  *példa:* `feature/new_mission`

- **`hotfix`**: `hotfix/` előtaggal kezdődjön, majd egy rövid, leíró név 
következzen  
  *példa:* `hotfix/duplicate_mission`
  
- **`release`**: `release/x.y.z` formájú legyen  
  *példa:* `release/1.2.3`

## Beolvasztási stratégia
A projekt a fa-szerkezetű, auditálható történet megőrzése érdekében minden 
beolvasztáshoz merge-commitot hoz létre, hogy:
- megmaradjon a funkciók és hibajavítások elkülönült története,  
- pontosan visszakövethető legyen, mely commitok mely feladathoz tartoztak,  
- auditálható legyen a fejlesztési folyamat,  
- hosszú távon érthető maradjon a projekt fejlődése.

### Kötelező

- `git merge --no-ff` minden `feature`, `release` és `hotfix` ág esetén.  
  Ez biztosítja, hogy a beolvasztás *minden* esetben külön merge commitot hozzon 
létre.

```
$ git checkout main
$ git merge --no-ff feature/my-feature
$ git push origin main
$ git branch -d feature/my-feature
```

### Megengedett

- Lokális, publikálatlan feature ágakon opcionális a `git rebase -i` használata 
(commitok összevonása, átnevezése).  
  A rebase csak *saját fejlesztés* tisztítására szolgál, **nem írható át vele közös history**.

### Tilos

- Fast-forward merge (`git merge` úgy, hogy nem jön létre merge commit).  
- Bármilyen rebase a `main`, `develop` vagy `release` ágakon.  
- Force-push (`--force`), amely közös history-t módosít.  

## Forrás

- [Gist](https://gist.github.com/vxhviet/9c4a522921ad857406033c4125f343a5)
