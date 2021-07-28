
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
- GPIO: portas programáveis de entrada e saída de dados
- NVIC: controle de interrupção de vetor aninhado 

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

<img src="https://user-images.githubusercontent.com/86391684/127261745-bb34e31f-0713-4224-a7e9-009da1ecf3c9.jpg" width="600" />

Na imagem a seguir temos as entradas analógicas, dentre elas foi escolhido a PA6 onde o pino do meio do potenciômentro será conectado

<img src="https://user-images.githubusercontent.com/86391684/127262804-e8efc0b8-b038-421f-99c1-7cc9c15f3d94.png" width="700" />

Para a **Capitação dos dados** será configurado um Conversor Analógico Digital (ADC) juntamente com o Acesso Direto a Memória (DMA), com o DMA temos os dados sendo colocados diretamente em um *Buffer* na memória, desafogando a unidade central de processamento (CPU). O conversor escolhido foi o ADC1 logo é importante localizá-lo no mapa de requisição DMA

<img src="https://user-images.githubusercontent.com/86391684/127264865-e4e83b8d-6136-45db-a6d6-f0d360c1350e.png" width="700" />

Como é visto o ADC1 se localiza no DMA2 e pode ser usado em *Stream 0* ou *Stream 4*

Um caso secundário da **captação dos dados** é quando eles serão salvos no SD Card, para isto será configurado uma interrupção com o botão de usuário, que ao clicar no botão, marcará o inicio do salvamento dos dados no cartão SD e ao clicar novamente encerrará o salvamento. Abaixo o esquemático do botão e suas relações juntamente com o resumo no diagrama do experimento

<img src="https://user-images.githubusercontent.com/86391684/127268635-0781ec76-2a03-4613-9c2c-26d91f15dc5f.png" width="500" />
<img src="https://user-images.githubusercontent.com/86391684/127268665-ee1ffd0e-fb1d-413d-918d-160f955149cb.png" width="500" />
<img src="https://user-images.githubusercontent.com/86391684/127268811-ce651b7c-2ec3-4ff8-a8f4-f4f116b3d742.jpg" width="600" />

Fica claro a coneção do botão com o pino PA0

Por fim temos a parte de **visualização dos dados**, esta seção também será dividida em duas partes. Na primeira parte teremos a visualização conínua dos dados no monitor serial, para isto foi instalado o monittor [PuTTY](https://www.putty.org/) no computador. esta comunicação entre o microcontrolador e o computador será dada pela ST_Link que faz a simulação de uma porta serial, o protocolo utilizado é a USART e o caminho percorrido pelos dados é descrito a seguir

<img src="https://user-images.githubusercontent.com/86391684/127372087-f8fa9f26-edf5-4544-b5ec-a6e0744a8a37.png" width="500" />

Como queremos enviar dados para o STLink, utilizamos o caminho pelo VCP_TX, que por sua vez é conectado ao pino PA9 do MCU

<img src="https://user-images.githubusercontent.com/86391684/127372252-29d7354b-f085-4e9f-b9ea-2241db716919.png" width="500" />

Na figura do diagrama do experimento temos um resumo disto

<img src="https://user-images.githubusercontent.com/86391684/127372699-6bcf66ca-a996-46f2-8a0a-394d33cd1a5f.jpg" width="600" />

Na segunta parte da **visualização dos dados** temos o SD Card, o tipo compativel com a conector no kit de desenvolvimento é o MicroSD e esta localizado conforme a figura a seguir

<img src="https://user-images.githubusercontent.com/86391684/127378549-5ff69932-7288-4335-a984-7fd8d9345979.jpg" width="650" />

No MicroSD será inserido um arquivo no formato .TXT, que por sua vez será inserido os dados com o auxílio, como já falado, do botão do usuário, A seguir temos a imagem do esquemático do cartão SD

<img src="https://user-images.githubusercontent.com/86391684/127378718-bc18c094-0e21-479a-a403-b7812d42cfb2.png" width="400" />

na parte superior do esquemático temos, além das alimentações, as linhas de comunicação com o conector, essa interface com o catão SD é dado pelo periférico SDMMC, na parte de baixo da imagem temos o uSD_Detect, linha responsável pela detecção do MicroSD conectado as *slot*, uSD_Detect esta conectado ao pino PI15 do MCU e deve ser configurado como GPIO_Input

<img src="https://user-images.githubusercontent.com/86391684/127379861-4ada9714-4355-4e5c-a973-0a2765a3a742.png" width="650" />

Por fim temos o resumo das conecções com o cartão SD 

<img src="https://user-images.githubusercontent.com/86391684/127381590-86768873-9bd2-4205-8210-dc74dfc1990c.jpg" width="600" />

_____________________________________________________________________________________________________________________________________________

# Configuração da Plataforma

Descrição da configuração da plataforma atravéz do CubeMX integrado na IDE STM32Cube, configurações não especificadas estão definidas como padrão 
para geração do código.

A partir de agora já temos a idéia do que será feito, e podemos prosseguir para a configuração do microcontrolador. na área de trabalho inicial de configuração, precisamos adicionar como será feito a comunicação da plataforma e o computador, nesse caso pela linha serial USB 

  1. Na guia "System Core" clique em SYS > Debug > Serial Wire

<img src="https://user-images.githubusercontent.com/86391684/124537839-070bc200-ddf1-11eb-8ccb-d9601a419d6e.png" width="800" />

É preciso definir também uma fonte de *clock* para o *hardware*, foi utilizado o circuito oscilador externo HSE 

  2. Ainda em "System Core" vá em RCC
  3. Habilite o "High Speed Clock (HSE)" Como "Crystal/Ceramic Resonator"

<img src="https://user-images.githubusercontent.com/86391684/124539281-869a9080-ddf3-11eb-9816-2cb537f7c4df.png" width="800" />

Partimos então para a configuração dos periféricos do experimento, a começar pela comunicação com o monitor serial do computador 

  1. Definimos então o pino PA9 como USART1_TX 
  2. Em *connectivity* vamos ate USART1 > Mode > Asynchronous

![image](https://user-images.githubusercontent.com/86391684/127383180-12e76951-72c9-46a4-b59c-b7e6ced8d5f6.png)

Na cofinguração do botão do usuário vamos definir o pino PA0 como GPIO_EXTI0

  1. Abra a congiguração do GPIO em System Core > GPIO || Ao selcionar o pino PA0 abres as configurações
  2. Defina o *GPIO mode* como modo de interrupção externa com detecção de disparo de borda ascendente || Assim temos a interrupção logo ao precionar o botão
  3. Em GPIO Pull-up/Pull-down defina como Pull-down || é definido como Pull-down pois, como é visto no esquemático do botão, o pino de entrada no MCU está em nível lógico baixo por padrão (quando o botão não está pressionado)
  4. Logo acima temos a aba NVIC va até ela e selecione enable || NVIC dá um controle para priorizar interrupções, além de outras utilidades, (no momento não implementado)

![image](https://user-images.githubusercontent.com/86391684/127388241-087ed066-0307-4326-aedc-65c4e6b2240e.png)

A configuração da recepçãos dos dados vindo do potenciômetro vem a seguir

  1. Definimos o pino PA6 como *ADC1_IN6*
  2. Seguinte vá em System Core > DMA > Na aba DMA2 clique em *ADD*
  3. Adicione o ADC1 ao DMA e configure em Mode > Circular ||desse modo temos o conversor AD captando e convertendo dados e o DMA colocando os dados diretamente na memória em um *buffer*, o modo circular faz com que quando o buffer estiver cheio de dados o DMA volte a enchelo pelo início
  4. Em analog > ADC1 > Parameter Settings é possivel ver as configurações do ADC, selecione as opções abaixo
     - Em *Clock Prescaler* selecione *PCLK2 divided by 8* ||assim teremos a derivação do clock de PCLK2 divido por 8
     - *Resolution* selecione *12 bits* ||com 12 bits ao variar o potenciômentro, variamos os dados de 0 até 4095 em decimal
     - Habilite *Continuous conversion mode* 
     - Habulite *DMA Continuous Rquests*
     - em *Rank* temos *Sampling time* selecione *480 Cycles* || define a quantidade de ciclos para a amostragem de um sinal
colocar imagem da configuração 

falar sobre a arvore de clock

colocar imagem da arvore de clock

A diminuição do clock do PCLK2, a divisão do PCLK2 por 8 e o aumento de ciclos de amostragem são ferramentas para aumentar o tempo em que um dado é gerado pelo ADC, isso é feito pois não necessitamos de uma grande quantidades de dados por segundo vindos do ADC. Por padrão o ADC necessecita de 12 ciclos de clock para a captação do dado em 12 bits, com o clock de 8MHz em PCLK2 utilizamos o *prescaler* para diminuir essa velocidade para 1MHz: 

12 bits: 1000Hz/(12 ciclos de conversão + 480 ciclos de captação) => ~2amostras/s (o que espeva ter)


