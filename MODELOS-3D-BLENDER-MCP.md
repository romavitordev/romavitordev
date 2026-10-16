# Modelos 3D profissionais com Claude + Blender MCP

Complemento do [Padrão de Qualidade de Sites](PADRAO-QUALIDADE-SITES.md) (seção 1.2). Ensina o Claude/agente a criar modelos 3D realistas **do zero** (sem assets prontos) controlando o Blender via MCP, e a levar o resultado para o site sem estragar a performance.
Copie para o repo do cliente quando precisar marcar o status (`[x]` feito, `[~]` parcial, `[ ]` pendente).

Atualizado em: 16/10/2026 (primeira versão)

Referência: Tesla Model Y modelado do zero, com um prompt de uma frase, em cerca de 2 horas de trabalho autônomo do Claude via Blender MCP ([post de @marmaduke091](https://x.com/marmaduke091/status/2109396343114666207), out/2026). Resultado escolhido a dedo: não espere esse nível na primeira resposta.

---

## 1. Quando usar

| Situação | Técnica |
|---|---|
| Objeto que precisa ser fiel e exclusivo (produto, embalagem, imóvel, móvel, carro) | **Blender MCP (este documento)** |
| Objeto genérico, rápido, sem exigência de fidelidade | Gerador IA ([Meshy](https://www.meshy.ai/), [Tripo](https://www.tripo3d.ai/), [Rodin](https://hyper3d.ai/)) ou asset do [Sketchfab](https://sketchfab.com/) / [Poly Pizza](https://poly.pizza/) |
| Pessoas, animais e formas orgânicas complexas | Gerador IA + ajuste no Blender (via MCP) |
| Fundo ou efeito decorativo (formas, partículas, gradientes) | Código direto: [ShaderGradient](PADRAO-QUALIDADE-SITES.md#shadergradient), Three.js / React Three Fiber, sem Blender |

---

## 2. Setup (máquina local, uma vez)

O Blender MCP roda **no computador**, com o Blender aberto. Não funciona em sessão na nuvem sem Blender.

1. Instalar o [Blender](https://www.blender.org/download/) (4.x ou superior) e o [uv](https://docs.astral.sh/uv/).
2. Baixar o `addon.py` do repositório [ahujasid/blender-mcp](https://github.com/ahujasid/blender-mcp).
3. Blender → *Edit → Preferences → Add-ons → Install* → escolher o `addon.py` → ativar.
4. Na viewport, tecla **N** → aba **BlenderMCP** → **Connect**.
5. Conectar ao Claude:
   - **Claude Code** (preferido: aguenta sessões longas e autônomas):
     ```bash
     claude mcp add blender uvx blender-mcp
     ```
   - **Claude Desktop** (`claude_desktop_config.json`):
     ```json
     { "mcpServers": { "blender": { "command": "uvx", "args": ["blender-mcp"] } } }
     ```
6. Teste: "crie um cubo com bevel e tire um screenshot da viewport".

> ⚠️ O Blender MCP executa código Python arbitrário dentro do Blender. Salve o `.blend` antes de sessões longas e trabalhe numa pasta só do projeto.

---

## 3. Regras para o agente

Copie para o `CLAUDE.md` / `AGENTS.md` do projeto que tiver modelagem 3D:

```markdown
## Modelagem 3D (Blender MCP)

- Model everything from scratch with `bpy` scripts. Never import ready-made
  models or assets unless the user explicitly asks.
- Before creating any mesh, write down the real-world dimensions (meters) of the
  object and its main parts. Work at real scale.
- Work in stages: blockout → main form → components → details → materials →
  lighting → render → web export. Do not skip ahead.
- After EVERY stage, take viewport screenshots (front, side, top, 3/4), compare
  them against the dimensions and the reference, and fix proportions before moving on.
- Prefer simple base meshes + modifiers (Mirror, Subdivision Surface, Bevel,
  Solidify, Weighted Normal) over dense hand-made meshes. Shade Smooth.
- Name every object, material and collection clearly (e.g. `Body`, `Wheel_FL`,
  `Glass_Windshield`). No leftover default materials.
- Use physically based Principled BSDF materials with realistic values.
- Studio scene: infinite backdrop (cyclorama), studio HDRI, soft shadows,
  3/4 camera with a 50–85 mm lens.
- Save versioned .blend files (`project_v01.blend`, `v02`…) after each stage.
- Keep working autonomously until the result looks like a professional product catalog.
```

---

## 4. Pipeline em etapas

| Etapa | O que fazer | Checagem obrigatória |
|---|---|---|
| **0. Briefing** | Medidas reais, lista de partes, estilo de render | Medidas escritas antes de modelar |
| **1. Blocagem** | Primitivas que definem volume e proporção | Screenshots frente/lado/topo batem com as medidas |
| **2. Forma principal** | Malha base com Mirror + Subdivision; linhas de caráter | Silhueta reconhecível só pela sombra |
| **3. Componentes** | Peças separadas (rodas, vidros, faróis, portas, interior…) | Cada peça nomeada e no lugar |
| **4. Detalhes** | Bevels, frestas de painel, logotipos, parafusos, texto | Close-ups em screenshot |
| **5. Materiais** | PBR de cada superfície (seção 5) | Preview em Eevee sem material default |
| **6. Cena e luz** | Cyclorama, HDRI de estúdio, câmera 3/4 | Sem áreas estouradas ou pretas |
| **7. Render final** | Cycles, denoise, AgX/Filmic, 1920×1080 ou mais | Render aprovado (vira o poster do site) |
| **8. Exportação web** | Seção 7 | Metas de tamanho e polígonos |

---

## 5. Materiais de referência (Principled BSDF)

Pontos de partida; ajuste olhando o render.

| Material | Base Color | Metallic | Roughness | Extras |
|---|---|---|---|---|
| Pintura automotiva | cor da marca | 0.0–0.3 | 0.25–0.35 | Coat 1.0, Coat Roughness 0.03 |
| Metal cromado | cinza claro | 1.0 | 0.05–0.1 | |
| Metal escovado | cinza | 1.0 | 0.3–0.45 | Anisotropic 0.5 |
| Vidro | branco | 0.0 | 0.0 | Transmission 1.0, IOR 1.45 |
| Vidro fumê | cinza escuro | 0.0 | 0.05 | Transmission 0.9 |
| Borracha / pneu | quase preto | 0.0 | 0.7–0.9 | noise leve no bump |
| Plástico | cor | 0.0 | 0.4–0.6 | |
| Couro | cor | 0.0 | 0.5–0.7 | noise fino no bump |
| Madeira | textura | 0.0 | 0.5–0.7 | normal map |
| Concreto / parede | cinza claro | 0.0 | 0.8–0.95 | noise no bump |

---

## 6. Prompts

Em inglês, como os prompts da seção 1.1 do padrão. Preencha o que está entre `[colchetes]`.

**Prompt principal (objeto do zero)**

```text
Using the Blender MCP, model a realistic [object] FROM SCRATCH. Do not import any ready-made model or asset.

1. Before modeling, list the real-world dimensions in meters and work at real scale.
2. Work in stages: blockout → main form → components → details → materials → lighting → render.
3. After EACH stage, take viewport screenshots (front, side, top and 3/4), evaluate the proportions and fix them before moving on.
4. Use simple base meshes with modifiers (Mirror, Subdivision Surface, Bevel, Solidify, Weighted Normal). Shade Smooth.
5. Name every object and material and organize them in collections.
6. Realistic PBR materials (Principled BSDF): [e.g. car paint with clearcoat, glass with transmission, rubber tires].
7. Studio scene: infinite backdrop, studio HDRI, soft shadows, 3/4 camera with a 70 mm lens.
8. Save a versioned .blend after each stage.
9. Finally, render in Cycles (1920x1080, denoise) and export an optimized .glb for the web (under [2] MB, Draco compressed).

Work autonomously until the result has professional product-catalog quality.
```

**Correção de proporção (quando algo parecer errado)**

```text
Take screenshots from the front, side and top. Compare [part] against the real dimensions you listed. List every proportion that is off by more than 5%, fix them one by one, and show the screenshots again.
```

**Exportação para o site**

```text
Prepare a web version of this scene: duplicate the collection, apply modifiers, decimate hidden or flat areas, bake procedural materials to [1024/2048] px textures (base color, roughness, normal), and export a .glb with +Y up. Target [100k] triangles and under [2] MB after Draco compression. Also save the final Cycles render as a [WebP] poster image.
```

**Exemplos de `[object]` para os nossos nichos**
- Imobiliária: "modern single-story 3-bedroom house with pool, concrete and wood facade" / "cutaway model of a 2-bedroom apartment".
- Hamburgueria / café: "artisan burger with brioche bun, melted cheddar and bacon" / "cappuccino cup with latte art on a wooden table".
- Fotografia / portfólio: "mirrorless camera with a 50 mm lens".

---

## 7. Exportação para o site (`.glb`)

1. Aplicar modificadores (Subdivision no nível 1 ou 2, não mais).
2. Reduzir polígonos: **50k a 150k triângulos** para hero; até 300k só se o modelo for o foco da página. Decimate onde não aparece.
3. Assar (bake) materiais procedurais em texturas de **1024 a 2048 px**.
4. Exportar **glTF Binary (.glb)**, +Y up, com materiais.
5. Comprimir:
   ```bash
   npx @gltf-transform/cli optimize modelo.glb modelo-otimizado.glb --compress draco --texture-compress webp
   ```
6. Conferir no [gltf.report](https://gltf.report/) ou no [glTF Viewer](https://gltf-viewer.donmccurdy.com/).

Metas:
- [ ] `.glb` final **≤ 5 MB** (ideal ≤ 2 MB para mobile).
- [ ] Carrega em menos de 3 s em 4G.
- [ ] 60 fps em celular intermediário.

---

## 8. Exibição no site

**Stack**
- Produto girando + AR no celular, sem animação de scroll: [`<model-viewer>`](https://modelviewer.dev/) (uma tag, funciona em site estático).
- Cena com scroll, câmera animada ou interação: React Three Fiber + [Drei](https://github.com/pmndrs/drei) (mesma regra da seção 1 do padrão: Next 15 App Router pede R3F v9 + React 19).

**`<model-viewer>` (site estático)**

```html
<script type="module" src="https://cdn.jsdelivr.net/npm/@google/model-viewer@[versão]/dist/model-viewer.min.js"></script>

<model-viewer
  src="/modelos/casa.glb"
  poster="/modelos/casa-poster.webp"
  alt="[Descrição do modelo para leitor de tela]"
  camera-controls
  auto-rotate
  rotation-per-second="10deg"
  shadow-intensity="1"
  environment-image="neutral"
  ar
  loading="lazy"
  reveal="auto"
  style="width: 100%; aspect-ratio: 4 / 3;">
</model-viewer>
```

Com `prefers-reduced-motion: reduce`, remova o `auto-rotate` via JS.

**React Three Fiber (Next)**

```tsx
'use client'
import { Suspense } from 'react'
import { Canvas } from '@react-three/fiber'
import { ContactShadows, Environment, OrbitControls, useGLTF } from '@react-three/drei'

function Modelo() {
  const { scene } = useGLTF('/modelos/casa.glb')
  return <primitive object={scene} />
}

export function Hero3D() {
  return (
    <Canvas dpr={[1, 2]} camera={{ position: [4, 2, 6], fov: 35 }}>
      <Suspense fallback={null}>
        <Modelo />
        <Environment preset="studio" />
        <ContactShadows position={[0, 0, 0]} opacity={0.5} blur={2.5} />
      </Suspense>
      <OrbitControls enableZoom={false} autoRotate autoRotateSpeed={0.5} />
    </Canvas>
  )
}

useGLTF.preload('/modelos/casa.glb')
```

Carregar com `dynamic(() => import('./Hero3D').then(m => m.Hero3D), { ssr: false })` e mostrar o render do Blender (poster) até o 3D ficar pronto.

**Checklist**
- [ ] Poster estático (o render do Blender) aparece antes do 3D e serve de fallback sem WebGL.
- [ ] 3D só carrega quando a seção entra na tela (lazy).
- [ ] Loader ou barra de progresso enquanto o `.glb` baixa.
- [ ] `dpr` limitado a `[1, 2]`; sombras e pós-processamento simplificados no celular.
- [ ] HDRI de estúdio + sombra de contato; tone mapping ACES/AgX.
- [ ] Rotação lenta e com easing; sem rotação automática com `prefers-reduced-motion`.
- [ ] `alt` / descrição do modelo para leitor de tela; controles por teclado quando houver interação.
- [ ] O 3D não cobre nem atrasa o CTA principal; LCP continua < 2,5 s (o poster é o LCP, não o canvas).

---

## 9. Critérios de aceite

- [ ] Modelado do zero, sem assets importados (salvo pedido explícito).
- [ ] Proporções conferidas por screenshots em 4 vistas.
- [ ] Objetos e materiais nomeados; nenhum material default.
- [ ] Render final em Cycles com qualidade de catálogo.
- [ ] `.glb` dentro das metas da seção 7.
- [ ] Checklist da seção 8 completo.
- [ ] `.blend` fonte guardado com o projeto (fora do bundle do site) e citado no README.

---

## 10. Limitações e cuidados

- Precisa do Blender aberto na máquina local; em sessão na nuvem, use geradores IA ou modele no próprio código (Three.js).
- Resultados como o da referência levam horas e várias iterações; combine prazo e custo com o cliente antes.
- Marca, logotipo ou produto de terceiros (ex.: carro de uma montadora) só com autorização ou para estudo interno, nunca em site de cliente sem permissão.
- Valores das tabelas e metas são pontos de partida, não regras rígidas: o render e o teste no celular decidem.
