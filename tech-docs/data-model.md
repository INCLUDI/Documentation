# Modello dei dati

Le attività si basano su una serie di parametri che vengono parsati da un file JSON.
Il file JSON (e dunque gli oggetti C# che ne derivano durante il parsing), segue il modello rappresentato in figura:

![Class diagram](/docs/tech-docs/images/INCLUDIkit-Class-diagram.svg)

Il modello è contenuto nello script DataModel.cs.

In questa sezione verrà descritto il significato degli oggetti e dei parametri che compongono il modello.

## ActivityConfiguration

è l'oggetto che costituisce la radice del JSON.

- `id: string` -> indica la label con cui è stato contrassegnato il gruppo di Addressable Assets da scaricare.
- `name: string` -> il nome dell'attività.
- `description: string` -> la descrizione dell'attività.
- `image: string` -> l'immagine associata all'attività.
- `scene: string` -> l'ambientazione (Scene) che viene caricata all'avvio dell'attività.
- `virtualAssistant: VirtualAssistantInfo` -> info relative a posizione e altre caratteristiche dell'assistente.
- `player: PlayerInfo` -> info relative alla posizione dell'utente nella scena.
- `classes: UserClass` -> indica l'insieme di classi per cui è stata progettata l'attività.
- `objsToAdd: List<SceneObj>` -> sono i prefab che vogliamo far comparire nella scena all'inizio dell'attività,
  e che sono presenti durante tutta l'esecuzione dell'attività
- `objsToRemove: List<string>` -> la lista dei nomi dei GameObject che vogliamo disattivare dalla scena all'inizio dell'attività.
  Solitamente si tratta di oggetti che bloccano la visuale o limitano l'azione dell'utente nella specifica attività.
- `eventGroups: List<EventGroup>` -> la lista di gruppi di step che compongono l'attività.

## VirtualAssistantInfo

- `transform: CustomTransform` -> posizione, rotazione e scale dell'assistente.
- `flipBalloon: bool` -> se il valore è settato a true, il fumetto dell'assistente virtuale viene ruotato di 180° lungo l'asse y.
  Di default il fumetto è alla sinistra dell'assistente,
  il parametro è utile nel caso l'assistente abbia degli ostacoli alla sua sinistra.
- `maxBalloonWidth: int` -> la larghezza massima che può avere il fumetto. La larghezza di default è 150,
  tuttavia può succedere che alcune scene siano ambientate in stanze con un soffitto basso o altri ostacoli,
  in tal caso è necessario aumentare la larghezza massima del fumetto per impedire al fumetto di crescere troppo in altezza.
- `lockRotation: bool` -> se settato a true, l'assistente virtuale rimane fisso nella sua rotazione iniziale,
  e non guarda verso la posizione dell'utente. Utile nel caso in cui l'assistente si trovi in degli angoli,
  in tal caso c'è il rischio che a causa della rotazione il fumetto incontri degli ostacoli.

## PlayerInfo

- `transform: CustomTransform` -> posizione, rotazione e scale dell'utente.
  Il valore y della transform deve coincidere con quello del pavimento.
- `groundOffset: float` -> per i devices che non hanno il room scale (come Cardboard),
  è necessario settare questo parametro (solitamente varia tra 1.6 e 1.8)
  per fare in modo che la trasform del giocatore sia ad altezza uomo.

## UserClass

- `year: int` -> l'anno della classe (da 1 a 3 per le scuole medie, da 1 a 5 per le superiori).
- `instituteType: string` -> l'istituto scolastico di appartenenza (e.g. Liceo Classico)

## EventGroup

- `type: string` -> stringa che indica il nome dell'EventGroupManager da creare all'inizio del gruppo di step.
  N.B.: è molto importante che il valore di type sia esattamente uguale al nome della classe dello specifico manager.
- `instructionsIntro: List<string>` -> la lista di istruzioni che l'assistente virtuale fornisce all'inizio del gruppo di step.
  Solitamente sono istruzioni che introducono l'utente all'attività,
  oppure che spiegano che azioni dovrà compiere all'interno del gruppo di step.
- `instructionsEnd: List<string>` -> la lista di istruzioni che l'assistente virtuale fornisce alla fine del gruppo di step.
- `stepsToReproduce: int` -> spesso un gruppo di step contiene un numero elevato di step;
  nel caso non si vogliano far eseguire tutti, bisogna specificare il numero di steo da eseguire.
  Se lasciato vuoto questo campo, di default verranno eseguiti tutti gli step.
- `randomEvents: bool` -> settare questo campo a true se si vuole riprodurre gli step in modo casuale all'interno di un gruppo di step.
  Il valore di default è false.
- `selectablesToSpawn: int` -> usato sempre in correlazione a selectableSpawnPoints.
  All'interno dello step, è possibile che ci sia un numero di oggetti maggiore rispetto a quelli che si vuole effettivamente generare
  (per esempio, si vuole generare 3 oggetti ad ogni step, ma i selectablesToActivate nei vari step sono 10, 9, 11, ecc.).
  Specificando questo campo, verranno generati soltanto n oggetti all'interno degli step.
- `interactablesToSpawn: int` -> vedi descrizione di `selectablesToSpawn`.
- `targetsToSpawn: int` -> vedi descrizione di `selectablesToSpawn`.
- `selectablesSpawnPoints: List<CustomTransform>` -> è l'insieme di posizioni fisse in cui si vuole generare gli oggetti nei singoli step.
  Specificando una lista di `selectablesSpawnPoints`, quando viene istanziato un oggetto,
  invece che la posizione relativa al suo prefab, viene presa una delle posizioni di `selectablesSpawnPoints`.
  N.B. è utile specificare una lista di `selectablesSpawnPoints` quando si ha a che fare con prefab che hanno pivot in posizioni analoghe
  (ad esempio degli sprite), altrimenti si corre il rischio che non tutti gli oggetti abbiano la posizione desiderata.
- `interactablesSpawnPoints: List<CustomTransform>` -> vedi descrizione di `selectablesToSpawn`.
- `targetsSpawnPoints: List<CustomTransform>` -> vedi descrizione di `selectablesToSpawn`.
- `selectablesRandomSpawn: bool` -> se specificato come true, gli oggetti generati mescoleranno le loro posizioni tra di loro.
  N.B. consigliabile settare questo parametro solo quando si ha a che fare con prefab che hanno pivot in posizioni analoghe
  (ad esempio degli sprite), altrimenti si corre il rischio che non tutti gli oggetti abbiano la posizione desiderata.
  Il valore di default è false.
- `interactablesRandomSpawn: bool` -> vedi descrizione di `selectablesRandomSpawn`.
- `targetsRandomSpawn: bool` -> vedi descrizione di `selectablesRandomSpawn`.
- `eventGroupObjs: EventObjs` -> gli oggetti da generare all'inizio del gruppo di step, e da rimuovere al termine dello stesso.
- `skipIfWrong: bool` -> se posto a true, in tutti gli step del gruppo di step, quando l'utente compie un'azione errata,
  si passa allo step successivo. Il comportamento di default è che se l'azione è sbagliata, si rimane nello step corrente
  (Questo parametro è utile nelle attività orali, quando l'utente non sa la risposta:
  se non specificato, l'applicazione rimarrebbe sempre allo stesso step).
- `events: List<EventConfiguration>` -> la lista di step che compongono il gruppo di step.

## EventConfiguration

- `eventObjs: EventObjs` -> gli oggetti da generare all'inizio dello step, e da rimuovere al termine dello stesso.
- `instructions: Instructions` -> le istruzioni che l'assistente fornisce come richiesta,
  feedback per azione corretta e feedback per azione sbagliata.
- `parameters: EventParameters` -> i parametri che indicano quali sono le azioni corrette, e altre info.

## EventObjs

- `selectablesToActivate: List<string>` -> la lista di oggetti di tipo selectable da attivare.
- `interactablesToActivate: List<string>` -> la lista di oggetti di tipo interactable da attivare.
- `targetsToActivate: List<string>` -> la lista di oggetti di tipo target da attivare.
- `othersToActivate: List<string>` -> la lista di oggetti di tipo other da attivare.
- `selectablesToDeactivate: List<string>` -> la lista di nomi di oggetti da rimuovere dalla scena.
- `interactablesToDeactivate: List<string>` -> la lista di nomi di oggetti da rimuovere dalla scena.
- `targetsToDeactivate: List<string>` -> la lista di nomi di oggetti da rimuovere dalla scena.
- `othersToDeactivate: List<string>` -> la lista di nomi di oggetti da rimuovere dalla scena.

## Instructions

- `request: List<string>` -> insieme di possibili richieste che l'assistente può fare all'inizio dello step.
- `correct: List<string>` -> insieme di possibili feedback che l'assistente può dare se l'utente commette un'azione sbagliata.
- `wrong: List<string>` -> insieme di possibili feedback che l'assistente può dare se l'utente commette un'azione sbagliata.

## EventParameters

- `correctSelectables: List<string>` -> lista con i nomi dei GameObject che rappresentano delle risposte giuste per lo step.
- `correctInteractables: List<string>` -> lista con i nomi dei GameObject che rappresentano delle risposte giuste per lo step.
- `correctTargets: List<string>` -> lista con i nomi dei GameObject che rappresentano delle risposte giuste per lo step.
- `correctAnswers: List<string>` -> lista delle possibili risposte giuste per lo step.
- `correctExpressions: List<string>` -> lista delle possibili espressioni giuste per lo step.
- `objsToActivate: List<SceneObj>` -> lista di oggetti di tipo other da attivare nella scena appena l'utente esegue l'azione giusta.
  Questi oggetti vanno inclusi nella lista di oggetti del campo othersToDeactivate se vogliono essere rimossi alla fine dello step.
- `objsToDeactivate: List<SceneObj>` -> lista di oggetti da rimuovere dalla scena appena l'utente esegue l'azione giusta.
- `finalTransform: CustomTransform` -> se indicato, un oggetto a scelta si muoverà rapidamente (tramite un DOTWeen)
  verso la posizione indicata da `finalTransform`.
  Quale degli oggetti viene selezionato è da implementare nell'event manager specifico del gruppo di step.
  Solitamente si usa quando i correctSelectables o correctInteractables sono composti da un solo elemento,
  oppure si applica al parametro di `CheckCorrectAction(...)`
- `numericParameter: int` -> parametro numerico che può servire per molti scopi.
  Ad esempio, per le attività Oculus il numero di secondi che bisogna far collidere un interactable con un target
  prima di completare lo step, oppure il numero di volte che un interactable deve collidere con il target.
  Come viene utilizzato questo parametro deve essere implementato nello specifico event manager del gruppo di step.

## SceneObj

- `prefab: string` -> parametro obbligatorio che indica il nome del prefab che viene istanziato.
- `name: string` -> se si vuole cambiare il nome del prefab, inserire il nome desiderato in questo campo.
  Il prefab istanziato sarà chiamato col nome che viene specificato qui.
  Se esiste un campo text, il valore del campo name ha la precedenza sul nome che assumerà il GameObject una volta istanziato.
- `text: string` -> se il prefab ha un figlio che ha un componente `TextMeshProUGUI`,
  con il campo text viene specificato che testo deve visualizzare il componente `TextMeshProUGUI`.
  nel caso il parametro name non sia specificato. il prefab viene istanziato con il nome indicato in text.

## CustomTransform

CustomTransform è analogo alla Transform, questa classe è necessaria per la serializzazione in un JSON.

## CustomVector3

CustomVector3 è analogo al Vector3, questa classe è necessaria per la serializzazione in un JSON.
