# Landing Page Profissional — Melri Siragusa

Versão final aprovada (2026-10-06).

## Conteúdo deste pacote

- `index.html` — página completa e autossuficiente: todo o CSS, todo o JavaScript e todas as imagens/logo (embutidas em base64) estão dentro deste único arquivo. Não há arquivos externos de CSS, JS ou imagem — nada para esquecer de subir.

## Como publicar

1. Acesse github.com/melrisarausa-sys/webdesigner-melri
2. Abra o arquivo `index.html` do repositório
3. Clique no ícone de lápis (editar)
4. Selecione tudo (Ctrl+A / Cmd+A) e apague
5. Cole o conteúdo do `index.html` deste pacote
6. Role até o final e clique em "Commit changes" direto na branch `main`
7. O Vercel publica automaticamente em ~1 minuto
8. Confirme em webdesigner.siragusa.com.br (Ctrl+Shift+R para evitar cache antigo)

## O que foi verificado antes da entrega

- Nenhum caminho local (sem `file://`, sem `/home/...`) — todo `src`/`href` é um data URI embutido, um link `https://`, uma âncora interna (`#oferta` etc.) ou um `mailto:`.
- 8 seções com id (`sobre`, `dor`, `projetos`, `beneficios`, `para-quem`, `como-funciona`, `faq`, `oferta`) presentes e únicas.
- Pixel do Meta (`fbq('init', ...)`, `PageView`, `trackCustom LandingPageWhatsApp`) intacto.
- Botões antes da seção "Oferta especial" (menu e hero) levam para `#oferta` com rolagem suave, na mesma aba.
- Botões da oferta, da seção final, do rodapé e a bolha flutuante continuam abrindo o WhatsApp.
- Zero overflow horizontal em 1920/1440/1366/1024/390/360px.
- Carrosséis (hero e projetos), menu mobile e FAQ testados e funcionando.
- Logo do rodapé, texto do botão final centralizado e demais ajustes aprovados preservados exatamente como estão.
