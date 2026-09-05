# Math

The Math module provides extended mathematical functions beyond the standard JavaScript Math object.

## Functions

### `math.acosh(x)`

Calculates the inverse hyperbolic cosine (arccosine hyperbolic) of a number.

**Parameters:**

| Parameter | Type     | Description                            |
| --------- | -------- | -------------------------------------- |
| `x`       | `number` | A number greater than or equal to 1    |

**Returns:** `number` - The inverse hyperbolic cosine of x in radians

**Formula:** `acosh(x) = ln(x + √(x² - 1))`

**Example:**

```javascript linenums="1"
var math = require("math");

var result = math.acosh(2);
console.log("acosh(2) =", result); // ≈ 1.3170

// For x = 1 (minimum valid input)
console.log("acosh(1) =", math.acosh(1)); // = 0

// Invalid input (x < 1) will return NaN
console.log("acosh(0.5) =", math.acosh(0.5)); // NaN
```

### `math.asinh(x)`

Calculates the inverse hyperbolic sine (arcsine hyperbolic) of a number.

**Parameters:**

| Parameter | Type     | Description     |
| --------- | -------- | --------------- |
| `x`       | `number` | Any real number |

**Returns:** `number` - The inverse hyperbolic sine of x in radians

**Formula:** `asinh(x) = ln(x + √(x² + 1))`

**Example:**

```javascript linenums="1"
var math = require("math");

var result = math.asinh(1);
console.log("asinh(1) =", result); // ≈ 0.8814

// Works with negative numbers
console.log("asinh(-1) =", math.asinh(-1)); // ≈ -0.8814

// Zero input
console.log("asinh(0) =", math.asinh(0)); // = 0
```

### `math.atanh(x)`

Calculates the inverse hyperbolic tangent (arctangent hyperbolic) of a number.

**Parameters:**

| Parameter | Type     | Description                              |
| --------- | -------- | ---------------------------------------- |
| `x`       | `number` | A number between -1 and 1 (exclusive)    |

**Returns:** `number` - The inverse hyperbolic tangent of x in radians

**Formula:** `atanh(x) = 0.5 * ln((1 + x) / (1 - x))`

**Example:**

```javascript linenums="1"
var math = require("math");

var result = math.atanh(0.5);
console.log("atanh(0.5) =", result); // ≈ 0.5493

// For x = 0
console.log("atanh(0) =", math.atanh(0)); // = 0

// Invalid input (|x| >= 1) will return infinity or NaN
console.log("atanh(1) =", math.atanh(1)); // Infinity
console.log("atanh(-1) =", math.atanh(-1)); // -Infinity
console.log("atanh(2) =", math.atanh(2)); // NaN
```

### `math.fequal(a, b, epsilon)`

Compares two floating-point numbers for equality within a specified tolerance.

**Parameters:**

| Parameter | Type     | Description                                      |
| --------- | -------- | ------------------------------------------------ |
| `a`       | `number` | First number to compare                          |
| `b`       | `number` | Second number to compare                         |
| `epsilon` | `number` | Tolerance for equality (default: typically 1e-9) |

**Returns:** `boolean` - True if |a - b| < epsilon

**Example:**

```javascript linenums="1"
var math = require("math");

// Standard comparison might fail due to floating-point precision
var a = 0.1 + 0.2;
var b = 0.3;
console.log("0.1 + 0.2 === 0.3:", a === b); // false

// Using fequal with tolerance
console.log("fequal(0.1 + 0.2, 0.3):", math.fequal(a, b)); // true

// Custom epsilon
console.log("fequal(1.001, 1.002, 0.01):", math.fequal(1.001, 1.002, 0.01)); // true
console.log("fequal(1.001, 1.002, 0.0001):", math.fequal(1.001, 1.002, 0.0001)); // false
```

## Hyperbolic Functions Overview

Hyperbolic functions are analogs of trigonometric functions but for hyperbolas rather than circles.

### Regular Hyperbolic Functions (available in standard Math)

- `Math.sinh(x)` - Hyperbolic sine: `(e^x - e^(-x)) / 2`
- `Math.cosh(x)` - Hyperbolic cosine: `(e^x + e^(-x)) / 2`
- `Math.tanh(x)` - Hyperbolic tangent: `sinh(x) / cosh(x)`

### Inverse Hyperbolic Functions (provided by this module)

- `math.asinh(x)` - Inverse hyperbolic sine
- `math.acosh(x)` - Inverse hyperbolic cosine
- `math.atanh(x)` - Inverse hyperbolic tangent

## Usage Examples

```javascript linenums="1"
// Scientific calculations
function calculateCatenaryLength(a, x1, x2) {
    // Catenary curve length calculation
    return a * (math.asinh(x2 / a) - math.asinh(x1 / a));
}

// Engineering applications
function relativisticVelocity(beta) {
    // beta = v/c (velocity as fraction of light speed)
    // Returns rapidity (hyperbolic angle)
    if (Math.abs(beta) >= 1) {
        throw new Error("Velocity must be less than speed of light");
    }
    return math.atanh(beta);
}

// Numerical analysis helpers
function safeFloatComparison(calculated, expected, tolerance) {
    if (tolerance === undefined) tolerance = 1e-6;
    return math.fequal(calculated, expected, tolerance);
}

// Advanced mathematical functions
function hypergeometricMean(a, b) {
    // One step of the arithmetic-geometric mean iteration
    var arithmetic = (a + b) / 2;
    var geometric = Math.sqrt(a * b);
    return [arithmetic, geometric];
}

function calculateAGM(a, b, iterations) {
    // Arithmetic-Geometric Mean using hyperbolic functions
    if (iterations === undefined) iterations = 10;
    var x = a;
    var y = b;

    for (var i = 0; i < iterations; i++) {
        var nextX = (x + y) / 2;
        var nextY = Math.sqrt(x * y);

        if (math.fequal(x, nextX) && math.fequal(y, nextY)) {
            break; // Converged
        }

        x = nextX;
        y = nextY;
    }

    return x; // AGM converges to same value
}

// Physics calculations - constructor function instead of class
function RelativisticCalculator() {
    this.c = 299792458; // Speed of light in m/s
}

RelativisticCalculator.prototype.rapidityToVelocity = function(rapidity) {
    // Convert rapidity (hyperbolic angle) to velocity
    return this.c * Math.tanh(rapidity);
};

RelativisticCalculator.prototype.velocityToRapidity = function(velocity) {
    // Convert velocity to rapidity
    var beta = velocity / this.c;
    if (Math.abs(beta) >= 1) {
        throw new Error("Velocity must be less than speed of light");
    }
    return math.atanh(beta);
};

RelativisticCalculator.prototype.addVelocities = function(v1, v2) {
    // Relativistic velocity addition using rapidities
    var rap1 = this.velocityToRapidity(v1);
    var rap2 = this.velocityToRapidity(v2);
    var totalRapidity = rap1 + rap2;
    return this.rapidityToVelocity(totalRapidity);
};

RelativisticCalculator.prototype.lorentzFactor = function(velocity) {
    // γ = cosh(rapidity)
    var rapidity = this.velocityToRapidity(velocity);
    return Math.cosh(rapidity);
};

// Signal processing
function generateCatenaryWave(amplitude, frequency, samples) {
    var waveform = [];

    for (var i = 0; i < samples; i++) {
        var x = (i / samples) * 4 * Math.PI; // 4π range
        var t = x - 2 * Math.PI; // Center around 0

        // Catenary function: y = a * cosh(x/a)
        var y = amplitude * Math.cosh(t / amplitude);
        waveform.push(y);
    }

    return waveform;
}

// Numerical methods
class NumericalMethods {
    static solveNewtonRaphson(func, derivative, initialGuess, tolerance = 1e-8, maxIter = 100) {
        var x = initialGuess;

        for (var i = 0; i < maxIter; i++) {
            var fx = func(x);
            var fpx = derivative(x);

            if (Math.abs(fpx) < tolerance) {
                throw new Error("Derivative too small - no convergence");
            }

            var newX = x - fx / fpx;

            if (math.fequal(x, newX, tolerance)) {
                return newX; // Converged
            }

            x = newX;
        }

        throw new Error("No convergence after " + maxIter + " iterations");
    }

    static integrateTrapezoidal(func, a, b, n = 1000) {
        var h = (b - a) / n;
        var sum = 0.5 * (func(a) + func(b));

        for (var i = 1; i < n; i++) {
            sum += func(a + i * h);
        }

        return h * sum;
    }
}

// Practical examples
function practicalMathExamples() {
    console.log("=== Extended Math Functions Demo ===");

    // 1. Cable hang calculation (catenary)
    console.log("\\n1. Cable Hang Calculation:");
    var cableLength = 50; // meters
    var sagParameter = 20; // parameter 'a' in catenary equation
    var span = 40; // horizontal span

    const x1 = -span / 2;
    const x2 = span / 2;
    const actualLength = calculateCatenaryLength(sagParameter, x1, x2);

    console.log(`Cable span: ${span}m`);
    console.log(`Calculated length: ${actualLength.toFixed(2)}m`);
    console.log(`Expected length: ${cableLength}m`);
    console.log(`Match: ${math.fequal(actualLength, cableLength, 1.0)}`);

    // 2. Relativistic velocity calculation
    console.log("\\n2. Relativistic Physics:");
    const calc = new RelativisticCalculator();

    try {
        const v1 = 0.6 * calc.c; // 60% speed of light
        const v2 = 0.7 * calc.c; // 70% speed of light

        const combined = calc.addVelocities(v1, v2);
        const classicalSum = v1 + v2;

        console.log(`v1: ${(v1/calc.c).toFixed(2)}c`);
        console.log(`v2: ${(v2/calc.c).toFixed(2)}c`);
        console.log(`Relativistic sum: ${(combined/calc.c).toFixed(3)}c`);
        console.log(`Classical sum: ${(classicalSum/calc.c).toFixed(2)}c`);
        console.log(`Difference: ${((classicalSum-combined)/calc.c).toFixed(3)}c`);

    } catch (error) {
        console.log("Error:", error.message);
    }

    // 3. Floating point comparison
    console.log("\\n3. Floating Point Precision:");

    const computedPi = 4 * Math.atan(1);
    const libraryPi = Math.PI;

    console.log(`Computed π: ${computedPi}`);
    console.log(`Library π:  ${libraryPi}`);
    console.log(`Exact equal: ${computedPi === libraryPi}`);
    console.log(`Float equal: ${math.fequal(computedPi, libraryPi, 1e-15)}`);

    // 4. Numerical root finding
    console.log("\\n4. Root Finding:");

    try {
        // Solve: x^3 - 2x - 5 = 0
        const func = (x) => x * x * x - 2 * x - 5;
        const derivative = (x) => 3 * x * x - 2;

        const root = NumericalMethods.solveNewtonRaphson(func, derivative, 2.0);
        const verification = func(root);

        console.log(`Root: x = ${root.toFixed(8)}`);
        console.log(`Verification: f(${root.toFixed(4)}) = ${verification.toExponential(2)}`);
        console.log(`Accurate: ${math.fequal(verification, 0, 1e-6)}`);

    } catch (error) {
        console.log("Root finding error:", error.message);
    }
}

// Interactive math calculator
function mathCalculator() {
    while (true) {
        const operation = dialog.choice([
            "Inverse Hyperbolic Functions",
            "Float Comparison",
            "Catenary Calculator",
            "Relativistic Calculator",
            "Exit"
        ]);

        if (operation === 4) break;

        switch (operation) {
            case 0:
                hyperbolicCalculator();
                break;
            case 1:
                floatComparisonDemo();
                break;
            case 2:
                catenaryCalculator();
                break;
            case 3:
                relativisticDemo();
                break;
        }
    }
}

function hyperbolicCalculator() {
    const value = parseFloat(keyboard.numKeyboard("Enter value:", "", 1, 20));
    if (isNaN(value)) return;

    let results = `Hyperbolic Functions for x = ${value}\\n\\n`;

    // Regular hyperbolic functions
    results += `sinh(${value}) = ${Math.sinh(value).toFixed(6)}\\n`;
    results += `cosh(${value}) = ${Math.cosh(value).toFixed(6)}\\n`;
    results += `tanh(${value}) = ${Math.tanh(value).toFixed(6)}\\n\\n`;

    // Inverse hyperbolic functions
    try {
        results += `asinh(${value}) = ${math.asinh(value).toFixed(6)}\\n`;
    } catch (e) {
        results += `asinh(${value}) = Error: ${e.message}\\n`;
    }

    try {
        if (value >= 1) {
            results += `acosh(${value}) = ${math.acosh(value).toFixed(6)}\\n`;
        } else {
            results += `acosh(${value}) = Error: value must be >= 1\\n`;
        }
    } catch (e) {
        results += `acosh(${value}) = Error: ${e.message}\\n`;
    }

    try {
        if (Math.abs(value) < 1) {
            results += `atanh(${value}) = ${math.atanh(value).toFixed(6)}\\n`;
        } else {
            results += `atanh(${value}) = Error: |value| must be < 1\\n`;
        }
    } catch (e) {
        results += `atanh(${value}) = Error: ${e.message}\\n`;
    }

    dialog.viewText(results, "Hyperbolic Functions");
}

function floatComparisonDemo() {
    const a = parseFloat(keyboard.keyboard("First number:", "", 1, 20));
    const b = parseFloat(keyboard.keyboard("Second number:", "", 1, 20));
    const epsilon = parseFloat(keyboard.keyboard("Tolerance (default 1e-9):", "1e-9", 1, 20));

    if (isNaN(a) || isNaN(b) || isNaN(epsilon)) return;

    const exactEqual = (a === b);
    const floatEqual = math.fequal(a, b, epsilon);
    const difference = Math.abs(a - b);

    const results = `Float Comparison Results:

a = ${a}
b = ${b}
epsilon = ${epsilon}

Exact equality (===): ${exactEqual}
Float equality: ${floatEqual}
Absolute difference: ${difference.toExponential(6)}
Within tolerance: ${difference < epsilon}`;

    dialog.viewText(results, "Float Comparison");
}
```

## Mathematical Constants

While not provided directly by this module, here are useful constants for scientific calculations:

```javascript linenums="1"
// Common mathematical constants
var CONSTANTS = {
    E: Math.E,                    // Euler's number ≈ 2.718
    PI: Math.PI,                  // π ≈ 3.14159
    LN2: Math.LN2,               // ln(2) ≈ 0.693
    LN10: Math.LN10,             // ln(10) ≈ 2.303
    LOG2E: Math.LOG2E,           // log₂(e) ≈ 1.443
    LOG10E: Math.LOG10E,         // log₁₀(e) ≈ 0.434
    SQRT1_2: Math.SQRT1_2,       // √(1/2) ≈ 0.707
    SQRT2: Math.SQRT2,           // √2 ≈ 1.414

    // Physical constants
    SPEED_OF_LIGHT: 299792458,    // m/s
    PLANCK: 6.62607015e-34,      // J⋅s
    BOLTZMANN: 1.380649e-23,     // J/K
    AVOGADRO: 6.02214076e23      // mol⁻¹
};
```
