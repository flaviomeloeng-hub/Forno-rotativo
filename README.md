# Unidade Sacramento — Forno Rotativo de Chamote (Togni)

Simulador interativo 3D de um forno rotativo de **chamote** (calcinação de argila/bauxita refratária),
produzido na unidade **Sacramento** e usado em **Poços de Caldas**. Grades simuladas: **45%, 70% e 90% de Al₂O₃**.

Arquivos:
- **`unidade-sacramento.html`** — sistema completo da Unidade Sacramento: planta inteira (alimentação,
  forno, resfriador, elevador + baias) e os módulos de **Produção** e **Manutenção** (IA de regras sobre
  o modelo físico: OEE, refugo previsto, desgaste de roletes/alianças, fadiga térmica do refratário,
  manutenção preventiva e recomendações de ajuste do passeio do forno).
- **`forno-rotativo-sacramento.html`** — modelo isolado, apenas do forno rotativo (sem os demais setores
  da planta), para uso pontual/focado no equipamento.

Ambos são HTML único, Three.js r128 via CDN, sem etapa de build.

---

## Como abrir / testar

- **Mais simples:** duplo clique no `.html` (abre no navegador). Precisa de internet só para baixar a biblioteca 3D.
- **Recomendado p/ desenvolvimento (preview ao vivo):** rodar um servidor local na pasta:
  ```bash
  npx serve .        # ou: python -m http.server 8000
  ```
  e abrir `http://localhost:8000/unidade-sacramento.html`.
- **Deploy:** arrastar a pasta no Netlify (mesmo fluxo do dashboard PCM).

---

## Controles (painel lateral)

| Grupo | Campo | O que faz |
|---|---|---|
| Material | Grade 45 / 70 / 90% Al₂O₃ | Define janela de queima, ângulo de repouso, densidade e cor |
| Processo | Temperatura da zona de queima | Pico térmico no material e intensidade da chama |
| Processo | Inclinação | Afeta o tempo de residência (τ) |
| Processo | Rotação (rpm) | Afeta τ e a velocidade do leito |
| Processo | Exaustor de tiragem (%) | Eficiência de combustão/transferência e temp. de exaustão |
| Processo | Vazão de alimentação (t/h) | Grau de enchimento e produção |
| Geometria | Comprimento / Ø externo | Geometria do casco (reconstrói a cena) |
| Estrutura | Ø da pista (aliança) | Diâmetro do anel de rolamento |
| Estrutura | Ø do rolete | Diâmetro dos roletes de apoio |
| Estrutura | Comprimento do rolete | Largura de face do rolete / eixo |
| Visualização | Perspectiva / Lado / Frente / Topo | Presets de câmera |
| Visualização | Corte, Material, Chama, Estrutura, Rótulos, Sombras, Girar | Toggles |

**Estrutura mecânica modelada:** 3 bases (alianças) → 2 roletes por base → 2 mancais por rolete (com eixo),
coroa dentada + pinhão + motor, exaustor de tiragem, queimador e capelas.

---

## Modelo de engenharia (e suas limitações)

- **Tempo de residência (τ):** equação de **Sullivan, Maier & Ralston (1927)**
  `τ[min] = 1,77 · L · √φ / (D · N · S)` — φ = ângulo de repouso (°), S = inclinação (°), N = rpm, D, L em m.
- **Velocidade axial:** `L / τ`.
- **Qualidade:** produto de um índice térmico (pico vs janela da grade) por um índice de tempo (τ vs τ mínimo).
- **Enchimento:** vazão volumétrica × τ / volume interno.

> ⚠️ **As janelas de queima, ângulos de repouso e tempos mínimos por grade são valores INDICATIVOS.**
> Devem ser calibrados com os dados reais de processo da Togni para que o índice de qualidade seja fiel.
> Os parâmetros estão centralizados no objeto `GR` no início do `<script>`.

---

## Estrutura do código (`<script>`)

1. Parâmetros e dados (`P`, `GR`, rampa de calor)
2. Modelo de engenharia (`residence`, `quality`, `fillPct`, etc.)
3. Renderer / cena / luzes (tonemapping ACES, ambiente procedural p/ reflexos)
4. Construção (`buildAll`, `makeLabel`, leito, chama, estrutura, capelas)
5. Câmera (órbita própria + presets, touch/pinch)
6. Estado / toggles (`applyClip`, `applyToggles`)
7. Painel de análise + perfil de temperatura (canvas 2D)
8. Log / lote rastreável
9. Loop de animação
10. Wiring da UI
11. Início

---

## Ideias de evolução

- Calibrar `GR` com dados reais (3 grades) e validar τ com tempos medidos em planta.
- Painel de calibração editável (sem mexer no código).
- Identidade visual Togni (cores, logo, fontes).
- Exportar relatório PDF do cenário simulado.
- Separar em módulos (`engine.js`, `scene.js`, `ui.js`) ao migrar para bundler.
