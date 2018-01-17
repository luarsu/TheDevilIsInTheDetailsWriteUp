# The Devil is in the Details 
# Luis Armas Suarez

# 1.- Synopsis
The devil is in the detail is a first person horror-puzzle game inspired by some childhood nightmares of the team that developed it. In the game, we play as a child trapped in a nightmarish maze that has to solve the challenges in it to collect and bring three gnome statues to their corresponding place in order to enter the safe and warm house and wake up. However, this task will be harder than it seems, as we will be constantly chased by the Bill the Tar Man, a man eater ghoul that lives in the maze and doesn’t like intruders. 
The Maze is full of secrets, and in order to distinguish what’s real from what’s not, you will have to use the magic glasses. With them, you will be able to solve all the riddles in the maze and wake up before Bill catches you.

# 2.- First weeks of development
During the first weeks of development we started meeting to agree on a game concept that we all liked and that could fit the first person game restriction we had for this project. After each of us proposed several ideas and we discussed them as a group, we decided to go for a game in which a double vision mechanic would be a key element. All the different game concepts that were proposed during those days can be seen in the following link:

https://github.com/gwoch/Break-the-Bill/blob/master/README.md

After the main game mechanic concept was chosen we started thinking about a general theme that would work with the vision mode. After some discussion, we decided that a kind of “escape room” game placed in a maze where, instead of wanting to get out a room, we want to get in to be safe. That idea defined the roots of The Devil is in the Details, and through the weeks of development that concept has evolved and changed as new ideas came up.

# 3.- My contributions to the game
During the development of The Devil is on the Details we have had to work as a team and, as part of the requirements of the course, we all had to contribute to the programming, art and design of it. However, as we were formed by four artists and two programmers, being me one of those two programmers, we all tended to do more of what we were best at. As I’ve mentioned before, with my background in programming I ended up in the role of lead gameplay and AI programmer. However, as I’m also highly interested in game design, I also had an important role as gameplay and level designer.
I think that it is also important to mention that working in a team of 6 people the coordination and communication between them is crucial, and to achieve this and finish the project successfully someone had to be in charge of this. Even though this role wasn’t decided from the beginning and didn’t directly affect the game, due to the group dynamics and my role as game designer, I ended up also working as project manager along Pablo Bueno Melchor.
In this position I was in charge of coordinating everyone, dividing tasks, setting up deadlines, checking that everyone met them and, inside the game, I also had to make sure that all the different features that each of us programmed worked well together.
# 3.1.- Gameplay programming
# 3.1.1-The Tar Man AI 
![Bill the Tar Man](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/monster2.png)
One of the core elements of The Devil is in the Details is the Bill the Tar Man. The idea when we designed it was to make the player feel chased and in danger all the time, with no time to rest or to feel safe. The Tar Man tries to make the player feel in this kind of terrifying nightmares where a monster is chasing you and there is no way to escape from him, because sooner or later he will be right behind your back.
As I am highly interested in artificial intelligence, I decided to take the task of designing the monster and programming it. To start that I had to design the different behaviours that he was going to have, and they are the following:
As it has been said before, the Tar Man is constantly following you, and he is good at it. However, depending on some parameters, he will act one way or another. By default, the monster follows you at a normal speed, so he is quite slow but as he always know the best way to go to your location you’ll eventually find him. 
To do this, I programmed an event that when it’s called, the monster will receive the player’s current position and a command to go there with unreal’s AI MoveTo instruction. For this instruction to work properly, I had to add a navigation volume in the level that covered the areas where the player is. 
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/monsterAI1.png)
As this instruction worked really well in all the tests we made, we used it for the following behaviour.
However, the monster has other behaviours too. When the Tar Man sees the player, he screams something (so the players can notice that the monster has spotted them and run away) and he increases considerably his velocity. To do this, the logic for the monster to follow the player is quite similar, but there are a few differences. First of all, for the monster to spot the player, I added a pawn sensing component to it and defined the vision field it has. After that, I defined the event on pawn sensing so, when the monster sees the player, it sets a boolean to true so the behaviour tree of the monster knows that it has to perform that version of the following player event. I also had to add a delay that lasts for the same amount of time that the pawnsensing checks if the monster is seeing something, so in case that the monster stops seeing the player for that time it sets the boolean to false and goes back to the slow follow behaviour.
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/monsterAI2.png)
Also, the dialog/laugh/scream that the monster makes when he spots the player had to include a boolean so it doesn’t perform it only if he sees the player again after a few seconds. Otherwise, the monster would say something everytime that he stops seeing the player and sees him again even if its a second after.
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/monsterAI3.png)
The code of the event to follow the player is just like the slow one, but it also changes the monster speed.
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/monsterAI4.png)
Finally, the monster has another behaviour that is triggered randomly after 2-3 minutes if he hasn’t spotted the player. If this event is triggered and the monster isn't seeing the player, he will scream and run super fast to a random position in the maze. This can be convenient for the player or maybe really bad, because this is the mode in which the monster runs faster, so if the random position where he is going happens to be close to where the player is, it will be a disadvantage.
The event is triggered with the set timer by function instruction and a random interval of time, and the command to the monster to go to a random position is similar to the follow ones, but it chooses a random position in the navigable radius.
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/monsterAI5.png)
It also sets a boolean that prevents the behaviour tree to interrupt this behaviour until it is finished.
The final behaviour tree is called every game tick and looks like this, prioritizing first the following behaviour of the monster when it sees the player, then the go to random location behaviour and finally the default slow follow behaviour:
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/monsterAI6.png)
The entire Blueprint for the monster AI looks like this:
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/monsterAI7.png)
Finally, another feature that wasn’t included in the monster’s blueprint but that is related to it is the random sound spawn. Every time the monster sees the player he says something. However, in order to confuse the players and make them feel threatened all the time, Each 2-3 minutes, in a random interval, the a sound of the monster is spawned in the back of the player. The intention of this feature is to make them look around, check if the monster is close or not and make them doubt about where is the monster even when they hear a sound.
To do this, le level blueprint takes the position of the player and after the random interval of time passes, it spawns a sound in a position behind the player.
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/monsterAI8.png)
To make the monster say different lines and not repeat the same ones all the time I also created a sound cue for it that chooses a random one every time

![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/monsterAI9.png)

# 3.1.2- Vision Mode & invisible objects
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/VisionMode.png)
Another key element of the game is the magic vision mode and the hidden objects and secrets of the maze. As I was interested in creating visual effects and I designed many of the functionalities that the vision mode included, I was also in charge of program it.
The vision mode is divided in two parts: The colour and visual effects and the visibility of the objects depending on the mode.
To make the green visual effect I created a post processing setting that changed the colour grading by a custom one made by Pablo Bueno Melchor. 
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/rgb.png)
To make this vision mode a bit more uncomfortable and avoid the players from using it all the time, I also added a widget that made the 
“glasses effect” and made the vision narrower.
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/Glasses.png)
Other than that, the logic behind the glasses is pretty simple. When the player presses the glasses button, the post processing effects change.
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/magicvision1.png)
For the invisibility of the objects, I created an invisible object class that parents any object that is hidden without the vision mode activated (including the monster) and that hides the object in game and disables the collisions for them.
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/magicvision2.png)
# 3.1.3- Health System & Damage system
The idea for the health system is quite basic: Everytime that the monster catches you, you lose one of the three hearts that you have. However, in order to make it look good and well , I had to include blueprints to several widgets, to the player’s character and to the monster.
First of all, I had to create a widget that shows the hearts that are left. Whenever the monster touches you, this widget deletes a full heart and adds an empty one to represent the life loss.
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/heart.png)
Also, to make the damage more noticeable to the player, I added a blood splash that appears when the damage event is called and plays a fade animation to make it look better.
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/damage1.png)
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/blood.png)
Finally, I have to include some code to the monster and to the player. The monster’s blueprint includes a function that checks if it has collided with the player and, if so, it applies damage to him and stays in the same place for a few seconds to let the player escape.
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/damage2.png)
As the monster applies the damage, when the player receives it it triggers an event that reduces the total health variable in 1, deletes a full heart and creates an empty one.
![](https://github.com/luarsu/TheDevilIsInTheDetailsWriteUp/blob/master/Screenshoots/damage3.png)
When the player’s life is 0, the death widget is called and the game is restarted. 
With all those parts combinated the whole damage system is completed.
# 3.1.3- Picking objects system





