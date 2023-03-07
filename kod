#include <ESP8266WiFi.h>
#include <WiFiClientSecure.h>
#include <UniversalTelegramBot.h>
#include <ArduinoJson.h>
#include <Wire.h> 
#include<Servo.h>
#include <WiFiClientSecure.h>
#include <WiFiUDP.h>
#include <WakeOnLan.h>

Servo brava; 
int angle = 0; 
unsigned long onTime = 0;
unsigned long offTime = 0;
unsigned long startMillis;
unsigned long currentMillis;
unsigned long elapsedSeconds;
unsigned long elapsedMinutes;
unsigned long elapsedHours;
float napon = 0.0;
int sifra=0;

int RelayPinKugle = D7;// define output pin for relay 
int RelayPinErco = D6;// define output pin for relay 
int RelayPinGaraza1 = D5;// define output pin for relay 
int RelayPinGaraza2 = D4;// define output pin for relay 
int RelayPinGaraza3 = D2;// define output pin for relay 
int RelayPinTerasa = D8;// define output pin for relay
int RelayPinReflektor = D9;// define output pin for relay
const int buzzer = D3; //buzzer to arduino pin D3
unsigned long previousMillis = 0;
const long INTERVAL = 6000;  

char ssid[] = "Laki";         // your network SSID (name)
char password[] = "inteljezakon"; // your network password
String chat_id = "5569752868";
#define TELEGRAM_BOT_TOKEN "6245273161:AAFgrRig0thxkqJ01D0ecSrQtXSi73tArRg"
// This is the Wifi client that supports HTTPS
WiFiClientSecure client;
UniversalTelegramBot bot(TELEGRAM_BOT_TOKEN, client);

bool in_password_state = false;

int delayBetweenChecks = 1000;
unsigned long lastTimeChecked;   //last time messages' scan has been done

unsigned long lightTimerExpires;
boolean lightTimerActive = false;
//
struct targetDevice {
  byte mac[6]; //The targets MAC address
  String deviceName;
};

targetDevice devices[] ={
  {{ 0xFC, 0xAA, 0x14, 0x1F, 0xCC, 0x10 }, "Anci"}, //BC-5F-F4-FF-FF-FF
  {{ 0x88, 0x88, 0x88, 0x88, 0x87, 0x88 }, "Laki"} //04-D9-F5-FF-FF-FF
};

// Change to match how many devices are in the above array.
int numDevices = 2;

//------- ---------------------- ------

WiFiUDP UDP;
/**
 * This will brodcast the Magic packet on your entire network range.
 */
IPAddress computer_ip(255,255,255,255); 
//


void setup() {
  Serial.begin(115200);
  startMillis = millis();
  brava.attach(D10); 
  pinMode(A0, INPUT);
  pinMode(RelayPinKugle, OUTPUT);
   // Set RelayPin as an output pin
  pinMode(RelayPinErco, OUTPUT);

   pinMode(RelayPinGaraza1, OUTPUT);
   pinMode(RelayPinGaraza2, OUTPUT);
   pinMode(RelayPinTerasa, OUTPUT);
   pinMode(RelayPinReflektor, OUTPUT);
   pinMode(RelayPinGaraza3, OUTPUT);
   pinMode(buzzer, OUTPUT); // Set buzzer - pin D3 as an output
   
   digitalWrite(RelayPinKugle, HIGH); // turn the relay ON (low is ON if relay is LOW trigger. change it to HIGH if you have got HIGH trigger relay)
  digitalWrite(RelayPinErco, HIGH); // turn the relay ON (low is ON if relay is LOW trigger. change it to HIGH if you have got HIGH trigger relay)
  digitalWrite(RelayPinGaraza1, HIGH); // turn the relay ON (low is ON if relay is LOW trigger. change it to HIGH if you have got HIGH trigger relay)
  digitalWrite(RelayPinGaraza2, HIGH); // turn the relay ON (low is ON if relay is LOW trigger. change it to HIGH if you have got HIGH trigger relay)
  digitalWrite(RelayPinTerasa, HIGH); // turn the relay ON (low is ON if relay is LOW trigger. change it to HIGH if you have got HIGH trigger relay)
  digitalWrite(RelayPinReflektor, HIGH); // turn the relay ON (low is ON if relay is LOW trigger. change it to HIGH if you have got HIGH trigger relay)
  digitalWrite(RelayPinGaraza3, HIGH); // turn the relay ON (low is ON if relay is LOW trigger. change it to HIGH if you have got HIGH trigger relay)

  // Set WiFi to station mode and disconnect from an AP if it was Previously connected
  WiFi.mode(WIFI_STA);
  WiFi.disconnect();
  delay(100);

  // attempt to connect to Wifi network:
  Serial.print("Connecting Wifi: ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(500);
  }

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());

  // Only required on 2.5 Beta
  client.setInsecure();

  
  // longPoll keeps the request to Telegram open for the given amount of seconds if there are no messages
  // This hugely improves response time of the bot, but is only really suitable for projects
  // where the the initial interaction comes from Telegram as the requests will block the loop for
  // the length of the long poll
  bot.longPoll = 60;
  bot.sendMessage(chat_id, "Sistem je upravo startovan 😁!");
}


void handleNewMessages(int numNewMessages) {

  for (int i = 0; i < numNewMessages; i++) {

    // If the type is a "callback_query", a inline keyboard button was pressed
    if (bot.messages[i].type ==  F("callback_query")) {
      String text = bot.messages[i].text;
      String chat_id = String(bot.messages[i].chat_id);
      Serial.print("Call back button pressed with text: ");
      Serial.println(text);

      if (text == F("RELAYKUGLEON")) 
      {
        digitalWrite(RelayPinKugle, LOW); 
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo upaljeno ✅");
      } 
      else if (text == F("RELAYKUGLEOFF")) 
      {
        digitalWrite(RelayPinKugle, HIGH); 
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo ugaseno ❌");
      }
      else if (text == F("RELAYREFLEKTORON")) 
      {
        digitalWrite(RelayPinReflektor, LOW); 
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo upaljeno ✅");
      } 
      else if (text == F("RELAYREFLEKTOROFF")) 
      {
        digitalWrite(RelayPinReflektor, HIGH); 
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo ugaseno ❌");
      } 
      else if (text == F("RELAYERCOON")) 
      {
        digitalWrite(RelayPinErco, LOW); 
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo upaljeno ✅");
      } 
      else if (text == F("RELAYERCOOFF")) 
      {
        digitalWrite(RelayPinErco, HIGH); 
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo ugaseno ❌");
      } 
      else if (text == F("RELAYTERASAON")) 
      {
        digitalWrite(RelayPinTerasa, LOW); 
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo upaljeno ✅");
      } 
      else if (text == F("RELAYTERASAOFF")) 
      {
        digitalWrite(RelayPinTerasa, HIGH);
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo ugaseno ❌"); 
      } 
      else if (text == F("RELAYGARAZA1ON"))
      {
        digitalWrite(RelayPinGaraza1, LOW);
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo upaljeno ✅");
      }
      else if (text == F("RELAYGARAZA1OFF"))
      {
        digitalWrite(RelayPinGaraza1, HIGH);
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo ugaseno ❌");
      }
      else if (text == F("RELAYGARAZA2ON"))
      {
        digitalWrite(RelayPinGaraza2, LOW);
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo upaljeno ✅");
      }
      else if (text == F("RELAYGARAZA2OFF"))
      {
        digitalWrite(RelayPinGaraza2, HIGH);
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo ugaseno ❌");
      }
      else if (text == F("RELAYGARAZA3ON"))
      {
        digitalWrite(RelayPinGaraza3, LOW);
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo upaljeno ✅");
      }
      else if (text == F("RELAYGARAZA3OFF"))
      {
        digitalWrite(RelayPinGaraza3, HIGH);
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo ugaseno ❌");
      }
      else if (text == F("SVENG"))
      {
        digitalWrite(RelayPinGaraza1, LOW);
        digitalWrite(RelayPinGaraza2, LOW);
        digitalWrite(RelayPinGaraza3, LOW);
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo upaljeno ✅");
      }
      else if (text == F("STANIG"))
      {
        digitalWrite(RelayPinGaraza1, HIGH);
        digitalWrite(RelayPinGaraza2, HIGH);
        digitalWrite(RelayPinGaraza3, HIGH);
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo ugaseno ❌");
      }
      else if (text == F("SVEN"))
      {
        digitalWrite(RelayPinErco, LOW);
        digitalWrite(RelayPinKugle, LOW);
        digitalWrite(RelayPinReflektor, LOW);
        digitalWrite(RelayPinTerasa, LOW);
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo upaljeno ✅");
      }
      else if (text == F("STANI"))
      {
        digitalWrite(RelayPinErco, HIGH);
        digitalWrite(RelayPinKugle, HIGH);
        digitalWrite(RelayPinReflektor, HIGH);
        digitalWrite(RelayPinTerasa, HIGH);
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Svetlo ugaseno ❌");
      }
      else if (text == F("TEST"))
      {
        digitalWrite(RelayPinKugle, LOW);// // turn the relay OFF (HIGH is OFF if relay is LOW trigger. change it to LOW if you have got HIGH trigger relay)
        digitalWrite(RelayPinErco, LOW);// // turn the relay OFF (HIGH is OFF if relay is LOW trigger. change it to LOW if you have got HIGH trigger relay)
        digitalWrite(RelayPinGaraza1, LOW);// // turn the relay OFF (HIGH is OFF if relay is LOW trigger. change it to LOW if you have got HIGH trigger relay)
        digitalWrite(RelayPinGaraza2, LOW);// // turn the relay OFF (HIGH is OFF if relay is LOW trigger. change it to LOW if you have got HIGH trigger relay)
        digitalWrite(RelayPinTerasa, LOW);// // turn the relay OFF (HIGH is OFF if relay is LOW trigger. change it to LOW if you have got HIGH trigger relay)
        digitalWrite(RelayPinReflektor, LOW);// // turn the relay OFF (HIGH is OFF if relay is LOW trigger. change it to LOW if you have got HIGH trigger relay)
        digitalWrite(RelayPinGaraza3, LOW);// // turn the relay OFF (HIGH is OFF if relay is LOW trigger. change it to LOW if you have got HIGH trigger relay)

        delay(5000);// wait for 5000 millisecond
    
        digitalWrite(RelayPinKugle, HIGH);// // turn the relay OFF (HIGH is OFF if relay is LOW trigger. change it to LOW if you have got HIGH trigger relay)
        digitalWrite(RelayPinErco, HIGH);// // turn the relay OFF (HIGH is OFF if relay is LOW trigger. change it to LOW if you have got HIGH trigger relay)
        digitalWrite(RelayPinGaraza1, HIGH);// // turn the relay OFF (HIGH is OFF if relay is LOW trigger. change it to LOW if you have got HIGH trigger relay)
        digitalWrite(RelayPinGaraza2, HIGH);// // turn the relay OFF (HIGH is OFF if relay is LOW trigger. change it to LOW if you have got HIGH trigger relay)
        digitalWrite(RelayPinTerasa, HIGH);// // turn the relay OFF (HIGH is OFF if relay is LOW trigger. change it to LOW if you have got HIGH trigger relay)
        digitalWrite(RelayPinReflektor, HIGH);// // turn the relay OFF (HIGH is OFF if relay is LOW trigger. change it to LOW if you have got HIGH trigger relay)
        digitalWrite(RelayPinGaraza3, HIGH);// // turn the relay OFF (HIGH is OFF if relay is LOW trigger. change it to LOW if you have got HIGH trigger relay)

        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Uspesan test ✅");
      }
      else if (text == F("RESET"))
      {
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Sistem će biti resetovan! ↺");
      }
      else if (text == F("OTVORI"))
      {
      for(angle = 180; angle>=1; angle-= 20)    // command to move from 0 degrees to 180 degrees 
        {                                  
          brava.write(angle);                 //command to rotate the servo to the specified angle
          delay(15); 
        }
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Vrata otkljucana 🔓");
      }
      else if (text == F("ZATVORI"))
      {
        for(angle = 0; angle < 180; angle += 20)    // command to move from 0 degrees to 180 degrees 
      {                                  
        brava.write(angle);                 //command to rotate the servo to the specified angle
        delay(15); 
      }
        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, "Vrata zakljucana 🔒");
      }
      else if (text == F("STANJE"))
      {
        bot.sendChatAction(chat_id, "typing");
        String statusKugle1 = digitalRead(RelayPinKugle) == LOW ? "ON ✅" : "OFF ❌";
        String statusErco2 = digitalRead(RelayPinErco) == LOW ? "ON ✅" : "OFF ❌";
        String statusGaraza1 = digitalRead(RelayPinGaraza1) == LOW ? "ON ✅" : "OFF ❌";
        String statusGaraza2 = digitalRead(RelayPinGaraza2) == LOW ? "ON ✅" : "OFF ❌";
        String statusTerasa = digitalRead(RelayPinTerasa) == LOW ? "ON ✅" : "OFF ❌";
        String statusReflektor = digitalRead(RelayPinReflektor) == LOW ? "ON ✅" : "OFF ❌";
        String statusGaraza3 = digitalRead(RelayPinGaraza3) == LOW ? "ON ✅" : "OFF ❌";
        String statusGaraze = digitalRead(angle) == LOW ? "Zakljucana 🔒" : "Otkljucana 🔓";
        String poruka="Stanje:\nKugle: " + statusKugle1 + "\nDrugi deo: " + statusErco2 + "\nGaraza 1: " + statusGaraza1 + "\nGaraza 2: " + statusGaraza2 + "\nTerasa: " + statusTerasa + "\nReflektor: " + statusReflektor + "\nGaraza 3: " + statusGaraza3 + "\nStatus garaze: " + statusGaraze + "\n🕑Vreme rada sistema: " + elapsedHours + ":" + elapsedMinutes % 60 + ":" + elapsedSeconds % 60 + " sekundi";
        bot.sendMessage(chat_id, poruka);
      }
      else if (text.startsWith("WOL")) {
        text.replace("WOL", "");
        int index = text.toInt();
        Serial.print("Sending WOL to: ");
        Serial.println(devices[index].deviceName);
        WakeOnLan::sendWOL(computer_ip, UDP, devices[index].mac, sizeof devices[index].mac);
      }
      else if (text == F("STATUS"))
      {
        int freeMem = ESP.getFreeHeap();
        napon = analogRead(A0) * (3.3 / 1023.0);
        String poruka = "💻SSID: " + WiFi.SSID() + "\n" +
        "📶Signalna snaga: " + WiFi.RSSI() + " dBm\n" +
        "ℹIP adresa: " + WiFi.localIP().toString() + "\n" +
        "ℹMAC adresa: " + WiFi.macAddress() + "\n" +
        "🏠Naziv uređaja: " + WiFi.hostname() + "\n" +
        "⚡Napon: " + String(napon) + " V\n" +
        "🐏Slobodna memorija: " + String(freeMem) + " bajtova\n" +
        "🕑Vreme rada: "+ elapsedHours + ":" + elapsedMinutes % 60 + ":" + elapsedSeconds % 60 + " sekundi";



        int numClients = WiFi.softAPgetStationNum();
        poruka += "\n📡Broj povezanih klijenata: " + String(numClients) + "";

          // Dodaje informacije o verziji firmware-a
          poruka += "\n🌐Verzija firmware-a: " + String(ESP.getSdkVersion()) + "\n";

          // Dodaje informacije o fleš memoriji
          uint32_t flashSize = ESP.getFlashChipSize() / 1024;
          uint32_t flashFree = (ESP.getFlashChipSize() - ESP.getFlashChipRealSize()) / 1024;
          poruka += "💾Veličina fleš memorije: " + String(flashSize) + " KB\n" +
              "💾Preostalo na fleš memoriji: " + String(flashFree) + " KB\n";



        bot.sendChatAction(chat_id, "typing");
        bot.sendMessage(chat_id, poruka);
      }

    } 
    else
    {
      String chat_id = String(bot.messages[i].chat_id);
      String text = bot.messages[i].text;
      Serial.println("Ispis poruke "+ text); //prikazuje poruku koju proisnik unese
      Serial.println("Chatid je "+ chat_id); //prikazuje chat id bota
      if (text == F("/spolja") && sifra==333) {

        String keyboardJson = R"([
  [
    { "text" : "✅ON", "callback_data" : "RELAYKUGLEON"},
    { "text" : "❌OFF", "callback_data" : "RELAYKUGLEOFF"  },
    { "text" : "✅ON", "callback_data" : "RELAYREFLEKTORON" },
    { "text" : "❌OFF", "callback_data" : "RELAYREFLEKTOROFF" }
  ],
  [
    { "text" : "✅ON", "callback_data" : "RELAYERCOON" },
    { "text" : "❌OFF", "callback_data" : "RELAYERCOOFF" },
    { "text" : "✅ON", "callback_data" : "RELAYTERASAON" },
    { "text" : "❌OFF", "callback_data" : "RELAYTERASAOFF" }
  ],
  [
    { "text" : "✅ALL ON", "callback_data" : "SVEN" },
    { "text" : "❌ALL OFF", "callback_data" : "STANI" }
  ],
  [
    { "text" : "10 Mins", "callback_data" : "TIME10" },
    { "text" : "20 Mins", "callback_data" : "TIME20" },
    { "text" : "30 Mins", "callback_data" : "TIME30" }
  ]
])";
bot.sendMessageWithInlineKeyboard(chat_id, "💡Spoljasnja rasveta", "", keyboardJson);
      }

      else if (text == F("/garaza") && sifra==333) {
        String keyboardJson = R"([
  [
    { "text" : "✅ON", "callback_data" : "RELAYGARAZA1ON" },
    { "text" : "❌OFF", "callback_data" : "RELAYGARAZA1OFF" },
    { "text" : "✅ON", "callback_data" : "RELAYGARAZA2ON" },
    { "text" : "❌OFF", "callback_data" : "RELAYGARAZA2OFF" }
  ],
  [
    { "text" : "✅ON", "callback_data" : "RELAYGARAZA3ON" },
    { "text" : "❌OFF", "callback_data" : "RELAYGARAZA3OFF" }
  ],
  [
    { "text" : "✅ALL ON", "callback_data" : "SVENG" },
    { "text" : "❌ALL OFF", "callback_data" : "STANIG" }
  ],
  [
    { "text" : "🔓OTVORI", "callback_data" : "OTVORI" },
    { "text" : "🔒ZATVORI", "callback_data" : "ZATVORI" }
  ],
  [
    { "text" : "10 Mins", "callback_data" : "TIME10" },
    { "text" : "20 Mins", "callback_data" : "TIME20" },
    { "text" : "30 Mins", "callback_data" : "TIME30" }
  ]
])";

bot.sendMessageWithInlineKeyboard(chat_id, "💡Garaza", "", keyboardJson);
      }

      else if (text == F("/wol")) {

        String keyboardJson = "[";
        for(int i = 0; i< numDevices; i++)
        {
          keyboardJson += "[{ \"text\" : \"" + devices[i].deviceName + "\", \"callback_data\" : \"WOL" + String(i) + "\" }]";
          if(i + 1 < numDevices){
            keyboardJson += ",";
          }
        }
        keyboardJson += "]";
        bot.sendMessageWithInlineKeyboard(chat_id, "Send WOL to following devices:", "", keyboardJson);
      }

      else if (text == F("/info") && sifra==333) {
        String keyboardJson = R"([       
  [
    { "text" : "💁STANJE", "callback_data" : "STANJE" }
  ],
  [
    { "text" : "🧪TEST", "callback_data" : "TEST" },
    { "text" : "↺RESET", "callback_data" : "RESET" }
  ],
  [
    { "text" : "ℹSTATUS", "callback_data" : "STATUS" }
  ],
  [
    { "text" : "10 Mins", "callback_data" : "TIME10" },
    { "text" : "20 Mins", "callback_data" : "TIME20" },
    { "text" : "30 Mins", "callback_data" : "TIME30" }
  ]
])";
bot.sendMessageWithInlineKeyboard(chat_id, "💁 Info", "", keyboardJson);
      }
      else
      {
        bot.sendMessage(chat_id, "Unesite sifru");
      }
      
      // When a user first uses a bot they will send a "/start" command
      // So this is a good place to let the users know what commands are available
      if (text == F("/start")) {
  // Postavite stanje šifre na "true" i zatražite od korisnika šifru
  String chat_id = bot.messages[0].chat_id;
  String text = bot.messages[0].text;
  in_password_state = true;
} else if (in_password_state) {
  // Proverite da li je šifra ispravna
  if (text == F("/333")) {
    String chat_id = bot.messages[0].chat_id;
    sifra=333;
    bot.sendMessage(chat_id, "Uspesno logovanje!\nKomande koje mozete koristiti su:\n/garaza, \n/spolja, \n/info");

    // Podesite stanje šifre na "false" kako bi se omogućilo korišćenje komandi
    in_password_state = false;
  } else {
    String chat_id = bot.messages[0].chat_id;
    bot.sendMessage(chat_id, "Neispravna sifra!");
  }
} else  {
  // Ova poruka se prikazuje samo ako niste u stanju šifre i ako unesete pogrešnu komandu
  String chat_id = bot.messages[0].chat_id;
  if (text == F("/garaza")) {
  } else if (text == F("/spolja")) {
  } else if (text == F("/info")) {
  } else {
    bot.sendMessage(chat_id, "Uneli ste neispravnu komandu. ⛔");
  }
}
}
}
}
void loop() {
  currentMillis = millis();  // trenutno vrijeme
  elapsedSeconds = (currentMillis - startMillis) / 1000;  // izračunaj proteklo vrijeme u sekundama
  elapsedMinutes = elapsedSeconds / 60;  // izračunaj proteklo vrijeme u minutama
  elapsedHours = elapsedMinutes / 60;  // izračunaj proteklo vrijeme u satima
  if (millis() > lastTimeChecked + delayBetweenChecks)  {

    // getUpdates returns 1 if there is a new message from Telegram
    int numNewMessages = bot.getUpdates(bot.last_message_received + 1);

    if (numNewMessages) {
      Serial.println("got response");
      handleNewMessages(numNewMessages);
    }
}

  Serial.print("SSID: ");
  Serial.println(WiFi.SSID());
  Serial.print("Signalna snaga: ");
  Serial.print(WiFi.RSSI());
  Serial.println(" dBm");
  Serial.print("IP adresa: ");
  Serial.println(WiFi.localIP());
  Serial.print("MAC adresa: ");
  Serial.println(WiFi.macAddress());
  
  // prikupljanje drugih informacija
  Serial.print("Naziv uređaja: ");
  Serial.println(WiFi.hostname());
  Serial.print("Vreme rada: ");
  Serial.print(millis() / 1000);
  Serial.println(" sekundi");
}
