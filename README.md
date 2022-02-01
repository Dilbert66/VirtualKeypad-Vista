 # Virtual Keypad for the Honeywell Vista alarm systems using an ESP32
 
 This sketch provides a virtual keypad web interface using the esp32 as a standalone web server using AES encrypted web socket communications. All keypad functionality provided as well as zone display.  
 
 This sketch uses portions of the code from the VirtualKeypad-Web example for DSC alarm systems found in the taligent/dscKeybusInterface respository at:  https://github.com/taligentx/dscKeybusInterface/blob/master/examples/esp32/VirtualKeypad-Web/VirtualKeypad-Web.ino.
 
 It was adapted to use the Vista alarm system library at: https://github.com/Dilbert66/esphome-vistaECP/tree/master/src/vistaEcpInterface, with the addition of two way AES encryption for the web socket and push notification capability for various applications. This version mainly focuses on the Telegram (https://telegram.org) messaging application due to it's ability to also provide the ability to control your system remotely using bots in addition to having push capability.  All this at no cost!
    
AES encryption was used instead of https  for web server because of the fact that I could not find a decent https server library available for the ESP32.  The few that are out there are very slow and buggy, in addition to the fact that https requires the use of SSL certificates which are a pain to use and setup especially if you use self signed versions.
    
 With AES encryption on the background web socket, we achieve very fast and full encrypted communications between the ESP32 and the web client using mimimal resources.  There is no need to encrypt the client software as there is no sensitive data there.  The passcode remains at the client side and is only used as a key to decrypt the communications channel.  The stronger the password used, the stronger the encryption will be.  For the purposes of this application, this should be more than adequate. 
    
Communications with the remote telegram service uses standard https TLS encryption using the SSL client library.
    
This application will only work on the ESP32 due to it's large amount of resources and the fact that we can easily run async tasks on it.  I've tried to get it to work on the esp8266 but it's resources were totally inadequate for this application.
    
 The web server , telegram notifications and bot cmd processes are all running independently in their own async tasks and do not affect the vista library communications.
 
## Setup

1. Install the following libraries directly from each Github repository:
           ESPAsyncWebServer: https://github.com/me-no-dev/ESPAsyncWebServer
         i. For the ESP32 install:
          AsyncTCP: https://github.com/me-no-dev/AsyncTCP
         ii. For the ESP8266 install:
          ESPAsyncTCP: https://github.com/me-no-dev/ESPAsyncTCP
 
     2. Install the filesystem uploader tools to enable uploading web server files:
         i. For the ESP32 install:
          https://github.com/me-no-dev/arduino-esp32fs-plugin
         ii. For the ESP8266 install the ESP8266FS tool:
          https://arduino-esp8266.readthedocs.io/en/latest/filesystem.html#uploading-files-to-file-system 
 
     3. Install the following libraries, available in the Arduino IDE Library Manager and
        the Platform.io Library Registry:
          ArduinoJson: https://github.com/bblanchon/ArduinoJson
          AESLib: https://github.com/suculent/thinx-aes-lib

     4. Set the AES encryption password that will be used to login to the panel and also used as a key
         for encrypting all web socket communications between the ESP and the browser.
 
     5. If desired, update the DNS hostname in the sketch.  By default, this is set to
        "vistakeypad" and the web interface will be accessible at: http://vistakeypad.local
       
      6. Copy all .h and cpp files from the https://github.com/Dilbert66/esphome-vistaECP/tree/master/src/vistaEcpInterface 
      repository location to your sketch directory or into a subdirectory within your arduino libraries folder.
 
      6.  Select the notification plugin you want to use, copy it to the sketch directory and then configure it by changing the relevent string variables in the plugin header file. The default plugin is vistaserial which only prints out the notifications on the serial port.
 
     7. Upload the sketch. Recommended to use board "ESP32 Dev Module with Minimal SPIFFS partition scheme (190K SPIFFS   partition) to get the maximum flash storage for program storage if using OTA.
 
     7. Upload the SPIFFS data containing the web server files (the "data" subdirectory contents):
         i. For the ESP32:
          Arduino IDE: Tools > ESP32 Sketch Data Upload
         ii. For the ESP8266:
          Arduino IDE: Tools > ESP8266 Sketch Data Upload 
 
     9. Access the virtual keypad web interface by the IP address displayed through
        the serial output or http://vistakeypad.local (for clients and networks that support mDNS).
 
     10. Once the sketch is loaded and running, any following updates can be done via OTA updates for initial testing.  
        See here for an example:  https://randomnerdtutorials.com/esp8266-ota-updates-with-arduino-ide-over-the-air/
        
#### NOTE
 I do not normally recommended leaving the ability to do OTA updates active on a production system. Once done testing, you  should either disable it by commenting out "useOTA" or set a good long passcode. Be aware that for uploading sketch data (web server files) via OTA, you cannot have a password set. Once all testing is done, you can then set your password of choice or disable the feature. 
 

 
 ![Telegram-Chat](https://user-images.githubusercontent.com/7193213/152043067-8921be0e-4cba-4985-8edb-0003a77ed149.png)
![Panel-Armed](https://user-images.githubusercontent.com/7193213/152043070-dae6e684-e9e4-4dec-932f-711ffa7c2035.png)
![Panel-Login](https://user-images.githubusercontent.com/7193213/152043071-3ce4df29-099c-4fdc-b382-b7058abccfb9.png)
