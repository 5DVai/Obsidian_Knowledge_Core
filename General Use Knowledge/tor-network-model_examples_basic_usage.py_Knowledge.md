# Tor Network Model: Basic Usage Example
**Source:** tor-network-model\examples\basic_usage.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
This script serves as a foundational example for utilizing the Tor Network Model toolkit, demonstrating a comprehensive workflow for simulating and analyzing the Tor network. It covers the generation of a synthetic Tor network, the creation of an adversary model, the building of circuits with realistic constraints, and the execution of a Monte Carlo simulation to evaluate network security. The script concludes with a visualization of the results, providing insights into the effectiveness of the adversary's attack.

The value of this script lies in its demonstration of key concepts in network security simulation, particularly within the context of the Tor network. It provides a reusable framework for setting up and running simulations, making it a valuable resource for researchers and engineers interested in network security and privacy.

## Key Concepts & Principles
- **Synthetic Network Generation:** Creating a simulated Tor network with specified numbers of guard, middle, and exit nodes.
- **Adversary Modeling:** Simulating an adversary with a defined number of compromised nodes to assess network vulnerability.
- **Circuit Building:** Constructing circuits with constraints to mimic realistic Tor usage scenarios.
- **Monte Carlo Simulation:** Running extensive simulations to statistically analyze network security.
- **Visualization:** Using data visualization to compare theoretical and observed attack success rates.

## Detailed Technical Analysis

### Synthetic Network Generation
The script begins by generating a synthetic Tor network using the `Network.generate_synthetic` method. This involves specifying the number of guard, middle, and exit nodes, which are crucial components of the Tor network's architecture. The use of a seed ensures reproducibility of the network configuration.

### Adversary Modeling
An adversary is modeled using the `RandomAdversary` class, which simulates the compromise of a subset of network nodes. The script calculates the compromise ratio and compares it to a theoretical value, providing a baseline for evaluating the adversary's effectiveness.

### Circuit Building
The `CircuitBuilder` class is used to construct circuits with specific constraints, such as requiring nodes from different countries and using persistent guards. This step is critical for simulating realistic Tor network behavior and assessing the impact of various constraints on security.

### Monte Carlo Simulation
The core of the script is the Monte Carlo simulation, executed by the `MonteCarloSimulator` class. This involves simulating a large number of circuits and clients to statistically evaluate the network's security against the modeled adversary. The results include the total number of circuits simulated, the number of compromised circuits, and the observed compromise rate.

### Visualization
The script concludes with a visualization step, using Plotly to create a bar chart comparing theoretical and observed compromise rates. This visual representation aids in understanding the effectiveness of the adversary's attack and the robustness of the network.

## Enterprise Q&A Bank

1. **Q:** What is the purpose of generating a synthetic Tor network in this script?
   **A:** To create a controlled environment for simulating and analyzing network security scenarios.

2. **Q:** How does the script ensure reproducibility in network generation and adversary modeling?
   **A:** By using a fixed seed value for random number generation.

3. **Q:** What role does the `CircuitBuilder` play in the simulation?
   **A:** It constructs circuits with specific constraints to mimic realistic Tor usage scenarios.

4. **Q:** Why is a Monte Carlo simulation used in this context?
   **A:** To statistically analyze the network's security by simulating a large number of circuits and clients.

5. **Q:** How does the visualization component enhance the script's utility?
   **A:** It provides a clear comparison between theoretical and observed attack success rates, aiding in the interpretation of results.

## Actionable Takeaways
- Ensure reproducibility in simulations by using fixed seed values.
- Model adversaries realistically to assess network vulnerabilities accurately.
- Use circuit constraints to simulate realistic network behavior.
- Leverage Monte Carlo simulations for statistical analysis of network security.
- Incorporate data visualization to enhance the interpretation of simulation results.

---
**Raw Content:**
```python
#!/usr/bin/env python3
"""
Basic usage example for the Tor Network Model toolkit.

This script demonstrates the fundamental workflow:
1. Generate a synthetic Tor network
2. Create an adversary model
3. Build circuits with realistic constraints
4. Run Monte Carlo simulation
5. Visualize results
"""

import sys
import os
sys.path.append('..')

from tor_sim import (
    Network, RandomAdversary, CircuitBuilder, CircuitConstraints,
    MonteCarloSimulator, SimulationConfig, Visualizer
)

def main():
    print("Tor Network Model - Basic Example")
    print("=" * 40)
    
    # Step 1: Generate a synthetic network
    print("\n1. Generating synthetic Tor network...")
    network = Network.generate_synthetic(
        num_guards=100,
        num_middles=1000,
        num_exits=200,
        seed=42
    )
    print(f"   Created network with {len(network)} nodes")
    print(f"   Guards: {len(network.guard_nodes)}")
    print(f"   Middles: {len(network.middle_nodes)}")
    print(f"   Exits: {len(network.exit_nodes)}")
    
    # Step 2: Create an adversary
    print("\n2. Creating random adversary...")
    adversary = RandomAdversary(num_compromised=50, seed=42)
    compromised = adversary.compromised_nodes(network)
    compromise_ratio = len(compromised) / len(network)
    print(f"   Adversary controls {len(compromised)} nodes ({compromise_ratio:.3f})")
    print(f"   Theoretical F = (m/N)²: {compromise_ratio**2:.4f}")
    
    # Step 3: Create circuit builder
    print("\n3. Setting up circuit builder...")
    constraints = CircuitConstraints(
        require_different_countries=True,
        use_persistent_guards=True,
        num_guard_nodes=3
    )
    builder = CircuitBuilder(constraints=constraints, seed=42)
    
    # Step 4: Run simulation
    print("\n4. Running Monte Carlo simulation...")
    config = SimulationConfig(
        num_circuits=10000,
        num_clients=100,
        circuits_per_client=100,
        verbose=False
    )
    
    simulator = MonteCarloSimulator(
        network=network,
        adversary=adversary,
        circuit_builder=builder,
        config=config
    )
    
    result = simulator.run()
    
    # Step 5: Display results
    print("\n5. Results:")
    print(f"   Total circuits simulated: {result.total_circuits}")
    print(f"   Compromised circuits: {result.compromised_circuits}")
    print(f"   Compromise rate: {result.compromise_rate:.4f}")
    print(f"   Expected (theoretical): {compromise_ratio**2:.4f}")
    print(f"   Simulation time: {result.simulation_time:.2f} seconds")
    
    # Step 6: Basic visualization
    print("\n6. Creating visualization...")
    try:
        visualizer = Visualizer()
        
        # Create a simple bar chart comparing theoretical vs observed
        import plotly.graph_objects as go
        
        fig = go.Figure()
        fig.add_trace(go.Bar(
            x=['Theoretical', 'Observed'],
            y=[compromise_ratio**2, result.compromise_rate],
            marker_color=['lightblue', 'lightcoral']
        ))
        
        fig.update_layout(
            title='Attack Success Rate: Theoretical vs Observed',
            yaxis_title='Compromise Rate',
            height=400
        )
        
        # Save plot
        fig.write_html('basic_example_results.html')
        print("   Visualization saved to 'basic_example_results.html'")
        
    except Exception as e:
        print(f"   Visualization failed: {e}")
    
    print("\nExample completed successfully!")
    print("For more advanced examples, see the Jupyter notebooks in the notebooks/ directory.")

if __name__ == '__main__':
    main()
```