# Clube de Vantagens — Template White-Label

## Deploy no Vercel
1. Crie um repositório novo no GitHub e suba só o `index.html` (na raiz).
2. Entre no Vercel → "Add New Project" → importe o repositório.
3. Não precisa configurar nada — o Vercel detecta como site estático e publica direto.

## Personalizar pra cada cliente
Abra o `index.html`, vá até o bloco `CONFIG` no final do arquivo (perto da tag `<script>`) e troque:
- `brand`: nome da marca do cliente (aparece no topo, no cartão e no rodapé)
- `city`: cidade/região do cliente
- `whatsapp`: número de contato no formato 5575999999999
- `monthlySavings`: valor do contador de economia

Pra trocar as cores, edite as variáveis no topo do `<style>` (`--navy`, `--gold`, `--coral`, `--river`).

Pra trocar os parceiros de exemplo pelos reais do cliente, edite o array `partners` no `<script>`.

## Painel de administração (admin.html)
Agora existe uma página separada — `admin.html` — onde você (ou o cliente) loga como administrador e cadastra/remove parceiros de verdade. Isso aparece automaticamente no site, sem editar código.

Passos:
1. No `admin.html`, preencha `supabaseUrl` e `supabaseAnonKey` (os mesmos do `index.html`).
2. Em `adminEmails`, coloque o e-mail que vai administrar (pode ser o seu, ou o do cliente).
3. No Supabase, vá em **Authentication → Users** e crie manualmente esse usuário admin (ou crie pelo próprio cadastro do site e depois promova ele).
4. Rode esse SQL adicional no **SQL Editor**:

```sql
create table public.partners (
  id uuid default gen_random_uuid() primary key,
  cat text not null,
  name text not null,
  description text not null,
  tag text not null,
  created_at timestamptz default now()
);

alter table public.partners enable row level security;

create policy "qualquer pessoa pode ver parceiros" on public.partners
  for select using (true);

create policy "admin pode inserir parceiros" on public.partners
  for insert with check (auth.jwt() ->> 'email' in ('seu-email@exemplo.com'));

create policy "admin pode remover parceiros" on public.partners
  for delete using (auth.jwt() ->> 'email' in ('seu-email@exemplo.com'));
```

Troque `'seu-email@exemplo.com'` pelo e-mail real do admin (tem que ser o mesmo que está em `adminEmails` no `admin.html`).

5. Suba o `admin.html` no mesmo repositório do `index.html` (na raiz, junto). Depois do deploy, o painel fica em `seusite.vercel.app/admin.html`.

Enquanto nenhum parceiro for cadastrado, o site mostra automaticamente os exemplos (farmácia, restaurante, etc.) — assim nunca fica vazio.

## Pra ativar o login com conta (Supabase — gratuito)
A página já tem todo o código de login/cadastro pronto. Falta só conectar a um banco de dados real, que é de graça pra começar:

1. Crie uma conta em https://supabase.com e um novo projeto.
2. Em **Project Settings → API**, copie a **Project URL** e a **anon public key**.
3. No `index.html`, ache o bloco `CONFIG` e cole esses dois valores em `supabaseUrl` e `supabaseAnonKey`.
4. No Supabase, vá em **SQL Editor** e rode:

```sql
create table public.members (
  id uuid references auth.users on delete cascade primary key,
  name text not null,
  card_number text not null,
  created_at timestamp with time zone default now()
);

alter table public.members enable row level security;

create policy "select own profile" on public.members
  for select using (auth.uid() = id);

create policy "insert own profile" on public.members
  for insert with check (auth.uid() = id);
```

5. Em **Authentication → Providers**, deixe "Email" ativado (já vem por padrão).
6. Em **Authentication → Settings**, se quiser pular a confirmação por e-mail (mais rápido pra testar), desative "Confirm email".

Depois disso, o botão "Entrar" abre o cadastro/login de verdade: a pessoa cria conta, recebe um número de cartão gerado automaticamente, e ao voltar no site já entra logada.

## O que tem na página
- Cartão de membro animado (visual de cartão físico com efeito de brilho)
- Busca de categorias/parceiros
- 9 categorias de desconto
- Grade de parceiros em destaque
- Seção "como funciona"
- Chamada para empresas que querem entrar no clube (WhatsApp)
