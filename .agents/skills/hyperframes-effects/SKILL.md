---
name: hyperframes-effects
description: Criar efeitos de vídeo para Reels usando HyperFrames + Claude Code. Use quando o usuário pedir efeitos de vídeo, animações para Reels, efeitos de camada, transições cinematográficas, ou mencionar HyperFrames, Digital Zoom, Orbit, Couch Wall, Glass Type, Cover Story, Chart Grow, Paper Cut, ou qualquer um dos 17 efeitos. Também use para setup do ambiente (Node.js, FFmpeg, HyperFrames) e para adaptar efeitos ao nicho de cabeleireiros.
metadata:
  version: 1.0.0
  source: "Os 17 efeitos de vídeo que o Claude cria — fabiano.app"
---

# HyperFrames — 17 Efeitos de Vídeo

Motor open-source da HeyGen (Apache 2.0, sem custo de render). Escreve a animação em HTML controlado pelo tempo, abre cada quadro no Chrome sem janela e junta tudo com o FFmpeg em MP4.

---

## Setup (uma vez só)

```bash
# 1. Instalar Node.js 22+ e FFmpeg
# Windows/Linux: nodejs.org/en/download e ffmpeg.org/download.html
# Mac: brew install node ffmpeg
node --version   # 22, 24 ou 26
ffmpeg -version

# 2. Instalar o plugin HyperFrames no Claude Code
claude plugin marketplace add heygen-com/hyperframes
claude plugin install hyperframes@hyperframes

# 3. Iniciar projeto (um por Reel)
npx hyperframes init meu-reel
cd meu-reel
# copie o vídeo para assets/bruto.mp4

# 4. Transcrever (Whisper local, sem chave, entende português)
npx hyperframes transcribe assets/bruto.mp4 --model small
# gera transcript.json com o tempo de cada palavra

# 5. Fazer o recorte (uma vez por vídeo — destrava 11 dos 17 efeitos)
npx hyperframes remove-background assets/bruto.mp4 -o assets/eu.webm --background-output assets/fundo.webm --quality best
# gera: assets/eu.webm (você com fundo transparente)
#       assets/fundo.webm (placa do fundo com buraco no seu formato)

# Pré-visualização (atualiza sozinha a cada mudança)
npx hyperframes preview

# Render final
npx hyperframes render --output final.mp4
# Só os efeitos com fundo transparente (para CapCut/Premiere)
npx hyperframes render --format webm --output efeitos.webm
npx hyperframes render --format mov --output efeitos.mov
```

**Ordem das camadas para efeitos atrás de você:**
1. `assets/fundo.webm` (placa com buraco)
2. O efeito
3. `assets/eu.webm` por cima (nunca cobre o rosto)

---

## Os 7 Prompts de Edição

### Prompt 1 — Base (cole antes de qualquer efeito)

```
Você é o meu editor de motion. Vou pedir efeitos de edição para vídeo vertical de Reels, um por vez, usando a HyperFrames. Regras para todos:

1. Formato 1080 por 1920, 30 quadros por segundo, duração que eu disser
2. Animação controlada pelo tempo, que dá para pular para qualquer segundo e sempre mostrar o mesmo quadro. Nada que dependa do relógio do navegador
3. Cada efeito entra na palavra que ele ilustra, pelo tempo do transcript.json, nunca antes
4. Quando o efeito põe alguma coisa atrás de mim, use 3 camadas: assets/fundo.webm embaixo, o efeito no meio e assets/eu.webm por cima. Nada passa na frente do meu rosto
5. Antes de construir do zero, procure no catálogo da HyperFrames um bloco que faça o mesmo e me diga qual vai usar
6. Movimento com mola ou curva suave, nunca linear. Texto em português, sem travessão, longe do rodapé e da lateral direita, onde ficam os botões do Instagram
7. A minha marca: cores [hex], fonte [nome], estilo [ex.: limpo, escuro, amarelo com branco]

Confirme as regras e espere o primeiro efeito.
```

### Prompt 2 — Recorte

```
Rode o recorte do meu vídeo:

npx hyperframes remove-background assets/bruto.mp4 -o assets/eu.webm --background-output assets/fundo.webm --quality best

Depois:
1. Monte uma prévia de 3 segundos com a placa do fundo, um retângulo amarelo no meio e o recorte por cima, para eu ver se o retângulo fica atrás de mim
2. Tire 4 quadros da prévia e confira a borda do cabelo, dos ombros e das mãos
3. Se aparecer halo de cor ou borda piscando, me diga em que segundo e o que sugere: outro trecho, menos movimento ou outra ferramenta de recorte
```

### Prompt 3 — Edição completa no tempo da fala

```
Este é o meu vídeo [assets/bruto.mp4] e a transcrição com o tempo de cada palavra [transcript.json]. O recorte está em assets/eu.webm e a placa do fundo em assets/fundo.webm.

Monte a edição com estes efeitos, no tempo da fala:
[cole os nomes, ex.: Digital Zoom, Chart Grow, Paper Cut, Pull-Back Reveal, Glass Type]

1. Me devolva primeiro a tabela: segundo de início e fim, a frase falada, o efeito e o bloco do catálogo que vai usar, se tiver
2. Cada efeito começa na palavra que ele ilustra
3. No máximo 1 efeito novo a cada 2 segundos, e a legenda o vídeo inteiro
4. Depois da minha aprovação, construa e abra a prévia
```

### Prompt 4 — Procurar no catálogo

```
Antes de construir o efeito [nome do efeito], procure no catálogo da HyperFrames:

npx hyperframes catalog --query "[o que o efeito faz, em inglês ou português]"

1. Me mostre os 3 blocos ou componentes mais próximos, com o nome, o que fazem e o link da prévia
2. Diga qual você usaria e o que vai mudar nele para ficar igual ao que eu pedi
3. Só instale com npx hyperframes add depois do meu ok
```

### Prompt 5 — Revisão antes do render

```
Antes do render final, revise a edição como um editor chato:

1. Tire uma folha com 4 quadros por segundo do vídeo inteiro
2. Para cada efeito, confira: entrou na palavra certa? Algum texto cortado, encostando na borda ou em cima da minha boca? O recorte tem halo ou borda piscando? Alguma coisa que devia ficar atrás de mim passou na frente?
3. Dê uma nota de 0 a 10 por efeito e corrija o que ficou abaixo de 8
4. Me mostre a folha de novo depois das correções
```

### Prompt 6 — Versão para gravar a tela (sem recorte)

```
Versão para gravar a tela: faça o efeito [nome] como uma página HTML só, com fundo verde puro #00FF00, em 1080 por 1920 ajustado para caber na janela, com um botão Começar que some quando clicado e dispara a animação, e um botão Repetir no fim.

Os textos: [textos]. As cores: [hex].
```

### Prompt 7 — Virar modelo de edição

```
Esta edição ficou aprovada. Transforme em modelo:

1. Salve a lista de efeitos, a ordem, a duração e as cores num arquivo modelo.md nesta pasta
2. Deixe como variável tudo que muda de um vídeo para o outro: o vídeo, as palavras de entrada, os textos e os números
3. Escreva em 5 linhas como eu faço o próximo Reel trocando só o vídeo e os textos
```

---

## Os 17 Efeitos (com prompt INSTALL de cada um)

> Todos os prompts abaixo vão após o Prompt 1 (base). Troque os [colchetes] pelo seu conteúdo. Os marcados com **recorte** usam `assets/eu.webm` e `assets/fundo.webm` do passo 5.

### 1. Orbit 🔄 **recorte**
Cartões com miniaturas orbitam em volta de você numa elipse: os que passam por trás somem atrás do seu corpo, os da frente passam por cima.

```
INSTALL: Efeito Orbit: 4 cartões verticais com [prints ou miniaturas, ex.: os 4 últimos posts] orbitam em volta de mim numa elipse levemente inclinada, uma volta a cada 3 segundos. Os cartões que passam por trás ficam entre assets/fundo.webm e assets/eu.webm, e os da frente passam por cima de mim, sem cobrir o rosto. Entra na palavra "[palavra]" e dura [segundos]. Veja antes os blocos three-orbiting-cards e orbit-card do catálogo.
```
Catálogo: https://hyperframes.heygen.com/catalog/components/three-orbiting-cards

---

### 2. Couch Wall 🖼️ **recorte**
A parede atrás de você se enche de quadros pendurados com miniaturas dos seus vídeos, cada uma com o número de visualizações embaixo, entrando uma por vez.

```
INSTALL: Efeito Couch Wall: na parede atrás de mim entram [3 a 6] quadros pendurados com [miniaturas dos meus vídeos], cada um com o número de visualizações embaixo, [ex.: 1,07 mi]. Um por vez, a cada 0,15 segundo, com um pop curto e um balanço leve, como se fossem pendurados na hora. Ficam entre assets/fundo.webm e assets/eu.webm, nunca na frente do meu rosto. Entra na palavra "[palavra]".
```
Guia recorte: https://hyperframes.heygen.com/guides/remove-background

---

### 3. UI World 💻
O vídeo vira parte de um app: em cima, uma janela de chat com área de soltar arquivos onde caem miniaturas, embaixo você continua falando.

```
INSTALL: Efeito UI World: na metade de cima, uma janela clara de app de IA desce com uma área tracejada escrita "[Solte os clipes aqui]", e [5] miniaturas de clipes caem nela uma a uma, a cada 0,12 segundo. Na metade de baixo, o meu vídeo continua. Na palavra "[palavra]", ela aparece grande no meio, em caixa alta. Interface recriada em HTML, sem print e sem logo de outra empresa.
```
Catálogo: https://hyperframes.heygen.com/catalog/blocks/claude-exchange

---

### 4. Frame Break 🖼️ **recorte**
O vídeo fica preso dentro da moldura de um post, sobre fundo escuro, e na palavra certa você sai pela borda enquanto o resto continua lá dentro.

```
INSTALL: Efeito Frame Break: o meu vídeo aparece dentro de uma moldura de post de Reels, com nome de perfil e ícones de curtir e comentar, sobre fundo escuro. Na palavra "[palavra]", o meu recorte (assets/eu.webm) passa por cima da borda da moldura, como se eu saísse da tela, enquanto o fundo do vídeo continua preso dentro dela, e a moldura dá um tranco de 3%. Duração [segundos].
```
Guia recorte: https://hyperframes.heygen.com/guides/remove-background

---

### 5. Digital Zoom 🔍
Um zoom digital rápido no seu rosto na palavra de ênfase, com cantoneiras de câmera e um REC no canto, e depois volta.

```
INSTALL: Efeito Digital Zoom: na palavra "[palavra]", um punch-in no meu rosto de 100% para 160% em 0,25 segundo, com curva rápida no começo e suave no fim, centralizado no rosto sem cortar a testa. Cantoneiras brancas de câmera nos 4 cantos e um REC vermelho piscando no canto de cima. Volta para 100% em [segundos].
```
Catálogo: https://hyperframes.heygen.com/catalog/components/push-in

---

### 6. Phone Mockup 📱
Um celular entra do lado do seu rosto mostrando a tela de um app, como uma conversa com a IA ou o seu produto.

```
INSTALL: Efeito Phone Mockup: um celular de borda fina entra da direita com mola e para ao lado do meu rosto, levemente inclinado, mostrando [a tela: uma conversa com a pergunta "[pergunta]" e a resposta "[resposta]", ou o meu app]. As mensagens aparecem uma de cada vez. Entra na palavra "[palavra]" e sai pela direita em [segundos].
```
Catálogo: https://hyperframes.heygen.com/catalog/components/device-frame-stage

---

### 7. Checklist ✅
Um painel escuro translúcido com os passos de um processo, marcando cada um na hora em que você fala.

```
INSTALL: Efeito Checklist: no topo, um painel escuro translúcido com o título "[PASSO A PASSO]" e o contador "1 de [3]". Os passos "[passo 1]", "[passo 2]" e "[passo 3]" entram um embaixo do outro, e o check de cada um se desenha na palavra em que eu falo dele, com o contador subindo junto.
```
Catálogo: https://hyperframes.heygen.com/catalog/components/marker-checklist-card

---

### 8. Headline Wall 📰 **recorte**
Bilhetes de papel com manchetes curtas aparecem pregados na parede atrás de você, um a um, levemente tortos.

```
INSTALL: Efeito Headline Wall: [5] bilhetes de papel com manchetes curtas, "[manchete 1]", "[manchete 2]" e assim por diante, aparecem pregados na parede atrás de mim, um a cada 0,15 segundo, levemente tortos, com alfinete e sombra suave. Ficam entre assets/fundo.webm e assets/eu.webm. Entra na palavra "[palavra]".
```
Guia recorte: https://hyperframes.heygen.com/guides/remove-background

---

### 9. Zoom-Through Cut ⚡
A câmera mergulha num ponto do quadro, com desfoque de movimento, e sai do outro lado já na próxima cena.

```
INSTALL: Efeito Zoom-Through Cut: na palavra "[palavra]", a câmera mergulha num ponto do quadro, [ex.: o centro da minha camiseta], escalando até 7 vezes com desfoque de movimento em 0,3 segundo, e sai do outro lado já no trecho seguinte, [segundo de início], desacelerando até parar.
```
Catálogo: https://hyperframes.heygen.com/catalog/components/zoom-through-transition

---

### 10. Chart Grow 📈 **recorte**
Um gráfico com os dias da semana se desenha subindo atrás de você, com uma etiqueta de crescimento no fim.

```
INSTALL: Efeito Chart Grow: atrás de mim, um gráfico de linha com os dias da semana embaixo, S, T, Q, Q, S, S e D, se desenha da esquerda para a direita subindo, com um ponto acendendo em cada dia e a etiqueta "[ESCALA]" no ponto final. Use os meus números reais, [números], ou nenhum número. Fica entre assets/fundo.webm e assets/eu.webm. Entra na palavra "[palavra]".
```
Catálogo: https://hyperframes.heygen.com/catalog/blocks/mk-line-graph

---

### 11. Pull-Back Reveal 🎬
O seu vídeo em tela cheia recua e revela que estava dentro de uma tela maior, como um app de edição com a timeline embaixo.

```
INSTALL: Efeito Pull-Back Reveal: começa com o meu vídeo em tela cheia. Na palavra "[palavra]", a câmera recua com desaceleração em 0,8 segundo e revela que o vídeo está dentro de [um app de edição, com a lista Cortes, Legenda, Zoom e Trilha do lado e a timeline embaixo, ou o meu produto]. Interface recriada em HTML.
```
Catálogo: https://hyperframes.heygen.com/catalog/components/pull-back-reveal

---

### 12. Green Screen 🟩 **recorte**
O fundo atrás de você some e dá lugar a outra cena, sem você ter gravado com tela verde.

```
INSTALL: Efeito Green Screen: na palavra "[palavra]", o fundo atrás de mim some num corte de 0,2 segundo e dá lugar a [a página do meu site, a tela do meu app ou uma cor sólida com o texto tal]. Use só assets/eu.webm por cima da nova cena, sem a placa do fundo. Mantenha a minha sombra suave no chão para não parecer colado.
```
Guia recorte: https://hyperframes.heygen.com/guides/remove-background

---

### 13. Paper Cut ✂️ **recorte**
Você vira uma figurinha recortada com borda branca, sobre papel quadriculado, com rabiscos de marca-texto e letras de bilhete de jornal.

```
INSTALL: Efeito Paper Cut: o meu recorte vira uma figurinha com borda branca de 12px e sombra, sobre papel quadriculado claro. Em volta, rabiscos de marca-texto [amarelo, azul e rosa], X e espirais desenhados a mão, e letras recortadas de jornal formando "[PALAVRA OU NÚMERO, ex.: 100%]", uma letra por vez, cada uma com fundo e fonte diferentes. Entra na palavra "[palavra]".
```
Catálogo: https://hyperframes.heygen.com/catalog/blocks/freeze-frame-dressing

---

### 14. Doodle Boil ✏️ **recorte**
Um contorno branco desenhado a mão em volta de você e rabiscos perto da cabeça, todos tremendo como animação feita a mão.

```
INSTALL: Efeito Doodle Boil: o fundo escurece um pouco, um contorno branco desenhado a mão aparece em volta do meu recorte e [3 ou 4] rabiscos, [ex.: uma claquete, um coração e uma estrela], surgem perto da minha cabeça. Contorno e rabiscos trocam de pose a cada 3 quadros, tremendo como animação feita a mão, sempre com a mesma sequência. Entra na palavra "[palavra]".
```
Catálogo: https://hyperframes.heygen.com/catalog/components/hw-boil

---

### 15. Vertigo 🌀 **recorte**
O efeito de dolly zoom do cinema: o fundo se afasta ou se aproxima enquanto você fica exatamente do mesmo tamanho.

```
INSTALL: Efeito Vertigo: na palavra "[palavra]", em 1 segundo, a placa do fundo (assets/fundo.webm) vai de 150% para 100%, ou o contrário, enquanto o meu recorte (assets/eu.webm) fica exatamente do mesmo tamanho e no mesmo lugar, com uma vinheta escurecendo as bordas. Legenda grande no peito, "[frase curta]", com a última palavra em destaque.
```
Catálogo: https://hyperframes.heygen.com/catalog/blocks/camera-dolly-zoom

---

### 16. Cover Story 📖 **recorte**
O vídeo vira capa de revista: um título serifado enorme atrás da sua cabeça e chamadas pequenas nas laterais.

```
INSTALL: Efeito Cover Story: o vídeo vira capa de revista. O título "[EDIÇÃO]" em letra serifada enorme, estilo Bodoni, entra letra por letra atrás da minha cabeça, entre assets/fundo.webm e assets/eu.webm, cobrindo a largura toda. Chamadas pequenas nas laterais, "[chamada 1]" e "[chamada 2]", e a data no canto. Entra na palavra "[palavra]".
```
Guia recorte: https://hyperframes.heygen.com/guides/remove-background

---

### 17. Glass Type 🔮 **recorte**
Uma palavra em letras grandes de vidro aparece atrás de você, com um reflexo de luz passando.

```
INSTALL: Efeito Glass Type: a palavra "[PALAVRA]" em letras grandes de vidro transparente, com borda clara, leve distorção do fundo e um reflexo de luz passando da esquerda para a direita, aparece atrás de mim, entre assets/fundo.webm e assets/eu.webm. Entra na palavra "[palavra]" com as letras subindo 20px e ganhando nitidez.
```
Catálogo: https://hyperframes.heygen.com/catalog/blocks/glass-hero

---

## +14 Efeitos Prontos no Catálogo

Instale com `npx hyperframes add <nome>` e peça para o Claude encaixar na palavra certa.

| # | Nome | O que faz | Instalar |
|---|------|-----------|---------|
| 18 | Torn Paper Rip | Rasgo de papel atravessa a tela, folha levanta com sombra e revela a cena seguinte | `npx hyperframes add torn-paper-rip` |
| 19 | Paper Collage Reveal | Mosaico de pedaços de papel rasgado voa para os lados e revela o que está embaixo | `npx hyperframes add paper-collage-reveal` |
| 20 | Whip Pan Cut | Chicotada lateral de câmera com desfoque, próxima cena entra na mesma direção | `npx hyperframes add whip-pan-cut` |
| 21 | Match Cut | Círculo acelera até uma troca de cena de um quadro só e continua encaixado | `npx hyperframes add match-cut` |
| 22 | Iris Reveal | Círculo se abre a partir de um ponto e revela a nova cena | `npx hyperframes add iris-reveal` |
| 23 | Ink Bleed Reveal | Tinta líquida se espalha no papel e recolhe revelando uma marca nítida | `npx hyperframes add ink-bleed-reveal` |
| 24 | Chromatic Aberration Wipe | Passada rápida com as cores separando em vermelho, verde e azul no pico | `npx hyperframes add chromatic-aberration-wipe` |
| 25 | Grade Split Reveal | Mesma imagem crua e tratada com borda passando — antes e depois de cor | `npx hyperframes add grade-split-reveal` |
| 26 | Marker Highlight | Traço de marcador, grifo, círculo ou sublinhado passa na palavra de ênfase | `npx hyperframes add marker-highlight` |
| 27 | Callout Circle | Elipse desenhada a mão envolve um ponto da tela com seta curva e rótulo | `npx hyperframes add hw-callout-circle` |
| 28 | Typed Prompt | Linha de prompt se digita no ritmo de uma pessoa, com cursor piscando | `npx hyperframes add typed-prompt` |
| 29 | Split Flap Board | Painel de aeroporto: cada letra gira pelo alfabeto até parar na certa | `npx hyperframes add split-flap-board` |
| 30 | Headline Slam | Manchete pesada desce na tela, pousa com tremor de 3 quadros e sai num chicote | `npx hyperframes add headline-slam` |
| 31 | Instagram Follow | Cartão de perfil do Instagram com botão de seguir, para o fim do vídeo | `npx hyperframes add instagram-follow` |

---

## Checklist antes de postar

- [ ] Cada efeito entra na palavra que ele ilustra (conferido pela transcrição)
- [ ] Nada passa na frente do rosto nem da boca
- [ ] O recorte não tem halo de cor nem borda piscando no cabelo e nas mãos
- [ ] Nenhum texto encosta na borda nem fica embaixo dos botões do Instagram
- [ ] Número em gráfico ou cartão é número real seu, ou não tem número
- [ ] O vídeo visto no celular, sem som, ainda faz sentido

---

## Para o nicho de cabeleireiros

| Tipo de conteúdo | Efeitos recomendados |
|-----------------|---------------------|
| Resultado antes/depois | Grade Split Reveal (#25) + Digital Zoom (#5) |
| Tutorial de técnica (ex.: mechas) | Checklist (#7) + Phone Mockup (#6) |
| Prova social / crescimento | Chart Grow (#10) + Couch Wall (#2) |
| Autoridade de marca | Cover Story (#16) + Glass Type (#17) |
| Destaque de palavra-chave | Marker Highlight (#26) + Digital Zoom (#5) |
| Transição entre cenas | Zoom-Through Cut (#9) ou Whip Pan Cut (#20) |

**Dica:** escolha 4–5 efeitos e repita em todo Reel — é isso que vira identidade visual. Comece pelo Digital Zoom (sem recorte) e pelo Glass Type ou Paper Cut (parecem edição cara).

---

## Links

- GitHub: https://github.com/heygen-com/hyperframes
- Primeiros passos: https://hyperframes.heygen.com/quickstart
- Catálogo: https://hyperframes.heygen.com/catalog
- Guia do recorte: https://hyperframes.heygen.com/guides/remove-background
- Todos os comandos: https://hyperframes.heygen.com/packages/cli
