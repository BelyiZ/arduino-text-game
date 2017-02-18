#include <Wire.h>
#include <LCD.h>
#include <LiquidCrystal_I2C.h>
#define LCD_HEIGHT 2
#define LCD_WIDTH 16
#define BTN_YES_PIN 4
#define BTN_NO_PIN 7

/**
 * С помощью этой переменной будем отрисовывать данные на дисплей
 * Параметры     Addr, En, Rw, Rs, d4, d5, d6, d7, backlighpin, polarity  - почему так задаюся хз((
 * LCD интерфейс нужно подключать SDA -> A4(SDA)   SCL -> A5(SCL)
 */
LiquidCrystal_I2C lcd(0x3F, 2, 1, 0, 4, 5, 6, 7, 3, POSITIVE);

/** Screen number */
int prevScreen = 0;
int screen = 0;
boolean yes;
boolean no;

boolean male;
String myName;
String partnerName;

char empty[LCD_WIDTH];


void setup() {
  memset(empty, ' ', sizeof(empty));

  pinMode(BTN_YES_PIN, INPUT);
  pinMode(BTN_NO_PIN, INPUT);

  lcd.begin(LCD_WIDTH, LCD_HEIGHT); // Подключаем экран с двумя рядам по 16 символов
  lcd.setBacklight(HIGH); // Включаем подсветку экрана
  lcd.home(); // Переместить курсор на начало
}

void loop() {
  yes = digitalRead(BTN_YES_PIN);
  no = digitalRead(BTN_NO_PIN);

  if (prevScreen != screen) {
    // чистим экран
    prevScreen = screen;
    lcdPrint(empty, 0, 0); 
    lcdPrint(empty, 1, 0); 
  }

  if (screen == 0) {
    lcdPrintAll("Hi! Let's play! ", 
                "<- Yes     No ->");
    nextScreen(2, 1);
  } else if (screen == 1) {
    lcdPrintAll("OK. Don't worry.",
                "Just go to hell!");      
    nextScreen(0, 0);
  } else if (screen == 2) {
    lcdPrintAll("What's your sex?", 
                "<- M        F ->");
    nextScreen(3, 3);
    if (yes|| no) {
      male = yes;
    }
  } else if (screen == 3) {
    lcdPrintAll("Whats your name?", 
         male ? "<- Bob    Rob ->" : 
                "<- Jane   Ann ->");
    nextScreen(4, 5);
    myName = male ? "Rob" : "Ann";
    partnerName = male ? "Mary" : "John";
  } else if (screen == 4) {
    lcdPrintAll("Worst name!I don", 
                "'t want to play!");
    nextScreen(0, 0);
  } else if (screen == 5) {
    lcdPrintAll("8 am.Go to work?", 
                "<- Yes     No ->");
    nextScreen(6, 7);
  } else if (screen == 6) {
    lcdPrintAll("By taxi?  By bus?", 
                "<- Taxi   Bus ->");
    nextScreen(8, 9);
  } else if (screen == 7) {
    lcdPrintAll("<- Call " + partnerName,
                "  Get a sleep ->");
    nextScreen(12, 5);
  } else if (screen == 8) {
    lcdPrintAll("You had an      ", 
                "acciden.   OK ->");
    nextScreen(11, 11);
  } else if (screen == 9) {
    lcdPrintAll("U got an award!", 
                "<Y By a car? N>");
    nextScreen(8, 19);
  } else if (screen == 10) {
    lcdPrintAll("Ur money stolen", 
                "<Y Suicied?  N>");
    nextScreen(11, 7);
  } else if (screen == 11) {
    lcdPrintAll("You are died!  ", 
                "<-  RESTART  ->");
    nextScreen(0, 0);
  } else if (screen == 12) {
    lcdPrintAll("Hi," + myName + "!Go to the",
                "<Y  cinema?  N>");
    nextScreen(13, 14);
  } else if (screen == 13) {
    lcdPrintAll("U had a sex and ", 
                "fell asleep.OK->");
    nextScreen(5, 5);
  } else if (screen == 14) {
    lcdPrintAll(partnerName + " left you.", 
                " <Y Suicied?  N>");
    nextScreen(11, 15);
  } else if (screen == 15) {
    lcdPrintAll("U got drunk and ", 
                "fell asleep.OK->");
    nextScreen(16, 16);
  } else if (screen == 16) {
    lcdPrintAll("8 am.Go to work?", 
                "<- Yes     No ->");
    nextScreen(6, 17);
  } else if (screen == 17) {
    lcdPrintAll("U're in a bar.  ", 
                "Y Find a girl? N");
    nextScreen(18, 15);
  } else if (screen == 18) {
    lcdPrintAll("U met with " + partnerName + ".", 
                "    <- OK ->    ");
    nextScreen(7, 7);
  } else if (screen == 19) {
    lcdPrintAll("U've become cool", 
                "Life's good!OK->");
    nextScreen(20, 20);
  } else if (screen == 20) {
    lcdPrintAll("You began to tra", 
                "vel by car! OK->");
    nextScreen(8, 8);
  } 

// Чтобы не обрабатывать нажатие 100500 раз
  if (yes || no) {
    delay(1000);
  }
}

/**
 * Отрисовывает строку на дисплей
 *
 * @param msg         - строка
 * @param numberStr   - номер строки на которой отобразить "msg"
 *                      (0 первая строка, 1 вторая строка)
 * @param startSymbol - номер строки на которой отобразить "msg"
 *                      (0 первая строка, 1 вторая строка)
 */
void lcdPrint(String msg, int numberStr, int startSymbol) {
    lcd.setCursor(startSymbol, numberStr); // Переводим курсор на строку {numberStr} на {startSymbol} символ
    lcd.print(msg); // Отрисовываем строку на дисплей
}

void lcdPrintAll(String fstStrMsg, String scndStrMsg) {
    lcdPrint(fstStrMsg, 0, 0);
    lcdPrint(scndStrMsg, 1, 0); 
}

void nextScreen(int ifYes, int ifNo) {
  if (yes || no) {
    prevScreen = screen;
    screen = yes ? ifYes : ifNo;
  }
}

