# Análise da Antena IFA — AN928.2 EFR32 Series 2 Layout Design Guide

## Fonte

Documento: `an928.2-efr32-series2-layout-design-guide.pdf` (Rev. 1.7, julho 2025)

---

## Tipo de Antena por Banda de Frequência

O documento define duas abordagens conforme a faixa de operação do chip:

| Banda       | Chips                              | Tipo de Antena               |
|-------------|-------------------------------------|------------------------------|
| 2,4 GHz     | EFR32xG21, xG22, xG24, xG26, xG27 | IFA on-board (50 Ω)          |
| Sub-GHz     | EFR32xG23, xG25, xG28              | Conector SMA / U.FL externo  |

> O EFR32FG23 (chip deste projeto, 902–930 MHz) utiliza **conector SMA externo** no board de referência BRD4210A. Uma IFA sub-GHz on-PCB seria inviável por tamanho (λ/4 @ 915 MHz ≈ 82 mm).

---

## Dimensão Explícita da IFA no Documento

A única dimensão de antena escrita em texto/esquemático em todo o documento é encontrada na **Figura 2.18** (página 13):

| Parâmetro                          | Valor      |
|------------------------------------|------------|
| Comprimento total do elemento (ANT1) | **19,5 mm** |
| Board de referência                | BRD4118A   |
| Chip                               | EFR32xG26  |
| Frequência                         | 2,4 GHz    |
| Impedância                         | 50 Ω       |

A anotação aparece diretamente no componente `ANT1` do esquemático:

```
ANT1  ←  símbolo de antena
19.5mm  ←  atributo de tamanho do footprint
```

---

## Por que 19,5 mm?

Para 2,4 GHz:

```
λ = c / f = 3×10⁸ / 2,45×10⁹ ≈ 122 mm
λ/4 ≈ 30,5 mm   (monopolo de referência)
```

A IFA opera com **19,5 mm ≈ λ/6** porque:
- O elemento dobrado em "F" redistribui a corrente, reduzindo o comprimento físico necessário
- O plano de terra integrado no PCB ajusta a ressonância
- O curto-circuito (shunt arm) desloca o ponto de alimentação para 50 Ω sem rede de casamento extra

---

## Estrutura da IFA (Inverted-F Antenna)

Visível nos layouts dos boards de referência (ex: BRD4180A, Figura 3.1):

```
    ┌──────────────────────────────┐  ← elemento horizontal radiante
    │                              │
    │feed                          │
    ┤←── alimentação (50R_IFA)     │
    │                              │
    ┤←── curto-circuito (GND)      │
    │                              │
══════════════════════════════════════  ← plano de terra (GND)
```

Componentes em série na alimentação (conforme esquemático):

- **L3** (0 Ω padrão) — opcional, para supressão de harmônicos
- **R1 / R3** (0 Ω padrão) — elementos opcionais de ajuste de antena (AT1)
- **P2 / J1** — conector U.FL para medição conduzida paralela

---

## Referência para Projeto Sub-GHz (915 MHz)

Para adaptar a IFA ao projeto EFR32FG23 @ 915 MHz, escalonar proporcionalmente:

```
Fator de escala = 2450 MHz / 915 MHz ≈ 2,68

Comprimento estimado = 19,5 mm × 2,68 ≈ 52 mm
```

> Este valor é uma estimativa inicial. O comprimento real depende do substrato (εr), largura da trilha, distância ao plano de terra e geometria específica da IFA. Recomenda-se simulação EM (ex: HFSS, Sonnet) ou uso da referência AN923.2 para o casamento sub-GHz.

---

## Documentos Relacionados

| Documento  | Conteúdo                                             |
|------------|------------------------------------------------------|
| AN923.2    | Matching Guide sub-GHz EFR32 Series 2                |
| AN930.2    | Matching Guide 2,4 GHz EFR32 Series 2                |
| BRD4210A   | Reference design EFR32xG23 (schematic + layout)      |
| BRD4180A   | Reference design EFR32xG21 com IFA 2,4 GHz           |
| BRD4118A   | Reference design EFR32xG26 com IFA 19,5 mm           |
