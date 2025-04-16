# Tables Etude Key Takeaways

### Exercise 1. Display a table on screen and highlight a cell.

We start by reading a csv file and converting it to a pandas dataframe. This csv file contains the OHLC prices of the BTC/USD pair from 2016 to 2025. 
```python
#Read data and convert entries to str
df = pd.read_csv('my_data.csv')
df.drop(labels='Unnamed: 0',axis=1,inplace=True)
df = df.iloc[:3,:3].map(str)
```
Key takeaways:
* Drop the index column `Unnamed: 0` using `drop()`. Notice the use of `axis=1` to select columns.
* The manim engine requires that the dataframe entries be of type `str` (since it tries to access its first element by indexing, that is, using `df[0]`, but this refers to the first column label if I'm not mistaken.)
* The built-in `map()` method applies the `str()` function to every entry on the dataframe.

Now create the actual table inside the `construct()` method.

```python
class LocFunction(Scene):
    def construct(self):

        #Create table 
        table = Table(
            table=df.to_numpy(),
            include_outer_lines=True,
            col_labels=list(map(Text,df.columns)),
            #add_background_rectangles_to_entries=True
        )
        #Write table on screen
        self.play(Write(table.scale(0.7)))
```
Key takeaways:
* Use `to_numpy()` to convert the dataframe to a numpy array. The elements of this array will be all of type `str`.
* The `col_labels` params only let me use `Text` objects so I had to apply the `Text()` function to every element from `df.columns`.
* Apparently `map()` returns an iterator-like object, so I used `list()` to get the desired list.
* The `add_background_rectangles_to_entries` param is not necessary for highlighting a cell. Default is False. I commented it out.
* Change the size of an object using the `scale()` method.

Choose a highlight color and extract the cell to be highlighted.
```python
#Set highlighting color and extract cell to highlight
highlight_color = ManimColor((0,0,255),alpha=1)
my_cell = table.get_highlighted_cell((2,3)).set_style(
    fill_opacity=0,
    fill_color = highlight_color
)
```
Key takeaways:
* Colors can be specified using the `ManimColor` class, which accepts an RGB tuple and an alpha value.
* The `get_highlighted_cell()` method returns a `BackgroundRectangle` object corresponding to the cell to highlight. It works even if you haven't highlighted any cell before, because:
* The `get_highlighted_cell()` method by default sets the fill color of the `BackgroundRectangle` it returns to **yellow**. That's why using the `set_style()` method is necessary. Not using it caused some strange behaviour where the cell changed from transparent (or black) to yellow immediately, and then smoothly to blue.
* The expression for `my_cell` is apparently equivalent to `table.get_highlighted_cell((2,3)).set_fill(highlight_color).set_opacity(0)`.

In order to use the `MoveToTarget()` method, it will be necessary to create a `target` with the `generate_target()` method, which represents the final state of the highlighted cell. It will just be a `BackgroundRectangle` with the `highlight_color`. 

```python
#Set up target for MoveToTarget()
my_cell.generate_target()
my_cell.target.set_opacity(1)
my_cell.target.set_fill(highlight_color)
```
Key takeaways:
* `generate_target()` apparently creates an attribute called `target` which is, according to some sources, just a copy of `my_cell`. 
* We set the fill color and opacity of `target` with `set_opacity()` and `set_fill()`.

Finally we adjust the relative positions of the `BackgroundRectangle` and the table and execute the `MoveToTarget()` method.

```python
#Adjust z indices for target and table
my_cell.target.set_z_index(0)
table.set_z_index(1)

#Execute MoveToTarget()
self.play(
    MoveToTarget(my_cell,run_time=0.1)
)
```
Key takeaways:
* The `set_z_index()` allows to set the relative positions of objects. Apparently it only accepts integers as params. A greater index places the object on an upper layer.
* It's remarkable that it's possible to set the z index of the table even after it's been written on screen. I think this just affects the `target` object.
* The `MoveToTarget()` method only requires the object whose target has been created previously. Control the playing time with `run_time`.

### Code for Exercise 1: Display a table on screen and highlight a cell.

```python
from manim import *
import pandas as pd

#Read data and convert entries to str
df = pd.read_csv('my_data.csv')
df.drop(labels='Unnamed: 0',axis=1,inplace=True)
df = df.iloc[:3,:3].map(str)

class LocFunction(Scene):
    def construct(self):

        #Create table 
        table = Table(
            table=df.to_numpy(),
            include_outer_lines=True,
            col_labels=list(map(Text,df.columns)),
            #add_background_rectangles_to_entries=True
        )

        #Write table on screen
        self.play(Write(table.scale(0.7)))

        #Set highlighting color and extract cell to highlight
        highlight_color = ManimColor((0,0,255),alpha=1)
        my_cell = table.get_highlighted_cell((2,3)).set_style(
            fill_opacity=0,
            fill_color = highlight_color
        )
        #my_cell = table.get_highlighted_cell((2,3)).set_fill(highlight_color).set_opacity(0)

        #Set up target for MoveToTarget()
        my_cell.generate_target()
        my_cell.target.set_opacity(1)
        my_cell.target.set_fill(highlight_color)

        #Adjust z indices for target and table
        my_cell.target.set_z_index(0)
        table.set_z_index(1)

        #Execute MoveToTarget()
        self.play(
            MoveToTarget(my_cell,run_time=0.1)
        )

        self.wait(duration=2)
```












