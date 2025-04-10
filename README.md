<img src="logo_lab_orange.png" alt="Logo MOODIE" width="200"/>

# MOODIE

O MOODIE é uma ferramenta desenvolvida especialmente para designers, com um foco pedagógico e prático para aproximá-los dos métodos digitais e das ferramentas computacionais. Inspirado na ideia de um moodboard e pensado de forma lúdica, o MOODIE busca facilitar o trabalho com imagens e dados extraídos da web, permitindo que designers experimentem, aprendam e apliquem técnicas digitais em seus projetos.

## Visão Geral

O MOODIE foi concebido para integrar e simplificar fluxos de trabalho que envolvam a coleta, o processamento e a análise de imagens. Atualmente, o MOODIE é composto por módulos independentes que podem ser utilizados tanto de forma isolada quanto em conjunto, possibilitando uma abordagem modular para atender diversas necessidades:

- **Módulo Download**: Automatiza o download de imagens a partir de links presentes em datasets (CSV), com opções avançadas de renomeação, extração de metadados e atualização do dataset original.
- **Módulo de Imagens**: (Em desenvolvimento ou implementação conforme a evolução do projeto) Destinado à manipulação, tratamento e análise visual das imagens baixadas.
- **Outros Módulos**: O projeto é pensado para expansão, permitindo a integração com novas funcionalidades que venham a complementar ou ampliar as capacidades do MOODIE.

## Motivação e Contexto

Inicialmente criado para uso pedagógico, o MOODIE foi concebido com o intuito de aproximar designers de práticas computacionais aplicadas, facilitando a experimentação e o aprendizado. Ao lidar com processos automatizados, como download e processamento de imagens, o MOODIE serve como um ponto de partida para que profissionais criativos possam explorar e se apropriar de métodos digitais, integrando-os de maneira intuitiva aos seus fluxos de trabalho.

## Funcionalidades

- **Integração com Webscraping**: Processa datasets extraídos de ferramentas de webscraping, permitindo o download automático de imagens.
- **Renomeação Inteligente**: Oferece diferentes estratégias para nomear as imagens baixadas (utilizando o nome original, um hash hexadecimal ou o pHash da imagem), evitando duplicações e facilitando a organização.
- **Extração de Metadados**: Coleta informações relevantes de cada imagem, como dimensões, tamanho do arquivo e metadados EXIF, enriquecendo o dataset.
- **Atualização de Datasets**: Permite a criação ou atualização do CSV original com as informações extraídas e os nomes das imagens baixadas.
- **Geração de Relatórios**: Cria relatórios detalhados sobre o processo de download, auxiliando na verificação de erros e inconsistências.

## Requisitos

- **Python 3.6+**
- **Bibliotecas Python**: `pandas`, `requests`, `Pillow`, `tqdm`, `ipywidgets`, `imagehash`, entre outras.
- **Ambiente Recomendo**: Google Colab ou outra interface interativa para notebooks Jupyter.

## Instalação e Execução

1. Clone o repositório:
   ```bash
   git clone https://github.com/seu_usuario/moodie.git
