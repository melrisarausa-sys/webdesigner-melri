# Landing Page Profissional — Melri Siragusa

Versão com otimizações técnicas de performance e Meta Pixel (2026-10-06).

## ⚠️ O que mudou nesta versão (leia antes de publicar)

Até a versão anterior, a página era um único arquivo `index.html` com tudo embutido (imagens em base64). Nesta versão, para permitir as otimizações de velocidade que você pediu (WebP, lazy loading, cache do navegador), as imagens foram movidas para arquivos separados:

```
webdesigner-melri/
├── index.html
└── images/
    ├── logo.webp
    ├── hero-photo.webp
    ├── sobre-photo.webp
    ├── final-photo.webp
    ├── pc-renatatrindade.webp
    ├── pc-casaneuro.webp
    ├── pc-despertareterapia.webp
    ├── pc-fernandanonato.webp
    └── pc-rememora.webp
```

Isso significa que o processo de publicação mudou: antes era só colar o texto do `index.html` no editor do GitHub. Agora você precisa enviar a pasta `images/` também. O jeito mais simples é fazer isso pelo navegador, arrastando os arquivos — veja o passo a passo abaixo.

## Como publicar (passo a passo)

1. Acesse github.com/melrisarausa-sys/webdesigner-melri
2. **Apague os arquivos antigos** (se existir uma pasta `images` antiga ou um `index.html` antigo, confira se os nomes batem com os desta pasta — se os nomes dos arquivos dentro de `images/` forem diferentes dos listados acima, apague os antigos para não deixar lixo no repositório)
3. Clique em **"Add file" → "Upload files"** (no canto superior direito da lista de arquivos do repositório)
4. Arraste para dentro da janela do navegador: o arquivo `index.html` **e** a pasta `images` inteira (pode arrastar a pasta — o GitHub reconhece e sobe tudo que tem dentro, mantendo a estrutura `images/arquivo.webp`)
5. Role até o final da página e clique em **"Commit changes"** direto na branch `main`
6. O Vercel publica automaticamente em ~1 minuto
7. Confirme em webdesigner.siragusa.com.br (Ctrl+Shift+R para evitar cache antigo)
8. Depois de publicar, rode novamente o PageSpeed Insights para conferir a nova nota de performance mobile

**Importante:** o `index.html` referencia as imagens pelo caminho `images/nome-do-arquivo.webp` (caminho relativo). Por isso a pasta `images` precisa estar no mesmo nível do `index.html` no repositório — não dentro de outra pasta, não em outro lugar.

## O que foi feito nesta rodada (revisão técnica)

### 1. Performance mobile

- **Causa principal do LCP alto (~6,2s):** a página inteira era um único arquivo HTML de ~1 MB, com todas as imagens (incluindo a foto principal) embutidas em base64 dentro do próprio texto. Isso obrigava o navegador a baixar o arquivo quase inteiro antes de conseguir sequer começar a desenhar a foto principal — não havia como o navegador "adiantar" esse download. Além disso, o Google Fonts estava bloqueando a renderização da página até terminar de carregar.
- **O que foi corrigido:**
  - As imagens foram extraídas do HTML e convertidas para arquivos `.webp` separados (formato mais moderno e leve que JPEG/PNG), com compressão ajustada para manter a qualidade visual.
  - O tamanho de cada imagem foi reduzido para o tamanho real de exibição na tela (a logo, por exemplo, estava sendo carregada ~3,5x maior do que o necessário; a foto da seção "sobre", ~2,3x maior).
  - A logo (usada no topo e no rodapé) agora é um único arquivo compartilhado, em vez de duas cópias idênticas embutidas duas vezes.
  - A foto principal (a que aparece primeiro, ao lado do título) ganhou prioridade de carregamento (`fetchpriority="high"` + `preload` no `<head>`) para ser buscada pelo navegador o quanto antes, sem lazy loading — exatamente como você pediu.
  - As demais imagens (rodapé, seção "sobre", seção final) ganharam `loading="lazy"`, ou seja, só carregam quando o visitante rola a página até perto delas.
  - As miniaturas do carrossel de projetos foram mantidas com carregamento imediato (sem lazy) por uma razão técnica: esse carrossel roda uma animação automática contínua assim que a página carrega, e usar lazy loading nelas criava risco de a imagem aparecer em branco por uma fração de segundo durante a animação, antes de o navegador terminar de buscá-la. Como são arquivos pequenos (a soma das 5 miniaturas é bem leve), o ganho de performance de deixá-las "lazy" seria mínimo e o risco visual não compensava — mantive carregamento normal para garantir zero prejuízo visual, como você pediu.
  - Todas as imagens ganharam `width`/`height` definidos no HTML, o que evita que a página "pule" enquanto carrega (reduz layout shift).
  - O carregamento da fonte do Google (Bodoni Moda + Montserrat) deixou de travar a renderização da página: agora ela carrega em paralelo, sem bloquear a exibição do restante do conteúdo (técnica padrão `preload` + `onload` + fallback via `<noscript>`).
  - Foi removido CSS morto que não era mais usado por nenhum elemento da página (sobras de versões anteriores do design) — isso reduz o tamanho do arquivo sem qualquer efeito visual.
  - **Peso total da página:** caiu de ~1 MB (um único arquivo) para ~59 KB de HTML + ~280 KB de imagens otimizadas (~340 KB no total) — uma redução de aproximadamente 68%.
  - Também foi corrigido um bug estrutural que o `index.html` tinha (um envoltório `<html><head>...<body>` duplicado, provavelmente de uma edição anterior) que deixava o HTML tecnicamente inválido, embora os navegadores "consertassem" isso sozinhos ao exibir a página. Corrigido para HTML válido.

- **O que eu NÃO fiz, para não arriscar o visual:** não mexi em nenhum texto, cor, espaçamento, animação, hierarquia ou comportamento responsivo. Não troquei bibliotecas nem adicionei dependências pesadas (a página já não usava nenhuma). Testei a página em 6 larguras de tela diferentes (1920, 1440, 1366, 1024, 390 e 360px) depois de cada mudança para garantir que nada quebrou.

### 2. Meta Pixel (ID 394909628869399)

- **Onde está implementado:** no `<head>` do `index.html`, dentro do bloco padrão "Meta Pixel Code" fornecido pelo próprio Facebook/Meta — não havia implementação duplicada ou conflitante em nenhum outro lugar da página (conferido com busca por todas as ocorrências de `fbq(` no arquivo inteiro: existe exatamente 1 `init` e 1 `trackCustom`).
- **Onde o PageView dispara:** automaticamente, uma única vez, assim que a página carrega — faz parte do próprio código padrão do Pixel (`fbq('track', 'PageView')`), logo depois do `fbq('init', '394909628869399')`.
- **Quais botões disparam o evento `LandingPageWhatsApp`:** todos os links que levam ao WhatsApp são selecionados automaticamente por um único script (`a[href^="https://wa.me/"]`), então não é uma lista fixa de botões — qualquer link para WhatsApp na página é rastreado automaticamente, incluindo: botão da oferta especial, botão final "Quero conversar sobre meu projeto", link do rodapé e a bolha flutuante do WhatsApp (4 links no total atualmente).
- **Confirmação de que não há eventos duplicados:** cada link de WhatsApp recebe exatamente um `addEventListener('click', ...)`, e o evento só dispara no clique real do usuário — nunca ao carregar a página, nunca ao rolar, nunca ao simplesmente passar o mouse.
- **Sobre o aviso "instalado mas não disparou recentemente" da extensão do Meta:** o código do Pixel em si já estava correto antes desta revisão. A causa mais provável desse aviso era o bug estrutural do HTML mencionado acima (o envoltório duplicado), que pode ter interferido na forma como a extensão analisava a página. Com o HTML corrigido e validado, a implementação está limpa e deve ser reconhecida corretamente. De qualquer forma, o ideal é você testar ao vivo depois da publicação: abra a página publicada, clique em um botão de WhatsApp e confira no Gerenciador de Eventos do Meta (Events Manager) se o `PageView` e o `LandingPageWhatsApp` aparecem.

### 3. Confirmação final — nenhuma mudança visual

Comparei a página antes e depois em todas as larguras de tela testadas: mesmo layout, mesmas cores, mesmas fontes, mesmos espaçamentos, mesmas animações (carrossel de projetos, carrossel do hero, menu mobile, FAQ, reveal ao rolar, bolha flutuante do WhatsApp), mesma responsividade. Os botões antes da seção "Oferta especial" continuam levando para `#oferta` com rolagem suave; os da oferta e os finais continuam abrindo o WhatsApp.

## O que foi verificado antes da entrega

- Nenhum caminho local (sem `file://`, sem `/home/...`) — todo `src`/`href` é um arquivo relativo dentro do próprio pacote (`images/...`), um link `https://`, uma âncora interna (`#oferta` etc.) ou um `mailto:`.
- 8 seções com id (`sobre`, `dor`, `projetos`, `beneficios`, `para-quem`, `como-funciona`, `faq`, `oferta`) presentes e únicas.
- Pixel do Meta (`fbq('init', ...)`, `PageView`, `trackCustom LandingPageWhatsApp`) intacto, único, sem duplicação.
- Botões antes da seção "Oferta especial" (menu e hero) levam para `#oferta` com rolagem suave, na mesma aba.
- Botões da oferta, da seção final, do rodapé e a bolha flutuante continuam abrindo o WhatsApp.
- Zero overflow horizontal em 1920/1440/1366/1024/390/360px.
- Carrosséis (hero e projetos), menu mobile e FAQ testados e funcionando.
- Todas as 9 imagens carregam corretamente, nos tamanhos certos, com lazy loading apenas onde é seguro.
- Logo do rodapé, texto do botão final centralizado e demais ajustes aprovados anteriormente preservados exatamente como estão.
