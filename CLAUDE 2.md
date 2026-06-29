# CLAUDE.md — Unidade Sacramento / Forno Rotativo de Chamote 3D

Contexto que o Claude Code deve carregar a cada sessão. Mantê-lo curto e verificável.

## O que é
Simulador 3D interativo de um forno rotativo de **chamote** (Togni — Sacramento/Poços de Caldas),
grades **45/70/90% Al₂O₃**. Stack: **Three.js r128** via CDN, sem build. Dois arquivos:
- `unidade-sacramento.html` — sistema completo (planta inteira + módulos de Produção e Manutenção/IA).
- `forno-rotativo-sacramento.html` — cópia derivada, restrita só ao setor do forno (sem alimentação/
  resfriador/elevador/baias). Ao alterar o arquivo completo, repassar manualmente as mudanças relevantes
  de engenharia/cálculo para esta cópia (ela compartilha o mesmo modelo, mas é mantida como arquivo separado).

## Como rodar / testar
- Não há etapa de build. Para preview: `npx serve .` e abrir o `.html` em `http://localhost:3000`.
- "Teste" = abrir no navegador e validar visualmente (não há suíte automatizada).
- Antes de finalizar uma mudança grande no `<script>`, validar a sintaxe: `node --check` no JS extraído.

## Convenções
- Comentários e textos de UI em **português (pt-BR)**.
- Manter **um único arquivo HTML** (CSS + JS inline) salvo se for explicitamente pedido para modularizar.
- **Não usar** `localStorage`/`sessionStorage` (o destino pode ser um artifact). Estado em memória (objeto `P`).
- Three.js r128: sem `OrbitControls` e sem `CapsuleGeometry`. A câmera usa controle de órbita próprio (já implementado).

## Invariantes que NÃO podem quebrar
- **Estrutura mecânica:** 3 bases (alianças) · 2 roletes por base · 2 mancais por rolete (com eixo). Os roletes giram.
- **Modelo de τ:** equação de Sullivan (1927) em `residence()`. Não trocar sem avisar.
- As janelas de queima por grade (`GR`) são **indicativas** — qualquer ajuste de valores reais deve ser sinalizado.
- O leito de material usa `InstancedMesh` (nódulos) + malha extrudada; o corte usa `clippingPlanes` só nas malhas em `K.clipMeshes`.

## Onde mexer
- Parâmetros de processo/grades: objeto `P` e `GR` (topo do `<script>`).
- Geometria 3D: função `buildAll()`.
- Cálculos: seção "MODELO DE ENGENHARIA".
- Painel/UI: HTML no topo + seção "UI WIRING".

## Estilo de pedido preferido pelo autor (Flávio)
- Entregas com acabamento alto, técnicas e embasadas. Mudanças aplicadas **todas de uma vez**, não incrementais.
