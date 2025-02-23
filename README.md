# Regional Prompter
![top](https://github.com/hako-mikan/sd-webui-regional-prompter/blob/imgs/top.jpg)
- custom script for [AUTOMATIC1111's stable-diffusion-webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui) 
- Different prompts can be specified for different regions

- [AUTOMATIC1111's stable-diffusion-webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui) 用のスクリプトです
- 垂直/平行方向に分割された領域ごとに異なるプロンプトを指定できます

## Language control / 言語制御
日本語: [![jp](https://img.shields.io/badge/lang-jp-green.svg)](https://github.com/hako-mikan/sd-webui-regional-prompter/blob/main/README.JP.md)

### Updates
- New feature : [regions by inpaint](#inpaint) (thanks [Symbiomatrix](https://github.com/Symbiomatrix))
- New feature : [regions by prompt](#divprompt) 
- 新機能 : [インペイントによる領域指定](#inpaint) (thanks [Symbiomatrix](https://github.com/Symbiomatrix))
- 新機能 : [プロンプトによる領域指定](#divprompt) 


# Overview
Latent couple extention performs U-Net calculations on a per-prompt basis, but this extension performs per-prompt calculations inside U-Net. See [here(Japanese)](https://note.com/gcem156/n/nb3d516e376d7) for details. Thanks to furusu for initiating the idea. Additional, Latent mode also supported.

## index
- [2D regions](#2D)
- [Latent mode(LoRA)](#latent) 
- [regions by inpaint](#inpaint) 
- [regions by prompt](#divprompt) 

		
																																																																																								 

## Usage
This section explains how to use the following image, explaining how to create the following image.  
![sample](https://github.com/hako-mikan/sd-webui-regional-prompter/blob/imgs/sample.jpg)  
Here is the prompt.
```
green hair twintail BREAK
red blouse BREAK
blue skirt
```
setting
```
Active : On
Use base prompt : Off
Divide mode : Vertical
Divide Ratio : 1,1,1
Base Ratio : 
````
This setting divides the image vertically into three parts and applies the prompts "green hair twintail" ,"red blouse" ,"blue skirt", from top to bottom in order.

### Active  
This extention is enabled only if "Active" is toggled.

### Prompt
Prompts for different regions are separated by `BREAK` keywords. 
Negative prompts can also be set for each area by separating them with `BREAK`, but if `BREAK` is not entered, the same negative prompt will be set for all areas.

Using `ADDROW` or `ADDCOL` anywhere in the prompt will automatically activate [2D region mode](#2d-region-assignment-experimental-function).

### Use base prompt
Check this if you want to use the base prompt, which is the same prompt for all areas. Use this option if you want the prompt to be consistent across all areas.
When using base prompt, the first prompt separated by `BREAK` is treated as the base prompt.
Therefore, when this option is enabled, one extra `BREAK`-separated prompt is required compared to Divide ratios.

Automatically turned on when `ADDBASE` is entered.
																																				   

### Divide ratio
If you enter 1,1,1, the image will be divided into three equal regions (33,3%, 33,3%, 33,3%); if you enter 3,1,1, the image will be divided into 60%, 20%, and 20%. Fractions can also be entered: 0.1,0.1,0.1 is equivalent to 1,1,1. For greatest accuracy, enter pixel values corresponding to height / width (vertical / horizontal mode respectively), eg 300,100,112 -> 512.

					  
				
										  
		   
																		

Using a `;` separator will automatically activate 2D region mode.
													  
																									  

### Base ratio
Sets the ratio of the base prompt; if base ratio is set to 0.2, then resulting images will consist of `20%*BASE_PROMPT + 80%*REGION_PROMPT`. It can also be specified for each region, in the same way as "Divide ratio" - 0.2, 0.3, 0.5, etc. If a single value is entered, the same value will be applied to all areas.

### Divide mode
Specifies the direction of division. Horizontal and vertical directions can be specified.
In order to specify both horizontal and vertical regions, see 2D region mode.

## calcutation mode  
### Attention  
Normally, use this one.  
### Latent
Slower, but allows separating LoRAs to some extent. The generation time is the number of areas x the generation time of one pic.

Example of Latent mode for [nendoorid](https://civitai.com/models/7269/nendoroid-figures-lora),
[figma](https://civitai.com/models/7984/figma-anime-figures) LoRA separated into left and right sides to create.  
<img src="https://github.com/hako-mikan/sd-webui-regional-prompter/blob/imgs/sample2.jpg" width="400">

### Use common prompt
If this option enabled, first part of the prompt is added to all region parts.

Automatically turned on when `ADDCOMM` is entered.
```
best quality, 20yo lady in garden BREAK
green hair twintail BREAK
red blouse BREAK
blue skirt
```
If common is enabled, this prompt is converted to the following:
```
best quality, 20yo lady in garden, green hair twintail BREAK
best quality, 20yo lady in garden, red blouse BREAK
best quality, 20yo lady in garden, blue skirt
```
So you must set 4 prompts for 3 regions. If `Use base prompt` is also enabled 5 prompts are needed. The order is as follows: common, base, prompt1,prompt2,...

## <a id="2D">2D region assignment</a>
You can specify a region in two dimensions. Using a special separator (`ADDCOL/ADDROW`), the area can be divided horizontally and vertically. Starting at the upper left corner, the area is divided horizontally when separated by `ADDCOL` and vertically when separated by `ADDROW`. The ratio of division is specified as a ratio separated by a semicolon. An example is shown below; although it is possible to use `BREAK` alone to describe only the ratio, it is easier to understand if COL/ROW is explicitly specified. Using `ADDBASE `as the first separator will result in the base prompt. If no ratio is specified or if the ratio does not match the number of separators, all regions are automatically treated as equal multiples.
In this mode, the direction selected in `Divide mode` changes which separator is applied first:
- In `Horizontal` mode, the image is first split to rows with `ADDROW` or `;` in Divide ratio, then each row is split to regions with `ADDCOL` or `,` in Divide ratio.
- In `Vertical` mode, the image is first split to columns with `ADDCOL` or `,` in Divide ratio, then each column is split to regions with `ADDROW` or `;` in Divide ratio.

In any case, the conversion of prompt clauses to rows and columns is from top to bottom, left to right.

```
(blue sky:1.2) ADDCOL
green hair twintail ADDCOL
(aquarium:1.3) ADDROW
(messy desk:1.2) ADDCOL
orange dress and sofa
```

```
Active : On
Use base prompt : Off
Divide mode : Horizontal
Divide Ratio : 1,2,1,1;2,4,6
Base Ratio : 
```

![2d](https://github.com/hako-mikan/sd-webui-regional-prompter/blob/imgs/2d.jpg)

																		
																		 
																																					
																																																								   
																																			 
																																																											  
																																																											  
																																			  
																																																																  
																																																																																
																																																																																																																																			

																												

## <a id="visualize">Visualise and make template</a>
Areas can be visualized and templates for prompts can be created.

![tutorial](https://github.com/hako-mikan/sd-webui-regional-prompter/blob/imgs/tutorial.jpg)

Enter the area ratio and press the button to make the area appear. Next, copy and paste the prompt template into the prompt input field.

```
fantasy ADDCOMM
sky ADDROW
castle ADDROW
street stalls ADDCOL
2girls eating and walking on street ADDCOL
street stalls
```
Result is following,
![tutorial](https://github.com/hako-mikan/sd-webui-regional-prompter/blob/imgs/sample3.jpg)

																						  
 

## <a id="inpaint">Mask regions aka inpaint+ (experimental function)</a>
It is now possible to specify regions using either multiple hand drawn masks or an uploaded image containing said masks (more on that later).
- First, make sure you switch to `mask divide mode` next to `horizontal` / `vertical`. Otherwise the mask will be ignored and regions will be split to as usual.
- Set `canvas width and height` according to desired image's, then press `create mask area`. If a different ratio or size is specified, the masks may be applied inaccurately (like in inpaint "just resize").
- Draw an outline of the region desired in the canvas area, or paint it fully, then press `draw region`. This will add a filled polygon corresponding to the mask, coloured according to the `region` number you're on.
- Pressing `draw region` will increment region by +1, allowing to draw the next region quickly. It will also keep a list of which regions were used for building the masks later. Up to ~360~ 256 regions can be used currently.
- It's possible to add to existing regions by selecting their previously used colour and drawing as usual. There's currently no way to clear regions except for a new mask area (this may be added).
- The `create mask area` button will show the mask of the region you've drawn previously, as it will be applied to prompt, according to the `region` number. The masks are detected via the region's unique colour.
- Once the region masks are ready, write your prompt as usual: Divide ratios are ignored. Base ratios still apply to each region. All flags are supported, and all BREAK / ADDX keywords (ROW/COL will just be converted to BREAK). Attention and latent mode supported (loras maybe).
- `Base` has unique rules in mask mode: When base is off, any non coloured regions are add to the first mask (therefore should be filled with the first prompt). When base is on, any non coloured regions will receive the base prompt in full, whilst coloured regions will receive the usual base weight. This makes base a particularly useful tool for specifying scene / background, with base weight = 0.
- For those wishing to upload masks instead of drawing: Note that this feature is still **very much a WIP**. All colours must be tagged somehow for the mask to apply (either change the LCOLOUR variable in code or manually add each of the colours to the image once). The colours are all variants of `HSV(degree,50%,50%)`, where degree (0:360) is calculated as the maximally distant value from all previous colours (so colours are easily distinguishable). The first few values are essentially: 0, 180, 90, 270, 45, 135, 225, 315, 22.5 and so on. The choice of colours decides to which region they correspond.

![RegionalMaskGuideB](https://github.com/hako-mikan/sd-webui-regional-prompter/blob/imgs/RegionalMaskGuideB.jpg)


Here is sample and code  
using mask and prompt`landscape BREAK moon BREAK girl`.
Using XYZ plot prompt S/R, changed `moon BREAK girl` to others.  
![RegionalMaskSample](https://github.com/hako-mikan/sd-webui-regional-prompter/blob/imgs/isample1.png)



## <a id="divprompt">region specification by prompt (experimental)</a>
The region is specified by the prompt. The picture below was created with the following prompt, but the prompt `apple printed` should only affect the shirt, but the actual apples are shown and so on. 
```
lady smiling and sitting, twintails green hair, white skirt, apple printed shirt
```
![prompt](https://github.com/hako-mikan/sd-webui-regional-prompter/blob/imgs/psample1.png)
If you enhance the effect of `apple printed` to `:1.4`, you get, 

![prompt](https://github.com/hako-mikan/sd-webui-regional-prompter/blob/imgs/psample4.png)
The prompt region specification allows you to calculate the region for the "shirt" and adapt the "printed apples".

![prompt](https://github.com/hako-mikan/sd-webui-regional-prompter/blob/imgs/psample6.png)
```
lady smiling and sitting, twintails green hair, white skirt, shirt BREAK
(apple printed:1.4),shirt
```
![prompt](https://github.com/hako-mikan/sd-webui-regional-prompter/blob/imgs/psample2.png)

### How to use
### syntax
```
baseprompt target1 target2 BREAK
effect1, target1 BREAK
effect2 ,target2
```
																																																																																			 

First, write the base prompt. In the base prompt, write the words (target1, target2) for which you want to create a mask. Next, separate them with BREAK. Next, write the prompt corresponding to target1. Then enter a comma and write target1. The order of the targets in the base prompt and the order of the BREAK-separated targets can be back to back.

```
target2 baseprompt target1  BREAK
effect1, target1 BREAK
effect2 ,target2
```
is also effective.

### threshold
The threshold used to determine the mask created by the prompt. This can be set as many times as there are masks, as the range varies widely depending on the target prompt. If multiple areas are used, enter them separated by commas. For example, hair tends to be ambiguous and requires a small value, while face tends to be large and requires a small value. These should be ordered by BREAK.

```
a lady ,hair, face  BREAK
red, hair BREAK
tanned ,face
```
`threshold : 0.4,0.6`
If only one input is given for multiple regions, they are all assumed to be the same value.

### Prompt and Prompt-EX
The difference is that in Prompt, duplicate areas are added, whereas in Prompt-EX, duplicate areas are overwritten sequentially. Since they are processed in order, setting a TARGET with a large area first makes it easier for the effect of small areas to remain unmuffled.

### Accuracy
In the case of a 512 x 512 image, Attention mode reduces the size of the region to about 8 x 8 pixels deep in the U-Net, so that small areas get mixed up; Latent mode calculates 64*64, so that the region is exact.  
```
girl hair twintail frills,ribbons, dress, face BREAK
girl, ,face
```
Prompt-EX/Attention
![prompt](https://github.com/hako-mikan/sd-webui-regional-prompter/blob/imgs/psample5.png)
Prompt-EX/Latent
![prompt](https://github.com/hako-mikan/sd-webui-regional-prompter/blob/imgs/psample3.png)


### Mask
When an image is generated, the generated mask is displayed. It is generated at the same size as the image, but is actually used at a much smaller size.

## Difference between base and common
```
a girl ADDCOMM (or ADDBASE)
red hair BREAK
green dress
```
If there is a prompt that says `a girl` in the common clause, region 1 is generated with the prompt `a girl , red hair`. In the base clause, if the base ratio is 0.2, it is generated with the prompt `a girl` * 0.2 + `red hair` * 0.8. Basically, common clause combines prompts, and base clause combines weights (like img2img denoising strength). You may want to try the base if the common prompt is too strong, or fine tune the (emphasis).
The immediate strength that corresponds to the target should be stronger than normal. Even 1.6 doesn't break anything.

### Acknowledgments
I thank [furusu](https://note.com/gcem156) for suggesting the Attention couple, [opparco](https://github.com/opparco) for suggesting the Latent couple, and [Symbiomatrix](https://github.com/Symbiomatrix) for helping to create the 2D generation code.



## Updates
- New feature, "2D-Region"
- New generation method "Latent" added. Generation is slower, but LoRA can be separated to some extent.
- Supports over 75 tokens
- Common prompts can be set
- Setting parameters saved in PNG info
