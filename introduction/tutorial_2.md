---
title: "Tutorial 2: Dashboard"
summary: "Accessing Badgeware's hardware features"
icon: build
publish: true
---
# Tutorial 2: A Dashboard
For the second tutorial, we're going to dive deep into Badgeware's vector drawing system as well as some of the different things we can access in hardware. We'll make a page that shows charts for our flash usage and our battery power, as well as showing the current time and allowing us to test the rear case lighting.

## Setting the stage
First off, you'll need to create an app folder with an `__init__.py`, an `assets` folder and an `icon.png` just as you did in the first tutorial. Now let's go into `__init__.py` and create our main `update()` function:
```python
def update():
    pass
```
And just like the first time - yay, a blank screen!

Just like in the first tutorial, you don't have to use the same colours that are mentioned here - use whatever works on your badge and to your tastes. One thing that we'll do here though is make all of our dimensions relative to the screen dimensions, rather than fixed numbers of pixels - that way, it should display the same whether you're on Tufty or Badger. Blinky is tricky, of course, because of its resolution but many of the same tecniques I'm doing here will still work. You might just have to leave out some text.

## Getting some dimensions
The main part of this dashboard will be two big arc shapes which we'll use as pie charts. We'll be writing a method which lets us draw one of these, but we'll need to pass it some dimensions. Here are the dimensions we want to calculate:

- The centre line of the screen vertically, which we'll call `centre_y`.
- The centre points of each pie chart horizontally, which we'll call `left_centre_x` and `right_centre_x`.
- The size of the pie charts, which we'll call `radius_outer` and `radius_inner`.

They're easy enough to work out. Let's say we're going to divide the screen into two, and have one pie in the centre of each half. That makes these positions quite easy to calculate.

The centre line of the screen is simply half of the height of the screen, so we can say `centre_y = screen.height / 2`.

The pie centres are also easy - if they're in the centre of each half, then they're at the one-quarter and three-quarters points across the screen. So we can say `left_centre_x = screen.width * 0.25` and `right_centre_x = screen.width * 0.75`.

And for the size of the pie charts, let's go with them taking up 80% of their respective screen half. 0.8 * 0.5 = 0.4, but then we have to halve that again since we need the radius of the pie chart, not the diameter. So in the end `radius_outer = screen.width * 0.2`.

Finally, we can work out the radius of the inner part of the ring from the outer part. Let's say that it's 60% of the outer radius, so `radius_inner = radius_outer * 0.6`.

Altogether that's:
```python
# Getting all our dimensions together
centre_y = screen.height / 2
left_centre_x = screen.width * 0.25
right_centre_x = screen.width * 0.75
radius_outer = screen.width * 0.2
radius_inner = radius_outer * 0.6

def update():
    pass
```
We're still not seeing anything on the screen, but that's okay - getting these dimensions together before we start is going to make things a lot easier down the road.

From here on, we're going to also make a unit of our own, which we'll call `du` for dimension unit. That way we can just use that for future dimensions and positions. It's going to be 1% of the screen width, and we'll add that in after our existing dimensions with
```python-raw
du = screen.width / 100
'''

## Drawing a circle
The first thing we're going to draw is a circle for the background of each pie chart. For this and all the following drawing we're going to be using Badgeware's vector shapes. These display beautifully with antialiasing, and can be easily rotated, scaled and translated using matrix transformations to get wonderfully smooth animation.

First, let's define our vector shapes using `shape.circle()`.
```python
screen.antialias = image.X4

# Getting all our dimensions together
centre_y = screen.height / 2
left_centre_x = screen.width * 0.25
right_centre_x = screen.width * 0.75
radius_outer = screen.width * 0.2
radius_inner = radius_outer * 0.6
du = screen.width / 100

def update():
    left_pie_background = shape.circle(left_centre_x, centre_y, radius_outer)
    right_pie_background = shape.circle(right_centre_x, centre_y, radius_outer)
```
You can see these two shapes have the same vertical coordinate and radius, but different horizontal coordinates. But we're still not seeing anything on the screen! That's because we've defined these shapes, but we haven't drawn them yet. Let's finally add in some drawing commands to set the background, set the pen for each shape and finally draw them.

```python
screen.antialias = image.X4

# Getting all our dimensions together
centre_y = screen.height / 2
left_centre_x = screen.width * 0.25
right_centre_x = screen.width * 0.75
radius_outer = screen.width * 0.2
radius_inner = radius_outer * 0.6
du = screen.width / 100

# Defining some colours too
light_grey = color.rgb(240, 240, 240)
med_grey = color.rgb(192, 192, 192)
dark_red = color.rgb(125, 0, 0)
light_red = color.rgb(255, 42, 42)
dark_blue = color.rgb(12, 60, 212)
light_blue = color.rgb(87, 147, 240)

def update():
    screen.pen = light_grey
    screen.clear()

    left_pie_background = shape.circle(left_centre_x, centre_y, radius_outer)
    right_pie_background = shape.circle(right_centre_x, centre_y, radius_outer)

    screen.pen = med_grey
    screen.shape(left_pie_background)
    screen.shape(right_pie_background)
```
Finally we're seeing something. You'll notice we added a few extra bits in there - we defined some colours to use now and later on, and we set `screen.antialias` to `X4` to get the highest image quality. You can experiment with `NONE`, `X2` and `X4` to see how this reduces the jaggedness in your vector shapes.

## Getting the data
If we're wanting to display fascinating stats about our badge, then we need to ask it what those stats are. The information we need can be found in the `badge` class and the `rtc` class, and we can ask them for an update every, well, every update. Once we've got it, we can start looking at how to display it.

> Note: We're going to assume that your badge already has the correct time set on the badge's RTC - if it doesn't it'll still display, just not the right time. You can quickly update the time by connecting to the internet and running the Clock app.

```python-raw
battery_percent = badge.battery_level()
battery_voltage = badge.battery_voltage()
total_flash, used_flash, _ = badge.disk_free()
year, month, day, hour, minute, second, _ = rtc.datetime()
```
You'll notice that for some of these we've got more than one variable before the `=` sign, and some of those are just an underscore `_`. If you've not run across this before, don't worry. Some of these functions return more than one value, or return a tuple containing several values. We don't necessarily need all of the values they return, but we still have to receive them or it'll throw an error. So, for the ones we don't need we can just assign them to `_`, which discards them.

Now we've got those values though, we need to represent them on screen.

## Drawing an arc
Badgeware has a vector shape which lets you draw an arc segment with a single command. It's called very simply:
```python-raw
my_arc = shape.arc(x, y, inner, outer, from, to)
```
Let's look at the arguments that takes.
- `x, y`: Position of the centre point
- `inner`: Inner radius
- `outer`: Outer radius
- `from`: Start angle (degrees)
- `to`: End angle (degrees)

Well, we've already worked out `x`, `y`, `inner` and `outer`, but that `from` and `to` still need calculating, and we'll need to do it for both charts. We could just do the calculations twice and in this case it's probably easiest to do so, but for the sake of the tutorial let's create a method we can call to convert a percentage into two pie sections. We'll add the following into our code right before our `update()` method:
```python-raw
def make_chart():
    pass
```
What we want is to give it the information we have, and for it to calculate the last two numbers we need and use them to make two shapes that it'll return. First let's add in all the parameters we want to send it.

```python-raw
def make_chart(x, y, inner, outer, percentage):
    pass
```
Now, we can use the first four as is, but that last one, `percentage`, needs to be converted into start and stop points for the slice in degrees. We'll make the starting point zero as that's nice and easy, and luckily the maths to convert to degrees is nice and easy:
```python-raw
def make_chart(x, y, inner, outer, percentage):
    seg_1_start = 0
    seg_1_end = (percentage / 100) * 359.999
```

Easy peasy. You'll see it says `seg_1` there - that's because we're going to return two `arc` shapes - one for the filled section of the chart and one for the unfilled part. We can fill in the rest easily though, as it just starts where the last one finished and goes all the way round to 360 degrees (or in this case 359.999 degrees, as we want to get very close without touching 360):
```python-raw
def make_chart(x, y, inner, outer, percentage):
    seg_1_start = 0
    seg_1_end = (percentage / 100) * 359.999
    seg_2_start = seg_1_end
    seg_2_end = 359.999
```

And now we've got all the information to make the shapes and return them. The new lines take the values we worked out or passed in, and feed them into an `arc()` method to generate the shapes.
```python-raw
def make_chart(x, y, inner, outer, percentage):
    seg_1_start = 0
    seg_1_end = (percentage / 100) * 359.999
    seg_2_start = seg_1_end
    seg_2_end = 359.999

    filled_section = shape.arc(x, y, inner, outer, seg_1_start, seg_1_end)
    unfilled_section = shape.arc(x, y, inner, outer, seg_2_start, seg_2_end)

    return filled_section, unfilled_section
```

Now we've made that - and you'll see that it's one of those methods we just saw with more than one return value - then we can call it inside our `update()` loop. Battery level will be on the left, and flash usage on the right.
```python
badge.mode(HIRES)
screen.antialias = image.X4

# Getting all our dimensions together
centre_y = screen.height / 2
left_centre_x = screen.width * 0.25
right_centre_x = screen.width * 0.75
radius_outer = screen.width * 0.2
radius_inner = radius_outer * 0.6
du = screen.width / 100

# Defining some colours too
light_grey = color.rgb(240, 240, 240)
med_grey = color.rgb(192, 192, 192)
dark_red = color.rgb(125, 0, 0)
light_red = color.rgb(255, 42, 42)
dark_blue = color.rgb(12, 60, 212)
light_blue = color.rgb(87, 147, 240)

# Takes in a percentage and dimensions to make two pie chart segments
def make_chart(x, y, inner, outer, percentage):
    seg_1_start = 0
    seg_1_end = (percentage / 100) * 359.999
    seg_2_start = seg_1_end
    seg_2_end = 359.999

    filled_section = shape.arc(x, y, inner, outer, seg_1_start, seg_1_end)
    unfilled_section = shape.arc(x, y, inner, outer, seg_2_start, seg_2_end)

    return filled_section, unfilled_section

def update():
    battery_percent = badge.battery_level()
    battery_voltage = badge.battery_voltage()
    total_flash, used_flash, _ = badge.disk_free("/system")
    year, month, day, hour, minute, second, _ = rtc.datetime()

    screen.pen = light_grey
    screen.clear()

    left_pie_background = shape.circle(left_centre_x, centre_y, radius_outer)
    right_pie_background = shape.circle(right_centre_x, centre_y, radius_outer)

    screen.pen = med_grey
    screen.shape(left_pie_background)
    screen.shape(right_pie_background)

    left_pie_filled, left_pie_unfilled = make_chart(left_centre_x, centre_y, radius_inner, radius_outer, battery_percent)
    
    memory_percent = (used_flash / total_flash) * 100
    right_pie_filled, right_pie_unfilled = make_chart(right_centre_x, centre_y, radius_inner, radius_outer, memory_percent)
    
    screen.pen = dark_red
    screen.shape(left_pie_filled)
    screen.pen = light_red
    screen.shape(left_pie_unfilled)

    screen.pen = dark_blue
    screen.shape(right_pie_filled)
    screen.pen = light_blue
    screen.shape(right_pie_unfilled)
```
Not bad. You might find that the battery level is just showing a solid ring if you're at 100% battery of course.

## Tweaking the looks
That circle background isn't bad, but it's not the most inspiring thing ever. We could switch it out for something cooler, though - what about a drop shadow for the dials instead?

First, let's add another new dimension, in the same place as the others, and we'll say it's 2 distance units:
```python-raw
shadow_distance = 2 * du
```
You can alter that value until you get it just the distance you want. Now, let's get rid of the lines where we draw the circles. Delete the following lines:
```python-raw
    left_pie_background = shape.circle(left_centre_x, centre_y, radius_outer)
    right_pie_background = shape.circle(right_centre_x, centre_y, radius_outer)

    screen.pen = med_grey
    screen.shape(left_pie_background)
    screen.shape(right_pie_background)
```

Instead, we're going to do some more drawing after we've worked out the shapes of the graphs, but before we draw them. We want to use the shapes we've already made, but have them shifted down and to the right by `shadow_distance` pixels. For this we'll use a `mat3` to transform the shape.

`mat3` is a transformation matrix. It combines any of translation (movement), rotation and scaling into one set of numbers that get applied to the points of a shape. We're going to make a simple `mat3` which we can reuse for other elements, which represents moving down and to the right by `shadow_distance` pixels. Then we can apply that matrix to any shape by setting the shape's `transform` property to the matrix.

```python-raw
shadow_matrix = mat3().translate(shadow_distance, shadow_distance)
```
The `mat3()` here creates a blank matrix. Then running the `translate()` method on it gives it a translation by the amounts we've provided for x and y. We're going to put this line just after we work out our dimensions, as it won't change as the program's being run.

In our `update()` method, we want to apply this matrix to the shapes we generate with our `make_chart()` method, draw them in a shadow colour, and then remove the matrix again so we can draw the charts themselves in the right place. Let's make a method that'll easily do that. You can put this below `make_chart()`.

```python-raw
def draw_drop_shadow(shape, colour, matrix):
    old_pen = screen.pen
    shape.transform = matrix
    screen.pen = colour
    screen.shape(shape)
    shape.transform = mat3()
    screen.pen = old_pen
```
Let's go through this line by line. First, we're remembering whtever colour the pen was set to, so the method can clean up after itself. Next we're applying the matrix to the shape - both of these, as well as the colour, get passed in using the method's parameters. Then we're setting the pen to the shadow colour, drawing the shape to the screen, and then we're cleaning up after ourselves by setting the pen back to what it was and setting the shape's transformation back to a blank matrix.

This is a pretty simple method, and there are a couple of things which you could customise to improve it. For example, this would currently only work on something that didn't already have a transformation applied to it. It also always draws to `screen` - both of these things are fixable, but they're not necessary for what we're doing here so let's move on.

Now we've made our method, we just need to call it. We have to call it after making the shapes, because we need to use them, but also before drawing the charts, because then the shadow would appear over the top of them. That leaves one place we can do it:

```python
screen.antialias = image.X4

# Getting all our dimensions together
centre_y = screen.height / 2
left_centre_x = screen.width * 0.25
right_centre_x = screen.width * 0.75
radius_outer = screen.width * 0.2
radius_inner = radius_outer * 0.6
du = screen.width / 100
shadow_distance = 2 * du

shadow_matrix = mat3().translate(shadow_distance, shadow_distance)

# Defining some colours too
light_grey = color.rgb(240, 240, 240)
med_grey = color.rgb(192, 192, 192)
dark_red = color.rgb(125, 0, 0)
light_red = color.rgb(255, 42, 42)
dark_blue = color.rgb(12, 60, 212)
light_blue = color.rgb(87, 147, 240)

# Takes in a percentage and dimensions to make two pie chart segments
def make_chart(x, y, inner, outer, percentage):
    seg_1_start = 0
    seg_1_end = (percentage / 100) * 359.999
    seg_2_start = seg_1_end
    seg_2_end = 359.999

    filled_section = shape.arc(x, y, inner, outer, seg_1_start, seg_1_end)
    unfilled_section = shape.arc(x, y, inner, outer, seg_2_start, seg_2_end)

    return filled_section, unfilled_section

def draw_drop_shadow(shape, colour, matrix):
    old_pen = screen.pen
    shape.transform = matrix
    screen.pen = colour
    screen.shape(shape)
    shape.transform = mat3()
    screen.pen = old_pen

def update():
    battery_percent = badge.battery_level()
    battery_voltage = badge.battery_voltage()
    total_flash, used_flash, _ = badge.disk_free("/system")
    year, month, day, hour, minute, second, _ = rtc.datetime()

    screen.pen = light_grey
    screen.clear()

    left_pie_filled, left_pie_unfilled = make_chart(left_centre_x, centre_y, radius_inner, radius_outer, battery_percent)
    
    memory_percent = (used_flash / total_flash) * 100
    right_pie_filled, right_pie_unfilled = make_chart(right_centre_x, centre_y, radius_inner, radius_outer, memory_percent)
    
    draw_drop_shadow(left_pie_filled, med_grey, shadow_matrix)
    draw_drop_shadow(left_pie_unfilled, med_grey, shadow_matrix)
    draw_drop_shadow(right_pie_filled, med_grey, shadow_matrix)
    draw_drop_shadow(right_pie_unfilled, med_grey, shadow_matrix)

    screen.pen = dark_red
    screen.shape(left_pie_filled)
    screen.pen = light_red
    screen.shape(left_pie_unfilled)

    screen.pen = dark_blue
    screen.shape(right_pie_filled)
    screen.pen = light_blue
    screen.shape(right_pie_unfilled)
```

There we go, looking good.

## Writing on the wall
It's time to get some text onto the screen. We're going to be using vector fonts for this as they'll scale nicely. There are three vector fonts in .af format here, but you can find more at [the Alright Fonts GitHub repository](https://github.com/lowfatcode/alright-fonts). We're using one called Mona Sans.

First, we'll load the font in at the very top of the file with the following line:
```python-raw
monasans = font.load("/system/assets/fonts/MonaSans-Medium.af")
```

Then we need to calculate a size for it. We'll create three variables we can choose from, and they're once again going to be based on one of our existing dimensions - that way everything still scales along with the screen size. You can test this out at any time by putting the line `badge.mode(HIRES)` at the very beginning of your python file to put the badge into high resolution mode. You'll see things are sharper, but nothing should move about or change size on the screen. To go back to low resolution mode, just remove that line. You can use whichever mode you want during the tutorial, it should work just the same with either.

Right after our existing dimensions, let's put:
```python-raw
text_size_l = 10 * du
text_size_m = 8 * du
text_size_s = 6 * du
```
These have given good results for us, but you can play about with them and see what works for you.

Finally, let's get some text on the screen. Almost all of the text we'll want to display will be centred on a point, so we'll want to write something that'll let us centre text. We're not using the more advanced text functions in the `text` class here, but instead drawing straight onto the image as it's faster and allows us to position the text more easily.

Let's make another new method just above `update()`:
```python-raw
def centred_text(text, x, y, size):
    width, height = screen.measure_text(text, size)
    new_x = x - (width / 2)
    new_y = y + height
    screen.text(text, new_x, new_y, size)
```
Going through this line by line, we can see that `screen.measure_text()` gives us the dimensions of the specified text at the specified font size. Then we're taking the centre point we passed into the method, and we're subtracting half of the width from x, and adding the height to y, so that the baseline of the text will be at the point we specified, with the text centred horizontally. Finally we're drawing the text to the screen at the specified size, using these new x and y coordinates.

Let's try using this to write the battery percentage as a number in the middle of its chart. We'll convert it into a string and add a percent sign, then pass it into our new method, specifying the centre point of that left pie chart:

```python
badge.mode(HIRES)
screen.antialias = image.X4

monasans = font.load("/system/assets/fonts/MonaSans-Medium.af")

# Getting all our dimensions together
centre_y = screen.height / 2
left_centre_x = screen.width * 0.25
right_centre_x = screen.width * 0.75
radius_outer = screen.width * 0.2
radius_inner = radius_outer * 0.6
du = screen.width / 100
shadow_distance = 2 * du
text_size_l = 10 * du
text_size_m = 8 * du
text_size_s = 6 * du

shadow_matrix = mat3().translate(shadow_distance, shadow_distance)

# Defining some colours too
light_grey = color.rgb(240, 240, 240)
med_grey = color.rgb(192, 192, 192)
dark_red = color.rgb(125, 0, 0)
light_red = color.rgb(255, 42, 42)
dark_blue = color.rgb(12, 60, 212)
light_blue = color.rgb(87, 147, 240)

# Takes in a percentage and dimensions to make two pie chart segments
def make_chart(x, y, inner, outer, percentage):
    seg_1_start = 0
    seg_1_end = (percentage / 100) * 359.999
    seg_2_start = seg_1_end
    seg_2_end = 359.999

    filled_section = shape.arc(x, y, inner, outer, seg_1_start, seg_1_end)
    unfilled_section = shape.arc(x, y, inner, outer, seg_2_start, seg_2_end)

    return filled_section, unfilled_section

def draw_drop_shadow(shape, colour, matrix):
    old_pen = screen.pen
    shape.transform = matrix
    screen.pen = colour
    screen.shape(shape)
    shape.transform = mat3()
    screen.pen = old_pen

def centred_text(text, x, y, size):
    width, height = screen.measure_text(text, size)
    new_x = x - (width / 2)
    new_y = y - height
    screen.text(text, new_x, new_y, size)

def update():
    battery_percent = badge.battery_level()
    battery_voltage = badge.battery_voltage()
    total_flash, used_flash, _ = badge.disk_free("/system")
    year, month, day, hour, minute, second, _ = rtc.datetime()

    screen.pen = light_grey
    screen.clear()

    left_pie_filled, left_pie_unfilled = make_chart(left_centre_x, centre_y, radius_inner, radius_outer, battery_percent)
    
    memory_percent = (used_flash / total_flash) * 100
    right_pie_filled, right_pie_unfilled = make_chart(right_centre_x, centre_y, radius_inner, radius_outer, memory_percent)
    
    draw_drop_shadow(left_pie_filled, med_grey, shadow_matrix)
    draw_drop_shadow(left_pie_unfilled, med_grey, shadow_matrix)
    draw_drop_shadow(right_pie_filled, med_grey, shadow_matrix)
    draw_drop_shadow(right_pie_unfilled, med_grey, shadow_matrix)

    screen.pen = dark_red
    screen.shape(left_pie_filled)
    screen.pen = light_red
    screen.shape(left_pie_unfilled)

    screen.pen = dark_blue
    screen.shape(right_pie_filled)
    screen.pen = light_blue
    screen.shape(right_pie_unfilled)

    screen.pen = color.black
    screen.font = monasans

    battery_text = str(battery_percent) + "%"
    centred_text(battery_text, left_centre_x, centre_y, text_size_l)
```