# Deep Learning Course

Este repositório contém os notebook's apresentados em sala pelo professor Luan Porfirio e Silva, no curso de pós-graduação de Data Science da FURB. O projeto do professor, pode ser acessado por meio deste [link][github-deeplearning-furb].

Além disto, o arquivo `Trabalho.ipynb` refere ao trabalho final da disciplina, detalhado abaixo.

## Trabalho Final - Classificação de textos para análise de sentimentos

Base de dados 

Instruções:
- O objetivo deste trabalho é criar um modelo binário de aprendizado de máquina para classificação de textos. 
Para isso, será utilizado a base de dados [IMDb](http://ai.stanford.edu/~amaas/data/sentiment/), que consiste de dados textuais de críticas positivas e negativas de filmes
- Uma vez treinado, o modelo deve ter uma função `predict` que recebe uma string como parâmetro e retorna o valor 1 ou 0, aonde 1 significa uma crítica positiva e 0 uma crítica negativa
- O pré-processamento pode ser desenvolvidado conforme desejar (ex.: remoção de stopwords, word embedding, one-hot encoding, char encoding)
- É preferível que seja empregado um modelo de recorrência (ex.: rnn, lstm, gru) para a etapa de classificação
- Documente o código (explique sucintamente o que cada função faz, insira comentários em trechos de código relevantes)
- **Atenção**: Uma vez treinado o modelo final, salve-o no diretório do seu projeto e crie uma célula ao final do notebook contendo uma função de leitura deste arquivo, juntamente com a execução da função `predict`

Sugestões:
- Explorar a base de dados nas células iniciais do notebook para ter um melhor entendimento do problema, distribuição dos dados, etc
- Após desenvolver a estrutura de classificação, é indicado fazer uma busca de hiperparâmetros e comparar os resultados obtidos em diferentes situações


## Setup do Projeto

### Criação de venv e instalação de dependências

```
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### Download do modelo com melhores resultados

É possível fazer download do modelo com melhores parâmetros a partir deste [link][link-download-modelo-predict].

Após o download, crie uma pasta no diretório raíz do projeto com o seguinte nome: `models` e cole o arquivo baixado para esta pasta.

## Experimentos Realizados

Para resolver este problema, foi segundo [este tutorial][tutorial-base-medium] como base.

Foi utilizada uma rede LSTM, cujo fluxo base pode ser descrito como:

1. Carregamento dos dataset's de treino e teste
2. União dos dataset's
3. Avaliação dos dados
4. Pré-processamentos
    
    4.1. Remoção de pontuações e caracteres especiais
    
    4.2. Substituição de todos os caracteres para minúsculo

5. Tokenização de palavras
6. Encoding de label's para números inteiros
7. Remoção de outliers
8. Padding/Truncate dos dados

    8.1. Aqui foi adotado a estratégia de limitar a 280 caracteres, que corresponde ao valor do 3º quartil, o que significa que 75% dos dados estão abaixo disto

    8.2. Ao final, todos os reviews ficaram com apenas 280 palavras

    8.3. Os reviews menores que 280 palavras, foram completados com o caractere 0 - que representa uma palavra inválida/inexistente

9. Realizado o split do dataset

    9.1. Foi separado em: 80/10/10, sendo 80% dedicados a treinamento, 10% para validação e 10% para teste

10. Elaboração do modelo

11. Treinamento e validação do modelo

12. Teste do modelo

13. Salvamento do modelo na memória

14. Carregamento do modelo e predição

    14.1. Neste passo, a entrada (texto) do usuário, é passada pelos mesmos processos descritos nas etapas 4, 5 e 8

    14.2. Caso uma palavra digitada pelo usuário não seja reconhecida pelo array de tokens de palavras, é considerado o valor 0, que considera uma palavra inexistente/inválida


### Configurações experimentadas

Para os testes, por conta do tempo demandando pelo algoritmo, foram realizados 2 tipos de teste:

1. Todo o dataset
2. Parte do dataset

A ideia de realizar o teste parcial é apenas ter uma noção de desempenho e mover os hiperparâmetros, enquanto o teste com todo o dataset busca melhores resultados

#### Testando com todo o dataset

| Número do teste | Batch Size | Quantidade de layers | Tamanho de entrada das camadas escondidas | Dropout | Test Loss | Test Accuracy | Quantidade de épocas de treinamento  |
|-----------------|------------|----------------------|-------------------------------------------|---------|-----------|---------------|--------------------------------------|
| 1               | 50         | 2                    | 256                                       | 0.3     | 0.607     | 0.737         | 2                                    |
| 2               | 64         | 4                    | 128                                       | 0.3     | 1.062     | 0.783         | 10                                   |

#### Testando com parte do dataset

| Número do teste | Batch Size | Quantidade de layers | Tamanho de entrada das camadas escondidas | Dropout | Test Loss | Test Accuracy | Quantidade de épocas de treinamento  | Quantidade de dados por classe |
|-----------------|------------|----------------------|-------------------------------------------|---------|-----------|---------------|--------------------------------------|--------------------------------|
| 1               | 50         | 2                    | 256                                       | 0.3     | 3.913     | 0.410         | 10                                   | 1000                           |
| 2               | 64         | 4                    | 128                                       | 0.3     | 2.539     | 0.510         | 10                                   | 1000                           |
| 3               | 64         | 4                    | 128                                       | 0.3     | 1.560     | 0.675         | 10                                   | 10000                          |


### Conclusão

Avaliando todos os resultados, é possível observar que, testando com todo o dataset, foi possível obter melhores métricas, apesar da quantidade limitade de épocas de treinamento.

O melhor modelo de todos é o número 2, o qual ficou com acurácia de `78,3%`. 

O resultado, ainda poderia ser mais otimizado, adicionando mais épocas e efetuando mais ajustes de hiperparâmetros.

[github-deeplearning-furb]: https://github.com/luanps/furb_deeplearning
[link-download-modelo-predict]: https://drive.google.com/file/d/1dxRgR1EJYKPusJj-86NB40k9LenbXcEI/view?usp=sharing
[tutorial-base-medium]: https://towardsdatascience.com/sentiment-analysis-using-lstm-step-by-step-50d074f09948