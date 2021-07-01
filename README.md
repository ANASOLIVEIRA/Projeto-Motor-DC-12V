**Projeto-Motor-DC-12V**

#Projeto Controlador de Velocidade para Motor CC de 12V
##Requisitos do Projeto:
- Motor CC de 12V
- Sistema que detecte a velocidade em que o motor está girando, projeto precisa ter 2 velocidades distintas no programa
- Deve-se utilizar um monstrador LCD para indicar a velocidade em RPM e o sentido de rotação
- Deve-se utilizar um botão que acionado, fará o motor parar e girar no sentido contrário, e tenha um alarme sonoro que indique essa mudança
- Deve-se ter um sistema de proteção para que não tenha acesso ao motor enquanto ele estiver funcionando

##Componentes utilizados
1. Placa Arduíno Mega
2. Display LDC 16x2
3. Módulo I2C
4. Sensor de Efeito Hall
5. Led Azul
6. Led Verde
7. Led Vermelho
8. Botão Push 1
9. Botão Push 2
10. Interruptor ON/OFF
11. Sensor Ultrassônico
12. Buzzer
13. Bateria
14. Resistores

##Objetivo
1. Leitura da rotação por minuto (RPM) de um motor 12v utilizando um sensor de efeito hall

##Funcionamento do programa descrito

Ao pressionar o botão 1, o buzzer emite um som e o led vermelho é acionado, depois de 1 segundo buzzer e led vermelho apagam, led azul acende e motor começa a rotacionar sentido horário com uma rotação baixa durante 10 segundos, e depois aumenta para a rotação máxima do motor.

Ao pressionar o botão 2, o buzzer emite um som e o led vermelho é acionado, depois de 1 segundo, buzzer e led vermelho apagam, led verde acende e motor começa a rotacionar sentido anti-horário com uma rotação baixa por 10 segundos, e depois aumenta para a rotação máxima do motor.

Ao eixo do motor estará fixado um imã, este imã irá ficar rotacionando, e a cada ciclo completo o imã fica posicionado a frente do sensor de efeito Hall. O sensor detecta o ciclo realizado pelo imã e envia a leitura do RPM realizado. 
![image](https://user-images.githubusercontent.com/61547619/124055793-8b75d380-d9f2-11eb-83db-faa4a7390e9e.png)


##Detalhamento técnico
1. Sensor Efeito Hall
O sensor de efeito Hall é um sensor de campo magnético. Podenso ser utilizado para detectar a rotação de um objeto, posicionamento do objeto ou detectar movimento.
O efeito hall é resultado pela força de Lorentz no movimento de elétrons a um campo magnético, onde se tem uma tensão de hall proporcional ao campo magnético aplicado. 

###1.1. Teoria de Efeito Hall

O fluxo de corrente em um material sem o efeito de uma campo magnético, tem suas linhas equipotenciais que se cruzam formando um ângulo de 90°.
Fb = q*V*B*senO
Onde 
Fb: força magnética [N]
q: módulo carga elétrica [C]
v: velocidade da carga [m/s]
B: campo magnético [T]
senO: ângulo entre direção da velocidade e a direção do campo magnético

Com um fluxo de corrente em um objeto sujeito a um campo magnético formando 90°, o ângulo do fluxo da corrente é alterado (sofrendo o efeito do campo magnético), que se é conhecido como ângulo Hall. Sendo assim as linhas equipotenciais se inclinam, causando uma tensão de Hall ao longo do objeto.
####1.1.1 Vantagens
- Alta sensibilidade
- Erros despreziveis de linearidade
- Erros desprezíveis em relação a vibração
- Repitibilidade
- Estabilidade















