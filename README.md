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
```

## ⚙️ Filosofia de controle

O controle da bomba foi desenvolvido separando três conceitos principais:

- **Comando:** existe uma solicitação para a bomba funcionar;
- **Permissão:** as condições de processo permitem o acionamento;
- **Estado do sistema:** o sistema está habilitado para executar o comando.

Essa separação evita que uma simples solicitação de funcionamento seja suficiente para acionar a bomba.

### Operação automática

No modo automático:

1. O operador realiza o START;
2. O sistema é habilitado;
3. O nível do tanque é monitorado;
4. Quando o nível atinge a condição de nível baixo, existe uma solicitação de bombeamento;
5. O PLC verifica os permissivos e intertravamentos;
6. Se todas as condições forem satisfeitas, a bomba é acionada;
7. O nível do tanque aumenta;
8. Ao atingir o nível alto, a bomba é desligada;
9. O nível volta a diminuir;
10. Quando o nível baixo é atingido novamente, um novo ciclo pode ser iniciado.

### Operação manual

No modo manual:

1. O operador realiza o START;
2. O sistema é habilitado;
3. O operador solicita o funcionamento através do comando manual;
4. O PLC verifica os permissivos e intertravamentos;
5. A bomba é acionada somente se as condições de operação forem válidas;
6. O comando STOP pode interromper a operação.

### Permissivos e intertravamentos

A bomba não é acionada somente porque existe um comando de funcionamento.

Antes da partida, o PLC verifica condições como:

- Ausência de nível alto;
- Ausência de falha de nível;
- Ausência de falha retida;
- Sistema liberado;
- Sistema habilitado;
- Comando de funcionamento ativo.

Dessa forma, o comando de partida e a autorização para acionamento são tratados separadamente.

### Tratamento de falhas

Quando os sensores de nível baixo e alto permanecem ativos simultaneamente, o sistema identifica uma inconsistência de nível.

Essa condição gera uma falha que é retida pelo PLC.

O comportamento implementado é:

```text
Falha detectada
      ↓
Bomba desligada
      ↓
Falha retida
      ↓
Condição física corrigida
      ↓
Sistema continua bloqueado
      ↓
RESET
      ↓
Sistema liberado
      ↓
Novo START
      ↓
Sistema habilitado
```
