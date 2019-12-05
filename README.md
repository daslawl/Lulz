# Lulz

from operator import add, mul
from copy import deepcopy

INPUT = 5
def get_action(opcode):
    s = str(opcode)
    op = s[-2:]
    remaining = s[:-2]

    if len(remaining) == 0:
        return int(op), None

    if len(remaining) == 1:
        return int(op), (int(remaining[-1]),0,0)
    if len(remaining) == 2:
        return int(op), (int(remaining[-1]),int(remaining[-2]),0)
    if len(remaining) == 3:
        return int(op), (int(remaining[-1]),int(remaining[-2]),int(remaining[-3]))

# 1
def parse_opcodes(lst):
    lst = deepcopy(lst)
    i = 0
    while True:
        # hax
        if lst[i] == 3:
            op = 3
        else:
            op, modes = get_action(lst[i])

        if op == 1:
            a = lst[i+1] if modes and modes[0] else lst[lst[i+1]]
            b = lst[i+2] if modes and modes[1] else lst[lst[i+2]]
            lst[lst[i+3]] = a + b
            i += 4
        elif op == 2:
            a = lst[i+1] if modes and modes[0] else lst[lst[i+1]] 
            b = lst[i+2] if modes and modes[1] else lst[lst[i+2]]
            lst[lst[i+3]] = a * b
            i += 4
        elif op == 3:
            lst[lst[i+1]] = INPUT
            i += 2
        elif op == 4:
            print( lst[i+1] if modes and modes[0] else lst[lst[i+1]] )
            i += 2
        elif op == 5:
            a = lst[i+1] if modes and modes[0] else lst[lst[i+1]]
            b = lst[i+2] if modes and modes[1] else lst[lst[i+2]]
            if a != 0:
                i = b
        elif op == 6:
            a = lst[i+1] if modes and modes[0] else lst[lst[i+1]]
            b = lst[i+2] if modes and modes[1] else lst[lst[i+2]]
            if a == 0:
                i = b
        elif op == 7:
            a = lst[i+1] if modes and modes[0] else lst[lst[i+1]]
            b = lst[i+2] if modes and modes[1] else lst[lst[i+2]]
            lst[lst[i+3]] = 1 if a < b else 0
            i += 4
        elif op == 8:
            a = lst[i+1] if modes and modes[0] else lst[lst[i+1]]
            b = lst[i+2] if modes and modes[1] else lst[lst[i+2]]
            lst[lst[i+3]] = 1 if a == b else 0
            i += 4
        elif op == 99:
            break
        

    return lst[0]


with open("e5_new.txt") as f:
    lst = list(map(int, f.read().split(",")))

print(parse_opcodes(lst))

# 2
