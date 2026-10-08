# Publicação: GitHub Pages + Render

O GitHub Pages publica somente a interface estática. O Render executa a API Node e guarda o catálogo. O endereço do backend é público, mas as rotas de alteração exigem a sessão administrativa.

## 1. Publicar a API no Render

1. Envie para o GitHub os arquivos `server.js`, `package.json`, `package-lock.json`, `render.yaml`, `data/products.json` e os arquivos da interface.
2. No Render, crie um **Blueprint** usando esse repositório. O `render.yaml` configura o serviço Node e o disco persistente montado em `/var/data`.
3. Nos Environment Variables do serviço, defina:
   - `ADMIN_PASSWORD`: uma senha forte, única e que não seja a senha de exemplo.
   - `FRONTEND_ORIGIN`: somente a origem do GitHub Pages, por exemplo `https://SEU-USUARIO.github.io` (sem caminho nem barra final). Se usar domínio próprio, informe a origem desse domínio.
   - `SESSION_SECRET` é gerado pelo Blueprint.
4. Aguarde o primeiro deploy e copie a URL HTTPS atribuída ao serviço, por exemplo `https://spectrum-api.onrender.com`.

O disco persistente é necessário porque o backend salva nele os produtos e o hash da senha administrativa. Não apague nem recrie o disco sem fazer backup dos dados.

## 2. Apontar o GitHub Pages para a API

Edite `api-config.js` e substitua o valor vazio pela URL HTTPS real do Render, sem barra no final:

```js
window.SPECTRUM_API_BASE = "https://spectrum-api.onrender.com";
```

Publique juntos `api-config.js`, a página atual como `index.html`, `style.css`, `extras.css` e `spectrum-logo.jpeg` na pasta configurada no GitHub Pages. A página carrega `api-config.js` antes do código que consulta a API.

## 3. Verificar

1. Abra `https://SEU-USUARIO.github.io/` e confirme que o catálogo aparece.
2. Abra `https://URL-DO-RENDER/api/products`; deve retornar a lista JSON de produtos.
3. Entre em “Área administrativa” com `ADMIN_PASSWORD` e teste salvar uma alteração. Recarregar a página deve manter a mudança.
4. Se aparecer `Failed to fetch`, confira se a URL no `api-config.js` está correta e se `FRONTEND_ORIGIN` corresponde exatamente à origem visível na barra do navegador.

O navegador precisa aceitar cookies de terceiros para manter a sessão entre GitHub Pages e Render. Se o navegador bloquear esses cookies, hospede a interface e a API sob o mesmo domínio próprio.

## Desenvolvimento local

Copie `.env.example` para `.env`, preencha `ADMIN_PASSWORD` e `SESSION_SECRET`, depois rode `npm start` e abra `http://localhost:3000`.
