# Discord Music Player
![npm](https://img.shields.io/npm/dt/discord-music-player?style=for-the-badge)
![npm](https://img.shields.io/npm/v/discord-music-player?style=for-the-badge)
![CodeFactor Grade](https://img.shields.io/codefactor/grade/github/SushiBtw/discord-music-player?color=%2348aaf1&style=for-the-badge)

### Note: This is the __DEVELOPMENT__ version of Discord Music Player for Discord.JS v13!

Discord Music Player is a powerful [Node.js](https://nodejs.org) module that allows you to easily implement music commands.
**Everything** is customizable, and everything can be done using this package - **there are no limitations!**

This package supports YouTube Videos and Playlists, Spotify Songs and Playlists.
Package is maintained by [SushiBtw](https://github.com/SushiBtw), but is an early fork of Androz2091.

### Requirements:
- [Discord.js v13](https://www.npmjs.com/package/discord.js),
- [Node.JS v16](https://nodejs.org/),

# Installation
*Node.JS v16 or newer is required to run this module.*
```sh
npm install --save discord-music-player@dev
```
Install **@discordjs/opus**:
```sh
npm install --save @discordjs/opus
```
**Install [FFMPEG](https://www.ffmpeg.org/download.html)!**

# Getting Started
**The code bellow, will show you how to use DMP in your code.**
*Please define your **Player** after the **client/bot** definition.*
```js
const Discord = require("discord.js");
const client = new Discord.Client({
    intents: [Intents.FLAGS.GUILDS, Intents.FLAGS.GUILD_MESSAGES, Intents.FLAGS.GUILD_VOICE_STATES]
});
const settings = {
    prefix: '!',
    token: 'YourBotTokenHere'
};

const { Player } = require("discord-music-player@8.0.0-dev2");
const player = new Player(client, {
    leaveOnEmpty: false, // This options are optional.
});
// You can define the Player as *client.player* to easly access it.
client.player = player;

client.on("ready", () => {
    console.log("I am ready to Play with DMP 🎶");
});

client.login(settings.token);
```

# Example Usage
*WARNING: This page is being designed, all working functions can be found bellow.*
```js
const { RepeatMode } = require('discord-music-player');

client.on('messageCreate', async (message) => {
    const args = message.content.slice(settings.prefix.length).trim().split(/ +/g);
    const command = args.shift();
    let guildQueue = client.player.getQueue(message.guild.id);

    if(command === 'play') {
        let queue = client.player.createQueue(message.guild.id);
        await queue.join(message.member.voice.channel);
        let song = await queue.play(args.join(' ')).catch(_ => {
            if(!guildQueue)
                queue.stop();
        });
    }

    if(command === 'playlist') {
        let queue = client.player.createQueue(message.guild.id);
        await queue.join(message.member.voice.channel);
        let song = await queue.playlist(args.join(' ')).catch(_ => {
            if(!guildQueue)
                queue.stop();
        });
    }

    if(command === 'skip') {
        guildQueue.skip();
    }

    if(command === 'stop') {
        guildQueue.stop();
    }

    if(command === 'removeLoop') {
        guildQueue.setRepeatMode(RepeatMode.DISABLED); // or 0 instead of RepeatMode.DISABLED
    }

    if(command === 'toggleLoop') {
        guildQueue.setRepeatMode(RepeatMode.SONG); // or 1 instead of RepeatMode.DISABLED
    }

    if(command === 'toggleQueueLoop') {
        guildQueue.setRepeatMode(RepeatMode.QUEUE); // or 2 instead of RepeatMode.DISABLED
    }

    if(command === 'setVolume') {
        guildQueue.setVolume(parseInt(args[0]));
    }

    if(command === 'seek') {
        guildQueue.seek(parseInt(args[0]) * 1000);
    }

    if(command === 'clearQueue') {
        guildQueue.clearQueue();
    }

    if(command === 'shuffle') {
        guildQueue.shuffle();
    }

    if(command === 'getQueue') {
        console.log(guildQueue);
    }

    if(command === 'getVolume') {
        console.log(guildQueue.volume)
    }

    if(command === 'nowPlaying') {
        console.log(`Now playing: ${guildQueue.nowPlaying}`);
    }

    if(command === 'pause') {
        guildQueue.setPaused(true);
    }

    if(command === 'resume') {
        guildQueue.setPaused(false);
    }

    if(command === 'remove') {
        guildQueue.remove(parseInt(args[0]));
    }
})
```

### Events:
```js
// Init the event listener only once (at the top of your code).
client.player
    // Emitted when channel was empty.
    .on('channelEmpty',  (queue) =>
        console.log(`Everyone left the Voice Channel, queue ended.`))
    // Emitted when a song was added to the queue.
    .on('songAdd',  (queue, song) =>
        console.log(`Song ${song} was added to the queue.`))
    // Emitted when a playlist was added to the queue.
    .on('playlistAdd',  (queue, playlist) =>
        console.log(`Playlist ${playlist} with ${playlist.songs.length} was added to the queue.`))
    // Emitted when there was no more music to play.
    .on('queueEnd',  (queue) =>
        console.log(`The queue has ended.`))
    // Emitted when a song changed.
    .on('songChanged', (queue, newSong, oldSong) =>
        console.log(`${newSong} is now playing.`))
    // Emitted when a first song in the queue started playing.
    .on('songFirst',  (queue, song) =>
        console.log(`Started playing ${song}.`))
    // Emitted when someone disconnected the bot from the channel.
    .on('clientDisconnect', (queue) =>
        console.log(`I was kicked from the Voice Channel, queue ended.`))
    // Emitted when deafenOnJoin is true and the bot was undeafened
    .on('clientUndeafen', (queue) =>
        console.log(`I got undefeanded.`))
    // Emitted when there was an error with NonAsync functions.
    .on('error', (error, queue) => {
        console.log(`Error: ${error} in ${queue.guild.name}`);
    });
```

# Passing custom data
While running the `Queue#createQueue()` method you can pass a `options#data` object to hold custom data.
This can be made in two ways:
```js
// Pass custom data
await player.createQueue(message.guild.id, {
    data: {
        queueInitMessage: message,
        myObject: 'this will stay with the queue :)',
        more: 'add more... there are no limitations...'
    }
});
// Or by using
queue.setData({
    whatever: 'you want :D'
});

// Access custom data
let queue = player.getQueue(message.guild.id);
let initMessage = queue.data.queueInitMessage;
await initMessage.channel.send(`This message object is hold in Queue :D`);
```
