## Detecção de Gênero em Radiografias de Tórax com Autoencoders
Este repositório reúne notebooks e código utilizados para investigar se representações latentes aprendidas por autoencoders podem reduzir a necessidade de dados anotados na classificação de gênero em radiografias de tórax.
O trabalho integra aprendizado não supervisionado (autoencoders) e aprendizado supervisionado (SVM), avaliando a eficácia do modelo em três bases públicas de imagens médicas.

## Sobre o Projeto
O objetivo do estudo é analisar se autoencoders treinados sem rótulos conseguem extrair vetores latentes suficientemente informativos para permitir que um classificador simples (SVM) diferencie radiografias de pacientes do sexo masculino e feminino.

A metodologia foi avaliada em dois cenários:
* Intra-dataset: treino e teste no mesmo conjunto
* Cross-dataset: treino em uma base e teste em outra, medindo robustez ao domain shift

## Bases de Dados Utilizadas
Três datasets públicos foram selecionados:
1. D1 – SPR X-Ray Gender Challenge
Focado exatamente na tarefa de gênero.
Melhor desempenho intra-dataset.
2. D2 – Montgomery + Shenzhen (Lung Segmentation)
Mistura de bases de duas instituições.
Maior variabilidade e mais ruído.
3. D3 – NIH ChestX-ray14 (224×224)
Base massiva e heterogênea.
Maior diversidade anatômica e demográfica.

Todos os datasets foram padronizados para grayscale, 224×224 e normalização [0,1].

## Arquitetura dos Modelos
Foram implementados três autoencoders, um para cada dataset:

* AE–D1: encoder mais simples, vetor latente de 128 dimensões
* AE–D2: arquitetura mais profunda, vetor latente de 160 dimensões
* AE–D3: arquitetura intermediária, vetor latente de 128 dimensões

Configurações comuns:
* MSE como função de perda
* Adam (1e-3)
* Batch size 32
* 30 épocas

O encoder é usado como extrator de características; o decoder é descartado após o treino.

## Resultados (Acurácia e F1)
Resumo dos resultados reportados no TCC nas avaliações de teste:

Desempenho Intra-Dataset
* Acurácia / F1
* AE–D1 → D1: 0.783 / 0.781
* AE–D2 → D2: 0.505 / 0.339
* AE–D3 → D3: 0.631 / 0.631

Desempenho Cross-Dataset
Acurácia / F1
* AE–D1 → D2: 0.710 / 0.667
* AE–D1 → D3: 0.586 / 0.586
* AE–D2 → D1: 0.784 / 0.783
* AE–D2 → D3: 0.599 / 0.599
* AE–D3 → D1: 0.772 / 0.770
* AE–D3 → D2: 0.458 / 0.352

Interpretação dos Números
* Melhor desempenho geral: AE–D1 no dataset D1
* Maior generalização: AE–D2 e AE–D3 quando aplicados em D1
* Maior sensibilidade ao domain shift: AE–D1 ao ser aplicado em D3
* Dataset mais difícil: D2, consistente com sua heterogeneidade
Esses resultados indicam que os embeddings aprendidos são úteis, mas sofrem com diferenças entre bases (protocolos, equipamentos, distribuição de classes).

## Conclusões Gerais

* Autoencoders são capazes de aprender representações úteis sem depender de rótulos.
* A combinação AE + SVM alcança desempenho competitivo com poucos dados anotados.
* O desempenho cai entre domínios diferentes, reforçando a importância do alinhamento entre dataset de treino e aplicação real.
* O método reduz significativamente a necessidade de rotulagem manual, um ponto crítico em imagens médicas.
