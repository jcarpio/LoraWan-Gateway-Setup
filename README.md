# LoraWan-Gateway-Setup
Dragino LPS8 gateway setup within Then Think Network with Dragino LHT65 temperature sensor and Datacake to display the data

# Materials

### -  <a target="_blank" href="https://www.amazon.es/HUAWEI-51060DYE-B311-221-LTE-CPE/dp/B07TK2X6NS/ref=sr_1_1?crid=1HETXX50JSXSX&amp;keywords=huawei+b311&amp;qid=1697994364&amp;sprefix=huawei+b311%252Caps%252C160&amp;sr=8-1&amp;ufe=app_do%253Aamzn1.fos.5e544547-1f8e-4072-8c08-ed563e39fc7d&_encoding=UTF8&tag=enkire-21&linkCode=ur2&linkId=55b34cb914aa5de6363ee744f44fb40b&camp=3638&creative=24630">Huawei B311</a>

### - <a target="_blank" href="https://www.amazon.es/D-Square-Ethernet-Internet-Repetidor-Ordenador/dp/B09XH3BW98/ref=sr_1_2_sspa?crid=129XWQYTR0N4G&amp;keywords=cable%252Brj45&amp;qid=1697994500&amp;sprefix=cable%252Brj%252Caps%252C193&amp;sr=8-2-spons&amp;sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&amp;th=1&_encoding=UTF8&tag=enkire-21&linkCode=ur2&linkId=e1cc09e1279cbf151f2e5c9739282a83&camp=3638&creative=24630">RJ45 LAN Cable</a>

### - <a target="_blank" href="https://www.amazon.es/MiaoMiao-Interior-Lorawan-Gateway-Service/dp/B091CC57ZT/ref=sr_1_fkmr0_1?__mk_es_ES=%25C3%2585M%25C3%2585%25C5%25BD%25C3%2595%25C3%2591&amp;crid=1KL7YYOOR0N65&amp;keywords=dragino+lps8&amp;qid=1697994631&amp;sprefix=dragino+lps8%252Caps%252C646&amp;sr=8-1-fkmr0&amp;ufe=app_do%253Aamzn1.fos.5e544547-1f8e-4072-8c08-ed563e39fc7d&_encoding=UTF8&tag=enkire-21&linkCode=ur2&linkId=de112ae3343f7043d7264827dba689b1&camp=3638&creative=24630">Dragino LPS8</a>

### - <a target="_blank" href="https://www.amazon.es/MiaoMiao-Interior-Lorawan-Gateway-Service/dp/B091CC57ZT/ref=sr_1_fkmr0_1?__mk_es_ES=%25C3%2585M%25C3%2585%25C5%25BD%25C3%2595%25C3%2591&amp;crid=1KL7YYOOR0N65&amp;keywords=dragino+lps8&amp;qid=1697994631&amp;sprefix=dragino+lps8%252Caps%252C646&amp;sr=8-1-fkmr0&amp;ufe=app_do%253Aamzn1.fos.5e544547-1f8e-4072-8c08-ed563e39fc7d&_encoding=UTF8&tag=enkire-21&linkCode=ur2&linkId=3b6b72d210ff3e64c8deb7a2c5941d0d&camp=3638&creative=24630">Dragino LHT65N Temperature sensor</a>

### - App Huawei AI Life

# Procedure

1. Connect the Dragino LPS8 gatway via the RJ45 cable to the Huawei B311 router. Previously, the Huawei B311 Router must be configured with its SIM card and must have access to the internet.
   
2. With the Huawei AI Life application connect to the Huawei B311 Router to obtain the IP address assigned to the Dragino LPS8

3. Access and configure the LPS8 Gateway
http://wiki.dragino.com/xwiki/bin/view/Main/User%20Manual%20for%20All%20Gateway%20models/LPS8N%20-%20LoRaWAN%20Gateway%20User%20Manual/#H2.A0AccessandConfigureLPS8N 

5. Show the information in Datacake. Follow the instructions in the manual and add the following code in the "Payload Decoder" field
http://wiki.dragino.com/xwiki/bin/view/Main/User%20Manual%20for%20LoRaWAN%20End%20Nodes/LHT65N%20LoRaWAN%20Temperature%20%26%20Humidity%20Sensor%20Manual/#H2.5ShowdataonDatacake
```
function Decoder(bytes, port) {
//Payload Formats of LHT65 Deveice
  return {
    
       //External sensor
       Ext_sensor:
       {
         "0":"No external sensor",
         "1":"Temperature Sensor",
         "4":"Interrupt Sensor send",
         "5":"Illumination Sensor",
         "6":"ADC Sensor",
         "7":"Interrupt Sensor count",
       }[bytes[6]&0x7F],
       
       //Battery,units:V
       BatV:((bytes[0]<<8 | bytes[1]) & 0x3FFF)/1000,
       
       //SHT20,temperature,units:â„ƒ
       TempC_SHT:((bytes[2]<<24>>16 | bytes[3])/100).toFixed(2),
       
       //SHT20,Humidity,units:%
       Hum_SHT:((bytes[4]<<8 | bytes[5])/10).toFixed(1),
       
       //DS18B20,temperature,units:â„ƒ
       TempC_DS:
       {
         "1":((bytes[7]<<24>>16 | bytes[8])/100).toFixed(2),
       }[bytes[6]&0xFF],       
       
       //Exti pin level,PA4
       Exti_pin_level:
       {
         "4":bytes[7] ? "High":"Low",
       }[bytes[6]&0x7F], 
       
       //Exit pin status,PA4
       Exti_status:
       {
         "4":bytes[8] ? "True":"False",
       }[bytes[6]&0x7F],    
       
       //BH1750,illumination,units:lux
       ILL_lx:
       {
         "5":bytes[7]<<8 | bytes[8],
       }[bytes[6]&0x7F],  

        //ADC,PA4,units:V
        ADC_V:
       {
         "6":(bytes[7]<<8 | bytes[8])/1000,
       }[bytes[6]&0x7F],  
       
        //Exti count,PA4,units:times
        Exit_count:
        {
          "7":bytes[7]<<8 | bytes[8],
        }[bytes[6]&0x7F],  
        
        //Applicable to working mode 4567,and working mode 467 requires short circuit PA9 and PA10
        No_connect:
        {
          "1":"Sensor no connection",
        }[(bytes[6]&0x80)>>7],  
  };
}
```


# Links

- Payload Formats of LHT65 Deveice
https://www.dragino.com/downloads/downloads/LHT65/payload_decode/ttn_payload_decode.txt

- Datacake LHT65N Lorawan Get Started
https://datacake.co/dragino-lht65-lorawan-temperature-humidity-sensor-dashboard-template

https://docs.datacake.de/lorawan/get-started

- Dragino LPS9 firmware Old Release
https://www.dragino.com/downloads/index.php?dir=LoRa_Gateway/LPS8/Firmware/Release/old_release/lgw--build-v5.4.1625218977-20210702-1744/

- Dragino LHT65N 
https://www.dragino.com/products/temperature-humidity-sensor/item/224-lht65n.html

- My Datacake Page
https://app.datacake.de/pd/11832e94-10e8-4970-9a23-8e06690aaf43

- Dragino LHT65N Online Manual
http://wiki.dragino.com/xwiki/bin/view/Main/User%20Manual%20for%20LoRaWAN%20End%20Nodes/LHT65N%20LoRaWAN%20Temperature%20%26%20Humidity%20Sensor%20Manual/

- Dragino LPS8 Gateway
http://wiki.dragino.com/xwiki/bin/view/Main/User%20Manual%20for%20All%20Gateway%20models/LPS8N%20-%20LoRaWAN%20Gateway%20User%20Manual/
