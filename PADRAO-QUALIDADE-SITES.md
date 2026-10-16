# Padrão de Qualidade de Sites (Front + Back)

Checklist base para todo site/sistema entregue pela Roma & Buganza. O projeto de referência interna é o buganza_imoveis (seção 6).
Use como ponto de partida em cada projeto e copie para o repo do cliente quando precisar marcar o status (`[x]` feito, `[~]` parcial, `[ ]` pendente).

Atualizado em: 16/10/2026 (seção 1.2: modelos 3D com Claude + Blender MCP, detalhada em [MODELOS-3D-BLENDER-MCP.md](MODELOS-3D-BLENDER-MCP.md))

---

## 1. Design e referências

Antes de abrir o editor, defina a direção visual com referências reais. Nada de "inventar do zero".

### Onde buscar referências

| Fonte | Para que serve | Como usar |
|---|---|---|
| [Refero Styles](https://styles.refero.design/) | Sistemas visuais extraídos de sites reais: cores, tipografia, espaçamento, motion e padrões de componentes. Busca por marca, clima (minimal, editorial, playful, alto contraste), cor, fonte ou URL. | Escolha 1 a 3 estilos próximos do nicho do cliente. Exporte o **DESIGN.md** do estilo e coloque na raiz do projeto (ou em `docs/`) para guiar o Claude/agente ao gerar telas. Cobre landing pages, preços, sites de produto e editoriais; não cobre dashboards/painéis internos. |
| [Cult UI](https://www.cult-ui.com/) | 154 componentes React animados, compatíveis com shadcn/ui (globo, kanban, mockups Apple, cards, botões Halo, efeitos com shader, Prompt Composer). Licença MIT, uso comercial liberado. | Projetos em React/Next com Tailwind v4 + shadcn: `npx shadcn@latest add @cult-ui/<componente>` (busca: `npx shadcn@latest search @cult-ui --query card`). O código entra em `components/ui/` (não é dependência), então revise e ajuste ao tema. Use 1 ou 2 destaques por página, não em tudo. |
| [ShaderGradient](https://shadergradient.co/) | Gradientes 3D animados (WebGL) para hero, fundos de seção e capas. Tem editor visual ([shadergradient.co/customize](https://www.shadergradient.co/customize)), plugin para Figma e componente para Framer. Também tem versão Vue/Nuxt. | Monte o gradiente no editor e copie a URL. Em React: `npm i @shadergradient/react @react-three/fiber three three-stdlib camera-controls` (+ `npm i -D @types/three`). Next 15 App Router pede R3F v9 + React 19. Em site estático, use o plugin do Figma para exportar GIF/imagem em vez de rodar WebGL. Código pronto na seção 1.1. |

### 1.1 Prompts e regras para agentes (copiados dos sites)

Prompts originais em inglês, como estão nos sites: os agentes entendem melhor assim. Preencha o que está entre `[colchetes]` com os dados reais do cliente.

#### Refero Styles

Em cada estilo há abas **Preview, DESIGN.md, Tailwind v4, CSS Variables e Design Tokens**, com botão **Download DESIGN.md**. Fluxo: escolher o estilo → baixar o DESIGN.md → salvar no projeto → usar um dos prompts abaixo citando o arquivo.

**Prompt genérico (qualquer tela)**

```text
Design [screen] for [audience] so they can [specific job]. Use the linked [Style] DESIGN.md for its captured type and color roles. Use our real copy, content, and product behavior; the reference does not define them. Include [required sections and states]. Explain which visual decisions came from the reference and which you changed for this task.
```

**Landing page**

```text
Design a landing page for [product] aimed at [audience] trying to [job]. Use the linked [Style] DESIGN.md for its [canvas, type, imagery and action color]. Place a real crop of [feature] beside the claim it proves. Add [evidence] and one clear next step. If the product image is only a concept, label it as such.
```

**Página de preços**

```text
Design a pricing comparison for [service] and [buyer]. Use the linked [Style] DESIGN.md for its [canvas, hierarchy and action color]. Compare our actual [plans], billing interval, included usage, overage, and cancellation terms in one reading path. Mark any missing commercial fact for confirmation instead of inventing it.
```

**Onboarding / primeiro acesso**

```text
Design first-run setup for [product] so [new user] can complete [first valuable action]. Use the linked [Style] DESIGN.md for its [canvas, surfaces and action color]. Ask for only the inputs this step needs. Show progress, validation, the back or skip policy, and what happens after completion according to our product rules.
```

**Dashboard / painel**

```text
Build an analytics overview for [product] used by [role]. The main job is [decision or action]. Use the linked [Style] DESIGN.md for its captured typography, color roles, and surface treatment. Use our actual metric names and data. Include [required filters and actions]. Show loading, empty, and error states based on our product requirements. Explain which visual rules you kept and where the dashboard needs a different layout.
```

**Configurações**

```text
Design settings for [product] where [role] manages [actual preferences and permissions]. Use the linked [Style] DESIGN.md for its [canvas, surfaces and emphasis color]. Group routine preferences apart from account and risky changes. Show current values, save behavior, access limits, and confirmation or undo from our product rules.
```

**Template de DESIGN.md (quando for montar o seu)**

```markdown
# Design direction

Reference: [style URL and date reviewed]
Purpose: [audience and task]

## Typography
Display: [family, weight, size, line height, use]
Body: [family, weight, size, line height, reading width]

## Color roles
Canvas and surfaces: [values and where they apply]
Text and actions: [values and contrast requirements]

## Layout
Content width: [value or range]
Spacing rhythm: [repeated gaps, not every one-off measurement]

## Open decisions
[What the reference cannot tell you about your product]
```

**Dicas do Refero**
- Cada cor precisa de um papel (fundo, superfície, texto, ação, estado). Hex sem função não diz ao agente quando usar, e o acento forte não deve se espalhar para todo controle.
- Defina o papel de cada fonte (display, leitura, labels, código) antes dos tamanhos. O tamanho do título do hero não é o padrão de todos os títulos.
- Para combinar com uma marca existente: preserve o sistema (cores, tipo, espaçamento, componentes, tom), não copie a página pixel a pixel. Diga ao agente o que manter e o que é novo.
- Com Claude Code: diga o resultado para o usuário (não só "edite o componente X"), passe o DESIGN.md com as qualidades a preservar e peça para rodar os checks e revisar no navegador.
- Revisão de UI gerada por IA, nesta ordem: (1) hierarquia: mensagem, estado e ação principal óbvios; (2) consistência: escala de tipo, papéis de cor, ritmo de espaço, peso dos componentes; (3) estados e telas reais: mobile, loading, vazio, erro, hover, selecionado, desabilitado e textos longos.

Coleções úteis: [landing pages](https://styles.refero.design/ai-agents/landing-page-design-prompts), [preços](https://styles.refero.design/ai-agents/pricing-page-design-prompts), [formulários](https://styles.refero.design/ai-agents/form-design-prompts), [onboarding](https://styles.refero.design/ai-agents/onboarding-design-prompts), [dashboards](https://styles.refero.design/ai-agents/dashboard-design-prompts), [SaaS](https://styles.refero.design/ai-agents/saas-app-design-prompts), [componentes](https://styles.refero.design/ai-agents/component-design-prompts), [pares de fontes](https://styles.refero.design/design-styles/website-font-pairings), [e-commerce](https://styles.refero.design/design-styles/ecommerce-websites), [agências](https://styles.refero.design/design-styles/agency-websites), [minimalistas](https://styles.refero.design/design-styles/minimal-websites), [dark mode](https://styles.refero.design/design-styles/dark-mode-websites).

#### Cult UI

**Regras para colar no `CLAUDE.md` / `AGENTS.md` do projeto**

```markdown
## Cult UI

- Cult UI components are shadcn/ui registry items. Install them with
  `npx shadcn@latest add @cult-ui/<name>`; never recreate them by hand.
- Find component names and docs in https://www.cult-ui.com/llms.txt. Read a
  component's docs at https://www.cult-ui.com/docs/components/<name>.md before using it.
- Installed source lives in `components/ui/`. Edit it there; it is owned code,
  not a dependency.
- Components use Tailwind CSS v4 and shadcn theme tokens (`bg-background`,
  `text-foreground`, `ring-ring`). Style with tokens, not hardcoded colors.
- Animated components use `motion`. Respect `prefers-reduced-motion`.
```

**MCP do shadcn no Claude Code** (o agente busca e instala componentes sozinho): `npx shadcn@latest mcp init --client claude`. Se o projeto ainda não tem nenhum componente Cult UI, adicione no `components.json`:

```json
{
  "registries": {
    "@cult-ui": "https://www.cult-ui.com/r/{name}.json"
  }
}
```

**Exemplos de pedido** (cite "cult-ui" para o agente buscar no registro certo):

```text
Show me the form controls in the cult-ui registry
Add the Halo Select and Halo Switch from cult-ui to the settings page
Build a pricing section using Halo Card and Border Beam Button from cult-ui
Replace the chat input with Prompt Composer from cult-ui
```

**Tema:** os componentes usam as mesmas variáveis do shadcn (`--card`, `--border`, `--input`, `--muted`, `--foreground`, `--background`, `--ring`, `--accent`, `--destructive`, `--chart-1..5`, `--radius`). Ajuste a marca nessas variáveis do `globals.css`, nunca com cor fixa no componente. Dark mode via classe `.dark` (ex.: `next-themes`).

#### ShaderGradient

O site é o editor visual (não tem prompts). Monte o gradiente em [shadergradient.co/customize](https://www.shadergradient.co/customize), copie a URL e use assim:

```tsx
'use client'
import { ShaderGradientCanvas, ShaderGradient } from '@shadergradient/react'

export function HeroGradient() {
  return (
    <ShaderGradientCanvas
      style={{ position: 'absolute', inset: 0 }}
      pixelDensity={1}
      fov={45}
      lazyLoad
    >
      <ShaderGradient
        control="query"
        urlString="[URL copiada do shadergradient.co/customize]"
      />
    </ShaderGradientCanvas>
  )
}
```

Exemplo de URL: `https://www.shadergradient.co/customize?animate=on&cDistance=3.6&cPolarAngle=90&color1=%2352ff89&color2=%23dbba95&color3=%23d0bce1&lightType=3d&shader=defaults&type=plane&uFrequency=5.5&uSpeed=0.4&uStrength=4`

- Tipos: `plane`, `sphere`, `waterPlane`. Cores em `color1..3` (use as cores da marca). `grain="on"` dá textura de filme.
- Performance: `pixelDensity` 1 no mobile, `lazyLoad` ligado, `animate="off"` quando o usuário prefere menos movimento, e no Next carregar com `dynamic(() => import(...), { ssr: false })`.

### 1.2 Modelos 3D (Claude + Blender MCP)

Para modelos 3D realistas e exclusivos (produto, imóvel, prato, objeto da marca), o Claude modela **do zero** controlando o Blender pelo [Blender MCP](https://github.com/ahujasid/blender-mcp), revisa por screenshots a cada etapa e exporta um `.glb` otimizado para o site. Guia completo, com setup, regras para o `CLAUDE.md`, prompts, materiais, exportação e código de exibição: **[MODELOS-3D-BLENDER-MCP.md](MODELOS-3D-BLENDER-MCP.md)**.

Resumo:
- Blender MCP para objeto fiel e exclusivo; gerador IA (Meshy, Tripo, Rodin) ou Sketchfab para objeto genérico; código (ShaderGradient, Three.js) para efeito decorativo.
- Medidas reais antes de modelar, pipeline em etapas e screenshots em 4 vistas depois de cada etapa.
- `.glb` com Draco, ≤ 5 MB (ideal ≤ 2 MB), 50k a 150k triângulos para hero.
- Render do Blender como poster/fallback; 3D carregado sob demanda com `<model-viewer>` ou React Three Fiber.

### Regras de uso das referências

- [ ] Direção visual definida a partir de referências (anotar no README quais estilos/sites inspiraram).
- [ ] Tokens definidos antes das telas: cores, fontes, escala de espaçamento, raios, sombras (variáveis CSS ou `tailwind.config`).
- [ ] No máximo 2 famílias de fonte; hierarquia de títulos clara.
- [ ] Contraste mínimo WCAG AA (4.5:1 texto, 3:1 textos grandes e ícones).
- [ ] Animações e efeitos (Cult UI, ShaderGradient) respeitam `prefers-reduced-motion`.
- [ ] Efeitos pesados (WebGL/shader) carregam depois do conteúdo (lazy / `dynamic(..., { ssr: false })`), têm fallback estático (imagem ou gradiente CSS) e são desligados ou simplificados no celular fraco.
- [ ] Efeito decorativo nunca atrapalha a leitura nem o CTA.
- [ ] Modelos 3D seguem os critérios de aceite do [MODELOS-3D-BLENDER-MCP.md](MODELOS-3D-BLENDER-MCP.md#9-critérios-de-aceite) (`.glb` otimizado, poster de fallback, lazy load, 60 fps no celular).

---

## 2. Frontend

### Layout e responsividade
- [ ] Mobile first; testado em 360px, 768px, 1280px e 1920px.
- [ ] Sem rolagem horizontal em nenhuma largura.
- [ ] Áreas de toque com pelo menos 44x44px.
- [ ] Tema claro/escuro quando fizer sentido para a marca.

### Conversão
- [ ] CTA principal acima da dobra.
- [ ] WhatsApp flutuante (para sites de negócio local).
- [ ] Formulário com estados de carregando, sucesso e erro, e fallback para WhatsApp.
- [ ] Prova social: depoimentos, números, logos, fotos reais.

### Performance
- [ ] Lighthouse mobile: Performance ≥ 90, Acessibilidade ≥ 95, Boas práticas ≥ 95, SEO ≥ 95.
- [ ] Core Web Vitals: LCP < 2,5s, CLS < 0,1, INP < 200ms.
- [ ] Imagens em WebP/AVIF, com `width`/`height`, `loading="lazy"` abaixo da dobra e `srcset`.
- [ ] Fontes com `font-display: swap` e pré-carregamento só da fonte principal.
- [ ] Scripts de terceiros (analytics, pixel, chat) carregados de forma adiada.

### Acessibilidade
- [ ] HTML semântico (`header`, `nav`, `main`, `section`, `footer`), um `h1` por página.
- [ ] Navegação completa por teclado, com foco visível.
- [ ] `alt` em todas as imagens de conteúdo; decorativas com `alt=""`.
- [ ] Labels em todos os campos de formulário.

### SEO
- [ ] `title` e `meta description` únicos por página.
- [ ] Canonical, Open Graph e Twitter Card.
- [ ] Schema JSON-LD adequado (`LocalBusiness`, `RealEstateAgent`, `LegalService`, etc.).
- [ ] `sitemap.xml` e `robots.txt`.
- [ ] Search Console e Google Business Profile configurados.
- [ ] GA4/GTM com eventos de clique em CTA e WhatsApp.

---

## 3. Backend

### Segurança
- [ ] HTTPS obrigatório e headers de segurança (Helmet ou equivalente: CSP, HSTS, X-Frame-Options).
- [ ] Rate limit geral e mais restrito em login e formulários.
- [ ] Validação de entrada no servidor (zod/joi) em todas as rotas.
- [ ] Honeypot ou captcha nos formulários públicos.
- [ ] Senhas com bcrypt/argon2; sessões/JWT com expiração.
- [ ] Segredos só em variáveis de ambiente, nunca no repositório (`.env` no `.gitignore`).
- [ ] CORS restrito aos domínios do projeto.
- [ ] Uploads com limite de tamanho e tipo validado.

### Dados
- [ ] Migrações versionadas.
- [ ] Backup automático do banco e teste de restauração.
- [ ] Índices nas consultas mais usadas.

### Operação
- [ ] Logs estruturados e tratamento central de erros (sem stack trace para o usuário).
- [ ] Rota de health check.
- [ ] Monitoramento de uptime e alerta de erro.
- [ ] Deploy documentado (`DEPLOY.md`) e checklist de deploy.

---

## 4. Compliance e LGPD

- [ ] Checkbox de consentimento nos formulários.
- [ ] Política de Privacidade e Termos de Uso, linkados no rodapé.
- [ ] Banner/política de cookies quando houver analytics ou pixel.
- [ ] Dados reais do controlador (razão social, CNPJ, e-mail de contato).

---

## 5. Entrega

- [ ] README do projeto com stack, como rodar, variáveis de ambiente e referências visuais usadas.
- [ ] Textos e fotos reais (sem placeholder) antes de publicar.
- [ ] Testado em Chrome, Safari (iOS) e Samsung Internet.
- [ ] Favicon, ícones e imagem de compartilhamento (OG image).
- [ ] Página 404 personalizada.
- [ ] Cliente treinado no painel (quando houver).

---

## 6. Referência interna: buganza_imoveis (Marcelo Imóveis)

O [buganza_imoveis](https://github.com/romavitordev/buganza_imoveis) é o projeto mais completo do estúdio e o modelo a seguir em sites com painel. Next.js 14 (App Router) + TypeScript + Tailwind + Prisma/Postgres (Neon) + Supabase Storage, com painel administrativo, chatbot, leads e testes. Quando for começar um site novo, abra os arquivos citados abaixo e copie o padrão.

### 6.1 Estrutura de pastas (modelo para projetos com painel)

```
app/
  page.tsx, layout.tsx          # home + layout raiz (metadata, JSON-LD, tema)
  (rotas públicas)/             # ex.: imoveis/(lista), imoveis/[slug]
  painel-<nome>/                # painel com nome NÃO óbvio (não /admin)
  api/
    <publicas>/                 # leitura via DTO, rate limit nas que gravam
    admin/                      # CRUD; toda rota chama o guarda de sessão
  error.tsx, not-found.tsx, robots.ts, sitemap.ts, opengraph-image.tsx
components/                     # componentes do site
components/admin/               # componentes do painel
lib/                            # regras e utilitários, um assunto por arquivo
  marca.ts                      # nome, CRECI/CNPJ, redes, e-mail: fonte única
  dto.ts                        # o que pode sair do banco para o público
  session.ts, ratelimit.ts, totp.ts, storage.ts, revalidate.ts, site-url.ts
prisma/schema.prisma, seed.ts
scripts/                        # up, banco local, seed demo, retenção, importação
tests/                          # Vitest: regras de negócio e segurança
middleware.ts                   # 1ª camada de proteção do painel
README.md, DEPLOY.md, CHECKLIST-DEPLOY.md, INFORMACOES-PENDENTES.md, .env.example
```

### 6.2 Padrões para copiar

**Segurança**
- **Proteção em duas camadas:** o `middleware.ts` barra `/painel-*` e `/api/admin`, *e* cada handler começa com `barrarSemSessao()` / cada página com `exigirSessao()` (`lib/session.ts`). Se o matcher do middleware falhar, a rota continua protegida.
- **Teste que trava a regressão:** `tests/admin-guard.test.ts` lê todas as rotas de `app/api/admin` e quebra se alguma não chamar o guarda, ou se consultar o banco antes de chamá-lo.
- **DTO com allowlist** (`lib/dto.ts`): campo por campo, nunca spread do objeto do Prisma. Campo novo no banco não vaza para o site sozinho. Testado em `tests/dto.test.ts` (ex.: `precoInterno` e endereço completo nunca saem).
- **Dado sensível só no servidor:** o número do WhatsApp fica em `lib/whatsapp-server.ts` (`import "server-only"`); os botões apontam para `/api/contato`, que redireciona. Nada de `NEXT_PUBLIC_` para dado que não pode ir ao navegador.
- **Headers de segurança e CSP comentada** em `next.config.mjs` (CSP, HSTS, X-Frame-Options, nosniff, Referrer-Policy, Permissions-Policy), com o motivo de cada exceção.
- **Sessão:** JWT (jose) em cookie `httpOnly`/`secure`/`sameSite=lax` de 8h; `AUTH_SECRET` obrigatório, sem valor padrão no código; 2FA TOTP opcional.
- **Rate limit** (`lib/ratelimit.ts`): Upstash Redis em produção, memória em dev, e *fail-open* (se o Redis cair, o site não para).
- **Rotas públicas que gravam** (`app/api/leads/route.ts`): rate limit por IP, honeypot que responde "ok" para o bot, validação e corte de tamanho em cada campo.
- **Painel escondido:** nome não óbvio (`/painel-mis`), `noindex` no layout do painel e **fora** do `robots.txt`, que é público.

**SEO e performance**
- `metadata` com `title.template`, `metadataBase` via `lib/site-url.ts` e `generateMetadata` por página de item.
- JSON-LD no layout (`RealEstateAgent`) e por item (anúncio + `BreadcrumbList`).
- `notFound()` dentro do `generateMetadata` para dar 404 real (sem soft-404) e `permanentRedirect` de URL antiga ou código para o slug.
- `sitemap.ts` dinâmico lendo o banco (só itens ativos, com `lastModified`); `opengraph-image.tsx` gerada.
- **ISR + invalidação na hora:** `revalidate = 300` nas páginas públicas e `revalidarPaginasPublicas()` (`lib/revalidate.ts`) em toda mutação do painel.
- **Imagens:** `deviceSizes`/`imageSizes` enxutos em `next.config.mjs` (menos transformações cobradas na Vercel), compressão no navegador antes do upload (`lib/comprimir-imagem.ts`: lado máximo 1920px, JPEG 0.82, nunca trava o upload), upload direto para o Supabase com URL assinada e blur placeholder (`lib/blur.ts`).
- `Cache-Control` com `s-maxage` + `stale-while-revalidate` nas APIs públicas de leitura.

**Design e UX**
- **Marca num arquivo só** (`lib/marca.ts`): nome, nome curto, CRECI, cidade, e-mail, redes, crédito do estúdio e dados do controlador LGPD. Trocar a marca é editar um arquivo.
- **Tema claro/escuro por tokens:** variáveis CSS em `globals.css` (`:root` e `[data-theme="dark"]`) ligadas ao Tailwind (`black` = tinta, `white` = papel, `secundario`), sem `dark:` espalhado. Script inline no `<head>` aplica o tema antes da primeira pintura (sem piscar).
- `prefers-reduced-motion` respeitado nas animações (cena SVG do hero).
- Mensagem do WhatsApp pré-preenchida que **combina com o rótulo do botão** ("Agendar visita" pede visita) e leva o código do anúncio.
- **Checklist de qualidade dentro do painel** (`components/admin/QualidadeAnuncio.tsx`): o cliente vê o que falta (3+ fotos, descrição com 300+ caracteres…) antes de publicar.
- `loading.tsx`, `error.tsx` e `not-found.tsx` próprios, inclusive no painel.

**LGPD**
- Consentimento no formulário, finalidade declarada em comentário na rota, política em `/privacidade`.
- **Retenção que cumpre a política:** `scripts/retencao.mjs` apaga o que passou do prazo; roda em modo de ensaio por padrão e só apaga com `--aplicar`. Prazos do script e da página andam juntos.
- Medição de audiência própria e pseudonimizada (`lib/track.ts` + `/api/track`), sem depender de pixel de terceiros.

**Operação e documentação**
- `npm run up`: sobe banco local, espera ficar pronto, cria tabelas na primeira vez e abre o site, tudo num comando.
- `db:seed` (só admin, para produção) separado de `db:demo` (dados de exemplo): o catálogo de produção nasce vazio.
- `.env.example` explicando **cada** variável (ex.: URL *pooled* vs *direta* do Neon, por que o WhatsApp é server-only, o que acontece se ficar vazio).
- README com stack, como rodar, seção **Qualidade** (`npm test`, `npm run lint`, `npx tsc --noEmit`), seção **Segurança** e diagrama da estrutura.
- `CHECKLIST-DEPLOY.md` em fases (decisões do cliente → contas → conteúdo → deploy → verificação → pós-lançamento) e `INFORMACOES-PENDENTES.md` com o que falta o cliente mandar.
- Comentários explicam o **porquê** das decisões (custo, segurança, LGPD), não o que o código faz.

### 6.3 O que levar para o próximo projeto (resumo)

- [ ] `lib/marca.ts` como fonte única da marca.
- [ ] DTO com allowlist + teste para os campos que nunca podem vazar.
- [ ] Guarda de sessão no handler + teste que varre as rotas do painel.
- [ ] `next.config` com CSP e headers de segurança.
- [ ] Rate limit + honeypot + validação em toda rota pública que grava.
- [ ] Dados sensíveis (telefone, chaves) só no servidor.
- [ ] ISR + `revalidatePath` nas mutações.
- [ ] Tokens de tema em variáveis CSS e script anti-flash.
- [ ] `sitemap.ts`, `robots.ts`, `opengraph-image.tsx`, JSON-LD e 404 real.
- [ ] Script de retenção alinhado com a política de privacidade.
- [ ] `npm run up`, `.env.example` comentado, README + CHECKLIST-DEPLOY + INFORMACOES-PENDENTES.

> Pontos a melhorar no próprio buganza: migrar para Next 15/React 19 quando for mexer na base; trocar `db push` por migrações versionadas (`prisma migrate`) antes do próximo ajuste de schema em produção; adicionar CI (lint + tsc + test) no GitHub Actions; o README cita `tests/painel-mis-guard.test.ts`, mas o arquivo se chama `tests/admin-guard.test.ts`.
