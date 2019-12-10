import math
import numpy as np

with open("e10.txt") as f:
    data = [line.rstrip() for line in f]

def dist(a, b):
    return math.sqrt(((a[0] - b[0]) ** 2 + (a[1] - b[1]) ** 2))

def is_on_line(fr, to, bet):
    return abs((dist(fr, bet) + dist(bet, to)) - dist(fr, to)) < 0.000001


# data = """.#..##.###...#######
# ##.############..##.
# .#.######.########.#
# .###.#######.####.#.
# #####.##.#.##.###.##
# ..#####..#.#########
# ####################
# #.####....###.#.#.##
# ##.#################
# #####.##.###..####..
# ..######..##.#######
# ####.##.####...##..#
# .#####..#.######.###
# ##...#.##########...
# #.##########.#######
# .####.#.###.###.#.##
# ....##.##.###..#####
# .#.#.###########.###
# #.#.#.#####.####.###
# ###.##.####.##.#..##""".splitlines()

asteroids = {(x, y) for y, line in enumerate(data) for x, c in enumerate(line) if c == "#"}

def find_ang(c, origo):
    # Vector pointing of origo
    a = np.array((origo[0], origo[1]-1))
    origo = np.array(origo)
    c = np.array(c)

    ba = a-origo
    bc = c-origo

    cosine_angle = np.dot(ba, bc) / (np.linalg.norm(ba) * np.linalg.norm(bc))

    if c[0] < origo[0]:
        return round(np.arccos(cosine_angle),6) + math.pi
    else:
        return round(np.arccos(cosine_angle),6)

def reachable_asteroids(asteroids, from_asteroid):
    return {find_ang(ast, from_asteroid): ast for ast in asteroids if ast is not from_asteroid}

def find_best_asteroid(asteroids):
    return max(((ast, reachable_asteroids(asteroids, ast)) for ast in asteroids), key=lambda x: len(x[1]))

best_ast = find_best_asteroid(asteroids)
print(best_ast)

# tot_burned = []
# i = 1
# while east or west:
#     burned = []
#     for ast in east:
#         if not any([is_on_line(main_ast, ast, b) for b in burned]):
#             print("BURN ",i, ast)
#             burned.append(ast)
#             tot_burned.append(ast)
#             i += 1

#     for b in burned:
#         east.remove(b)

#     burned = []
#     print("BREAK")
        


# def dist_from_orig(a):
#     return math.sqrt(((a[0] - main_ast[0]) ** 2 + (a[1] - main_ast[1]) ** 2))

# NORTH_VEC = ( main_ast[0],main_ast[1]-1)
# SOUTH_VEC = ( main_ast[0],main_ast[1]+1)

# east = sorted([ast for ast in asteroids if (ast[0] >= main_ast[0]) and ast != main_ast], key=lambda x: (round(find_ang(NORTH_VEC,x, main_ast),6), dist_from_orig(x)))
# west = sorted([ast for ast in asteroids if (ast[0] < main_ast[0]) and ast != main_ast], key=lambda x: (round(find_ang(SOUTH_VEC,x, main_ast),6), dist_from_orig(x)))

# assert len(east) + len(west) == len(asteroids)-1


# print("AAAAAAAAAAAAAAAAAA",main_ast, west)
# print("YOLOaaaaa",find_ang(SOUTH_VEC,(4,4), main_ast))
# print("YOLObbbbb",find_ang(SOUTH_VEC,(10,16), main_ast))

# tot_burned = []
# i = 1
# while east or west:
#     burned = []
#     for ast in east:
#         if not any([is_on_line(main_ast, ast, b) for b in burned]):
#             print("BURN ",i, ast)
#             burned.append(ast)
#             tot_burned.append(ast)
#             i += 1

#     for b in burned:
#         east.remove(b)

#     burned = []
#     print("BREAK")

#     for ast in west:
#         if not any([is_on_line(main_ast, ast, b) for b in burned]):
#             print("BURN",i, ast)
#             burned.append(ast)
#             tot_burned.append(ast)
#             i += 1
#     for b in burned:
#         west.remove(b)

#     burned = []

# print("lulz", tot_burned[199])



