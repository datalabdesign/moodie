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

![](color_map.png)

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

### 2.5 Visualização do Corpus

Esta etapa do Moodie permite criar representações visuais do seu conjunto de imagens, facilitando a exploração e a identificação de padrões. Você pode gerar "paredes de imagens" (imagewalls) que organizam suas fotos em uma grade, com opções para filtrar, agrupar e exibir informações relevantes.

![](exemplo_image_wall.jpg)

**O que você pode fazer?**

* **Visualização Geral:** Criar uma visão geral de todas as imagens do seu corpus em uma única grade.
* **Agrupamento Visual:** Organizar as imagens em paredes separadas com base em categorias ou características específicas (por exemplo, por cor dominante, por similaridade visual - se você executou a etapa de clusterização).
* **Filtragem:** Exibir apenas um subconjunto das imagens com base em critérios como representatividade (as imagens mais típicas de um grupo), ranking (se você tiver dados de classificação associados às imagens) ou cor dominante.
* **Exibição de Paletas de Cores:** Incluir automaticamente a paleta de cores dominante de cada grupo de imagens diretamente na parede visual.
* **Eliminação de Duplicatas (Opcional):** Antes de gerar as visualizações, você pode optar por remover imagens duplicadas ou muito semelhantes para obter uma representação mais limpa do seu corpus único.

**Como funciona?**

1.  **Seleção da Coluna de Imagens:** Primeiro, você informa ao Moodie qual coluna da sua tabela de dados contém os nomes dos arquivos de imagem.
2.  **Opções de Agrupamento (Opcional):** Se desejar, você pode escolher uma coluna para agrupar suas imagens. O Moodie criará uma parede de imagens separada para cada grupo único nessa coluna. Por exemplo, se você tiver uma coluna "Estilo", poderá visualizar uma parede para cada estilo diferente.
3.  **Opções de Filtragem (Opcional):**
    * **Nenhum:** Exibe todas as imagens (após a remoção de duplicatas, se selecionado).
    * **Representatividade:** Para cada grupo (ou para todo o corpus, se não houver agrupamento), o Moodie identifica as imagens que são mais representativas visualmente, com base nas características extraídas anteriormente. Você pode definir quantas imagens "Top N" deseja visualizar por grupo.
    * **Ranking:** Se você tiver uma coluna com valores de ranking (por exemplo, pontuações de popularidade), pode usar essa opção para exibir as imagens com os maiores ou menores rankings (Top N). Você precisa selecionar a coluna de ranking e a ordem (crescente ou decrescente).
    * **Cor Dominante:** Permite exibir as imagens com as cores dominantes mais claras ou mais escuras (Top N), com base na coluna de cor dominante extraída na etapa anterior.
4.  **Tamanho dos Miniaturas:** Você pode definir a largura e a altura desejadas para as miniaturas das imagens na parede visual. O Moodie ajustará automaticamente o layout para acomodar o número de imagens.
5.  **Recorte (Opcional):** A opção "Crop (Quadrado)" instrui o Moodie a recortar as imagens para um formato quadrado antes de redimensioná-las para as miniaturas, garantindo uma grade visualmente uniforme.
6.  **Paletas de Cores (Opcional):** Você pode escolher se deseja incluir uma faixa com a cor dominante e uma pequena paleta de cores representativas abaixo de cada parede de imagens (para a visualização geral e/ou para cada grupo).
7.  **Eliminação de Duplicatas (Opcional):** Ao marcar essa caixa, o Moodie realizará uma verificação para remover imagens que são cópias exatas, quase idênticas (visualmente muito semelhantes) ou muito próximas em termos de suas características visuais (embeddings). **Se você já executou a etapa de remoção de duplicatas anteriormente, não é necessário marcar esta opção novamente.**

**Como o Moodie lida com a eliminação de duplicatas?**

Se você optar por eliminar duplicatas, o Moodie aplica uma série de verificações:

1.  **Duplicatas Exatas (Hash MD5):** Ele compara um "código" único gerado a partir do conteúdo de cada arquivo. Se os códigos forem idênticos, os arquivos são considerados cópias exatas.
2.  **Duplicatas Quase Iguais (pHash):** Ele usa um algoritmo chamado "perceptual hash" (pHash) que cria uma "impressão digital" visual da imagem. Imagens com pHashes muito semelhantes (uma diferença de até 4 bits) são consideradas cópias quase idênticas.
3.  **Duplicatas por Características Visuais (Opcional):** Se você já extraiu as características visuais das imagens, o Moodie pode comparar esses "códigos visuais". Imagens com códigos muito próximos (uma similaridade de cosseno muito alta) são consideradas visualmente muito semelhantes.

O Moodie mantém a primeira imagem encontrada de cada grupo de duplicatas e remove as demais antes de gerar as paredes visuais. Um resumo do número de duplicatas removidas por cada método é exibido.

**Resultado:**

Ao final do processo, o Moodie gerará uma ou mais imagens (arquivos `.jpg`) contendo as paredes visuais do seu corpus (geral e/ou por grupos), salvas em uma pasta chamada `imagewall` dentro do seu `project_dir`. Se você optou por incluir paletas de cores, elas serão exibidas abaixo de cada parede. Você poderá visualizar essas imagens diretamente no seu ambiente de análise.

A visualização do corpus é um recurso de apoio para obter *insights* rápidos sobre o conteúdo visual da sua coleção de imagens, identificar padrões e comunicar suas descobertas de forma visualmente rica.

---

### 2.6 Moodie Trends: Sistema de Recomendação

Este módulo permite encontrar imagens similares e criar curadorias personalizadas.

**1. Configuração Inicial:**

| Etapa                     | Descrição                                                                 | Ações do Usuário                                                                 |
| ------------------------- | --------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| Seleção Coluna de Imagens | Indica qual coluna contém os nomes dos arquivos de imagem.                | Escolher a coluna correspondente no menu suspenso.                               |
| Seleção Coluna Embedding  | Escolhe a coluna com os vetores de características visuais das imagens. | Selecionar a coluna de embeddings no menu suspenso.                             |
| Seleção Métrica          | Define como a similaridade visual será calculada.                          | Escolher entre Cosseno, Euclidiana ou Correlação no menu suspenso.              |
| Peso do Embedding         | Ajusta a importância das características visuais na busca.                 | Usar o slider para definir um peso (0.0 a 10.0).                                |

**2. Métricas de Similaridade:**

| Métrica     | Descrição                                                                                                | Quando Usar                                                                                                                               |
| ----------- | -------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| **Cosseno** | Mede a similaridade do ângulo entre os vetores, ignorando a magnitude.                                  | Para encontrar imagens com padrões visuais semelhantes (composição, elementos), independentemente de brilho ou contraste.                     |
| **Euclidiana** | Calcula a distância geométrica entre os vetores. Menor distância indica maior similaridade.             | Para encontrar imagens que são globalmente mais próximas em termos de cores e texturas.                                                     |
| **Correlação** | Mede a relação linear entre os vetores, focando em variações semelhantes nas características visuais. | Para encontrar imagens com mudanças de iluminação ou variações de cor semelhantes, ignorando os níveis absolutos das características. |

**3. Colunas Extras (Opcional):**

| Recurso                | Descrição                                                                                              | Ações do Usuário                                                                                                                               |
| ---------------------- | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| Seleção de Colunas     | Escolha colunas adicionais de metadados para refinar a recomendação (numéricas ou de texto).          | Marcar as caixas de seleção ao lado das colunas desejadas.                                                                                       |
| Ajuste de Pesos        | Defina a importância de cada coluna extra na recomendação.                                             | Usar sliders para definir pesos individuais para cada coluna selecionada.                                                                      |
| Tipos de Similaridade  | Define como a similaridade será calculada para cada tipo de coluna extra.                             | O Moodie detecta automaticamente o tipo da coluna (numérica, texto, conjunto) e oferece opções de similaridade adequadas.                           |
| - Numérica             | Calcula a diferença entre os valores. Permite definir uma diferença máxima para normalização.           | (Interno ao Moodie) Ajustar a "diferença máxima" se necessário.                                                                                    |
| - Texto                | Compara a similaridade entre os textos.                                                              | (Interno ao Moodie)                                                                                                                              |
| - Conjunto             | Compara a similaridade entre conjuntos de elementos (e.g., tags).                                      | (Interno ao Moodie)                                                                                                                              |

**Passo B: Ajustar Agregador, Separador e Peso para Colunas Extras**

Nesta etapa, você pode refinar como as colunas extras selecionadas serão usadas no cálculo de similaridade.

* **Agregador:** Esta opção permite definir como lidar com múltiplos valores encontrados em uma única célula da coluna extra.
    * **Para colunas numéricas:**
        * `none`: Utiliza o valor numérico diretamente (se houver apenas um). Se houver múltiplos valores (separados pelo separador definido), o primeiro valor encontrado será usado.
        * `mean`: Calcula a média de todos os valores numéricos encontrados na célula (após a divisão pelo separador).
        * `median`: Calcula a mediana de todos os valores numéricos encontrados na célula.
        * **Quando usar:** Use `mean` ou `median` se suas células contiverem múltiplos valores numéricos relevantes (por exemplo, faixas de preço, múltiplas medidas) e você quiser usar uma medida central para a comparação. Use `none` se cada célula contiver um único valor representativo ou se você quiser usar apenas o primeiro valor de múltiplos.
    * **Para colunas de texto (com a opção de similaridade Jaccard):**
        * `none`: Utiliza a string de texto diretamente para comparação exata.
        * `jaccard`: Calcula a similaridade entre conjuntos de palavras ou termos presentes na célula de referência e na célula da imagem sendo comparada. Para isso, a string é dividida usando o separador definido. A similaridade de Jaccard é a razão entre o número de elementos em comum e o número total de elementos nos dois conjuntos.
        * **Quando usar:** Use `jaccard` se suas colunas de texto contiverem múltiplos termos ou tags relevantes (por exemplo, estilos, materiais, palavras-chave) separados por um delimitador, e você quiser medir a sobreposição desses termos. Use `none` para uma comparação de texto exata.

* **Separador:** Defina o caractere ou a string usada para separar múltiplos valores dentro de uma mesma célula da coluna extra.
    * **Para colunas numéricas:** Se uma célula contiver "10,20,30", você definiria "," como separador para que os valores 10, 20 e 30 sejam considerados para o cálculo da média ou mediana.
    * **Para colunas de texto (com a opção Jaccard):** Se uma célula contiver "azul,vermelho,amarelo", você definiria "," como separador para que "azul", "vermelho" e "amarelo" sejam tratados como elementos separados para calcular a similaridade de Jaccard.
    * **Quando usar:** Insira o caractere que delimita os múltiplos valores em suas células. Deixe em branco se cada célula contiver apenas um valor.

* **Peso:** Ajuste a importância desta coluna extra no cálculo geral de similaridade. Um peso maior fará com que essa característica tenha uma influência maior na ordenação dos resultados da recomendação.

Ao configurar o agregador, o separador e o peso de cada coluna extra, você pode personalizar o sistema de recomendação para priorizar as características mais relevantes para a sua análise.


**4. Busca por Similaridade:**

| Etapa             | Descrição                                                                | Ações do Usuário                                                                 |
| ----------------- | -------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| Modo de Referência | Define a origem da imagem de referência para a busca.                     | Escolher entre "Imagem interna do DF" ou "Upload imagem externa" no menu.        |
| Imagem de Referência | Seleciona a imagem a ser usada como base para a busca.                  | Se interna: inserir o nome do arquivo. Se externa: fazer o upload do arquivo. |
| Top N             | Define o número de imagens similares a serem retornadas.                 | Usar o slider para definir a quantidade desejada.                                |
| Critério de Ordem | Define como as imagens similares serão ordenadas.                         | Escolher entre Relevância, Contraste ou Concordância no menu suspenso.          |

**5. Critérios de Ordenação:**

| Critério      | Descrição                                                                                                | Quando Usar                                                                                                                                                              |
| ------------- | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Relevância** | Ordena por similaridade decrescente, mostrando as imagens mais parecidas com a referência.               | Para encontrar as imagens que melhor correspondem à imagem de referência e aos critérios definidos.                                                                     |
| **Contraste** | Ordena por similaridade crescente, mostrando as imagens menos parecidas com a referência.                | Para encontrar imagens que são visualmente distintas ou que podem complementar a imagem de referência. Útil para exploração de diversidade.                               |
| **Concordância** | Ordena por similaridade com base na proximidade a um ponto médio de similaridade (nem muito similar, nem muito diferente). | Para encontrar imagens que são representativas do "meio" do seu corpus em relação à referência. Pode ser útil para identificar imagens típicas ou evitar extremos de similaridade. |

**6. Resultados:**

Após a configuração e a execução da busca, o Moodie Trends gerará:

* **Mapas Perceptuais de Cores:** Visualizações da distribuição de cores (dominantes e representativas) nas imagens recomendadas.
* **Paletas de Cores:** Paletas temáticas e acessíveis geradas a partir das cores das imagens recomendadas.
* **Corpus de Imagens Similares:** Um conjunto contendo as Top N imagens consideradas similares.
* **Síntese de Metadados:** Um resumo dos critérios e pesos utilizados na sua recomendação personalizada.

Essas tabelas oferecem uma visão organizada do processo de configuração e das opções disponíveis no módulo Moodie Trends, facilitando a compreensão e o uso de seus recursos.

---


<p align="left">
  Feito no Datalab/Design · UNEB — 2025<br/>
  Uso acadêmico não‑comercial · ver arquivo <strong>LICENSE</strong>
</p>
