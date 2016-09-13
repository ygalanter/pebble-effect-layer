<b>EffectLayer</b> is a visual effect layer for Pebble Smartwatch. User places the layer over screen at set coordinates and special effects are applied to that area of the screen. Effects work both Aplite/Basalt/Chalk platforms unless specified (e.g. Blur won't work on Aplite)

To use the library add <code>#include <pebble-effect-layer/pebble-effect-layer.h></code> to your source. EffectLayer library is implemented in efficient pay-to-play way so only effects that you actualy use get compiled into your binary.

![Inverter](http://i.imgur.com/6t9r3qa.gif "Inverter")
![Bitmap Mask](http://i.imgur.com/JspSsx1.gif "Bitmap Mask")
![Text Mask](http://i.imgur.com/EdKu49w.png "Text Mask")


Currently supported effects:
<ul>
<li>Invert</li>
<li>Invert b/w only</li>
<li>Invert brightness, preserve hue</li>
<li>Vertical Mirror</li>
<li>Horizontal Mirror</li>
<li>Rotate 90 degrees (counter- or clock-wise)</li>
<li>Blur</li>
<li>Zoom</li>
<li>Lens</li>
<li>Mask</li>
<li>FPS</li>
<li>Shadow</li>
<li>Outline</li>
<li>Colorize (replace a color with another)</li>
<li>Color Swap</li>
</ul>
<hr>
<h3>Functions</h3>

<code>EffectLayer* effect_layer_create(GRect frame)</code> - creates effect layer and returns pointer to it. Parameter is GRect with coordinates and layer size, for example <code>EffectLayer* effect_layer = effect_layer_create(GRect(0, 0, 144, 168));</code> will cover entire screen

<code>void effect_layer_destroy(EffectLayer *effect_layer)</code> - destroys effect layer and frees memory.

<code>Layer* effect_layer_get_layer(EffectLayer *effect_layer)</code> - gets underlying Layer of the effect layer for basic manipulations such as addin to Window root layer or layer's changing frame or bounds

<code>Layer* effect_layer_set_frame(EffectLayer *effect_layer, GRect frame)</code> - shortcut to set the frame of the effect layer.

<code>void effect_layer_add_effect(EffectLayer *effect_layer, effect_cb* effect, void* param)</code> - adds effect to the layer. Parameters are - effect layer that you created previously, name of the function to perform effect, optional data to pass to the effect. User can call this function multiple times on the same effect layer, adding multiple effects, so there's no need to create multiple layers. At this time library has following built in effect functions to represent the effects listed above:
<ul>
<li>effect_invert</li>
<li>effect_mirror_vertical</li>
<li>effect_mirror_horizontal</li>
<li>effect_rotate_90_degrees</li>
<li>effect_blur</li>
<li>effect_zoom</li>
<li>effect_lens</li>
<li>effect_mask</li>
<li>effect_fps</li>
<li>effect_shadow</li>
<li>effect_outline</li>
<li>effect_colorize</li>
<li>effect_colorswap</li>
</ul>

<code>void effect_layer_remove_effect(EffectLayer *effect_layer)</code> - removes last added effect from the layer. parameters is effect layer that you created previously.

<h3>Usage</h3>

<code>effect_layer_add_effect(my_effect_layer, effect_invert, NULL);</code> - adds inverter effect

<code>effect_layer_add_effect(my_effect_layer, effect_invert_bw_only, NULL);</code> - Inverts pixels within the layer bounds if the pixels equal GColorBlack or GColorWhite. All other pixels are left untouched allowing for 2.x style black and white themes with splashes of color added for Pebble Times.

<code>effect_layer_add_effect(my_effect_layer, effect_invert_brightness, NULL);</code> - Inverts the perceived brightness of pixels within the layer bounds, except for pixels that equal GColorBlack and GColorWhite (can be used with effect_invert_bw_only to invert black and white pixels). The hue of the pixels is left mostly intact and was initially intended to maintain good contrast of color in cases where an app/watchface has both a light and dark mode with color UI elements without having to have multiple sets of color resources/coding. Works best when color elements are all designed to have good contrast on either a light or dark background and then the effect layer is hidden for that background and shown for the opposite background.

<code>effect_layer_add_effect(my_effect_layer, effect_mirror_vertical, NULL);</code> - adds vertical mirror effect 

<code>effect_layer_add_effect(my_effect_layer, effect_mirror_horizontal, NULL);</code> - adds horizontal mirror effect

<code>effect_layer_add_effect(my_effect_layer, effect_rotate_90_degrees, (void *)true);</code> - rotates 90 degrees counterclockwise

<code>effect_layer_add_effect(my_effect_layer, effect_rotate_90_degrees, (void *)false);</code> - rotates 90 degrees
clockwise

<code>effect_layer_add_effect(my_effect_layer, effect_blur, (void *)my_radius);</code> - Creates blur effect (on Basalt/Pebble Time only), where my_radius is uint_8t radius of the blur.

<code>effect_layer_add_effect(my_effect_layer, effect_zoom, EL_ZOOM(x,y));</code> - Zooms screen area under layer by x and y

<code>effect_layer_add_effect(my_effect_layer, effect_lens, EL_LENS(f,d));</code> - Applies Lens effect, where f & d are focal distance of the lens and distane to object respectfully.

<code>effect_layer_add_effect(my_effect_layer, effect_mask, mask);</code> - Applies Mask, achieving various transparency effects. See <a href="http://codecorner.galanter.net/2015/04/15/mask-effect-for-effectlayer-for-pebble/" target="_blank">this article</a> on usage examples.

<code>effect_layer_add_effect(my_effect_layer, effect_fps, EffectFPS);</code> - displays average FPS of the app

<code>effect_layer_add_effect(my_effect_layer, effect_shadow, &shadow);</code> - adds shadow of a given color to objects on screen of given color. <code>shadow</code> is a parameter of type <code>EffectOffset</code>:
```c
typedef struct {
   GColor orig_color; //color of pixel being ofset
   GColor offset_color; //new color of pixel at offset coords
   int8_t offset_x; // horizontal ofset
   int8_t offset_y; // vertical offset
} EffectOffset;
```   
For example if you want to give all red text under layer a yellow shadow 2 pixels long, you parameter would be:
```c
EffectOffset shadow;
shadow.orig_color = GColorRed;
shadow.offset_color = GColorYellow;
shadow.offset_y = 2;  
shadow.offset_x = 2;
```
You can specify option in the parameter <code>shadow.option = 1;</code> to display long continuous shadow (to use this on Aplite you also need to specify <code>shadow.aplite_visited</code> array to keep track of set pixels. See <a href="Color Shadow">https://github.com/ygalanter/Color_Shadow/</a> watchface for example implementation.

<code>effect_layer_add_effect(my_effect_layer, effect_outline, &shadow);</code> - Similar to the Shadow effect, however the color is added in all directions.

<code>effect_layer_add_effect(my_effect_layer, effect_colorize, &colorpair);</code> - Replaces the first color with the second, useful for 'painting' a b&w watchface. <code>colorpair</code> is a parameter of type <code>EffectColorpair</code>:
```c
typedef struct {
  GColor firstColor;  // first color (target for colorize, one of set in colorswap)
  GColor secondColor; // second color (new color for colorize, other of set in colorswap)
} EffectColorpair;
```
For example if you want to take your black and white watchface, and make it black and green, your parameter for the above call would be:
```c
EffectColorpair colorpair;
colorpair.firstColor = GColorWhite;
colorpair.secondColor = GColorGreen;
```

<code>effect_layer_add_effect(my_effect_layer, effect_colorswap, &colorpair);</code> - Similar to the Colorize effect, but also replaces the second color with the first, effectively like a targeted invert (useful for when you have two colors and just want them flipped, but they're either not a direct invert, or you don't want to affect other colors)

<h3>Extensions</h3>

Library is user-extendable, if you define your function in format

<code>void effect_my_own_cool_effect(GContext* ctx, GRect position, void* param) { /* cool effect code */ }</code>

you can add it to EffectLayer in manner similar to above:

<code>effect_layer_add_effect(my_effect_layer, effect_my_own_cool_effect, param);</code> 

<h3>Usage examples</h3>

<a href="https://github.com/ygalanter/SimpleStriped">Simple Striped watchface</a> uses Mask effect to show background image thru transparent text<br>
<a href="https://github.com/ygalanter/Tv-Time">TV Time watchaface</a> uses inverter effect for battery info and blur effect for antialiasing fonts</a><br>
<a href="https://github.com/ygalanter/Long_Shadow">Long Shadow watchface</a> uses shadow effect in "long" mode</a>


