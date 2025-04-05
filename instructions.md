# Prerequisites
* Open Terminal - instructions can be found [here](https://support.apple.com/en-gb/guide/terminal/apd5265185d-f365-44cb-8b09-71a064a42125/mac)
* Install HomeBrew by running this command in your terminal `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
* Close and reopen terminal
* Install Python3 by running the command `brew install python` from your terminal. Terminal is available from your installed apps.
* Install a text editor such as [Atom](https://atom.io/) or [Sublime text](https://www.sublimetext.com/)
* Install virtualenv by running `python3 -m pip install --user virtualenv` in the terminal

# Setup Your Environment

Download [the file in the folder](https://drive.google.com/drive/folders/1vvs3I6qTOUIK0YwQcAbIvuWufeuYopyQ?usp=share_link) and unzip it.

Once unzipped open the directory created in atom or sublime by going to file->open directory and finding the folder in the top left-hand corner of the menu bar.

Also open a terminal by right-clicking on the folder where these instructions are and select `open terminal at folder`.  
![Open Terminal](https://cdn.shortpixel.ai/spai/w_700+q_lossy+ret_img+to_webp/https://ladedu.com/wp-content/uploads/2020/07/Open-a-Mac-Terminal-window-from-Finder-at-the-selected-folder.png)

In the terminal you will also need to set up a virtual python environment.  
`python3 -m venv venv && source venv/bin/activate`

Install requirements for game by running  
`pip install -r requirements.txt && source venv/bin/activate`

# Build a Game!

We are using a framework called pygame zero. This will allow us to create a game easily with the various functions already implemented.

## Part 1 - Drawing the game on the screen


A part of pygame zero has a draw function (also known as a hook) defined [here](https://pygame-zero.readthedocs.io/en/stable/hooks.html#draw).  
This is in charge of drawing images also known as sprites on the screen.

The sprites have already been provided in the images directory.

1. Create and save a file called `flappybird.py` in the same directory that got created after unzipping - The `.py` extension denotes that the file is a python based script.
2. In the file add the following lines
```python  
TITLE = 'Flappy Bird' # Name in the Window  
# Size of the Window in pixels  
WIDTH = 400  
HEIGHT = 708  
```  
3. Add the following code at the bottom of your file.
```python  
def draw():  
    screen.blit('background', (0, 0))
 ```  

Now run the app by executing the following command in your terminal

```bash  
pgzrun flappybird.py
```  

You should now see a window with an image inside it.

Next lets add the bird onto the screen!

4. Add `bird = Actor('bird1', (75, 350))` to the bottom of the file, there should be no indentation at all.
   1. This line sets up an Actor, which is essentially an object that has the ability to interact with user actions and also other Actors.

5. In the draw function add the following line. `bird.draw()`. Your function should look like this now:
```python  
def draw():  
    screen.blit('background', (0, 0))
    bird.draw()
```   
Run the application again.

This screen should look like this

![Screen](https://pygamezero-bird.readthedocs.io/en/latest/_images/background_and_bird.png)

6. Let's draw two pipes on the screen now. Add them to the bottom of your file.
```python  
top_pipe = Actor('top', (300,0))  
bottom_pipe = Actor('bottom', (300,500))  
```  
Remember to add them to the draw function as well
```python  
top_pipe.draw()  
bottom_pipe.draw()  
```  

Now run the application.

**UH oh!** there's no gap for the bird to fly through.  
We fix this by dynamically setting a variable to create a gap. Replace the lines where you set the pipe actors with the following:

```python  
gap = 140  
top_pipe = Actor('top', (300, 0))  
bottom_pipe = Actor('bottom', (300, top_pipe.height + gap))  
```  

Run it again, you should see a gap where the bird can fly through!


## Part 2 - Movement

Let's add an interaction with the mouse.  
All Actor objects have a variety of properties that you can assign to them. Some default properties are x and y co-ordinates.

### Flying

1. Get the bird to move up on the click of the left button

Let's add the following code

```python  
def on_mouse_down():  
    # By default this is the left mouse button
    bird.y -= 50
 ```  

Now lets run the app and test this.

You will see that on each click the bird will move up 50 pixels.

But it looks a bit weird and the bird now doesn't come back down.  
If we want to make the bird move smoothly we need to move him in small amounts, and do it so often that it looks smooth.

2. Add a property called speed to bird. This should be on the line after defining the bird object.
```python  
# Bird
bird = Actor('bird1', (75, 350))  
bird.speed = 1 # add this new line
```  

3. An update function is a function that gets called again and again very quickly. Normally 60 times in every second. Here we'll add the speed to the y co-ordinate to bring the bird back down at a steady rate.
```python  
def update():  
    bird.y += bird.speed
```  

### Moving forward

Now we're going to concentrate on movement, but we're not going to be updating the `bird` object.

1. Define a variable called `scroll_speed` at the bottom of the file and give it the value of `1`.
```python  
scroll_speed = 1  
```  

2. Now we're going to move the two pipes by that scroll speed but on the x-axis. You'll want to add the following lines to your `update()` function.
```python 
top_pipe.x -= scroll_speed  
bottom_pipe.x -= scroll_speed  
```  

Now let's run the Application. Notice that we're negating the scroll speed.

3. Let's get the pipes to repeat now so once they're off the screen, they get recreated at different positions. We will want to recreate when the right side of the pipe is less than `0`.

To do this we add the following to the `update()` function.

```python  
if top_pipe.right < 0:  
   offset = random.randint(150, HEIGHT - 150)
   top_pipe.midbottom = (WIDTH, offset)
   bottom_pipe.midbottom = (WIDTH, offset + top_pipe.height + gap)
```  

You'll notice random has been added. To use the random library, you'll need to add `import random` as the first line of the file.

Now let's run the application. We have looping pipes which gives us the illusion of moving forward in the game.

On click our bird moves 50px up, but still it's not 100% smooth on a click, it feels like the bird is teleported up. Let's add gravity to the game.

4. On a mouse click we are going to change the `speed` property to a fixed number. Update `on_mouse_down` to reflect what is shown below

```python  
def on_mouse_down():  
    bird.speed = -6.5
```  

5. Create a variable at the end of your file called `gravity` and assign it the value of `0.3`
```python  
gravity = 0.3  
```  

6. Finally, let's add gravity to the `speed` property in the `update()` function, by adding this as the first line of the function.
```python  
bird.speed += gravity  
```  

### Animation

Let's make the bird flap now.

In the `update()` function lets add the following
```python  
if bird.speed > 0:  
   bird.image = "bird1"
else:  
   bird.image = "bird0"
 ```  

This changes the image of the bird dependent on the speed. So when we click, the image of the bird is the bird1.png image otherwise it's bird0.png image

Test it out! you should be at a point where you are able to see the bird flapping, and also have the illusion of moving forward.

## Part 3 - Collisions

Now we need to handle collisions which should be the following.

Going off the screen or hitting a pipe!

First lets start with a function to handle resetting the game.

1. Add a function just after `on_mouse_down()` called `reset()`. This should reset the speed and position of the bird. Also reset the position of the pipes.
```python  
def reset():  
   bird.speed = 1
   bird.center = (75, 350)
   bird.alive = True
   top_pipe.center = (300, 0) 
   bottom_pipe.center = (300, top_pipe.height + gap)  
```  

2. Add a condition to the end of the `update()` function to check that the bird of the height is not greater than the height or less than 0. It should look something like this.
```python  
if bird.y > HEIGHT or bird.y < 0:  
   reset()
 ```  

3. Add the following to the section where we created our bird object (at the bottom of the file). This sets the default value for the `alive` object field. 
```python
# Bird
# bird = Actor('bird1', (75, 350))
# bird.speed = 1
bird.alive = True  # add this new line
```

Let's test this again. You're game should reset all that's been defined when you are beyond the windows limits.

Now let's handle colliding with the pipes.

4. Let's add a function to run when hitting a pipe.
```python  
def hit_pipe():  
   print("Hit pipe!")
   bird.image = "birddead"
   bird.alive = False
```  

5. In the `update()` function we are going to detect collisions. Luckily pygame zero has in build collision functions. We can add the following lines to handle this.

```python  
if bird.colliderect(top_pipe) or bird.colliderect(bottom_pipe):  
   hit_pipe()
```  

Let's test this and see.

When we hit the pipe the image changes but just for a moment, and we can still fly.  
We need to make two changes.

6. First is in the `on_mouse_down()` function. Where we're only going to allow the speed to change, if the bird is alive.

The function should look like this now.

```python  
def on_mouse_down():  
   if bird.alive:
      bird.speed = -6.5
```  

7. Second is updating the image, the image should only change if the bird is alive as well.

In the `update()` function, make this change in the section that updates the bird pictures
```python
# Add this first line and indent the rest
if bird.alive:
     if bird.speed > 0:
         bird.image = "bird1"
     else:
         bird.image = "bird0"
```

## Part 4 - Score

Nearly there, last bit is to add a score to the screen.

1. Let's assign a score to the bird in the reset function. Add these line to the `reset()` function

```python  
bird.score = 0 # This will hold the points  
top_pipe.pair_number = 1 # This is number value pair of pipes that have been passed.  
```  

2. Let's add to the draw function the score to come on the screen. You will need to add the following to the `draw()` function
```python  
screen.draw.text(  
  str(bird.score),
  color='white',
  midtop =(20, 10),
  fontsize=70)
 ```  

3. Add the following to increment the score in the `update()` function. First we need to adjust the `pair_number` property and add this to the conditional block where we loop the pipes.
```python  
#if top_pipe.right < 0:  
#        offset = random.randint(150, HEIGHT - 150)
#        top_pipe.midbottom = (WIDTH, offset)
#        bottom_pipe.midbottom = (WIDTH, offset + top_pipe.height + gap)
         top_pipe.pair_number += 1   # add this new line
```  

4. Add this condition in the `update()` function after the collision detection section.
```python 
# if bird.colliderect(top_pipe) or bird.colliderect(bottom_pipe):
#   hit_pipe()

if top_pipe.right < bird.x:   # add these 2 new lines
   bird.score = top_pipe.pair_number
 ```

5. Add the default value for the new object `score` field
```python
# Bird
# bird = Actor('bird1', (75, 350))
# bird.speed = 1
# bird.alive = True
bird.score = 0 # add this new line
```

6. Add the default value for the pipe's `pair_number` field
```python
# Pipes
# gap = 140
# top_pipe = Actor('top', (300, 0))
# bottom_pipe = Actor('bottom', (300, top_pipe.height + gap))
top_pipe.pair_number = 1  # Add this new line
```

Now you can test this and your game is complete!
