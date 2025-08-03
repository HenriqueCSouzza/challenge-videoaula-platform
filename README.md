
# 🧱 Guia para Criação de Projeto do Zero — Baseado em Plataforma de video aula

Este documento serve como um guia para criar uma aplicação de videoaulas semelhante à Udemy. A arquitetura, boas práticas e tecnologias aqui descritas devem ser seguidas fielmente.

---

## 🧰 Tecnologias Utilizadas

| Stack              | Tecnologia                                  |
|-------------------|---------------------------------------------|
| **Framework**      | [Next.js 15 (App Router)](https://nextjs.org/) |
| **Linguagem**      | [TypeScript](https://www.typescriptlang.org/) |
| **Estilização**    | [MUI](https://mui.com/)                     |
| **Formulários**    | [React Hook Form](https://react-hook-form.com/), [Zod](https://zod.dev/) |
| **HTTP Client**    | [Axios](https://axios-http.com/)            |
| **Requisições**    | [SWR](https://swr.vercel.app/)              |
| **Player de Vídeo**| [React Player](https://github.com/cookpete/react-player) |
| **Banco de Dados** | [Supabase](https://supabase.com/) ✅         |
| **Auth**           | [NextAuth.js](https://next-auth.js.org/)   |
| **Email**          | [Resend](https://resend.com/)              |
| **Validação**      | Validação client-side com Zod e backend com schema |
| **Erros**          | Tratativas com Prisma (ou Supabase), Zod, e client-side |
| **Logs**           | [Sentry](https://sentry.io/)               |
| **Armazenamento**  | [Amazon S3](https://aws.amazon.com/s3/)    |
| **Vídeos**         | [Vimeo Private Embed](https://vimeo.com/)  |

---

## 🗂️ Estrutura do Projeto

```
/app
  ├─ /auth               → login, recuperação de senha
  ├─ /painel             → player de aulas (acesso dos alunos)
  ├─ /matricula          → formulário de matrícula
  └─ /admin              → painel administrativo (Modulos, Aulas, Usuários, Arquivos)

/lib
  ├─ api.ts              → instância do Axios
  ├─ supabase.ts         → client do Supabase
  └─ utils               → helpers gerais (ex: tratamento de erros)

/components
  ├─ forms               → formulários com RHF e Zod
  ├─ modals              → modais de criação/edição
  ├─ player              → vídeo + AccordionLesson
  └─ hooks               → hooks customizados

/pages/api
  └─ /backoffice         → rotas administrativas (CRUD)
     /matricula          → rota de matrícula + geração de contrato
     /auth               → autenticação NextAuth
     /esqueci-senha      → gerar código, validar código, redefinir senha

/prisma (caso use Prisma)
  └─ schema.prisma       → usado somente se quiser referência
```

---

## 📄 Funcionalidades a Serem Implementadas

### 1. **Formulário de Matrícula**
- Captura dados do aluno e endereço.
- Cria o usuário no Supabase.
- Gera o contrato PDF.
- Envia link de pagamento via Asaas (mockar).

### 2. **Login e Recuperação de Senha**
- Login via NextAuth
- Recuperação de senha com:
  - envio de código via e-mail
  - validação do código
  - formulário para nova senha

### 3. **Painel do Aluno**
- Exibição de vídeo com React Player
- Lista de módulos e aulas com Accordion
- Liberação de conteúdo após pagamento e assinatura

### 4. **Painel Admin**
- CRUD de Unidades e Aulas
- CRUD de Usuários
- Upload de arquivos para S3 (contratos, vídeos, imagens)
- Modal com react-hook-form + zod
- Listagem com paginação e SWR

---

## 🧾 Regras de Backend

- **Validação com Zod**: tanto client quanto server.
- **Resposta Paginada Padrão**:
```ts
{
  data: {
    meta: {
      totalPages,
      from,
      to,
      hasNextPage,
      hasPrevPage
    },
    items: [],
    links: { first, prev, next, last }
  }
}
```
- **Erro Padronizado**:
```ts
return NextResponse.json({
  message: "Erro ao validar dados",
  issues: zodError.flatten().fieldErrors
}, { status: 400 })
```

---

## 📥 Uploads

- Envio de contratos, vídeos e imagens para S3.
- Categorias devem ser organizadas no bucket.
- No caso de vídeo, o aluno só pode assistir se estiver autenticado.

---

## 🔐 Regras de Acesso

- **Aluno**: acessa `/painel/aulas`, mas só vê as aulas liberadas.
- **Admin**: acessa `/admin`, com autenticação por middleware.
- **Public Pages**: login, matrícula, recuperação de senha.

---

## ✅ Checklist para Início do Projeto

- [ ] Criar projeto Next.js com App Router
- [ ] Instalar dependências principais (`MUI`, `RHF`, `Zod`, `NextAuth`, `Supabase`, etc)
- [ ] Criar banco de dados no Supabase
- [ ] Configurar Auth no Supabase (email/senha)
- [ ] Criar estrutura de rotas conforme `/app`
- [ ] Criar modais de cadastro com formulário padronizado
- [ ] Criar lógica de matrícula com PDF + mock Asaas
- [ ] Implementar upload para S3 com categorias
- [ ] Garantir acesso restrito aos vídeos com autenticação
- [ ] Implementar player com ReactPlayer e Accordion de aulas

---

## 📘 Observações Finais

- Todos os formulários devem ser feitos com **React Hook Form + Zod**
- Usar `Controller` sempre que precisar integrar MUI
- Priorizar UX simples e objetivo
- Evitar reprocessamentos desnecessários: usar `SWR` e memoização
- Trabalhar em commits pequenos e com mensagens claras
