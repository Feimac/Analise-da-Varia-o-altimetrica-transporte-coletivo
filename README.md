# Análise da Variação Altimétrica das Rotas do Transporte Coletivo de Curitiba

Este repositório reúne o código‑fonte, os dados e o artigo associados ao estudo que investiga como as variações topográficas influenciam o planejamento, o desempenho energético e a acessibilidade da Rede Integrada de Transporte (RIT) de Curitiba.

## Objetivo

Quantificar, em ambos os sentidos de viagem, os ganhos e perdas de elevação ao longo das **254 linhas de ônibus** da cidade, gerando para cada rota um perfil altimétrico detalhado e métricas de desnível acumulado. As informações resultantes subsidiam análises de:

* impacto da topografia sobre consumo de combustível e emissões;
* viabilidade de implantação de frotas elétricas e BRT;
* adequação de infraestrutura de acessibilidade (rampas, abrigos) em trechos íngremes.

## Dados e Fontes

| Recurso                       | Fonte                                   | Observações                        |
| ----------------------------- | --------------------------------------- | ---------------------------------- |
| Polilinhas oficiais das rotas | API URBS (2024‑2025)                    | 254 linhas com geometria em WGS‑84 |
| Curvas de nível de 1 m        | Modelo Digital de Elevação (IBGE, 2020) | Cobertura completa de Curitiba     |
| Limites administrativos       | IPPUC / GeoCuritiba                     | Para recorte territorial           |
| Imagens de referência         | Orthofotos 2022 (SRC Paraná)            | Validação visual dos perfis        |

Todos os dados são reprojetados para **SIRGAS 2000 / UTM 23S (EPSG:31982)** para garantir coerência métrica.

## Metodologia

1. **Pré‑processamento**

   * Eliminação de vértices redundantes e simplificação controlada das rotas para 15 m de tolerância.
   * Construção de STR‑tree (R‑tree) com \~140 k segmentos das curvas de nível para acelerar consultas espaciais.

2. **Estimativa de altitude via Inverse Distance Weighting (IDW)**

   * Para cada vértice da rota, localizam‑se as duas iso‑altitudes mais próximas em lados opostos da trajetória.
   * A altitude é calculada por ponderação inversa da distância aos segmentos identificados.

3. **Cálculo de desníveis**

   * Para cada par de vértices consecutivos obtém‑se Δh.
   * A soma dos Δh positivos e negativos fornece ganho e perda acumulados por sentido.

4. **Validação**

   * Amostragem randômica de 120 vértices comparada a valores medidos por GNSS de alta resolução.
   * Erro médio absoluto observado: **±0,85 m**.

## Resultados Principais

| Linha (sentido Centro → Terminal) | Ganho acumulado (m) | Perda acumulada (m) |
| --------------------------------- | ------------------- | ------------------- |
| Boqueirão (Ligeirão)              | +176                | −174                |
| Inter 2                           | +112                | −110                |
| Barreirinha                       | +58                 | −61                 |
| Fazendinha                        | +23                 | −22                 |

*Os resultados completos, incluindo mapas interativos e gráficos de perfis, estão disponíveis em `outputs/`.*

## Estrutura do Repositório

```
.
├── dados/                  # fontes brutas (não versionadas)
├── notebooks/
│   ├── preparacao.ipynb    # ETL dos dados
│   └── principal.ipynb     # algoritmo IDW + análises
├── outputs/                # figuras, mapas, CSVs gerados
├── LICENSE                 # Licença MIT
└── README.md               # este documento
```

## Licença

Distribuído sob **Licença MIT**. Consulte o arquivo `LICENSE` para detalhes.

## Autor e Contato

Felipe Snitynski Camillo – Departamento Acadêmico de Informática, UTFPR
Email: [felipesnitynski@alunos.utfpr.br](mailto:felipesnitynski@alunos.utfpr.br)

## Referências

Camillo, F. S. (2025). *Análise da Variação Altimétrica das Rotas do Transporte Coletivo de Curitiba e Suas Implicações*. XXIX Simpósio Brasileiro de Sistemas de Informação.
