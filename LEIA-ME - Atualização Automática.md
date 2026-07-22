# 🔄 Atualização automática do painel da Brasa

Hoje o painel (https://jc-bolettoimoveis.github.io/dashboard_Brasa_Church/) lê um arquivo
`dados.json` que você precisa **gerar e subir toda semana**. Este pacote acaba com esse trabalho:
um robô do GitHub lê a sua planilha do Google sozinho, calcula tudo (inclusive o clima) e
reescreve o `dados.json`. Você passa a **só preencher a planilha**.

O painel (`index.html`) **não muda** — ele continua lendo o `dados.json`, que agora é atualizado sozinho.

---

## Passo 1 — Deixar a planilha "legível pelo link" (30 segundos)

O robô precisa conseguir abrir a planilha sem senha.

1. Abra a planilha **BC Contagem 2026** no Google Sheets.
2. Botão **Compartilhar** (canto superior direito).
3. Em **Acesso geral**, troque para **"Qualquer pessoa com o link"** e deixe como **Leitor**.
4. **Concluir**.

> São só números de contagem, sem dados pessoais — pode deixar como leitura pública tranquilamente.
> (Se preferir manter privada, me avise que uso um método com senha; dá um pouco mais de trabalho.)

## Passo 2 — Adicionar os arquivos ao repositório

No repositório `dashboard_Brasa_Church` (o mesmo que já está no ar), **suba estes itens**,
mantendo os nomes das pastas (substitua os que já existirem):

```
index.html                           (NOVA versão do painel — agora com a seção Kids)
dados.json                           (dados já prontos, com Kids — o robô reescreve depois)
.github/workflows/atualizar.yml      (o robô — quando e o que rodar)
build/gerar_dados.py                 (lê a planilha, calcula, gera o dados.json)
build/fatores.json                   (feriados, Copa e dias no Centro de Eventos — editável)
build/kids.json                      (presença de crianças por domingo — só números)
requirements.txt                     (bibliotecas que o robô usa)
```

Pelo site do GitHub: no repositório → **Add file → Upload files** → arraste o `index.html`,
o `dados.json`, o `requirements.txt` e as pastas `.github` e `build` → **Commit changes**.
(Ao subir `index.html`, ele substitui o antigo — pode confirmar.)

> ⚠️ **Não** precisa subir `docs_preview_dados.json` nem a pasta `__pycache__` (sobras de teste).

## Passo 3 — Ligar e rodar a primeira vez

1. No repositório, abra a aba **Actions**. Se pedir, clique em *I understand my workflows, enable them*.
2. Clique no workflow **"Atualizar painel Brasa"** → botão **Run workflow** → **Run**.
3. Aguarde ~1 minuto (fica verde ✓). Ele lê a planilha, busca o clima e regrava o `dados.json`.
4. Abra o painel e confira. Pronto!

A partir daí ele roda **sozinho toda segunda-feira de manhã**. E sempre que você fizer uma
contagem nova e quiser ver na hora, é só ir em **Actions → Run workflow** (um clique).

---

## Como manter

- **Nova contagem**: preencha na planilha do jeito que você já faz (as abas por horário).
  O robô soma sozinho — não precisa mexer em mais nada.
- **Feriado / jogo / culto no Centro de Eventos**: edite `build/fatores.json` (tem exemplos dentro)
  e suba a alteração. Poucas vezes por ano.
- **Kids (crianças)**: os números ficam em `build/kids.json`, no formato
  `"2026-07-19": {"total": 314, "vis": 2, "byhour": {"9":109,"11":109,"16":0,"18":46,"20":50}}`.
  Para um domingo novo, adicione uma linha assim (copiando do relatório do Kids). Se preferir,
  me manda o PDF do Kids que eu devolvo a linha pronta — ou montamos uma automação só pra ele depois.
- O **clima** é buscado automaticamente — não precisa mexer.
- **Horário do robô**: dá para mudar em `.github/workflows/atualizar.yml` (linha `cron`).
  Hoje: toda segunda, 09h de Brasília.

## Como funciona (resumo)

1. Baixa a planilha como Excel pelo link público do Google.
2. Lê as abas de cada culto e soma o total de cada domingo (mesma estrutura que você já usa).
3. Busca o clima hora a hora de Porto Alegre (Open-Meteo, gratuito) para cada data.
4. Junta feriados / Copa / local (do `fatores.json`).
5. Escreve o `dados.json` e faz o commit — o painel mostra na próxima abertura.

Testado contra a planilha real: 24 domingos, 98.992 presenças, tudo batendo.
