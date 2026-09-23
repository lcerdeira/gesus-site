# gesus-site — página de produto do GeSUS (gesus.com.br)

Página estática, uma única página, sem dependência de servidor. Existe para que
`gesus.com.br` esteja **sempre no ar**, independentemente da máquina de
homologação na AWS (que desliga às 02h e não tem certificado para esse nome).

## Conteúdo da pasta

```
index.html                 a página inteira (HTML + CSS embutido, sem JS)
assets/icon.svg            marca GeSUS (arco + linha de vida)
assets/favicon.ico         favicon 32px
assets/icon-32.png         favicon PNG
assets/icon-192.png        ícone 192 (PWA / Android)
assets/icon-512.png        ícone 512 (PWA / Open Graph)
assets/apple-touch-icon.png ícone 180 (iOS)
CNAME                      domínio do GitHub Pages: gesus.com.br
.nojekyll                  desliga o processamento Jekyll no Pages
```

Os ícones são os mesmos de `infosaude/gesus/core/gesus/web/`, reaproveitados sem
alteração, para que o site institucional e a plataforma tenham a mesma marca.

Para conferir localmente, basta abrir `index.html` no navegador — não há build.

## O que a página **não** contém (regra do projeto)

Nenhum nome de cliente, município ou secretaria; nenhum número de adoção;
nenhuma promessa de resultado (economia, aumento de repasse, nota); nenhum nome
de credor, hospital ou fornecedor. Toda a página fala do produto, não de casos.
Ao editar, mantenha essa regra — e mantenha o aviso
**"Solução privada da SyntenIA. Sem vínculo com o Ministério da Saúde."**,
que aparece duas vezes (seção "Como contratar" e rodapé).

## Publicar no GitHub Pages

**Já publicado em 23/09/2026:** repositório [lcerdeira/gesus-site](https://github.com/lcerdeira/gesus-site)
(público, como o Pages em conta gratuita exige), Pages ligado na `main` / raiz,
domínio `gesus.com.br` reconhecido pelo arquivo `CNAME`. Falta só o DNS (abaixo).

Os comandos que criaram o repositório, para referência:

```bash
# 1. criar o repositório público (a partir do conteúdo desta pasta)
cd /Users/lshlt19/GitHub/BRtribAI/gesus-site
git init -b main
git add .
git commit -m "Site institucional do GeSUS"

gh repo create lcerdeira/gesus-site --public --source=. --remote=origin --push
# (ou crie o repositório pela interface do GitHub e use git remote add origin ... && git push -u origin main)
```

```bash
# 2. ligar o Pages na branch main, pasta raiz
gh api -X POST repos/lcerdeira/gesus-site/pages \
  -f 'source[branch]=main' -f 'source[path]=/'
```

Ou, pela interface: **Settings → Pages → Source: Deploy from a branch →
`main` / `/ (root)` → Save**. Em seguida, em **Custom domain**, informe
`gesus.com.br` (o arquivo `CNAME` já traz esse valor; o GitHub vai reconhecê-lo).
Depois que o DNS propagar, marque **Enforce HTTPS** — o certificado é emitido
pelo próprio GitHub, de graça, e é isso que resolve o problema de certificado
que o domínio tem hoje.

Atualizações futuras: editar `index.html`, `git commit`, `git push`. O Pages
republica sozinho em cerca de um minuto.

## DNS no GoDaddy

Hoje `gesus.com.br` aponta direto para a máquina de homologação
(`18.228.83.236`). A troca abaixo tira **apenas o domínio raiz e o `www`** da
AWS, deixando **`app.gesus.com.br` intacto** apontando para a mesma máquina.

### Remover

| Tipo | Nome | Valor atual |
|------|------|-------------|
| A | `@` | `18.228.83.236` |
| A ou CNAME | `www` | (o que estiver lá hoje) |

### Criar

| Tipo | Nome | Valor | TTL |
|------|------|-------|-----|
| A | `@` | `185.199.108.153` | 600 |
| A | `@` | `185.199.109.153` | 600 |
| A | `@` | `185.199.110.153` | 600 |
| A | `@` | `185.199.111.153` | 600 |
| CNAME | `www` | `lcerdeira.github.io` | 600 |

Opcionalmente, para IPv6, quatro registros `AAAA` em `@`:
`2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`,
`2606:50c0:8003::153`.

### Manter como está

| Tipo | Nome | Valor | Observação |
|------|------|-------|------------|
| A | `app` | `18.228.83.236` | **não mexer** — é o acesso à plataforma na AWS |

Se `app` ainda não existir como registro próprio, crie-o **antes** de remover o
`A` do `@`, para não derrubar o acesso de homologação. Confirme depois com:

```bash
dig +short gesus.com.br        # deve responder os quatro IPs 185.199.x.153
dig +short www.gesus.com.br    # deve responder lcerdeira.github.io
dig +short app.gesus.com.br    # deve continuar respondendo 18.228.83.236
```

A propagação costuma levar de alguns minutos a algumas horas. Enquanto o GitHub
não conclui a emissão do certificado, o **Enforce HTTPS** fica desabilitado na
tela do Pages — é normal; basta voltar e marcá-lo depois.

> Ponta solta conhecida: o certificado TLS de `app.gesus.com.br` continua sendo
> problema da máquina da AWS. Esta página não resolve isso — ela só garante que
> o endereço principal, o que vai em material de divulgação, esteja sempre no ar.

## Contato

O botão "Solicitar demonstração" usa um `mailto:` para
`contato@syntenia.com.br`, com assunto e corpo pré-preenchidos. Foi a escolha
deliberada em vez do Formspree: não depende de conta, de domínio autorizado nem
de JavaScript, e funciona no primeiro dia em que o site sobe. Se no futuro
quiser um formulário de verdade, troque o bloco `.demo .actions` por um `<form
action="https://formspree.io/f/xnpnvbgb" method="POST">` e autorize
`gesus.com.br` nas configurações do formulário no Formspree.
