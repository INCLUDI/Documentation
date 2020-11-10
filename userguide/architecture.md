# Struttura del Kit

## Flusso delle attività

Le attività sono tutte basate sull'algoritmo rappresentato nel seguente diagramma di flusso.

![Flow diagram](draw.io/INCLUDI kit-Flow diagram.png)

## PlatformManager

GameObject fondamentale per gestire tutte le funzioni specifiche della piattaforma su cui è installato il kit.
Il PlatformManager deve essere equipaggiato con un MonoBehaviour che estende questa classe astratta
ed esegue l'override dei metodi astratti e, se necessario, di quelli virtuali.
Lo script `PlatformManager.cs` implementa i metodi virtuali `LoadActivity()` per caricare una scena nuova
e per `LoadMenuScene()` per tornare alla scena del menu,
oltre che la callback `SceneLoaded(UnityEngine.ResourceManagement.AsyncOperations.AsyncOperationHandle<SceneInstance> scenes)`.
Di questi metodi è possibile eseguire l'override se è necessario eseguire delle operazioni aggiuntive
(per esempio, nelle applicazioni mobile, quando si cambia scena, è necessario abilitare/disabilitare la modalità XR).
Inoltre fornisce i metodi astratti `ActivityReady()` e `ActivityCompleted()`,
che devono essere implementati a seconda della piattaforma di destinazione.
`PlatformManager.cs` fornisce anche i metodi astratti che servono ad identificare il `Type`
degli oggetti da aggiungere ai GameObject con cui l'utente interagisce durante l'attività.
In particolare, questi metodi sono:

- `Type SelectableTriggerType()`
- `Type InteractableTriggerType()`
- `Type TargetTriggerType()`
- `Type VirtualAssistantTriggerType()`
- `Type ButtonTriggerType()`

All'interno della classe "concreta", questi metodi devono eseguire l'override per ritornare un Type che è "concreto".
Per fare un esempio, `InteractableTriggerType()` nella soluzione per Cardboard viene implementato così:

```csharp
public override Type InteractableTriggerType()
{
    return typeof(CardboardInteractableTrigger);
}
```

mentre nella soluzione Oculus viene implementato così:

```csharp
public override Type InteractableTriggerType()
{
    return typeof(OculusInteractableTrigger);
}
```

Il PlatformManager implementa `DontDestroyOnLoad()`, perchè è necessario che sia persistente tra le scene.

## ActivityManager

L'ActivityManager è il componente principale che regola il flusso delle attività,
ed è costituito da un GameObject che contiene l'omonimo Monobehaviour.
L'ActivityManager si occupa di scorrere i gruppi di step tenendo conto del gruppo corrente,
e all'interno del gruppo corrente di scorrere gli step.
I due metodi che eseguono tale scorrimento sono `NextEventGroup()` e `NextEvent()`.
l'ActivityManager inoltre si occupa di parsare il JSON dell'attività e eseguire le istruzioni a seconda dei suoi parametri.
L'ActivityManager si occupa di informare l'assistente virtuale di dare istruzioni all'utente,
tramite i metodi asincroni `PlaySingleInstruction(string instruction, string animatorParam)`
e `PlayInstructionSequence(List<string> instructions)`.

## EventGroupManager

`EventGroupManager` è una classe astratta che estende la classe `ScriptableObject`
che a sua volta è implementata in diverse classi "concrete", e di cui l'ActivityManager ne mantiene un reference.
L'istanza di EventGroupManager viene creata tutte le volte l'ActivityManager invoca il metodo NextEventGroup()
(ovvero tutte le volte che si comincia un nuovo gruppo di step), a seconda del valore del parametro type dello step corrente.
In altre parole, il parametro type indica che tipo di EventGroupManager viene creato dall'ActivityManager.
L'EventGroupManager implementa i metodi che controllano l'azione dell'utente e che sono chiamati dall'ActivityManager tramite i metodi
`CheckCorrectAction(GameObject selectable)`, `CheckCorrectAction(GameObject selectable, GameObject interactable = null)`
e `CheckCorrectAnswer(string answer)`, verifica che l'azione sia corretta o sbagliata controllando l'oggetto `CorrectParameters`
dell'evento corrente, e notifica l'assistente di fornire feedback a seconda che l'azione dell'utente sia corretta o sbagliata.
Solitamente, se l'azione è giusta, viene chiamato il metodo `NextEvent()` dell'ActivityManager,
altrimenti l'applicazione si rimette in attesa dell'azione dell'utente.
Eventuali feedback visivi/audio (come tick, croce e barra di progresso)
a seguito dell'azione dell'utente vengono gestiti dall'EventGroupManager stesso.
Se l'azione è corretta, prima di chiamare il metodo `ActivityManager.NextEvent()`,
l'EventGroupManager deve invocare il metodo SaveAction passando come parametri tutte le informazioni utili dell'azione dell'utente.

## VirtualAssistantManager

MonoBehaviour che gestisce il comportamento dell'assistente virtuale e viene attaccato al suo relativo GameObject.
Implementa il pattern singleton, così tutti gli oggetti possono avere il suo riferimento.
