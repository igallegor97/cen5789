# **Relatório Completo: Análise Transcriptômica de *Citrus sinensis***

## **1. Introdução**  
Este relatório descreve um pipeline completo para análise de expressão diferencial de genes (DEGs) em *Citrus sinensis*, comparando condições experimentais (IN vs NI). O script utiliza dados de RNA-seq processados pelo *Salmon* e análises estatísticas com *DESeq2*.

---

## **2. Fluxo do Script**  
O processo divide-se em 9 etapas principais:  

1. **Preparação**: Carrega bibliotecas e arquivos necessários.  
2. **Metadados**: Organiza informações das amostras.  
3. **Importação**: Lê dados de expressão do *Salmon*.  
4. **Análise DEG**: Identifica genes diferencialmente expressos com *DESeq2*.  
5. **Pré-visualização**: Prepara dados para gráficos.  
6. **Volcano Plot**: Visualiza DEGs com significância estatística.  
7. **PCA**: Mostra similaridade entre amostras.  
8. **Heatmap**: Exibe padrões de expressão dos DEGs.  
9. **Exportação**: Salva resultados em arquivos.  

---

## **3. Explicação Detalhada**  

### **3.1. Preparação (`library()`)**
- **O que faz?** Carrega as ferramentas (bibliotecas R) necessárias.  
- **Por quê?** Cada biblioteca tem funções específicas:  
  - `tximport`: Converte dados do *Salmon* para contagens genéticas.  
  - `DESeq2`: Realiza análise estatística de expressão diferencial.  
  - `ggplot2`: Cria gráficos profissionais.  

### **3.2. Metadados (`samples`)**
- **O que faz?** Lê um arquivo (TSV) com informações das amostras (ex.: saúde da planta, tratamento).  
- **Por quê?** Esses dados são essenciais para agrupar amostras corretamente (ex.: "IN" = infectada, "NI" = saudável).  

### **3.3. Importação de Contagens (`tximport`)**
- **O que faz?**  
  - Localiza arquivos do *Salmon* (ferramenta que quantifica expressão gênica).  
  - Converte contagens de transcritos para níveis genéticos usando `tx2gene`.  
- **Por quê?** Genes podem ter múltiplos transcritos; essa etapa unifica os dados.  

### **3.4. Análise com DESeq2**
- **O que faz?**  
  1. Filtra genes pouco expressos (<10 contagens no total).  
  2. Normaliza os dados para comparar amostras.  
  3. Testa diferenças entre grupos ("IN" vs "NI").  
- **Por quê?**  
  - Genes com baixa expressão podem gerar falsos positivos.  
  - *DESeq2* ajusta para diferenças técnicas entre amostras (ex.: profundidade de sequenciamento).  

### **3.5. Volcano Plot**
- **O que faz?** Gráfico que combina:  
  - **Eixo X**: Magnitude da diferença (log2 *fold change*).  
  - **Eixo Y**: Significância estatística (-log10 *p-value* ajustado).  
  - **Cores**: Genes sobreexpressos (vermelho), subexpressos (azul), não significativos (cinza).  
- **Por quê?**  
  - Identifica visualmente os DEGs mais relevantes.  
  - Limites: `|log2FC| > 1` e `padj < 0.05` (controle de falsos positivos).  

### **3.6. PCA (Análise de Componentes Principais)**
- **O que faz?** Gráfico 2D que mostra similaridade entre amostras.  
- **Por quê?**  
  - Verifica se amostras do mesmo grupo se agrupam.  
  - Detecta *outliers* ou problemas técnicos.  

### **3.7. Heatmap**
- **O que faz?** Mapa de calor com padrões de expressão dos DEGs.  
- **Por quê?**  
  - Agrupa genes com perfis similares (possíveis vias biológicas em comum).  
  - Cores: Vermelho (alta expressão), azul (baixa expressão).  

### **3.8. Exportação**
- **O que faz?** Salva:  
  - **Lista completa de genes** (`DEG_results_all.csv`).  
  - **Genes significativos** (`DEG_results_significant.csv`).  
  - **IDs para análise funcional** (`deg_gene_ids.txt`).  

---

## **4. Considerações Finais**  
- **Robustez**: O script inclui filtros para evitar artefatos técnicos (ex.: genes pouco expressos).  
- **Adaptabilidade**: Limites de gráficos ajustam-se automaticamente aos dados.  
- **Reprodutibilidade**: Todos os passos são documentados para replicação futura.    

--- 

# **Arquivos Necessários e Escolha de Parâmetros Estatísticos**

## **5. Arquivos Necessários para Interpretação dos Resultados**

Para executar este script corretamente e interpretar os resultados, são precissos os seguintes arquivos:

### **5.1. Arquivos de Entrada**
| Arquivo               | Descrição | Formato | Por que é importante? |
|-----------------------|-----------|---------|-----------------------|
| **`tx2gene.txt`**     | Tabela que relaciona transcritos (isoformas) a genes. | TSV (2 colunas: `TXNAME`, `GENEID`) | Permite somar as contagens de todos os transcritos de um mesmo gene. |
| **`metadata_citrus.tsv`** | Metadados das amostras (ex.: condição experimental, lote, etc.). | TSV (colunas: `SampleName`, `SampleHealthState`, etc.) | Essencial para agrupar as amostras corretamente no DESeq2. |
| **Arquivos `.sf` do Salmon** | Resultados do Salmon (quantificação de expressão gênica). | `.sf` (gerados pelo Salmon) | Contêm as contagens brutas de expressão para cada transcrito/amostra. |

### **5.2. Arquivos de Saída Gerados**
| Arquivo                         | Descrição | Como interpretar? |
|---------------------------------|-----------|-------------------|
| **`DEG_results_all.csv`**       | Lista completa de genes com estatísticas. | - `log2FoldChange`: Diferença de expressão (valores positivos = maior em IN; negativos = maior em NI). <br> - `padj`: *p-value* ajustado (significativo se < 0.05). |
| **`DEG_results_significant.csv`** | Genes diferencialmente expressos (DEGs). | Filtrado por `padj < 0.05` e `|log2FC| > 1`. Genes aqui são candidatos prioritários para validação. |
| **`deg_gene_ids.txt`**          | IDs dos DEGs (um por linha). | Pode ser usado em ferramentas de enriquecimento funcional (ex.: GO, KEGG). |
| **`volcano_plot.png`**          | Gráfico de significância vs magnitude de mudança. | Pontos vermelhos/azuis = DEGs; cinza = não significativos. |
| **`PCA_optimized.png`**         | PCA mostrando similaridade entre amostras. | Amostras do mesmo grupo devem agrupar-se. |
| **`heatmap_all_DEGs.png`**      | Padrões de expressão dos DEGs. | Cores: vermelho (alta expressão), azul (baixa). |

---

## **6. Explicação dos Parâmetros Estatísticos Escolhidos**

### **6.1. Limiar de Significância (`padj < 0.05`)**
- **O que é?** O *p-value* ajustado (`padj`) controla falsos positivos em testes múltiplos.  
- **Por que 0.05?**  
  - Padrão na literatura para equilibrar rigor estatístico e sensibilidade.  
  - Equivale a um risco de 5% de falsos positivos.  

### **6.2. Limiar de *Fold Change* (`|log2FC| > 1`)**
- **O que é?** Mede a magnitude da diferença de expressão (em escala log2).  
- **Por que 1?**  
  - Corresponde a uma mudança de **2x** (pois `2^1 = 2`).  
  - Filtra mudanças biologicamente relevantes (evitando genes com diferenças pequenas).  

### **6.3. Filtro de Genes Pouco Expressos (`rowSums(counts) >= 10`)**
- **O que faz?** Remove genes com menos de 10 contagens no total.  
- **Por quê?**  
  - Genes com baixa expressão têm estimativas menos confiáveis.  
  - Reduz ruído estatístico no DESeq2.  

### **6.4. Transformação VST (`vst(dds, blind=FALSE)`)**
- **O que faz?** Estabiliza a variância dos dados para PCA e heatmap.  
- **Por que não usar contagens brutas?**  
  - Dados de RNA-seq têm variância que depende da média (ex.: genes altamente expressos têm maior variância).  
  - A transformação corrige isso para análises exploratórias.  

### **6.5. Número de Genes no Heatmap (`ntop = 500`)**
- **O que faz?** Limita o heatmap aos 500 genes com maior variânça.  
- **Por quê?**  
  - Melhora a visualização (evita sobrecarga com milhares de genes).  
  - Foca nos genes com maiores diferenças entre grupos.  

---

## **7. Exemplo Prático: Como Interpretar um Resultado**
Suponha que no arquivo `DEG_results_significant.csv` você encontre:  

| gene       | log2FoldChange | padj   | significance |  
|------------|----------------|--------|--------------|  
| `Cs4g12345`| 3.2            | 0.001  | up           |  
| `Cs2g67890`| -2.1           | 0.02   | down         |  

- **`Cs4g12345`**:  
  - **Sobreexpresso em IN** (`log2FC = 3.2` → `2^3.2 ≈ 9.5x` maior em IN).  
  - **Altamente significativo** (`padj = 0.001`).  
- **`Cs2g67890`**:  
  - **Subexpresso em IN** (`log2FC = -2.1` → `2^-2.1 ≈ 0.23x` ou 4.3x menor em IN).  

---

## **8. Dicas para Análises Futuras**  
1. **Enriquecimento Funcional**: Use `deg_gene_ids.txt` para fazer análise de enriquecimento funcional e termos GO.  
2. **Ajuste de Parâmetros**: Se houver muitos/poucos DEGs, teste `padj < 0.01` ou `|log2FC| > 0.5`.  

**Anexos**:  
- Código completo (disponível no script R).  
- Exemplos de gráficos gerados (`volcano_plot.png`, `PCA.png`, `heatmap.png`).  

---  
