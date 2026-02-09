# Automação de Máquina de Enchimento de Caixas

<p align="center">
  <img src="https://img.shields.io/badge/Categoria-Projeto%20Académico-blue" alt="Categoria: Projeto Académico">
  <img src="https://img.shields.io/badge/Área-Automação%20Industrial-orange" alt="Área: Automação Industrial">
  <img src="https://img.shields.io/badge/Programação-Ladder-red" alt="Programação: Ladder">
  <img src="https://img.shields.io/badge/PLC-Fatek%20FBs--20MC-purple" alt="PLC: Fatek FBs-20MC">
</p>

> [Projeto Académico] Desenvolvimento e implementação em linguagem Ladder para a automação de uma máquina de enchimento automático de caixas, com controlo de tapete, enchimento por nível ou tempo, contagem de caixas e sinalização de estado. Projeto realizado no âmbito da UC de Automação Industrial do CTeSP em Instalações Elétricas e Automação.

---

## Índice

- Introdução e Objetivos
- Descrição do Funcionamento da Máquina
  - Sequência de Operação
  - Sinalização Luminosa
- Componentes e Lógica de Controlo
  - Entradas
  - Saídas
  - Memórias
  - Contadores
  - Temporizadores
  - Simbologia Ladder Utilizada
- Desenvolvimento do Programa Ladder
  - Estado de Repouso
  - Arranque da Máquina
  - Caixa Detetada
  - Estado Caixa Cheia
  - Caixa Não Cheia
  - Loop de Ciclo
  - Paragem de Ciclo - Stop
  - Limite de Caixas Cheias
- Testes e Verificação
  - Teste de Funcionamento
  - Teste de Pressionar Start e Stop ao Mesmo Tempo
- Conclusão e Aprendizagens
- Documentação do Projeto
- Licença

---

### 1. Introdução e Objetivos

Este projeto, desenvolvido no âmbito da Unidade Curricular de **Automação Industrial** do **CTeSP em Instalações Elétricas e Automação** da ESTGA – Universidade de Aveiro, teve como objetivo principal o desenvolvimento da programação em linguagem **Ladder** para uma máquina de enchimento automático de caixas. O trabalho focou-se na aplicação prática de conceitos de automação, garantindo o desenvolvimento funcional, a fácil interpretação da lógica e a correspondência das entradas e saídas a uma aplicação real.

O projeto foi elaborado na plataforma de programação e simulação **WinProLadder** da Fatek, utilizando o autómato **Fatek FBs-20MC**.

### 2. Descrição do Funcionamento da Máquina

O sistema consiste num processo de enchimento automático de caixas num tapete rolante. O processo não é responsável pela colocação ou remoção das caixas, focando-se no seu posicionamento e enchimento.

#### 2.1. Sequência de Operação

-   **Início/Fim do Processo:** O processo inicia ao pressionar um botão **START** (Normalmente Aberto) e termina ao pressionar um botão **STOP** (Normalmente Fechado).
-   **Movimento do Tapete:** O tapete arranca automaticamente após o processo ser iniciado.
-   **Posicionamento da Caixa:** O tapete é parado quando a face lateral direita da caixa é detetada por um **fotosensor** (ON-OFF, Normalmente Aberto).
-   **Enchimento da Caixa:** Com a caixa em posição e o tapete parado, um **solenoide** é ativado para abrir a válvula do funil. O enchimento termina quando um **sensor de nível** é ativado (ON) ou após 5 segundos, o que ocorrer primeiro.
-   **Reinício do Tapete:** O tapete arranca automaticamente 10 segundos após o fim do enchimento de uma caixa.
-   **Contagem de Caixas:** O processo é automaticamente parado após o enchimento de 10 caixas. Pode ser reiniciado por um operador, pressionando novamente o botão START.
-   **Paragem de Emergência:** Todas as ações em curso são interrompidas/paradas quando o botão STOP é pressionado.

#### 2.2. Sinalização Luminosa

-   **Luz de Caixa Cheia (Amarela):** Ativada quando a caixa fica cheia e permanece ligada enquanto a caixa estiver a ser deslocada e deixar de ser detetada pelo fotosensor.
-   **Luz de Operação (Verde):** Ativada enquanto o processo estiver a operar.
-   **Luz de Paragem (Vermelha):** Ativada enquanto o processo estiver parado.

### 3. Componentes e Lógica de Controlo

#### 3.1. Entradas

| Endereço | Nomenclatura | Descrição |
| :------- | :----------- | :-------- |
| X1 | BP_Start | Botão de pressão Start NA |
| X2 | BP_Stop | Botão de pressão Stop NF |
| X3 | Q_Fotosensor | Fotosensor de deteção de Caixa |
| X4 | Q_SensorNivel | Sensor de Nível de Líquido das Caixas |

#### 3.2. Saídas

| Endereço | Nomenclatura | Descrição |
| :------- | :----------- | :-------- |
| Y1 | H_Operação | Luz de Operação (Verde) |
| Y2 | H_Paragem | Luz de Paragem (Vermelha) |
| Y3 | H_CxCheia | Luz de Caixa Cheia (Amarela) |
| Y4 | MT | Motor do Tapete |
| Y5 | YV1-YV2 | Solenoide Enchimento de Caixas |

#### 3.3. Memórias

Memórias retentivas foram utilizadas para gerir os estados da máquina (Repouso, Start, Stop, Avanço de Ciclo, Retomar Ciclo, Contador, Timer, Caixa Detetada) e garantir a persistência de estados mesmo com a desenergização.

#### 3.4. Contadores

-   **C1 (ContaCxCheias):** Contador crescente (CTU) com limite de 10 caixas. Retentivo, incrementa a cada sinal externo (transição de 0 para 1) e ativa a sua saída ao atingir o limite. Resetado por um pulso no contacto CLR.

#### 3.5. Temporizadores

-   **T200 (Timer5s):** Temporizador com delay na subida (TON) de 5 segundos para o enchimento de caixas.
-   **T255 (Timer10s):** Temporizador com delay na subida (TON) de 10 segundos para o tempo de espera após o enchimento.

#### 3.6. Simbologia Ladder Utilizada

-   **Contacto Normalmente Aberto (NO):** Verdadeiro quando o sinal físico está presente.
-   **Contacto Normalmente Fechado (NC):** Verdadeiro quando o sinal físico está ausente.
-   **Saídas:** Ativam atuadores fisicamente quando o símbolo é ativado no programa.
-   **Set (S) e Reset (R):** Blocos lógicos que forçam um estado (verdadeiro/falso) a uma variável booleana, mantendo-o até ser alterado.
-   **Função Menor Que (<):** Função matemática que ativa uma saída enquanto uma variável selecionada for menor que um valor definido.
-   **Diferenciadas (Flanco Positivo/Negativo):** Instruções que dão um pulso de um ciclo quando detetam uma mudança de estado (0 para 1 ou 1 para 0).
-   **Rungs:** Linhas de programação executadas de cima para baixo e da esquerda para a direita.

### 4. Desenvolvimento do Programa Ladder

O programa Ladder foi desenvolvido com foco na organização, comentários detalhados e uso de símbolos globais/locais. A estrutura incluiu:

-   **Estado de Repouso:** Estado inicial da máquina ao ser energizada, condicionado pelo contador de caixas ser 0 e a luz de Operação desligada.
-   **Arranque da Máquina:** Ativado pelo botão START, transita do estado de Repouso.
-   **Caixa Detetada:** Lógica para parar o tapete quando o fotosensor deteta a caixa.
-   **Estado Caixa Cheia:** Lógica para ativar a luz de caixa cheia e gerir o enchimento por sensor de nível ou tempo.
-   **Caixa Não Cheia:** Tratamento para casos onde o enchimento não atinge o nível no tempo limite.
-   **Loop de Ciclo:** Lógica para reiniciar o tapete após o enchimento e o tempo de espera.
-   **Paragem de Ciclo - Stop:** Interrupção de todas as ações em curso e ativação da luz de paragem.
-   **Limite de Caixas Cheias:** Paragem automática do processo após 10 caixas e espera por novo comando START.

### 5. Testes e Verificação

Foram realizados testes rigorosos para verificar o correto funcionamento do programa:

-   **Teste de Funcionamento:** Simulação completa do ciclo de enchimento, incluindo o arranque, posicionamento, enchimento, reinício do tapete e paragem por limite de caixas.
-   **Teste de Pressionar Start e Stop ao Mesmo Tempo:** Verificação da prioridade e segurança do sistema em caso de comandos conflitantes.

### 6. Conclusão e Aprendizagens

Este trabalho prático foi fundamental para consolidar os conhecimentos em programação Ladder e automação industrial. A experiência permitiu aprofundar a compreensão sobre a utilização de contadores, temporizadores e a lógica de controlo sequencial, essenciais para o desenvolvimento de sistemas automatizados robustos e seguros. A atenção à organização, comentários e correspondência com aplicações reais foi uma aprendizagem chave.

### 7. Documentação do Projeto

O relatório completo do projeto (`RelatórioProjetoAI.pdf`) e o enunciado original (`trabalho_pratico_1(1).pdf`) estão disponíveis em formato PDF na pasta `project-files/`.
### 8. Licença

Este projeto está licenciado sob a **MIT License**. Veja o ficheiro `LICENSE` para mais detalhes.
