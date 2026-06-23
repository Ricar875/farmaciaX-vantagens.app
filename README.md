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

## O que tem na página
- Cartão de membro animado (visual de cartão físico com efeito de brilho)
- Busca de categorias/parceiros
- 9 categorias de desconto
- Grade de parceiros em destaque
- Seção "como funciona"
- Chamada para empresas que querem entrar no clube (WhatsApp)
-
