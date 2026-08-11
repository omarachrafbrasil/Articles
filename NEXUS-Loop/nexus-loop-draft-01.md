<!--
File:        NEXUS-LOOP.md
Author(s):   Omar Achraf     / omar@uvsbr.com.br / omarachraf@gmail.com
Date:        2026-06-10      Updated: 2026-06-10
Version:     2.0
Description: Especificação Técnica de Arquitetura, Engenharia de Software
             e Modelo de Negócios do ecossistema NEXUS-LOOP.
             Framework Universal de Co-Simulação e Carga Útil Sintética
             para Gêmeos Digitais Interativos (X-in-the-Loop).
             Inclui enquadramento no edital FINEP Mais Inovação Brasil
             Rodada 2 — Mobilidade Sustentável (TRL 3 a 7).

Copyright:   © 2026 UVSBR — Unmanned Vehicle Systems do Brasil
             Todos os direitos reservados.
             Propriedade intelectual de Omar Achraf / UVSBR.
             Reprodução, distribuição ou uso comercial total ou parcial
             deste documento somente mediante autorização expressa e
             por escrito do titular.
-->

# NEXUS-LOOP: Framework Universal de Co-Simulação e Carga Útil Sintética

**Subtítulo:** Framework Universal de Simulação e Carga Útil para Gêmeos Digitais Interativos (X-in-the-Loop)

**Especificação Técnica de Arquitetura, Engenharia de Software e Modelo de Negócios — Professional Services**

**Versão:** 2.0 | **Data:** 2026-06-10

---

> **© 2026 UVSBR — Unmanned Vehicle Systems do Brasil**
> Todos os direitos reservados. Este documento e o ecossistema NEXUS-LOOP nele descrito constituem propriedade intelectual exclusiva de **Omar Achraf / UVSBR**.
> Reprodução, distribuição, licenciamento ou uso comercial — total ou parcial — somente mediante autorização expressa e por escrito do titular.
>
> **Contato:** omar@uvsbr.com.br · omarachraf@gmail.com · +55 41 9 9965-5395
> **Web:** https://uvsbr.com.br

---

## Sumário

- [0. Sumário Executivo](#0-sumário-executivo)
- [1. Glossário Técnico Avançado](#1-glossário-técnico-avançado)
- [2. Introdução, Contexto de Mercado e Propósito do Sistema](#2-introdução-contexto-de-mercado-e-propósito-do-sistema)
- [3. Arquitetura do Ecossistema em Camadas](#3-arquitetura-do-ecossistema-em-camadas)
- [4. Mecânica de Implementação HIL: Software vs Hardware Físico](#4-mecânica-de-implementação-hil-software-vs-hardware-físico)
- [5. Mecânica Lógica do Protocolo e Blindagem de Rede/Serial no Windows](#5-mecânica-lógica-do-protocolo-e-blindagem-de-redeserial-no-windows)
- [6. Módulo de Emulação de Sensores Sintéticos e Física Climática Integrada](#6-módulo-de-emulação-de-sensores-sintéticos-e-física-climática-integrada)
- [7. Módulo de Carga Útil Visual, Térmica FLIR e Integração de Viewports](#7-módulo-de-carga-útil-visual-térmica-flir-e-integração-de-viewports)
- [8. Módulos de Sensores Complexos LIDAR e RADAR e Sistemas Táticos de Armas](#8-módulos-de-sensores-complexos-lidar-e-radar-e-sistemas-táticos-de-armas)
- [9. Expansão do Hub Universal para Outros Domínios de Veículos](#9-expansão-do-hub-universal-para-outros-domínios-de-veículos)
- [10. Arquitetura de Simulação em Enxame e Inteligência Distribuída](#10-arquitetura-de-simulação-em-enxame-e-inteligência-distribuída)
- [11. Módulo de Auditoria, Depuração e Sniffing Open-Source](#11-módulo-de-auditoria-depuração-e-sniffing-open-source)
- [12. Estruturação Comercial, Precificação e Professional Services](#12-estruturação-comercial-precificação-e-professional-services)
- [13. Enquadramento em Editais de Subvenção Econômica FINEP Mais Inovação](#13-enquadramento-em-editais-de-subvenção-econômica-finep-mais-inovação)
- [Referências e Plataformas Padrão](#referências-e-plataformas-padrão)

---

## 0. Sumário Executivo

### O Problema

O desenvolvimento de veículos autônomos, sistemas aviônicos e gimbals de inspeção industrial no Brasil enfrenta uma barreira dupla: de um lado, softwares corporativos de simulação como Ansys Autonomy e dSPACE custam centenas de milhares de reais por licença anual e são trancados por restrições de exportação de defesa; do outro, ferramentas de código aberto como ArduPilot/PX4 estão presas aos próprios firmwares e não fornecem ganchos limpos para empresas que projetam hardware próprio. O resultado prático é que integradores nacionais constroem protótipos caros e arriscados prematuramente, realizam testes de campo dispendiosos para descobrir bugs de software, e perdem meses e recursos que poderiam ser gastos no desenvolvimento do produto final.

### A Solução

O **NEXUS-LOOP** é um framework de software brasileiro de co-simulação e carga útil sintética que atua como plataforma de engenharia habilitadora para o ciclo completo de validação X-in-the-Loop (MIL → SIL → HIL). Ele conecta simuladores comerciais de física de alto nível (X-Plane 12, DCS World, Gazebo, BeamNG e outros) a uma camada central de injeção de ruído, falhas estocásticas e geração de payloads sintéticos (câmera FLIR, LIDAR, RADAR, sonar), entregando ao hardware ou software de controle do integrador exatamente o que um sensor real entregaria no mundo físico — incluindo imperfeições, falhas e degradações climáticas — sem que o integrador precise construir um único protótipo físico para a fase de validação de software.

### Proposta de Valor Central

- **Elimina protótipos físicos prematuros:** o hardware do cliente valida seu firmware contra sensores sintéticos de alta fidelidade na bancada de laboratório antes de qualquer voo ou navegação real.
- **Independência de simulador:** a arquitetura em camadas (Adapter/Wrapper) permite trocar o simulador de fundo sem alterar uma linha do código de controle do cliente.
- **Protocolo binário unificado:** o mesmo pacote de bytes com SessionID, SequenceID e CRC trafega via UDP, porta COM ou CAN Bus, garantindo determinismo temporal e rastreabilidade total.
- **Geração oculta de payload:** imagens visuais e térmicas sintéticas baseadas em Google Maps 3D com Shaders WebGL influenciados pelo clima em tempo real, sem que o cliente saiba ou precise se preocupar com a origem dos dados.
- **Suporte a enxames:** emulação de redes Ad-Hoc com raio de conectividade, atenuação de sinal por terreno, jitter de latência e saturação de banda para testar IAs de coordenação multi-agente.

### Mercado-Alvo

Indústrias de defesa nacional, fornecedoras da Petrobras (inspeção offshore, ROVs, drones), institutos de pesquisa aeronáutica (ITA, INPE, IPT), empresas de automação agrícola e logística de mineração, e qualquer integrador brasileiro que desenvolva firmware embarcado para veículos autônomos aéreos, marítimos, subaquáticos ou terrestres.

### Modelo de Negócios

O produto é comercializado como combinação de **licença de software** (DLL central + wrappers + Starter Kits) com **Professional Services** (consultoria associada de instalação, integração do modelo CAD do cliente no simulador, desenvolvimento de drivers customizados e treinamento). Valores de referência: licença core entre R$ 45.000 e R$ 80.000 por bancada; pacote de serviços entre R$ 30.000 e R$ 60.000 por projeto; suporte anual entre R$ 15.000 e R$ 35.000.

### Enquadramento em Fomento Público

O NEXUS-LOOP é a infraestrutura de pesquisa aplicada que permite a um projeto de mobilidade autônoma — aérea, aquaviária ou metroferroviária — transitar de forma segura e auditável entre TRL 3 (prova de conceito analítica em MIL) e TRL 7 (demonstração de protótipo em ambiente operacional representativo com HIL rígido), janela exata exigida pela **FINEP Mais Inovação Brasil — Rodada 2 — Mobilidade Sustentável**. A plataforma viabiliza a parceria com ICTs (universidades e centros de pesquisa) ao separar claramente o papel científico (algoritmos MIL/SIL) do papel industrial (embarque em hardware e certificação HIL), atendendo o Arranjo Simples com participação mínima de ICT de 5% do orçamento total.

---

## 1. Glossário Técnico Avançado

- **HIL (Hardware-in-the-Loop):** Metodologia de teste onde o hardware embarcado real do cliente é conectado a um ambiente simulado em tempo real, interagindo via sinais elétricos ou digitais como se estivesse operando no mundo físico.
- **SIL (Software-in-the-Loop):** Validação onde o código do firmware de controle é executado e testado de forma virtualizada diretamente no computador, sem a necessidade da placa física.
- **MIL (Model-in-the-Loop):** Simulação puramente matemática de algoritmos e blocos lógicos (ex: MATLAB/Simulink) rodando em co-simulação com o motor de dinâmica do veículo.
- **X-in-the-Loop:** Termo genérico que engloba as fases MIL, SIL e HIL de validação de sistemas complexos.
- **Gimbal:** Sistema eletromecânico motorizado que estabiliza e orienta cargas úteis de sensores nos eixos de Roll (Rolagem), Pitch (Arfagem) e Yaw (Guinada).
- **FLIR (Forward Looking Infrared):** Câmera térmica que detecta a radiação infravermelha emitida pelo calor dos corpos e materiais, convertendo-a em imagens visuais sintéticas.
- **LIDAR (Light Detection and Ranging):** Sensor óptico ativo que emite pulsos de laser de alta frequência e calcula o tempo de retorno para gerar nuvens de pontos tridimensionais do ambiente (Point Clouds).
- **Radar / Clutter:** Sensor de radiofrequência para detecção de alvos e medição de velocidades Doppler. Clutter é o ruído eletromagnético espúrio causado por reflexões indesejadas do solo ou gotas de chuva.
- **DataRef / CommandRef:** Variáveis estruturadas de memória do X-Plane 12 para leitura de estados físicos (DataRefs) ou injeção de comandos de controle (CommandRefs).
- **Export.lua:** API e ambiente de script nativo do DCS World para extrair dados cinemáticos de aeronaves e injetar comandos de atuação via linguagem Lua.
- **DXGI (Desktop Duplication API):** Interface do Windows de altíssima performance que captura buffers de tela diretamente da memória de vídeo (VRAM) da GPU, evitando gargalos de barramento com a CPU.
- **Vulkan / OpenGL:** APIs gráficas de renderização 3D. O X-Plane 12 utiliza Vulkan nativamente, exigindo plugins com instanciamento moderno (XPLMInstance) e janelas de sobreposição para evitar conflitos de barramento.
- **MTBF (Mean Time Between Failures):** Tempo Médio Entre Falhas — métrica estatística de confiabilidade que determina a frequência esperada de quebra de um sensor ou sistema.
- **MTTR (Mean Time To Repair):** Tempo Médio Para Reparo — tempo que um sensor leva para se restabelecer após uma falha.
- **Endianness:** Ordem em que os bytes de dados numéricos multibyte são armazenados na memória (Little-Endian ou Big-Endian). Deve ser documentada e tratada explicitamente com funções `htons`/`ntohs` para garantir interoperabilidade entre plataformas.
- **Slew Rate:** Velocidade máxima angular de rotação de um motor ou junta de gimbal, expressa em graus por segundo. Limita a velocidade de resposta do sensor ao comando do operador.
- **Jitter:** Variação estatística no tempo de chegada de pacotes de dados em uma rede, ou variação residual de alta frequência na estabilização física de um sensor.
- **SessionID:** Campo de 4 bytes (uint32) gerado via Unix Timestamp no início de cada sessão de simulação, usado para detectar reinicializações de hardware e limpar buffers obsoletos automaticamente.
- **SequenceID:** Campo de 8 bytes (uint64) incrementado a cada pacote, usado para descartar pacotes fora de ordem e detectar perdas de dados.
- **CRC (Cyclic Redundancy Check):** Campo de 2 bytes (uint16) no final de cada pacote para detectar corrupção de dados por ruído elétrico no cabo.
- **TRL (Technology Readiness Level):** Escala de 1 a 9 que mede a maturidade tecnológica de uma inovação. TRL 3 é prova de conceito analítica; TRL 7 é demonstração de protótipo em ambiente operacional relevante.
- **Pesquisa Aplicada:** Trabalhos teóricos ou práticos voltados à aquisição de novos conhecimentos direcionados a um objetivo prático e comercial específico, distinta da pesquisa básica (TRL 1–2).
- **Subvenção Econômica:** Modalidade de fomento público que concede recursos financeiros não reembolsáveis para empresas investirem em projetos de inovação tecnológica de alto risco.
- **ICT (Instituto de Ciência e Tecnologia):** Entidade sem fins lucrativos (universidades, centros de pesquisa) com missão de execução de atividades de pesquisa científica e tecnológica.
- **Contrapartida Financeira:** Aporte financeiro obrigatório da empresa proponente, aplicado no projeto como contrapartida ao recurso público recebido.

---

## 2. Introdução, Contexto de Mercado e Propósito do Sistema

O desenvolvimento de veículos autônomos, sistemas aviônicos de defesa e gimbals de inspeção industrial no Brasil esbarra em uma barreira crítica: o custo astronômico e o engessamento de softwares de simulação corporativos internacionais (como Ansys Autonomy, VRXPERIENCE ou dSPACE), cujas licenças anuais superam os seis dígitos por máquina e exigem contratos de importação complexos. Do outro lado, ferramentas gratuitas focadas em hobbistas (como o ecossistema básico do ArduPilot/PX4) estão presas aos próprios firmwares, falhando em fornecer ganchos de integração limpos para empresas que projetam hardware próprio do zero.

Este projeto estabelece um **Framework Universal de Simulação e Carga Útil Independente**, atuando como caixa preta de alta fidelidade baseada em engenharia de software de tempo real. O objetivo do sistema não é calibrar os microradianos dinâmicos dos motores de um sensor ou os perfis aerodinâmicos microscópicos de uma asa, mas sim fornecer uma infraestrutura computacional indestrutível para **validar a integração lógica e eletrônica de sistemas de controle**.

Ao utilizar simuladores maduros de mercado (como X-Plane 12 e DCS World) estritamente como "motores escravos de geração de mundo, balística e clima", e injetar uma camada unificada de corrupção estatística de dados, o framework resolve a maior dor dos integradores nacionais (indústrias de defesa, segurança e óleo & gás): **elimina a necessidade de construir protótipos físicos prematuros, acelera o Time-to-Market e corta os custos logísticos associados a campanhas de testes de campo arriscadas.**

O ecossistema não possui concorrente direto no mercado nacional. Softwares internacionais equivalentes (Ansys AVxcelerate, Presagis, AGI STK) custam centenas de milhares de dólares, não possuem integração nativa com X-Plane 12 ou DCS World, e são trancados por restrições de exportação de defesa. O NEXUS-LOOP democratiza essa infraestrutura para o integrador brasileiro, operando sobre simuladores comerciais de baixo custo já disponíveis no mercado.

---

## 3. Arquitetura do Ecossistema em Camadas

Para garantir que o núcleo de simulação de sensores e o modelo de controle do cliente permaneçam totalmente agnósticos em relação ao simulador utilizado, o framework adota o padrão de projeto estrutural **Adapter/Wrapper**. O sistema divide-se em três camadas independentes e suporta qualquer combinação N×M entre simuladores de física e sistemas de controle do cliente.

### Visão Geral N×M: Qualquer Simulador para Qualquer Hardware

```
 ╔══════════════════════════════════════════════════════════════════════════════╗
 ║                  LADO N — MOTORES DE FÍSICA (Simuladores)                  ║
 ╠══════════════╦══════════════╦══════════════╦══════════════╦═════════════════╣
 ║  AERONÁUTICO ║   COMBATE    ║   MARÍTIMO   ║  TERRESTRE   ║    AGRÍCOLA /   ║
 ║  X-Plane 12  ║  DCS World   ║ Veh.Sim VSF  ║  BeamNG.drv  ║  Farming Sim   ║
 ║  (Física FAA)║  (Balística) ║  Gazebo ROV  ║  rFactor 2   ║  Constr. Sim   ║
 ║  Plane Maker ║  Export.lua  ║  ROS2/UUV    ║  ETS2 Trucks ║  Vortex Studio ║
 ╚══════════════╩══════════════╩══════════════╩══════════════╩═════════════════╝
        │                │             │              │               │
        ▼ Wrapper C++    ▼ Wrapper Lua ▼ Wrapper ROS  ▼ SharedMem    ▼ Modding API
 ╔══════════════════════════════════════════════════════════════════════════════╗
 ║                    DLL CENTRAL NEXUS-LOOP (Windows)                        ║
 ║  ┌─────────────────────────────────────────────────────────────────────┐   ║
 ║  │ Camada de Ruído e Falhas: GPS Gaussiano, IMU Drift, Weibull/MTBF   │   ║
 ║  │ Geração de Payload: FLIR (DXGI/CesiumJS), LIDAR PCL, RADAR PPI    │   ║
 ║  │ Emulador de Rede Enxame: raio, atenuação terreno, jitter, BW      │   ║
 ║  │ Protocolo Unificado: SessionID + SequenceID + CRC  (UDP/COM/CAN)  │   ║
 ║  └─────────────────────────────────────────────────────────────────────┘   ║
 ╚══════════════════════════════════════════════════════════════════════════════╝
        │ UDP          │ COM Serial     │ CAN Bus       │ Bytes/RTSP
        ▼              ▼                ▼               ▼
 ╔══════════════════════════════════════════════════════════════════════════════╗
 ║                  LADO M — HARDWARE / SOFTWARE DO CLIENTE                   ║
 ╠══════════════╦══════════════╦══════════════╦══════════════╦═════════════════╣
 ║  MATLAB /    ║  FIRMWARE    ║  PILOTO AUTO ║  IA ENXAME   ║   PAINEL GCS /  ║
 ║  Simulink    ║  STM32/ESP32 ║  Pixhawk /   ║  Python /    ║   LabVIEW /     ║
 ║  LabVIEW     ║  Texas Instr ║  Hardware    ║  C++ multi-  ║   Supervisório  ║
 ║  Blocos MIL  ║  Arduino HAL ║  Customizado ║  agente      ║   Customizado   ║
 ╚══════════════╩══════════════╩══════════════╩══════════════╩═════════════════╝
```

O operador seleciona o simulador de física (N) no arquivo `mapping_config.json` e conecta o hardware ou software de controle (M) na porta configurada. A DLL central aplica o mesmo pipeline de ruído, falhas e payload sintético independentemente da combinação escolhida.

### Diagrama de Camadas de Software

```
┌─────────────────────────────────────────────────────────────────┐
│         CAMADA 1: NÚCLEO DE CONTROLE E SENSORES (Core Layer)    │
│  Algoritmos do cliente: PID, LQR, Kalman, IA de Enxame         │
│  Modelos de ruído, atrasos e relógios estatísticos de falha     │
│  Opera em C# (.NET) ou C++ nativo — agnóstico ao simulador     │
└──────────────────────────────┬──────────────────────────────────┘
                               │ Interface Unificada (Contrato)
                               ▼
┌─────────────────────────────────────────────────────────────────┐
│          CAMADA 2: INTERFACE UNIFICADA (Interface Contract)     │
│  Entradas: Lat, Lon, Alt, acelerações, vento, temperatura — SI │
│  Saídas: Roll, Pitch, Yaw de -1.0 a 1.0, comandos de atuador  │
│  Dicionário JSON: mapeia variáveis genéricas ↔ variáveis do    │
│  simulador selecionado sem alterar o código de controle        │
└──────────────────────────────┬──────────────────────────────────┘
     ┌──────────┬──────────────┼──────────────┬──────────┐
     ▼          ▼              ▼              ▼          ▼
┌─────────┐ ┌────────┐ ┌──────────────┐ ┌─────────┐ ┌──────────┐
│Wrapper  │ │Wrapper │ │Wrapper       │ │Wrapper  │ │Wrapper   │
│X-Plane  │ │DCS     │ │VSF/Gazebo    │ │BeamNG / │ │Farming / │
│C++ .xpl │ │Lua     │ │ROS2 nodes    │ │rFactor  │ │Constr.   │
│DataRefs │ │Export  │ │UDP/TCP msgs  │ │SharedMem│ │Modding   │
└─────────┘ └────────┘ └──────────────┘ └─────────┘ └──────────┘
```

### Camada 1: Núcleo de Controle e Processamento de Sensores

Opera puramente em C# (.NET) ou C++ Nativo. Encapsula as equações matemáticas do piloto automático do cliente (PID, LQR, Filtros de Kalman), os modelos de erro contínuo e os relógios estatísticos de falha. Esta camada não sabe o que é o X-Plane ou o DCS; ela apenas consome e injeta dados através de um contrato abstrato fixo.

### Camada 2: Interface Unificada

Contrato rígido de software que define as variáveis de entrada de sensores (Latitude, Longitude, Altitude, Acelerações angulares e lineares, Vetores de vento e temperatura) em unidades SI e as variáveis de comando de saída dos atuadores (Roll, Pitch, Yaw de -1.0 a 1.0).

### Camada 3: Wrappers de Tradução de Simuladores

- **Wrapper X-Plane 12:** Plugin nativo em C++ compilado como arquivo `.xpl` para o SDK 4.0+. Acopla-se ao loop de física do simulador, lê e escreve em alta velocidade nas DataRefs e CommandRefs nativas. Para objetos 3D como gimbals, utiliza `XPLMCreateInstance` e `XPLMSetInstancePosition` com DataRefs customizadas para compatibilidade com o motor Vulkan. Para raycasting no terreno, utiliza `XPLMProbeTerrainXYZ` com matrizes de transformação sequenciais (espaço da câmera → espaço do avião → espaço do mundo).
- **Wrapper DCS World:** Script em Lua acoplado a `LuaExportAfterNextFrame()` dentro do arquivo `Export.lua`. Extrai matrizes cinemáticas via `LoGetSelfData()` e `LoGetWindAtPoint()`, abre sockets UDP bidirecionais e injeta comandos de controle via `LoSetCommand()`.
- **Wrappers Adicionais:** Nós ROS2/Gazebo para domínios de robótica; shared memory para simuladores automotivos (Assetto Corsa, BeamNG.drive); scripts de modding para Farming Simulator e Construction Simulator.

### Dicionário de Mapeamento Dinâmico

A DLL central mantém um arquivo `mapping_config.json` que amarra variáveis internas genéricas (`EIXO_PRINCIPAL_Y`) às variáveis específicas de cada simulador selecionado. Trocar de X-Plane para DCS ou para um simulador marítimo é uma operação de configuração, não de código.

---

## 4. Mecânica de Implementação HIL: Software vs Hardware Físico

O framework suporta as duas abordagens padrão da indústria de validação de hardware embarcado:

### Abordagem A: Modo Emulação por Software via `#define`

Utilizada nas fases iniciais de projeto ou em laboratórios com restrição de orçamento eletrônico.

- **Mecânica:** O engenheiro de firmware do cliente cria uma Camada de Abstração de Hardware (HAL) dentro do código C do microcontrolador (STM32, ESP32, Texas Instruments, Arduino). Através da diretiva `#define MODO_SIMULACAO_HIL 1`, o processador ignora os pinos físicos de barramento dos sensores reais.
- O chip passa a ler as variáveis que chegam do cabo USB (Porta COM Virtual CDC) ou cabo de rede UDP vindo da DLL, inserindo esses dados diretamente nas variáveis internas do loop PID como se fossem leituras reais de sensores.
- **Vantagem:** Rapidez de desenvolvimento, custo zero de fiação eletrônica, depuração direta do código lógico de controle.
- **Consideração:** O código compilado para simulação difere do código final de voo. Para certificação, usa-se a Abordagem B.

### Abordagem B: Placa Intermediária HIL de Alta Fidelidade Mecânica

Exigida em processos rigorosos de certificação militar ou industrial, onde é proibido alterar qualquer linha do firmware final que irá para o veículo real.

- **Mecânica:** O hardware de controle do cliente opera em modo real absoluto, tentando ler fisicamente os pinos eletrônicos de seus barramentos. Os chips físicos dos sensores reais (giroscópios, acelerômetros, receptores GPS) são fisicamente removidos ou desativados.
- A DLL conecta-se via USB a uma Placa Intermediária de Interface de Bancada (microcontrolador FPGA ou ARM de alta velocidade). Esta placa traduz os dados numéricos da DLL em sinais elétricos puros de silício em tempo real, emulando protocolos SPI, I2C, pulsos PWM/SBUS e tensões analógicas nos pinos físicos da placa do cliente.
- **Resultado:** O firmware final, sem alteração, recebe dados de simulação indistinguíveis de sensores físicos reais.

### Simetria de Protocolo entre as Abordagens

Independentemente de o cliente usar `#define` via cabo USB (COM) ou Placa Intermediária via cabo de rede (UDP) ou CAN Bus, **o pacote que trafega nos fios tem exatamente a mesma estrutura binária**. Muda apenas a tomada de transporte. Isso garante que o Sniffer Open-Source e os scripts de diagnóstico funcionem identicamente em ambos os cenários.

### Restrições de Memória e Processamento no Firmware do Cliente

O firmware embarcado que implementa o modo HIL precisa reservar espaço de memória e ciclos de CPU para a camada de comunicação com a DLL sem prejudicar o loop de controle em tempo real:

- **DMA / Interrupção assíncrona:** A recepção dos pacotes UDP ou Serial deve ser gerenciada por DMA ou thread de baixa prioridade, nunca bloqueando o loop principal do PID.
- **Structs binárias estáticas:** O formato do pacote deve ser definido como `struct` em C com layout fixo, evitando `malloc`, `sscanf` ou parsing de strings que fragmentam a memória e consomem ciclos.
- **Timestamp de sincronização:** O campo de tempo absoluto do motor de física (incluído no payload da DLL) deve ser usado para calcular derivadas e integrais do controle, garantindo comportamento correto mesmo quando o computador do simulador sofre variações de frame rate.
- **Watchdog de comunicação:** Se a DLL parar de enviar pacotes por mais de N milissegundos (configurável), o firmware aciona o modo Failsafe e desarma os atuadores, exatamente como um drone real faz ao perder o sinal do rádio controle.

---

## 5. Mecânica Lógica do Protocolo e Blindagem de Rede/Serial no Windows

Para garantir estabilidade, imunidade a panes elétricas e determinismo temporal em frequências de 50 Hz a 100 Hz, o tráfego de dados adota regras rígidas de empacotamento e blindagem do sistema operacional Windows.

### Estrutura Unificada do Pacote Binário

```
┌────────────────────────────────────────────────────────────────────────┐
│  SESSION_ID (4 Bytes)  │  SEQUENCE_ID (8 Bytes)  │  PAYLOAD  │  CRC   │
│      uint32            │       uint64             │  variável │ uint16 │
└────────────────────────────────────────────────────────────────────────┘
```

- **SessionID (uint32, 4 bytes):** Gerado via Unix Timestamp no milissegundo exato em que a simulação inicia na DLL. Se o engenheiro desconectar fisicamente o cabo USB/Rede da placa no meio de um teste, ao reconectar, o hardware reinicia, gera um novo SessionID e dispara a primeira mensagem. A DLL identifica a mudança de ID, esvazia instantaneamente os buffers de memória antigos e ressuscita a simulação automaticamente, sem travamentos.
- **SequenceID (uint64, 8 bytes):** Contador incremental que sobe de 1 em 1 a cada pacote enviado. O validador central da DLL guarda o último SequenceID processado. Pacotes com ID menor ou igual são descartados imediatamente como lixo de rede ou dados obsoletos. Garante que o modelo de controle nunca leia dados fora de ordem cronológica.
- **CRC (uint16, 2 bytes):** Verificação de Redundância Cíclica (CRC16) no fim do pacote. Se um ruído eletromagnético leve no cabo serial alterar um único bit — fazendo um ângulo de inclinação de 5° virar 500° — a DLL recalcula o CRC, detecta a quebra de integridade e descarta o pacote, protegendo os atuadores físicos na bancada contra comandos violentos e destrutivos.

### Canais de Transporte Suportados

- **UDP (Rede Ethernet):** Máxima performance, latência mínima, ideal para hardware com porta RJ45. Na bancada local, perda de pacotes é praticamente zero. Pacotes obsoletos são descartados pelo SequenceID — nunca pelo sistema operacional.
- **Porta COM Virtual (USB Serial CDC):** Compatível com microcontroladores via chips FTDI, CH340 ou USB CDC nativo. Auto-reconexão transparente: se o cabo for puxado no meio do teste, a DLL detecta a exceção, fecha a porta de forma segura, aguarda 1 segundo e tenta reconectar automaticamente. O novo SessionID gerado pelo hardware ao reiniciar sinaliza o reset para a DLL.
- **CAN Bus (via adaptador USB-to-CAN):** Para hardware industrial e aeroespacial que utiliza barramento CAN/UAVCAN/DroneCAN. A DLL inclui wrapper para adaptadores como Canable/PCAN-USB.

### Endianness e Compatibilidade

O protocolo adota Little-Endian nativo (padrão das CPUs x86/x64 e microcontroladores ARM/ESP32) para maximizar performance e eliminar conversões desnecessárias. A documentação do protocolo especifica explicitamente o byte order de cada campo. Para depuração com Wireshark, o pacote de instalação inclui um dissector Lua customizado que exibe os campos decodificados em formato legível.

### Blindagem de Threads no Windows

A thread da DLL que gerencia o loop de escuta do `UdpClient` ou da `SerialPort` opera com `ThreadPriority.AboveNormal` ou `ThreadPriority.Highest`. Se o loop travar por milissegundos devido a gargalos gráficos dos simuladores, aplica-se a política de **Descarte de Buffer por Estouro**: a fila é limpa e mantém-se estritamente o pacote de dados mais fresco. Um dado de 50 ms atrás não tem valor para um avião em voo; o dado atual mais recente é o único relevante.

---

## 6. Módulo de Emulação de Sensores Sintéticos e Física Climática Integrada

Os simuladores comerciais entregam nas suas saídas de rede a "Verdade Absoluta" matemática do motor de física: dados perfeitos, limpos, sem imprecisões. Chips de sensores reais instalados em placas de circuito sofrem com ruídos contínuos, desvios térmicos e limitações eletrônicas de silício.

A DLL central atua como **Emulador de Filtros de Ruído Parametrizáveis**, pegando os dados limpos do jogo e distorcendo-os de forma controlada antes de enviá-los ao hardware do cliente.

### Emulação de GPS

- **Taxa de Atualização Lenta:** O GPS real de drones opera em 5 Hz ou 10 Hz. A DLL amortece os dados de Latitude e Longitude usando um timer estático para atualizar os valores apenas nos intervalos fixos de 100 ms ou 200 ms, independentemente do frame rate do simulador.
- **Ruído de Posição Multipath:** Injeção de Ruído Gaussiano com desvio padrão configurável (±2 a ±5 metros por padrão) nas coordenadas geográficas, simulando interferências de nuvens, chuva ou rebatimento de sinal de satélite em prédios ou montanhas.
- **Degradação Climática Automática:** Se o X-Plane 12 reportar chuva forte (`sim/weather/rain_percent > 0.5`), o algoritmo aumenta automaticamente o desvio padrão do erro de GPS, simulando a perda de precisão real sob tempestades.

### Emulação de IMU: Giroscópios e Acelerômetros

- **Vibração Mecânica Estrutural (White Noise):** Injeção de ruído de alta frequência baseado em ondas senoidais cruzadas com ruído branco nas propriedades de velocidade angular e forças G. A amplitude é atrelada à rotação do motor do veículo fornecida pelo jogo, simulando o tremor mecânico real de um motor aeronáutico ou diesel.
- **Gyro Drift (Desvio Acumulado):** Variável acumuladora linear de erro térmico que cresce lentamente com o tempo de simulação, forçando o algoritmo de controle do cliente a executar rotinas de recalibração em voo, exatamente como um sistema embarcado real precisa fazer.

### Motor de Falhas Estatísticas: MTBF, MTTR e Distribuições

A DLL roda em segundo plano um relógio de confiabilidade com suporte a múltiplas distribuições estatísticas:

- **Distribuição Exponencial:** Para falhas puramente aleatórias ao longo do tempo (MTBF simples).
- **Distribuição de Weibull:** Para simular envelhecimento de componentes — a probabilidade de falha aumenta com as horas de operação acumuladas.
- **Distribuição Normal/Log-Normal:** Para modelar o MTTR — o tempo de recuperação do sensor após uma falha.

O engenheiro configura parâmetros como `sensor=GPS, distribution=Weibull, mtbf_hours=150`. A DLL calcula o envelhecimento e injeta falhas críticas de surpresa no meio do teste: travamento do giroscópio no último valor, perda total do GPS, ruído catastrófico no altímetro. O sistema de segurança do cliente deve provar que consegue detectar a quebra e pousar com segurança.

### Injeção de Falhas Determinísticas (Sob Demanda)

Paralelo ao motor estatístico, a DLL expõe uma API de injeção direta para testes de certificação onde o engenheiro precisa provocar uma falha exata num momento preciso:

```csharp
MinhaDll.InjetarFalhaImediata(SensorType.IMU_Giroscopio, TipoFalha.TravadoNoUltimoValor);
MinhaDll.InjetarFalhaImediata(SensorType.GPS, TipoFalha.PerdaTotalDeSinal);
MinhaDll.InjetarFalhaImediata(Estrutura.AsaEsquerda, TipoFalha.QuebraFisica);
```

---

## 7. Módulo de Carga Útil Visual, Térmica FLIR e Integração de Viewports

Para clientes integradores que desenvolvem soluções de sensoriamento remoto, inspeção de dutos ou sistemas de vigilância aérea, o framework gerencia o feed de vídeo da câmera do gimbal através de dois cenários complementares.

### Cenário A: Captura de Display Nativo via DXGI Viewports

Focado em aproveitar os motores térmicos militares e câmeras de pods de mira nativas existentes dentro do DCS World ou em aeronaves modificadas do X-Plane 12.

- **Isolamento da Tela do FLIR:** Utilizando arquivos de layout de monitores do simulador (como `MonitorSetup.lua` no DCS), o jogo renderiza o retângulo de pixels da câmera de mira (viewport do TGP ou MFD) de forma isolada em um Monitor Virtual Secundário Oculto criado no Windows, sem monitor físico plugado.
- **Captura Direta na VRAM via DXGI:** A DLL em C# executa a Desktop Duplication API do Windows, varrendo estritamente a memória gráfica do monitor virtual oculto e capturando o retângulo exato dos pixels do FLIR a 60 FPS com latência próxima a zero microssegundos.
- **Injeção de Ruídos de Linha de Vídeo:** Com o array de bytes da imagem na memória, a DLL injeta efeitos reais de hardware de vídeo: linhas pretas horizontais de estática analógica por afastamento da base de recepção, congelamento completo do frame por falha de hardware, ruído de granulação digital térmico. O feed de vídeo final "sujo" é transmitido via RTSP ou arrays de bytes em memória para a bancada do cliente.
- **Aplicação:** O cliente que compra a câmera FLIR de um fabricante terceiro e precisa testar o seu sistema de processamento de imagem térmica sem comprar o hardware real imediatamente.

### Cenário B: Geração Oculta de Vídeo Sintético via Google Maps 3D

Focado em clientes que estão projetando o próprio Gimbal físico do zero e precisam validar a óptica da lente, o zoom e os algoritmos de rastreamento de alvo, sem depender dos gráficos do jogo.

- **O Navegador Invisível (Headless Browser):** A DLL executa em memória RAM um navegador Chromium oculto (via CefSharp ou Puppeteer Sharp) que carrega uma página web com a biblioteca CesiumJS integrada aos Photorealistic 3D Tiles do Google Maps.
- **Sincronização Cinemática Espacial:** O plugin lê a posição de latitude, longitude e altitude do avião no jogo, além dos ângulos de Roll, Pitch e Yaw que o hardware do gimbal do cliente enviou. O script JavaScript move a câmera do Google Maps de forma idêntica no navegador oculto, gerando a perspectiva exata em primeira pessoa da câmera real.
- **Parâmetros de Lente Configuráveis:** A API expõe `ConfigurarSensorOptico(resolucaoLargura, resolucaoAltura, fovHorizontalGraus, taxaAtualizacaoHz)` para que o cliente defina exatamente os parâmetros ópticos do sensor que está desenvolvendo, sem precisar saber que o Google Maps está por trás.

### Shaders WebGL para Imagem Térmica Baseada no Clima

A foto colorida de satélite do Google (RGB) passa por um filtro de pós-processamento via Shaders de Fragmento WebGL, influenciado em tempo real pelas variáveis climáticas do simulador:

- **Thermal Washout:** Se o X-Plane reportar chuva pesada (`rain_percent > 0.5`), o Shader reduz o contraste global da imagem térmica em até 80%, imitando o resfriamento real da água sobre as superfícies que cega câmeras infravermelhas.
- **Inércia Térmica Temporal:** O Shader lê a hora do dia do simulador (`local_time_sec`). Ao meio-dia, telhados e pistas de concreto ganham cores amarelas/brancas ultra-quentes. Às 03:00, a terra esfria para tons roxos escuros, mas o mar mantém coloração média estável pela alta inércia térmica da água.
- **Atenuação por Neblina:** Se a visibilidade horizontal do simulador cair, o Shader mistura ruído estático analógico na imagem e borra as bordas dos objetos, reduzindo o alcance útil do sensor de forma verossímil.
- **Propriedade Intelectual Protegida:** O cliente final não sabe que a imagem foi gerada pelo Google Maps; recebe apenas o array de bytes da câmera através da API da DLL.

### Limitações de Altitude e Resolução do Google Maps

O Google Maps foi feito para visualização em escala urbana ou de avião — não para voos rasantes de drone. Abaixo de ~60 metros de altitude, a textura do solo se torna borrada (resolução máxima de ~15 cm/pixel nas melhores cidades) e o modelo 3D de relevo apresenta erros de elevação de 1 a 3 metros. Para voos baixos (inspeção de dutos a 10 m, aproximação de plataformas offshore), o cliente deve injetar um modelo 3D próprio gerado por fotogrametria RTK da área específica. A DLL suporta este modo via recorte no CesiumJS sobre a coordenada correta.

---

## 8. Módulos de Sensores Complexos LIDAR e RADAR e Sistemas Táticos de Armas

### Emulação de Sensor LIDAR: Nuvem de Pontos

Para clientes que desenvolvem drones de inspeção de refinarias ou plataformas que precisam de dados de nuvens de pontos para algoritmos de desvio de obstáculos:

- A DLL consome a malha geométrica de elevação digital tridimensional do relevo (MDT/DEM) da região de voo de forma oculta.
- Simula matematicamente a rotação do espelho óptico do sensor LIDAR, disparando milhares de raios de interseção geométrica por segundo contra a malha de relevo.
- Entrega ao cliente (via UDP) uma estrutura de dados Point Cloud no formato padrão XYZI (Posição + Intensidade), com ruído de precisão de fase de ±5 centímetros e atenuação ou perda completa de pontos quando o laser cruza nuvens ou neblina intensa configurados no simulador.

### Emulação de Sensor RADAR com Clutter Atmosférico

Para sistemas aviônicos de defesa e detecção de alvos marítimos ou terrestres:

- O algoritmo calcula o cone de varredura eletromagnética da antena com base nos ângulos do nariz do avião ou do gimbal. Calcula a Seção Reta de Radar (RCS) dos alvos no ambiente. Superfícies de água retornam sinal nulo (preto no radar); montanhas de pedra perpendiculares geram forte eco (brilhante).
- Se o simulador indicar tempestade ou chuva pesada, a DLL mistura uma matriz de ruído verde borrado e granulado no display de varredura (PPI ou B-Scope), representando o Radar Clutter real causado pela reflexão das ondas nas gotas de água no ar.

### Sistemas Táticos de Armas e Balística

Módulo para validação de sistemas de gerenciamento de armas (SMS) em aeronaves de combate via DCS World ou X-Plane 12:

- **Metralhadoras e Canhões:** O comando de disparo aciona cálculo de balística de alta frequência, levando em conta gravidade, arrasto supersônico e vetor de vento extraído do simulador. O sistema cruza a trajetória com o relevo tridimensional e devolve a coordenada de impacto e o sinal de "Alvo Atingido".
- **Bombas Queda Livre ou Guiadas a Laser:** A DLL calcula a parábola balística de queda. Em bombas guiadas por laser, a bomba rastreia a coordenada geográfica exata do solo onde a câmera do gimbal está apontando com o laser designator. O X-Plane 12 processa o alívio de peso na asa na fração de segundo em que o comando de Release é enviado.
- **Mísseis de Busca Térmica ou Radar:** O DCS World é utilizado como motor principal neste quesito. O DCS gera a queima do propelente, calcula a perseguição contra a assinatura infravermelha do alvo ou a reflexão de ondas de radar, computando o uso de contramedidas eletrônicas (Chaffs e Flares) e retornando as coordenadas em tempo real do vetor do míssil.

---

## 9. Expansão do Hub Universal para Outros Domínios de Veículos

O framework adota o conceito de **Matriz de Co-Simulação N-para-M**: qualquer simulador de física especializada de mercado (N) pode ser acoplado à DLL central via dicionários de mapeamento dinâmico em JSON para alimentar qualquer hardware/software de controle do cliente (M). O cliente escolhe o simulador que melhor representa o veículo que está desenvolvendo; o restante do pipeline permanece idêntico.

### Matriz de Simuladores e Domínios Suportados

| Domínio | Simuladores Integráveis (N) | Mecanismo de Integração | Exemplos de Cliente (M) |
|---|---|---|---|
| **Aeronáutico Civil** | X-Plane 12, FlightGear | Plugin C++ (.xpl), DataRefs | Drone de entrega, UAV de mapeamento, piloto automático próprio |
| **Aeronáutico Militar** | DCS World | Export.lua + UDP bidirecional | SMS de armas, gimbal de mira, sistema ECM/ECCM |
| **Robótica / Drones ROS** | Gazebo Sim, **MuJoCo** (DeepMind), Webots, AirSim, Isaac Gym | ROS2 nodes, topics UDP, MJCF/URDF models | Algoritmos de enxame, RL (PPO/SAC/TD3), evitação de colisão, SLAM, manipulação dextra |
| **Marítimo / Subaquático** | Vehicle Simulator VSF, UUV Sim, Gazebo aquático | UDP/TCP + ROS msgs | ROV Petrobras, sistema de Posicionamento Dinâmico, AUV de inspeção |
| **Automotivo / Pesado** | BeamNG.drive, rFactor 2, Assetto Corsa | Shared Memory API | ADAS, ABS/ESC, sistema de frenagem de caminhão autônomo de mina |
| **Logística Rodoviária** | Euro Truck Simulator 2, American Truck Sim | Shared Memory + plugin | Controle de frota, sistema de piloto automático para rodovias |
| **Agrícola** | Farming Simulator 22/25 | API de Modding (Lua/C++) | Trator autônomo, colheitadeira de precisão, pulverizador de drones |
| **Construção / Mineração** | Construction Simulator, Vortex Studio | API Modding + RPC | Escavadeira remota, guindaste autônomo, esteira de minério |
| **Ferroviário** | OpenRails, Trainz Simulator | Plugin + Named Pipes | Sistema CBTC de sinalização digital, piloto automático de metrô |

### Fluxo de Dados N×M

```
[ SIMULADOR (N) ] ──→ [ WRAPPER ESPECÍFICO ] ──→ [ DICIONÁRIO JSON ]
                                                         │
                                                         ▼
                                               [ DLL CENTRAL NEXUS-LOOP ]
                                               [ Aplica Ruído, Falhas,  ]
                                               [ Payload Sintético      ]
                                                         │
                        ┌────────────────────────────────┤
                        ▼                                ▼
              [ UDP / COM / CAN ]              [ RTSP / Bytes ]
                        │                                │
            [ HARDWARE / FIRMWARE (M) ]    [ MATLAB / LabVIEW / GCS ]
```

Trocar de X-Plane para BeamNG ou para Gazebo é uma alteração no arquivo `mapping_config.json` — não requer modificação no código de controle do cliente nem no código da DLL.

### Domínio Marítimo e Subaquático: Navios, Submarinos e ROVs

Mercado offshore da Petrobras e engenharia naval de defesa.

- **Simuladores Integráveis:** Vehicle Simulator (VSF) para hidrodinâmica de ondas e ancoragem; UUV Simulator (ROS/Gazebo) para física de submarinos e ROVs subaquáticos.
- **Atuadores de Entrada:** `THRUST_PORT / THRUST_STARBOARD` (hélices de bombordo e boreste); `BOW_THRUSTER / STERN_THRUSTER` (motores laterais de proa e popa para Posicionamento Dinâmico); `HYDROPLANE_PITCH` (aletas de profundidade de submarinos); `ROV_VERTICAL_THRUSTER` (controle de flutuabilidade); `BALLAST_PUMP_RATE` (tanques de lastro).
- **Sensores de Saída:** `DEPTH_SENSOR` (pressão hidrostática em metros); `SONAR_ALTITUDE` (distância acústica vertical ao leito marinho); `DVL` (Doppler Velocity Log — velocidade real em relação ao fundo, onde GPS não penetra); `USBL` (posicionamento acústico submarino com atrasos reais baseados na velocidade do som na água de ~1500 m/s); `FORWARD_LOOKING_SONAR` (detecção de obstáculos no escuro abissal); `HEADING_REPEATER` (agulha giroscópica marítima com desvios magnéticos de estruturas metálicas).

### Domínio Automotivo e Transporte Pesado

Validação de sistemas ADAS, frenagem eletrônica e logística de mineração remota.

- **Simuladores Integráveis:** Assetto Corsa / rFactor 2 para física de pneus de alta frequência e transferência de massa em curvas; Euro Truck Simulator 2 para frenagem a ar comprimido e fadiga de chassi de caminhões; BeamNG.drive para física de corpo macio (Soft-Body) com deformação real de metal, quebra de suspensão e torção de chassi.
- **Atuadores de Entrada:** `STEERING_ANGLE` (ângulo analógico do volante, -1.0 a 1.0); `THROTTLE_PRESSURE` (curso do acelerador ou torque do motor elétrico); `BRAKE_PRESSURE` (pressão hidráulica das pinças para validar ABS); `CLUTCH_ENGAGEMENT` (embreagem); `GEAR_SELECT` (seleção de marchas); `RETARDER_BRAKE` (freio motor eletromagnético de caminhões pesados).
- **Sensores de Saída:** `WHEEL_SPEED_FL/FR/RL/RR` (velocidades individuais das quatro rodas para ABS); `STEERING_FEEDBACK_TORQUE` (força de retorno para Force Feedback do volante de teste); `SUSPENSION_TRAVEL_FL/FR/RL/RR` (curso de suspensão para monitoramento de carga por eixo); `TYRE_SLIP_RATIO` (escorregamento do pneu no asfalto); `ODOMETER/SPEEDOMETER` (com erros por patinação em lama ou gelo).

### Domínio de Maquinário Pesado, Agrícola e Construção

Automação do agronegócio e operação remota de frotas de mineração.

- **Simuladores Integráveis:** Farming Simulator para malhas de solo deformável e implementos agrícolas; Construction Simulator para escavadeiras e caminhões basculantes; Vortex Studio para dinâmica industrial de cabos de aço, guindastes e esteiras.
- **Atuadores de Entrada:** `ARTICULATION_ANGLE` (dobra do chassi central de tratores articulados pesados); `BOOM_HYDRAULIC_VALVE / STICK_HYDRAULIC_VALVE` (válvulas hidráulicas dos braços da escavadeira); `BUCKET_TILT_VALVE` (inclinação da caçamba); `PTO_ENGAGEMENT` (tomada de força traseira para implementos de colheita); `DIFFERENTIAL_LOCK` (bloqueio de diferencial para lama extrema).
- **Sensores de Saída:** `HYDRAULIC_PRESSURE_X` (pressão nos pistões para alarmes de sobrecarga); `GRAIN_TANK_LEVEL` (nível do tanque de grãos em colheitadeiras autônomas); `IMU_CHASSIS_PITCH_ROLL` (inclinação para evitar tombamento em rampas, com ruído de trepidação de motor diesel de baixa frequência e alta amplitude); `WHEEL_TORQUE_LOAD` (resistência ao arrastar implementos pesados); `IMPLEMENT_HEIGHT` (altura do implemento em relação ao solo).

---

## 10. Arquitetura de Simulação em Enxame e Inteligência Distribuída

Quando o ecossistema evolui para validar frotas autônomas, robótica em enxame ou sistemas de supervisão centralizados, a DLL deixa de gerenciar um único canal e passa a atuar como **Orquestrador de Mundo e Simulador de Matriz de Comunicação** entre agentes.

### Arquitetura do Fluxo de Dados Distribuídos

```
┌───────────────────────────────────────────────────────────────────┐
│               SISTEMA DE SUPERVISÃO CENTRAL                       │
│    Painel GCS / Operador Humano / IA Mãe (Nível Estratégico)     │
└───────────────▲───────────────────────────────▲───────────────────┘
                │ Link de Telemetria             │ Longo Alcance
                ▼                               ▼
┌───────────────────────────┐     ┌───────────────────────────────┐
│    AGENTE AUTÔNOMO 1      │◄───►│      AGENTE AUTÔNOMO 2        │
│  IA de Borda (SIL/HIL)    │     │  IA de Borda (SIL/HIL)        │
│  Decisões Locais          │     │  Evitação de Colisão          │
└───────────────▲───────────┘     └───────────────▲───────────────┘
                │ Física UDP Limpa                 │ Física UDP Limpa
┌───────────────┴──────────────────────────────────┴───────────────┐
│        DLL CENTRAL (ORQUESTRADOR DE REDE E FÍSICA)               │
│  Modula latências inter-agentes, colisões de rádio e perdas      │
└───────────────▲──────────────────────────────────▲───────────────┘
                │                                  │
┌───────────────┴──────────────────────────────────┴───────────────┐
│        SIMULADOR DE AMBIENTE (N INSTÂNCIAS DE FÍSICA)            │
│    X-Plane Multi-UAV / DCS Multi-Agente / Gazebo / ROS2         │
└───────────────────────────────────────────────────────────────────┘
```

### Parâmetros de Customização do Emulador de Comunicação Inter-Agentes

- **Raio Máximo de Conectividade (`max_comms_range_meters`):** Se o Drone 1 se afastar do Drone 2 além do limite configurado, a DLL bloqueia e descarta os pacotes UDP entre eles, forçando a IA do enxame a lidar com o isolamento do agente e reconfigurar rotas.
- **Atenuação por Obstáculos de Terreno (`terrain_blocking_factor`):** A DLL cruza a linha de visada entre dois agentes com a malha 3D. Se o Drone 1 entrar atrás de uma montanha em relação ao Drone 2, a taxa de entrega de pacotes cai drasticamente, simulando a perda real de sinal por sombreamento de relevo.
- **Taxa de Perda de Pacotes por Interferência (`packet_loss_probability`):** Probabilidade de descarte estocástico configurável via distribuição Uniforme ou Gaussiana, para simular ambientes industriais poluídos eletromagneticamente como plataformas de petróleo.
- **Jitter de Latência (`network_latency_jitter_ms`):** Latência variável entre pacotes (ex: 5 ms a 80 ms aleatórios) para testar a robustez dos algoritmos de sincronização de relógio interno do enxame.
- **Saturação de Canal (`channel_bandwidth_limit_kbps`):** Se múltiplos drones tentarem enviar dados ao mesmo tempo para a estação central, a DLL calcula o estouro de banda e atrasa ou descarta pacotes de menor prioridade, simulando saturação real de frequências de rádio.

### Integração com Aprendizado por Reforço e Fechamento do Sim-to-Real Gap

Para clientes que desenvolvem políticas de controle via Reinforcement Learning (RL) — algoritmos como PPO, SAC e TD3 usando ambientes MuJoCo, Isaac Gym ou Gazebo — o NEXUS-LOOP atua em duas fases complementares:

- **Durante o Treino (MIL/SIL):** O ambiente de treino RL recebe, via API da DLL, parâmetros de **domain randomization** — variações estocásticas nos parâmetros físicos como coeficientes de atrito, amortecimento das juntas, ganhos de atuadores, latências de sensores e ruídos de IMU. Ao randomizar esses parâmetros durante o treino, a política aprendida se torna robusta a incertezas físicas e fecha o sim-to-real gap quando embarcada no hardware real.
- **Durante a Validação (HIL):** A política treinada é embarcada no microcontrolador do cliente. O NEXUS-LOOP injeta nos sensores físicos via HIL os mesmos perfis de ruído e falha usados durante o treino, verificando que o comportamento real do hardware corresponde ao comportamento simulado e que as funções de recompensa (reward functions) continuam sendo satisfeitas em condições de hardware real.
- **Variáveis de Observation Space e Action Space:** A DLL expõe via API genérica os vetores de estado (posição, velocidade, orientação, leituras de sensores ruidosas) que alimentam o observation space do agente RL, e recebe os vetores de ação (torques de juntas, forças de atuadores, comandos de propulsão) que compõem o action space — mantendo a mesma interface usada no simulador de treino.

### Conexão das IAs Locais: Evitação de Colisão e Navegação

Cada robô roda um script local (Python, C++) que pede à DLL os dados dos próprios sensores com o ruído correspondente. As mensagens de coordenação entre robôs passam obrigatoriamente pelo filtro de rede da DLL, onde são aplicados os atrasos e perdas configurados. O cliente valida se os algoritmos de evitação de colisão e comportamento de enxame (Swarm Flocking) sobrevivem a falhas de link.

### Conexão do Sistema Supervisório Central

- **Variáveis de supervisão recebidas pela IA Mãe:** `HEARTBEAT_ALL_AGENTS` (flags de presença de cada ID ativo); `SWARM_CENTER_OF_MASS` (ponto geométrico central do enxame); `MISSION_STATUS / TASK_ALLOCATION` (vetor de bits indicando qual robô executa qual subtarefa).
- **Comandos de saída do supervisório:** `SET_SWARM_FORMATION (Line, V-Shape, Circle)` — altera a formação geométrica da frota; `RE_ROUTE_WAYPOINTS` — atualiza coordenadas de destino do enxame; `ABORT_MISSION_ALL / RETRIEVE` — força o enxame inteiro a pousar ou retornar à base de forma coordenada.

---

## 11. Módulo de Auditoria, Depuração e Sniffing Open-Source

### O Problema que o Módulo Resolve

Sem ferramentas de depuração adequadas, equipes de integração passam dias tentando descobrir se um bug está no algoritmo de controle, na inversão de bytes da rede, num problema de Endianness entre ARM e x86, ou num pacote chegando fora de ordem. O módulo de Sniffing elimina essa adivinhação.

### O Sniffer Open-Source: Logador CSV

O Sniffer captura o tráfego UDP/COM em tempo real e grava um arquivo `.csv` estruturado, legível em MATLAB, Excel, Python (Pandas) ou LabVIEW sem nenhuma ferramenta adicional.

**Colunas do arquivo de log:**

| Timestamp_Windows_ms | Agent_ID | Session_ID | Sequence_ID | Delta_Sequence | Latency_ms | Payload_Field_1 | ... | Status_Rede |
|---|---|---|---|---|---|---|---|---|
| 1718040000000 | 01 | 998811 | 1201 | 1 | 2.5 | -23.554 | ... | CONNECTED |
| 1718040000015 | 02 | 998811 | 0896 | **2** | 85.0 | -23.555 | ... | **LAG_SPIKE** |

- **`Delta_Sequence`:** Diferença entre o SequenceID atual e o anterior. Valor 1 = rede perfeita. Valor 2 ou mais = pacote perdido (furo na rede). No Excel, basta filtrar esta coluna por valores > 1 para achar todas as falhas de transmissão.
- **`Latency_ms`:** Calculado via timestamp do pacote; indica o atraso da linha.
- **`Status_Rede`:** String qualitativa calculada pela DLL — CONNECTED, LAG_SPIKE, RECONNECTED, SESSION_CHANGED.

### Ferramentas de Análise Incluídas no Starter Kit

- **Script MATLAB:** Importa o CSV, calcula taxa de perda de pacotes, plota gráfico de `Delta_Sequence` por tempo (os picos mostram os furos) e histograma de jitter de rede.
- **Script Python (Pandas):** Versão equivalente ao MATLAB para ambientes sem licença.
- **Análise de Enxame:** Para frotas multi-agente, o script filtra por `Agent_ID` e plota topologia de rede dinâmica: linhas verdes para latência baixa, vermelhas para perda de pacotes, nós isolados para agentes que perderam contato total.
- **Auditoria de Causa Raiz:** Se dois drones colidirem durante o teste SIL, o engenheiro analisa o log frame por frame para verificar se a colisão ocorreu por decisão errada da IA ou por surto de latência injetado pela DLL naquele exato segundo.

### Integração com Wireshark

Para tráfego UDP, o pacote de instalação inclui um dissector Lua customizado para Wireshark. Ao capturar os pacotes na porta configurada, o Wireshark exibe os campos decodificados: `[Sessão: 17180400] [Sequência: 4521] [Tipo: Sensores] [Latitude: -23.55]`, eliminando a necessidade de decifrar bytes hexadecimais manualmente.

---

## 12. Estruturação Comercial, Precificação e Professional Services

### Por que Professional Services é o Core do Negócio

No mercado brasileiro de engenharia de defesa, óleo & gás e agronegócio avançado, a maioria dos clientes tem verba disponível mas não tem tempo nem especialistas para configurar ambientes de simulação complexos. O engenheiro de firmware está atolado no desenvolvimento do algoritmo; não tem bandwidth para aprender a API do SDK do X-Plane 12 com suporte a Vulkan, configurar monitores virtuais no Windows ou debugar por que o Export.lua do DCS não está enviando pacotes na frequência correta. O NEXUS-LOOP é vendido como **solução funcionando no primeiro dia**, não como software para o cliente descobrir como usar.

### Catálogo de Licenças de Software

| Produto | Conteúdo | Valor Referência |
|---|---|---|
| Licença Core (por bancada) | DLL central + Wrappers X-Plane e DCS + Starter Kits C#/MATLAB/C++ | R$ 45.000 – R$ 80.000 |
| Módulo Offshore/Subaquático | Wrappers VSF/Gazebo-ROV + sensores DVL/USBL/Sonar | R$ 30.000 – R$ 50.000 |
| Módulo Terrestre/Agrícola | Wrappers BeamNG/FarmSim + sensores hidráulicos/IMU-chassis | R$ 25.000 – R$ 40.000 |
| Módulo Enxame + IA Distribuída | Emulador de rede Ad-Hoc + supervisório multi-agente | R$ 35.000 – R$ 60.000 |
| Suporte e Manutenção Anual | Atualizações de compatibilidade com novas versões dos simuladores | R$ 15.000 – R$ 35.000/ano |

### Catálogo de Professional Services

Taxa diária de consultoria de engenharia: R$ 2.500 a R$ 5.000/dia.

- **Serviço 1: Comissionamento Turnkey da Bancada.** Instalação presencial ou remota do ecossistema completo: configuração de drivers de monitor virtual, instalação dos plugins no X-Plane 12, configuração do Export.lua no DCS, calibração do rádio RadioMaster/EdgeTX em modo Joystick HID, testes de loopback e validação do fluxo de dados. Entrega: bancada rodando os exemplos do Starter Kit no primeiro dia.
- **Serviço 2: Modelagem do Veículo do Cliente no Simulador.** Recebe o arquivo CAD complexo do time de mecânica (SolidWorks, Inventor, Fusion 360). Executa o processo de redução de polígonos (defeature), extrai parâmetros de massa e inércia, configura o modelo geométrico no Plane Maker do X-Plane 12 ou cria os arquivos de configuração para o DCS. Entrega: drone ou avião proprietário do cliente voando de forma realista no simulador.
- **Serviço 3: Integração de Hardware Customizado HIL.** Se o cliente usa microcontrolador específico (STM32, TI, CLP industrial), desenvolve o driver customizado ou adapta a DLL para o protocolo serial, barramento elétrico ou pinagem exata da placa do cliente. Entrega: hardware físico acendendo LEDs e movendo atuadores na bancada integrados ao simulador.
- **Serviço 4: Módulo de Sensores Customizados.** Desenvolvimento de wrappers para sensores específicos do cliente (câmeras industriais, sensores de gás, sensores de corrente elétrica para inspeção de redes de transmissão). Entrega: novo módulo integrado ao pipeline da DLL com ruído e falhas parametrizáveis.
- **Serviço 5: Treinamento de Equipe.** Workshop de 2 a 4 dias cobrindo arquitetura da DLL, uso dos Starter Kits, leitura dos logs CSV de diagnóstico e extensão dos wrappers existentes. Entrega: equipe do cliente autossuficiente para manutenção e expansão do ecossistema.

### Exemplo de Proposta Comercial Típica

| Item | Valor |
|---|---|
| Licença Core (1 bancada) | R$ 60.000 |
| Serviço 1: Comissionamento Turnkey (5 dias) | R$ 20.000 |
| Serviço 2: Modelagem do drone do cliente | R$ 15.000 |
| Serviço 5: Treinamento de equipe (2 dias) | R$ 8.000 |
| **Total do Contrato Inicial** | **R$ 103.000** |
| Suporte Anual (opcional) | R$ 20.000/ano |

### Argumento de ROI para o Comprador

- Custo médio de 1 dia de campanha de testes de campo (combustível, diárias, logística, deslocamento, autorizações ANAC/DECEA, risco de perda do protótipo): R$ 8.000 a R$ 20.000.
- Custo de uma licença Ansys AVxcelerate com módulos aeroespaciais e HIL: > R$ 150.000/ano.
- A contratação do NEXUS-LOOP se paga integralmente ao evitar apenas 5 a 10 dias de testes de campo malsucedidos, ou ao substituir uma licença de software importado que não pode ser comprado devido a restrições de exportação de defesa.

---

## 13. Enquadramento em Editais de Subvenção Econômica FINEP Mais Inovação

Este capítulo estabelece as diretrizes estratégicas e a redação técnica para submeter o ecossistema NEXUS-LOOP ao edital **SELEÇÃO PÚBLICA MCTI/FINEP/FNDCT — Subvenção Econômica à Inovação em Fluxo Contínuo — Finep Mais Inovação Brasil — Rodada 2 — Mobilidade Sustentável**, estruturado estritamente sob o escopo de **Pesquisa Aplicada (TRL 3 a 7)**.

### A. Posicionamento Estratégico do NEXUS-LOOP como Motor de Pesquisa Aplicada

Em editais de subvenção, o foco do fomento é o **produto final inovador do cliente** (ex: drone de carga autônomo, ROV submarino de inspeção, sistema de tráfego de mobilidade aérea avançada). O NEXUS-LOOP entra na proposta como a **Infraestrutura de Engenharia de Pesquisa Aplicada**, indispensável para transformar teorias matemáticas em hardware funcional e validado.

- **Aderência à Pesquisa Aplicada:** O framework não realiza pesquisa básica (ciência pura, TRL 1 e 2). Ele entra em ação no momento em que o cliente precisa aplicar teorias de controle e inteligência artificial em um cenário real e prático de mobilidade (Linhas 1, 2 e 3 do edital), testando a viabilidade comercial e de engenharia do protótipo.
- **Aderência à Linha 1 — Mobilidade Aérea:** Viabiliza o desenvolvimento de ecossistemas de mobilidade aérea avançada através do teste de voo autônomo, segurança de voo, sistemas de tráfego aéreo e coordenação de enxames de UAVs em ambiente simulado de alta fidelidade meteorológica. Cobre diretamente os focos do edital: propulsão sustentável, novos materiais e aeroestruturas (via modelos CAD injetados no simulador), além de voo autônomo e vertiportos.
- **Aderência à Linha 2 — Mobilidade Aquaviária:** Atua no teste de sistemas de propulsão e navegação autônoma para transporte fluvial e marítimo com foco em eficiência operacional e redução de emissões, emulando sensores de profundidade, DVL e sonar de alta fidelidade sem exigir locação de embarcações reais. Suporta validação de sistemas de propulsão elétrica e híbrida e integração com outros modais de transporte — todos focos explícitos do edital.
- **Aderência à Linha 3 — Mobilidade Metroferroviária:** Posiciona-se como ambiente de simulação para ferrovia digital, validando hardwares de controle de via, velocidade, segurança e sinalização em HIL sem interromper a operação das vias reais. Cobre os focos de digitalização, eficiência energética e aumento de velocidade citados no edital.
- **Aderência à Linha 4 — Micromobilidade:** Embora secundária, a plataforma suporta validação de sistemas de controle para bicicletas e triciclos elétricos autônomos e soluções digitais de compartilhamento inteligente de micromobilidade, desde que o foco seja o transporte de pessoas e cargas (não lazer), conforme restrição do edital.
- **A Dor Curada perante os Avaliadores da FINEP:** Projetos de sistemas autônomos complexos costumam travar ou estourar orçamentos por testar a pesquisa aplicada diretamente em campo. O NEXUS-LOOP traz o campo para o laboratório de forma digital e determinística, reduzindo drasticamente o risco técnico e financeiro do projeto.

### B. Mapeamento de TRL em Pesquisa Aplicada: A Janela TRL 3 a 7

O NEXUS-LOOP é a ferramenta que garante a evolução contínua da pesquisa aplicada ao longo de toda a janela exigida pelo edital:

- **TRL 3 — Prova de Conceito Analítica (Model-in-the-Loop):**
  - *Foco da Pesquisa:* Validação inicial das equações de controle e algoritmos de IA de borda ou enxame.
  - *Mecânica NEXUS-LOOP:* O cérebro em desenvolvimento (scripts Python/MATLAB) conecta-se à DLL para receber a verdade absoluta e limpa da física do simulador (X-Plane/DCS), provando que a lógica matemática aplicada é viável sem nenhum hardware físico.

- **TRL 4 — Validação de Componentes em Laboratório (Software-in-the-Loop):**
  - *Foco da Pesquisa:* Integração do software de controle ao ambiente emulado de sensores.
  - *Mecânica NEXUS-LOOP:* O código do firmware é testado em modo SIL utilizando a emulação de sensores sintéticos (Google Maps 3D via CesiumJS). Começa a injeção de ruídos gaussianos básicos para testar a estabilidade do software frente a dados imperfeitos.

- **TRL 5 — Validação de Subsistemas em Ambiente Simulado (HIL Inicial por Software):**
  - *Foco da Pesquisa:* Teste do algoritmo rodando dentro do processador do microcontrolador real.
  - *Mecânica NEXUS-LOOP:* O hardware do cliente executa o firmware ativando o `#define MODO_SIMULACAO_HIL`. A DLL central conecta-se via USB (COM) ou Rede (UDP) e injeta as distorções dinâmicas de clima do simulador (efeito de lavagem térmica FLIR sob chuva, degradação de GPS em tempestade), validando a capacidade do processador de lidar com dados imperfeitos em tempo real.

- **TRL 6 — Protótipo de Subsistema Demonstrado em Ambiente Representativo (HIL Rígido de Bancada):**
  - *Foco da Pesquisa:* Teste de estresse do hardware final sem alteração de código, simulando a infraestrutura física real.
  - *Mecânica NEXUS-LOOP:* O hardware físico de controle (piloto automático ou computador de missão do gimbal) é acoplado à Placa Intermediária HIL física. A DLL gerencia a comunicação em barramento elétrico rígido (CAN Bus) injetando jitter de rede e falhas estocásticas de Weibull/MTBF. O sistema é estressado simulando cenários operacionais severos de laboratório que seriam impossíveis de reproduzir de forma controlada em campo.

- **TRL 7 — Demonstração do Protótipo de Sistema em Ambiente Operacional Relevante (Validação Pré-Voo):**
  - *Foco da Pesquisa:* Certificação final e mitigação completa de riscos antes da fabricação em massa do produto comercial.
  - *Mecânica NEXUS-LOOP:* O operador assume o controle do protótipo usando o rádio RadioMaster/EdgeTX real na bancada, enquanto o sistema de supervisão do cliente (Simulink/LabVIEW) monitora a resiliência do robô frente a panes simuladas. O Sniffer Open-Source grava os arquivos `.csv` com as métricas de SequenceID e SessionID para auditoria, gerando a rastreabilidade de engenharia necessária para certificar o protótipo no nível TRL 7 e provar a prontidão para testes de voo ou navegação reais.

### C. O Arranjo com ICTs: Divisão Clara de Papéis

O edital exige obrigatoriamente a participação de no mínimo uma ICT brasileira, com mínimo de 5% do orçamento total destinado a ela. O NEXUS-LOOP cria a divisão de tarefas natural para este arranjo:

- **Papel Científico da ICT (Universidade / Centro de Pesquisa):** Atua nas fases iniciais de pesquisa aplicada (TRL 3 e 4). Os pesquisadores utilizam o ambiente MIL/SIL do NEXUS-LOOP para criar e validar modelos de inteligência artificial de enxame, filtragem de sensores (Kalman, EKF), algoritmos de evitação de colisão e controle adaptativo. Suas despesas são cobertas pelos serviços de consultoria ICT no orçamento.
- **Papel Industrial da Empresa Proponente:** Absorve a pesquisa validada pela ICT e utiliza a infraestrutura HIL do NEXUS-LOOP (TRL 5, 6 e 7) para embarcar esses algoritmos no hardware real, rodar os testes de confiabilidade com falhas de Weibull e preparar o produto final para o mercado, aportando a contrapartida financeira obrigatória conforme o seu porte.

### D. Limites Financeiros do Edital para Referência

| Arranjo | Valor Mínimo | Valor Máximo | ICT Mínimo | Contrapartida (Média Empresa I) |
|---|---|---|---|---|
| Arranjo Simples | R$ 5 milhões | R$ 10 milhões | 5% do total | 30% (Simples) / 15% (Rede) |
| Arranjo em Rede | R$ 5 milhões | R$ 20 milhões | 5% do total | 20% (Rede) |

### E. Modelos de Redação Prontos para Formulários da FINEP

#### Campo: Grau de Incerteza Tecnológica e Mérito da Pesquisa Aplicada

> *"O presente projeto enquadra-se estritamente como Pesquisa Aplicada, visando transitar de forma segura e sistemática entre a prova de conceito analítica em laboratório e a demonstração de um protótipo funcional em ambiente operacional relevante (janela de TRL 3 a TRL 7). O maior grau de incerteza tecnológica reside na resposta do hardware de controle embarcado real frente às imperfeições físicas do mundo em tempo real (TRL 5 e 6). Para mitigar esse risco de forma científica e determinística, a metodologia adotará a plataforma NEXUS-LOOP. Este ecossistema atuará como um emulador de sensores sintéticos ruidosos (LIDAR, FLIR, Radar) e injetor de falhas estocásticas (Weibull/MTBF). Através de simulações integradas de Hardware-in-the-Loop (HIL) conectadas à dinâmica ambiental e de balística dos simuladores (X-Plane 12 / DCS World), o NEXUS-LOOP permitirá validar as malhas de controle e a robustez lógica do sistema antes dos ensaios de campo reais, garantindo a evolução segura da maturidade tecnológica do produto."*

#### Campo: Metodologia de Execução e Integração com a ICT

> *"A metodologia de pesquisa aplicada do projeto será estruturada em cooperação com a ICT parceira, utilizando o pipeline modular X-in-the-Loop do NEXUS-LOOP. Nas fases iniciais (TRL 3 e 4), a ICT utilizará a interface genérica do framework em nível MIL/SIL para simular e refinar os algoritmos de Inteligência Artificial de enxame e controle em ambiente de blocos (MATLAB/Simulink). Nas fases subsequentes de desenvolvimento industrial (TRL 5, 6 e 7), a empresa proponente herdará essa biblioteca de software e utilizará a DLL do NEXUS-LOOP com prioridade de thread elevada no Windows para injetar os dados tratados via barramento digital (CAN Bus / Serial CDC) direto no processador físico do veículo. A integridade dos dados e o descarte de pacotes obsoletos serão geridos por chaves de SequenceID e SessionID, e toda a auditoria será registrada por um Sniffer Open-Source em arquivos abertos .CSV, gerando a rastreabilidade de engenharia necessária para certificar o protótipo final no nível TRL 7."*

#### Campo: Relevância do Tema e Impacto Econômico

> *"A adoção da plataforma NEXUS-LOOP nacionaliza e democratiza a infraestrutura de testes de alta fidelidade para integradores de sistemas no Brasil, eliminando a dependência de suítes de software internacionais de altíssimo custo restritas por burocracias de exportação de defesa. O impacto econômico direto reflete-se na redução drástica do tempo de desenvolvimento (Time-to-Market) do veículo autônomo, na economia imediata com despesas de logística, viagens de campo e locação de frotas de ensaio, além de blindar a propriedade intelectual do projeto através de uma arquitetura de software em camadas independentes que mascara a origem dos dados de simulação."*

---

## Referências e Plataformas Padrão

### Aeronaves e Veículos de Teste Padrão Gratuitos

- **X-Plane 12:** Cessna 172 Skyhawk (aviação geral e drones de asa fixa)
- **DCS World:** Su-25T Frogfoot ou TF-51D Mustang — ambos incluídos na versão gratuita do DCS
- **Gazebo / ROS2:** modelos URDF padrão de quadrotores e UAVs da comunidade PX4/ArduPilot
- **MuJoCo (DeepMind):** modelos MJCF de referência para manipuladores robóticos (Franka Panda, UR5, Shadow Hand) — gratuitos no repositório oficial

---

### Referências Bibliográficas

#### Arquitetura de Software e Padrões de Projeto

**[1]** GAMMA, E.; HELM, R.; JOHNSON, R.; VLISSIDES, J. *Design Patterns: Elements of Reusable Object-Oriented Software.* Addison-Wesley, 1994. ISBN 0-201-63361-2.
> Base conceitual do padrão Adapter/Wrapper adotado na Camada 3 do NEXUS-LOOP para desacoplamento entre simuladores e núcleo de controle.

#### Metodologia X-in-the-Loop: HIL, SIL e MIL

**[2]** ISERMANN, R.; SCHAFFNIT, J.; SINSEL, S. Hardware-in-the-Loop Simulation for the Design and Testing of Engine-Control Systems. *Control Engineering Practice*, v. 7, n. 5, p. 643–653, 1999. DOI: 10.1016/S0967-0661(98)00175-9.
> Artigo fundacional da metodologia HIL aplicada a sistemas de controle embarcados; estabelece os princípios de temporização e determinismo que o NEXUS-LOOP implementa via SessionID/SequenceID.

**[3]** HANSELMANN, H. Hardware-in-the-Loop Simulation as a Standard Procedure for Development of ECU Software. *SAE Technical Paper* 940209, 1994. DOI: 10.4271/940209.
> Define o conceito de bancada HIL como substituto de protótipos físicos prematuros — argumento central do modelo de negócios do NEXUS-LOOP.

**[4]** DJABIN, J. et al. Scalable Hardware in the Loop (HIL) System for Real-Time Swarm Drone Control Simulation. *2024 IEEE International Workshop on Technologies for Defense and Security (TechDefense)*. DOI: 10.1109/TechDefense63521.2024.10863036.
> Valida em 2024 a abordagem do Capítulo 10: HIL escalável para enxames de drones, com coordenação distribuída em tempo real.

**[5]** SANTOS, M. A. et al. Model-in-the-Loop Design and Flight Test Validation of Flight Control Laws for a Small Fixed-Wing UAV. *Drones*, v. 9, n. 9, p. 624, 2025. DOI: 10.3390/drones9090624.
> Demonstra experimentalmente o pipeline MIL → SIL → voo real com Pixhawk/PX4, validando a metodologia dos Capítulos 3 e 4.

#### Simulação de Sensores Sintéticos e Geração de Dados

**[6]** ANSYS / TELEDYNE FLIR. *Teledyne FLIR Thermal Cameras Now Operational in Ansys AVxcelerate Sensors Suite.* Ansys Blog, 2023. Disponível em: https://www.ansys.com/blog/teledyne-flir-thermal-cameras-in-ansys-avxcelerate.
> Referência de mercado para o estado da arte em simulação de câmeras térmicas FLIR — plataforma concorrente comercial ao módulo de Carga Útil Sintética do NEXUS-LOOP (Capítulo 7).

**[7]** CESIUM / GOOGLE MAPS PLATFORM. *Photorealistic 3D Tiles from Google Maps Platform Now Included in Cesium ion.* Press Release, 26 out. 2023. Disponível em: https://cesium.com/blog/2023/10/26/photorealistic-3d-tiles-in-cesium-ion/.
> Documenta a disponibilidade geral (GA) dos Google Photorealistic 3D Tiles via CesiumJS — tecnologia de base do Cenário B do Capítulo 7 para geração oculta de imagens sintéticas.

**[8]** GOOGLE FOR DEVELOPERS. *Photorealistic 3D Tiles — Map Tiles API.* Google Maps Platform Documentation, 2024. Disponível em: https://developers.google.com/maps/documentation/tile/3d-tiles.
> Especificação técnica oficial da API utilizada pelo módulo de geração de payload visual e térmico do NEXUS-LOOP.

#### Protocolos de Comunicação para Sistemas Autônomos

**[9]** MEIER, L. et al. MAVLink: Micro Air Vehicle Communication Protocol. *QGroundControl GCS*, 2009. LGPL License. Disponível em: https://mavlink.io.
> Protocolo de referência para as mensagens `HIL_SENSOR` (#114), `HIL_GPS` (#113) e `HIL_ACTUATOR_CONTROLS` (#92) que inspiram o cabeçalho binário SessionID/SequenceID/CRC do NEXUS-LOOP.

**[10]** KOUBAA, A. et al. Micro Air Vehicle Link (MAVLink) in a Nutshell: A Survey. *IEEE Access*, v. 7, p. 87658–87680, 2019. DOI: 10.1109/ACCESS.2019.2924410.
> Survey completo do MAVLink 1.0 e 2.0, detalhando campos de segurança, autenticação e suporte a múltiplos veículos — base para o design do protocolo binário do NEXUS-LOOP.

**[11]** COUCEIRO, M. et al. Towards a Unified Decentralized Swarm Management and Maintenance Coordination Based on MAVLink. *IEEE Int. Conf. on Autonomous Robot Systems and Competitions (ICARSC)*, 2016. DOI: 10.1109/ICARSC.2016.7781964.
> Extensão do MAVLink para coordenação de enxames — valida a abordagem do Capítulo 10 de usar protocolo unificado para comunicação inter-agentes.

#### Confiabilidade, Falhas Estatísticas e Distribuição de Weibull

**[12]** ABERNETHY, R. B. *The New Weibull Handbook.* 5. ed. North Palm Beach: R. B. Abernethy, 2006. ISBN 978-0-9653062-3-2.
> Referência fundamental para o motor de falhas estatísticas do NEXUS-LOOP: distribuições de Weibull e Exponencial para MTBF/MTTR, "bathtub curve" de confiabilidade e análise de modos de falha em engenharia aeroespacial e industrial.

**[13]** WEIBULL, W. A Statistical Distribution Function of Wide Applicability. *Journal of Applied Mechanics*, v. 18, n. 3, p. 293–297, 1951.
> Artigo original de Waloddi Weibull definindo a distribuição de probabilidade usada no motor de injeção de falhas estocásticas do Capítulo 6.

#### Simulação de Robótica e Aprendizado por Reforço

**[14]** TODOROV, E.; EREZ, T.; TASSA, Y. MuJoCo: A Physics Engine for Model-Based Control. *2012 IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS)*, p. 5026–5033. DOI: 10.1109/IROS.2012.6386109.
> Artigo fundacional do simulador MuJoCo (DeepMind), compatível com o NEXUS-LOOP via Wrapper Gazebo/MuJoCo para validação MIL de algoritmos de controle de robôs articulados, manipuladores e sistemas locomotores.

**[15]** SCHULMAN, J. et al. Proximal Policy Optimization Algorithms. *arXiv preprint*, arXiv:1707.06347, 2017.
> Algoritmo PPO — o mais utilizado em pipelines de RL para robótica. O NEXUS-LOOP atua como ambiente de injeção de ruído e falhas para o treino de políticas PPO/SAC/TD3 em modo SIL, aumentando a robustez da transferência simulação → hardware real (sim-to-real gap).

**[16]** ANDRYCHOWICZ, M. et al. Learning Dexterous In-Hand Manipulation. *International Journal of Robotics Research*, v. 39, n. 1, p. 3–20, 2020. DOI: 10.1177/0278364919887447.
> Demonstra que **domain randomization** — injeção deliberada de variações nos parâmetros físicos durante o treino — é a técnica-chave para fechar o sim-to-real gap. O módulo de ruído parametrizável do NEXUS-LOOP (ruído gaussiano, drift, vibração, falhas Weibull) implementa exatamente esta abordagem para o integrador de hardware.

**[17]** MAKOVIYCHUK, V. et al. Isaac Gym: High Performance GPU-Based Physics Simulation for Robot Learning. *arXiv preprint*, arXiv:2108.10470, 2021.
> Descreve o Isaac Gym (NVIDIA) — simulador acelerado por GPU com suporte a treinamento paralelo massivo de políticas RL. O NEXUS-LOOP não replica o Isaac Gym (escopo diferente), mas é complementar: onde o Isaac treina a política, o NEXUS-LOOP valida o hardware resultante via HIL.

#### Visão Computacional Sintética e Gêmeos Digitais

**[18]** MÜLLER, M. et al. Flying Cars: From Concept to Implementation in Autonomous Drone Racing. *IEEE Robotics and Automation Letters*, v. 4, n. 4, p. 3970–3977, 2019. DOI: 10.1109/LRA.2019.2928721.
> Valida o uso de câmeras sintéticas e ambientes virtuais para treino e validação de controladores de voo em drones de alta velocidade — aplicação direta dos Capítulos 7 e 8 do NEXUS-LOOP.

**[19]** SYNOPSYS. *How Digital Twins Will Dramatically Reduce Field Testing for Autonomous Vehicles.* Synopsys Blog, jun. 2022. Disponível em: https://blogs.synopsys.com/optical-solutions/2022/06/13/how-digital-twins-will-dramatically-reduce-field-testing-for-autonomous-vehicles.
> Contextualiza a proposta de valor do NEXUS-LOOP no mercado global: modelos de sensores físicos combinados com ambientes virtuais para gerar dados sintéticos que eliminam dependência de testes de campo.

#### Padrões e Normas Técnicas

**[20]** RTCA. *DO-178C: Software Considerations in Airborne Systems and Equipment Certification.* Washington, DC: RTCA, 2012.
> Norma que rege o desenvolvimento de software aeronáutico embarcado. A rastreabilidade de engenharia gerada pelo Sniffer Open-Source do NEXUS-LOOP (logs CSV com SessionID/SequenceID) contribui para a evidência de verificação e validação exigida pelo DO-178C.

**[21]** RTCA. *DO-254: Design Assurance Guidance for Airborne Electronic Hardware.* Washington, DC: RTCA, 2000.
> Norma complementar ao DO-178C para hardware eletrônico embarcado. A bancada HIL do NEXUS-LOOP suporta a coleta de evidências de teste necessárias para níveis de assurance DAL A–D.

**[22]** IEEE. *IEEE 1641: Standard for Signal and Test Definition.* Institute of Electrical and Electronics Engineers, 2010.
> Define métodos formais para modelos de sinais de teste e comportamento de sistemas de teste — referência para a estrutura de injeção de sinais da Placa Intermediária HIL física descrita no Capítulo 4.

#### Fomento à Inovação

**[23]** MCTI / FINEP / FNDCT. *Seleção Pública: Subvenção Econômica à Inovação em Fluxo Contínuo — Finep Mais Inovação Brasil — Rodada 2 — Mobilidade Sustentável.* Anexo 1. Brasília: FINEP, janeiro 2026.
> Edital de referência para o enquadramento do NEXUS-LOOP como infraestrutura de pesquisa aplicada TRL 3–7. Linhas temáticas: Mobilidade Aérea, Aquaviária, Metroferroviária e Micromobilidade.

---

### Protocolos e Padrões Técnicos de Implementação

- **MAVLink 2.0:** mensagens `HIL_SENSOR` (#114), `HIL_GPS` (#113), `HIL_ACTUATOR_CONTROLS` (#92) como referência para o cabeçalho binário do protocolo interno
- **Endianness:** Little-Endian nativo; `htons`/`ntohs` para campos de cabeçalho onde interoperabilidade Big-Endian é exigida
- **Point Cloud LIDAR:** formato XYZI compatível com PCL (Point Cloud Library) e ROS2
- **URDF/MJCF:** formatos de descrição de robôs suportados como entrada para configuração de modelos dinâmicos nos simuladores integrados
- **OGC 3D Tiles:** padrão aberto do Open Geospatial Consortium usado pelo CesiumJS + Google Maps Platform para geração de terreno sintético

### Tecnologias de Software Utilizadas

- **Captura de Vídeo:** Microsoft DXGI Desktop Duplication API + biblioteca SharpDX (C#)
- **Geração de Imagens Sintéticas:** CesiumJS + Google Photorealistic 3D Tiles (OGC 3D Tiles) + Shaders WebGL GLSL
- **Headless Browser:** CefSharp (Chromium Embedded Framework) ou Puppeteer Sharp para renderização offscreen
- **Simuladores de Robótica:** MuJoCo (DeepMind, gratuito desde 2022), Gazebo Sim / ROS2, Webots (Cyberbotics)
- **Comunicação de Rede:** `System.Net.Sockets.UdpClient`, `System.IO.Ports.SerialPort` com `ThreadPriority.AboveNormal`
- **Análise de Dados:** scripts Python (Pandas/NumPy), MATLAB, LabVIEW para leitura dos logs CSV do Sniffer
- **Diagnóstico de Rede:** Wireshark com dissector Lua customizado para o protocolo binário NEXUS-LOOP


---

## Propriedade Intelectual e Contato

**NEXUS-LOOP** é uma criação original de **Omar Achraf**, desenvolvida no âmbito da **UVSBR — Unmanned Vehicle Systems do Brasil**.

| | |
|---|---|
| **Titular** | Omar Achraf |
| **Empresa** | UVSBR — Unmanned Vehicle Systems do Brasil |
| **Website** | https://uvsbr.com.br |
| **E-mail corporativo** | omar@uvsbr.com.br |
| **E-mail pessoal** | omarachraf@gmail.com |
| **Telefone / WhatsApp** | +55 41 9 9965-5395 |

**© 2026 UVSBR — Todos os direitos reservados.**

Este documento, a arquitetura de software, os protocolos, os algoritmos e o modelo de negócios descritos neste material constituem propriedade intelectual exclusiva de Omar Achraf / UVSBR. Qualquer forma de reprodução, distribuição, sublicenciamento, engenharia reversa ou uso comercial — total ou parcial — está estritamente proibida sem autorização expressa e por escrito do titular.

Para licenciamento, parcerias, projetos de pesquisa aplicada ou contratação de Professional Services, entre em contato através dos canais acima.
