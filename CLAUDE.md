# Caderno de Receitas Social — Documentação do projeto

## Visão geral

Aplicação de gestão de redes sociais com dois clientes fixos, cada um em sua própria página HTML. Dados salvos no Supabase.

## Arquivos principais

- `index.html` — página de entrada / seleção de cliente
- `gestta.html` — painel do cliente Gestta
- `guiavitta.html` — painel do cliente Guia Vitta

## Estrutura de cada página de cliente

### Sidebar (shelf)
- Botões de rede social: Instagram (ativo), TikTok, Facebook, WhatsApp (em breve)
- Separador
- **Calendário** — exibe posts "Pronto p/ agendar" e "Publicados" de todos os canais
- **Relatórios** — visão geral e análise por pilar de conteúdo

### Views disponíveis (`STATE.view`)
- `'kanban'` — board com 4 colunas de status + mix strip de planejamento mensal
- `'calendar'` — calendário mensal (usa `STATE.allPosts`, todos os canais)
- `'reports'` — relatórios por pilar (usa `STATE.allPosts`, todos os canais)

## Dados no Supabase

**Tabela:** `posts`

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | uuid | chave primária |
| `cliente_id` | text | `'gestta'` ou `'guia-vitta'` |
| `rede_id` | text | `'instagram'`, `'tiktok'`, etc. |
| `tema` | text | ideia/título do post |
| `data` | date | data agendada (ISO: YYYY-MM-DD) |
| `dia_semana` | text | dia da semana por extenso |
| `horario` | time | horário de publicação |
| `formato` | text | Carrossel, Reel, Post estático... |
| `pilar` | text | pilar de conteúdo (ver abaixo) |
| `objetivo` | text | objetivo de negócio |
| `cta` | text | call to action principal |
| `hashtags` | text | hashtags principais |
| `briefing_visual` | text | briefing para design |
| `story_apoio` | text | story que acompanha o post |
| `legenda` | text | legenda final completa |
| `status` | text | `planejar`, `design`, `pronto`, `publicado` |
| `agendado` | boolean | se foi agendado manualmente na plataforma |

## Pilares de conteúdo (`PLANEJAMENTO`)

| Pilar | % do mix | Meta mensal | Objetivo estratégico |
|-------|----------|-------------|----------------------|
| Educação Profunda | 40% | 8 | Constrói autoridade técnica; SEO de nicho; atrai decisores |
| Prova Social | 25% | 5 | Reduz fricção de decisão; ativa prova social |
| Bastidores | 15% | 3 | Humaniza a marca; constrói afinidade |
| Venda Direta | 20% | 4 | Converte demanda já educada; CTAs de assinatura/trial |

**Importante:** os nomes dos pilares no banco de dados devem ser exatamente iguais às chaves acima (case-sensitive). Qualquer variação (ex: "Venda direta" ou "Bastidores e Cultura") não será reconhecida pelos filtros.

## Estado da aplicação (`STATE`)

```js
STATE = {
  activeClient,  // CLIENTE_ID fixo da página
  activeNet,     // rede ativa ('instagram' por padrão)
  filter,        // filtro de pilar no kanban ('all' ou nome do pilar)
  view,          // 'kanban' | 'calendar' | 'reports'
  calYear,       // ano exibido no calendário/mix strip
  calMonth,      // mês exibido (0-indexed)
  posts,         // posts do canal ativo (fetchPosts)
  allPosts,      // todos os posts do cliente (fetchAllPosts — usado no calendário e relatórios)
}
```

## Comportamento do mix strip (Planejamento do mês)

- Aparece apenas na view `kanban`
- Conta posts de `STATE.posts` (canal ativo) filtrados pelo mês/ano de `STATE.calYear`/`STATE.calMonth`
- **Não** mostra posts de outros canais nem de outros meses
- O Relatórios (`STATE.allPosts`) conta tudo sem filtro de mês ou canal — é normal a diferença entre os dois

## Clientes

| Página | CLIENTE_ID | CLIENTE_NOME | CLIENTE_COR |
|--------|-----------|--------------|-------------|
| gestta.html | `'gestta'` | Gestta | `#d4849a` |
| guiavitta.html | `'guia-vitta'` | Guia Vitta | `#0d3b66` |

## Convenções de código

- Todo código JS está inline em cada `.html` (sem bundler)
- Sem frameworks — vanilla JS puro
- CSS inline no `<style>` de cada arquivo
- Variáveis CSS (custom properties) para cores no `:root`
- Para mudanças que afetam os dois clientes, editar **ambos** os arquivos
