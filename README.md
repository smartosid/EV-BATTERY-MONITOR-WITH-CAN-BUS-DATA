# EV-BATTERY-MONITOR-WITH-CAN-BUS-DATA

An electric vehicle (EV) battery can be utilized in two major ways: supporting the power grid (Vehicle-to-Grid or V2G) and serving as a power buffer for home energy systems to enhance self-consumption (Vehicle-to-Home or V2H). To enable these functionalities, an ESP8266 microcontroller is employed to manage and coordinate the communication over the CAN bus using standard CAN data frames.

The ESP8266 is interfaced with an MCP2515 CAN bus controller, allowing it to handle CAN protocols effectively. Optionally, an LCD display is connected to provide real-time debugging information, such as test runtime and the number of CAN frames sent. Power for the system is drawn from the 12V pin of the vehicle’s OBD-II port. A voltage regulator steps down the 12V supply to 5V, which powers the ESP8266 and MCP2515. A power switch is included to manually control the system’s operation.

Programming is done using the Arduino IDE. The MCP_CAN library (GitHub - coryjfowler/MCP_CAN_lib) is used to communicate with the MCP2515. The microcontroller sends periodic CAN requests to gather key battery parameters in a specific sequence for efficiency testing.

1. Cell Voltage Collection:
The process starts with acquiring the voltage data from 96 cell pairs. Each voltage value is 2 bytes long and reported in millivolts. A request for group 2 data initiates the transmission. The first response frame includes 4 bytes (2 voltage readings). Successive requests fetch 28 additional data frames, each carrying 7 bytes. Due to this framing, some voltage values span two consecutive frames. Once all 96 voltages are gathered, they are uploaded to a local database over WiFi.

2. Battery Temperature Collection:
Battery temperatures are retrieved by requesting group 4 data. The initial response is followed by four more data frames. These responses yield readings from three temperature sensors, with each temperature sent as a 1-byte value in Celsius. After successful collection, the data is transmitted to the database.

3. Battery Voltage and Current:
Group 1 data is requested next to obtain the battery’s overall voltage and current. This involves one request frame and five additional data queries. The voltage is reported in centivolts (cV) using 2 bytes, and the current is sent in millivolts (mV) using 4 bytes. Current values can be both positive (charging) and negative (discharging). Once retrieved, these values are also pushed to the database.

The ESP8266 connects to a local WiFi network using the ESP8266 WiFi library. Data is sent to an InfluxDB instance—a time-series database—via RESTful API calls. Thanks to this setup, complete battery data (voltage, temperature, and current) is collected and uploaded every 10 seconds, which is sufficient for the intended performance and efficiency evaluations.

