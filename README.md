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

# Day 3
part 1
```javascript
const input = `R1009,D335,L942,D733,L398,U204,L521,D347,L720,U586,R708,D746,L292,U416,L824,U20,R359,D828,R716,U895,L498,D671,L325,D68,L667,U134,L435,D44,R801,U654,R188,U542,L785,D318,L806,U602,L465,U239,R21,U571,R653,U436,L52,U380,R446,D960,R598,U590,L47,U972,L565,D281,R790,U493,R864,D396,R652,D775,L939,D284,R554,U629,L842,D837,R554,D795,R880,D301,R948,U974,L10,D898,R588,D743,L334,U59,L413,U511,L132,U771,R628,D805,R465,D561,R18,D169,L580,D99,L508,U964,L870,D230,L472,U897,L85,U306,L103,U322,L637,U464,R129,D514,R454,U479,R801,U18,R929,U181,L113,D770,L173,D124,L122,U481,L666,D942,L534,U608,R90,U576,L641,U249,L857,U197,R783,D92,L938,D192,L698,D862,R995,U12,R766,D323,R934,U315,R956,D234,R983,D246,L153,U26,L779,D628,R174,D385,L758,D486,R132,U414,R915,D511,L152,D309,L708,D755,L679,D166,L699,U734,R55,D224,L582,U798,L348,U219,L304,U621,L788,D538,R781,D509,R486,U581,R759,D892,R16,D552,L82,D618,L309,D610,L645,U146,L328,U569,L307,D385,L249,D231,R928,U681,R384,D337,R715,D798,L788,D604,R517,U766,R368,U430,L49,U236,R621,U656,R997,U268,L18,D789,L935,D87,L670,U35,R463,D71,R268,U728,R693,D863,R656,D654,L350,U796,L72,U562,R56,U10,L651,D751,L557,D518,R901,D741,R787,D332,R723,D980,R206,U670,R645,D927,L641,D863,R478,D568,L858,D990,L124,D864,L162,U361,L407,U674,R508,D284,L675,D794,L138,U55,L781,U37,R956,D364,L111,U721,L91,U559,L852,U351,R994,U446,L162,D345,R92,D941,R572,U185,R615,D590,R459,D313,R127,D315,R96,U751,R210,D620,L790,U826,R410,D652,R549,D698,L805,U814,L364,U905,L96,U997,L689
L1008,D451,L146,D628,R877,U486,L464,U815,L119,U208,R686,U477,L510,D353,R189,D437,R461,D645,R639,U650,R491,D744,L798,U514,R598,U64,R668,U771,R21,U782,L564,U632,R23,U112,R947,U649,L205,D804,R277,U683,L828,U662,R890,U420,L908,U484,R535,D515,R390,U7,L287,D967,R497,U502,L893,D851,R426,D656,R622,U46,L106,U590,R646,D29,R467,D896,L155,U382,L992,D189,L34,U16,R132,U35,L586,U812,L539,D409,R776,D42,R58,U323,R569,D965,R648,D789,R478,D587,R162,D834,R979,D993,L944,U84,R93,U903,R491,U713,L646,U235,R120,U286,L919,U34,L662,U834,L812,D271,L73,U410,L758,U210,R712,U581,L520,D654,L981,D516,R312,U123,L153,U433,R368,U606,L882,U362,L261,U587,R441,D691,L699,U135,L825,D25,R142,U191,L358,D554,L487,D802,L542,D266,R283,U222,R113,D259,R828,U182,R402,U627,R769,D426,L768,U571,R118,U684,R803,D430,R942,U514,R711,D225,R299,U45,L214,U712,L673,U787,L164,D703,L616,D587,R624,D326,L614,D779,L904,D563,L98,U137,R687,U425,R615,U671,L361,D47,L767,D951,R791,D116,R664,U704,R291,U535,L322,D989,R467,U7,L974,D276,R901,U51,L567,D641,R112,U102,R753,D127,R486,D143,R259,U212,L97,U505,R377,U473,R514,D912,L928,U401,R772,D416,R695,U784,L524,D341,R402,U749,L1,U1,L109,U921,L754,U66,L927,U708,R551,D687,R129,D346,L408,D330,L300,D920,R170,D353,R97,D74,R850,D511,R275,U872,L748,U344,R610,D391,R963,D98,L89,U259,R651,U651,L31,D142,L104,U770,L482,D677,R823,D110,L606,U897,L631,U437,L551,D550,R301,D762,R349,D824,R260,U438,R249,D636,L386,U926,R367,U231,R752,U854,L481,D764,R516,D273,L726,D778,R483,U513,R129,D135,L224`

class LineSegment {
  /**
   * @param {Array<Array<number>>}coords 2 coordinates: [x,y], begin->end
   */
  constructor (coords) {
    this.coords = coords
  }

  /**
   * @param {'R'|'L'|'U'|'D'}direction
   * @param {number}segmentLength
   */
  newSegment (direction, segmentLength) {
    const [x, y] = this.coords[1]

    switch (direction) {
      case 'R':
        return new LineSegment([
          this.coords[1],
          [x + segmentLength, y]
        ])
      case 'L':
        return new LineSegment([
          this.coords[1],
          [x - segmentLength, y]
        ])
      case 'U':
        return new LineSegment([
          this.coords[1],
          [x, y + segmentLength]
        ])
      case 'D':
        return new LineSegment([
          this.coords[1],
          [x, y - segmentLength]
        ])
      default:
        throw new Error(`unknown wire segment direction ${direction}`)
    }
  }

  intersectsWith (otherLineSegment) {
    const p0_x = this.coords[0][0]
    const p0_y = this.coords[0][1]
    const p1_x = this.coords[1][0]
    const p1_y = this.coords[1][1]

    const p2_x = otherLineSegment.coords[0][0]
    const p2_y = otherLineSegment.coords[0][1]
    const p3_x = otherLineSegment.coords[1][0]
    const p3_y = otherLineSegment.coords[1][1]

    //  based on an algorithm in Andre LeMothe's "Tricks of the Windows Game Programming Gurus": https://www.amazon.com/dp/0672323699/
    const s1_x = p1_x - p0_x
    const s1_y = p1_y - p0_y
    const s2_x = p3_x - p2_x
    const s2_y = p3_y - p2_y

    const s = (-s1_y * (p0_x - p2_x) + s1_x * (p0_y - p2_y)) / (-s2_x * s1_y + s1_x * s2_y)
    const t = (s2_x * (p0_y - p2_y) - s2_y * (p0_x - p2_x)) / (-s2_x * s1_y + s1_x * s2_y)

    if (s >= 0 && s <= 1 && t >= 0 && t <= 1) {
      // Collision detected
      const x = p0_x + (t * s1_x)
      const y = p0_y + (t * s1_y)
      if (x === 0 && y === 0) return null
      return [x, y]
    }

    return null // No collision
  }
}

class Wire {
  constructor () {
    // first wire segment starts at 0,0
    /**
     * @type {LineSegment[]}
     */
    this.lineSegments = [new LineSegment([[0, 0], [0, 0]])]
  }

  /**
   * @param {LineSegment}lineSegment
   */
  addLineSegment (lineSegment) {
    this.lineSegments.push(lineSegment)
  }

  /**
   * @return {LineSegment}
   */
  getLastLineSegment () {
    return this.lineSegments.slice(-1).pop()
  }

  /**
   * @param {Wire}otherWire
   * @return {number[]}
   */
  intersectionsWith (otherWire) {
    // iterate over all our line segments
    return this.lineSegments.reduce(
      (allIntersections, lineSegment) => {
        // and compare each of our line segments with all other line segments
        const newIntersections = otherWire.lineSegments.reduce(
          (intersections, otherLineSegment) => {
            // if we find an intersection, we add it to the list
            const intersectionCoord = lineSegment.intersectsWith(otherLineSegment)
            if (intersectionCoord) {
              intersections.push(intersectionCoord)
            }
            return intersections
          },
          /** @type{Array<number>} */[]
        )
        // add all newly find intersections of our line segment with the other wire
        return [...allIntersections, ...newIntersections]
      },
      /** @type{Array<number>} */[]
    )
  }
}

const distance = input.trim()
  .split('\n')
  .map(wireValues => wireValues.split(','))
  .map(wireFragmentStrings => {
    return wireFragmentStrings.map(wireFragmentString => {
      const direction = wireFragmentString.charAt(0)
      const segmentLength = Number.parseInt(wireFragmentString.substring(1))
      return { direction, segmentLength }
    })
  })
  .map(wireFragmentTuples =>
    wireFragmentTuples.reduce((wire, wireFragmentTuple) => {
      const { direction, segmentLength } = wireFragmentTuple
      const nextSegment = wire.getLastLineSegment().newSegment(direction, segmentLength)
      wire.addLineSegment(nextSegment)
      return wire
    }, new Wire())
  )
  .reduce(
    (intersectonsResult, wire) => {
      // we will compare each wire with every other wire (cartesian product) in the list
      const foundIntersections = intersectonsResult.processedWires.reduce(
        (allIntersections, previousWire) => {
          const intersections = wire.intersectionsWith(previousWire)
          return [...allIntersections, ...intersections]
        },
        []
      )
      intersectonsResult.intersections.push(...foundIntersections)
      intersectonsResult.processedWires.push(wire)
      return intersectonsResult
    },
    {
      intersections: /** @type {Array<Array<number>>} */[],
      processedWires: /** @type{Array<Wire>} */ []
    }
  )
  .intersections.reduce(
    (distance, intersectionDistance) => {
      const manhattanDistance = Math.abs(intersectionDistance[0]) + Math.abs(intersectionDistance[1])
      return manhattanDistance < distance ? manhattanDistance : distance
    },
    Number.MAX_SAFE_INTEGER
  )

console.log(distance)
```

# Day 5

```javascript
const programMemory = require('./day5input')

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

const instructionMode = (instructionCode, pos) => (((instructionCode / (10 * 10 ** pos))) >> 0) % 10

const parseInstructionArgs = (executionPointer) => {
  const instructionCode = executionPointer.dereference()
  const icFirst = instructionMode(instructionCode, 1)
  const icSecond = instructionMode(instructionCode, 2)

  const arg0 = executionPointer.dereference(1)
  const arg1 = executionPointer.dereference(2)
  const resultAddress = executionPointer.dereference(3)
  const arg0Value = icFirst ? arg0 : new Pointer(executionPointer.memory, arg0).dereference()
  const arg1Value = icSecond ? arg1 : new Pointer(executionPointer.memory, arg1).dereference()
  const resultPointer = new Pointer(executionPointer.memory, resultAddress)

  return { arg0Value, arg1Value, resultPointer }
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
   * sums
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  1: (executionPointer) => {
    // load args
    const { arg0Value, arg1Value, resultPointer } = parseInstructionArgs(executionPointer)
    return () => {
      // execute and write result
      resultPointer.write(arg0Value + arg1Value)
      // return next instruction location
      return executionPointer.pointerWithOffset(4)
    }
  },
  /**
   * multiply
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  2: (executionPointer) => {
    // load args
    const { arg0Value, arg1Value, resultPointer } = parseInstructionArgs(executionPointer)
    return () => {
      // execute and write result
      resultPointer.write(arg0Value * arg1Value)

      // return next instruction location
      return executionPointer.pointerWithOffset(4)
    }
  },
  /**
   * write input
   * @param {Pointer}executionPointer
   * @param {Pointer}inputPointer
   * @param {Pointer}outputPointer
   * @return {function(): Pointer}
   */
  3: (executionPointer, inputPointer, outputPointer) => {
    const input = inputPointer.dereference()
    const arg0Address = executionPointer.dereference(1)
    const resultPointer = new Pointer(executionPointer.memory, arg0Address)

    return () => {
      resultPointer.write(input)
      // return next instruction location
      return executionPointer.pointerWithOffset(2)
    }
  },
  /**
   * write output
   * @param {Pointer}executionPointer
   * @param {Pointer}inputPointer
   * @param {Pointer}outputPointer
   * @return {function(): Pointer}
   */
  4: (executionPointer, inputPointer, outputPointer) => {
    const arg0Address = executionPointer.dereference(1)
    const outValue = new Pointer(executionPointer.memory, arg0Address).dereference()

    return () => {
      outputPointer.write(outValue)
      console.log(`${outValue}`)

      // return next instruction location
      return executionPointer.pointerWithOffset(2)
    }
  },
  /**
   * jump if true
   * @param {Pointer}executionPointer
   * @param {Pointer}inputPointer
   * @param {Pointer}outputPointer
   * @return {function(): Pointer}
   */
  5: (executionPointer, inputPointer, outputPointer) => {
    const { arg0Value, arg1Value } = parseInstructionArgs(executionPointer)

    return () => arg0Value ? new Pointer(executionPointer.memory, arg1Value) : executionPointer.pointerWithOffset(3)
  },
  /**
   * jump if false
   * @param {Pointer}executionPointer
   * @param {Pointer}inputPointer
   * @param {Pointer}outputPointer
   * @return {function(): Pointer}
   */
  6: (executionPointer, inputPointer, outputPointer) => {
    const { arg0Value, arg1Value } = parseInstructionArgs(executionPointer)

    return () => arg0Value ? executionPointer.pointerWithOffset(3) : new Pointer(executionPointer.memory, arg1Value)
  },
  /**
   * less than
   * @param {Pointer}executionPointer
   * @param {Pointer}inputPointer
   * @param {Pointer}outputPointer
   * @return {function(): Pointer}
   */
  7: (executionPointer, inputPointer, outputPointer) => {
    const { arg0Value, arg1Value, resultPointer } = parseInstructionArgs(executionPointer)

    return () => {
      arg0Value < arg1Value ? resultPointer.write(1) : resultPointer.write(0)
      return executionPointer.pointerWithOffset(4)
    }
  },
  /**
   * equals
   * @param {Pointer}executionPointer
   * @param {Pointer}inputPointer
   * @param {Pointer}outputPointer
   * @return {function(): Pointer}
   */
  8: (executionPointer, inputPointer, outputPointer) => {
    const { arg0Value, arg1Value, resultPointer } = parseInstructionArgs(executionPointer)

    return () => {
      arg0Value === arg1Value ? resultPointer.write(1) : resultPointer.write(0)
      return executionPointer.pointerWithOffset(4)
    }
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
   * @param {Pointer}inputPointer
   * @param {Pointer}outputPointer
   */
  constructor (programMemory, inputPointer, outputPointer) {
    /**
     * @type {Pointer}
     */
    this.inputPointer = inputPointer
    /**
     * @type {Pointer}
     */
    this.outputPointer = outputPointer
    /**
     * @type {Pointer}
     */
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
    const opcode = instructionCode % 100
    const instruction = instructions[opcode]
    if (instruction) {
      return instruction(executionPointer, this.inputPointer, this.outputPointer)
    }
    throw new Error(`illegal opcode: ${opcode}`)
  }
}

// part 1
const input = [1]
const output = [0]
new Program(programMemory, new Pointer(input, 0), new Pointer(output, 0)).run()

// part 2
const input = [5]
const output = [0]
new Program(programMemory, new Pointer(input, 0), new Pointer(output, 0)).run()
```

# Day 6

1
```javascript
//const input = require('./day6input')
const input = `COM)B
B)C
C)D
D)E
E)F
B)G
G)H
D)I
E)J
J)K
K)L`

/**
 * orbit as a double linked list
 */
class Orbit {
  /**
   * @param {AllOrbits}allOrbits
   * @param {string}orbitString
   */
  static create (allOrbits, orbitString) {
    const [previousId, id] = orbitString.split(')')

    const orbit = allOrbits.getOrCreateOrbit(id)
    const previous = allOrbits.getOrCreateOrbit(previousId)

    if (previous._next) {
      // we have a branching point here, cut parent->child
      const otherChild = previous._next
      previous._next = null
      // Note that we don't cut the parent<-child, so we still have our tree structure which we need to calculate our index
      orbit._previous = previous

      // add the 2 orbits as they will function as the start of a series
      allOrbits.orbitSeries.push(otherChild)
      allOrbits.orbitSeries.push(orbit)
    } else {
      orbit.previous = previous
    }

    return orbit
  }

  /**
   * @param {string}id
   */
  constructor (id) {
    this.id = id
    /**
     * @type {Orbit}
     */
    this._previous = null
    /**
     * @type {Orbit}
     */
    this._next = null
  }

  /**
   * @param {Orbit}next
   */
  set next (next) {
    next._previous = this
    this._next = next
  }

  /**
   * @param {Orbit}previous
   */
  set previous (previous) {
    previous._next = this
    this._previous = previous
  }

  calculateIndex () {
    if (this._previous) {
      return this._previous.calculateIndex() + 1
    } else {
      // COM
      return 0
    }
  }
}

class AllOrbits {
  /**
   * @param orbit
   * @return {AllOrbits}
   */
  static create (orbit) {
    const allOrbits = new AllOrbits()
    allOrbits.addOrbit(orbit)
    allOrbits.orbitSeries.push(orbit)
    return allOrbits
  }

  constructor () {
    /** @type {Object.<string,Orbit>} */
    this.all = {}
    /**
     * @type {Array<Orbit>}
     */
    this.orbitSeries = []
  }

  /**
   * @param {Orbit}orbit
   */
  addOrbit (orbit) {
    this.all[orbit.id] = orbit
  }

  /**
   * @param {string}id
   */
  getOrCreateOrbit (id) {
    let orbit = this.all[id]
    if (orbit == undefined) {
      orbit = new Orbit(id)
      this.all[id] = orbit
    }
    return orbit
  }
}

const totalOrbitalDistances = input.trim()
  .split('\n')
  .reduce(
    (allOrbits, orbitString) => {
      Orbit.create(allOrbits, orbitString)
      return allOrbits
    },
    AllOrbits.create(new Orbit('COM', null, null))
  ).orbitSeries
  .map(orbitSegment => {
    let orbit = orbitSegment
    const firstIdx = orbit.calculateIndex()
    let lastIdx = firstIdx
    while (orbit._next !== null) {
      orbit = orbit._next
      lastIdx = orbit.calculateIndex()
    }
    return [firstIdx, lastIdx]
  })
  // Adaptation on Gauss' som formula. Gauss was a cool guy. Thanks Gauss: https://nl.wikipedia.org/wiki/Somformule_van_Gauss
  .map(([startIdx, endIdx]) => ((startIdx + endIdx) * ((endIdx - startIdx) + 1)) / 2)
  .reduce((previousValue, currentValue) => previousValue + currentValue)

console.log(totalOrbitalDistances)
```

# Day 7
### 1
```javascript
const programMemory = require('./day7input')

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

const instructionMode = (instructionCode, pos) => (((instructionCode / (10 * 10 ** pos))) >> 0) % 10

const parseInstructionArgs = (executionPointer) => {
  const instructionCode = executionPointer.dereference()
  const icFirst = instructionMode(instructionCode, 1)
  const icSecond = instructionMode(instructionCode, 2)

  const arg0 = executionPointer.dereference(1)
  const arg1 = executionPointer.dereference(2)
  const resultAddress = executionPointer.dereference(3)
  const arg0Value = icFirst ? arg0 : new Pointer(executionPointer.memory, arg0).dereference()
  const arg1Value = icSecond ? arg1 : new Pointer(executionPointer.memory, arg1).dereference()
  const resultPointer = new Pointer(executionPointer.memory, resultAddress)

  return { arg0Value, arg1Value, resultPointer }
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
   * sums
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  1: (executionPointer) => {
    // load args
    const { arg0Value, arg1Value, resultPointer } = parseInstructionArgs(executionPointer)
    return () => {
      // execute and write result
      resultPointer.write(arg0Value + arg1Value)
      // return next instruction location
      return executionPointer.pointerWithOffset(4)
    }
  },
  /**
   * multiply
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  2: (executionPointer) => {
    // load args
    const { arg0Value, arg1Value, resultPointer } = parseInstructionArgs(executionPointer)
    return () => {
      // execute and write result
      resultPointer.write(arg0Value * arg1Value)

      // return next instruction location
      return executionPointer.pointerWithOffset(4)
    }
  },
  /**
   * write input
   * @param {Pointer}executionPointer
   * @param {Array<number>}inputStream
   * @return {function(): Pointer}
   */
  3: (executionPointer, inputStream) => {
    const input = inputStream.shift()
    const arg0Address = executionPointer.dereference(1)
    const resultPointer = new Pointer(executionPointer.memory, arg0Address)

    return () => {
      resultPointer.write(input)
      // return next instruction location
      return executionPointer.pointerWithOffset(2)
    }
  },
  /**
   * write output
   * @param {Pointer}executionPointer
   * @param {Array<number>}inputStream
   * @param {Array<number>}outputStream
   * @return {function(): Pointer}
   */
  4: (executionPointer, inputStream, outputStream) => {
    const arg0Address = executionPointer.dereference(1)
    const outValue = new Pointer(executionPointer.memory, arg0Address).dereference()

    return () => {
      outputStream.push(outValue)

      // return next instruction location
      return executionPointer.pointerWithOffset(2)
    }
  },
  /**
   * jump if true
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  5: (executionPointer) => {
    const { arg0Value, arg1Value } = parseInstructionArgs(executionPointer)

    return () => arg0Value ? new Pointer(executionPointer.memory, arg1Value) : executionPointer.pointerWithOffset(3)
  },
  /**
   * jump if false
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  6: (executionPointer) => {
    const { arg0Value, arg1Value } = parseInstructionArgs(executionPointer)

    return () => arg0Value ? executionPointer.pointerWithOffset(3) : new Pointer(executionPointer.memory, arg1Value)
  },
  /**
   * less than
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  7: (executionPointer) => {
    const { arg0Value, arg1Value, resultPointer } = parseInstructionArgs(executionPointer)

    return () => {
      arg0Value < arg1Value ? resultPointer.write(1) : resultPointer.write(0)
      return executionPointer.pointerWithOffset(4)
    }
  },
  /**
   * equals
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  8: (executionPointer) => {
    const { arg0Value, arg1Value, resultPointer } = parseInstructionArgs(executionPointer)

    return () => {
      arg0Value === arg1Value ? resultPointer.write(1) : resultPointer.write(0)
      return executionPointer.pointerWithOffset(4)
    }
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
    /**
     * @type {Array<number>}
     */
    this.inputStream = []
    /**
     * @type {Array<number>}
     */
    this.outputStream = []
    /**
     * @type {Pointer}
     */
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
    const opcode = instructionCode % 100
    const instruction = instructions[opcode]
    if (instruction) {
      return instruction(executionPointer, this.inputStream, this.outputStream)
    }
    throw new Error(`illegal opcode: ${opcode}`)
  }
}

// stackoverflow
function perm (xs) {
  const ret = []

  for (let i = 0; i < xs.length; i = i + 1) {
    const rest = perm(xs.slice(0, i).concat(xs.slice(i + 1)))

    if (!rest.length) {
      ret.push([xs[i]])
    } else {
      for (let j = 0; j < rest.length; j = j + 1) {
        ret.push([xs[i]].concat(rest[j]))
      }
    }
  }
  return ret
}

// part 1
const phaseSettings = [0, 1, 2, 3, 4]
const maxPhase = perm(phaseSettings).reduce((maxPhase, phaseSettings) => {
  // amp a
  const programA = new Program(programMemory)
  programA.inputStream.push(phaseSettings.shift(), 0)
  programA.run()
  // amp b
  const programB = new Program(programMemory)
  programB.inputStream.push(phaseSettings.shift(), programA.outputStream.shift())
  programB.run()
  // amp c
  const programC = new Program(programMemory)
  programC.inputStream.push(phaseSettings.shift(), programB.outputStream.shift())
  programC.run()
  // amp d
  const programD = new Program(programMemory)
  programD.inputStream.push(phaseSettings.shift(), programC.outputStream.shift())
  programD.run()
  // amp e
  const programE = new Program(programMemory)
  programE.inputStream.push(phaseSettings.shift(), programD.outputStream.shift())
  programE.run()

  const newPhase = programE.outputStream.shift()
  return newPhase > maxPhase ? newPhase : maxPhase
}, 0)

console.log(maxPhase)
```
## 2
```javascript
const programMemory = require('./day7input')
const debug = false

class AsyncStream {
  constructor () {
    /**
     * @type {number[]}
     */
    this.queue = []
    /**
     * @type {Array<function(number):void>}
     */
    this.readResolves = []
  }

  /**
   * @return {Promise<number>}
   */
  async read () {
    const value = this.queue.shift()
    if (value == null) {
      return new Promise(resolve => { this.readResolves.push(resolve) })
    } else {
      return Promise.resolve(value)
    }
  }

  /**
   * @param {number}value
   */
  write (value) {
    if (value == null) {
      throw new Error('cant write empty value')
    }

    const resolve = this.readResolves.shift()
    if (resolve) {
      resolve(value)
    } else {
      this.queue.push(value)
    }
  }
}

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

  toString () {
    return `${this.dereference()}@${this.address}`
  }
}

const instructionMode = (instructionCode, pos) => (((instructionCode / (10 * 10 ** pos))) >> 0) % 10

const parseInstructionArgs = (executionPointer) => {
  const instructionCode = executionPointer.dereference()
  const icFirst = instructionMode(instructionCode, 1)
  const icSecond = instructionMode(instructionCode, 2)

  const arg0 = executionPointer.dereference(1)
  const arg1 = executionPointer.dereference(2)
  const resultAddress = executionPointer.dereference(3)
  const arg0Value = icFirst ? arg0 : new Pointer(executionPointer.memory, arg0).dereference()
  const arg1Value = icSecond ? arg1 : new Pointer(executionPointer.memory, arg1).dereference()
  const resultPointer = new Pointer(executionPointer.memory, resultAddress)

  debug && console.log(`Load args: ${arg0Value}, ${arg1Value}`)
  return { arg0Value, arg1Value, resultPointer }
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
   * sums
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  1: (executionPointer) => {
    // load args
    const { arg0Value, arg1Value, resultPointer } = parseInstructionArgs(executionPointer)
    return () => {
      // execute and write result
      resultPointer.write(arg0Value + arg1Value)
      debug && console.log(`+ -> ${resultPointer.toString()}`)
      // return next instruction location
      return executionPointer.pointerWithOffset(4)
    }
  },
  /**
   * multiply
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  2: (executionPointer) => {
    // load args
    const { arg0Value, arg1Value, resultPointer } = parseInstructionArgs(executionPointer)
    return () => {
      // execute and write result
      resultPointer.write(arg0Value * arg1Value)
      debug && console.log(`* -> ${resultPointer.toString()}`)
      // return next instruction location
      return executionPointer.pointerWithOffset(4)
    }
  },
  /**
   * read input
   * @param {Pointer}executionPointer
   * @param {AsyncStream}inputStream
   * @return {function(): Pointer}
   */
  3: (executionPointer, inputStream) => {
    const arg0Address = executionPointer.dereference(1)
    const resultPointer = new Pointer(executionPointer.memory, arg0Address)

    return async () => {
      const input = await inputStream.read()
      if (input == null) {
        throw new Error('Input empty')
      }
      resultPointer.write(input)
      debug && console.log(`in -> ${resultPointer.toString()}`)
      // return next instruction location
      return executionPointer.pointerWithOffset(2)
    }
  },
  /**
   * write output
   * @param {Pointer}executionPointer
   * @param {AsyncStream}inputStream
   * @param {AsyncStream}outputStream
   * @return {function(): Pointer}
   */
  4: (executionPointer, inputStream, outputStream) => {
    const arg0Address = executionPointer.dereference(1)
    const outValue = new Pointer(executionPointer.memory, arg0Address).dereference()

    return () => {
      outputStream.write(outValue)
      debug && console.log(`out -> ${outValue}`)
      // return next instruction location
      return executionPointer.pointerWithOffset(2)
    }
  },
  /**
   * jump if true
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  5: (executionPointer) => {
    const { arg0Value, arg1Value } = parseInstructionArgs(executionPointer)

    return () => {
      if (arg0Value) {
        debug && console.log(`jmp -> ${arg1Value}`)
        return new Pointer(executionPointer.memory, arg1Value)
      } else {
        debug && console.log('jmp -> +3')
        return executionPointer.pointerWithOffset(3)
      }
    }
  },
  /**
   * jump if false
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  6: (executionPointer) => {
    const { arg0Value, arg1Value } = parseInstructionArgs(executionPointer)

    return () => {
      if (arg0Value) {
        debug && console.log('jmp -> +3')
        return executionPointer.pointerWithOffset(3)
      } else {
        debug && console.log(`jmp -> ${arg1Value}`)
        return new Pointer(executionPointer.memory, arg1Value)
      }
    }
  },
  /**
   * less than
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  7: (executionPointer) => {
    const { arg0Value, arg1Value, resultPointer } = parseInstructionArgs(executionPointer)

    return () => {
      arg0Value < arg1Value ? resultPointer.write(1) : resultPointer.write(0)
      debug && console.log(`less than -> ${resultPointer.toString()}`)
      return executionPointer.pointerWithOffset(4)
    }
  },
  /**
   * equals
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  8: (executionPointer) => {
    const { arg0Value, arg1Value, resultPointer } = parseInstructionArgs(executionPointer)

    return () => {
      arg0Value === arg1Value ? resultPointer.write(1) : resultPointer.write(0)
      debug && console.log(`equals -> ${resultPointer.toString()}`)
      return executionPointer.pointerWithOffset(4)
    }
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
   * @param id
   */
  constructor (programMemory, id = '') {
    /**
     * @type {AsyncStream}
     */
    this.inputStream = new AsyncStream()
    /**
     * @type {AsyncStream}
     */
    this.outputStream = new AsyncStream()
    /**
     * @type {Pointer}
     */
    this.nextInstructionPointer = new Pointer(programMemory, 0)
    this.id = id
  }

  async run () {
    while (this.nextInstructionPointer) {
      await this.step()
    }
  }

  // in case you want to add debugging :o)
  async step () {
    debug && console.log(`-----${this.id}-----`)
    const nextInstruction = this.loadNextInstruction(this.nextInstructionPointer)
    this.nextInstructionPointer = await nextInstruction()
  }

  /**
   * @param {Pointer}executionPointer
   * @return {function(): Pointer}
   */
  loadNextInstruction (executionPointer) {
    debug && console.log(`nip -> ${executionPointer.toString()}`)
    const instructionCode = executionPointer.dereference()
    const opcode = instructionCode % 100
    const instruction = instructions[opcode]
    if (instruction) {
      return instruction(executionPointer, this.inputStream, this.outputStream)
    }
    throw new Error(`illegal opcode: ${opcode}`)
  }
}

// stackoverflow
function perm (xs) {
  const ret = []

  for (let i = 0; i < xs.length; i = i + 1) {
    const rest = perm(xs.slice(0, i).concat(xs.slice(i + 1)))

    if (!rest.length) {
      ret.push([xs[i]])
    } else {
      for (let j = 0; j < rest.length; j = j + 1) {
        ret.push([xs[i]].concat(rest[j]))
      }
    }
  }
  return ret
}

// part 2
const phaseSettingsSource = perm([5, 6, 7, 8, 9])

let maxPhase = 0

const calculateValue = async () => {
  for (const phaseSettings of phaseSettingsSource) {
    const programA = new Program(programMemory.slice(), 'AMP-A')
    const programB = new Program(programMemory.slice(), 'AMP-B')
    const programC = new Program(programMemory.slice(), 'AMP-C')
    const programD = new Program(programMemory.slice(), 'AMP-D')
    const programE = new Program(programMemory.slice(), 'AMP-E')

    let finished = false

    programA.run().then(() => {
      finished = true
    })
    programB.run().then(() => {
      finished = true
    })
    programC.run().then(() => {
      finished = true
    })
    programD.run().then(() => {
      finished = true
    })
    programE.run().then(() => {
      finished = true
    })

    programA.inputStream.write(phaseSettings[0])
    programB.inputStream.write(phaseSettings[1])
    programC.inputStream.write(phaseSettings[2])
    programD.inputStream.write(phaseSettings[3])
    programE.inputStream.write(phaseSettings[4])

    let startInput = 0

    do {
      programA.inputStream.write(startInput)
      programB.inputStream.write(await programA.outputStream.read())
      programC.inputStream.write(await programB.outputStream.read())
      programD.inputStream.write(await programC.outputStream.read())
      programE.inputStream.write(await programD.outputStream.read())
      startInput = await programE.outputStream.read()
    } while (!finished)

    maxPhase = startInput > maxPhase ? startInput : maxPhase
  }
}

calculateValue().then(() => console.log(maxPhase))

```
