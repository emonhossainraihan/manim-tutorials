- `--disable_caching`
- `--use_opengl_renderer`

## Run on GoogleColab

```
!sudo apt update
!sudo apt install libcairo2-dev ffmpeg texlive texlive-latex-extra texlive-fonts-extra texlive-latex-recommended texlive-science tipa libpango1.0-dev
!pip install git+https://github.com/ManimCommunity/manim.git
!pip install IPython --upgrade
```
A URL like git://github.com will mean just the plain Git protocol. To clarify that it should use Git over HTTPS instead, the protocol is written git+https. Now, git itself does not need this, you can do just git clone https://....

For other tools like pip, which can handle many protocols, https://... will indicate that it should do just a GET request for that URL, and git:// would indicate that it should attempt to clone the repo with the plain Git protocol. git+https is used to indicate that it should clone the repo over HTTPS (just as git+ssh is used to indicate it should do so using SSH).

## TransformMatchingTex

```py
def construct(self):
  a = MathTex("2x", "+", "8", "=", "0")
  b = MathTex("2x", "=", "-", "8")
  self.play(TransformMatchingTex(a, b, path_arc=PI))
```

## shearTransformation

[discord_code](https://discord.com/channels/581738731934056449/669259565941915723/841715359366774804)

```py
class shearTranssform(LinearTransformationScene):
    def __init__(self, **kwargs):
        LinearTransformationScene.__init__(
            self,
            show_coordinates=True,
            **kwargs
        )
        self.foreground_plane_kwargs = {
            "x_range": [-config["frame_width"], config["frame_width"]],
            "y_range": [-config["frame_width"], config["frame_width"]],
            "faded_line_ratio": 1,
        }

    def construct(self):
        matrix_place = [["a", "b"], ["c", "d"]]
        mat = [[1, 1], [0, 1]]
        xy = [["x"], ["y"]]
        sq = self.add_unit_square()

        m_p = Matrix(matrix_place).to_corner(UL)
        self.add_foreground_mobject(m_p)
        m_p[0][0].set_color(GREEN)
        m_p[0][1].set_color(RED)
        m_p[0][2].set_color(GREEN)
        m_p[0][3].set_color(RED)

        self.play(Write(m_p))

        self.apply_matrix(mat)
        self.wait()
```

# Grid/Table

```py
class GridGrid(Scene):
    def construct(self):
        grid = VGroup()
        for pos in range(64):
            square = Square(side_length=0.8)
            int = Integer(pos+1)
            square.add(int)
            grid.add(square)
        grid.arrange_in_grid(buff=0)
        self.add(grid)
        self.wait()
```

![Screenshot from 2021-05-17 15-17-57](https://user-images.githubusercontent.com/34622219/118464937-35d7b700-b723-11eb-871f-49c72198eb1f.png)

```py
a = list(range(-4,4,1))
a.remove(0)

axes_config = dict(
    x_axis_config = dict(
        x_min = -5,
        x_max = 5,
        include_numbers = True,
        numbers_to_show = list(range(-5,6,1))
    ),
    y_axis_config = dict(
        include_numbers = True,
        numbers_to_show = a
    ),
)

class Numberplane(Scene):
    def construct(self):
        Numberplane = NumberPlane(**axes_config)
        lines = Numberplane.get_lines()

        self.play(Write(Numberplane))
        self.wait()

class Vectorscene(VectorScene):
    def construct(self):
        self.add_axes(animate=True)
        self.add_plane(animate=True)
        self.wait()

    def add_axes(self, animate=False, color=WHITE, **kwargs):
        axes = Axes(color=color, tick_frequency=1,**axes_config)
        if animate:
            self.play(ShowCreation(axes))
        self.add(axes)
        return axes
```

## Random Motion

```py
import numpy as np

class Introduction(Scene):
    def construct(self):
        particle = Circle(radius=0.25, color=BLUE, fill_color=BLUE, fill_opacity=1)
        square = Square(side_length=5)
        stop = square.get_top()
        sbot = square.get_bottom()
        sleft = square.get_left()
        sright = square.get_right()
        def updater(mob,dt = 1):
            cord1 = np.random.uniform(low=-2.5, high = 2.5)
            cord2 = np.random.uniform(low= -2.5, high = 2.5)
            next_position = np.array((cord1,cord2, 0))
            if next_position[1] >= stop[1]:
                next_position[1] = next_position[1] - stop[1]
            if next_position[1] <= sbot[1]:
                next_position[1] = next_position[1] + sbot[1]
            if next_position[0] >= sright[0]:
                next_position[0] = next_position[0] - sright[0]
            if next_position[0] <= sleft[0]:
                next_position[0] = next_position[0] + sleft[0]
            mob.move_to(next_position)

        self.play(ShowCreation(particle),ShowCreation(square))
        particle.add_updater(updater)
        self.wait(2)
```

![random](https://user-images.githubusercontent.com/34622219/118465479-bd252a80-b723-11eb-99f8-d1392b922dde.jpg)

## Complex Transformation

```py

class Riemann(Scene):
    def construct(self):
        plane = ComplexPlane(x_line_frequency=0.2, y_line_frequency=0.2)
        moving_plane = plane.copy()
        critical_line = moving_plane[1][45]
        moving_plane[1].remove(critical_line)
        moving_plane.prepare_for_nonlinear_transform(num_inserted_curves=100)

        self.play(
            Write(plane, run_time=3),
            FadeIn(moving_plane),
        )
        self.wait()
#         self.play(
#             moving_plane.animate.apply_complex_function(lambda z: zeta(z)),
#             run_time=6,
#         )
        self.wait(2)
```
