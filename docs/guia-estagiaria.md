# Guia Passo a Passo — Estagiária

**Projeto:** ProspectAI
**Repositório:** https://github.com/ojosemeirelles/ProspectAI
**Documento de tarefas:** `docs/tarefas-internacionalizacao.md`

---

## 1. Configurar o ambiente

### 1.1 Instalar ferramentas necessárias (se ainda não tiver)

- **Node.js 18+** — https://nodejs.org
- **Git** — https://git-scm.com
- **VS Code** (recomendado) — https://code.visualstudio.com

### 1.2 Clonar o repositório

Abra o terminal e execute:

```bash
git clone https://github.com/ojosemeirelles/ProspectAI.git
cd ProspectAI
```

### 1.3 Instalar as dependências

```bash
npm install
```

### 1.4 Configurar a chave da API

Crie o arquivo `.env.local` na raiz do projeto:

```bash
echo "NEXT_PUBLIC_GEMINI_API_KEY=CHAVE_QUE_O_JOSE_VAI_TE_PASSAR" > .env.local
```

> **IMPORTANTE:** Nunca commite este arquivo. Ele já está no `.gitignore`.

### 1.5 Testar se funciona

```bash
npm run dev
```

Acesse http://localhost:3000 no navegador. Se a página carregar, está tudo certo.

---

## 2. Criar uma branch (MUITO IMPORTANTE)

**Nunca trabalhe direto na `main`.** A branch `main` é o código de produção (o que está no ar). Trabalhar nela diretamente pode quebrar o app.

### 2.1 Certifique-se de estar na main atualizada

```bash
git checkout main
git pull origin main
```

### 2.2 Crie sua branch de trabalho

```bash
git checkout -b feature/internationalization
```

Isso cria uma cópia da `main` onde você pode trabalhar sem afetar nada.

### 2.3 Confirme que está na branch certa

```bash
git branch
```

Deve aparecer:

```
  main
* feature/internationalization
```

O `*` indica em qual branch você está.

---

## 3. Fluxo de trabalho diário

### 3.1 Antes de começar a trabalhar cada dia

```bash
git checkout feature/internationalization
git pull origin main
```

Isso garante que sua branch está atualizada com qualquer mudança na main.

### 3.2 Enquanto trabalha — salve seu progresso com commits

Depois de completar cada tarefa (ou parte significativa), faça um commit:

```bash
# Ver o que mudou
git status

# Adicionar os arquivos que você modificou (exemplo)
git add types/index.ts
git add lib/constants.ts

# Criar o commit com uma mensagem descritiva
git commit -m "feat: add country field to types and countries list"
```

**Dicas para commits:**
- Faça commits pequenos e frequentes (1 por tarefa é ideal)
- Use mensagens descritivas em inglês
- Prefixos: `feat:` para feature nova, `fix:` para correção

### 3.3 Enviar suas mudanças para o GitHub

```bash
git push origin feature/internationalization
```

Na primeira vez pode pedir para configurar o upstream:
```bash
git push --set-upstream origin feature/internationalization
```

---

## 4. Quando terminar todas as tarefas

### 4.1 Certifique-se que tudo funciona

```bash
npm run dev
```

Teste com Brasil, Espanha e EUA (conforme Tarefa 8 do documento de tarefas).

### 4.2 Faça o push final

```bash
git add .
git commit -m "feat: complete internationalization support"
git push origin feature/internationalization
```

### 4.3 Crie um Pull Request (PR)

1. Acesse https://github.com/ojosemeirelles/ProspectAI
2. Vai aparecer um banner amarelo: "feature/internationalization had recent pushes"
3. Clique em **"Compare & pull request"**
4. Preencha:
   - **Título:** `feat: add internationalization support`
   - **Descrição:** Liste o que foi feito (tarefas completadas, países testados)
5. Clique em **"Create pull request"**
6. **NÃO clique em Merge** — avise o José que o PR está pronto para revisão

---

## 5. Comandos Git úteis (cola rápida)

| Comando | O que faz |
|---------|-----------|
| `git status` | Ver arquivos modificados |
| `git diff` | Ver o que mudou nos arquivos |
| `git log --oneline -5` | Ver últimos 5 commits |
| `git branch` | Ver em qual branch está |
| `git checkout main` | Voltar para a main |
| `git checkout feature/internationalization` | Voltar para sua branch |
| `git stash` | Guardar mudanças temporariamente |
| `git stash pop` | Recuperar mudanças guardadas |

---

## 6. Se algo der errado

### "Mexi em algo que não devia"
```bash
# Desfazer mudanças NÃO commitadas em um arquivo
git checkout -- nome-do-arquivo.ts
```

### "Quero descartar TUDO que fiz e recomeçar"
```bash
git checkout main
git branch -D feature/internationalization
git checkout -b feature/internationalization
```

### "Fiz commit errado"
```bash
# Desfaz o último commit mas mantém as mudanças
git reset --soft HEAD~1
```

### "Não consigo dar push"
```bash
git pull origin feature/internationalization --rebase
git push origin feature/internationalization
```

### Em caso de dúvida
**Pare e pergunte ao José.** É melhor perguntar do que quebrar algo.

---

## 7. Estrutura de arquivos que você vai mexer

```
ProspectAI/
├── types/index.ts              ← Tarefa 1: adicionar country aos tipos
├── lib/constants.ts            ← Tarefa 2: criar lista de países
├── components/SearchForm.tsx   ← Tarefas 3 e 4: seletor de país + labels
├── app/page.tsx                ← Tarefas 5 e 7: prompt de busca
└── components/LeadDetail.tsx   ← Tarefa 6: prompt de relatório
```

**NÃO mexa em outros arquivos** a menos que seja necessário para as tarefas.

---

## 8. Checklist final antes de criar o PR

- [ ] Todas as 8 tarefas completadas
- [ ] Testei com Brasil e funciona como antes
- [ ] Testei com Espanha e resultados vêm em espanhol
- [ ] Testei com EUA e resultados vêm em inglês
- [ ] Nenhum erro no console do navegador (F12)
- [ ] `npm run dev` roda sem erros
- [ ] Todos os commits com mensagens descritivas
- [ ] Push feito para a branch `feature/internationalization`
- [ ] PR criado no GitHub
