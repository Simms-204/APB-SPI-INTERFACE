 APB-SPI-INTERFACE
Below is a professional README-style description that you can directly use in your GitHub project.



 APB to SPI Slave Interface

Top Module

The `top_module` integrates all the functional blocks required for an APB-to-SPI Slave Interface. It acts as the top-level controller, connecting the APB bus, SPI slave logic, baud rate generator, shift register, and slave-select controller. The module receives configuration data through the APB interface and performs SPI serial communication accordingly.



Top Module Inputs

| Signal      | Width | Description                                                                    |
| ----------- | ----- | ------------------------------------------------------------------------------ |
| `PCLK`      | 1     | APB system clock used to synchronize all modules.                              |
| `PRESET_N`  | 1     | Active-low asynchronous reset for the complete SPI subsystem.                  |
| `PADDR_i`   | 3     | APB address bus used to access SPI configuration and data registers.           |
| `PWRITE_i`  | 1     | APB write control signal. High for write operation and Low for read operation. |
| `PSEL_i`    | 1     | APB peripheral select signal indicating the SPI slave is selected.             |
| `PENABLE_i` | 1     | APB enable signal used during the data transfer phase.                         |
| `PWDATA_i`  | 8     | Data bus carrying write data from the APB master.                              |
| `miso_i`    | 1     | SPI Master-In Slave-Out input carrying serial data from the SPI master.        |



Top Module Outputs

| Signal                    | Width | Description                                                                  |
| ------------------------- | ----- | ---------------------------------------------------------------------------- |
| `mosi_o`                  | 1     | SPI Master-Out Slave-In output transmitting serial data to the SPI master.   |
| `SCLK`                    | 1     | Generated SPI serial clock based on the configured baud rate and SPI mode.   |
| `PRDATA_o`                | 8     | Read data returned to the APB master during read operations.                 |
| `spi_interrupt_request_o` | 1     | Interrupt signal asserted after completion of SPI transmission or reception. |
| `PREADY_o`                | 1     | Indicates that the APB transaction has completed successfully.               |
| `PSLVERR_o`               | 1     | APB slave error signal asserted for invalid accesses or protocol violations. |
| `ss_o`                    | 1     | Slave Select signal used to enable or disable SPI communication.             |



Internal Functional Blocks

1. Baud Rate Generator (`baud_rate_generator`)

Description

The Baud Rate Generator produces the SPI serial clock (`SCLK`) from the APB clock (`PCLK`). It generates different clock edges required for SPI transmission and reception based on the configured baud rate, clock polarity (CPOL), clock phase (CPHA), and SPI operating mode.

Inputs

* APB Clock (`PCLK`)
* Reset (`PRESET_N`)
* Clock Polarity (CPOL)
* Clock Phase (CPHA)
* Baud Rate Prescaler (`SPPR`)
* Baud Rate Divider (`SPR`)
* SPI Mode
* Slave Select
* Stop-in-Wait Control

Outputs

* SPI Clock (`SCLK`)
* Baud Rate Divisor
* MISO Receive Clock
* MISO Receive Clock (Alternate Edge)
* MOSI Send Clock
* MOSI Send Clock (Alternate Edge)



2. SPI Shift Register (`SPI_SHIFTER`)

 Description

The SPI Shifter performs serial-to-parallel and parallel-to-serial data conversion. It shifts data out through the MOSI line during transmission and simultaneously captures incoming serial data from the MISO line. The shifting operation depends on CPOL, CPHA, LSB/MSB selection, and generated SPI clocks.

 Inputs

* APB Clock
* Reset
* Slave Select
* Send Data Enable
* LSB First Enable
* CPOL
* CPHA
* Generated SPI Clock Signals
* MOSI Parallel Data
* MISO Serial Input
* Receive Data Enable

Outputs

* MOSI Serial Output
* Received Parallel Data (`data_miso_o`)



3. SPI Slave Select Controller (`SPI_Slave_select`)

Description

The Slave Select Controller manages the activation and deactivation of the SPI Slave. It controls the Slave Select (`SS`) signal and indicates whether a transmission is currently in progress. It also generates receive enable signals after successful communication.

Inputs

* APB Clock
* Reset
* Master/Slave Configuration
* Send Data Request
* Stop-in-Wait Enable
* SPI Mode
* Baud Rate Divisor

Outputs

* Slave Select (`SS`)
* Transfer-In-Progress (`TIP`)
* Receive Data Enable



4. APB SPI Slave Interface (`SPI_APB_SLAVE`)

Description

This block serves as the bridge between the APB bus and the SPI interface. It receives APB read/write transactions, stores configuration parameters into internal registers, supplies transmit data to the SPI shifter, collects received data, and provides APB-compliant responses including interrupts and status signals.

Inputs

* APB Clock
* Reset
* APB Address
* APB Write Signal
* APB Select
* APB Enable
* APB Write Data
* Slave Select Status
* Received SPI Data
* Receive Complete Signal
* Transfer-In-Progress Status

Outputs

* APB Read Data
* Master/Slave Selection
* Clock Polarity (CPOL)
* Clock Phase (CPHA)
* LSB First Selection
* Stop-in-Wait Control
* Baud Rate Prescaler
* Baud Rate Divider
* SPI Interrupt Request
* APB Ready Signal
* APB Error Signal
* Send Data Control
* MOSI Parallel Data
* SPI Mode Selection



Overall Working

1. The APB master writes configuration registers such as SPI mode, baud rate, CPOL, CPHA, and transmit data through the APB interface.
2. The APB Slave module stores these settings and provides them to the SPI functional blocks.
3. The Baud Rate Generator produces the SPI clock and timing signals according to the configured parameters.
4. The Slave Select Controller activates the SPI slave and manages the communication process.
5. The SPI Shifter serially transmits data through the MOSI line while simultaneously receiving data through the MISO line.
6. Once the transfer is complete, the received data is stored in the APB Slave registers, an interrupt is generated if enabled, and the APB master can read the received data through the APB interface.

This modular architecture separates configuration, timing generation, data shifting, and communication control into independent RTL blocks, making the design easier to verify, maintain, and extend.
