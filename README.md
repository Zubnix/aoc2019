# aoc2019
kladderblok
# DAY 1
```javscript
const input = `146561
98430
131957
81605
70644
55060
93217
107158
110769
94650
141070
72381
100736
105705
99003
94057
110662
74429
55509
63492
102007
72627
95183
112072
122313
116884
125451
106093
140678
121751
149018
58459
138306
149688
82927
72676
95010
88439
51807
103175
107633
126439
128879
112054
52873
114493
77365
76768
60838
89692
66217
96060
100338
139063
126869
106490
128967
116312
56822
52422
124579
117120
106245
105255
66975
115340
145764
149427
64228
64237
67887
103345
134901
50226
126991
122314
140818
129687
149792
101148
73411
87078
121272
108804
96063
81155
62058
112684
134263
128454
99455
91689
141448
143892
103257
64352
90769
78307
111855
130153`
```

# 1
```javascript
const calcFuel = module => Math.floor(module/3)-2
input.split("\n").map(value=>value.trim()).map(calcFuel).reduce((total,num)=>total+num)
```

# 2
```javascript
const calcFuel = module => Math.max(Math.floor(module/3)-2,0)
const allFuel = mass => {
  if(mass === 0){ return 0 }
  const fuel = calcFuel(mass)
  return fuel + allFuel(fuel)
}

input.split("\n").map(value => value.trim()).map(allFuel).reduce((total, num) => total+num)
```

# Day 2
```javascript
class Pointer {
  /**
   * @param {Array<number>}memory
   * @param {number}address
   */
  constructor (memory, address) {
    /**
     * @type {Array<number>}
     */
    this.memory = memory
    /**
     * @type {number}
     */
    this.address = address
  }

  /**
   * @return {number}
   */
  dereference (offset) {
    return this.memory[this.address + (offset || 0)]
  }

  /**
   * @param {number}value
   */
  write (value) {
    this.memory[this.address] = value
  }

  /**
   * @param {number}offset
   * @return {Pointer}
   */
  pointerWithOffset (offset) {
    return new Pointer(this.memory, this.address + offset)
  }
}

/**
 * @type {{
 * '99': (function(Pointer): function(): null),
 * '1': (function(Pointer): function(): Pointer),
 * '2': (function(Pointer): function(): Pointer)}
 * }
 */
const instructions = {
  /**
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  1: (executionPointer) =>
    () => {
      // load args
      const arg0Address = executionPointer.dereference(1)
      const arg1Address = executionPointer.dereference(2)
      const resultAddress = executionPointer.dereference(3)
      const arg0Value = new Pointer(executionPointer.memory, arg0Address).dereference()
      const arg1Value = new Pointer(executionPointer.memory, arg1Address).dereference()
      const resultPointer = new Pointer(executionPointer.memory, resultAddress)

      // execute and write result
      resultPointer.write(arg0Value + arg1Value)

      // return next instruction location
      return executionPointer.pointerWithOffset(4)
    },
  /**
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  2: (executionPointer) =>
    () => {
      // load args
      const arg0Address = executionPointer.dereference(1)
      const arg1Address = executionPointer.dereference(2)
      const resultAddress = executionPointer.dereference(3)
      const arg0Value = new Pointer(executionPointer.memory, arg0Address).dereference()
      const arg1Value = new Pointer(executionPointer.memory, arg1Address).dereference()
      const resultPointer = new Pointer(executionPointer.memory, resultAddress)

      // execute and write result
      resultPointer.write(arg0Value * arg1Value)

      // return next instruction location
      return executionPointer.pointerWithOffset(4)
    },
  /**
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  99: (executionPointer) => () => null
}

class Program {
  /**
   * @param {Array<number>}programMemory
   */
  constructor (programMemory) {
    this.nextInstructionPointer = new Pointer(programMemory, 0)
  }

  run () {
    while (this.nextInstructionPointer) {
      this.step()
    }
  }

  // in case you want to add debugging :o)
  step () {
    const nextInstruction = this.loadNextInstruction(this.nextInstructionPointer)
    this.nextInstructionPointer = nextInstruction()
  }

  /**
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  loadNextInstruction (executionPointer) {
    const instructionCode = executionPointer.dereference()
    const opcode = instructionCode
    const instruction = instructions[opcode]
    if (instruction) {
      return instruction(executionPointer)
    }
    throw new Error(`illegal opcode: ${opcode}`)
  }
}

// part 1
let programMemory = [1, 0, 0, 3, 1, 1, 2, 3, 1, 3, 4, 3, 1, 5, 0, 3, 2, 1, 13, 19, 1, 9, 19, 23, 2, 13, 23, 27, 2, 27, 13, 31, 2, 31, 10, 35, 1, 6, 35, 39, 1, 5, 39, 43, 1, 10, 43, 47, 1, 5, 47, 51, 1, 13, 51, 55, 2, 55, 9, 59, 1, 6, 59, 63, 1, 13, 63, 67, 1, 6, 67, 71, 1, 71, 10, 75, 2, 13, 75, 79, 1, 5, 79, 83, 2, 83, 6, 87, 1, 6, 87, 91, 1, 91, 13, 95, 1, 95, 13, 99, 2, 99, 13, 103, 1, 103, 5, 107, 2, 107, 10, 111, 1, 5, 111, 115, 1, 2, 115, 119, 1, 119, 6, 0, 99, 2, 0, 14, 0]
// replace position 1 with the value 12 and replace position 2 with the value 2. What value is left at position 0 after the program halts?
programMemory[1] = 12
programMemory[2] = 2
new Program(programMemory).run()
console.log(programMemory[0])

// part 2
let noun = -1
let verb = -1
do {
  noun++
  do {
    verb++

    programMemory = [1, noun, verb, 3, 1, 1, 2, 3, 1, 3, 4, 3, 1, 5, 0, 3, 2, 1, 13, 19, 1, 9, 19, 23, 2, 13, 23, 27, 2, 27, 13, 31, 2, 31, 10, 35, 1, 6, 35, 39, 1, 5, 39, 43, 1, 10, 43, 47, 1, 5, 47, 51, 1, 13, 51, 55, 2, 55, 9, 59, 1, 6, 59, 63, 1, 13, 63, 67, 1, 6, 67, 71, 1, 71, 10, 75, 2, 13, 75, 79, 1, 5, 79, 83, 2, 83, 6, 87, 1, 6, 87, 91, 1, 91, 13, 95, 1, 95, 13, 99, 2, 99, 13, 103, 1, 103, 5, 107, 2, 107, 10, 111, 1, 5, 111, 115, 1, 2, 115, 119, 1, 119, 6, 0, 99, 2, 0, 14, 0]
    new Program(programMemory).run()
  } while (programMemory[0] !== 19690720 && verb < 100)
  verb = -1
} while (programMemory[0] !== 19690720 && noun < 100)
console.log(100 * programMemory[1] + programMemory[2])
```
