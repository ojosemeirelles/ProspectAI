# ProspectAI — Tarefas de Internacionalização

**Projeto:** ProspectAI
**Repositório:** https://github.com/ojosemeirelles/ProspectAI
**URL de produção:** https://prospectai-six.vercel.app
**Data:** 29/03/2026
**Objetivo:** Expandir o ProspectAI para funcionar em outros países além do Brasil.

---

## Contexto

Atualmente o ProspectAI funciona apenas para o Brasil. Os estados brasileiros estão fixos no código, o prompt do Gemini sempre monta a localização como `"..., Brasil"` e todos os textos da IA são gerados em português.

O objetivo é adicionar um seletor de país para que o usuário possa buscar leads em qualquer país, com o prompt se adaptando automaticamente ao idioma e localização.

---

## Stack do Projeto

- **Next.js 15** + **React 19** + **TypeScript**
- **Tailwind CSS 4** (estilização)
- **Google Gemini API** (`@google/genai`) — busca de leads e relatórios
- **Lucide React** (ícones)

---

## Estrutura de Arquivos Relevantes

```
lib/constants.ts         → Lista de estados brasileiros (BRAZILIAN_STATES)
types/index.ts           → Tipos Lead e SearchParams
components/SearchForm.tsx → Formulário de busca (ICP, serviço, estado, cidade)
app/page.tsx             → Página principal, lógica de busca com Gemini
components/LeadDetail.tsx → Página de detalhe do lead, relatório com Gemini
```

---

## Tarefas

### Tarefa 1 — Atualizar os tipos (fácil)
**Arquivo:** `types/index.ts`

- Adicionar o campo `country: string` ao tipo `SearchParams`
- Adicionar o campo opcional `country?: string` ao tipo `Lead`

**Exemplo de como deve ficar:**
```typescript
export type SearchParams = {
  icp: string;
  service: string;
  country: string;  // NOVO
  state: string;
  city: string;
};

export type Lead = {
  // ... campos existentes ...
  country?: string;  // NOVO
};
```

---

### Tarefa 2 — Criar dados de países (fácil)
**Arquivo:** `lib/constants.ts`

- Criar um array `COUNTRIES` com os principais mercados
- Cada país deve ter: `value` (código ISO), `label` (nome do país), `language` (idioma para o prompt da IA)
- Manter o `BRAZILIAN_STATES` existente

**Países sugeridos para incluir:**
| Código | País | Idioma |
|--------|------|--------|
| BR | Brasil | pt-BR |
| PT | Portugal | pt-PT |
| ES | Espanha | es |
| US | Estados Unidos | en |
| MX | México | es |
| AR | Argentina | es |
| CO | Colômbia | es |
| CL | Chile | es |
| UK | Reino Unido | en |
| DE | Alemanha | de |
| FR | França | fr |
| IT | Itália | it |

**Exemplo:**
```typescript
export const COUNTRIES = [
  { value: "BR", label: "Brasil", language: "pt-BR" },
  { value: "PT", label: "Portugal", language: "pt-PT" },
  { value: "ES", label: "Espanha", language: "es" },
  { value: "US", label: "Estados Unidos", language: "en" },
  // ... etc
];
```

---

### Tarefa 3 — Adicionar seletor de País no formulário (média)
**Arquivo:** `components/SearchForm.tsx`

- Adicionar um novo estado `country` com valor padrão `"BR"`
- Adicionar um `<select>` de País **antes** do campo Estado
- Lógica condicional:
  - Se `country === "BR"` → mostrar dropdown de estados brasileiros (comportamento atual)
  - Para qualquer outro país → trocar o dropdown por um `<input>` de texto livre para o usuário digitar a região/estado
- Atualizar o `onSearch` para enviar `country` junto
- Atualizar placeholders dinamicamente:
  - Brasil: "Ex: São Paulo"
  - Espanha: "Ex: Madrid"
  - EUA: "Ex: New York"
  - Outros: "Ex: Nome da cidade"

**Layout sugerido (grid de 3 colunas):**
```
[ País        ] [ Estado/Região ] [ Cidade       ]
```

---

### Tarefa 4 — Atualizar labels da UI (fácil)
**Arquivo:** `components/SearchForm.tsx`

- Trocar o label "Estado" por "Estado / Região"
- Trocar a option "Todo o Brasil" por "Todo o país"
- Manter toda a UI em português (não precisa fazer i18n completo)

---

### Tarefa 5 — Adaptar o prompt de busca do Gemini (média)
**Arquivo:** `app/page.tsx` (função `handleSearch`)

O que mudar:

1. **Localização:** Atualmente monta `"${state}, Brasil"`. Deve montar usando o nome do país selecionado
2. **Idioma do prompt:** Buscar o `language` do país em `COUNTRIES` e pedir para o Gemini gerar o `aiSummary` nesse idioma
3. **Idioma do prompt em si:** O prompt pode continuar em português (o Gemini entende), mas a instrução do `aiSummary` deve especificar o idioma de saída

**Trecho atual (linha 27):**
```typescript
const locationStr = city ? `${city}, ${state}, Brasil` : `${state}, Brasil`;
```

**Como deve ficar:**
```typescript
const countryData = COUNTRIES.find(c => c.value === country);
const countryName = countryData?.label || country;
const language = countryData?.language || "pt-BR";
const locationStr = city
  ? `${city}, ${state}, ${countryName}`
  : `${state}, ${countryName}`;
```

E no prompt, trocar:
```
"aiSummary": "Resumo de oportunidade de no máximo 3 linhas em português (pt-BR)..."
```
Por:
```
"aiSummary": "Resumo de oportunidade de no máximo 3 linhas em ${language}..."
```

---

### Tarefa 6 — Adaptar o prompt de relatório detalhado (média)
**Arquivo:** `components/LeadDetail.tsx`

- Mesmo princípio da Tarefa 5
- O relatório do lead deve ser gerado no idioma do país
- Receber `country` via `searchParams` e usar para definir o idioma
- Passar o idioma na instrução do prompt de relatório

---

### Tarefa 7 — Incluir país no JSON pedido ao Gemini (fácil)
**Arquivo:** `app/page.tsx`

- No template JSON dentro do prompt, adicionar o campo `"country"` para que o Gemini retorne o país de cada lead

**Adicionar no JSON do prompt:**
```json
"country": "País do negócio"
```

---

### Tarefa 8 — Testar (obrigatória)
Fazer buscas com pelo menos 3 países e validar:

| Teste | País | O que verificar |
|-------|------|----------------|
| 1 | Brasil (SP) | Deve funcionar exatamente como antes |
| 2 | Espanha | Resultados em espanhol, leads reais |
| 3 | Estados Unidos | Resultados em inglês, leads reais |

Para cada teste verificar:
- [ ] Leads retornados são reais e do país correto
- [ ] Digital Pain Score faz sentido
- [ ] aiSummary está no idioma certo
- [ ] Relatório detalhado gera no idioma certo
- [ ] Links do Google Maps funcionam
- [ ] Nenhum erro no console do browser

---

## Ordem de execução sugerida

| Passo | Tarefa | Complexidade | Estimativa |
|-------|--------|-------------|------------|
| 1 | Tarefa 1 — Tipos | Fácil | 10 min |
| 2 | Tarefa 2 — Constants de países | Fácil | 15 min |
| 3 | Tarefa 3 — Seletor no form | Média | 40 min |
| 4 | Tarefa 4 — Labels da UI | Fácil | 10 min |
| 5 | Tarefa 7 — País no JSON | Fácil | 5 min |
| 6 | Tarefa 5 — Prompt de busca | Média | 30 min |
| 7 | Tarefa 6 — Prompt de relatório | Média | 30 min |
| 8 | Tarefa 8 — Testes | Obrigatória | 30 min |

---

## Como rodar o projeto local

```bash
# Clonar o repositório
git clone https://github.com/ojosemeirelles/ProspectAI.git
cd ProspectAI

# Instalar dependências
npm install

# Criar arquivo .env.local com a chave do Gemini
echo "NEXT_PUBLIC_GEMINI_API_KEY=SUA_CHAVE_AQUI" > .env.local

# Rodar o servidor de desenvolvimento
npm run dev
```

O app vai abrir em http://localhost:3000

---

## Observações

- Criar uma branch separada para essas mudanças: `git checkout -b feature/internationalization`
- Fazer commits pequenos e descritivos por tarefa
- Não alterar arquivos fora do escopo listado
- Em caso de dúvida, consultar a documentação do Gemini: https://ai.google.dev/docs
