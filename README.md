
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
- APB: Arquitetura avançada de barramento de microcontrolador
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

Por fim temos o resumo das conexões com o cartão SD 

<img src="https://user-images.githubusercontent.com/86391684/127381590-86768873-9bd2-4205-8210-dc74dfc1990c.jpg" width="600" />

_____________________________________________________________________________________________________________________________________________

# Configuração da Plataforma

Descrição da configuração da plataforma atravéz do CubeMX integrado na IDE STM32Cube, configurações não especificadas estão definidas como padrão 
para geração do código.

A partir de agora já temos a ideia do que será feito, e podemos prosseguir para a configuração do microcontrolador. na área de trabalho inicial de configuração, precisamos adicionar como será feito a comunicação da plataforma e o computador, nesse caso pela linha serial USB 

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

  1. Abra a congiguração do GPIO em System Core > GPIO || Ao selecionar o pino PA0 abres as configurações
  2. Defina o *GPIO mode* como modo de interrupção externa com detecção de disparo de borda ascendente || Assim temos a interrupção logo ao precionar o botão
  3. Em GPIO Pull-up/Pull-down defina como Pull-down || É definido como Pull-down pois, como é visto no esquemático do botão, o pino de entrada no MCU está em nível lógico baixo por padrão (quando o botão não está pressionado)
  4. Logo acima temos a aba NVIC va até ela e selecione enable || NVIC dá um controle para priorizar interrupções, além de outras utilidades

![image](https://user-images.githubusercontent.com/86391684/127388241-087ed066-0307-4326-aedc-65c4e6b2240e.png)

  Em System Core > NVIC temos as configurações de prioridades de interrupções, nelas foi deixado com prioridade maior (valor numérico menor) as interrupções internas do sistema, por seguinte foram configuradas as demais conformem surgissem no código.
  
  ![image](https://user-images.githubusercontent.com/86391684/127602243-9f93cdfa-7ec5-49da-afd3-ce8f35677258.png)

  obs.: se não configurado isso, o código fica preso dentro de funções da biblioteca HALL, quando tais funções forem chamadas dentro de alguma rotina de tratamento de interrupção. Este erro é relatado em fóruns, e deverá ser explicado melhor aqui quando se obter uma resposta definitiva.


A configuração da recepçãos dos dados vindo do potenciômetro vem a seguir

  1. Definimos o pino PA6 como *ADC1_IN6*
  2. Seguinte vá em System Core > DMA > Na aba DMA2 clique em *ADD*
  3. Adicione o ADC1 ao DMA e configure em Mode > Circular || Desse modo temos o conversor AD captando e convertendo dados e o DMA colocando os dados diretamente na memória em um *buffer*, o modo circular faz com que quando o buffer estiver cheio de dados, o DMA volte a enchelo pelo início
  4. Em analog > ADC1 > Parameter Settings é possível ver as configurações do ADC, selecione as opções abaixo
     - Em *Clock Prescaler* selecione *PCLK2 divided by 8* || Assim teremos a derivação do clock de PCLK2 divido por 8
     - *Resolution* selecione *12 bits* ||com 12 bits ao variar o potenciômentro, variamos os dados de 0 até 4095 em decimal
     - Habilite *Continuous conversion mode* 
     - Habilite *DMA Continuous Rquests*
     - em *Rank* temos *Sampling time* selecione *480 Cycles* || Define a quantidade de ciclos para a amostragem de um sinal

![image](https://user-images.githubusercontent.com/86391684/127425182-ffa2fd9f-d58e-4472-be13-1da318a9d9c5.png)

O clock que vai pro ADC vem de uma derivação do clock do APB2 que vem do PCLK2 para esse experimento definimos o clock do APB2 como 8MHz

  5. Na guia *Clock Configuration* coloque o valor de 8 MHz no *APB2 peripheral clocks* e aperte "enter" o CUBE IDE automaticamente calculará os valores ideais para o restante

![Localização do APB2 em clock configuration](https://user-images.githubusercontent.com/86391684/127427956-646cda87-c0dd-4a1a-ba95-0be8eb5bc31b.jpg)

A diminuição do clock do PCLK2, a divisão do PCLK2 por 8 e o aumento de ciclos de amostragem são ferramentas para aumentar o tempo em que um dado é gerado pelo ADC, isso é feito pois não necessitamos de uma grande quantidades de dados por segundo vindos do ADC. Por padrão o ADC necessecita de 12 ciclos de clock para a captação do dado em 12 bits, com o clock de 8MHz, em PCLK2 utilizamos o *prescaler* para diminuir essa velocidade para 1MHz: 

12 bits: 1000Hz/(12 ciclos de conversão + 480 ciclos de captação) => ~2amostras/s (o que esperava ter)

Por fim temos a configuração do Conector SD

  1. Volte para *Pinout & Configuration*
  2. Vá até connectivity > SDMMC2 > Mode e selecione *SD 4 bits Wide bus*
  3. Em *configuration* clique na aba *NVIC Settings* e habilite o *SDMMC2 global interrrupt*
  
![image](https://user-images.githubusercontent.com/86391684/127430677-a8ef86ef-1941-403d-9603-80e9344ac993.png)

  4. Ainda em *SDMMC2 configuration* clique na aba *DMA Settings* e em *Add* e adicione TX e o RX do SDMMC2 no DMA

![image](https://user-images.githubusercontent.com/86391684/127431076-b5a8be0a-f04e-4ada-a328-5a3538c9f1e6.png)

  5. definimos então o pino PI15, de detecção do cartão SD, como entrada (GPIO_Input)
  6. Subsequentemente vá até Middleware > FATFS > em *Mode* marque *SD card*
  7. Em *configuration* clique na aba *Platform Settings*, em Detect_SDIO, selecione o PI15 na coluna *Found Solutions*

![image](https://user-images.githubusercontent.com/86391684/127433019-51ce4260-dd97-4f6a-807e-8b25e32cc642.png)

  8. Na guia *Advanced Settings*, habilete *Use dma template*

![image](https://user-images.githubusercontent.com/86391684/127433681-ce2c05b3-d2c0-4770-9015-576ebedb3128.png)

  9. Na aba *Project Manager* recomendado alterar o *Minimum Heap Size* para "0x400" e o *Minimum Stack Size* para "0x800"

![image](https://user-images.githubusercontent.com/86391684/127434000-5a883465-548c-403b-8e10-6d33d6a4ea3b.png)

Assim finalizamos a configuração da plataforma. Podemos então salvar o projeto e gerar o código inicial, é possivel salvar o projeto atravéz do ícone de disquete :floppy_disk: ou pelo atalho "Ctrl+S" ou pela guia File > Save.

_____________________________________________________________________________________________________________________________________________

### Programação 

Dentro do *main* a única função executada é a de iniciar as requisições do ADC com DMA:
'HAL_ADC_Start_DMA(&hadc1,(uint32_t*)adc_buf, ADC_BUF_LEN);'
nela é passado a estrutura do ADC 'hadc1', um ponteiro para o *buffer* utilizado para salvar as requisições 'adc_buf', e o tamanho desse *buffer* 'ADC_BUF_LEN' que no caso foi utilizado de 512 lugares. Apesar de termos os 512 posições com dados, nem todos esses dados serão eviadas pela serial ou salvados no catão SD, acontece que as derivações de *clock* não foram suficientes em diminuir a velocidade de requisições do ADC, isso acaba sendo inviável logo que não queremos muitos dados sendo expostos constamentes pela serial do computador, impossibilitando a visualização suave das informações.

o código então se resume em três funções:

'HAL_ADC_ConvHalfCpltCallback' e 'HAL_ADC_ConvCpltCallback' são funções de interrupção do ADC. 'HAL_GPIO_EXTI_Callback' é uma função também de interrupção, só que de interrupção externa, ela é a rotina de tratamento da interrupção gerada pelo botão do usuário.

- 'HAL_ADC_ConvHalfCpltCallback' é chamada toda vez que o DMA completar a metade do *buffer* do ADC de dados.
   - Nessa função é pego o valor da metade do *buffer*, ou seja da posição 256, e enviado pela porta serial para o computador, o restante da amostra é descartado, ou seja, não é feito nada com as 255 posições iniciais.
- 'HAL_ADC_ConvCpltCallback' é chamada toda vez que o DMA completar totalmente o *buffer* do ADC de dados.
   - Nessa função é pego o valor da última posição do *buffer*, ou seja da posição 512, e enviado da mesma forma para o monitor serial

Dessa forma estamos pegando um dado a cada 256 posições. Vale resaltar que o DMA utilizando é circular, então ao preencher todos os 512 lugares de dados, o DMA volta pro inicio do *buffer* e repete o processo. Portanto temos uma espécide de *buffer ping pong*, enquanto o DMA preenche metade do buffer, é executado uma ação na outra metade.

- 'HAL_GPIO_EXTI_Callback' é chamada toda vez que é apertado o botão do usuário. Nela temos o 'controll_gravacao' que basicamente registra se é o primeiro ou o segundo clique no botão, indicando início e fim de gravação respectivamente. No primeiro clique, a função condicional *if* se torna verdadeira, nela é executada as funções de configurações e preparação para o catão SD receber dados. A partir de então a função é finalizada e o SD esta pronto para receber dados. Como a variável 'controll_gravacao' foi alterada pelo clique, temos uma reação em cadeia que as funções 'HAL_ADC_ConvHalfCpltCallback' e 'HAL_ADC_ConvCpltCallback' passam a enviar dados tanto para o monitor serial quanto para o cartão SD.
  Ao clicar no botão novamente, temos a alteração na variável de controle, e a função condicional *if* é negada, executando o fechamento do arquivo do cartão SD, agora as funçoes de interrupção gerada pelo ADC não enviam dados ao cartão SD.
 
 Extra: foi criado uma função para a geração de um arquivo separado no cartão SD para cada "segundo clique" no botão do usuário, ou seja, fazer duas gravações de dados numa mesma execução do código acaba não substituindo o arquivo anterior no cartão SD.
 
_____________________________________________________________________________________________________________________________________________

# Conclusões e observações

Para a visualização dos dados no computador é necessário um monitor serial configurado corretamente, com base no *baund rate* padrão da configuração da USART1, 115200 bits/s, configuramos o monitor serial, é preciso confirmar a porta COM conectado ao MCU em "Gerenciador de Dispositivos". A seguir a configuração no monitor serial PuTTY

![image](https://user-images.githubusercontent.com/86391684/127731002-53de3e09-afb4-48e6-a8e2-6eea8215a259.png)

Executando o *Firmware* pode-se abrir o monitor serial

viazualize os dados de tensão na tela do computador 

imagem

Apertando o botão é apresentada as mensagens de configuração do cartão SD demarcando o inicio da gravação

imagem

apertando o botão novamente surge a mensagem "Fim da gravação!!!"

### visualização dos arquivos no MicroSD

Plugando o MicroSD no computador é possivel visualizar os arquivos ".txt" gerados, o número de arquivos depende da quantidade de gravações realizadas, na imagem foram três

![image](https://user-images.githubusercontent.com/86391684/127731505-efde0e88-8f9c-403a-805c-ffadb989ec91.png)

Como visto, dentro dos arquivos temos os dados salvos em uma coluna.


### visualização dos dados no Matlab

É possivel, atravéz do Matlab, visualizar os dados em um gráfico

Salve o arquivo desejado em uma pasta em seu *desktop*. No matlab crie um *New Script* e salve-o na mesma pasta do arquivo, na área de programação digite o seguinte código fazendo os devidos ajustes:

close all;
clear all;
data = importdata('DADOS1.TXT');
figure ()
plot(data.data)
ylabel('Tensão Potenciômetro')
xlabel('Número de amostras')
legend('Tensão')
title ('Teste do conversor AD')

Ao rodar este código será gerado um gráfico de tensões por número de amostras

![image](https://user-images.githubusercontent.com/86391684/127731451-2bb081dd-41eb-4853-b542-99c168f4b476.png)

_____________________________________________________________________________________________________________________________________________
## Referências 

 - [Exemplos de programas utilizando potenciometro, interrupção, DMA e ADC](https://www.digikey.com/en/maker/projects/getting-started-with-stm32-working-with-adc-and-dma/f5009db3a3ed4370acaf545a3370c30c)
 - [Criando arquivo de sistema no cartão SD](https://www.youtube.com/watch?v=I9KDN1o6924)
 - [Exeplo de interrupção utilizando o botão do usuário](https://deepbluembedded.com/stm32-external-interrupt-example-lab/)
 - [Cartão SD utilizando cominição SPI, demonstração de funções inportantes](https://www.youtube.com/watch?v=spVIZO-jbxE)
 - [Descrição das funções FATFS utilizadas no programa](http://elm-chan.org/fsw/ff/00index_e.html)
 
