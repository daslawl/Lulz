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
        idx = self.param_idx(idx, True)
        if idx < len(self.mem):
            self.mem[idx] = val
        else:
            self.extra_mem[idx] = val

    def get_mem(self, idx):
        idx  =self.param_idx(idx)
        if idx < len(self.mem):
            return self.mem[idx]
        else:
            return self.extra_mem[idx]

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
        self.store_mem(2, b)
        self.ip += 4

    def relative_base(self):
        self.rel_base += self.get_mem(0)
        self.ip += 2

    # TODO: clean up this mess
    def param_idx2(self, idx, assign=False):
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
        if conf[idx] == 2:
            return self.mem[self.ip + idx + 1] + self.rel_base 
        elif conf[idx] == 0 or assign:
            return self.mem[self.ip + idx + 1]
        elif conf[idx] == 1:
            return self.ip + idx + 1
        else:
            print("Unimplemented mode")
    
    def get_idx(self, idx, assign, mode):            
        if mode == 2:
            return self.mem[self.ip + idx + 1] + self.rel_base 
        elif mode == 0 or assign:
            return self.mem[self.ip + idx + 1]
        elif mode == 1:
            return self.ip + idx + 1


    def param_idx(self, idx, assign=False):
        s = self.mem[self.ip]

        quote, reminder = divmod(s, 10)

        if quote == 0:
            return self.mem[self.ip + idx + 1]
            
        op = reminder
        quote, reminder = divmod(quote, 10)

        if quote == 0:
            return reminder*10+op

        
        quote, reminder1 = divmod(quote, 10)

        if quote == 0:
            return self.get_idx(idx,assign,(reminder1, 0, 0)[idx])
        
        quote, reminder2 = divmod(quote, 10)

        if quote == 0:
            return self.get_idx(idx,assign, (reminder1, reminder2, 0)[idx])

        quote, reminder3 = divmod(quote, 10)

        if quote == 0:
            return self.get_idx(idx,assign, (reminder1, reminder2, reminder3)[idx])
            
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
                yield self.output()
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
    with open("e9_input2.txt") as f:
        data = list(map(int, f.read().split(",")))
    # 1
    for v in Computer(data).run(1):
        print(v)
    #2 
    for v in Computer(data).run(2):
        print(v)
    
