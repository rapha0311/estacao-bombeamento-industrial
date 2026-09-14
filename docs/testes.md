# Testes e Validação

## Objetivo

Os testes foram realizados no ambiente de simulação do CODESYS para verificar o comportamento da lógica de controle, dos intertravamentos, do tratamento de falhas e da interface WebVisu.

A validação foi realizada através da alteração das condições do processo e da observação das variáveis internas, estado da bomba e indicadores da HMI.

---

## 1. Testes funcionais

| Teste | Condição aplicada | Resultado esperado | Resultado |
|---|---|---|---|
| Partida manual | START + modo manual + comando manual | Bomba liga | Aprovado |
| Parada manual | STOP durante operação | Bomba desliga | Aprovado |
| Partida automática | START + modo automático + nível baixo | Bomba liga | Aprovado |
| Nível alto | Nível alto ativo durante operação | Bomba desliga | Aprovado |
| Nível alto na partida | START com nível alto | Bomba permanece desligada | Aprovado |
| Inconsistência de sensores | Nível baixo + nível alto simultaneamente | Falha detectada | Aprovado |
| Falha durante operação | Falha aplicada com bomba ligada | Bomba desliga e falha é retida | Aprovado |
| Correção da falha | Condição física da falha removida | Falha permanece retida | Aprovado |
| RESET | RESET após correção da falha | Sistema liberado sem ligar a bomba | Aprovado |
| Novo START | START após RESET | Sistema habilitado | Aprovado |
| RESET + START durante falha | Ambos acionados simultaneamente | Sistema não deve iniciar | Aprovado |
| Mudança Manual → Automático | Alteração do modo durante operação | Estado do sistema é tratado pela lógica de transição | Aprovado |
| Mudança Automático → Manual | Alteração do modo durante operação | Estado da bomba/sistema é tratado pela lógica de transição | Aprovado |

---

## 2. Teste do ciclo automático

O ciclo automático foi validado utilizando a simulação do nível do tanque.

### Sequência

```text
START
  ↓
Sistema habilitado
  ↓
Nível baixo
  ↓
Bomba liga
  ↓
Nível aumenta
  ↓
Nível alto
  ↓
Bomba desliga
  ↓
Nível diminui
  ↓
Nível baixo
  ↓
Bomba liga novamente
```

O ciclo foi executado repetidamente durante a simulação.

### Resultado

**Aprovado**

O sistema realizou automaticamente os ciclos de enchimento e esvaziamento do tanque conforme as condições programadas.

## 3. Teste de falha dos sensores

Foi aplicada a condição:

```text
xNivelBaixo = TRUE
xNivelAlto  = TRUE
```

Essa combinação representa uma inconsistência entre os sensores.

### Comportamento esperado

```text
Inconsistência
      ↓
Falha de nível
      ↓
Falha retida
      ↓
Bomba desligada
      ↓
Sistema bloqueado
```

### Resultado

**Aprovado**

A inconsistência foi detectada e a falha permaneceu retida mesmo após a correção da condição dos sensores.

## 4. Teste de recuperação de falha

Após a correção da condição de falha, foi verificado se o sistema retornaria automaticamente à operação.

### Comportamento esperado

A correção da condição física não deve iniciar a bomba automaticamente.

O operador deve executar:

```text
Correção da falha
      ↓
RESET
      ↓
START
      ↓
Nova operação
```

### Resultado

**Aprovado**

O RESET liberou o sistema sem provocar a partida da bomba.

Foi necessário executar um novo START para permitir uma nova operação.

## 5. Teste de intertravamento por nível alto

Durante a operação, o nível alto foi ativado.

### Comportamento esperado

A condição de nível alto deve remover a permissão de funcionamento e desligar a bomba.

### Resultado

**Aprovado.**

A bomba permaneceu desligada enquanto a condição de nível alto estava presente.

Após a remoção da condição, o sistema voltou a permitir uma nova solicitação de operação conforme a lógica programada.

## 6. Teste dos comandos da HMI

Os principais comandos da WebVisu foram testados:

- START;
- STOP;
- RESET;
- Modo automático;
- Modo manual;
- Liga manual.

Também foram verificados os indicadores de:

- Sistema liberado;
- Sistema habilitado;
- Bomba;
- Falha;
- Nível baixo;
- Nível alto.

### Resultado

**Aprovado.**

Os comandos e indicadores apresentaram comportamento compatível com a lógica implementada no PLC.

## 7. Teste do contador de ciclos

O contador foi desenvolvido para registrar somente a transição:

```text
Bomba = 0
     ↓
Bomba = 1
```

Dessa forma, uma bomba permanecendo ligada durante vários ciclos de execução do PLC não gera múltiplas contagens.

### Resultado

**Aprovado.**

O contador incrementou somente quando ocorreu uma nova partida da bomba.

## 8. Teste do horímetro

O horímetro foi implementado para acumular o tempo enquanto **xBomba** permanece ativa.

### Resultado

**Aprovado.**

O valor acumulado aumentou durante o funcionamento da bomba e permaneceu estável quando a bomba estava desligada.

*Observação: o horímetro utilizado neste projeto é uma simulação baseada no ciclo de execução de 20 ms utilizado durante os testes.*

## 9. Critérios de validação

O projeto foi considerado funcionalmente validado quando:

- Os comandos produziram os estados esperados;
- Os permissivos impediram acionamentos indevidos;
- O nível alto interrompeu a operação;
- As inconsistências dos sensores foram detectadas;
- As falhas foram retidas;
- O RESET não provocou partida automática;
- Um novo START foi necessário após uma falha;
- As transições entre modos foram tratadas;
- O ciclo automático funcionou continuamente;
- A HMI apresentou os estados principais do processo;
- O contador de ciclos funcionou corretamente;
- O horímetro acumulou o tempo de funcionamento.

## 10. Resultado geral

### Status do projeto: FUNCIONALMENTE VALIDADO

A lógica de controle, a simulação do processo e a interface WebVisu foram testadas individualmente e em conjunto.

Os principais cenários de operação normal, parada, falha, recuperação e mudança de modo apresentaram o comportamento esperado.
