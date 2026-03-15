## Design Notes

This folder contains several versions of the system developed throughout the project.

Initially, the transmitter and receiver were implemented as separate GNU Radio flowgraphs. These components were later integrated into a single system.

Early versions required external input files to be provided through **File Source** blocks. As the design evolved, this approach was replaced with a **PyQt-based GUI**, implemented using the GNU Radio Embedded Python Block, allowing the system to run interactively without manually loading files.

The final version contains a single flowgraph that integrates both the transmitter and receiver, including the acknowledgement (ACK) paths required for communication feedback.
