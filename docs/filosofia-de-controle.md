# Filosofia de Controle

## Objetivo

A lógica de controle foi desenvolvida com o objetivo de garantir que a bomba somente seja acionada quando houver uma solicitação de operação e todas as condições necessárias para o funcionamento estiverem satisfeitas.

A estratégia foi construída separando três conceitos:

- Comando;
- Permissão;
- Estado do sistema.

Essa separação evita que uma simples solicitação de acionamento seja suficiente para ligar a bomba.

---

## 1. Comando

O comando representa uma solicitação para que a bomba opere.

No projeto, essa solicitação depende do modo de operação.

### Modo automático

No modo automático, a condição de nível baixo gera a solicitação de funcionamento da bomba.

```text
Modo automático
      +
Nível baixo
      ↓
Comando para ligar
```

Entretanto, o comando sozinho não liga a bomba.

A solicitação ainda precisa passar pelas permissões e pelo estado do sistema.

### Modo manual

No modo manual, a solicitação de funcionamento é realizada através do comando manual.

```text
Modo manual
      +
Liga manual
      ↓
Comando para ligar
```

## 2. Permissão

A permissão determina se a bomba pode realmente ser acionada.

A bomba não pode operar quando:

- O nível alto está ativo;
- Existe uma falha de nível;
- Existe uma falha de nível retida.

Portanto, uma solicitação de funcionamento não significa necessariamente que a bomba será ligada.

```text
COMANDO
   │
   ▼
PERMISSÕES
   │
   ├── Nível alto?
   ├── Falha?
   └── Falha retida?
   │
   ▼
Pode operar?
```

Essa separação entre comando e permissão é importante em sistemas industriais porque permite que uma condição de segurança ou processo impeça a atuação mesmo quando existe uma solicitação de funcionamento.

## 3. Estado do Sistema

Além do comando e das permissões, o sistema possui estados internos que determinam se a operação está liberada.

São utilizados principalmente:

- xSistemaLiberado
- xSistemaHabilitado

### Sistema liberado

Indica que o sistema não está bloqueado por uma falha retida.

Após uma falha, o sistema permanece bloqueado mesmo depois que a condição física responsável pelo problema desaparece.

É necessário executar o procedimento de RESET.

### Sistema habilitado

Representa a autorização operacional obtida através de um novo START.

Isso cria uma separação importante:

```text
RESET
  ↓
Sistema liberado
  ↓
START
  ↓
Sistema habilitado
  ↓
Bomba pode operar
```

O RESET, portanto, não funciona como um comando de partida.

## 4. Operação automática

A sequência normal do modo automático é:

```text
START
  ↓
Sistema habilitado
  ↓
Monitoramento do nível
  ↓
Nível baixo
  ↓
Comando de ligar
  ↓
Verificação das permissões
  ↓
Bomba ligada
  ↓
Nível aumenta
  ↓
Nível alto
  ↓
Bomba desligada
  ↓
Nível diminui
  ↓
Nível baixo
  ↓
Novo ciclo
```

A simulação do processo permite visualizar essa sequência continuamente no WebVisu.

## 5. Operação manual

No modo manual, o operador possui o comando direto de solicitação da bomba.

A sequência é:

```text
START
  ↓
Sistema habilitado
  ↓
Liga manual
  ↓
Verificação das permissões
  ↓
Bomba ligada
```

O modo manual não elimina as proteções do sistema.

Mesmo em operação manual, condições como nível alto e falhas continuam impedindo ou interrompendo o funcionamento da bomba.

## 6. Tratamento de falhas

Uma das principais condições de falha do projeto é a inconsistência dos sensores de nível.

A inconsistência ocorre quando os dois sensores estão ativos simultaneamente:

```text
Nível baixo = 1
Nível alto  = 1
        ↓
Inconsistência de nível
        ↓
Falha de nível
```

Essa condição é considerada inválida para o processo e gera uma falha retida.

## 7. Retenção da falha

A variável **xFalhaTravada** foi utilizada para memorizar a ocorrência da falha.

O comportamento é:

```text
Falha detectada
      ↓
Falha retida
      ↓
Bomba desligada
      ↓
Sistema bloqueado
```

Mesmo que a condição física da falha desapareça:

```text
Falha física corrigida
      ↓
Falha instantânea = 0
      ↓
Falha retida = 1
```

O sistema permanece bloqueado.

Isso evita que uma simples correção da condição de processo provoque uma partida automática.

## 8. Procedimento de RESET

Para liberar o sistema após uma falha:

```text
Falha física corrigida
        ↓
RESET
        ↓
Falha retida removida
        ↓
Sistema liberado
        ↓
Novo START
        ↓
Sistema habilitado
```

O RESET não liga a bomba.

Essa separação foi implementada propositalmente para evitar que a recuperação de uma falha provoque uma partida automática do equipamento.

## 9. Intertravamento por nível alto

O nível alto possui prioridade sobre o comando de funcionamento.

Quando o nível alto é detectado:

```text
Nível alto
   ↓
Permissão removida
   ↓
Bomba desligada
```

Portanto, mesmo que exista um comando de funcionamento, a bomba não deve permanecer ligada enquanto a condição de nível alto estiver presente.

## 10. Transição entre modos

A mudança entre automático e manual também possui tratamento específico.

O objetivo é evitar que uma mudança de modo mantenha indevidamente uma condição de funcionamento herdada do modo anterior.

As transições são monitoradas através da variável:

**xModoAnterior**

Dessa forma, o programa consegue identificar quando ocorreu uma mudança entre:

- Manual → Automático;
- Automático → Manual.

Cada transição possui tratamento próprio para garantir que o estado da bomba e da habilitação do sistema seja coerente com o novo modo.

## 11. Princípio geral da lógica

A filosofia geral pode ser resumida da seguinte maneira:

```text
                SOLICITAÇÃO
                     │
                     ▼
                PERMISSÕES
                     │
                     ▼
              ESTADO DO SISTEMA
                     │
                     ▼
                  BOMBA
```

Ou seja:

### O operador ou o processo pode solicitar a operação, mas somente as condições permitidas pelo sistema podem autorizar a atuação.

Esse princípio é fundamental para a construção de uma lógica de controle industrial organizada e previsível.
