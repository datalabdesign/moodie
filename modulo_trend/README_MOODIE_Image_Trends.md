
<p align="center">
  <img src="MOODIE_TRENDS.png" alt="Logo MOODIE Image Trends" width="320"/>
</p>

# MOODIE Image Trends &nbsp;·&nbsp; guia passo‑a‑passo
> **Uma trilha de aprendizado visual para quem ainda não programa.**

MOODIE Image Trends é um _notebook_ que transforma **um monte de imagens soltas** num
conjunto de **painéis, paletas e recomendações** prontas para análise de tendências.

Ele foi pensado para disciplinas de **Pesquisa Visual**, **Design de Moda**,
**UX Research** e **Semiótica** onde o aluno quer experimentar IA **sem escrever código**.

---

## 🌱 0. O que você precisa

* uma pasta `imagens/` com suas fotos (.jpg/.png).  
* um arquivo CSV com **pelo menos** o nome de cada arquivo de imagem  
  (pode incluir colunas de descrição, autor, likes, estação etc.).  
* Conta Google → abra o notebook no **Google Colab** e rode célula por célula.

---

## 🔍 1. Amostragem do Dataset  
<sub>_arquivo `sample_dataset.py`_</sub>

| O que faz | Por quê isso importa | Resultado salvo |
|-----------|---------------------|-----------------|
|Selecionar **amostra aleatória** ou **balanceada**|Evitar que uma categoria gigante esconda as menores|`datasets/dataset_sampled_*.csv`|
|Fixar “seed”|Para que toda a turma reproduza o mesmo experimento|—|

---

## 🧠 2. Extração de “impressões digitais” (embeddings)  
<sub>_arquivo `extract_features.py`_</sub>

Escolha **um dos modelos**👇 (já vêm prontos do ImageNet):

| Coluna criada | Como é o modelo | Bom para… |
|---------------|-----------------|-----------|
|`VGG16_features`|15 milhões de parâmetros, filtros 3 × 3 empilhados|Padrões de textura, tecidos, moodboards|
|`ResNet50_features`|“atalhos” que evitam perda de informação|Formas complexas, objetos de produto|
|`MobileNetV2_features`|Modelo _light_ para celular|Testes rápidos, datasets enormes|
|`InceptionV3_features`|Vários tamanhos de filtro ao mesmo tempo|Imagens muito detalhadas ou confusas|

*👉 Saída:* `dataset_with_features.csv`

---

## 🧹 3. Remoção de Imagens Duplicadas  
<sub>_arquivo `deduplicate_visual.py`_</sub>

1. **Hash exato (MD5)** – arquivos idênticos.  
2. **pHash** – mesmas imagens com tamanho diferente, bordas, _prints_.  
3. **Embeddings** – fotos quase iguais tiradas em sequência.

**Depois da limpeza** recebemos:

```
imagens/duplicatas/                  ← cópia das repetidas
imagewall/CORPUS_NO_DUPLICATE.jpg   ← mosaico limpo
datasets/dataset_no_duplicates.csv
```

---

## 🎨 4. Mapas de Cor & Paletas

| Etapa | Explicação simples |
|-------|-------------------|
|Encontrar **cor dominante**|A maior mancha de cor da foto (via _ColorThief_)|
|Agrupar cores em 2‑D|Convertemos para o espaço de cor CIELAB e usamos **UMAP** para ter “semelhança perceptual”|
|Criar **células**|Cada grupo vira um quadrado proporcional à quantidade de fotos naquela cor|
|Gerar **paletas temáticas**|Harmonias automática : dominante + contraste + apoio + brilho|
|Checar **acessibilidade**|Ajustamos as cores para terem contraste mínimo (WCAG 4.5)|
|Simular **daltonismo**|Protano, deutero e tritano – para ver se a paleta ainda comunica|

*Arquivos‑chave*  
`dashboards/dashboard_dom_topN.png` (dominantes)  
`dashboards/dashboard_rep_topN.png` (representativas)  
`paleta/paletas_tematicas_*.png`

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
