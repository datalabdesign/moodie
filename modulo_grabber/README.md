<img src="MOODIE_GRABBER.png" alt="Logo MOODIE" width="400"/>

# MOODIE Grabber (Módulo de Coleta de Imagens)

**MOODIE Grabber** é o módulo de coleta de imagens da ferramenta [MOODIE](https://github.com/datalabdesign/moodie), desenvolvido para permitir o download e pré-processamento de imagens a partir de um arquivo CSV com links. Ele está em fase **beta**, sendo o primeiro módulo funcional da ferramenta MOODIE.

Este módulo foi pensado como uma introdução prática ao uso de imagens em pipelines de pesquisa em design, oferecendo desde a coleta até os primeiros metadados estruturados que alimentam etapas posteriores de análise visual e exploração criativa.

[![Abrir no Google Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/datalabdesign/moodie/blob/main/modulo_grabber/moodie_grabber_clean.ipynb)



## ✦ Atualizações (08 / 05 / 2025)

* **Compatibilidade total com JSON**  
  O módulo identifica automaticamente a estrutura de listas em JSON, “achata” chaves aninhadas e converte em CSV antes da análise — perfeito para APIs que retornam grandes coleções de itens.

* **Download paralelo de imagens (+ retry/back-off)**  
  Agora as imagens são baixadas com até 10 threads simultâneas, com cabeçalhos *User-Agent* aleatórios e política de re-tentativa (códigos 429/5xx), acelerando o processo e reduzindo bloqueios por servidor.

---

## ✦ Funcionalidades

O MOODIE Grabber permite:

- **Leitura de CSV ou JSON** contendo links de imagens (campo configurável);
- **Sampleamento estatístico** do dataset completo:
  - Amostragem proporcional, aleatória, por classe ou por tamanho (upsample/downsample);
- **Remoção de duplicatas** com base na seleção de colunas específicas;
- **Download automático de imagens em paralelo**, com:
  - Política de retry/back-off integrada;
  - Estratégias de nomeação: nome original, hash hexadecimal ou hash perceptual;
- **Extração automática de metadados**:
  - Tamanho do arquivo, dimensões, tipo e formato da imagem;
  - Informações EXIF (quando disponíveis);
  - Domínio de origem do link (útil para análise de proveniência);
- Geração de um **dataset estruturado** com os dados das imagens baixadas;
- Integração com os demais módulos da ferramenta MOODIE (Metadata e Trends).

---

## ✦ Requisitos

Este módulo é compatível com o Google Colab, sem necessidade de instalação local. As bibliotecas utilizadas são automaticamente disponíveis no ambiente Colab:

- `pandas`
- `numpy`
- `PIL` (do pacote `Pillow`)
- `imagehash`
- `tqdm`
- `requests`
- `ipywidgets`
- `matplotlib`
- `IPython.display`

---

## ✦ Como usar

A execução do MOODIE Grabber no Google Colab é feita **passo a passo, executando as células do notebook na ordem** e interagindo com a interface. Abaixo, um resumo prático de cada etapa:

### 0.0 Antes de começar, vamos precisar de um CSV
Prepare um arquivo `.csv` com pelo menos uma coluna contendo os links de imagens, como no exemplo abaixo

| col a  | link_imagem                 |
|----------|-----------------------------|
| ........ | https://exemplo.com/img1.jpg |
| ........ | https://exemplo.com/img2.jpg |



### 01. Instalando as dependências
Clique no botão **“Instalar Dependências”** e aguarde a instalação dos pacotes necessários.

### 02. Conectando ao Google Drive (opcional, mas recomendado)
O MOODIE funciona melhor se você estiver conectado ao seu Google Drive. Isso ajuda a evitar lentidão com arquivos grandes e mantém seus dados salvos entre sessões.

Se preferir, também é possível continuar trabalhando apenas com uploads locais, sem o Drive.

### 03. Criando diretórios de trabalho
Você poderá configurar:
- Se deseja salvar os dados no seu Google Drive ou apenas no ambiente temporário do Colab;
- Se já tem uma pasta existente ou deseja criar um novo diretório para o projeto;
- O nome do projeto, que será usado para nomear arquivos; ex: `dataset_nome_do_projeto.csv`
- Notas sobre o projeto, que serão registradas para referência futura.

Defina os parâmetros de execução no início do notebook:
- Caminho do arquivo CSV;
- Nome da coluna com os links;
- Caminho da pasta onde salvar as imagens.

A estrutura de pastas criada é pensada para integração com os outros módulos da ferramenta MOODIE, embora este módulo utilize apenas algumas delas:

```
/seu diretório
├── datasets  ---> Local onde os datasets em csv serão salvos
├── reports  ---> Local onde os relatórios e documentações dos processamentos serão salvos
├── imagens  ---> Local onde as imagens baixadas ficam armazenadas
├── imagewall  ---> Local onde as visualizações de dados das imagewals ficam armazenadas (módulo trends)
├── paleta  ---> Local onde as paletas de cores extraídas do dataset serão salvas (módulo metadata e trends
```


### 04. Upload e análise do CSV (obrigatório)
Você fará upload de um CSV contendo os links das imagens. O MOODIE irá:
- Ler o arquivo e armazenar os dados em um DataFrame global;
- Exibir colunas e tipos de dados;
- Identificar valores ausentes;
- Analisar colunas com valores únicos;
- Sugerir possíveis chaves para identificação de duplicatas.

### 05. Remoção de duplicatas (opcional)
Com base na análise anterior, você pode optar por remover duplicatas usando colunas específicas. Tenha atenção:
- Só remova se tiver certeza que são duplicatas;
- Bases com memes ou imagens virais podem repetir conteúdo com propósito;
- Guarde sempre uma cópia do CSV original.

### 06. Sampleamento do dataset (opcional)
Você pode aplicar amostragem para:
- Reduzir o tamanho do dataset;
- Balancear categorias;
- Testar com subconjuntos antes de processar tudo.

### 07. Estratégias de nomeação de imagens
Antes de baixar, escolha como deseja nomear os arquivos:
- **Nome Original**: mantém o nome da imagem conforme o link (pode gerar conflitos se houver nomes repetidos);
- **HEX**: gera nomes únicos com identificadores hexadecimais;
- **pHash**: usa o hash perceptual da imagem, útil para identificar visualmente imagens parecidas.

### Após configurar, **basta seguir executando as células uma a uma**, sempre lendo as instruções e respondendo os widgets exibidos. O MOODIE cuidará do restante automaticamente.  Ao final da execução, será gerado um dataset `.csv` com os dados das imagens baixadas e você poderá acessar tudo em cada um dos subdiretórios criados conforme a árvore indicada acima.



## Sobre o Autor

**Elias Bitencourt** é Professor Adjunto no Curso de Design da Universidade do Estado da Bahia (UNEB), com Doutorado em Comunicação pela FACOM/UFBA e Mestrado em Cultura e Sociedade pelo IHAC/UFBA. Foi pesquisador visitante no Centro Milieux (Concordia University, Canadá) em 2019. Atualmente, coordena o **Datalab/Design (CNPq)** na UNEB, um centro de pesquisa em visualização de dados e metodologias digitais. Sua pesquisa investiga visualização de dados, estudos de plataformas, imaginários digitais e mediação algorítmica nas relações sociais. É pesquisador colaborador no **Inova Media Lab** (Universidade Nova de Lisboa) e na rede internacional **Public Data Lab**. [Mais em](https://eliasbitencourt.com)

---

## Status

O projeto encontra-se em **fase beta** e está em desenvolvimento contínuo. Contribuições, sugestões e colaborações são bem-vindas.

---

## Licença

Este repositório está sob uma **Licença de Uso Restrito com Atribuição**.  
O conteúdo pode ser utilizado para fins acadêmicos e não-comerciais com devida atribuição ao autor.  
Modificações ou redistribuição exigem permissão.  
Veja o arquivo [`LICENSE.txt`](./LICENSE.txt) para mais detalhes.

## Como citar moodie (formato APA):

Bitencourt, E. (2025). *MOODIE Image Trends: Modular Observational & Operational Design Image Explorer* (Versão beta) [Repositório GitHub]. Datalab/Design – Universidade do Estado da Bahia. https://github.com/datalabdesign/moodie
