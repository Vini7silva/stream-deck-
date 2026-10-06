# stream-deck-

o codigo para quem quer saber como foi o metodo utilizado no desenvolvimento do strem deck 9 botões , e esp32c3 nano 

// Definição dos GPIOs para os 9 botões (Pinos estáveis validados por si)
const int botoes[] = {1, 3, 4, 5, 6, 7, 8, 9, 10};

// Estado anterior dos botões para evitar repetições contínuas no fluxo serial
bool estadoAnterior[] = {HIGH, HIGH, HIGH, HIGH, HIGH, HIGH, HIGH, HIGH, HIGH};

void setup() {
  // Inicializa o canal USB Serial nativo na velocidade padrão de 115200 bps
  Serial.begin(115200);
  delay(500);

  // Configura os pinos dos botões como entrada utilizando o Pull-up interno do chip
  // O pino vai para LOW quando o botão físico conecta o pino diretamente ao GND
  for (int i = 0; i < 9; i++) {
    pinMode(botoes[i], INPUT_PULLUP);
  }
}

void loop() {
  // Varredura contínua dos 9 botões físicos
  for (int i = 0; i < 9; i++) {
    bool estadoAtual = digitalRead(botoes[i]);

    // Detecta a transição exata de SOLTO (HIGH) para PRESSIONADO (LOW)
    if (estadoAtual == LOW && estadoAnterior[i] == HIGH) {
      // Envia a string padrão que o software no Windows está à espera de ler
      Serial.print("BOTAO_");
      Serial.println(i + 1);
      
      // Debounce estável para evitar cliques duplos por ruído elétrico
      delay(150); 
    }
    
    // Atualiza o histórico de estados para a próxima varredura
    estadoAnterior[i] = estadoAtual;
  }
  
  // Pequena pausa de ciclo para garantir a estabilidade térmica e de CPU do ESP32-C3
  delay(10);
}
