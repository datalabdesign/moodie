<img src="https://github.com/datalabdesign/moodie/blob/main/logo_moodie_all.png" alt="Logo MOODIE ALL" width="600"/>

# MOODIE · Modular Observational & Operational Design Image Explorer  
> **Ferramenta open‑source (beta) para coleta, exploração e análise visual de grandes acervos de imagens — sem precisar programar.**

MOODIE foi criado como um **laboratório pedagógico** que aproxima estudantes e profissionais de Design das metodologias de visão computacional, mineração de dados visuais e mapeamento de tendências.  
O fluxo completo é dividido em **dois notebooks independentes, porém complementares**:

| Sequência | Notebook (Google Colab) | Para que serve? | Abrir agora |
|-----------|-------------------------|-----------------|-------------|
| **1.** | **MOODIE Grabber**<br><sub>Coleta & pré‑processamento</sub> | Faz o _download_ em lote a partir de um CSV de links, renomeia arquivos, remove duplicatas e gera o primeiro dataset estruturado. | [![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/datalabdesign/moodie/blob/main/modulo_grabber/01_MOODIE_GRABBER_v2_BETA.ipynb) |
| **2.** | **MOODIE Image Trends**<br><sub>Análise, cores, recomendações</sub> | Recebe uma **pasta de imagens** (vinda do Grabber ou sua própria), extrai *features*, remove duplicatas, cria mapas de cor, image‑walls e um sistema de recomendação visual Top N. | [![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/datalabdesign/moodie/blob/main/modulo_trend/02_MOODIE_TREND_BETA_V11.ipynb) |

> **Fluxo resumido** • *(a) Colete ⇢ (b) Explore ⇢ (c) Recomende)*  
> 1️⃣ **Grabber** baixa/organiza ► 2️⃣ **Trends** analisa, gera paletas, painéis e recomendações.

---

## ✦ Funcionalidades em Destaque

### ① MOODIE Grabber
- Leitura de CSV com links de imagem (coluna configurável).  
- **Sampleamento** (down‑/up‑/random) antes do download.  
- Nomeação opcional (`original`, `hex`, `pHash`).  
- **Remoção de duplicatas** por pHash ou chaves do CSV.  
- Extração de metadados (EXIF, dimensões, domínio, tamanho).  
- Saídas: pasta `imagens/`, dataset CSV, relatório, estrutura pronta para o módulo Trends.

### ② MOODIE Image Trends
- **Amostragem** extra (caso queira prototipar rápido).  
- **Extração de embeddings** com VGG16 · ResNet50 · MobileNetV2 · InceptionV3 (TensorFlow).  
- **Deduplicação** em três camadas (MD5, pHash, embedding).  
- **Mapas perceptuais** de cor + paletas temáticas & acessíveis (simulação de daltonismo).  
- **Image‑walls** ordenadas por similaridade, cor, ranking ou representatividade.  
- **Sistema de recomendação**:  
  - Métricas Cosseno • Euclidiana • Correlação.  
  - Pesos ajustáveis para embeddings **e** colunas extras (média, mediana, Jaccard).  
  - Usa imagem interna ou **upload externo** como referência.  
- Dashboards automáticos (referência + wall + paletas) prontos para apresentação.  
- Exportação **ZIP** com CSVs, painéis e imagens geradas (um clique).

---

## ✦ Requisitos Rápidos

Ambos os notebooks rodam **100 % no Google Colab**.  
Dependências (`tensorflow`, `scikit‑learn`, `distinctipy`, `umap-learn`, etc.) são instaladas automaticamente na primeira execução.

- Python ≥ 3.9 (versão do Colab).  
- GPU opcional em Colab para acelerar embeddings.

---

## ✦ Workflow Passo‑a‑Passo

1. **Prepare seu CSV** (links) **ou** uma pasta/ZIP de imagens.  
2. **Abra o notebook Grabber** → configure parâmetros → execute as células em ordem.  
   - Resultado: pasta `imagens/` + `datasets/` + `reports/`.  
3. **Monte (ou revise) a pasta de imagens** que será analisada.  
4. **Abra o notebook Trends** → carregue a pasta `imagens/` (e o CSV, se houver).  
5. Siga as seções do Trends:  
   1. _Import → Features → Duplicatas → Cores → Imagewalls → Recomendações_.  
6. Baixe o ZIP final com dashboards, paletas e CSVs para uso em relatórios, apresentações ou moodboards.

*(Você pode pular o Grabber se já tiver as imagens localmente.)*

---


## Sobre o Autor

[Elias Bitencourt](https://eliasbitencourt.com) é Professor Adjunto no Curso de Design da Universidade do Estado da Bahia (UNEB), com Doutorado em Comunicação pela FACOM/UFBA e Mestrado em Cultura e Sociedade pelo IHAC/UFBA. Foi pesquisador visitante no Centro Milieux (Concordia University, Canadá) em 2019. Atualmente, coordena o **Datalab/Design (CNPq)** na UNEB, um centro de pesquisa em visualização de dados e metodologias digitais. Sua pesquisa investiga visualização de dados, estudos de plataformas, imaginários digitais e mediação algorítmica nas relações sociais. É pesquisador colaborador no **Inova Media Lab** (Universidade Nova de Lisboa) e na rede internacional **Public Data Lab**.

---

## Status

O projeto encontra-se em **fase beta** e está em desenvolvimento contínuo. Contribuições, sugestões e colaborações são bem-vindas.

---

## Licença

Este repositório está sob uma **Licença de Uso Restrito com Atribuição** (acadêmico e não‑comercial).    
O conteúdo pode ser utilizado para fins acadêmicos e não-comerciais com devida atribuição ao autor.  
Modificações ou redistribuição exigem permissão. Para mais detalhes, consulte o arquivo [`LICENSE.txt`](./LICENSE.txt) para mais detalhes.

## Como citar moodie (formato APA):

Bitencourt, E. (2025). *MOODIE Image Trends: Modular Observational & Operational Design Image Explorer* (Versão beta) [Repositório GitHub]. Datalab/Design – Universidade do Estado da Bahia. https://github.com/datalabdesign/moodie

<p align="left">
  <sub>Site: <a href="https://datalab.org">datalab.org</a> &nbsp;·&nbsp; Instagram: <a href="https://www.instagram.com/datalabdesign">@datalabdesign</a></sub>
</p>

