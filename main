#include<at89c51xd2.h>

#define BAUDRATE 2400
#define FREQ 12000000
#define BUFFER_SIZE 1000
#define EEPROM_START_ADDR 0x0000 // Starting address in EEPROM

sbit UART_BUSY = P1^0; // Indicator for UART transmission in progress

void delay(unsigned int count) {
    unsigned int i, j;
    for (i = 0; i < count; i++)
        for (j = 0; j < 1275; j++);
}

void initializeUART() {
    TMOD = 0x20; // Set Timer 1 in 8-bit auto-reload mode
    TH1 = 256 - (FREQ / (32 * 12 * BAUDRATE)); // Calculate TH1 value for the specified baud rate
    TR1 = 1; // Start Timer 1
    SCON = 0x50; // Set Mode 1, 8-bit UART, enable receiver
}

char receiveByte() {
    while (RI == 0); // Wait until reception is complete
    RI = 0; // Reset the receive interrupt flag
    return SBUF; // Return received data
}

void transmitByte(char byte) {
    UART_BUSY = 1; // Indicate UART transmission in progress
    SBUF = byte; // Load data into the buffer
    while (TI == 0); // Wait until transmission is complete
    TI = 0; // Reset the transmit interrupt flag
    UART_BUSY = 0; // Indicate UART transmission complete
}

void receiveData(char* buffer, int bufferSize) {
    unsigned long startTime, endTime;
    int i;

    for (i = 0; i < bufferSize; i++) {
        // Record start time
        startTime = TL1 | (TH1 << 8);

        buffer[i] = receiveByte();

        // Record end time
        endTime = TL1 | (TH1 << 8);

        // Calculate reception speed
        float receptionSpeed = 1.0 / ((endTime - startTime) * 2.0 / FREQ);

        // Print real-time reception speed
        printf("Real-time Reception Speed: %.2f bps\n", receptionSpeed);
    }
}
void storeDataInEEPROM() {
    unsigned int i;
    unsigned int addr = EEPROM_START_ADDR;

    for (i = 0; i < BUFFER_SIZE; i++) {
        receivedData[i] = receiveByte();
        writeEEPROM(addr, receivedData[i]);
        addr++; // Move to the next EEPROM address
    }
}

void retrieveDataFromEEPROM() {
    unsigned int i;
    unsigned int addr = EEPROM_START_ADDR;

    for (i = 0; i < BUFFER_SIZE; i++) {
        receivedData[i] = readEEPROM(addr);
        addr++; // Move to the next EEPROM address
    }
}

void sendData(const char* data, int dataSize) {
     unsigned long startTime, endTime;
    int i;

    for (i = 0; i < dataSize; i++) {
        // Record start time for transmission
        startTime = TL1 | (TH1 << 8);

        transmitByte(data[i]);

        // Record end time for transmission
        endTime = TL1 | (TH1 << 8);

        // Calculate transmission speed
        float transmissionSpeed = 1.0 / ((endTime - startTime) * 2.0 / FREQ);

        // Print real-time transmission speed
        printf("Real-time Transmission Speed: %.2f bps\n", transmissionSpeed);
    }
}

void main() {
    char receivedData[BUFFER_SIZE];
    const char sendData[] ="Finance Minister Arun Jaitley Tuesday hit out at former RBI governor Raghuram Rajan for predicting that the next banking crisis would be triggered by MSME lending, saying postmortem is easier than taking action when it was required. Rajan, who had as the chief economist at IMF warned of impending financial crisis of 2008, in a note to a parliamentary committee warned against ambitious credit targets and loan waivers, saying that they could be the sources of next banking crisis. Government should focus on sources of the next crisis, not just the last one. In particular, government should refrain from setting ambitious credit targets or waiving loans. Credit targets are sometimes achieved by abandoning appropriate due diligence, creating the environment for future NPAs, Rajan said in the note. Both MUDRA loans as well as the Kisan Credit Card, while popular, have to be examined more closely for potential credit risk. Rajan, who was RBI governor for three years till September 2016, is currently.";

    initializeUART();

    // Receive data from PC
    printf("Send data from PC to MCU: ");
    receiveData(receivedData, BUFFER_SIZE);

    // store the received data in EEPROM .
    storeDataInEEPROM();
         
    // Retrieve data from EEPROM
    retrieveDataFromEEPROM();     

    //Transmit data from MCU to PC
    printf("Sending data from MCU to PC...\n");
    sendData(sendData, sizeof(sendData));

    while (1);
}
