
# MOODIE Grabber (Módulo de Coleta de Imagens)

**MOODIE Grabber** é o módulo de coleta de imagens da ferramenta [MOODIE](https://github.com/datalabdesign/moodie), desenvolvido para permitir o download e pré-processamento de imagens a partir de um arquivo CSV com links. Ele está em fase **beta**, sendo o primeiro módulo funcional da ferramenta MOODIE.

Este módulo foi pensado como uma introdução prática ao uso de imagens em pipelines de pesquisa em design, oferecendo desde a coleta até os primeiros metadados estruturados que alimentam etapas posteriores de análise visual e exploração criativa.

---

## ✦ Funcionalidades

O MOODIE Grabber permite:

- Leitura de um **CSV contendo links de imagens** (campo configurável);
- **Sampleamento estatístico** do dataset completo:
  - Amostragem proporcional, aleatória, por classe ou por tamanho;
- **Remoção de duplicatas visuais** com base em `pHash` e distância de Hamming;
- **Download automático das imagens**, com:
  - Opção de nomeação: nome original, hash hexadecimal, ou hash perceptual;
- **Extração automática de metadados básicos**:
  - Tamanho do arquivo, dimensões, tipo e formato da imagem;
  - Informações EXIF (quando disponíveis);
  - Domínio de origem do link (útil para análise de proveniência);
- Geração de um **dataset estruturado** com os dados das imagens baixadas;
- Integração com os demais módulos da ferramenta MOODIE (Metadata e Trends).

---

## ✦ Requisitos

Este módulo é compatível com o Google Colab, sem necessidade de instalação local. As bibliotecas utilizadas são instaladas automaticamente no ambiente Colab:

- `pandas`
- `numpy`
- `PIL`
- `imagehash`
- `tqdm`
- `requests`
- `IPython.display`
- Bibliotecas padrão do Python (`os`, `shutil`, `pathlib`, etc.)

---

## ✦ Como usar

1. Prepare um arquivo `.csv` com pelo menos uma coluna contendo os links de imagens. Exemplo:

```
id,titulo,link_imagem
1,Imagem A,https://exemplo.com/img1.jpg
2,Imagem B,https://exemplo.com/img2.jpg
```

2. Defina os parâmetros de execução no início do notebook:
   - Caminho do arquivo CSV;
   - Nome da coluna com os links;
   - Caminho da pasta onde salvar as imagens.

3. Execute célula por célula no notebook, ajustando as opções de amostragem, nomeação e deduplicação conforme necessário.

4. Ao final da execução, será gerado um dataset `.csv` com os dados das imagens baixadas.

---

## ✦ Exemplo de uso

- Coletar 500 imagens balanceadas por categoria;
- Eliminar duplicatas com `pHash` (limiar: 6);
- Salvar imagens com hash hexadecimal como nome;
- Gerar dataset com colunas: `image_name`, `phash`, `link`, `domain`, `filesize`, `dimensions`, `EXIF`.

---

## ✦ Sobre a Ferramenta MOODIE

**MOODIE** (Modular Observational & Operational Design Image Explorer) é uma ferramenta composta por três módulos:

- **Grabber** (coleta e download)
- **Metadata** (extração e organização)
- **Trends** (exploração visual e recomendação)

O projeto foi idealizado como uma ferramenta **pedagógica**, para aproximar designers dos métodos digitais e da imaginação computacional aplicada ao design e às visualidades contemporâneas.

---

## ✦ Sobre o Autor

**Elias Bitencourt** é Professor Adjunto no Curso de Design da Universidade do Estado da Bahia (UNEB), com Doutorado em Comunicação pela FACOM/UFBA e Mestrado em Cultura e Sociedade pelo IHAC/UFBA. Foi pesquisador visitante no Centro Milieux (Canadá) em 2019. Coordena o Datalab/Design (CNPq) na UNEB, com pesquisas em visualização de dados, plataformas digitais, imaginários computacionais e mediação algorítmica.

---

## ✦ Licença

Este módulo está sob uma **Licença de Uso Restrito com Atribuição**.

- O conteúdo pode ser utilizado para fins acadêmicos e não-comerciais, com devida **atribuição ao autor**.
- **Modificações e redistribuições não são permitidas sem autorização expressa**.
- Para mais detalhes, veja [`LICENSE.txt`](../LICENSE.txt).

---

## ✦ Como citar (formato APA)

Bitencourt, E. (2025). *MOODIE Grabber: módulo de coleta de imagens da ferramenta MOODIE* (Versão beta) [Notebook Jupyter]. Datalab/Design – Universidade do Estado da Bahia. https://github.com/datalabdesign/moodie
