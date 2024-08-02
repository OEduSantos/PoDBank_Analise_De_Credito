# Entendimento do Negócio

## Abordagem CRISP-DM

O projeto segue a metodologia CRISP-DM (Cross-Industry Standard Process for Data Mining) com o objetivo de prever a capacidade de um cliente para reembolsar um empréstimo. A PoD Bank busca expandir o acesso ao crédito para clientes com histórico de crédito insuficiente ou inexistente. Diversas fontes de dados, como registros de transações bancárias, histórico de pagamentos e informações socioeconômicas, são utilizadas para construir um modelo preditivo que avalia a probabilidade de inadimplência.

### Objetivo

O objetivo é auxiliar a empresa a minimizar os riscos de crédito enquanto possibilita o acesso a empréstimos para indivíduos que tradicionalmente não teriam essa oportunidade, equilibrando responsabilidade financeira e inclusão social.

---

# Entendimento dos Dados

## Visão Geral da Carteira

### Impressões Iniciais

A base de dados abrange 215.257 pessoas, das quais 8% (aproximadamente 17 mil) são inadimplentes. A faixa etária varia de 21 a 69 anos, com uma média de 43 anos. A maior taxa de inadimplência é observada entre 30 e 40 anos.

### Análise Demográfica

- **Gênero**: 67% da base é composta por mulheres, com uma taxa de inadimplência de 7%. Homens representam 33% da base, com uma taxa de 10%. Há três registros sem informação de gênero, todos adimplentes.
- **Estado Civil**: 64% são casados, com uma taxa de inadimplência de 7,59%. Casados apenas no civil (9,92%) e solteiros (9,90%) seguem em ordem.

### Análise Econômica e Financeira

A renda anual varia de 25.650,00P a 13.500.000,00P, com uma média de 168.556,85P. 

- **Ocupação**: 52% estão empregados, com uma inadimplência de 9,56%. Comerciantes (23%) e aposentados (18%) têm taxas de inadimplência de 7,6% e 5,4%, respectivamente.

### Análise dos Empréstimos

- **Valores dos Empréstimos**: Variam de 45.000,00P a 4.050.000,00P, com uma média de 599.496,00P.
- **Prazo dos Empréstimos**: Duração mínima de 8 meses, média de 22 meses e máxima de 45 meses.
- **Tipos de Empréstimos**: Predominantemente pessoais (inadimplência de 8,36%) e rotativos (inadimplência de 5,43%).

Esses insights iniciais são fundamentais para compreender o perfil dos clientes e direcionar a modelagem preditiva.

---

# Preparação dos Dados

A preparação dos dados envolveu várias etapas para garantir a qualidade e a completude do conjunto de dados:

## Limpeza dos Dados

- Foi recebido um documento com o nome e descrição das variáveis do projeto; variáveis no conjunto de dados que não estavam presentes na documentação foram removidas.

## Criação de Variáveis

- O processo de criação de Variáveis de Agregação com janelas temporais resultou em uma ABT com 4.477 variáveis.

## Seleção de Variáveis

Para a seleção de variáveis, foram realizadas as seguintes etapas:

- **Geração de Metadados**: Um DataFrame foi criado contendo metadados das variáveis, incluindo tipo de dados, quantidade e percentual de valores nulos e cardinalidade, para entender a estrutura e qualidade dos dados.
- **Filtragem de Colunas com Valores Nulos**: Colunas com percentual elevado de valores nulos foram identificadas e removidas para garantir a integridade e qualidade dos dados a serem analisados.
- **Conversão de Tipos de Dados**: Colunas específicas foram convertidas para o tipo inteiro, facilitando o processamento e a análise das variáveis.
- **Remoção de Características Altamente Correlacionadas**: Características altamente correlacionadas foram identificadas e removidas, mantendo apenas uma das colunas correlacionadas para evitar redundância e colinearidade.
- **Tratamento de Valores Faltantes e Codificação**: Valores faltantes foram tratados com imputação e variáveis categóricas foram codificadas, preparando os dados para a modelagem.
- **Cálculo da Importância das Características**: Um classificador Gradient Boosting foi utilizado para calcular a importância das características, identificando aquelas que mais contribuem para a variável alvo.
- **Seleção de Variáveis Relevantes**: As características mais importantes foram selecionadas com base na importância calculada, focando naquelas com maior impacto na previsão da capacidade de um cliente para reembolsar um empréstimo.

---

# Modelagem

## Modelos Baseline

Modelos baseline foram criados inicialmente sem tratamento especial dos dados:

- Foram desenvolvidos dois modelos baseline, um com AutoML utilizando a biblioteca PyCaret e outro com cinco modelos manuais. Embora tenham apresentado resultados satisfatórios, o modelo inicial não pôde ser colocado em produção devido à presença de variáveis não documentadas no conjunto de dados.

## Regressão Logística

### 1. Pré-processamento dos Dados

- **Imputação de Dados:**
  Utilizou-se o `SimpleImputer` para preencher valores faltantes. Características numéricas foram imputadas com a média e características categóricas com a moda. A imputação foi realizada separadamente para os conjuntos de treino e teste.

- **Codificação de Variáveis Categóricas:**
  Aplicou-se `TargetEncoder` para codificar variáveis categóricas, transformando categorias em valores baseados na média do target, adequando as variáveis para a modelagem.

- **Remoção de Características Altamente Correlacionadas:**
  A matriz de correlação foi calculada e características altamente correlacionadas foram removidas, utilizando um limiar de 0,5 para identificar e eliminar redundâncias.

- **Seleção de Variáveis Relevantes:**
  Um modelo de `XGBoost` foi utilizado para calcular a importância das características e as 20 variáveis mais importantes foram selecionadas para a modelagem.

- **Análise de Variáveis vs Log(Odds):**
  Gráficos de variáveis vs `log(odds)` foram criados para avaliar a relação entre variáveis e a variável alvo. O coeficiente de determinação \( R^2 \) foi calculado para medir o ajuste do modelo linear.

- **Transformações de Variáveis:**
  Transformações potenciais para variáveis categóricas foram avaliadas com base no \( R^2 \) do `log(odds)` das variáveis transformadas. As transformações consideradas foram a transformação logarítmica absoluta e a quadrática.

- **Aplicação de Transformações:**
  As melhores transformações foram aplicadas às variáveis com base nos resultados da análise, substituindo as variáveis originais nas bases de dados.

- **Cálculo do \( R^2 \) Atualizado:**
  O \( R^2 \) foi recalculado para variáveis após as transformações aplicadas, usando a função `calculate_r2_for_logodds`, para identificar quais variáveis ainda não são lineares com o `log(odds)` da variável alvo.

- **Análise de Variáveis Categóricas:**
  Variáveis categóricas com baixa cardinalidade (<= 20) foram identificadas e verificada a ordenação entre categorias. Gráficos foram gerados para visualizar a relação entre volume e taxa de eventos.

- **Categorização com Árvores de Decisão:**
  Uma árvore de decisão foi utilizada para categorizar variáveis numéricas em bins com um número definido de categorias (`n_categories`). Os limites dos bins foram ajustados para evitar sobreposição.

- **Aplicação de Bins:**
  Bins resultantes da árvore de decisão foram aplicados às variáveis numéricas nos conjuntos de treino e teste. Novas variáveis categóricas (`TFT_`) foram criadas para representar essas categorias.

- **One-Hot Encoding:**
  Realizou-se o `one-hot encoding` das variáveis categóricas resultantes da categorização. O `OneHotEncoder` foi utilizado para converter essas variáveis em uma forma binária adequada para modelos de machine learning.

- **Preparação Final dos Dados:**
  Os conjuntos de treino e teste foram preparados com as variáveis transformadas e codificadas, ficando prontos para a modelagem de Regressão Logística.

### 2. Regressão Logística

- **Objetivo:**
  Ajustar um modelo de regressão logística e avaliar a importância das variáveis.

- **Processo:**
  - **Modelo de Regressão:** O modelo de regressão logística foi ajustado utilizando as variáveis selecionadas.
  - **Scorecard:** Foi gerado um scorecard com coeficientes, p-valores e estatísticas de Wald para cada variável.

### 3. Seleção de Variáveis Significativas

- **Objetivo:**
  Filtrar as variáveis significativas para a construção final do modelo.

- **Processo:**
  - **Filtragem por P-Valor:** Variáveis com p-valores menores ou iguais a 0,05 foram selecionadas.
  - **Limitação de Variáveis:** O número de variáveis selecionadas foi limitado para a construção final do modelo.
![Scorecard - Regressão Logistica](Imagens/Scorecard%20-%20Regress%C3%A3o%20Logistica.png)

### 4. Pontuação do Modelo

- **Objetivo:**
  Calcular e adicionar pontuações de probabilidade (scores) para os conjuntos de dados de treino e teste.

- **Processo:**
  - **Cálculo de Pontuações:** A pontuação de probabilidade (`Score_1`) e sua complementar (`Score_0`) foram calculadas usando o modelo ajustado.
  - **Atualização dos Dados:** Essas pontuações foram adicionadas aos conjuntos de dados de treino e teste.

### 5. Avaliação do Modelo

- **Objetivo:**
  Avaliar o desempenho do modelo usando métricas de desempenho e criar uma tabela detalhada.

- **Processo:**
  - **Cálculo de Métricas:** Foram calculados o KS, AUC e Gini para os dados de treino e teste.
  - **Tabela por Decil:** O score foi dividido em 10 faixas (decis) e foi gerada uma tabela com a taxa de eventos e volume por decil.
  - **Visualização:** Um gráfico de barras foi plotado para comparar a taxa de eventos por decil entre os conjuntos de treino e teste.

![Resultados - Regressão Logistica](Imagens/Resultados%20-%20Regress%C3%A3o%20Logistica.png)

## Modelo Desafiante

### 1. Pré-processamento dos Dados

- **Imputação de Dados:**
  Utilizou-se o `SimpleImputer` para preencher valores faltantes. As características numéricas foram imputadas com a mediana, enquanto as características categóricas foram imputadas com a moda. A imputação foi realizada separadamente para os conjuntos de treino e teste.

- **Codificação de Variáveis Categóricas:**
  Aplicou-se `TargetEncoder` para transformar variáveis categóricas em valores baseados na média do target, adequando as variáveis para a modelagem.

- **Escalonamento de Variáveis Numéricas:**
  Utilizou-se `StandardScaler` para normalizar as variáveis numéricas, ajustando a escala das características.

- **Processamento dos Dados:**
  Variáveis categóricas e numéricas foram processadas em pipelines separados e combinadas usando `ColumnTransformer`. Os dados foram transformados para os conjuntos de treino, teste e produção.

### 2. Ajuste e Avaliação do Modelo

- **Treinamento de Modelos:**
  Foram treinados os seguintes modelos: `DecisionTreeClassifier`, `LogisticRegression`, `RandomForestClassifier`, `GradientBoostingClassifier`, `XGBClassifier` e `LGBMClassifier`.

- **Hiperparametrização com Optuna:**
  Utilizou-se o `Optuna` para otimizar os hiperparâmetros do `GradientBoostingClassifier`. Uma função objetivo foi definida para ajustar os parâmetros do modelo, incluindo taxa de aprendizado, número de estimadores, profundidade máxima, entre outros. Foi aplicada uma estratégia de validação cruzada para avaliar o desempenho do modelo.

- **Treinamento do Melhor Modelo:**
  Após a otimização dos hiperparâmetros, o `GradientBoostingClassifier` foi treinado com os melhores parâmetros encontrados.

- **Avaliação do Modelo Ajustado:**
  Foram calculadas as métricas para o modelo ajustado e gerada uma tabela detalhada com taxas de eventos e volumes por decil para os conjuntos de treino e teste. Também foram gerados gráficos para comparar a taxa de eventos por decil entre os conjuntos de treino e teste.
  
![Métricas do Gradient Boosting](Imagens/Métricas%20-%20Gradient%20Boostig.png)

### 3. Resultados

- **Tabela Detalhada:**
  Tabelas detalhadas foram criadas com as métricas de performance, incluindo taxas de eventos e percentuais totais de eventos para diferentes deciles.

- **Visualização dos Resultados:**
  Gráficos de barras foram plotados para comparar a taxa de eventos por decil entre os conjuntos de treino e teste.
![Resultados - Gradient Boosting](Imagens/Resultados%20-%20Gradient%20Boosting.png)

## Resultados

### Ganho Líquido com o Modelo Desafiante

Ao adotarmos o modelo desafiante e realizando um corte de 6,67% do público que se encontra no primeiro decil, teríamos um ganho líquido de até 1,81%.

### Comparativo de Cenários

| Cenário                | Volume Público | Volume Aprovado | Taxa de Aprov. | Volume Maus | Taxa de Maus | Receita                        | Perda                            | Balanço                        | % Ganho |
|------------------------|----------------|-----------------|----------------|-------------|--------------|--------------------------------|----------------------------------|--------------------------------|---------|
| **Cenário Vigente**    | 45.174         | 45.174          | 100,0%         | 3.613       | 8,0%         | P 6.616.042.771,87             | P 2.165.979.048,00              | P 4.450.063.723,87             | 0%      |
| **Regressão Logística**| 45.174         | 42.161          | 93,3%          | 2.818       | 6,7%         | P 6.174.768.214,12             | P 1.689.379.728,00              | P 4.485.388.486,12             | 0,79%   |
| **Gradient Boosting**  | 45.174         | 42.162          | 93,3%          | 2.743       | 6,5%         | P 6.174.914.670,99             | P 1.644.417.528,00              | P 4.530.497.142,99             | 1,81%   |
| **Gradient Boosting x Cenário Vigente** | -              | -3.012          | -6,7%          | -870        | -1,5%        | -P 441.128.100,87             | -P 521.561.520,00              | P 80.433.419,13               | 1,81%   |

- **Volume Público:** Total de clientes analisados.
- **Volume Aprovado:** Total de clientes aprovados para crédito.
- **Taxa de Aprov.:** Percentual de aprovação.
- **Volume Maus:** Total de clientes inadimplentes.
- **Taxa de Maus:** Percentual de inadimplência.
- **Receita:** Receita total gerada com os clientes aprovados.
- **Perda:** Valor total perdido devido a inadimplência.
- **Balanço:** Diferença entre Receita e Perda.
- **% Ganho:** Percentual de ganho líquido em relação ao cenário vigente.



É possível conferir mais detalhadamente os resultados aqui.


## Próximas Etapas

Com base nos resultados obtidos, as próximas etapas são:

1. **Avaliação pelos Stakeholders**: Apresentar os resultados do modelo aos stakeholders para revisão e aprovação. Essa etapa é crucial para garantir o alinhamento e o suporte necessário para a implementação.

2. **Preparação para Deployment**: Se o modelo for aprovado, coordenar com a equipe de TI para planejar e executar o deploy do modelo no ambiente produtivo. Isso envolve avaliar as necessidades de infraestrutura e assegurar a integração com os sistemas existentes.

3. **Monitoramento Contínuo**: Estabelecer um plano de monitoramento para acompanhar o desempenho do modelo e identificar qualquer desvio na sua precisão ao longo do tempo. Isso permitirá ajustes e manutenção proativos para garantir a eficácia contínua.

Essas etapas são fundamentais para garantir que os benefícios do modelo sejam otimizados e que a implementação seja realizada de maneira eficaz e sustentável.

## Pontos de Melhoria

- **Feature Engineering**: O processo de feature engineering pode ser aprimorado com a colaboração de um Engenheiro de Dados. Esse profissional pode ajudar a gerar um maior número de variáveis e, potencialmente, melhorar ainda mais o desempenho do modelo.


