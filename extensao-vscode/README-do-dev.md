# Guia da pasta extensao-vscode

Esta pasta e a raiz do projeto da extensao do VS Code do GBit Shell.
Ela e independente do CLI publicado: mexer aqui nao altera nada em `pip install gbit-shell`
nem no pacote do npm. O unico ponto de contato e o `extension.js`, que e copiado
para `gbit_shell/_vscode_ext/extension.js` quando o motor precisar dele (veja o passo 5).

## Conteudo

| Arquivo | Para que serve |
|---|---|
| `package.json` | ficha da extensao (nome, publisher, versao, comandos, icone) |
| `extension.js` | o codigo da extensao |
| `icon.png` | icone 256x256 usado na listagem e na pagina do Marketplace |
| `README.md` | a pagina que aparece no Marketplace |
| `LICENSE` | MIT |
| `assets/` | imagens usadas dentro do README |
| `CHANGELOG.md` | historico de versoes mostrado na aba CHANGELOG |
| `.vscodeignore` | o que fica fora do pacote |
| `.gitignore` | o que fica fora do git |

## Os 4 numeros de versao (nao confunda)

| Onde | Produto | Versao atual |
|---|---|---|
| `extensao-vscode/package.json` | extensao do VS Code (Marketplace) | 1.0.1 |
| `package.json` da raiz | pacote npm `gbit-shell` | 1.0.0 |
| `pyproject.toml` | pacote pip `gbit-shell` | 1.0.0 |
| `gbit_shell/__init__.py` | o que o `gbit --version` mostra | 1.0.0 |

Publicar extensao nova = subir so o primeiro. Os outros tres nao precisam mudar.

## Passo 1 - testar o pacote local

Dentro desta pasta, no PowerShell ou Git Bash:

```
npx @vscode/vsce package
```

Sai um arquivo tipo `gislaine.gbit-shell-terminal-1.0.1.vsix`. Instale para conferir:

```
code --install-extension gislaine.gbit-shell-terminal-1.0.1.vsix
```

Depois `Ctrl+Shift+P` -> `Developer: Reload Window`.
Se o `npx` reclamar de versao, rode `npx @vscode/vsce package --allow-missing-repository`
ou simplesmente `npm i -g @vscode/vsce` e use `vsce package`.

## Passo 2 - publisher e token (so uma vez)

1. Abra `https://marketplace.visualstudio.com/manage` e crie o publisher **gislaine**.
   Tem que ser exatamente `gislaine`, porque e o campo `publisher` do `package.json`.
   Se o nome estiver ocupado, escolha outro ID e troque no `package.json`.
2. Abra `https://dev.azure.com`, entre com a mesma conta Microsoft.
3. Canto superior direito -> User settings -> Personal access tokens -> New Token.
4. Organization: **All accessible organizations**. Scopes: Show all scopes ->
   **Marketplace -> Manage**. Copie o token (aparece uma vez so).

## Passo 3 - publicar

```
npx @vscode/vsce login gislaine
npx @vscode/vsce publish
```

O login pede o token e guarda. Nas proximas vezes e so `publish`.

Se ja tiver o `.vsix` pronto:

```
npx @vscode/vsce publish --packagePath gislaine.gbit-shell-terminal-1.0.1.vsix
```

Antes de cada nova publicacao, suba `version` no `package.json`. O Marketplace
recusa um numero que ja existe.

## Passo 4 - conferir

```
code --install-extension gislaine.gbit-shell-terminal
```

Pagina publica: `marketplace.visualstudio.com/items?itemName=gislaine.gbit-shell-terminal`

## Passo 5 - manter o `gbit vscode` funcionando

O comando `gbit vscode` do CLI monta um `.vsix` a partir de um arquivo embutido no Python
(`gbit_shell/_vscode_ext/extension.js`), nao desta pasta. Se voce editar o `extension.js`
aqui, copie para la:

```
cp extensao-vscode/extension.js gbit_shell/_vscode_ext/extension.js
```

Sem isso, a versao instalada pelo `gbit vscode` continua a antiga.

## O que ja esta corrigido nesta pasta

- `icon` apontando para `icon.png` (o VS Code exige 256x256, PNG).
- Titulos dos comandos sem o prefixo repetido. Antes o VS Code mostrava
  "GBit Shell: GBit Shell: Novo terminal" porque o titulo ja trazia a categoria.
- `repository` apontando para `Gislaine-web3/gbit-shell` (o endereco antigo,
  `Gislaine-programadora`, nao existe mais e virava link quebrado no Marketplace).
- Badge de versao no README alinhado com o `package.json`.

## Publicar no GitHub

```
git add extensao-vscode
git commit -m "extensao-vscode: icone, metadata e correcoes do pacote"
ghpush --remote https://github.com/Gislaine-web3/gbit-shell.git
```
