<img src="https://github.com/datalabdesign/moodie/blob/main/logo_moodie_all.png" alt="Logo MOODIE ALL" width="600"/>

# MOODIE Web Companion

**MOODIE Web Companion** é um notebook em Google Colab criado para preparar, anotar e exportar corpora de imagens para o **MOODIE Web**. Ele funciona como a etapa Python da pipeline: organiza projetos, descompacta imagens, associa metadados, permite amostragem, extrai *features* visuais com modelos de visão computacional e gera um pacote `.moodie` otimizado para análise no navegador.

**MOODIE Web** (*Modular Observational & Operational Design Image Explorer*) é uma aplicação web experimental do Datalab Design para exploração visual de corpora de imagens. A interface permite carregar imagens, metadados e projetos `.moodie`, inspecionar a estrutura do corpus, detectar ausências e duplicatas, construir *imagewalls*, comparar regimes de visualidade, projetar imagens em mapas 2D, aplicar filtros e overlays categóricos, explorar relações de semelhança no Pixel Eye e organizar curadorias analíticas no MoodieBoard.

O Companion existe porque a extração de embeddings com modelos de visão computacional ainda é pesada demais para ser executada integralmente no navegador. O MOODIE Web consegue operar com descritores superficiais extraídos do lado do cliente, como cor, brilho, contraste e bordas, mas análises mais densas exigem vetores pré-computados. O Companion produz esses vetores, compacta as matrizes quando necessário e exporta tudo em um formato que o MOODIE Web consegue ler diretamente.

O Companion não substitui o web app. Ele prepara o corpus para que a análise possa ser feita na interface web, com menor dependência de programação durante a etapa exploratória.

| Componente | Função | Ambiente |
|---|---|---|
| MOODIE Web | Exploração visual, comparação de regimes, imagewalls, Pixel Eye, MoodieBoard, filtros, overlays e leitura de projetos `.moodie` | Navegador |
| MOODIE Web Companion | Organização do projeto, importação de imagens/metadados, amostragem, extração de features, labels/captions e exportação `.moodie` | Google Colab / Python<br>[![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/datalabdesign/moodie/blob/main/MOODIE_WEB_COMPANION_V2.ipynb) |
| Arquivo `.moodie` | Pacote autocontido com thumbnails, metadata, features, projeções e manifesto do projeto | Intercâmbio entre Companion e Web |

## Para que serve

O Companion foi desenvolvido para pesquisas, disciplinas e projetos que trabalham com acervos de imagens digitais, especialmente em design, comunicação, humanidades digitais, métodos digitais, estudos de plataformas, visualização de dados e análise de visualidades algorítmicas.

Ele permite transformar uma coleção de imagens em uma base navegável pelo MOODIE Web. O resultado não é apenas um dataset com colunas adicionais, mas um pacote analítico que preserva imagens, metadados, embeddings, rótulos, captions, projeções 2D e informações de compatibilidade para a interface web.

## Fluxo geral

| Etapa | Módulo do Companion | O que faz | Saída principal |
|---|---|---|---|
| 1 | Instalação de dependências | Instala e carrega bibliotecas necessárias ao Colab | Ambiente Python preparado |
| 2 | Google Drive | Conecta o notebook ao Drive, quando necessário | Acesso aos arquivos do usuário |
| 3 | Diretórios de trabalho | Cria ou recupera a estrutura do projeto | `project_dir`, `imagens/`, `datasets/`, cache do projeto |
| 4 | Descompactação de imagens | Extrai imagens de um `.zip` ou usa imagens já existentes | Pasta `imagens/` organizada |
| 5 | Upload e análise da base | Carrega CSV, JSON ou PKL e associa metadados às imagens | `global_df` consolidado |
| 6 | Amostragem | Gera subcorpus por downsample, upsample ou amostra aleatória | Dataset amostral ativo |
| 7 | Extração de features e rótulos | Processa imagens com modelos CNN, ViT, captioning e faces | Colunas `*_features`, `*_labels`, `*_scores` |
| 8 | Exportação para MOODIE Web | Cria thumbnails, compacta features, calcula hashes, gera projeções e empacota | Arquivo `.moodie` |

## Requisitos

O Companion foi pensado para rodar em Google Colab. Não é necessário instalar Python localmente.

| Item | Recomendação |
|---|---|
| Ambiente | Google Colab |
| Python | Versão padrão do Colab |
| GPU | Opcional, mas recomendada para ViT, BLIP, ViT-GPT2 e InsightFace |
| Entrada mínima | Um arquivo `.zip` com imagens ou uma pasta `imagens/` |
| Metadados | CSV, JSON ou PKL opcional |
| Navegador | Necessário para uso posterior do MOODIE Web |

Bibliotecas usadas pelo notebook incluem `pandas`, `numpy`, `Pillow`, `ipywidgets`, `scikit-learn`, `tensorflow`, `torch`, `timm`, `transformers`, `umap-learn`, `networkx`, `python-louvain`, `insightface` e dependências auxiliares instaladas automaticamente pelo próprio notebook.

## Estrutura de projeto

O Companion organiza o trabalho em uma pasta de projeto. A estrutura pode variar conforme as opções usadas, mas segue esta lógica geral:

```text
projeto_moodie/
├── imagens/
│   ├── imagem_001.jpg
│   ├── imagem_002.jpg
│   └── ...
├── datasets/
│   ├── dataset_ativo.pkl
│   ├── dataset_ativo.csv
│   └── backups/
├── reports/
├── imagenet_labels_ref/
├── vision_network_labels/
└── Moodie_web/
    └── projeto_data_hora.moodie
```

O arquivo `.moodie` é um pacote `.zip` com extensão própria. Ele pode incluir:

| Arquivo/Pasta | Conteúdo |
|---|---|
| `manifest.json` | Informações gerais do projeto e da exportação |
| `metadata.csv` | Metadados do corpus sem as colunas pesadas de features |
| `thumbs/` | Thumbnails JPEG otimizados para uso no navegador |
| `features/index.json` | Índice das matrizes vetoriais e projeções |
| `features/*.f32.bin` | Matrizes `float32` em formato binário lido pelo MOODIE Web |
| `originals/` | Imagens originais, apenas se essa opção for marcada |

## Como usar

### 1. Abrir o notebook

Abra o notebook `MOODIE_WEB_COMPANION_V1.ipynb` no Google Colab e execute as células em sequência. O Companion foi estruturado como uma interface de widgets; a maior parte das decisões é feita por botões, campos e seletores.

### 2. Instalar dependências

Execute o módulo de instalação. Ele prepara as bibliotecas necessárias e reinicia componentes quando o Colab exigir. Essa etapa deve ser executada antes de qualquer processamento.

### 3. Conectar ao Google Drive

A conexão com o Drive é opcional. Use-a quando as imagens, datasets ou projetos estiverem armazenados no Google Drive ou quando você quiser manter os resultados persistidos fora do ambiente temporário do Colab.

### 4. Criar ou recuperar diretórios de trabalho

Informe o nome do projeto e o diretório base. O Companion cria a estrutura de pastas e tenta recuperar caches ou datasets já existentes quando o projeto já foi iniciado anteriormente.

### 5. Adicionar imagens

O módulo de imagens aceita dois fluxos principais:

| Modo | Quando usar |
|---|---|
| Usar imagens existentes | Quando a pasta `imagens/` já contém os arquivos do projeto |
| Descompactar ZIP | Quando você tem um `.zip` com imagens e subpastas |

Se não houver dataset carregado, o Companion pode construir automaticamente um dataset mínimo a partir da pasta de imagens.

### 6. Carregar metadados

O módulo de upload e análise da base aceita CSV, JSON e PKL. Ele identifica possíveis colunas de imagem, permite selecionar quais colunas manter e cria um preview aleatório para conferência antes de salvar o dataset ativo.

Use PKL quando o dataset já contém features, embeddings ou estruturas internas que não devem ser convertidas para string. Use CSV ou JSON para metadados tabulares simples.

### 7. Fazer amostragem, se necessário

A amostragem é opcional. Ela serve para criar subcorpora de teste antes de processar todo o acervo.

| Método | Uso |
|---|---|
| Random Sample | Sorteia `N` linhas ou imagens sem estratificação |
| Downsample | Reduz o dataset mantendo distribuição por categoria, quando uma coluna categórica é informada |
| Upsample | Replica itens de categorias minoritárias para balancear grupos |

O módulo também calcula indicadores simples de representatividade da amostra em relação ao corpus original, incluindo distribuição por categoria e intervalo de confiança aproximado.

### 8. Extrair features, labels e captions

Escolha a coluna de imagem e selecione os modelos. O Companion processa as imagens, salva embeddings em colunas `*_features` e, quando solicitado, também salva rótulos, scores ou captions em colunas complementares.

As colunas seguem este padrão:

| Tipo | Exemplo | Descrição |
|---|---|---|
| Features | `ResNet50_features` | Vetor numérico usado para similaridade, projeção e exportação |
| Labels | `ResNet50_labels` | Top-3 rótulos ImageNet, quando disponíveis |
| Scores | `ResNet50_scores` | Probabilidades associadas aos rótulos ImageNet |
| Captions | `BLIP_labels` | Descrição textual em inglês gerada por modelo captioning |
| Faces | `gender_label`, `age_label`, `age_class` | Estimativas derivadas do módulo InsightFace |

### 9. Exportar para `.moodie`

Depois da extração, use o módulo **MOODIE Web Exporter**. Ele permite selecionar as features a exportar, definir tamanho e qualidade dos thumbnails, normalizar vetores, calcular hashes, gerar projeções 2D e empacotar o projeto.

Antes de gerar o arquivo, clique em **Estimar tamanho**. A estimativa informa número de linhas, imagens únicas, peso aproximado de thumbnails, metadata, features e tamanho total. Quando a redução PCA está ativa, a estimativa também informa a dimensão final e a variância explicada por modelo.

## Modelos disponíveis

O Companion organiza os modelos por arquitetura. A escolha do modelo importa porque cada arquitetura descreve a imagem segundo uma lógica distinta. O objetivo não é tratar o embedding como uma verdade visual, mas como uma inscrição vetorial situada: um modo específico de tornar imagens comparáveis.

### CNN + ImageNet-1k

Modelos CNN operam por convoluções, detectando padrões locais como bordas, texturas, formas simples e composições progressivamente mais complexas. As versões usadas no Companion são pré-treinadas em ImageNet-1k. Quando a opção de rótulos é marcada, o notebook salva os Top-3 rótulos do vocabulário ImageNet e suas probabilidades.

| Modelo | Biblioteca | Saída | Indicação de uso | Referência |
|---|---|---|---|---|
| MobileNetV2 | TensorFlow/Keras | Features + Top-3 ImageNet labels/scores | Processamento rápido, grandes coleções, testes iniciais, padrões de cor e textura em baixa ou média resolução | [Sandler et al., 2018](https://arxiv.org/abs/1801.04381) |
| VGG16 | TensorFlow/Keras | Features + Top-3 ImageNet labels/scores | Leitura mais sensível a textura e borda; útil para agrupamentos por aparência local | [Simonyan & Zisserman, 2014](https://arxiv.org/abs/1409.1556) |
| ResNet50 | TensorFlow/Keras | Features + Top-3 ImageNet labels/scores | Equilíbrio entre forma, textura e categoria; bom ponto de partida para corpus heterogêneo | [He et al., 2015](https://arxiv.org/abs/1512.03385) |
| InceptionV3 | TensorFlow/Keras | Features + Top-3 ImageNet labels/scores | Sensível a variações de escala; útil quando objetos aparecem em tamanhos e contextos distintos | [Szegedy et al., 2015](https://arxiv.org/abs/1512.00567) |
| EfficientNet-B0 | timm/PyTorch | Features + Top-3 ImageNet labels/scores | Boa relação entre custo e desempenho; útil para detalhes finos, cenas naturais e contexto visual | [Tan & Le, 2019](https://arxiv.org/abs/1905.11946) |

### Vision Transformer + ImageNet-1k

Modelos ViT dividem a imagem em *patches*, transformam cada patch em um token e usam autoatenção para relacionar regiões distantes da imagem. Isso favorece leituras de composição, co-ocorrência e relação entre partes da cena.

| Modelo | Biblioteca | Saída | Indicação de uso | Referência |
|---|---|---|---|---|
| ViT16_1k | timm/PyTorch | Features + Top-3 ImageNet labels/scores | Composição visual, layouts complexos, imagens gráficas, relações espaciais entre partes | [Dosovitskiy et al., 2020](https://arxiv.org/abs/2010.11929) |

### ViT + Captioning

Modelos de captioning combinam um codificador visual e um decodificador de linguagem. Em vez de escolher uma classe fixa do ImageNet, eles geram descrições livres em inglês. O Companion também armazena features visuais desses modelos, permitindo comparar uma representação visual orientada por pré-treino multimodal.

| Modelo | Biblioteca | Saída | Indicação de uso | Referência |
|---|---|---|---|---|
| ViT_GPT2 | Transformers | Features + caption em inglês | Descrições curtas, triagem semântica, cenas com ação ou contexto narrativo | [ViT](https://arxiv.org/abs/2010.11929), [GPT-2](https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf) |
| BLIP | Transformers | Features + caption em inglês | Descrições mais semânticas, exploração temática, anotação automática de corpus | [Li et al., 2022](https://arxiv.org/abs/2201.12086) |

### Faces: InsightFace Buffalo-L

O módulo de faces é experimental. Ele usa a biblioteca InsightFace com o modelo `buffalo_l` para detectar rostos e estimar idade e gênero quando há uma face detectável na imagem. O Companion grava `gender_label`, `age_label` e `age_class`. Quando não detecta rosto, registra `no_face`.

| Módulo | Saída | Uso possível | Observação |
|---|---|---|---|
| Faces / InsightFace Buffalo-L | `gender_label`, `age_label`, `age_class` | Triagem, filtros exploratórios, análise crítica de vieses demográficos em modelos de visão | As estimativas não devem ser tratadas como identificação real de pessoas, idade ou gênero. São saídas probabilísticas de um modelo sujeito a vieses e erros. |

## Sobre ImageNet labels e captions

As labels ImageNet e as captions cumprem papéis diferentes.

| Saída | Modelos | O que representa | Limitação principal |
|---|---|---|---|
| Top-3 ImageNet labels | MobileNetV2, VGG16, ResNet50, InceptionV3, EfficientNet-B0, ViT16_1k | Classes mais prováveis dentro do vocabulário ImageNet-1k | Vocabulário fechado, centrado em objetos e categorias do dataset de treino |
| Scores | Modelos ImageNet | Probabilidades associadas às labels | Não medem relevância cultural, social ou interpretativa da imagem |
| Captions | ViT_GPT2, BLIP | Frases livres em inglês sobre a imagem | Podem alucinar detalhes, omitir elementos e reproduzir vieses do pré-treino multimodal |
| Features | Todos os modelos visuais | Vetores usados para comparação e projeção | Dependem da arquitetura, do pré-treino e do modo como a imagem é pré-processada |

O Companion permite salvar labels e captions porque elas ajudam na inspeção, na documentação e em análises complementares. Para o MOODIE Web, no entanto, a base principal das comparações vetoriais são as colunas `*_features` exportadas para o pacote `.moodie`.

## Redes imagem-rótulo e rótulo-rótulo

Quando a opção **Salvar rótulos dos modelos** está ativa, o Companion pode gerar redes para os modelos com labels ImageNet. Essas redes são exportadas em tabelas de nós e arestas, além de arquivos compatíveis com Gephi.

| Rede | Descrição | Uso |
|---|---|---|
| Imagem-rótulo | Conecta cada imagem aos rótulos ImageNet atribuídos pelo modelo | Inspecionar quais imagens compartilham categorias previstas |
| Rótulo-rótulo | Conecta rótulos que aparecem associados às mesmas imagens | Mapear coocorrências de categorias previstas |

As redes são geradas para `MobileNetV2`, `VGG16`, `ResNet50`, `InceptionV3`, `EffNetB0` e `ViT16_1k`. Modelos de captioning não geram essas redes porque produzem frases livres, não labels discretas do ImageNet.

## Exportação para MOODIE Web

O módulo **MOODIE Web Exporter** transforma o dataset ativo em um arquivo `.moodie`. Ele executa cinco operações principais.

| Operação | O que faz | Impacto |
|---|---|---|
| Deduplicação de features | Salva um vetor por imagem física única, não por linha repetida | Reduz o peso do pacote e evita redundância vetorial |
| Thumbnails | Converte imagens para JPEG com tamanho e qualidade definidos | Torna a navegação no navegador mais leve |
| Metadata | Remove colunas pesadas de features do CSV e preserva metadados relevantes | Mantém o corpus legível e filtrável no MOODIE Web |
| Features binárias | Salva matrizes `float32` em `.f32.bin` | Permite leitura eficiente pelo JavaScript |
| Projeções 2D | Gera PCA 2D, t-SNE 2D e UMAP 2D, quando selecionados | Permite usar mapas pré-computados no MOODIE Web |

### Deduplicação por imagem única

Se a mesma imagem aparece em várias linhas do dataset, o exportador salva apenas um vetor para aquela imagem e registra o mapeamento por `image_name`. Isso reduz o tamanho do pacote sem eliminar linhas do metadata. No MOODIE Web, diferentes linhas podem continuar apontando para a mesma imagem.

### Normalização L2

A opção **Normalizar vetores (L2)** ajusta cada vetor para ter comprimento igual a 1. Isso faz com que as comparações fiquem mais baseadas na direção do vetor do que na magnitude absoluta dos valores.

| Com normalização L2 | Sem normalização L2 |
|---|---|
| Comparações por cosseno tendem a ficar mais estáveis | Magnitude bruta dos vetores permanece preservada |
| Reduz o risco de vetores com valores maiores dominarem a distância | Pode ser útil quando a magnitude tem significado analítico |
| Recomendado como padrão para embeddings de similaridade | Usar apenas quando houver motivo para preservar escala absoluta |

Para o uso comum no MOODIE Web, recomenda-se manter a normalização L2 ativa.

### MD5 e aHash

O exportador pode calcular dois tipos de hash.

| Hash | O que mede | Uso no MOODIE Web |
|---|---|---|
| MD5 | Identidade exata do arquivo em nível binário | Detectar duplicatas exatas |
| aHash | Assinatura perceptual simples baseada em luminosidade | Detectar imagens visualmente parecidas de forma aproximada |

O metadata exportado inclui `__md5` e `__ahash`. Para compatibilidade com versões anteriores do MOODIE Web, o exportador também pode manter `__phash` como alias temporário do aHash. Tecnicamente, esse campo não deve ser interpretado como pHash real se a função usada foi aHash.

## Redução PCA da matriz principal

Alguns modelos podem gerar vetores muito grandes, especialmente quando a saída salva corresponde a uma sequência completa do modelo em vez de um vetor agregado. Um exemplo comum é uma saída ViT achatada:

```text
197 tokens × 768 dimensões = 151.296 dimensões
```

Exportar matrizes desse tamanho para uso em navegador pode tornar o `.moodie` pesado demais e prejudicar upload, leitura, memória e interação. Para evitar isso, o exportador inclui redução PCA da matriz principal.

Essa redução é diferente das projeções 2D. A redução PCA da matriz principal cria uma versão compacta do embedding ainda multidimensional, usada pelo MOODIE Web para similaridade, recomendação e representatividade. Já PCA 2D, t-SNE 2D e UMAP 2D criam coordenadas bidimensionais para visualização espacial.

| Processo | Entrada | Saída | Uso |
|---|---|---|---|
| Redução PCA da matriz principal | Vetor original, por exemplo 151.296D | Vetor compacto, por exemplo 256D ou 512D | Similaridade, medóide, centróide, recomendação, comparação vetorial |
| PCA 2D | Matriz principal | Coordenadas 2D | Mapa visual |
| t-SNE 2D | Matriz principal ou pré-redução | Coordenadas 2D | Mapa de vizinhança local |
| UMAP 2D | Matriz principal ou pré-redução | Coordenadas 2D | Mapa não linear importável pelo Web |

### Modos de redução

| Modo | Funcionamento | Quando usar |
|---|---|---|
| PCA com dimensão fixa | Reduz para `auto_max_dim` sempre que a dimensão original excede o limite | Quando o objetivo principal é controlar o tamanho do pacote |
| PCA adaptativo por variância explicada | Escolhe a menor dimensão necessária para atingir a variância mínima desejada, sem ultrapassar `auto_max_dim` | Quando é importante equilibrar preservação estatística e viabilidade no navegador |
| Sem limite (`auto_max_dim = 0`) | Não reduz a matriz principal | Apenas para features pequenas ou testes locais controlados |

### Variância explicada

A variância explicada indica quanto da estrutura de variação dos vetores originais foi preservada pela versão reduzida. Uma variância explicada de 90% significa que a matriz compactada mantém 90% da variação estatística capturada pelo PCA e descarta 10%.

Isso não quer dizer que 90% do “significado” foi preservado, nem que 90% das imagens foram mantidas. Significa que, segundo o PCA, os principais eixos de variação do espaço vetorial continuam presentes na representação reduzida.

| Variância explicada | Interpretação prática |
|---|---|
| 85% | Compressão mais agressiva; útil para exploração rápida e pacotes leves |
| 90% | Bom padrão para uso web e comparação geral entre imagens |
| 95% | Melhor preservação para análises mais finas, com aumento de tamanho |
| 99% | Alta preservação, frequentemente pesada para navegação web |

O botão **Estimar tamanho** deve ser usado antes da exportação. Ele informa a dimensão final e a variância explicada por modelo. Se a meta não for atingida dentro de `auto_max_dim`, aumente o limite ou reduza a variância mínima.

## Projeções 2D

O exportador pode salvar projeções 2D pré-computadas. Elas são úteis porque algumas projeções são custosas para o navegador.

| Projeção | Característica | Uso |
|---|---|---|
| PCA 2D | Linear, preserva grandes eixos de variação | Visão geral rápida da estrutura global |
| t-SNE 2D | Não linear, favorece vizinhanças locais | Explorar ilhas de proximidade visual |
| UMAP 2D | Não linear, equilibra estrutura local e global | Exploração de agrupamentos e continuidade entre regiões |

Quando a matriz possui mais de 50 dimensões, o Companion faz uma pré-redução automática para 50D antes de t-SNE/UMAP. Essa operação é técnica e visa reduzir custo computacional.

## Como o MOODIE Web usa o `.moodie`

Depois de exportar o arquivo, abra o MOODIE Web e carregue o `.moodie` na área de upload. A interface lê o manifesto, os metadados, os thumbnails, as matrizes vetoriais e as projeções disponíveis.

No MOODIE Web, as features exportadas aparecem como opções de **Fonte vetorial**. A partir delas, você pode construir regimes de visualidade combinando inscrição vetorial, métrica de semelhança e projeção de espaço.

| Elemento do MOODIE Web | Relação com o Companion |
|---|---|
| Fonte vetorial | Usa colunas `*_features` exportadas no `.moodie` |
| Métrica de semelhança | Calcula proximidade entre vetores, por exemplo cosseno ou euclidiana |
| Projeção de espaço | Pode usar projeções pré-computadas ou projeções calculadas no navegador |
| Representatividade | Usa medóide ou centróide sobre a matriz vetorial ativa |
| Ranking | Usa colunas numéricas do metadata |
| Color by | Usa colunas categóricas ou multivalor do metadata |
| Duplicatas | Usa nome de arquivo, MD5 e aHash quando disponíveis |
| Pixel Eye | Sincroniza projeção 2D, imagewall e seleção de vizinhos |
| MoodieBoard | Usa o estado analítico do corpus para organizar curadorias e comparações |

## Recomendações de uso

| Situação | Configuração sugerida |
|---|---|
| Primeiro teste com corpus grande | Amostra aleatória + MobileNetV2 ou EfficientNet-B0 + `auto_max_dim=256` ou `512` |
| Corpus gráfico, layouts ou peças visuais complexas | ViT16_1k + PCA adaptativo por variância explicada |
| Exploração temática por descrição | BLIP ou ViT_GPT2 com captions salvas |
| Comparação metodológica entre arquiteturas | Exportar ao menos um modelo CNN, um ViT e um captioning |
| Navegação web mais leve | Thumbnails entre 160 e 240 px, JPEG 70–80, PCA adaptativo com 90% |
| Análise mais fina | Aumentar `auto_max_dim` para 768 ou 1024 e mirar 95% de variância, se o pacote continuar viável |
| Diagnóstico de duplicatas | Manter MD5 e aHash ativos |

## Limitações

O Companion não deve ser entendido como um classificador universal de imagens. Os modelos carregam pressupostos arquiteturais, dados de treino, vocabulários e vieses específicos. Labels ImageNet são úteis para inspeção, mas não substituem análise interpretativa. Captions podem descrever cenas de forma plausível, mas também podem omitir, simplificar ou alucinar elementos. Estimativas de idade e gênero por modelos de face devem ser tratadas como material crítico para análise de viés, não como informação confiável sobre pessoas.

A redução PCA preserva variação estatística, não significado cultural. Uma variância explicada alta tende a preservar relações globais entre vetores, mas diferenças locais ou minoritárias podem ser reduzidas. Por isso, recomenda-se comparar modelos, verificar a variância explicada e inspecionar visualmente os resultados no MOODIE Web.

## Citação

Bitencourt, E. (2026). *MOODIE: Modular Observational & Operational Design Image Explorer* [Web app and Python companion]. Datalab Design, Universidade do Estado da Bahia. https://moodie.datalabdesign.org

## Autor

**Elias Bitencourt** é professor no curso de Design da Universidade do Estado da Bahia (UNEB) e coordenador do Datalab Design. Suas pesquisas articulam visualização de dados, métodos digitais, estudos de plataformas, visualidades algorítmicas e mediações sociotécnicas. O MOODIE integra uma agenda de investigação sobre imagens como objetos informacionais e operacionais em ambientes digitais.

## Status

O MOODIE Web e o MOODIE Web Companion estão em desenvolvimento. O fluxo atual é funcional para preparação e exportação de projetos `.moodie`, mas mudanças de interface, formato de pacote e módulos analíticos podem ocorrer em versões futuras.

## Licença

Este repositório está sob licença de uso restrito com atribuição. O conteúdo pode ser utilizado para fins acadêmicos e não comerciais com devida atribuição ao autor. Modificações, redistribuição ou uso comercial exigem autorização.
