import math

with open("e10.txt") as f:
    data = [line.rstrip() for line in f]

asteroids = set()


# def find_blocked(pos_x, pos_y):
#     blocked = 0
#     asters = 0
#     # X

#     cur_x, cur_y = pos_x - 1, pos_y
#     while cur_x >= 0:
#         if data[cur_y][cur_x] == "#":
#             asters += 1
#         cur_x -= 1
#     if asters > 1:
#         blocked += asters - 1
#     asters = 0

#     cur_x, cur_y = pos_x + 1, pos_y
#     while cur_x < WIDTH:
#         if data[cur_y][cur_x] == "#":
#             asters += 1
#         cur_x += 1

#     if asters > 1:
#         blocked += asters - 1
#     asters = 0

#     # Y
#     cur_x, cur_y = pos_x, pos_y - 1
#     while cur_x >= 0 and cur_y >= 0:
#         if data[cur_y][cur_x] == "#":
#             asters += 1
#         cur_y -= 1
#     if asters > 1:
#         blocked += asters - 1
#     asters = 0

#     cur_x, cur_y = pos_x, pos_y + 1
#     while cur_x < WIDTH and cur_y < HEIGHT:
#         if data[cur_y][cur_x] == "#":
#             asters += 1
#         cur_y += 1

#     if asters > 1:
#         blocked += asters - 1
#     asters = 0

#     # XY
#     cur_x, cur_y = pos_x + 1, pos_y + 1
#     while cur_x < WIDTH and cur_y < HEIGHT:
#         if data[cur_y][cur_x] == "#":
#             asters += 1
#         cur_x += 1
#         cur_y += 1

#     if asters > 1:
#         blocked += asters - 1
#     asters = 0

#     cur_x, cur_y = pos_x - 1, pos_y - 1
#     while cur_x >= 0 and cur_y >= 0:
#         if data[cur_y][cur_x] == "#":
#             asters += 1
#         cur_x -= 1
#         cur_y -= 1

#     asters = 0
#     return blocked


def dist(a, b):
    return math.sqrt(((a[0] - b[0]) ** 2 + (a[1] - b[1]) ** 2))


def is_on_line(fr, to, bet):
    return dist(fr, bet) + dist(bet, to) == dist(fr, to)


data = """.#..#
.....
#####
....#
...##""".splitlines()

WIDTH = len(data[0])
HEIGHT = len(data)
print(data)

ast_count = sum([1 for line in data for x in line if x == "#"])
print(ast_count)

asteroids = [(x, y) for x in range(WIDTH) for y in range(HEIGHT) if data[y][x] == "#"]


def none_between(a, b, cloud):
    for c in cloud:
        a = a[0] + 0.5, a[1] + 0.5
        b = b[0] + 0.5, b[1] + 0.5
        c = c[0] + 0.5, c[1] + 0.5
        if c == a or c == b:
            continue
        if is_on_line(a, b, c):
            return False
    return True


def find_cols(ast1, asts):

    # print(asts)
    det = 0
    for ast in asts:
        # print("Checking ast", ast)
        if none_between(ast1, ast, asts):
            det += 1

    return det


for main_ast in asteroids:
    nw = [ast for ast in asteroids if ast[0] < main_ast[0] and ast[1] < main_ast[1]]
    ne = [ast for ast in asteroids if ast[0] >= main_ast[0] and ast[1] < main_ast[1]]
    se = [ast for ast in asteroids if ast[0] >= main_ast[0] and ast[1] >= main_ast[1]]
    sw = [ast for ast in asteroids if ast[0] < main_ast[0] and ast[1] <= main_ast[1]]
    cols = (
        find_cols(main_ast, nw)
        + find_cols(main_ast, ne)
        + find_cols(main_ast, se)
        + find_cols(main_ast, sw)
    )
    print(cols)

