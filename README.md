# STM32 Event-Driven Architecture: EXTI Interrupts & Software Timers

Este repositório contém a implementação de um projeto em C para microcontroladores STM32, com o objetivo de demonstrar o controle de hardware de baixo nível utilizando interrupções externas (EXTI), eliminando completamente o uso de *polling* para a leitura de entradas físicas.

O código foi desenvolvido para demonstrar duas abordagens diferentes de tratamento de interrupções (ISR - Interrupt Service Routine) em um ambiente de desenvolvimento profissional.

## Hardware e Ferramentas Utilizadas

* **Microcontrolador:** STM32F411CEU6 (Black Pill) - Arquitetura ARM Cortex-M4
* **Gravador/Debugger:** ST-Link V2
* **Periféricos Externos:** 2x Chaves Tácteis (Push-buttons) e 2x LEDs indicativos.
* **IDE:** STM32CubeIDE v1.9 (Garantindo compatibilidade total na estrutura de pastas e HAL).

## Arquitetura do Software

O sistema foi configurado para responder a eventos na borda de descida (*Falling Edge*), ativando os resistores de *Pull-up* internos do STM32. Para evitar o bloqueio da CPU com funções como `HAL_Delay()` na detecção, o projeto foi dividido em duas lógicas de resposta:

### 1. Resposta Imediata (Botão 1 - Pino PA1)
* **Conceito:** Ação atômica resolvida 100% dentro da ISR.
* **Funcionamento:** Quando a interrupção `EXTI1` é disparada, o Callback executa a função `HAL_GPIO_TogglePin`, invertendo o estado lógico do LED 1 em tempo real e retornando a CPU para o loop principal imediatamente.

### 2. Resposta Diferida / Sinalização (Botão 2 - Pino PA2)
* **Conceito:** Uso de *Flags* globais e temporizador de software não-bloqueante.
* **Funcionamento:** A interrupção `EXTI2` acende o LED 2 e altera o valor de uma Flag global (`Bot_2 = 1`). A ISR é encerrada rapidamente. O `while(1)` identifica a Flag, inicia um contador (`cnt_Bot_2`) e gerencia o tempo (1 segundo) para desligar o LED, mantendo o sistema responsivo durante o processo.

## Como executar

1. Clone este repositório.
2. Importe o projeto no STM32CubeIDE (Recomendado v1.9).
3. Verifique as configurações de pinagem no arquivo `.ioc`:
   - `PA1` configurado como `GPIO_EXTI1` (Pull-up, Falling edge).
   - `PA2` configurado como `GPIO_EXTI2` (Pull-up, Falling edge).
   - `PB0` e `PB1` configurados como `GPIO_Output`.
4. Compile o projeto e faça o upload para a placa via ST-Link.

## Próximos Passos (Roadmap)
- [ ] Substituir o temporizador de software do Botão 2 por **Hardware Timers** internos do STM32, eliminando qualquer atraso residual no loop principal.
- [ ] Implementar rotina de *Debounce* por hardware.
