# add_updater

## Manim lesson on updaters

Updaters will allow you to run a piece of code on each frame. Updater are executed after the animation.

```py
from manim import *
class Example(Scene):
    def construct(self):
        square = Square()
        square.shift(UP* (config["frame_y_radius"] - 2))
        self.add(square)

        child = Square().scale(0.5).next_to(square, RIGHT)
        self.add(child)

        ## define updater first then give it an action
        child.add_updater(lambda mob: mob.next_to(square,RIGHT))

        self.play(square.animate.shift(DOWN * (config["frame_y_radius"] -2 )))
```

![](./videos/example2.gif)

```
# multiple updater
t = DecimalNumber()
t.add_updater(lambda mob: mob.next_to(square,RIGHT))
t.add_updater(lambda mob: mob.set_value(square.get_center()[1]))
```

## Using sobmobject concept

Child object have same effect what parent object imposes

```py
square.add(child)
self.play(square.animate.shift(DOWN * (config["frame_y_radius"] -2 )))
```

![](./videos/example2.gif)

## MoveAlongCurve

```py
class MoveAlongCurve(Scene):
    def construct(self):
        a = Axes()
        self.add(a)

        func = lambda x: (x+2)*(x+1)*(x-2)*(x-1)
        g = a.get_graph(func)
        self.add(g)

        def move_along_curve(mob,dt):
            new_x = mob.get_center()[0] + dt
            mob.move_to(a.coords_to_point(new_x,func(new_x)))

        d = Dot().move_to(a.coords_to_point(-2,func(-2)))
        d.add_updater(move_along_curve)
        self.add(d)

        self.wait(4)
```

![](./videos/example3.gif)

```py
## Another implement
v = ValueTracker(-2)
def move_along_curve(mob,dt):
    mob.move_to(a.coords_to_point(v.get_value(),func(v.get_value())))

d = Dot().move_to(a.coords_to_point(-2,func(-2)))
d.add_updater(move_along_curve)
self.add(d)
self.play(v.animate.set_value(3),run_time=3)
```

## LissajousCurve

To make the curve smooth increase the `run_time`

```py
class LissajousCurve(Scene):
    def construct(self):

        def lissajous_position(t):
            A = 1
            B = 1
            a = 1
            b = 1
            delta = 1
            x = A * np.sin(a*t + delta)
            y = B * np.sin(b*t)
            return np.array([x,y,0])

        v = ValueTracker()
        line = VMobject()

        def update_dot(mob,dt):
            ## dt is cumulative hence use ValueTracker
            new_position = lissajous_position(v.get_value())
            d.move_to(new_position)
            ## each frame add
            line.add_line_to(new_position)

        d = Dot()
        d.move_to(lissajous_position(0))
        d.add_updater(update_dot)
        self.add(d)

        line.points = [d.get_center()]
        self.add(line)

        self.play(v.animate.set_value(10),rate_func=linear)
        self.wait()
```

![](./videos/example4.gif)
