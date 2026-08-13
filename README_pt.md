# GAS-DETECTOR

<p align="center">
  <img alt="Linguagem principal" src="https://img.shields.io/badge/C%2B%2B-ESP32-blue?style=for-the-badge&logo=cplusplus">
  <img alt="Arduino" src="https://img.shields.io/badge/Arduino-Compatible-00979D?style=for-the-badge&logo=arduino&logoColor=white">
  <img alt="Wokwi" src="https://img.shields.io/badge/Simulado%20no-Wokwi-purple?style=for-the-badge">
  <img alt="Status" src="https://img.shields.io/badge/status-%20Finalizado-yellow?style=for-the-badge">
</p>

<p align="center">
  <strong>Portugues</strong>
  &nbsp;|&nbsp;
  <a href="./README_en.md">English</a>
</p>

<p align="center">
  Sistema ciberfísico para detecção de gás inflamável com ESP32, sensor de gás, LEDs de status, buzzer e log serial em JSON.
</p>

<p align="center">
  <a href="https://wokwi.com/projects/462937229989048321">Abrir simulacao no Wokwi</a>
</p>

---

## Sobre o projeto

O **GAS-DETECTOR** é um protótipo de sistema embarcado para monitoramento de gás inflamável. O projeto foi desenvolvido em C++ e simulado na plataforma Wokwi, usando um ESP32 como unidade de controle.

A aplicação monitora o nível de gás, classifica a condição do ambiente em estados operacionais e aciona sinais visuais e sonoros conforme o risco detectado.

## Funcionalidades

- Leitura periódica do nível de gás.
- Classificação por estados: `DESLIGADO`, `SEGURO`, `ALERTA`, `PERIGO`, `FALHA_SENSOR` e `FALHA_PINO`.
- LEDs indicadores para status seguro, alerta e perigo.
- Buzzer para sinalização sonora em situações criticas.
- Botão físico para ligar, desligar ou silenciar o alarme.
- Média móvel para suavizar leituras instáveis.
- Histerese nos limiares para evitar troca constante de estado.
- Registro de eventos em buffer circular.
- Saída serial em formato JSON para integração com gateways, dashboards ou Node-RED.
- Verificação de falhas por timeout, leituras invalidas e problemas nos pinos de saída.

## Componentes

| Componente | Função |
| --- | --- |
| ESP32 DevKit C V4 | Microcontrolador principal |
| Sensor de gás | Entrada analógica para nível de gás |
| LED verde | Ambiente seguro |
| LED amarelo | Nível intermediário de alerta |
| LED vermelho | Nível perigoso |
| Buzzer | Alarme sonoro |
| Botão | Liga, desliga e silencia o sistema |
| Resistores | Limitação de corrente e apoio ao circuito |

## Pinagem

| Pino ESP32 | Componente |
| --- | --- |
| GPIO 2 | LED vermelho |
| GPIO 4 | LED amarelo |
| GPIO 5 | LED verde |
| GPIO 25 | Buzzer |
| GPIO 27 | Botão |
| GPIO 34 | Saída analógica do sensor de gás |
| 3V3 | Alimentação do sensor |
| GND | Referencia comum do circuito |

## Maquina de estados

| Estado | Condição | Saída |
| --- | --- | --- |
| `DESLIGADO` | Sistema aguardando acionamento | LEDs e buzzer desligados |
| `SEGURO` | Média de gás abaixo do limite de alerta | LED verde ligado |
| `ALERTA` | Média de gás em faixa intermediaria | LED amarelo ligado |
| `PERIGO` | Média de gás acima do limite critico | LED vermelho e buzzer ligados |
| `FALHA_SENSOR` | Sensor sem resposta dentro do tempo esperado | LEDs vermelho/amarelo alternados e buzzer |
| `FALHA_PINO` | Erros repetidos ou falha de hardware | Todos os LEDs piscando e buzzer |

## Limiares configurados

```cpp
#define SEGURO_SUBIDA   1500
#define SEGURO_DESCIDA  1350
#define PERIGO_SUBIDA   2800
#define PERIGO_DESCIDA  2600
```

Esses valores usam **histerese**, ou seja, o sistema usa limites diferentes para subir e descer de estado. Isso evita que pequenas variações do sensor façam o alarme alternar rapidamente entre seguro, alerta e perigo.

## Funcionamento

1. O sistema inicia desligado e aguarda o pressionamento do botão.
2. Ao ligar, entra no estado `SEGURO`.
3. A cada 2 segundos, uma leitura do sensor e processada.
4. A média das últimas 5 leituras e usada para reduzir ruido.
5. Conforme o valor médio, o sistema muda para `SEGURO`, `ALERTA` ou `PERIGO`.
6. Em estado de perigo, o buzzer e acionado.
7. Ao desligar, o histórico recente de logs e exibido no Serial Monitor.

## Saída serial

O sistema publica os dados em JSON, facilitando integração com outras ferramentas:

```json
{
  "device_id": "gas_sensor_01",
  "ts": 12345,
  "estado": "ALERTA",
  "gas_raw": 1800,
  "gas_avg": 1620,
  "erros": 0
}
```

## Como simular

1. Acesse o projeto no Wokwi:

   [https://wokwi.com/projects/462937229989048321](https://wokwi.com/projects/462937229989048321)

2. Inicie a simulação.
3. Pressione o botão para ligar o sistema.
4. Acompanhe os LEDs, o buzzer e o Serial Monitor.

## Estrutura do projeto

```text
.
├── sketch.ino
├── diagram.json
└── README.md
```

| Arquivo | Descrição |
| --- | --- |
| `sketch.ino` | Código principal do sistema embarcado |
| `diagram.json` | Circuito usado na simulação do Wokwi |
| `README.md` | Documentação do projeto |

## Observação para uso em hardware real

Na simulação, a leitura do sensor e gerada com valores aleatórios:

```cpp
leituraAtual = random(0, 4096);
```

Para montar o sistema fisicamente, substitua essa linha por:

```cpp
leituraAtual = analogRead(SENSOR);
```

Depois disso, calibre os limiares de acordo com o sensor utilizado, o ambiente de teste e a faixa de leitura obtida no Serial Monitor.

## Possíveis melhorias

- Adicionar display OLED ou LCD para exibir o estado localmente.
- Enviar alertas por Wi-Fi usando MQTT, HTTP ou Telegram.
- Criar dashboard para histórico das leituras.
- Salvar os logs em banco de dados.
- Implementar calibração automática do sensor.
- Adicionar documentação com fotos ou esquema elétrico do circuito físico.

## Autor

Desenvolvido por **Mati Limas** e **Arcanjowz** como projeto sobre sistemas ciberfísicos.

---

<p align="center">
  Projeto acadêmico de detecção de gás inflamável com ESP32, C++ e Wokwi.
</p>

