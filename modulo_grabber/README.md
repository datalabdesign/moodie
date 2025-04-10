<img src="MOODIE_GRABBER.png" alt="Logo MOODIE" width="400"/>

# MOODIE

**MOODIE Vision Design** (Modular Observational & Operational Design Image Explorer) é uma ferramenta experimental em fase beta voltada à exploração visual e análise de imagens para fins pedagógicos e criativos. Organizado em três módulos independentes — *Grabber*, *Metadata* e *Trends* — o MOODIE foi desenvolvido para aproximar designers de métodos digitais e ferramentas computacionais, estimulando novas formas de pesquisa visual, mapeamento de tendências e montagem de moodboards.

Atualmente, apenas o módulo **Download** está disponível para uso. Os demais módulos encontram-se em desenvolvimento.

---

## ✦ Módulos da Ferramenta

### 1. Módulo `Image Grabber`
Permite baixar imagens a partir de uma coluna com links em um arquivo CSV. Entre as funcionalidades disponíveis:

- **Sampleamento estatístico** do dataset completo;
- **Remoção de duplicatas** visuais (via pHash);
- **Downsample ou upsample** de conjuntos desequilibrados;
- **Opções de nomeação** das imagens: nome original do link, hash pHash ou hash hexadecimal;
- **Extração de metadados básicos**, como:
  - EXIF (quando disponível),
  - Domínio do link,
  - Tamanho e formato da imagem;
- Geração de um **dataset com os metadados** coletados.

### 2. Módulo `metadata` (em desenvolvimento)
Permite análise e extração de metadados a partir de uma pasta ou arquivo `.zip` contendo imagens, sem a necessidade de um CSV. Funcionalidades previstas:

- Renomear imagens com hash hexadecimal ou pHash;
- Extrair metadados básicos (dimensões, formato, EXIF);
- Gerar paletas de cores dominantes e representativas para cada imagem, salvas como `.png`;
- Exportar um dataset com os dados extraídos, para uso posterior no módulo Trends.

### 3. Módulo `trends` (em desenvolvimento)
Recebe como entrada uma pasta de imagens e o dataset gerado por um dos módulos anteriores. Este módulo permitirá:

- Enriquecimento dos dados com:
  - Extração de cores,
  - Extração de *features* com diferentes modelos de visão computacional;
- Visualizações baseadas em:
  - Similaridade entre imagens,
  - Critérios definidos por relevância ou variáveis do dataset;
- Sistema de **recomendação visual**:
  - O usuário fornece uma imagem de referência (interna ou externa),
  - Define os critérios de recomendação (features, cor, tamanho, etc.),
  - Recebe uma *imagewall* com imagens similares, relatório de tendências e paletas sugeridas.

---

## Objetivo

MOODIE é uma ferramenta **pedagógica** criada para explorar a interseção entre design, imaginação computacional e métodos digitais. O projeto visa fornecer um ambiente de experimentação acessível a estudantes, pesquisadores e designers interessados em usar dados visuais como matéria-prima para investigações criativas e analíticas.

---

## Sobre o Autor

**Elias Bitencourt** é Professor Adjunto no Curso de Design da Universidade do Estado da Bahia (UNEB), com Doutorado em Comunicação pela FACOM/UFBA e Mestrado em Cultura e Sociedade pelo IHAC/UFBA. Foi pesquisador visitante no Centro Milieux (Concordia University, Canadá) em 2019.

Atualmente, coordena o **Datalab/Design (CNPq)** na UNEB, um centro de pesquisa em visualização de dados e metodologias digitais. Sua pesquisa investiga visualização de dados, estudos de plataformas, imaginários digitais e mediação algorítmica nas relações sociais. É pesquisador colaborador no **Inova Media Lab** (Universidade Nova de Lisboa) e na rede internacional **Public Data Lab**.

---

## Status

O projeto encontra-se em **fase beta** e está em desenvolvimento contínuo. Contribuições, sugestões e colaborações são bem-vindas.

---

## Licença

Este repositório está sob uma **Licença de Uso Restrito com Atribuição**.  
O conteúdo pode ser utilizado para fins acadêmicos e não-comerciais com devida atribuição ao autor.  
Modificações ou redistribuição exigem permissão.  
Veja o arquivo [`LICENSE.txt`](./LICENSE.txt) para mais detalhes.

## Como citar este repositório (formato APA):

Bitencourt, E. (2025). *MOODIE: Modular Observational & Operational Design Image Explorer* (Versão beta) [Repositório GitHub]. Datalab/Design – Universidade do Estado da Bahia. https://github.com/datalabdesign/moodie


