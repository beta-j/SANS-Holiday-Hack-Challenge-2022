# OBJECTIVE 4 - Recover the Web Ring #
## 4F - Glamtariel's Fountain ##

### OBJECTIVE : ###
>Stare into Glamtariel’s fountain and see if you can find the ring!  What is the filename of the ring she presents you?  Talk to Hal Tandybuck in the Web Ring for hints.
#  

## HINTS: ##
<details>
  <summary>Hints provided for Objective 4F</summary>
  
>-	Early parts of this challenge can be solved by focusing on Glamtariel’s WORDS.
>-	Sometimes we can hit web pages with [XXE](https://owasp.org/www-community/vulnerabilities/XML_External_Entity_(XXE)_Processing) when they aren’t expecting it!
</details>

#  

## PROCEDURE : ##
Based on the hints we are given for this objective it looks like we’re going to need to use **XML injection** to crack this.  We are presented with an image of Glamtariel and one of her fountain and a number of objects we can drag onto either one of them.  For every item we drag the fountain and Glamtariel give us some information – some of the words are in CAPS and one of the hints seems to indicate that we should pay special attention to these words:
**`PATH, TRAFFIC FLIES, TAMPER, APP , TYPE, SIMPLE FORMAT, RINGLIST`**

Using Burpsuite to intercept the web requests we see that the site is using json to format its messages.  However, if we change the `Content-Type` to `xml` and convert the json payload to xml format (I used [https://www.freeformatter.com/json-to-xml-converter.html](https://www.freeformatter.com/json-to-xml-converter.html) for this), the website seems to still function normally – so it looks like we can feed the website xml formatted payloads instead of json.

Glamtariel conveniently tells us that she keeps all of her rings in her `RINGLIST` file and that she likes using a SIMPLE FORMAT for such a list – so in all likelihood we must be looking for a file called `ringlist.txt`.

Now that we’ve determined that we can pass on XML to the website we can try a basic test for XXE vulnerability by using a `REPLACE` ([I used code found here for this bit](https://github.com/payloadbox/xxe-injection-payload-list)).  Instead of passing `img4` directly as the value for `imgDrop` we assign it to a variable called `example` and then pass the contents of `example` to `imgDrop`. In this case the response we receive is exactly the same – which is great news because it means that the site is vulnerable to XXE 😊

![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/5b5c8aff-63b4-4cfc-9013-e60a421496f7)



Now we can try a more complex file disclosure attack by passing the path of a file we want to peek into.   We’re assuming that we’re looking for a file called `ringlist.txt` and by inspecting the website sources we know that there are a number of directories as follows:
```
/static/css/
/static/images/
/static/js
```
![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/4bfdae8a-beea-46de-a332-d80857041e51)

Oh...and we mustn’t forget that to prepend the `/app/` directory (That’s what the ‘APP’ hint was for!)

So, we can craft a payload to look for `ringlist.txt` in these directories, eg:
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE replace [<!ENTITY ent SYSTEM "file:///app/static/images/ringlist.txt"> ]>
<root>
	<imgDrop>&ent;</imgDrop>
	<reqType>xml</reqType>
	<who>princess</who>
</root>
```
This returns a Pretty interesting response – we did get the contents of the `ringlist.txt` but apparently Glamtariel doesn’t keep her secrets there anymore.  However, we are told to visit  `https://glamtarielsfountain.com/static/images/pholder-morethantopsupersecret63842.png` where we are served up an image of a top-secret looking manila folder. 

![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/01b6d5bb-39c9-4198-b0f3-b3da7bb4ac14)




Looking closely at the folder we see that the folder itself is labelled `x_phial_pholder_2022` and that it has two files in it called `bluering.txt` and `redring.txt`.  By modifying the xml payload, we used earlier we can peek into the contents of these two text files that are located in the `/app/static/images/x_phial_pholder/2022/` directory.

### Bluering.txt: ###
>"I love these fancy blue rings! You can see we have two of them. Not magical or anything, just really pretty.^She definitely tries to convince everyone that the blue ones are her favorites. I'm not so sure though.",

### Redring.txt: ###
>“Hmmm, you still seem awfully interested in these rings. I can't blame you, they are pretty nice.^Oooooh, I can just tell she'd like to talk about them some more.",

...that’s nothing we don’t know already – so all this work for nothing?!  Well... maybe not... the website shows us two blue rings, a red ring and a fourth silver ring.  So maybe there’s a third file called `silverring.txt`? It’s definitely worth a shot...and sure enough we find another message inside `silverring.txt`:

![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/c862e094-dd2b-400e-aa01-2edb7da66f93)

### Silverring.txt: ###
```
  "appResp": "I'd so love to add that silver ring to my collection, but what's this? Someone has defiled my red ring! Click it out of the way please!.^Can't say that looks good. Someone has been up to no good. Probably that miserable Grinchum!",
  "droppedOn": "none",
  "visit":"static/images/x_phial_pholder_2022/redring-supersupersecret928164.png,267px,127px"
```
There’s another hint pointing us towards a new URL for a new png file – loading that up gives us a close-up view of the red ring.   There’s an inscription on the inside of the ring that says `goldring_to_be_deleted.txt`.

Now things are surely getting interesting – there’s a fifth ring which is set to be deleted – maybe we can have a peek into `goldring_to_be_deleted.txt` using the same XXE method...

![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/3b945782-3e0e-4d0a-8714-3634b611472d)


### Goldring_to_be_deleted.txt: ###
> "Hmmm, and I thought you wanted me to take a look at that pretty silver ring, but instead, you've made a pretty bold REQuest. That's ok, but even if I knew anything about such things, I'd only use a secret TYPE of tongue to discuss them.^She's definitely hiding something.",

This is quite a cryptic message, but so far this objective has shown that looking at the words in CAPS helps point in the right direction.  In this case we have `REQTYPE`.  After spending hours hammering away at this objective – this is immediately familiar.  We are passing on three variables in our xml payload; `imgDrop`, `who` and `reqType`. 
So far, we’ve been passing our XXE payload to the `imgDrop` variable, but maybe we should try passing it to `reqType` instead:

```diff
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE replace [<!ENTITY ent SYSTEM "file:///app/static/images/x_phial_pholder_2022/goldring_to_be_deleted.txt"> ]>
<root>
-	<imgDrop>&ent;</imgDrop>
- <reqType>xml</reqType>
+	<imgDrop>img1</imgDrop>
+	<reqType>&ent;</reqType>
	<who>princess</who>
</root>
```

We now get the following response:
```
  "appResp": "No, really I couldn't. Really? I can have the beautiful silver ring? I shouldn't, but if you insist, I accept! In return, behold, one of Kringle's golden rings! Grinchum dropped this one nearby. Makes one wonder how 'precious' it really was to him. Though I haven't touched it myself, I've been keeping it safe until someone trustworthy such as yourself came along. Congratulations!^Wow, I have never seen that before! She must really trust you!",
  "droppedOn": "none",
  "visit":"static/images/x_phial_pholder_2022/goldring-morethansupertopsecret76394734.png,200px,290px"
```

And behold we are given a URL to the gold ring and the answer to this objective: **`goldring-morethansupertopsecret76394734.png`**! 

![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/0c94f82a-d9db-489d-a6c1-a1eb399cd5cc)




 
