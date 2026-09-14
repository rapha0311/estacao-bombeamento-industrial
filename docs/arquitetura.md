# Arquitetura do Sistema

## Visão geral

O projeto foi desenvolvido utilizando uma arquitetura baseada em três camadas:

1. Processo
2. Controle PLC
3. Supervisão e operação HMI

## Camada de Processo

Representa o processo industrial simulado.

Principais elementos:

- Tanque;
- Sensor de nível baixo;
- Sensor de nível alto;
- Bomba.

## Camada de Controle PLC

Responsável pela tomada de decisão do sistema.

Implementada no CODESYS utilizando Ladder Logic.

Principais funções:

- Controle da bomba;
- Gerenciamento de START e STOP;
- Operação manual e automática;
- Permissivos;
- Intertravamentos;
- Detecção de falhas;
- Retenção de falhas;
- RESET;
- Gerenciamento dos estados do sistema.

## Camada de Simulação

Desenvolvida em Structured Text.

Responsável por simular:

- Variação do nível do tanque;
- Estados dos sensores;
- Ciclos da bomba;
- Horímetro.

## Camada HMI

Implementada utilizando WebVisu.

Permite:

- Visualizar o nível do tanque;
- Visualizar os sensores;
- Selecionar o modo de operação;
- Executar START, STOP e RESET;
- Acionar a bomba em modo manual;
- Visualizar falhas;
- Visualizar ciclos da bomba;
- Visualizar horas de funcionamento.

## Fluxo do sistema

```text
PROCESSO
   │
   │ Sensores
   ▼
PLC / CODESYS
   │
   ├── Lógica Ladder
   ├── Permissivos
   ├── Intertravamentos
   ├── Falhas
   └── Controle da bomba
   │
   ├──────────────► BOMBA
   │
   ▼
SIMULAÇÃO DO PROCESSO
   │
   ▼
WEBVISU / HMI
   │
   ├── Comandos
   ├── Status
   ├── Alarmes
   └── Informações do processo
