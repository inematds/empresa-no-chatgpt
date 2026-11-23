# Empresa no ChatGPT

[![Empresa no ChatGPT](guia/assets/banner.jpg)](https://inematds.github.io/empresa-no-chatgpt/guia/)

**🇧🇷 [Português](README.md) · 🇺🇸 [English](README.en.md) · 🇪🇸 [Español](README.es.md)**

🎬 Vídeo: https://www.youtube.com/watch?v=b76_IUEDREU

## O que é

**Empresa no ChatGPT** é um kit grátis para você reivindicar o perfil da sua empresa na **Central de Negócios do ChatGPT** (o "Business Hub", lançado pela OpenAI em novembro de 2026), igual ao perfil de empresa do Google. Serve para quem tem loja, consultório, escritório ou vende pela internet. Traz o teste do que o ChatGPT diz de você hoje, a ficha para preencher, as dicas do que deu errado com a gente e um pedido pronto para um agente de IA fazer o cadastro no seu navegador. À mão, precisa só de uma conta do ChatGPT, mesmo a grátis, e é de graça. O caminho com o agente é opcional e usa o Claude Code (Anthropic), que pede um plano pago do Claude e um computador com o Chrome.

**Você pode recuperar sua empresa no ChatGPT.** Hoje, quem responde por ela é o que ele achou por aí. Quando procuramos "INEMA", ele mostrou o instituto do meio ambiente da Bahia, com 6 endereços. Nenhum era o nosso.

## 📖 Guia de uso

Guia completo (landing + passo a passo, com as telas reais): **https://inematds.github.io/empresa-no-chatgpt/guia/**

## Os 3 passos

1. **Ache a sua empresa:** `chatgpt.com/business-hub` → pesquise o nome curto e o completo → escolha a do seu endereço ou **Criar**.
2. **Prove que é sua:** local físico = código no telefone comercial; loja online = um registro TXT no DNS do site (também há e-mail da empresa e Shopify).
3. **Diga quem você é:** categoria, descrição de até 300 caracteres, entrega, devolução → identidade (documento + selfie) → convide a equipe.

## Peça para o agente fazer (o pedido)

Opcional e pago à parte: cole no [Claude Code](https://claude.com/claude-code) com a extensão [Claude in Chrome](https://claude.com/claude-in-chrome) (ele usa o seu navegador já logado; pede um plano pago do Claude). Sem agente, siga os 3 passos acima à mão, de graça. O pedido completo e o que o agente fez no nosso caso estão em [`automatico/PEDIDO-AGENTE.md`](automatico/PEDIDO-AGENTE.md).

```text
Quero cadastrar a minha empresa no ChatGPT (Central de Negócios, chatgpt.com/business-hub).
Leia o kit em https://github.com/inematds/empresa-no-chatgpt (as dicas e a ficha do perfil).
Dados: nome <NOME>, <local físico ou loja online>, site <SITE>, endereço <ENDEREÇO>, o que fazemos <2 ou 3 frases>.
Use o meu navegador logado na conta da EMPRESA. Faça antes o teste do kit. Descrição com no máximo 300 caracteres.
Pare e me pergunte antes de aceitar termos, mexer no DNS, pôr telefone/e-mail público e enviar.
Código do telefone, documento e selfie são comigo. No fim, prints e o que falta.
```

## O kit

| Arquivo | Para quê |
|---|---|
| [`kit/1-teste-o-que-o-chatgpt-diz.md`](kit/1-teste-o-que-o-chatgpt-diz.md) | 8 perguntas para ver o que o ChatGPT diz de você + ficha antes/depois |
| [`kit/2-ficha-do-perfil.md`](kit/2-ficha-do-perfil.md) | todos os campos que o cadastro pede + modelo de descrição ≤ 300 caracteres |
| [`kit/3-dicas.md`](kit/3-dicas.md) | 19 dicas do nosso cadastro real (o que deu errado e como evitar) |
| [`automatico/PEDIDO-AGENTE.md`](automatico/PEDIDO-AGENTE.md) | como o agente fez o cadastro sozinho e o pedido para colar |
| [`kit/conferir_perfil.py`](kit/conferir_perfil.py) | confere a ficha: descrição ≤ 300 e nome/site/telefone iguais em todo lugar |
| [`automatico/txt_cloudflare.py`](automatico/txt_cloudflare.py) | cria o registro TXT no Cloudflare e espera aparecer no DNS público |
| [`automatico/conferir_txt.sh`](automatico/conferir_txt.sh) | confere se o TXT já aparece (qualquer provedor de DNS) |

## A prova (22/11/2026)

- **Antes:** "INEMATDS" → nenhuma empresa; "INEMA" → instituto do meio ambiente da Bahia, 6 endereços.
- **Depois:** INEMATDS LTDA criada pelo agente em ~10 minutos, loja online, **inema.club verificado** por DNS, categoria "Empresa de IA". Status: rascunho até a verificação de identidade do dono.
- Fonte do recurso: [OpenAI, "Empresas no ChatGPT"](https://openai.com/pt-BR/index/introducing-businesses-chatgpt/) (beta; regras podem mudar).

---

## Instalação técnica

Pré-requisitos: Python 3.10+, `dig` (pacote `dnsutils`/`bind-utils`) para conferir o DNS. Para criar o TXT pelo script: domínio no Cloudflare e um token com permissão *Zone → DNS → Edit*. Sem dependências externas.

```bash
git clone https://github.com/inematds/empresa-no-chatgpt && cd empresa-no-chatgpt

# conferir a ficha antes do cadastro
python3 kit/conferir_perfil.py kit/exemplo-ficha.json          # copie e edite o exemplo

# criar o TXT de verificação no Cloudflare (token só no ambiente, nunca no código)
export CLOUDFLARE_API_TOKEN=...
python3 automatico/txt_cloudflare.py --dominio seusite.com.br \
  --valor "openai-business-profile=ungi_..." --dry-run          # mostra o que vai fazer
python3 automatico/txt_cloudflare.py --dominio seusite.com.br \
  --valor "openai-business-profile=ungi_..."                    # cria e espera aparecer

# conferir o TXT em qualquer provedor
bash automatico/conferir_txt.sh seusite.com.br

# testes (10 casos: limite de 300, divergências de site/telefone, tipo local sem endereço, nome do TXT, dry-run)
python3 -m unittest discover -s tests
```

`txt_cloudflare.py` sai 0 quando o TXT aparece no DNS público, 1 em erro e 2 se criou mas ainda não propagou.

## O processo, do início ao fim

| # | Passo | Comando / onde | Quem |
|---|---|---|---|
| 1 | Teste do "antes" | 8 perguntas de `kit/1-teste-o-que-o-chatgpt-diz.md` no ChatGPT | 🧠 dono (ou o agente) |
| 2 | Ficha do perfil | `python3 kit/conferir_perfil.py minha-ficha.json` | 🧠 dono |
| 3 | Abrir a Central e pesquisar | `chatgpt.com/business-hub` no navegador logado na conta da empresa | 🤖 agente (Claude in Chrome) |
| 4 | Criar ou escolher a empresa | "Criar '<nome>'" → Começar → local físico / loja online | 🤖 agente |
| 5 | Provar que é sua | DNS: `python3 automatico/txt_cloudflare.py …` → "Verificar domínio" · telefone: código | ⚙️ script (com o sim do dono) / 🧠 dono |
| 6 | Categoria, descrição, entrega, devolução → Enviar | formulário | 🤖 agente (mostra antes de enviar) |
| 7 | Identidade (Persona: documento + selfie) | botão "Verificar" na visão geral | 🧠 dono |
| 8 | Convidar a equipe | "Convidar" na Central | 🧠 dono |
| 9 | Teste do "depois" (7 dias) | as mesmas 8 perguntas | 🧠 dono |

## Uso responsável

Reivindique só empresa que é sua ou que você representa. Não publique telefone ou e-mail que não podem receber cliente. O kit não promete posição nas respostas do ChatGPT.

Projetos e cursos gratuitos: https://inema.club · Licença MIT.
