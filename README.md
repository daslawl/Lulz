from copy import deepcopy
from itertools import permutations
from collections import defaultdict


class Computer:
    def __init__(self, mem, ip=0):
        self.mem = deepcopy(mem)
        self.ip = ip
        self.input_value = None
        self.rel_base = 0
        self.extra_mem = defaultdict(int)

    def store_mem(self, idx, val):
        # print("store_mem", idx, val)
        store_idx = self.param_idx(idx, True)
        if store_idx < len(self.mem):
            self.mem[store_idx] = val
        else:
            self.extra_mem[store_idx] = val

    def get_mem(self, idx):
        print("get_mem", idx)
        print("get_mem", self.param_idx(idx))
        if self.param_idx(idx) < len(self.mem):
            return self.mem[self.param_idx(idx)]
        else:
            return self.extra_mem[self.param_idx(idx)]

    def add(self):
        self.store_mem(2, self.get_mem(0) + self.get_mem(1))
        self.ip += 4

    def mul(self):
        self.store_mem(2, self.get_mem(0) * self.get_mem(1))
        self.ip += 4

    def input(self):
        self.store_mem(0, self.input_value)
        self.input_value = None
        self.ip += 2

    def output(self):
        out = self.get_mem(0)
        print(out)
        self.ip += 2
        return out

    def je(self):
        self.ip = self.get_mem(1) if self.get_mem(0) else self.ip + 3

    def jne(self):
        self.ip = self.get_mem(1) if not self.get_mem(0) else self.ip + 3

    def lt(self):
        self.store_mem(2, 1 if self.get_mem(0) < self.get_mem(1) else 0)
        self.ip += 4

    def eq(self):
        b = 1 if self.get_mem(0) == self.get_mem(1) else 0
        # print("boo", b, self.get_mem(0), self.get_mem(1))
        self.store_mem(2, b)
        self.ip += 4

    def relative_base(self):
        self.rel_base += self.get_mem(0)
        print("self.rel_base += ", self.get_mem(0), self.rel_base)
        self.ip += 2

    # TODO: clean up this mess
    def param_idx(self, idx, assign=False):
        s = str(self.mem[self.ip])

        if len(s) == 1:
            return self.mem[self.ip + idx + 1]
        op, remaining = s[-2:], s[:-2]

        if len(remaining) == 0:
            return int(op)

        if len(remaining) == 1:
            conf = (int(remaining[-1]), 0, 0)
        if len(remaining) == 2:
            conf = (int(remaining[-1]), int(remaining[-2]), 0)
        if len(remaining) == 3:
            conf = (int(remaining[-1]), int(remaining[-2]), int(remaining[-3]))
        print("conf", conf, conf[idx], self.rel_base)

        if conf[idx] == 0 or assign:
            return self.mem[self.ip + idx + 1]
        elif conf[idx] == 1:
            return self.ip + idx + 1
        else:
            print("OTHER")
            return self.rel_base + idx + 1

    def param_value(self, idx):
        return self.mem[self.param_idx(idx)]

    def op(self):
        s = str(
            self.mem[self.ip] if self.ip < len(self.mem) else self.extra_mem[self.ip]
        )
        return int(s[-2:])

    def run(self, input_value):
        self.input_value = input_value

        opcode = self.op()

        while opcode != 99:
            opcode = self.op()
            print("OP", opcode, self.ip)
            if opcode == 1:
                self.add()
            if opcode == 2:
                self.mul()
            if opcode == 3:
                if self.input_value:
                    self.input()
                else:
                    return
            if opcode == 4:
                return self.output()
            if opcode == 5:
                self.je()
            if opcode == 6:
                self.jne()
            if opcode == 7:
                self.lt()
            if opcode == 8:
                self.eq()
            if opcode == 9:
                self.relative_base()


if __name__ == "__main__":
    with open("day9/e9.txt") as f:
        data = list(map(int, f.read().split(",")))
    # 1

    # with open("day7/e7.txt") as f:
    #     data = list(map(int, f.read().split(",")))
    data = [109, 1, 204, -1, 1001, 100, 1, 100, 1008, 100, 16, 101, 1006, 101, 0, 99]
    print(Computer(data).run(5))
