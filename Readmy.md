# Oscillating Rotating Motion with Phase Offset for OpenFOAM v2412

A custom `solidBodyMotionFunction` for OpenFOAM v2412 that extends the standard `oscillatingRotatingMotion` by introducing a **phase offset**. This enables prescribed pitch–plunge motions with arbitrary phase differences while remaining fully compatible with `multiMotion`.

---

## Features

- Compatible with **OpenFOAM v2412**
- Adds a user-defined `phase` parameter
- Supports any phase angle (degrees converted to radians by the user)
- Works with `multiMotion`
- Suitable for:
  - Flapping foils
  - Oscillating hydrofoils
  - Pitch–plunge studies
  - Bio-inspired propulsion
  - Dynamic mesh simulations
  - Overset mesh simulations

---

## Motion Equation

The standard OpenFOAM motion is

\[
\theta(t)=A\sin(\omega t)
\]

This implementation modifies it to

\[
\theta(t)=A\sin(\omega t+\phi)
\]

where

- \(A\) = rotation amplitude (degrees)
- \(\omega\) = angular frequency (rad/s)
- \(\phi\) = phase angle (radians)

Example:

- Phase = 0

\[
\theta=A\sin(\omega t)
\]

- Phase = +90°

\[
\theta=A\cos(\omega t)
\]

which is commonly used for efficient pitch–plunge propulsion.

---

## Installation

Clone or copy the directory into your user source folder

```bash
mkdir -p $WM_PROJECT_USER_DIR/src
cd $WM_PROJECT_USER_DIR/src
```

Compile

```bash
wmake libso
```

The library will be created as

```text
$FOAM_USER_LIBBIN/libOscillatingRotatingMotionPhase.so
```

---

## Load the Library

Add to `system/controlDict`

```cpp
libs
(
    "liboverset.so"
    "libOscillatingRotatingMotionPhase.so"
);
```

---

## Usage

Example inside `dynamicMeshDict`

```cpp
Blade
{
    solidBodyMotionFunction multiMotion;

    multiMotionCoeffs
    {
        linearMotion
        {
            solidBodyMotionFunction oscillatingLinearMotion;

            oscillatingLinearMotionCoeffs
            {
                amplitude   (0 0.2 0);
                omega       6.283185307;
            }
        }

        rotatingMotion
        {
            solidBodyMotionFunction oscillatingRotatingMotionPhase;

            oscillatingRotatingMotionPhaseCoeffs
            {
                origin      (0.18 -0.2 0);

                amplitude   (0 0 -10);

                omega       6.283185307;

                phase       1.57079632679;

                axis        (0 0 1);
            }
        }
    }
}
```

---

## Phase Values

| Phase | Radians | Motion |
|-------:|---------:|--------|
| 0° | 0 | In phase |
| 90° | 1.57079632679 | Pitch leads by 90° |
| -90° | -1.57079632679 | Pitch lags by 90° |
| 180° | 3.14159265359 | Opposite phase |

---

## Important Notes

- The phase value is specified in **radians**.
- The motion uses the same frequency for translation and rotation.
- When used with `multiMotion`, the body first translates and then rotates.
- The rotation center follows the translated body automatically, allowing realistic pitch–plunge motion.

---

## Tested With

- OpenFOAM v2412
- dynamicOversetFvMesh
- multiSolidBodyMotionSolver
- overInterDyMFoam
- Pitch–Plunge Hydrofoil Simulations

---

## Citation

If you use this modification in academic work, please cite OpenFOAM and reference this repository.

---

## License

Distributed under the GNU General Public License (GPL), consistent with OpenFOAM.
