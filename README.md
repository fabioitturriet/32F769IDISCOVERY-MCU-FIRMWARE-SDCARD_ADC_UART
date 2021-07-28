
# Experimento 02 - SDCard-ADC-UART
_____________________________________________________________________________________________________________________________________________
**Autores:** Patrick Morás e Fábio Itturriet  
**Data:** 29/07/2021           
**Objetivo:** Esse projeto visa a configuração da plataforma STM32F769NI utilizando alguns dispositivos como ADC, DMA e UART para salvar dados, provenientes de um divisor de tensão variável, em um cartão de memória MicroSD.                       
**IDE=** STM32CUBE IDE v.1.6.1

_____________________________________________________________________________________________________________________________________________

# Esquemático do Experimento

Diagrama geral do projeto

<img src="https://user-images.githubusercontent.com/86391684/124515737-8a5bf200-ddb6-11eb-902a-e5e8262e6ec3.jpg" width="900" />

Diagrama específico do experimento

<img src="https://user-images.githubusercontent.com/86391684/127251673-4c74b70a-9d69-4d62-8189-6f50979698ed.png" width="900" />

_____________________________________________________________________________________________________________________________________________

# Abreviações

- DMA: controlador de acesso direto à memória em STM32
- UART: transmissor receptor assíncrono universal
- USART: Transmissor receptor assíncrono síncrono universal
- TX: Transmitir
- IRQ: Interrupção
- ADC: Conversor analógico-digital 
- MCU: Microcontrolador
- GND: filtro graduado de densidade neutra ("Terra")

_____________________________________________________________________________________________________________________________________________

### Começando um novo projeto

Inicialmente reproduzimos o STM32CubeIDE, na área de trabalho do Cube IDE criamos um novo projeto
  
  1. Vá até a guia File > New > STM32 Project

<img src="https://user-images.githubusercontent.com/86391684/124516208-87adcc80-ddb7-11eb-8ec8-adc3c2a376dc.png" width="800" />

  2. Aberta a janela de seleção de plataformas "Target selection"
  3. Escolha a plataforma de desenvolvimento a ser utilizada, nesse caso a STM32F769I-DISCO

<img src="https://user-images.githubusercontent.com/86391684/124516617-7d400280-ddb8-11eb-8277-5a0868bb48db.png" width="800" />

  4. Na janela de configuração, dê um nome ao seu projeto e prossiga com "Finish"

<img src="https://user-images.githubusercontent.com/86391684/127256920-8c09eaea-b113-44b8-a81c-1eb886711790.png" width="400" />

### Descrição do experimento

Como visto no objetivo deste experimento, a ideia é gerar dados análogicos, converte-los com o auxílio do ADC, salva-los e visualizá-los.
Fica claro então a divisão entre as partes do experimento que nos auxilia para descreve-lo:

1. Geração dos dados
2. Captação dos dados 
3. Visualização dos dados

Na **geração dos dados** temos um potenciômetro com seus pinos das extremidades conectados ao 3.3V e ao GND

<img src="https://user-images.githubusercontent.com/86391684/127261745-bb34e31f-0713-4224-a7e9-009da1ecf3c9.jpg" width="750" />

Na imagem a seguir temos as entradas analógicas, dentre elas foi escolhido a PA6 onde o pino do meio do potenciômentro será conectado

<img src="https://user-images.githubusercontent.com/86391684/127262804-e8efc0b8-b038-421f-99c1-7cc9c15f3d94.png" width="700" />

Para a **Capitação dos dados** será configurado um Conversor Analógico Digital (ADC) juntamente com o Acesso Direto a Memória (DMA), com o DMA temos os dados sendo colocados diretamente em um *Buffer* na memória, desafogando a unidade central de processamento (CPU). O conversor escolhido foi o ADC1 logo é importante localizá-lo no mapa de requisição DMA

<img src="https://user-images.githubusercontent.com/86391684/127264865-e4e83b8d-6136-45db-a6d6-f0d360c1350e.png" width="700" />

Como é visto o ADC1 se localiza no DMA2 e pode ser usado em *Stream 0* ou *Stream 4*

Um caso secundário da **captação dos dados** é quando eles serão salvos no SD Card, para isto será configurado uma interrupção com o botão de usuário, que ao clicar no botão, marcará o inicio do salvamento dos dados no cartão SD e ao clicar novamente encerrará o salvamento. Abaixo o esquemático do botão e suas relações juntamente com o resumo no diagrama do experimento

<img src="https://user-images.githubusercontent.com/86391684/127268635-0781ec76-2a03-4613-9c2c-26d91f15dc5f.png" width="500" />
<img src="https://user-images.githubusercontent.com/86391684/127268665-ee1ffd0e-fb1d-413d-918d-160f955149cb.png" width="500" />
<img src="https://user-images.githubusercontent.com/86391684/127268811-ce651b7c-2ec3-4ff8-a8f4-f4f116b3d742.jpg" width="750" />

Fica claro a coneção do botão com o pino PA0

