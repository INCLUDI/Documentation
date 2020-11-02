# Getting started

## Scaricare un progetto e i submodules

I due progetti Kit-Mobile e Kit-Oculus includono le repo Kit-Shared e Kit-Assets tramite git submodules.
Per inizializzare correttamente uno dei due progetti Kit-Mobile o Kit-Oculus bisogna quindi eseguire
il comando `git clone --recurse-submodules --remote-submodules` per clonare la repo comprendendo i submodules.
Per ulteriori informazioni sui link simbolici su Windows, visita
[la documentazione ufficiale di git](https://git-scm.com/book/en/v2/Git-Tools-Submodules).

## Creare i link simbolici

I submodules vengono scaricati nella cartella /Submodules, quindi non sono visibili all'interno del progetto Unity.
Per fare in modo che i file siano visibili da Unity, occorre creare dei link simbolici a delle cartelle
specifiche che bisogna rendere visibili nella cartella Assets del progetto principale.

- nel caso di Kit-Shared bisogna creare dei link alla cartella `Shared` e `Resources`.
- nel caso di Kit-Assets bisogna creare dei link alla cartella `AddressableAssets` e `AddressableAssetsData`.

Per creare un link simbolico (su Windows) apri il terminale nella cartella `Assets` del progetto e digita i seguenti comandi:

- `mklink /d ..\Submodules\Kit-Assets\Assets\AddressableAssets AddressableAssets`
- `mklink /d ..\Submodules\Kit-Assets\Assets\AddressableAssetsData AddressableAssetsData`
- `mklink /d ..\Submodules\Kit-Assets\Assets\Shared Shared`
- `mklink /d ..\Submodules\Kit-Assets\Assets\Resources Resources`

Per ulteriori informazioni sui link simbolici su Windows, visita
[questo sito](https://docs.microsoft.com/it-it/windows-server/administration/windows-commands/mklink).

## N.B.

Assicurarsi che tutti i progetti siano usati con la stessa versione di Unity.
