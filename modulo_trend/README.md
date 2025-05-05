<p align="left">
  <img src="LOGO_MOODIE.png" alt="Logo MOODIE Image Trends" width="320"/>
</p>

# MOODIE Image Trends &nbsp;·&nbsp; guia passo‑a‑passo
> **Uma trilha de aprendizado visual para quem ainda não programa.**

MOODIE Image Trends é um _notebook_ que transforma **uma coleção de imagens** num
conjunto de **painéis, paletas e recomendações** prontas para análise de tendências. 

Ele foi pensado para disciplinas de **Pesquisa Visual**, **Design de Moda**,
**UX Research** e **Semiótica** onde o aluno pode tirar proveito de técnicas avançadas de IA integradas em processos de design **sem precisar escrever código**.


MOODIE Image Trends_ é um notebook interativo para Google Colab que transforma **uma coleção de imagens** num
conjunto de **painéis, paletas e recomendações** prontas para análise de tendências. 

Inicialmente concebido como ferramenta pedagógica para as disciplinas de Introdução aos métodos digitais aplicados ao design e visualização de dados do curso de design da UNEB, o objetivo é permitir que estudantes de Design explorem de forma prática processos de exploração em Moda, editorial, UX Research e metodologia projetual, integrando conceitos de machine learning, vetores e modelos de visão computacional, sem a necessidade de codificação. Pesquisadores interessados em mapear padrões visuais ou detectar tendências em grandes acervos também encontrarão nele um ponto de partida para análises mais aprofundadas.

---

## O que você precisa

* uma pasta ou arquivo zip  com as imagens que vc quer examinar (.jpg/.png).  
* um arquivo CSV contendo metadados e outras informações complementares sobre suas imagens (opcional)
* Conta Google → abra o notebook no **Google Colab** e rode célula por célula.

<sub>_você pode usar os dados que obteve no módulo grabber do moodie_</sub>


## 1. Visão geral do fluxo

## 1. Visão geral do fluxo

| Etapa | O que entra | O que acontece | O que sai | Usos Potenciais (O que permite fazer) |
|-------|-------------|---------------|-----------|---------------------------------------|
| 1. Importação | • Pasta **`imagens/`**<br>• (Opcional) arquivo **CSV** com metadados | Notebook carrega imagens; se houver CSV, ele associa cada linha à imagem pelo nome do arquivo. | `global_df` (DataFrame) com pelo menos a coluna de nome da imagem e quaisquer colunas extras do CSV | Organizar e catalogar coleções visuais, associar informações textuais (metadados) às imagens para análise posterior. |
| 2. Extração de *features* | `global_df` + escolha do modelo | Cada imagem é codificada num vetor numérico (embedding). | Nova coluna `…_features` anexada ao `global_df` | Transformar imagens em dados numéricos comparáveis para análise visual, identificar semelhanças visuais de forma automatizada. |
| 3. Remoção de duplicatas | `global_df` com embeddings | Algoritmo greedy identifica: 1) arquivos idênticos, 2) imagens quase‑iguais (pHash) e 3) embeddings muito próximos; move cópias p/ subpasta e grava CSVs. | • **dataset_no_duplicates.csv**<br>• **dataset_duplicates.csv**<br>• 3 image‑walls (full / sem dups / apenas dups) | Limpar bases de dados visuais de redundâncias, otimizar análises focando em conteúdo único, identificar e analisar variações sutis entre imagens quase idênticas. |
| 4. Mapas perceptuais & paletas | `global_df` (sem duplicatas) | Agrupa cores dominantes & representativas ➀, cria mapa perceptual, extrai paletas temáticas e versões acessíveis (WCAG + simulação de daltonismo). | PNGs dos mapas, paletas temáticas e acessíveis + CSV com células de cor | Visualizar a distribuição de cores em um conjunto de imagens, identificar paletas de cores predominantes e temáticas, gerar paletas acessíveis para design inclusivo. |
| 5. Imagewalls | DataFrame filtrado (por ranking, cor, representatividade ②, etc.) | Constrói mural (thumbs ordenados), opcionalmente gera paleta resumida no rodapé. | JPEG da wall + CSVs de **wall_info** & **images_info** além de relatórios e csvs com detalhes sobre os metadados associados às imagens presentes na imagewall| Criar visualizações gerais ou sínteses visuais do corpus com base em critérios específicos (similaridade visual, cores, metadados como preço, engajamento etc.), gerar referências visuais para projetos de design. |
| 6. Recomendação | Embeddings + colunas extra + parâmetros de usuário | Calcula similaridade (cosseno/ euclidiana/ correlação); combina pesos; devolve Top‑N, gera wall + dashboards de cor + dashboard‑síntese; empacota tudo em ZIP. | • imagewall_topN.png<br>• dashboards_dom / rep<br>• dashboard_resumo.png<br>• CSVs com detalhamento descritivo recomendações | Explorar tendências e mapear padrões visuais combinando algoritmos de recomendação customizáveis com parâmetros do usuário e técnicas avançadas de visão computacional. Descobrir imagens visualmente semelhantes dentro de um conjunto de dados, gerar recomendações visuais personalizadas com base em preferências ou critérios definidos, criar mood boards dinâmicos e exploratórios. |

➀ **Método**: k‑means ponderado pela frequência das cores → clustering em LAB → cada célula exibida em grade 20×20.

② **Método**: o critério de representatividade seleciona as imagens que mais se aproximam da média do centróide do grupo (caso usuário opte por agrupar por algum critério) ou do corpus.

---


## 2. Módulos detalhados

### 2.1 Amostragem (Sample Dataset)
*Uso opcional* — permite selecionar subconjuntos amostrais por aleatoriedade. Se você tem um dataset com csv, a interface irá te oferecer algumas opções de amostragem. Se você possui apenas um conjunto de imagens, a interface detecta automaticamente e permite apenas que vc informe o total de imagens que vc deseja na sua amostra final.

| Entrada | Método | Indicado quando |
|---|---|---|
| CSV | Downsample | Utilize para reduzir o tamanho do dataset, mantendo a proporção entre as categorias (se especificada). Útil quando o dataset é muito grande e você deseja uma amostra menor para prototipagem ou análise exploratória sem perder a representatividade das classes majoritárias. |
| CSV | Upsample | Utilize para aumentar o tamanho de categorias minoritárias, replicando amostras existentes. Útil para balancear datasets desequilibrados, onde algumas categorias têm muito menos exemplos que outras, o que pode prejudicar o treinamento de modelos de *machine learning*. |
| CSV/IMAGEM | Random Sample | Utilize para selecionar um número aleatório de amostras do dataset inteiro, sem considerar as categorias. Útil para obter uma visão geral rápida do dataset ou quando as categorias não são relevantes para a amostragem. Se estiver usando apenas imagens, esta é a única opção disponível para criar um subconjunto aleatório. |


### 2.2 Extração de *features*
### 2.2 Extração de Características Visuais das Imagens

O Moodie utiliza modelos de inteligência artificial pré-treinados para "ler" suas imagens e transformá-las em representações numéricas, chamadas de "características visuais" (ou *features*). Pense nisso como se cada imagem ganhasse um código secreto que descreve o que ela contém visualmente.

**Por que isso é importante?**

Essa etapa permite que o Moodie compare e encontre similaridades entre as imagens de uma maneira que um computador consegue entender. É a base para todas as análises visuais que você poderá fazer, como encontrar imagens parecidas, agrupar imagens por estilo, e identificar tendências visuais.

**Como funciona?**

1.  **Escolha do Modelo:** O Moodie oferece uma lista de modelos pré-treinados (como os mostrados na tabela abaixo). Cada modelo é como um especialista em identificar diferentes tipos de informações visuais. Você pode escolher um ou mais modelos para analisar suas imagens.
2.  **"Leitura" das Imagens:** O Moodie pega cada uma de suas imagens e a passa pelo modelo escolhido. O modelo realiza uma série de operações para identificar padrões visuais (formas, cores, texturas, objetos, etc.).
3.  **Criação do "Código Visual":** Para cada imagem, o modelo gera um "código" numérico (o *embedding* ou vetor de características) que resume o que ele "viu".
4.  **Armazenamento:** Esse "código visual" é guardado em uma nova coluna na sua tabela de dados, chamada `Modelo_features`.

**Dicas importantes:**

* **Processamento:** Essa etapa pode levar algum tempo, especialmente com muitas imagens. Se você não tem um computador potente, comece com uma amostra menor (por exemplo, 100-200 imagens) para testar e entender qual modelo funciona melhor para o seu projeto. Depois, você pode rodar a análise no seu dataset completo.
* **Escolha do Modelo:** A tabela abaixo descreve brevemente para que tipo de análise cada modelo pode ser mais adequado. Experimentar diferentes modelos pode trazer resultados interessantes!

| Modelo | Foco Principal | Bom para identificar… |
|---|---|---|
| **VGG16** (padrão) | Texturas e cor global | Aparência geral das cores e texturas predominantes nas imagens. |
| **ResNet50** | Formas complexas e objetos | Formas de objetos, detalhes de produtos e estruturas visuais mais complexas. |
| **MobileNetV2** | Texturas e cor global (rápido) | Análise rápida de cores e texturas, ideal para grandes coleções e testes iniciais. |
| **InceptionV3** | Variação de escala e detalhes | Detalhes finos e objetos em diferentes tamanhos dentro da mesma imagem. |

Ao final dessa etapa, suas imagens estarão representadas numericamente, prontas para serem exploradas e analisadas visualmente com as outras ferramentas do Moodie!


---

### 2.3 Remoção de Duplicatas Visuais

Depois de extrair as características visuais das suas imagens, o Moodie pode identificar e remover automaticamente imagens duplicadas ou muito semelhantes em sua coleção. Isso é útil para limpar seu dataset, evitar redundâncias nas análises futuras e garantir que as tendências visuais identificadas sejam representativas de imagens únicas.

**Como o Moodie identifica duplicatas?**

O Moodie utiliza três métodos inteligentes para encontrar duplicatas, verificando diferentes níveis de semelhança:

1.  **Cópias Exatas (Hash MD5):** Procura por arquivos que são idênticos byte a byte. É como verificar se dois arquivos são exatamente a mesma cópia.
2.  **Cópias Quase Iguais (pHash):** Identifica imagens que são muito parecidas visualmente, mesmo que tenham pequenas diferenças (como compressão ou pequenas edições). Pense nisso como encontrar fotos quase idênticas, mas com pequenas variações.
3.  **Semelhança Visual Profunda (Embedding):** Utiliza os "códigos visuais" (embeddings) extraídos na etapa anterior para encontrar imagens que são visualmente muito semelhantes em um nível mais profundo, mesmo que não sejam cópias exatas ou quase exatas. Se você usou mais de um modelo, é recomendado que vc remova as duplicatas usando o mesmo modelo que irá prosseguir com as análises

**O que acontece durante a remoção?**

* **Seleção da "Âncora":** Para cada grupo de imagens duplicadas, o Moodie escolhe a primeira imagem que encontrou como a "âncora" (a imagem que será mantida).
* **Movimento das Duplicatas:** As cópias identificadas são movidas automaticamente para uma pasta chamada `duplicatas` dentro da sua pasta de `imagens/`. Isso mantém seu conjunto de dados principal limpo, mas você ainda tem acesso às duplicatas, caso precise revisá-las.
* **Acompanhamento do Processo:** Enquanto o Moodie trabalha, você verá barras de progresso mostrando o andamento da análise.
* **Resultado Final:** Ao final do processo, um botão **Baixar ZIP** aparecerá. Ao clicar, você baixará um arquivo compactado (.zip) contendo:
    * Um dataset "limpo" (sem as duplicatas removidas).
    * Um registro das duplicatas que foram encontradas.
    * Paredes de imagens (image walls) mostrando:
        * Todas as imagens do seu conjunto inicial.
        * Apenas as imagens únicas (após a remoção das duplicatas).
        * A coleção de imagens que foram identificadas como duplicatas.

**Por que remover duplicatas?**

* **Análises mais precisas:** Garante que as tendências visuais e os resultados das análises futuras não sejam distorcidos por múltiplas cópias da mesma imagem.
* **Economia de espaço:** Ajuda a reduzir o tamanho da sua coleção de imagens, facilitando o gerenciamento e o processamento.
* **Melhor organização:** Mantém seu conjunto de dados principal mais organizado e focado em imagens únicas.

Ao utilizar a remoção de duplicatas do Moodie, você garante uma base de dados visual mais limpa e confiável para suas explorações e análises de tendências!

---

### 2.4 Análise de Cores e Geração de Paletas

Esta etapa do Moodie mergulha nas cores do seu conjunto de imagens para criar um mapa visual da distribuição de cores predominantes e sugerir paletas temáticas relevantes para o seu corpus.

**Mapa Perceptual de Cores:**

Imagine um mapa onde cada ponto representa uma cor presente no seu conjunto de imagens. Cores semelhantes visualmente ficam próximas umas das outras, criando "regiões" de cores predominantes. O Moodie gera esse mapa perceptual combinando as cores de todas as suas imagens e aplicando um algoritmo que agrupa cores semelhantes espacialmente. A intensidade da cor em certas áreas do mapa indica a concentração dessas tonalidades no seu corpus.

**Exemplo Visual:**

[Inclua aqui o link para a imagem do mapa perceptual. Exemplo: `![](./caminho/para/sua/imagem_mapa_perceptual.png)`]

*Este mapa mostra como as cores estão distribuídas no seu conjunto de imagens. Áreas com cores vibrantes indicam uma maior ocorrência dessas tonalidades.*

**Como o Moodie encontra as "zonas" de cor?**

O Moodie utiliza conceitos da **teoria das cores** para identificar regiões significativas no mapa perceptual. Ele se baseia na ideia do **círculo cromático**, onde as cores estão dispostas em um ciclo baseado em suas relações de matiz.

1.  **Divisão por Matiz:** O Moodie divide o espectro de cores em 24 faixas de matiz (cada uma com 15 graus no círculo cromático), atribuindo um nome a cada faixa (por exemplo, Vermelho, Laranja, Amarelo, Verde, Azul, Violeta).
2.  **Agrupamento por Proximidade:** No mapa perceptual, cores com matizes semelhantes e próximas espacialmente tendem a se agrupar. O algoritmo identifica esses agrupamentos como "zonas" de cor predominantes no seu corpus.
3.  **Análise Estatística:** Para cada "zona", o Moodie calcula a média das tonalidades presentes para determinar a cor representativa daquela região.

**Paletas Temáticas:**

Além do mapa, o Moodie extrai paletas de cores temáticas, sugerindo conjuntos de 5 cores que podem ser relevantes ou representativas do seu corpus sob diferentes perspectivas.

**Lógica e Métricas das Paletas Temáticas:**

A escolha das cores para cada paleta temática se baseia em uma combinação da distribuição de cores no mapa perceptual e em métricas que avaliam as características das cores dentro de cada "zona". A lógica geral é identificar cores distintas e representativas de diferentes áreas do mapa, priorizando aquelas que melhor se encaixam na "temática" da paleta.

| Tema da Paleta | Lógica Principal | Métricas Utilizadas |
|---|---|---|
| **Colorido** | Prioriza cores com alta saturação e luminosidade intermediária, buscando variedade e vivacidade. | Saturação (alta), Luminosidade (próxima de 0.5), Distância de cor entre as amostras. |
| **Brilho** | Seleciona cores claras e saturadas, evocando energia e destaque. | Luminosidade (alta), Saturação (alta). |
| **Suave** | Escolhe cores claras e com baixa saturação (tons pastel), transmitindo delicadeza e calma. | Luminosidade (alta), Saturação (baixa). |
| **Profundo** | Opta por cores escuras e saturadas, sugerindo intensidade e mistério. | Luminosidade (baixa), Saturação (alta). |
| **Escuro** | Foca em cores predominantemente escuras, com alguma saturação para evitar tons puramente acromáticos. | Luminosidade (baixa), Saturação (moderada a alta), Peso da frequência de cores escuras. |
| **Acessível** | Seleciona cores que são distinguíveis para pessoas com diferentes tipos de daltonismo (protanopia, deuteranopia, tritanopia). | Distância de cor (Delta E) simulada para diferentes tipos de daltonismo (mínimo de contraste seguro). |

Para a paleta "Acessível", o Moodie tenta selecionar uma cor representativa de cada uma das 5 "zonas" de cor identificadas, garantindo que as cores escolhidas tenham contraste suficiente para serem distinguidas por pessoas com daltonismo. Se não for possível encontrar 5 cores distintas e seguras, a paleta pode conter menos cores, com um aviso indicando isso.

**Renomeando Imagens:**

O Moodie oferece a opção de renomear seus arquivos de imagem com base em diferentes critérios extraídos durante a análise:

| Critério de Renomeação | Descrição | Útil para… |
|---|---|---|
| **Não renomear** | Mantém os nomes de arquivo originais. | Quando você já possui uma convenção de nomenclatura estabelecida ou não deseja alterar os nomes dos arquivos. |
| **Renomear por HEX** | Renomeia os arquivos com o código hexadecimal da cor dominante detectada na imagem. | Organizar visualmente as imagens por sua cor principal. |
| **Renomear por pHash** | Renomeia os arquivos com o valor do pHash (perceptual hash) da imagem. O pHash gera uma "impressão digital" visual da imagem, útil para identificar duplicatas aproximadas. | Agrupar imagens visualmente semelhantes (mesmo com pequenas alterações). |

**Metadados EXIF:**

EXIF (Exchangeable Image File Format) são metadados incorporados em arquivos de imagem pela maioria das câmeras digitais e smartphones. Se presentes nas suas imagens, o Moodie pode extrair diversas informações úteis:

* **Data e Hora da Captura:** Quando a foto foi tirada.
* **Modelo da Câmera/Celular:** O dispositivo utilizado para capturar a imagem.
* **Configurações da Câmera:** ISO, abertura, velocidade do obturador, distância focal.
* **Informações de Lente:** Modelo da lente utilizada.
* **Dados de GPS:** Coordenadas geográficas de onde a foto foi tirada (latitude e longitude).

Esses metadados podem ser valiosos para análises contextuais e para entender a origem das suas imagens. O Moodie verifica a presença dessas informações e as disponibiliza para você explorar em seus dados.

---

## 🖼️ 5. Image‑Walls

| O que você escolhe | O que o notebook faz | Arquivo gerado |
|--------------------|----------------------|----------------|
|**Top N Relevantes**|Pega as imagens mais parecidas com algo (embedding) e faz o mosaico|`imagewall/IW_Geral_rep_top_N.jpg`|
|**Ranking** (likes, preço…)|Ordena pela coluna escolhida e mostra só as primeiras|`…ranking_MIN_PRICE_top_10.jpg`|
|**Cor dominante**|Ordena da foto mais escura para a mais clara (ou vice‑versa)|`…colordom_top_50.jpg`|

Rodapés mostram a **paleta resumida** da parede.

---

## 🤖 6. Recomendador (o “coração” do Trends)

1. **Escolha a imagem‑referência**  
   * interna (nome do CSV) **ou** upload externo.
2. **Selecione o embedding** e **pese** sua importância.  
3. **Adicione colunas extras** (ex.: “estação”, “preço”):  
   * numéricas ➜ média, mediana …  
   * texto ➜ Jaccard (semelhança de listas).  
4. **Critério de ordenação**  
   * _Relevância_ = os mais parecidos.  
   * _Contraste_  = os mais diferentes.  
   * _Concordância_ = meio‑termo (nem tão iguais, nem tão opostos).

No final você baixa **um ZIP** com:

```
imagewall_topN.png
dashboard_dom.png
dashboard_rep.png
dashboard_resumo.png  ← tudo em uma página
dataset/recomendacoes_full.csv
```

---

## 🏃‍♀️ 7. Passo‑a‑passo rápido (TL;DR)

```text
1. Carregue seu CSV + imagens
2. Rode ► “Amostragem”        (opcional)
3. Rode ► “Extração de Features”
4. Rode ► “Remoção de Duplicatas”
5. Rode ► “Mapas & Paletas”   (apenas clicar)
6. Rode ► “Image‑Walls”
7. Rode ► “Recomendação”
```

Cada botão gera **links de download** direto no Colab.

---

## 📚 Para saber mais

* Simonyan, K. & Zisserman, A. (2014). _Very Deep Convolutional Networks…_  
* He, K. et al. (2015). _Deep Residual Learning…_  
* Brettel, H. et al. (1997). _Computerized simulation of color blindness…_

---

<p align="center">
  Feito no Datalab/Design · UNEB — 2025<br/>
  Uso acadêmico não‑comercial · ver arquivo <strong>LICENSE</strong>
</p>
