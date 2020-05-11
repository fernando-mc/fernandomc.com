+++
Description = "A look at building a silly game with Vue.js"
Tags = [
  "Serverless",
  "Vue.js"
]
Categories = [
  "Projects",
]
title = "Creating a Simple Game with Vue.js and Vercel"
publishdate = "2020-05-11T12:13:24-07:00"
date = "2020-05-11T12:13:24-07:00"
[image]
    feature = "/images/20-projects-20-days/imhungry.png"
+++

As the 6th project in my [20 projects in 20 days series](https://fernandomc.com/posts/twenty-projects-in-twenty-days/) I made a game using [Vue.js](https://vuejs.org/) and deployed it with [Vercel](https://vercel.com/).

Here's how I did it.
<!--more-->

## What's the Game?

Well, it's kinda silly if we're being honest. You're an emoji character that you move around with the arrow keys looking for goal, cats and other things. [Try it](https://vue-game.now.sh/) for yourself! Use the up, down, left, and right arrow keys to navigate around.

## Making the Game

In order to make this game, I relied on Vue.js. To take a look at my code, you can clone it [here](https://github.com/fernando-mc/vue-game) and then open the `index.html` file inside the `frontend` folder in your browser.

- `git clone https://github.com/fernando-mc/vue-game.git`

After you clone it, you'll see two files in the `frontend` folder:

- `index.html`
- `index.js`

**`index.html`**

In the HTML file, I create a simple HTML website:

```html
<meta charset="utf-8"/>
<html>
    <title>I'm hungry</title>
    <head>
        <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    </head>
    <body style="background: teal">
    <div id='game'>
        <input type="hidden"
            v-on:keyup.up="moveUp" 
            v-on:keyup.down="moveDown" 
            v-on:keyup.left="moveLeft"
            v-on:keyup.right="moveRight"
        >
        <div 
            v-bind:style="{ position: divPosition, left: goalLocationX + 'px', top: goalLocationY + 'px' }">
            {{ goal }}
        </div>
        <div id='face'
            v-bind:style="{ position: divPosition, left: locationX + 'px', top: locationY + 'px' }">
            {{ face }}
        </div>
        
        <div >{{ userScore }}</div>
    </div>
            
        <script src="index.js"></script>
    </body>
</html>
```

First, and importantly, I have the `<meta charset="utf-8"/>` tag, that's needed to make sure my emoji's appear correctly. 

Then I use the JSDeliver CDN link for Vue.js to make sure I can use it in the page.

From there, I have a `game` div that includes all the different parts of the game that i'll be using.

It starts with a hidden `input` tag that has several `v-on` events that it watches for. In this case, it looks for the `keyup` action for the arrow keys (`up`, `down`, `left`, and `right`). When one of those keys is pressed, it runs a function that we'll look at in a moment.

There's also a few other divs on the page to display:

1. The 'goal' - what the emoji character is chasing in the game.
2. The 'face' - what our emoji character is at the moment.
3. The 'userScore' - Really the number of 'goals' eaten times ten.

At the end of the page, it then brings in the `index.js` file.

**`index.js`**

Inside of here, we create a new Vue `app`:

```js
var app =  new Vue({
    el: '#game',
    data: {
      face: ':)',
      goal: 'goal',
      singleUnusedEl: '',
      userScore: 0,
      locationX: 100,
      locationY: 100,
      goalLocationX: 160,
      goalLocationY: 180,
      divPosition: 'absolute'
    },
```

We initialize this with some starting data - like the initial face, what the "goal" element contains, the score, and the location of the goal and the character. In the next section, we create a function that will bind our arrow key presses to the functions we create to move the character around:


```js
    // ...
    mounted: function () {
      this.$nextTick(function () {
        // Code that will run only after the
        // entire view has been rendered
        window.addEventListener("keydown", function(e) {
          if (e.keyCode === 37) {app.moveLeft()};
          if (e.keyCode === 38) {app.moveUp()};
          if (e.keyCode === 39) {app.moveRight()};
          if (e.keyCode === 40) {app.moveDown()};
        });
      });
    },
```

The next section of the file is the `methods` section, which allows us to define some methods we want to run when different things happen on our page. Here are the highlights of each of these:

1. `check` - This function allows us to check if the user has gotten close enough to the goal for us to record it. When the user does, it regenerates the 'goal' somewhere else, adds points to the score, and levels up the character.

```js
  check: function() {
    if (Math.abs(this.goalLocationX - this.locationX) + Math.abs(this.goalLocationY - this.locationY) < 20) {
      this.generateGoal();
      this.addPoints();
      this.levelUp();
    }
  },
```

2. `generateGoal` - This is the function that finds a random spot on the canvas to move the goal to for the user to get it.

```js
  generateGoal: function() {
    console.log('more goal!');
    this.goalLocationX = Math.ceil(Math.floor((Math.random() * 300) + 1) / 10) * 10; 
    this.goalLocationY = Math.ceil(Math.floor((Math.random() * 300) + 1) / 10) * 10;
  },
```

3. `levelUp` - When the user gets to the goal, they level up and the emoji changes.

```js
  levelUp: function() {
    if (this.userScore === 10){
      this.face = "👶"
      this.goal = "🍼"
    };
    if (this.userScore === 20){
      this.face = "🧒"
      this.goal = "🍕"
    };
    // ... lots more of theses
  }
```

4. `addPoints` - This increments the score recorded in the top left. 

```js
addPoints: function() {
    console.log('more points!');
    this.userScore += 10;
  },
```


5. `moveUp` - This and the three other functions named like it move the emoji character around the page.

```js
  moveUp: function () {
    console.log('uped!');
    this.locationY -= 10;
    this.check();
  },
```

And that's it really! You can try this out for yourself in your own browser and play around with it, change the story and have fun!

## Deploying the Game

In order to deploy this for everyone, I made a [Vercel.com](https://vercel.com/) account for free and installed the Vercel CLI with NPM.

- `npm i -g vercel`

After that, I logged into my Vercel account from the CLI:

- `vercel login`

Then I just entered my email and Vercel sent me a quick verification email. After clicking the verification link I went through a quick setup process for my project with the `vercel` command.

- `vercel`

From there I followed the prompts and made sure to specify my project folder and then my project was deployed! It's live [here](https://vue-game.now.sh/) now!

Want to keep an eye out for other projects I'm working on? Bookmark [this post](https://fernandomc.com/posts/twenty-projects-in-twenty-days/) for the other projects in this 20 projects series or sign up for my [mailing list](/mailing-list)!

