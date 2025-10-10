# 🌱 Dashboard de Análise do Cadastro Ambiental Rural (CAR) — Regional de Tupã/SP

## 📘 Descrição do Projeto
Este projeto é uma **evolução do meu primeiro dashboard sobre o Cadastro Ambiental Rural (CAR)**, criado com o objetivo de aprimorar as análises e oferecer uma visão mais detalhada da situação ambiental dos imóveis rurais na **Regional de Tupã/SP**.

Os dados foram obtidos a partir do **Portal de Dados Abertos do Governo Federal**, respeitando as boas práticas de anonimização — foram **ocultados trechos dos números de registro do CAR** e **removidas as informações de latitude e longitude**, mantendo apenas a referência aos municípios.

---

## 🎯 Objetivo
O painel foi desenvolvido para:
- Compreender a **distribuição das áreas rurais** e seus usos;
- Analisar a **situação das Reservas Legais (RL)** e **Áreas de Preservação Permanente (APP)**;
- Identificar **passivos ambientais** e **níveis de conformidade** com a legislação;
- Apoiar **decisões de gestão ambiental** e **planejamento territorial**.

---

## 🧩Preparação e Tratamento dos Dados
Os dados utilizados neste projeto foram obtidos a partir do Portal de Dados Abertos do Governo Federal (dados.gov.br), que contém informações do Cadastro Ambiental Rural (CAR) de todos os municípios e estados brasileiros.
Para otimizar a análise e concentrar o estudo na Regional de Tupã (SP), foram desenvolvidos dois scripts em Python utilizando a biblioteca pandas:

  - Filtragem geográfica: Um script para extrair apenas os registros dos municípios de interesse e do estado de São Paulo, reduzindo significativamente o tamanho da base.
  - Padronização de formato numérico: Um segundo script foi criado para corrigir e padronizar as casas decimais das colunas de área (em hectares), garantindo consistência nos cálculos dentro do Power BI.

  Esses processos de pré-tratamento foram fundamentais para garantir a integridade e a performance da análise no Power BI.
---
## 🧭 Principais Análises Realizadas

### 🔹 Visão Geral
- Quantidade de CARs cadastrados;
- Área total (ha) e média da área dos imóveis;
- % de Vegetação Nativa (VGN);
- % de Pequenos e Grandes Imóveis;
- Gráficos de **área do imóvel x área consolidada por município** e **CARs por condição do cadastro**;
- **Mapa interativo** com a localização dos imóveis (municípios).

### 🔹 Área Consolidada
- Indicador da **% de área consolidada** e sua **classificação ambiental**:
  > 70% — Alta Consolidação (maior pressão ambiental)
  30–70% — Equilíbrio entre produção e preservação
  < 30% — Baixa Consolidação (maior conformidade ambiental)
- Gráfico de rosca com a % por classificação.

### 🔹 Reserva Legal (RL)
- Indicadores de % de RL Aprovada, Não Averbada e Proposta;
- Gráficos por módulo fiscal (MF) e por município;
- Integração com **situação ambiental** via *Tooltips*.

### 🔹 Passivo Ambiental
- Indicadores de **Área a Recompor** e **Área a Compensar**;
- Gráficos de pizza por Módulo Fiscal e **comparação por município**;
- Matriz detalhada com situação ambiental e número do CAR (parcialmente mascarado).

### 🔹 Situação Ambiental
- Cards: RL Ideal e RL Total;
- Gráficos com **Taxa Indicativa de Conformidade Ambiental** e **% APP por MF**.

### 🔹 PRA – Programa de Regularização Ambiental
- Quantidade de CARs e adesão ao PRA;
- Gráficos por município e por ano.

### 🔹 Análises Extras
- Principais influenciadores: RL Ideal, Total RL, Conformidade, Tipo de Imóvel, Municípios e APP;
- Gráfico de dispersão: municípios (eixo X) x área de APP (eixo Y) com tamanho da bolha proporcional à área do imóvel.

### 🔹 Ranking de Propriedades Rurais
- Ranking das maiores propriedades com medida DAX `RANKX`;
- Indicadores interativos com imagens ilustrativas (sem identificação real) das 3 maiores propriedades.

---

## ⚙️ Principais Medidas DAX

```DAX
Area a Recompor = 
VAR Exigida = [RL Ideal]
VAR Declarada = [Total RL]
RETURN
IF(Declarada < Exigida; Exigida - Declarada; 0)

Area a Compensar = 
VAR AreaRecompor = [Area a Recompor]
VAR AreaDisponivel = [Remanescente de VGN]
RETURN
IF(AreaRecompor > AreaDisponivel; AreaRecompor - AreaDisponivel; 0)

Situação Ambiental = 
SWITCH(
    TRUE();
    [Area a Recompor] > 0 || [Area a Compensar] > 0; "Com Passivo";
    [Area a Recompor] < 0 || [Area a Compensar] < 0; "Com Ativo Ambiental";
    "Em Conformidade"
)

Situacao_Conformidade = 
SWITCH(
    TRUE();
    'Temas Ambientais'[modulos_fiscais] <= 4; "Até 4 MF - Regra Especial";
    'Temas Ambientais'[modulos_fiscais] > 4 && 'Temas Ambientais'[solicitacao_adesao_pra] = "SIM"; "Acima de 4 MF - Conformidade via PRA";
    'Temas Ambientais'[modulos_fiscais] > 4 && 'Temas Ambientais'[solicitacao_adesao_pra] <> "SIM"; "Acima de 4 MF - Precisa Regularização";
    BLANK()
)

Taxa de Indicativa de CA = 
SUMX(
    'Temas Ambientais';
    [area_remanescente_vegetacao_nativa] +
    'Temas Ambientais'[area_reserva_legal_averbada] +
    'Temas Ambientais'[area_preservacao_permanente]
) / [Soma Area por Imovel]
```

---

## 📸 Dashboard

Abaixo estão algumas prévias do dashboard desenvolvido:

<p align="center">
  <img src="https://github.com/LuucasGabriel/CAR_Cadastro_Ambiental_Rural/blob/main/IMG/Introdu%C3%A7%C3%A3o.png?raw=true" width="800"/>
</p>

<p align="center">
  <img src="https://github.com/LuucasGabriel/CAR_Cadastro_Ambiental_Rural/blob/main/IMG/Vis%C3%A3o%20Geral.png?raw=true" width="800"/>
</p>

<p align="center">
  <img src="https://github.com/LuucasGabriel/CAR_Cadastro_Ambiental_Rural/blob/main/IMG/Vis%C3%A3o%20Geral(Detalhamento).png?raw=true" width="800"/>
</p>

  
<p align="center">
  <img src="https://github.com/LuucasGabriel/CAR_Cadastro_Ambiental_Rural/blob/main/IMG/Tool_tip(%C3%81rea%20Consolidada).png?raw=true" width="800"/>
</p>


<p align="center">
  <img src="https://github.com/LuucasGabriel/CAR_Cadastro_Ambiental_Rural/blob/main/IMG/Reserva%20Legal.png?raw=true" width="800"/>
</p>

<p align="center">
  <img src="https://github.com/LuucasGabriel/CAR_Cadastro_Ambiental_Rural/blob/main/IMG/Passivo%20Ambiental.png?raw=true" width="800"/>
</p>

<p align="center">
  <img src="https://github.com/LuucasGabriel/CAR_Cadastro_Ambiental_Rural/blob/main/IMG/Tool_tip(Situa%C3%A7%C3%A3o%20Ambiental).png?raw=true" width="800"/>
</p>

<p align="center">
  <img src="https://github.com/LuucasGabriel/CAR_Cadastro_Ambiental_Rural/blob/main/IMG/PRA%20-%20Programa%20de%20Regulariza%C3%A7%C3%A3o%20Ambiental.png?raw=true" width="800"/>
</p>

<p align="center">
  <img src="https://github.com/LuucasGabriel/CAR_Cadastro_Ambiental_Rural/blob/main/IMG/An%C3%A1lises%20Extras.png?raw=true" width="800"/>
</p>

<p align="center">
  <img src="https://github.com/LuucasGabriel/CAR_Cadastro_Ambiental_Rural/blob/main/IMG/Rank%20de%20Propriedades%20Rurais.png?raw=true" width="800"/>
</p>

---

## 🧩 Regras de Negócio Explicadas

- **Situação Ambiental:** indica se o imóvel possui *passivo* (área a recompor ou compensar), *ativo ambiental* (área excedente de vegetação nativa) ou está *em conformidade*.
- **Módulos Fiscais:** definem a regra de conformidade — até 4 MF segue regime especial; acima de 4 MF precisa estar regularizado via PRA.
- **Taxa Indicativa de Conformidade Ambiental:** representa a proporção da vegetação nativa e áreas protegidas em relação à área total do imóvel.
- **Área Consolidada:** corresponde à parte do imóvel ocupada por uso agropecuário antes de 22/07/2008, conforme o Código Florestal.

---

## 🧠 Aprendizados
Durante o desenvolvimento, aprofundei meu entendimento sobre:
- O funcionamento do **CAR e suas regras ambientais**;
- As métricas de conformidade e passivo ambiental;
- O uso de **DAX avançado** no Power BI para análise ambiental.

Este projeto contou também com o **apoio de um colega de trabalho**, que contribuiu com explicações sobre as regras de negócio e aspectos legais do CAR.

---

## 🛠️ Tecnologias Utilizadas
- **Power BI** (modelagem, DAX e visualizações)
- **Portal de Dados Abertos do Governo Federal**
- **SQL** (BigQuery)
- **GitHub** (versionamento e documentação)

---

## 🔒 Aviso de Privacidade
Todos os dados utilizados são **públicos**, porém os **números de registro CAR foram parcialmente mascarados** e **as coordenadas geográficas (latitude/longitude) removidas**, garantindo a **não identificação individual dos imóveis**.

---

## 👨‍💻 Autor
**Lucas Gabriel**  
💼 Profissional de Análise de Dados e Power BI  
📍 Regional de Tupã/SP  
📧 [Insira seu e-mail ou LinkedIn aqui]
