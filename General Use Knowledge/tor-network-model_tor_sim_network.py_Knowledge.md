# Tor Network Topology Representation
**Source:** tor-network-model\tor_sim\network.py  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `network.py` module provides a comprehensive representation of the Tor network topology, focusing on the modeling of nodes and their interactions within the network. This module is crucial for simulating and analyzing the Tor network, which is a decentralized network designed to enhance privacy and security for its users. The code defines the structure and behavior of Tor nodes, including their types, flags, and network properties. It also includes methods for generating synthetic networks, loading from consensus files, and exporting/importing network configurations in JSON format.

This module is valuable for its detailed encapsulation of Tor network concepts, making it a reusable asset for simulations and analyses of network behaviors. It provides a robust framework for understanding the dynamics of Tor nodes, their roles, and their interactions, which are essential for developing privacy-preserving technologies and conducting network research.

## Key Concepts & Principles
- **NodeType:** Enumeration of Tor node types (Guard, Middle, Exit).
- **NodeFlag:** Enumeration of various flags that describe node capabilities and status.
- **Node Class:** Represents a Tor relay node with properties like bandwidth, geographic information, and flags.
- **Network Class:** Manages a collection of nodes, providing methods for adding, removing, and querying nodes based on various criteria.

## Detailed Technical Analysis

### Node Representation
The `Node` class is a dataclass that encapsulates the properties of a Tor relay node. It includes attributes for network properties such as bandwidth and geographic information, as well as methods to determine the node's role (guard, middle, exit) based on its flags.

#### Properties and Methods
- **is_guard, is_exit, is_middle:** Determine the node's role based on its flags.
- **effective_bandwidth:** Calculates the effective bandwidth for path selection, considering observed and advertised bandwidths.

### Network Management
The `Network` class manages the collection of nodes and provides methods for network analysis and manipulation. It uses the NetworkX library to maintain a graph representation of the network.

#### Key Methods
- **add_node, remove_node:** Manage the addition and removal of nodes in the network.
- **get_nodes_by_type, get_nodes_by_country, get_nodes_by_as:** Query nodes based on type, country, or Autonomous System (AS).
- **generate_synthetic:** Creates a synthetic network for testing purposes, allowing for controlled experiments and simulations.

### Data Persistence
The module includes methods for exporting and importing network configurations in JSON format, facilitating data persistence and sharing.

## Enterprise Q&A Bank

1. **Q:** How does the module determine if a node can be used as a guard?
   **A:** A node is considered a guard if it has the `GUARD` and `RUNNING` flags set.

2. **Q:** What is the purpose of the `effective_bandwidth` property?
   **A:** It calculates the effective bandwidth for path selection, using the minimum of observed and advertised bandwidths if observed bandwidth is available.

3. **Q:** How can a synthetic network be generated using this module?
   **A:** By calling the `generate_synthetic` method with parameters for the number of guard, middle, and exit nodes, and optionally specifying countries and a random seed.

4. **Q:** What library is used for graph representation in the `Network` class?
   **A:** The NetworkX library is used for graph representation.

5. **Q:** How does the module handle node removal?
   **A:** The `remove_node` method deletes the node from the internal dictionary and graph, and clears cached node lists.

## Actionable Takeaways
- Use the `Node` class to encapsulate Tor relay node properties and behaviors.
- Leverage the `Network` class for managing and analyzing collections of nodes.
- Utilize the `generate_synthetic` method for creating test networks.
- Export and import network configurations using the JSON methods for data persistence.
- Apply the `get_nodes_by_*` methods for querying nodes based on specific criteria.

---
**Raw Content:**
```python
# (The raw content of the file is provided here for reference)
```