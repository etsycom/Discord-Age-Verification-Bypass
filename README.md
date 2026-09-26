# Discord Age Verification Bypass
## Step 1:
You need to enable the setting that allows you to open the inspect element inside the Discord app. To do this, close Discord and navigate to `%appdata%/discord/settings.json` on Windows, or `~/.config/discord/settings.json` on Linux, and add the following line:
```json
"DANGEROUS_ENABLE_DEVTOOLS_ONLY_ENABLE_IF_YOU_KNOW_WHAT_YOURE_DOING": true
```
Once you've done this, reopen Discord and press `ctrl + shift + i`; this should open the inspect element window. If it does not, ensure the settings.json file is properly formatted.
## Step 2:
Open the console and paste the following line (NOTE: you may need to type 'allow pasting' before the console lets you paste anything):
```js
webpackChunkdiscord_app.push([[Symbol()],{},f=>{try{Object.values(f.c).some((e)=>{if(e.id=='174459'){e.exports.default.extendSuperProperties(e)}else if(e.id=='734057')for(const c of Object.values(e.exports.A.loadAllGuildAndPrivateChannelsFromDisk()))c.nsfw_=false})}catch{}}]);
```
After entering the code, you can close the inspect window and access NSFW channels without having verified your age. You will need to do this every time you start Discord, although some file trickery could be done to make it so you don't have to (post an issue about this if anyone cares).
# Code Explanation
Now I get it, you're probably saying something along the lines of, "Why on Earth would I paste some random code from some random guy into my Discord client??" If you don't understand the code, that's what you should be saying. Here's a more readable version of the same code:
```js
webpackChunkdiscord_app.push([[Symbol()], {}, f => {
    try {
        Object.values(f.c).some((e) => {
            if (e.id == '174459')
                e.exports.default.extendSuperProperties(e)
            else if (e.id == '734057')
                for (const c of Object.values(e.exports.A.loadAllGuildAndPrivateChannelsFromDisk()))
                    c.nsfw_ = false
        })
    }
    catch {
    }
}]);
```
Discord functions on a bunch of modules that all serve different purposes. For what we're trying to do, we need to access two modules: the module that stores information on channels, and the module that handles retrieving messages.

The way we go about obtaining these modules is a bit finicky. Firstly, we push an element onto `webpackChunkdiscord_app`. The push method that's called here is Discord's own implementation which takes an array with three elements, the third of which is a function. Inside this function, we iterate through each module and check to see if the IDs match those of the modules we're looking for.

We first check to see if we've found the module that handles retrieving messages (id 174459). When we find it, we call the `extendSuperProperties` method, and pass it `e` (the module itself). Whenever Discord wants to make a `GET` request, it contains a header called `X-Super-Properties`; this is how Discord determines if it should ask you for age verification or not. One flaw of this header however, is that Discord always encodes it in base 64. By passing `e` to the `extendSuperProperties` method, it makes it impossible for Discord to encode it in base 64, so it doesn't set the header. This now lets us receive `GET` responses from NSFW channels.

Now that we can read the channels with the `GET` requests, it would be nice to see them in Discord.

Upon finding the module that stores information on channels (id 734057), we call its `loadAllGuildAndPrivateChannelsFromDisk` method. This returns a list of every channel ID stored locally by Discord, which we iterate through and make it so Discord doesn't think any of them are NSFW. Now, Discord doesn't give us the age verification screen and we can use the channel normally.

The part of this that could prove to be problematic is how it checks for the module id. I'm not sure if these are random every time Discord updates, but if they ever end up changing, this won't work anymore. I'm also not 100% sure if clobbering the `X-Super-Properties` header breaks anything else, but I haven't seen anything wrong so far.



I started working on this as soon as I saw the update. It took about 3 hours, but I found it fun overall; about as fun as cracking Adobe products. I rate it an 8/10 on the fun scale.
