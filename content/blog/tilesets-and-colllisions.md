/*
Title: タイルセットと衝突
Description: タイルセットを使用してタイルマップを作成し、タイルセット、パレット、およびタイルマップをエクスポートします。衝突も追加します。
Author: テールリングリーン
Date: 17 April 2021
Template: blog-item
Placing: 1
Tags: タイルセット、タイルマップ、パレット、ビデオモード、IRQ、タイルマップスタジオ、タイル
*/

# テレセット、テレマップ、パレット
## 目的
私たちの目的は、スクリーンショットのようにタイルマップを再作成することですが、タイルセットとタイルマップは異なります。タイルマップには透明なタイルがあり、透明なタイルの背後に背景が表示されます。デモのスクリーンショットには、タイルセットに2つのタイルのみがあり、背景はなく、小さなパレットがあります。背景パレットで背景を追加し、新しいタイルマップを追加します。
<br><br>

## 古いタイルマップ
![タイルマップ](/assets/images/tilemap2.png)
<br>
<small>以前から。</small>
<br><br>

```c
#include <string.h>
#include <maxmod.h>
#include "toolbox.h"
#include "input.h"
#include "backgrounds/autumnBackground.h"
#include "backgrounds/winterBackground.h"
#include "backgrounds/springBackground.h"
#include "backgrounds/summerBackground.h"
#include "tilesets/seasonsTileset.h"
#include "tilemaps/springMap.h"
#include "soundbank.h"
#include "soundbank_bin.h"
#define IRQ_VBLANK 0x001

// Size of your map
#define MapWidth 256
#define MapHeight 256

// Interpret the map as an array of screen entries (16-bit values)
u16 *my_map = (u16*)(autumnBackgroundMap);

// Size of a GBA screenblock
#define SbWidth 32
#define SbHeight 32
int map_x = 0;
int map_y = 0;
// change these to load a different part of the map:
int map_offset_x = 0;
int map_offset_y = 0;

// which screenblock to copy to
const int sbb = 30;
```
## Map Loading
This is our function for loading the map into sections.
```c
void load_map() {
    for (int y = 0; y < SbHeight; y++) {
        for (int x = 0; x < SbWidth; x++) {
            // figure out the coordinates within the map
            map_x = map_offset_x + x;
            map_y = map_offset_y + y;
            
            //se_mem[sbb][x + y*SbWidth] |= SE_PALBANK(0);
            
            /*
            // copy the entries from the map data into the screenblock:
            se_mem[sbb][x + y*SbWidth] = my_map[map_x + map_y * MapWidth];
            */
            se_mem[sbb][x + y*SbWidth] = my_map[map_x + map_y * MapWidth];
        }
    }
}
```
## Background
The background image is broken down and shown as a tilemap.
<br>

![タイルマップ](/assets/images/autumnBackground.png)
<br>
<small>背景を追加します。</small>

```c
#define BackgroundLayer 0
void background()
{
	// Load palette
    memcpy(pal_bg_mem, autumnBackgroundPal, autumnBackgroundPalLen);
    // Load tiles into CBB 0
    memcpy(&tile_mem[0][31], autumnBackgroundTiles, autumnBackgroundTilesLen);
    // Load map into SBB 30
    //load_map();
    
    memcpy(&se_mem[30][0], autumnBackgroundMap, autumnBackgroundMapLen);
	for (int i=0; autumnBackgroundMapLen; i++)
		se_mem[30][i] |= SE_PALBANK(0);

    
    // set up BG0 for a 4bpp 64x32t map, using
    //   using charblock 0 and screenblock 31
    REG_BG0CNT= BG_CBB(0) | BG_SBB(30) | BG_4BPP | BG_REG_32x32;
}
```
<br><br>

## Tileset
This is the tileset we will be using
<br>

![タイルマップ](/assets/images/seasonsTileset.png)
<br>
<small>The tileset.</small>

## Tilemap
This is the tilemap we will be using. Open it with Usenti and export it as a .c or .h file. 
<br>

![タイルマップ](/assets/images/autumnTilemap.png)
<br>
<small>背景を追加します。</small>

### Export Settings Usenti
![タイルマップ](/assets/images/exportSettingsUsenti.png)
<br>
<small>Export Settings in Usenti。</small>


## TilemapStudio
You can make the tilemap using Tilemap Studio, a tilemap editor for Windows.
![タイルマップ](/assets/images/autumnTilemapStudio.png)
<br>
<small>背景を追加します。</small>

## 
You can also use TilemapStudio, also
![タイルマップ](/assets/images/autumnTilemapStudio.png)
<br>
<small>背景を追加します。</small>


## インクルード
インクルードを追加することから始めます。





<br><br>
