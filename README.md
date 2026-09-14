# Estação de Bombeamento Industrial — CODESYS + HMI

![Interface HMI da estação de bombeamento](imagens/ihm-principal.png)

Sistema de controle, supervisão e simulação de uma estação de bombeamento industrial desenvolvido no ambiente CODESYS.

O projeto utiliza Ladder Logic para implementação da lógica de controle e Structured Text para simulação do processo, com supervisão e operação através de uma interface WebVisu.

## 📋 Sobre o projeto

O sistema simula uma estação de bombeamento responsável pelo controle do nível de um tanque.

A aplicação possui:

- Operação automática e manual;
- Comandos de START, STOP e RESET;
- Controle de uma bomba;
- Sensores de nível baixo e alto;
- Permissivos e intertravamentos de processo;
- Detecção de inconsistência entre sensores;
- Retenção de falhas;
- Bloqueio de partida após falha;
- Simulação do nível do tanque;
- Contador de ciclos da bomba;
- Horímetro de funcionamento;
- Interface HMI desenvolvida com WebVisu.

## 🎯 Objetivo

Desenvolver e validar uma lógica de controle PLC para uma estação de bombeamento industrial, utilizando CODESYS, com foco em:

- Controle de processo;
- Lógica Ladder;
- Operação manual e automática;
- Intertravamentos;
- Tratamento de falhas;
- Supervisão através de HMI;
- Simulação de processo sem necessidade de hardware físico.

## 🛠️ Tecnologias utilizadas

- CODESYS
- Ladder Logic (LD)
- Structured Text (ST)
- WebVisu
- PLC Runtime
- Simulação de processo

## 🏗️ Arquitetura do sistema

O sistema foi estruturado em três camadas principais:

### 1. Processo

Representa o processo industrial simulado:

- Tanque;
- Sensor de nível baixo;
- Sensor de nível alto;
- Bomba de transferência.

### 2. Controle — PLC

O CODESYS executa a lógica de controle da estação.

A lógica Ladder é responsável por:

- Gerenciamento dos comandos;
- Operação automática e manual;
- Permissivos de acionamento;
- Intertravamentos;
- Detecção de inconsistência dos sensores;
- Retenção de falhas;
- Controle da bomba;
- Gerenciamento de START, STOP e RESET.

### 3. Supervisão — HMI

A interface WebVisu permite ao operador:

- Visualizar o nível do tanque;
- Visualizar os sensores;
- Selecionar o modo de operação;
- Comandar START, STOP e RESET;
- Acionar a operação manual;
- Visualizar o estado da bomba;
- Visualizar falhas;
- Acompanhar ciclos da bomba;
- Acompanhar o horímetro.

### Fluxo do sistema

```text
PROCESSO
   │
   ├── Nível Baixo
   ├── Nível Alto
   │
   ▼
┌─────────────────────────┐
│      PLC - CODESYS      │
│                         │
│  Ladder Logic           │
│  Permissivos            │
│  Intertravamentos       │
│  Falhas                 │
│  Modos de operação      │
└────────────┬────────────┘
             │
             ▼
          BOMBA
             │
             ▼
      SIMULAÇÃO DO
         PROCESSO

             ▲
             │
      ┌──────┴──────┐
      │   WebVisu   │
      │     HMI     │
      └─────────────┘
