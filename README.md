# TTGO-T8-ESP32

## T8 V1.7
![image](https://github.com/LilyGO/TTGO-T8-ESP32/blob/master/image/T81.7.jpg)
## T8 V1.3
![image](https://github.com/LilyGO/TTGO-T8-ESP32/blob/master/image/T8%201.3.jpg)
## T8 V1.1
![image](https://github.com/LilyGO/TTGO-T8-ESP32/blob/master/image/T8_V1.1.jpg)

========================
Change SD.h library with mySD.h library from GitHub - nhatuan84/esp32-micro-sdcard 59.

#include <SPI.h>
#include <mySD.h>

File root;
const int chipSelect = 13;

void setup()
{
  Serial.begin(115200);
  Serial.print("Initializing SD card...");
  pinMode(SS, OUTPUT);


  //SDClass::begin(uint8_t csPin, int8_t mosi, int8_t miso, int8_t sck) 
  if (!SD.begin(13, 15, 2, 14)) {
    Serial.println("initialization failed!");
    return;
  }
  
  Serial.println("initialization done.");

  root = SD.open("/");
  
  printDirectory(root, 0);
  
  Serial.println("done!");
}

void loop()
{
  // nothing happens after setup finishes.
}

void printDirectory(File dir, int numTabs) {
  // Begin at the start of the directory
  dir.rewindDirectory();
  
  while(true) {
     File entry =  dir.openNextFile();
     if (! entry) {
       // no more files
       //Serial.println("**nomorefiles**");
       break;
     }
     for (uint8_t i=0; i<numTabs; i++) {
       Serial.print('\t');   // we'll have a nice indentation
     }
     // Print the 8.3 name
     Serial.print(entry.name());
     // Recurse for directories, otherwise print the file size
     if (entry.isDirectory()) {
       Serial.println("/");
       printDirectory(entry, numTabs+1);
     } else {
       // files have sizes, directories do not
       Serial.print("\t\t");
       Serial.println(entry.size(), DEC);
     }
     entry.close();
   }
}

1000×1000 87.6 KB
Conclusion, nothing wrong with SD Card GPIO print on the back of the board.

The root of this issue cause by SD.h library which unable to detect TTGO SD card because it used custom CS, MOSI, MISO, SCK sequence.

Thanks a lot PYLON for the help.

=====================================================
 have the same problem but the solution i found is more easy.
I use the sketch [SD_Test] from:
[Archivo][Ejemplos][SD(Esp32)] (Sorry but i am spanish)
I only change this:

Serial.begin(115200);
if (!SD.begin()) {
Serial.println("Card Mount Failed");
return;
}

for this:

Serial.begin(115200);
SPI.begin(14, 2, 15, 13);
if (!SD.begin(13)) {
Serial.println("Card Mount Failed");
return;
}

and it works for me.

================================
