# Q-MUON

Q-MUON proposes a quantum implementation behind MUON’s reasoning-unit interface. A caller would request inference, inspect state and manage the unit’s lifecycle through that interface. [Q-HENA](https://github.com/getHarshOnline/Q-HENA/blob/main/docs/index.md) would coordinate multiple units. The current Rust scaffold re-exports MONAD and has no callable unit API.

::::card-group

:::card

[Read the state-overlap example](https://causality.in/q-muon/the-quantum-local-unit/#a-local-unit-on-a-quantum-substrate)

Follow two sensor readings through a proposed quantum similarity calculation.

:::

:::card

[Read the shared unit contract](https://github.com/getHarshOnline/MUON/blob/main/docs/index.md#a-unit-is-anything-that-fills-the-slot)

See the responsibilities a quantum implementation would inherit.

:::

:::card

[Build the kernel crate and the paper](building.md)

Check the kernel against its pinned MONAD dependency, or typeset the paper on its own.

:::

::::

## Adapt the unit behind the shared contract

MUON’s draft interface gives every implementation five responsibilities:

| Responsibility | What a quantum implementation would provide |
| --- | --- |
| Inference | Prepare the input, run the chosen computation and return its measured response |
| State | Declare what can be inspected or saved, and at what fidelity |
| Ordered records | Identify inference, resource use, measurement procedure and shot count |
| Resources | Estimate the complete operation and check permission and reserved capacity before running it |
| Lifecycle | Initialize, step, checkpoint, restore and stop according to its compatibility profile |

The caller can work with a measurement summary without managing the backend. To resume an interrupted computation, it needs a separate promise about which state the unit can recover.

The fork would start from a fixed MUON revision before adding its quantum adaptation. A test of the interface checks whether callers can use the unit. A separate task comparison measures accuracy and resource use against a relevant classical unit at the same error tolerance. Preparing inputs, reading results and recovering interrupted work all contribute to that comparison.

## A checkpoint needs a backend-specific promise

A compatibility profile would identify the classical contract and adaptation revisions, state and checkpoint formats, backend, topology, calibration or noise model, circuit compilation, measurement procedure, seed, shots and replay class. A restore must check those properties before using a saved checkpoint.

The proposed modes are `exact`, `semantic` and `restart_only`. They distinguish restoring state, recovering equivalent behavior under specified observations, and starting again. Their schema, tolerances and tests have not been defined.

Each admitted unit must declare exactly one mode in its proposed `QuantumUnitCompatibilityProfileV1`; recovery cannot promise more than that mode supports. Provider drift, expired calibration, changed topology, incompatible circuit or state representation, and an unverifiable checkpoint must all refuse recovery. A classical checkpoint is never presented as restorable by a quantum unit.

Q-HENA would bind each unit's reservation, swarm checkpoint and adaptation evidence to the exact profile digest and restore mode. Its collective recovery must respect every participating unit's guarantee, including any repeated preparation or measurement a restart requires.

Consider a future unit using backend A, topology T1, calibration C7 and 4,096 measurement shots. It can save its circuit parameters and classical configuration, and it declares `restart_only`. After interruption, those inputs would let it repeat the computation; they would not restore its interrupted quantum state. Q-HENA would have to budget for that restart.

If the backend now reports calibration C8, the old profile no longer matches. Reusing the circuit parameters would not establish that the recovery promise still holds. The replacement needs a compatible profile before the swarm can rely on it.

[State and recovery in the product guide](https://causality.in/q-muon/the-quantum-local-unit/#state-and-recovery-need-their-own-meaning) explains the consequence for the collective task.

## Inspect the current source

Follow the [build guide](building.md#check-the-kernel-crate) for the tools and online or offline kernel check. A successful check resolves the [kernel manifest](../kernel/Cargo.toml) dependency on MONAD's kernel at its pinned commit and type-checks the [crate root](../kernel/src/lib.rs) against it. The crate re-exports MONAD, and no quantum directories exist yet. A unit implementation and tests of inference, circuits and recovery are future work.

The [paper](../paper/Q-MUON.tex) imports inherited notation and [Q-MUON’s definitions](../paper/shared/q-muon/definitions.tex). It contains an abstract. The [inherited protocol header](../protos/muon/unit.proto) declares no unit messages.

The [build instructions](building.md) cover the kernel and paper checks. For terms and notices, see [Licence](../README.md#licence).
