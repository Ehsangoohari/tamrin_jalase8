import turtle
import random
import time

# تنظیمات صفحه
WIDTH, HEIGHT = 900, 500
screen = turtle.Screen()
screen.setup(WIDTH, HEIGHT)
screen.bgcolor("white")  
screen.tracer(0)

# تنظیمات اشکال
SHAPE_SIZE = 50
SPEED = 5

# کلاس چندضلعی
class Polygon:
    def __init__(self, x, y, color, shape_type):
        self.x = x
        self.y = y
        self.color = color
        self.shape_type = shape_type 
        self.vx = random.choice([-SPEED, SPEED])
        self.vy = random.choice([-SPEED, SPEED])
        self.turtle = turtle.Turtle()
        self.turtle.penup()
        self.turtle.speed(0)
        self.turtle.goto(self.x, self.y)
        self.turtle.color(self.color)
        self.draw()

    def draw(self):
        if self.shape_type == "triangle":
            self.turtle.shape("triangle")
        elif self.shape_type == "square":
            self.turtle.shape("square")
        elif self.shape_type == "rectangle":
            self.turtle.shape("square")
            self.turtle.shapesize(1, 2)

    def move(self):
        self.x += self.vx
        self.y += self.vy
        if self.x - SHAPE_SIZE < -WIDTH // 2 or self.x + SHAPE_SIZE > WIDTH // 2:
            self.vx *= -1
        if self.y - SHAPE_SIZE < -HEIGHT // 2 or self.y + SHAPE_SIZE > HEIGHT // 2:
            self.vy *= -1
        self.turtle.goto(self.x, self.y)

    def clear(self):
        self.turtle.clear()
        self.turtle.hideturtle()

    def collide(self, other):
        return abs(self.x - other.x) < SHAPE_SIZE and abs(self.y - other.y) < SHAPE_SIZE

# تولید یک شکل تصادفی
def random_shape():
    shape_type = random.choice(["triangle", "square", "rectangle"])
    color = random.choice(["red", "green", "blue"])
    x = random.randint(-WIDTH // 2 + SHAPE_SIZE, WIDTH // 2 - SHAPE_SIZE)
    y = random.randint(-HEIGHT // 2 + SHAPE_SIZE, HEIGHT // 2 - SHAPE_SIZE)
    return Polygon(x, y, color, shape_type)

# ایجاد یک شکل جدید از یکی از اشکال برخورد کرده
def create_new_shape(shape):
    return Polygon(shape.x, shape.y, shape.color, shape.shape_type)

# لیست اشکال
shapes = [random_shape() for _ in range(10)]

# حلقه اصلی
while True:
    for shape in shapes:
        shape.move()

    # بررسی برخوردها
    new_shapes = []
    for i in range(len(shapes)):
        for j in range(i + 1, len(shapes)):
            if shapes[i] is not None and shapes[j] is not None and shapes[i].collide(shapes[j]):
                shapes[i].clear()
                shapes[j].clear()

                # ایجاد یک شکل جدید از یکی از دو شکل برخورد کرده
                new_shape = create_new_shape(shapes[i])  
                new_shapes.append(new_shape)

                # حذف اشکال برخورد کرده از لیست
                shapes[i] = None
                shapes[j] = None
                break

    # حذف اشکال حذف‌شده و اضافه‌کردن شکل‌های جدید
    shapes = [shape for shape in shapes if shape is not None] + new_shapes

    screen.update()
    time.sleep(0.03)
