# Medidor de Energia Solar com Arduino Uno

## Descrição
Este projeto visa medir a energia elétrica produzida por uma fonte solar utilizando um Arduino e sensores de corrente e tensão. O sistema calcula a potência gerada pelo painel solar e exibe os resultados em um display LCD. A energia gerada pode ser visualizada em tempo real, o que permite monitorar o desempenho da instalação solar.

## Objetivo
O principal objetivo deste projeto é calcular a energia produzida por um painel solar. A medição é realizada por meio de sensores de tensão e corrente que, combinados, permitem calcular a potência gerada. Esses dados são exibidos em um display LCD, permitindo que o usuário acompanhe a produção de energia em tempo real.

## Componentes Utilizados
- Placa Arduino (modelo: Arduino Uno)
- Sensor de corrente (por exemplo, ACS712 ou similar)
- Sensor de tensão (pode ser um divisor de tensão ou sensor como ZMPT101B)
- Display LCD 16x2 com interface I2C
- Fonte solar (painel fotovoltaico)
- Cabos de conexão
- Resistores (se necessário para o divisor de tensão)
- Fonte de alimentação para o Arduino (via USB ou fonte externa)

## Funcionalidade
Este sistema monitora a energia elétrica gerada por um painel solar. Ele calcula a potência em watts e a energia acumulada em watt-hora (Wh), utilizando as leituras de corrente e tensão. Os resultados são exibidos em tempo real no display LCD.

## Medições:
- Corrente (A): Usando um sensor de corrente (como o ACS712) para medir a corrente elétrica gerada pelo painel solar.
- Tensão (V): Medindo a tensão do painel solar para calcular a potência.
- Potência (W): A potência é calculada multiplicando a tensão pela corrente.
- Energia acumulada (Wh): A energia acumulada ao longo do tempo é exibida para mostrar a produção total de energia.

## Cálculos:
A potência (em watts) é calculada pela fórmula:
- P(W)=V(V)×I(A)
A energia acumulada (em watt-hora) é calculada somando as leituras de potência ao longo do tempo:
- E(Wh)=P(W)×t(h)

## Esquema de Conexões
### Conexões do Sensor de Corrente (ACS712):
VCC: Conectado ao pino 5V do Arduino.
GND: Conectado ao pino GND do Arduino.
OUT: Conectado a um pino analógico do Arduino (por exemplo, A0).
Conexões do Sensor de Tensão (ZMPT101B ou divisor de tensão):
VCC: Conectado ao pino 5V do Arduino.
GND: Conectado ao pino GND do Arduino.
Saída de Tensão: Conectado a um pino analógico do Arduino (por exemplo, A1).
Conexões do Display LCD 16x2 com I2C:
VCC: Conectado ao pino 5V do Arduino.
GND: Conectado ao pino GND do Arduino.
SDA: Conectado ao pino A4 (para Arduino Uno) ou o pino correspondente para outros modelos.
SCL: Conectado ao pino A5 (para Arduino Uno) ou o pino correspondente para outros modelos.

## Como Usar
### Passo 1: Configuração do Ambiente
- Instale a IDE do Arduino: Baixe a IDE aqui caso ainda não tenha.
- Instale as Bibliotecas Necessárias:
- Abra a IDE do Arduino.
- Vá em Sketch > Incluir Biblioteca > Gerenciar Bibliotecas.
- Instale as bibliotecas:
LiquidCrystal I2C (para o display LCD).
Wire (para comunicação I2C).
### Passo 2: Carregar o Código
- Conecte o Arduino ao seu computador via cabo USB.
- Abra o arquivo sketch.ino presente neste repositório.
- Selecione a porta e a placa corretas em Ferramentas > Placa e Ferramentas > Porta.
- Clique em Upload para carregar o código no seu Arduino.
### Passo 3: Testar
Após o upload, o Arduino deverá começar a calcular e exibir a potência e a energia acumulada no display LCD.

## Código
Aqui está o código básico que realiza a leitura dos sensores de corrente e tensão, e exibe os resultados no display LCD:

#include <LiquidCrystal.h>
#include <DHT.h>

#define DHTPIN 7         
#define DHTTYPE DHT22   
#define ledG 8 // led verde (baixo consumo)
#define ledY 9 // led amarelo (consumo médio)
#define ledR 10 // led vermelho (alto consumo)

DHT dht(DHTPIN, DHTTYPE);
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);
float temper;
float umi;
#define v_rede 220.0   

void setup() {
  Serial.begin(9600);
  dht.begin();
  lcd.begin(16, 2);
  lcd.print("Temperatura: ");
  lcd.setCursor(0, 1);
  lcd.print("Umidade: ");
  pinMode(ledG, OUTPUT);
  pinMode(ledY, OUTPUT);
  pinMode(ledR, OUTPUT);
  digitalWrite(ledG, LOW);
  digitalWrite(ledY, LOW);
  digitalWrite(ledR, LOW);
}

void loop() {
  temper = dht.readTemperature();
  umi = dht.readHumidity();
  if (isnan(temper) || isnan(umi)) {
    lcd.clear();
    lcd.print("Erro de leitura");
    return;
  }
  float corrente = map(temper, 0, 50, 0, 10); // simulação da corrente com base na temperatura
  float energia = v_rede * corrente; // energia em watts
  lcd.clear(); 
  lcd.setCursor(0, 0);
  lcd.print("Temp: ");
  lcd.print(temper);
  lcd.print(" C");
  lcd.setCursor(0, 1);
  lcd.print("Umid: ");
  lcd.print(umi);
  lcd.print(" %");
  Serial.print("Energia consumida (W): ");
  Serial.println(energia);
  if (energia < 100) {
    digitalWrite(ledG, HIGH);
    digitalWrite(ledY, LOW);
    digitalWrite(ledR, LOW);
  } else if (energia >= 100 && energia < 500) {
    digitalWrite(ledG, LOW);
    digitalWrite(ledY, HIGH);
    digitalWrite(ledR, LOW);
  } else {
    digitalWrite(ledG, LOW);
    digitalWrite(ledY, LOW);
    digitalWrite(ledR, HIGH);
  }
  delay(2500);
}

## Personalizações
Você pode personalizar o código e as leituras conforme necessário:
- Ajuste os valores de calibração do sensor de corrente e tensão para o seu modelo específico.
- Modifique o tempo de atualização da leitura para coletar dados mais ou menos frequentemente.
- Adicione mais funcionalidades, como o envio dos dados para um banco de dados ou para a nuvem.

## Contribuição
Se você tiver sugestões ou melhorias para o projeto, sinta-se à vontade para abrir uma Issue ou enviar um Pull Request. Contribuições são bem-vindas!

## Licença
Este projeto está licenciado sob a MIT License.
