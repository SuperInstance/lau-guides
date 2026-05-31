# lau-guides: Plug-and-Play Integration Guide

> Real-world patterns for combining lau-* math and physics crates.
> Copy. Paste. Ship.

## Quick Start: "I want to..."

| Goal | Crates | One-liner |
|------|--------|-----------|
| Analyze data topologically | `lau-topological-data-analysis` + `lau-persistent-homology` | Find the shape of your data, not just statistics |
| Build a learning agent | `lau-reinforcement-learning` + `lau-statistical-learning` | Q-learning with PAC guarantees |
| Monitor agent health | `lau-time-series` + `lau-signal-processing` + `lau-control-theory` | PID-controlled telemetry streams |
| Find equilibria in multi-agent | `lau-game-theory` + `lau-optimization` | Nash equilibria via BFGS |
| Model populations as flows | `lau-fluid-dynamics` + `lau-stochastic-processes` | Agents as Navier-Stokes with Brownian noise |
| Secure communication | `lau-cryptography` + `lau-information-theory` | HMAC + mutual information audit |
| Schedule tasks | `lau-queueing-theory` + `lau-convex-optimization` | Erlang C + LP for capacity planning |
| Analyze social networks | `lau-graph-theory` + `lau-network-science` | PageRank + community detection + SIR spreading |
| Find optimal trajectories | `lau-variational-methods` + `lau-optimal-control` | Euler-Lagrange + LQR |
| Detect chaos in behavior | `lau-chaos-theory` + `lau-dynamical-systems` | Lyapunov exponents + bifurcation diagrams |
| Reason about contracts | `lau-logic-foundations` + `lau-category-theory` | DPLL SAT + functor composition |
| Quantum-inspired models | `lau-quantum-mechanics` + `lau-tensor-analysis` | Superposition states on curved manifolds |

## Integration Patterns

### Pattern A: Topological Quality Assurance

Detect regime changes in data using persistent homology:

```rust
use lau_topological_data_analysis::complex::VietorisRips;
use lau_persistent_homology::persistence::PersistenceDiagram;

fn detect_regime_change(data: &[Vec<f64>], threshold: f64) -> bool {
    let vr = VietorisRips::new(data, threshold);
    let diagram = PersistenceDiagram::from_complex(&vr);
    
    // A significant H1 feature = loop = cyclic behavior = regime change
    let long_bars: Vec<_> = diagram.h1_features()
        .iter()
        .filter(|f| f.death - f.birth > 0.5)
        .collect();
    
    !long_bars.is_empty()
}
```

### Pattern B: RL Agent with Safety Constraints

Reinforcement learning bounded by control theory:

```rust
use lau_reinforcement_learning::q_learning::QLearner;
use lau_control_theory::pid::PidController;
use lau_convex_optimization::duality::KktSolver;

// Train agent with Q-learning
let mut agent = QLearner::new(state_size, action_size)
    .with_learning_rate(0.1)
    .with_discount(0.95)
    .with_epsilon(0.1);

// PID controller keeps agent behavior in bounds
let mut pid = PidController::new(1.0, 0.1, 0.05)
    .with_output_limits(-1.0, 1.0);

// KKT verifies constraints are satisfied
let kkt = KktSolver::new();
```

### Pattern C: Network-Aware Task Scheduling

Graph topology meets queueing theory:

```rust
use lau_graph_theory::shortest_paths::Dijkstra;
use lau_queueing_theory::mm1::MM1Queue;
use lau_game_theory::nash_equilibrium::NashSolver;

// Build network topology
let network = build_agent_network();

// Find shortest paths for task routing
let paths = Dijkstra::shortest_paths(&network, source);

// Model each agent as an M/M/1 queue
let queue = MM1Queue::new(arrival_rate, service_rate);

// Verify: mean wait time within SLA
assert!(queue.mean_wait_time() < sla_threshold);
```

### Pattern D: Physics-Informed Agent Dynamics

PDE solver + optimization for agent field models:

```rust
use lau_numerical_pde::heat::HeatSolver;
use lau_optimization::gradient_descent::GradientDescent;
use lau_dynamical_systems::continuous::RK4Integrator;

// Diffuse agent information through a field
let mut heat = HeatSolver::new_1d(100, 0.01);
heat.set_initial_condition(agent_density);
heat.solve_crank_nicolson(100);

// Optimize agent placement
let optimizer = GradientDescent::new(0.01)
    .with_momentum(0.9);
let optimal = optimizer.minimize(agent_cost_function);
```

### Pattern E: Cryptographic Agent Authentication

```rust
use lau_cryptography::rsa::RsaKeyPair;
use lau_cryptography::hmac::HmacSha256;
use lau_information_theory::mutual_info::MutualInformation;

// Generate agent identity
let keypair = RsaKeyPair::generate(2048);
let agent_id = keypair.public_key().fingerprint();

// Sign messages
let msg = b"agent heartbeat";
let signature = keypair.sign(msg);

// Verify + measure information content
let hmac = HmacSha256::new(secret_key);
assert!(hmac.verify(msg, &tag));
```

## Cargo Workspace Setup

```toml
[workspace]
members = ["my-agent"]

# In my-agent/Cargo.toml:
[dependencies]
lau-reinforcement-learning = "0.1"
lau-graph-theory = "0.1"
lau-time-series = "0.1"
lau-cryptography = "0.1"
# ... add what you need

# Optional: feature-gated heavy deps
[features]
physics = ["lau-fluid-dynamics", "lau-quantum-mechanics", "lau-electromagnetism"]
ml = ["lau-statistical-learning", "lau-reinforcement-learning", "lau-evolutionary-computation"]
crypto = ["lau-cryptography", "lau-number-theory", "lau-information-theory"]
```

## Testing Your Integration

Every lau-* crate follows the same pattern:

```rust
#[cfg(test)]
mod tests {
    #[test]
    fn crate_integration_works() {
        // 1. Create the main type
        // 2. Feed it data
        // 3. Assert properties
        // 4. Verify roundtrips
    }
}
```

Quick smoke test for any crate:

```bash
cargo add lau-WHATEVER
cargo test  # if it compiles, you're good
```

## Performance Tips

- **Parallelism:** `lau-network-science`, `lau-graph-theory` use `rayon` — just `RAYON_NUM_THREADS=N cargo run`
- **Sparse vs Dense:** `lau-matrix-analysis` and `lau-numerical-linear-algebra` support both — use sparse when >90% zeros
- **Memory:** Wave 19 physics crates (fluid dynamics, quantum mechanics) can be memory-heavy at large grid sizes — start small
- **Faster builds:** `cargo build -j$(nproc)` — all crates are independent, so parallel builds fly
- **Hot loops:** `lau-signal-processing` and `lau-optimization` are designed for inner-loop use — no allocations in hot paths

## Architecture: How It All Fits Together

```
┌─────────────────────────────────────────────┐
│                YOUR PROJECT                  │
├─────────────────────────────────────────────┤
│  Agent Layer: RL, Game Theory, Fuzzy Logic  │
├─────────────────────────────────────────────┤
│  Analysis: TDA, Time Series, Info Theory     │
├─────────────────────────────────────────────┤
│  Math Core: Linear Algebra, Optimization,    │
│             Category Theory, Logic           │
├─────────────────────────────────────────────┤
│  Physics: Fluid Dynamics, QM, EM, Thermo    │
├─────────────────────────────────────────────┤
│  Infrastructure: Crypto, Graph, Queueing     │
└─────────────────────────────────────────────┘
```

Pick any layer. Combine any crates. They're designed to compose.

## Contributing

Found a bug? Want a new integration pattern? [Open an issue](https://github.com/SuperInstance/lau-guides/issues).

Built something cool with lau-* crates? PR your pattern into this guide.

## License

MIT — use it however you want.

🦀 *The hermit crab carries its home on its back. These crates carry math in theirs.*
