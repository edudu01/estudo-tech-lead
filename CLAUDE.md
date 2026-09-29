# Guia de estudos — Pós Tech em Tech Management (FIAP)

Site estático e pessoal de estudos baseado na ementa da [Pós Tech em Tech Management da FIAP](https://postech.fiap.com.br/curso/tech-management/). Não é o material oficial das aulas: as explicações são resumos feitos a partir das fontes citadas em cada disciplina. Todo o conteúdo é em português (pt-BR).

## Estrutura

- `index.html`: página única (CSS e JS inline). Renderiza tudo a partir de `window.CURSO`: navegação lateral, busca, checkboxes de progresso, anotações, flashcards, tema claro/escuro e exportação em JSON. O progresso, as anotações e as estatísticas dos flashcards ficam no `localStorage` (`tm-done`, `tm-notes`, `tm-cards`, `tm-theme`).
- `glossario.js`: define `window.GLOSSARIO` como `[termo, definição (HTML), [IDs das disciplinas]]`. A página ordena os termos, agrupa por letra e cria os links "Veja em". A definição deve resumir o que já está na disciplina citada (as fontes ficam lá), e todo ID precisa existir.
- `fases/fase-0N.js`: conteúdo de cada fase (1 a 5). Cada arquivo faz `window.CURSO.push({...})` e é carregado por uma `<script>` comum. Não use ES modules, para a página continuar abrindo via `file://`.

Não há build nem dependências: para ver, abra o `index.html` no navegador.

## Formato dos dados (por fase)

```js
{ n, periodo, titulo, descricao,
  disciplinas: [{
    titulo, resumo,                       // resumo aceita HTML
    topicos: [{ t, c, k: [] }],           // t = título, c = HTML, k = pontos-chave (HTML)
    pratica,                              // HTML
    perguntas: [[pergunta, resposta]],
    aprofundamento: [[texto, url, descricao]],  // opcional: só materiais GRATUITOS
    referencias: [[texto, url]]           // url = "" quando não houver link
  }],
  challenge: { intro, entregas: [] } }
```

Os IDs das disciplinas vêm do título (`f{n}-{slug}`); os dos tópicos, do índice (`{id-da-disciplina}-{i}`); e os das perguntas (flashcards), também do índice (`{id-da-disciplina}-q{i}`). Renomear uma disciplina ou reordenar tópicos ou perguntas faz o progresso salvo desses itens se perder. Para acrescentar perguntas, coloque-as no fim da lista.

## Regras de conteúdo

- Padrão de cada tópico: o `c` traz a explicação e, em seguida, parágrafos `<p><b>Na prática:</b> …</p>` ou `<p><b>Exemplo:</b> …</p>` e `<p><b>Erros comuns:</b> …</p>`. Cada disciplina tem 8 perguntas, das quais pelo menos uma é de situação ("Situação: …").
- **Toda disciplina precisa de `referencias`**, com as fontes de onde o conteúdo saiu: livros, normas, leis, sites oficiais. Não acrescente conteúdo explicativo sem a fonte correspondente.
- Só inclua URLs que você tenha certeza de que existem, de preferência fontes oficiais. Na dúvida, cite a obra sem link.
- `aprofundamento` recebe apenas materiais gratuitos e legais, com links verificados por busca na web (nada de Scribd ou PDFs piratas).
- Siga a ementa da FIAP para fases, disciplinas e tópicos. Quando um termo for ambíguo (ex.: "IT41T" → IT4IT, "V4 Model" → C4 Model, "LAP"), diga isso no texto.
- Em temas regulatórios que mudam rápido (AI Act, PL 2338/2023, art. 19 do Marco Civil), avise para conferir a situação atual.
- Dentro das strings JS, escape aspas duplas (`\"`) e não quebre as strings em várias linhas.

## Validação

Não há `node` nesta máquina. Para validar, renderize com o Chrome headless e confira que aparecem as 23 disciplinas e o texto de progresso:

```bash
google-chrome --headless=new --disable-gpu --no-sandbox --virtual-time-budget=4000 \
  --dump-dom file:///opt/estudo-tech-lead/index.html | grep -o 'id="ptext">[^<]*'
```

Se aparecer `0 de N tópicos`, todos os arquivos carregaram. Um erro de sintaxe em algum `fase-0N.js` faz a fase sumir ou a página ficar em branco. Para checar só a sintaxe dos dados, dá para usar o `gjs`, que está instalado. Para uma checagem visual, use `--screenshot` com `--window-size=1300,1800` (desktop) e `390,1400` (celular).
