# Como trocar o icone da extensao (e por que ele nao atualizou)

## O que aconteceu

O arquivo `icon.png` na pasta nao e o que o VS Code mostra na lista de extensoes.
O VS Code le o icone de dentro do pacote **.vsix** instalado. Renomear um arquivo
na pasta nao muda nada no pacote que ja esta instalado na sua maquina.

Alem disso, o seu `package.json` atual aponta o perfil do terminal para um icone
**embutido** do VS Code:

```json
"contributes": {
  "terminal": {
    "profiles": [
      { "id": "gbit-shell.terminal-profile", "title": "GBit Shell", "icon": "terminal" }
    ]
  }
}
```

O valor `"terminal"` e o nome de um icone interno do VS Code (o desenho generico de
terminal). Nao e o arquivo `icon.png`. Ou seja: mesmo trocando o arquivo, a lista de
terminais continua mostrando o icone antigo do VS Code.

## Os dois icones sao lugares diferentes

| Onde aparece | De onde vem | Como trocar |
|---|---|---|
| Lista de extensoes (Ctrl+Shift+X) | campo `icon` do `package.json` | republicar a extensao |
| Lista de terminais (setinha ao lado do +) | `contributes.terminal.profiles[].icon` | apontar para o arquivo |
| Pagina do Marketplace (browser) | `IconUrl` dentro do `.vsix` | republicar |

## Passo 1 - desinstalar a versao antiga

O VS Code nao troca o icone se a versao for a mesma. Ele considera que ja esta
instalado e nao faz nada.

```
code --uninstall-extension gislaine.gbit-shell-terminal
```

Se reclamar que nao existe, abra o VS Code, va em Ctrl+Shift+X, ache **GBit Shell**,
clique na engrenagem e em **Uninstall**.

## Passo 2 - subir a versao

Abra `package.json` e mude:

```
"version": "1.0.1",
```

para:

```
"version": "1.0.2",
```

Sem isso o pacote novo tem o mesmo nome do antigo e a instalacao nao e considerada
uma atualizacao.

## Passo 3 - conferir o campo do icone da extensao

O `package.json` precisa ter, perto do nome:

```json
"name": "gbit-shell-terminal",
"displayName": "GBit Shell",
"description": "...",
"icon": "icon.png",
```

O caminho e relativo a pasta do `package.json`. Se o arquivo estiver em
`extensao-vscode/icon.png`, o valor e `icon.png`. Se estiver em
`extensao-vscode/assets/icon.png`, o valor e `assets/icon.png`.

## Passo 4 - (opcional) usar o icone no perfil do terminal

Se quiser o mesmo desenho na lista de terminais, troque a linha 41 de
`package.json`:

```
"icon": "terminal",
```

por:

```
"icon": "$(terminal)",
```

Atencao: o perfil de terminal so aceita **icones de codigo** do VS Code (no formato
`$(nome)`), nao aceita um arquivo PNG. Entao o desenho continua sendo o do VS Code.
Se o seu objetivo e ter o desenho proprio na lista de terminais, isso nao e possivel
pela API de perfis. O que da para fazer e deixar o titulo mais claro, por exemplo
`"title": "GBit Shell"` (que ja esta) e o icone `$(terminal-powershell)` ou
`$(chevron-right)` para diferenciar dos outros terminais.

Opcoes de icone de codigo uteis: `$(terminal)`, `$(chevron-right)`, `$(rocket)`,
`$(zap)`, `$(code)`.

## Passo 5 - gerar o pacote novo

Dentro da pasta `extensao-vscode`:

```
npx @vscode/vsce package
```

Sai `gislaine.gbit-shell-terminal-1.0.2.vsix`.

Se quiser conferir se a imagem entrou mesmo no pacote:

```
python -c "import zipfile;print([n for n in zipfile.ZipFile('gislaine.gbit-shell-terminal-1.0.2.vsix').namelist() if 'png' in n])"
```

Tem que aparecer `extension/icon.png`.

## Passo 6 - instalar e recarregar

```
code --install-extension gislaine.gbit-shell-terminal-1.0.2.vsix
```

Depois, no VS Code, `Ctrl+Shift+P` -> **Developer: Reload Window**.

Se o icone AINDA nao mudar, feche o VS Code inteiro e abra de novo (a lista de
extensoes fica em memoria durante a sessao) e confira com:

```
code --list-extensions --show-versions | grep gbit
```

Tem que mostrar `gislaine.gbit-shell-terminal@1.0.2`.

## Se continuar teimando

Rode com a pasta de extensoes aberta:

```
code --extensions-dir %USERPROFILE%\.vscode\extensions
```

E apague a pasta `gislaine.gbit-shell-terminal-1.0.1`. E o cache da versao antiga.
