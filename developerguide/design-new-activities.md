# Progettare nuove attività

## Addressable Assets

Gli asset relativi a scene, file JSON e oggetti delle attività sono contrassegnati come addressable assets
(per informazioni sugli assressable assets, visita [la documentazione](https://docs.unity3d.com/Packages/com.unity.addressables@latest)).
è quindi necessario come prima cosa importare nel progetto il pacchetto Addressables tramite Package Manager.

Gli addressable assets sono contenuti nella repo Kit-Assets, che deve essere contenuta come submodule nei progetti padre.
La cartella AddressableAssets contiene le seguenti sottocartelle:

- JSON: è la cartella che contiene tutti i file JSON che contengono le configurazioni delle attività.
- Scenes: in questa cartella ci sono tutte le scene disponibili.
  Si consiglia raggruppare gli asset di ogni scena in altrettante sottocartelle.
- Activities: in questa cartella sono contenuti tutti lgi asset delle attività.
  Si consiglia di creare una sottocartella per ogni attività, a meno che non ci siano più attività che utilizzano gli stessi asset.
- Images: contiene gli sprite usati come immagini delle attività che vengono visualizzate nel menu.

## Configurare un addressable group

Per contrassegnare un asset come addressable, è necessario marcarlo come addressable nell'instpector
oppure trascinarlo nella finestra degli Addressable Groups (Window > Asset Management > Addressables > Groups).
I gruppi di addressable sono organizzati come segue:

- I JSON delle attività appartengono al gruppo \_Activities.
  Tutti gli asset di questo gruppo devono essere contrassegnati con una o più label che indicano il dispositivo che supportano,
  nello specifico Cardboard o Oculus.
  Questo permette al menu principale di filtrare le attività a seconda della label con cui sono state contrassegnate.
- Le scene appartengono al gruppo \_Scenes.
  N.B.: è importante dare agli addressables delle scene lo stesso nome con cui vengono chiamate all'interno del JSON,
  dato che vengono poi caricate per nome.
  Questo è l'unico gruppo in gli asset vengono buildati separatamente invece che nello stesso asset bundle,
  per ridurre i tempi di download quando si vuole scaricare solo una scena.
- Il gruppo \_Images contiene le immagini relative alle attività.
- Per gli asset delle singole attività, è consigliato creare un gruppo per ogni nuova attività.
  Per tutti gli asset di un'attività, è necessario assegnare una label che deve corrispondere
  al campo id del JSON relativo all'attività stessa
