# PeakDetection

PeakDetection � uma biblioteca Arduino para detec��o de pico em tempo real em dados de sensores.

[Read in english](https://github.com/leandcesar/PeakDetection/blob/master/README.gif).

## Algoritmo

Baseia-se no princ�pio de dispers�o: se um novo data point � um n�mero `x` de desvios-padr�o de uma m�dia m�vel, o algoritmo sinaliza (tamb�m chamado de z-score).

O algoritmo tem 3 entradas:

* `lag`: � o atraso da janela em movimento (tamanho da m�dia m�vel). Esse par�metro determina o quanto seus dados ser�o suavizados e o quanto o algoritmo � adapt�vel a mudan�as na m�dia de longo prazo dos dados. Quanto mais estacion�rio for o seu dado, mais atrasos voc� deve incluir. Se os seus dados contiverem tend�ncias que variam com o tempo, considere a rapidez com que voc� deseja que o algoritmo se adapte a essas tend�ncias.

* `threshold`: este par�metro � o n�mero de desvios-padr�o da m�dia m�vel acima da qual o algoritmo classificar� um novo data point como sendo um sinal. Este par�metro deve ser definido com base em quantos sinais voc� espera. O threshold, portanto, influencia diretamente a sensibilidade do algoritmo e, portanto, a frequ�ncia com que o algoritmo sinaliza.

* `influence`: � o z-score no qual o algoritmo sinaliza. Este par�metro determina a influ�ncia dos sinais no threshold de detec��o do algoritmo. Se colocar em 0, os sinais n�o ter�o influ�ncia sobre o threshold, de forma que os sinais futuros ser�o detectados com base em um threshold que � calculado com uma m�dia e um desvio-padr�o que n�o � influenciado por sinais anteriores. Voc� deve colocar esse par�metro entre 0 e 1, dependendo da medida em que os sinais podem influenciar sistematicamente a tend�ncia de varia��o de tempo dos dados.

## Fun��es

- begin()
- add()
- getPeak()
- getFilt()

### begin()

Inicializa o objeto PeakDetection e as configura��es para os par�metros de ajustes. Se nenhum par�metro for definido, o padr�o permanece.

```C++
peakdetection.begin(lag,threshold,influence);
```

Alternadamente:

```C++
peakdetection.begin(); //lag=32, threshold=2, influence=0.5
```

### add()

Adiciona um novo data point ao algoritmo, calcula o desvio-padr�o e a m�dia m�vel.

```
peakdetection.add(datapoint);
```

### getPeak()

Retorna o status do pico do �ltimo data point adicionado. {-1, 0, 1}, representando abaixo, dentro ou acima do threshold do desvio padr�o, respectivamente.

```
double peak = peakdetection.getPeak();
```

### getFilt()

Retorna o �ltimo data point filtrado pela m�dia m�vel.

```
double filtered = peakdetection.getFilt();
```

## Instala��o

Para usar essa biblioteca:

1. Baixe o zip e descompacte o arquivo baixado.
2. Copie a pasta para a pasta de bibliotecas do Arduino (_C:/Users/username/Documents/Arduino/libraries_).
3. Renomeie para PeakDetection.

## Exemplo

### C�digo

```C++
#include <PeakDetection.h> // importa a biblioteca

PeakDetection peakDetection; // cria um objeto PeakDetection

void setup() {
  Serial.begin(9600); // define a taxa de dados para a comunica��o serial
  pinMode(A0, INPUT); // pino anal�gico usado para conectar o sensor
  peakDetection.begin(48, 3, 0.6); // define o lag, threshold e influence
}

void loop() {
    double data = (double)analogRead(A0)/512-1; // l� o valor do sensor e converte em um intervalo entre -1 e 1
    peakDetection.add(data); // adiciona um novo data point
    int peak = peakDetection.getPeak(); // retorna 0, 1 ou -1
    double filtered = peakDetection.getFilt(); // m�dia m�vel
    Serial.print(data); // imprime o data point
    Serial.print(",");
    Serial.print(peak); // imprime o status do pico
    Serial.print(",");
    Serial.println(filtered); // imprime a m�dia m�vel
}
```

### Sa�da

![Example output](https://github.com/leandcesar/PeakDetection/blob/master/examples/output.gif)

## Cr�ditos

* [StackOverFlow](https://stackoverflow.com/questions/22583391/peak-signal-detection-in-realtime-timeseries-data).
