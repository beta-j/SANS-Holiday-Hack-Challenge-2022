# OBJECTIVE 4 - Recover the Web Ring #
## 4E - Open Boria Mine Door ##

### OBJECTIVE : ###
>Open the door to the Boria Mines. Help Alabaster Snowball in the Web Ring to get some hints for this challenge.
#  

## HINTS: ##
<details>
  <summary>Hints provided for Objective 4E</summary>
  
>-  The locks take input, render some type of image, and process on the back end to unlock. To start, take a good look at the source HTML/JavaScript.
>-	Understanding how [Content-Security-Policy](https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html) works can help with this challenge
>-	Developers use both client- and server-side [input validation](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html) to keep out naughty input.

</details>

#  

## PROCEDURE : ##
My first thought was to try and find a keyboard input that resembles the elven fonts enough to fool the captcha system.  
After some trial and error, I found that I could unlock Pin1 by entering a string of `mmmmmmmmmmmmmmmmmm` which only worked since the two pins are white and on the same line.

For Pin2 I could see by examining the code, that the input accepts html (There’s a convenient reminder in the code’s inline documentation to filter out html next time).  I attempted to use `<img src=></img>` tags to link to external images that can connect the two pins.  In the black box I could see the thumbnail for a broken image link which told me that the html was working but also told me that the code would not accept image files as inputs to render.  
Next, I figured that I could use html tags to render svg shapes.  By looking at the sources for the frame I could determine that each black box had a width of 200 pixels and height of 170 pixels and so by using the input: ``<svg width=200 height=170><rect width=200 height=170 “fill:rgb(255,255,255)" /></svg>`` I was able to fill Pin2 entirely in white.

Pin3 came this close to driving me crazy.  I tried all sorts of approaches and different svg shapes and techniques.  Until finally – just out of sheer desperation I changed the ``“fill:rgb(0,0,255)”`` of my code to ``“fill=”#0000FF”`` and it worked!  No idea why – but sometimes pen-testing is just about trying stuff until it works eh!  So the following code unlocks Pin3:
```html
<svg width=200 height=170><rect width=200 height=170 fill=#0000FF /></svg>
```

Pin4 attempts to filter out some special characters by using the `string.replace` JavaScript function.  This is easily bypassed since by using this function on its own, JavaScript will only replace the first matching value it finds. So, I was able to go around this very easily just by placing an extra `<>` in front of the input string.  Also, the svg code had to be modified slightly to draw two rectangles on top of each other now:
```html
<><svg width=200 height=170><rect width=200 height=80 fill=white /><rect x=0 y=60 width=200 height=90 fill=blue /></svg>
```
I also realised that if instead of clicking on the ‘GO’ button, I hit the enter key to submit the input, I could just pass on the html without prepending it with `<>` this seems to indicate that the input sanitisation is only happening when the **‘GO’** button is clicked.  In fact, on closer examination of the code we can see that the `sanitizeInput` script is being called by an `onblur` event.  
 ![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/399af1c2-2a50-49a4-b42c-24ee6fa1c5e1)

This means that the input text is sanitised as soon as the text box loses focus (eg. When the user clicks on another text box, presses TAB or clicks the ‘GO’ button.  However, if we simply submit the text by hitting the ENTER key the text box never loses focus and the sanitizeInput script is never called!

Pin5 builds on the input sanitisation of Pin4 by adding the `/gi` modifier to the `string.replace` function which should make JavaScript match on every instance of `“`,`’`,`<` or `>`in the string.  But it still calls the `sanitizeInput` function with an `onblur` event.  So, we can simply submit our html by hitting the ENTER key.  The svg tag was modified to draw a blue box with a diagonal red line across it:
```html
<svg width=200 height=170><rect width=200 height=170 fill=blue /><line x1=0 y1=130 x2=200 y2=45 stroke=red stroke-width=20 /></svg>>>
```

This brings us to the final pin; Pin6.  Strangely enough it seems that this one doesn’t have any attempt at input sanitisation at all and we just need to modify the svg tag to unlock it:
```html
<svg width=200 height=170><rect width=200 height=170 fill=blue /><line x1=0 y1=70 x2=200 y2=110 stroke=red stroke-width=20 /><line x1=0 y1=30 x2=200 y2=30 stroke=#00FF00 stroke-width=20 /></svg>
```

![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/fc17a3f7-3ddf-4a29-bcb8-55035b30cc25)
