# Padrão de Qualidade de Sites (Front + Back)

Checklist base para todo site/sistema entregue pela Roma & Buganza.
Use como ponto de partida em cada projeto e copie para o repo do cliente quando precisar marcar o status (`[x]` feito, `[~]` parcial, `[ ]` pendente).

Atualizado em: 10/10/2026

---

## 1. Design e referências

Antes de abrir o editor, defina a direção visual com referências reais. Nada de "inventar do zero".

### Onde buscar referências

| Fonte | Para que serve | Como usar |
|---|---|---|
| [Refero Styles](https://styles.refero.design/) | Sistemas visuais extraídos de sites reais: cores, tipografia, espaçamento, motion e padrões de componentes. Busca por marca, clima (minimal, editorial, playful, alto contraste), cor, fonte ou URL. | Escolha 1 a 3 estilos próximos do nicho do cliente. Exporte o **DESIGN.md** do estilo e coloque na raiz do projeto (ou em `docs/`) para guiar o Claude/agente ao gerar telas. Cobre landing pages, preços, sites de produto e editoriais; não cobre dashboards/painéis internos. |
| [Cult UI](https://www.cult-ui.com/) | Componentes React animados, compatíveis com shadcn/ui (carrosséis 3D, dynamic island, efeitos com shader, textos animados, blocos prontos). Licença MIT, uso comercial liberado. | Projetos em React/Next com Tailwind + shadcn: `npx shadcn@latest add https://cult-ui.com/r/<componente>.json`. O código entra no projeto (não é dependência), então revise e ajuste ao tema. Use 1 ou 2 destaques por página, não em tudo. |
| [ShaderGradient](https://shadergradient.co/) | Gradientes 3D animados (WebGL) para hero, fundos de seção e capas. Tem editor visual, plugin para Figma e Framer. | Monte o gradiente no editor do site e copie a configuração/URL. Em React: `npm i @shadergradient/react @react-three/fiber three three-stdlib camera-controls` (+ `-D @types/three`). Next 15 App Router pede R3F v9 + React 19. Em site estático, exporte imagem/vídeo em vez de rodar WebGL. |

### Regras de uso das referências

- [ ] Direção visual definida a partir de referências (anotar no README quais estilos/sites inspiraram).
- [ ] Tokens definidos antes das telas: cores, fontes, escala de espaçamento, raios, sombras (variáveis CSS ou `tailwind.config`).
- [ ] No máximo 2 famílias de fonte; hierarquia de títulos clara.
- [ ] Contraste mínimo WCAG AA (4.5:1 texto, 3:1 textos grandes e ícones).
- [ ] Animações e efeitos (Cult UI, ShaderGradient) respeitam `prefers-reduced-motion`.
- [ ] Efeitos pesados (WebGL/shader) carregam depois do conteúdo (lazy / `dynamic(..., { ssr: false })`), têm fallback estático (imagem ou gradiente CSS) e são desligados ou simplificados no celular fraco.
- [ ] Efeito decorativo nunca atrapalha a leitura nem o CTA.

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
