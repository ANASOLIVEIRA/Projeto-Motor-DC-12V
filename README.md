# TTC2

# Projeto DESENVOLVIMENTO DE PROTÓTIPO DE SISTEMA DE INSPEÇÃO ÓPTICA DE PLACAS DE CIRCUITO IMPRESSO PARA LINHAS DE PRODUÇÃO COM INSERÇÃO MANUAL DE COMPONENTES
## Requisitos do Projeto:
- Motor CC de 12V
- Sistema que detecte a velocidade em que o motor está girando, projeto precisa ter 2 velocidades distintas no programa, critério
- Deve-se utilizar um monstrador LCD para indicar a velocidade em RPM e o sentido de rotação
- Deve-se utilizar um botão que acionado, fará o motor parar e girar no sentido contrário, e tenha um alarme sonoro que indique essa mudança
- Deve-se ter um sistema de proteção para que não tenha acesso ao motor enquanto ele estiver funcionando

## Componentes utilizados
1. Placa Arduíno Mega
2. Display LDC 16x2
3. Shield L293D - Ponte H
4. Motor DC 12V - C9045 60001 RD548311
5. Fonte 12VDC (Motor)
6. Módulo I2C
7. Sensor de Efeito Hall - Módulo 3144
8. Led Azul
9. Led Verde
10. Led Vermelho
11. Botão Push 1 - Sentido Horário
12. Botão Push 2 - Sentido Anti-Horário
13. Botão Push 3 - Velocidade - 3 velocidades (opcional)
14. Botão Push 4 - Incremento (Calibração)
15. Botão Push 5 - Decremento (Calibração)
16. Interruptor ON/OFF
17. Sensor Ultrassônico HC-SR04
18. Buzzer
19. Bateria
20. Resistores

## Objetivo
- Leitura da rotação por minuto (RPM) de um motor 12v utilizando um sensor de efeito hall
- Sensor ultrassônico HC-SR04 para segurança, quando abre a caixa do projeto, tendo assim acesso ao motor, quando abre a tampa o sensor para o motor

## Funcionamento do programa descrito

Ao pressionar o botão 1, o buzzer emite um som e o led vermelho é acionado, depois de 1 segundo buzzer e led vermelho apagam, led verde acende e motor começa a rotacionar sentido horário com uma rotação baixa. Ao pressionar o botão de velocidade a rotação irá aumentar para uma rotação média, e ao pressionar o botão novamente a rotação muda para a rotação máxima.

Ao pressionar o botão 2, o buzzer emite um som e o led vermelho é acionado, depois de 1 segundo, buzzer e led vermelho apagam, led azul acende e motor começa a rotacionar sentido anti-horário com uma rotação baixa. Ao pressionar o botão de velocidade a rotação irá aumentar para uma rotação média, e ao pressionar o botão novamente a rotação muda para a rotação máxima.

Ao eixo do motor estará fixado um imã, este imã irá ficar rotacionando, e a cada ciclo completo o imã fica posicionado a frente do sensor de efeito Hall. O sensor detecta o ciclo realizado pelo imã e envia a leitura do RPM realizado. 

![image](https://user-images.githubusercontent.com/61547619/124055793-8b75d380-d9f2-11eb-83db-faa4a7390e9e.png)

### Segurança
O motor fica isolado em uma caixa, e considerando como um item que pode vir a causar acidentes foi implementado um sistema de segurança, quando a tampa da caixa é aberta o sensor ultrassônico realiza uma leitura fora do parâmetro, emitindo uma mensagem de alerta no display e para o motor.

### 1. Calibração
Para realizar a calibração foi utilizado a lógica de incrementar ou decrementar o valor lido para realar a compensação de leitura do sensor.

## Detalhamento técnico
### 1. Sensor Efeito Hall
O sensor de efeito Hall é um sensor de campo magnético. Podenso ser utilizado para detectar a rotação de um objeto, posicionamento do objeto ou detectar movimento.
O efeito hall é resultado pela força de Lorentz no movimento de elétrons a um campo magnético, onde se tem uma tensão de hall proporcional ao campo magnético aplicado. 

### 1.1. Teoria de Efeito Hall

O fluxo de corrente em um material sem o efeito de uma campo magnético, tem suas linhas equipotenciais que se cruzam formando um ângulo de 90°.

Fb = q*V*B*senO

Onde 

- Fb: força magnética [N]
- q: módulo carga elétrica [C]
- v: velocidade da carga [m/s]
- B: campo magnético [T]
- senO: ângulo entre direção da velocidade e a direção do campo magnético

Com um fluxo de corrente em um objeto sujeito a um campo magnético formando 90°, o ângulo do fluxo da corrente é alterado (sofrendo o efeito do campo magnético), que se é conhecido como ângulo Hall. Sendo assim as linhas equipotenciais se inclinam, causando uma tensão de Hall ao longo do objeto.
### 1.1.1 Vantagens
- Alta sensibilidade
- Erros despreziveis de linearidade
- Erros desprezíveis em relação a vibração
- Repitibilidade
- Estabilidade

## Código do Programa

```C++

#include <Ultrasonic.h>
#include <LiquidCrystal_I2C.h>
#define DISPLAY_W 16
#include <AFMotor.h>

#define pino_trigger 40
#define pino_echo 42
AF_DCMotor motor(1);

LiquidCrystal_I2C lcd(0x27,DISPLAY_W,2);

float revolutions=0;
int rpm=0; // max value 32,767 16 bit
long  startTime=0;
long  elapsedTime=0;
int i;//controle velocidade pulsos
int velocidade=0;
Ultrasonic ultrasonic(pino_trigger, pino_echo);
float distancia;
float result;
void setup() 
{
  
    //Sensor
    pinMode(2, INPUT_PULLUP); //Sensor 3144
    lcd.init();
    lcd.backlight();
    lcd.setCursor(0,0);
    lcd.print("RPM: ");
    lcd.setCursor(0,1);
    lcd.print("Sentido: "); 
    Serial.begin(9600);
    //Motor
    motor.setSpeed(0);//Set motor parado

    pinMode(22, OUTPUT);//Led azul
    pinMode(24, OUTPUT);//led verde
    pinMode(26, OUTPUT);//led vermelho
    pinMode(28, INPUT);//Botão sent horário
    pinMode(30, INPUT);//Botão sent anti-horario
    pinMode(34, INPUT);//Botão velocidade
    pinMode(38, INPUT);//Botão calibraçao +
    pinMode(36, INPUT);//Botão calibração -
    pinMode(32, INPUT);//buzzer
}


 
void loop() 
{
  float cmMsec, inMsec;
  long microsec = ultrasonic.timing();
  cmMsec = ultrasonic.convert(microsec, Ultrasonic :: CM);
  inMsec = ultrasonic.convert(microsec, Ultrasonic :: IN);
  motor.setSpeed(0);//Motor inicia parado
  hcsr04(); // FAZ A CHAMADA DO MÉTODO "hcsr04
    revolutions=0; rpm=0;
    startTime=millis(); //startTime=millis();      
    attachInterrupt(digitalPinToInterrupt(2),interruptFunction,RISING);
    delay(1000);
    detachInterrupt(2);                
//now let's see how many counts we've had from the hall effect sensor and calc the RPM
    elapsedTime=millis()-startTime;//elapsedTime=millis()-startTime;     //finds the time, should be very close to 1 sec

    

    if(revolutions>0)
    {
        rpm=(max(1, revolutions) * 6) / elapsedTime; //60000        //calculates rpm//rpm=(max(1, revolutions) * 60000) / elapsedTime;        //calculates rpm
    }
    lcd.setCursor(0,0);
    String outMsg = String("RPM :") + rpm;
    fillMessage2DisplayWidth(outMsg);
    lcd.print(outMsg);
    Serial.println(outMsg);

    if(result > 0)
    {
      motor.setSpeed(0);
      motor.run(RELEASE); //Desliga o motor
      lcd.init();
      lcd.backlight();
      lcd.setCursor(4,0);
      lcd.print("ATENCAO! ");
      lcd.setCursor(2,1);
      lcd.print("PORTA ABERTA");
    }
    //Calibração
    if(revolutions>0)
    {
      int mais = 36;
      int menos = 38;
      int incrementa=0;
      int decrementa=0;
      decrementa = digitalRead(menos); //DECREMENTA
      incrementa = digitalRead(mais); //INCREMENTA
      if(decrementa == HIGH)
      {
        rpm=((max(1, revolutions) * 60000) / elapsedTime) - 100;
        lcd.setCursor(0,0);
        String outMsg = String("RPM :") + rpm;
        fillMessage2DisplayWidth(outMsg);
        lcd.print(outMsg);
        Serial.println(outMsg);
      }
        if(incrementa == HIGH)
      {
        rpm=((max(1, revolutions) * 60000) / elapsedTime) + 100;
        lcd.setCursor(0,0);
        String outMsg = String("RPM :") + rpm;
        fillMessage2DisplayWidth(outMsg);
        lcd.print(outMsg);
        Serial.println(outMsg);
      }
    }
}
void hcsr04()//Função Leitura De distância no operador
{
    digitalWrite(pino_trigger, LOW); 
    delay(200); 
    digitalWrite(pino_trigger, HIGH);
    delay(100);
    digitalWrite(pino_trigger, LOW);
    long microsec = ultrasonic.timing();
    distancia = ultrasonic.convert(microsec, Ultrasonic :: CM);
    //result = (float)(String(distancia)); 
    result = distancia;
    delay(500); 
 }
void M1()
{
    //begin motor
    if(28 == HIGH)//
    {
      digitalWrite(26,HIGH);//LIGA LED VERMELHO
      digitalWrite(32,HIGH);//LIGA BUZZER
      delay(1000);//Espera 1 segundo
      digitalWrite(26,HIGH);//DESLIGA LED VERMELHO
      digitalWrite(32,HIGH);//DESLIGA BUZZER
      digitalWrite(24,HIGH);//LIGA LED VERDE
      motor.run(RELEASE);//M1 parado
      delay(500);
      motor.setSpeed(116);//M1 na rotação mínima
      motor.run(FORWARD);//Set rotação sentido horário
    }
    if(30 == HIGH)
    {
      digitalWrite(26,HIGH);//LIGA LED VERMELHO
      digitalWrite(32,HIGH);//LIGA BUZZER
      delay(1000);//Espera 1 segundo
      digitalWrite(26,HIGH);//DESLIGA LED VERMELHO
      digitalWrite(32,HIGH);//DESLIGA BUZZER
      digitalWrite(22,HIGH);//LIGA LED AZUL
      motor.run(RELEASE);//M1 parado
      delay(500);
      motor.setSpeed(116);//M1 na rotação mínima
      motor.run(BACKWARD);//Set rotação sentido horário
    }
    if(34 == HIGH)
    {
      i++;
      motor.run(RELEASE);//M1 parado
      delay(500);
      motor.setSpeed(190);//M1 na rotação média
      motor.run(FORWARD);//Set rotação sentido horário

      if(i == 2)
      {
        motor.setSpeed(0);
        motor.run(RELEASE);//Motor stop
        delay(500);
        motor.setSpeed(255);//Motor rotação máxima
        motor.run(FORWARD);//Set sentido anti-horario
      }
      }
    if(34 == HIGH)
    {
      i++;
      motor.run(RELEASE);//M1 parado
      delay(500);
      motor.setSpeed(190);//M1 na rotação média
      motor.run(BACKWARD);//Set rotação sentido horário

      if(i == 2)
      {
        motor.setSpeed(0);
        motor.run(RELEASE);//Motor stop
        delay(500);
        motor.setSpeed(255);//Motor rotação máxima
        motor.run(BACKWARD);//Set sentido anti-horario
      }
      }
}
void interruptFunction() //interrupt service routine
{  
    revolutions++;
}

void fillMessage2DisplayWidth(String & message)
{
    if(message.length()<DISPLAY_W+1)
    {
        while(message.length()<DISPLAY_W)
    {
        message+=" "; 
    }
    return;
  }
 //message is too wide for 1 line of the display, truncate it
    message = message.substring(0, DISPLAY_W-1); 
}

```


Referências
Módulo 3144
https://www.youtube.com/watch?v=pIflB4FQpNE&ab_channel=Clockatronic
