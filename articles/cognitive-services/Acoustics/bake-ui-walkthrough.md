---
title: Effettuare il bake dell'acustica con Project Acoustics
titlesuffix: Azure Cognitive Services
description: Questo documento descrive il processo di invio di un bake di acustica tramite l'estensione dell'editor Unity.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: c37e050cd762cb173d64f78b5267e4ad252d17a9
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902247"
---
# <a name="bake-acoustics"></a>Effettuare il bake dell'acustica

Questo documento descrive il processo di invio di un bake di acustica tramite l'estensione dell'editor Unity. Per altre informazioni di base sull'acustica, vedere [Informazioni sull'acustica](what-is-acoustics.md). Per una guida introduttiva, vedere [Introduzione](getting-started.md).

## <a name="import-the-plugin"></a>Importare il plug-in

Importare il pacchetto del plug-in di acustica nel progetto, quindi aprire l'interfaccia utente di acustica scegliendo **Window > Acoustics** (Finestra > Acustica) nel menu di Unity:

![Aprire la finestra Acoustics (Acustica)](media/WindowAcoustics.png)

## <a name="principles"></a>Principi

La finestra degli strumenti Acoustics (Acustica) raccoglie le informazioni necessarie al motore di acustica per calcolare l'acustica per la scena. Per creare un bake, sono necessari cinque passaggi:

1. Creare o contrassegnare la mesh di navigazione del giocatore
2. Contrassegnare la geometria dell'acustica
3. Assegnare le proprietà dei materiali acustici alla geometria
4. Visualizzare in anteprima il posizionamento del probe
5. Effettuare il bake

Dopo aver completato il bake, vedere [Panoramica del processo di progettazione per l'acustica](design-process.md) per i passaggi facoltativi di progettazione post-bake.

## <a name="create-or-mark-a-navigation-mesh"></a>Creare o contrassegnare una mesh di navigazione

Una mesh di navigazione viene usata per inserire i punti di probe per la simulazione. È possibile usare il [flusso di lavoro della mesh di navigazione](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) di Unity oppure specificare la propria mesh di navigazione. Le mesh di navigazione create con il flusso di lavoro di Unity verranno selezionate dal sistema di acustica. Per usare le proprie mesh, contrassegnarle dalla scheda **Objects** (Oggetti) nel passaggio successivo.

## <a name="objects-tab"></a>Scheda Objects (Oggetti)

Aprire la scheda **Objects** (Oggetti) della finestra **Acoustics** (Acustica). Usare questa scheda per contrassegnare gli oggetti nella scena. All'oggetto verranno semplicemente aggiunti i componenti **AcousticsGeometry** o **AcousticsNavigation**. È anche possibile usare il [flusso di lavoro dei componenti di Unity standard](https://docs.unity3d.com/Manual/UsingComponents.html) per applicare o rimuovere il contrassegno negli oggetti.

Selezionare uno o più oggetti nella finestra della scena o nella gerarchia e quindi applicare o rimuovere il contrassegno in **AcousticsGeometry** o **AcousticsNavigation** come descritto di seguito. Se non è selezionato nulla, è possibile applicare o rimuovere il contrassegno in tutti gli elementi della scena contemporaneamente.

È possibile contrassegnare solo Mesh Renderers (Renderer di mesh) e Terrains (Terreni). Tutti gli altri tipi di oggetto verranno ignorati. Le caselle di controllo consentiranno di applicare o rimuovere il contrassegno in tutti gli oggetti interessati.

Se nella scena non sono selezionati elementi, l'aspetto sarà simile a quello dell'immagine seguente:

![Scheda Objects (Oggetti) senza selezioni](media/ObjectsTabNoSelectionDetail.png)

Se nella finestra della scena o della gerarchia sono selezionati elementi, l'aspetto sarà simile a quello dell'immagine seguente:

![Scheda Objects (Oggetti) senza selezioni](media/ObjectsTabWithSelectionDetail.png)

### <a name="objects-tab-parts"></a>Parti della scheda Objects (Oggetti)

Le parti della pagina della scheda sono:

1. Pulsanti di selezione della scheda, con la scheda **Objects** (Oggetti) selezionata. Usare questi pulsanti per scorrere i vari passaggi della creazione di un bake di acustica, da sinistra a destra.
2. Breve descrizione di che cosa fare quando si usa questa pagina.
3. Filtri disponibili per la finestra della gerarchia. Usarli per filtrare nella finestra della gerarchia gli oggetti del tipo specificato, per poterli contrassegnare più facilmente. Se non è ancora stato selezionato alcun valore per l'acustica, selezionando le ultime due opzioni non verrà visualizzato nulla. Possono tuttavia essere utili per trovare gli oggetti che verranno contrassegnati in seguito.
4. Quando non è selezionato alcun oggetto, questa sezione mostra lo stato di tutti gli oggetti nella scena:
    * Total (Totale): numero totale di oggetti attivi non nascosti nella scena.
    * Ignored (Ignorati): numero di oggetti diversi da Mesh Renderers (Renderer di mesh) o dai Terrains (Terreni).
    * Mesh: numero di oggetti Mesh Renderers (Renderer di mesh) nella scena
    * Terrain (Terreno): numero di oggetti terreno nella scena
    * Geometry (Geometria): numero di oggetti Mesh o Terrain (Terreno) nella scena contrassegnati come "Acoustics Geometry" (Geometria acustica)
    * Navigation (Navigazione): numero di oggetti Mesh o Terrain (Terreno) nella scena contrassegnati come "Acoustics Navigation" (Navigazione acustica). Questo numero non include gli elementi NavMesh di Unity.
5. Mostra il numero totale di oggetti contrassegnabili nella scena, ovvero solo i renderer di mesh e i terreni. Mostra le caselle di controllo che è possibile usare per contrassegnare (aggiungere il componente appropriato a) tali oggetti come geometria o navigazione per l'acustica
6. Quando non viene selezionato nulla, questa nota ricorda di selezionare gli oggetti per contrassegnarli, se necessario. È anche possibile selezionare una o entrambe le caselle di controllo per contrassegnare tutti gli oggetti nella scena senza effettuare selezioni.
7. Quando sono presenti oggetti selezionati, questa sezione mostra lo stato solo degli oggetti selezionati.
8. Mostra il numero totale di oggetti selezionati contrassegnabili. Selezionando o deselezionando le caselle di controllo, il contrassegno verrà applicato o rimosso solo per gli oggetti selezionati.

Se alcuni oggetti sono contrassegnati e altri no, la casella di controllo appropriata mostrerà un valore "misto":

![Casella di controllo con valore misto](media/MixedObjectSelectionDetail.png)

Facendo clic sulla casella di controllo, tutti gli oggetti verranno contrassegnati e, facendo di nuovo clic, il contrassegno verrà rimosso da tutti gli oggetti.

Gli oggetti possono essere contrassegnati sia per la geometria che per la navigazione.

### <a name="guidelines-for-marking-objects"></a>Linee guida per contrassegnare gli oggetti

Assicurarsi di contrassegnare tutti gli oggetti come **Acoustics Geometry** (Geometria acustica) se devono occludere, riflettere o assorbire il suono. La geometria acustica può includere elementi come suolo, muri, tetti, finestre e vetrate, tappeti e mobili di grandi dimensioni. È possibile includere oggetti più piccoli, ad esempio lampade, elementi decorativi, plafoniere, perché non aumentano significativamente il costo del bake. È importante non tralasciare gli elementi principali, ad esempio il suolo o un soffitto. Non includere inoltre elementi che non influiranno sull'acustica, ad esempio mesh di collisione.

La trasformazione di un oggetto in fase di calcolo dei probe, tramite la scheda **Probes** (Probe) sotto, è fissa nei risultati del bake. Se si sposta uno qualsiasi degli oggetti contrassegnati nella scena, sarà necessario ripetere il calcolo dei probe ed effettuare di nuovo il bake della scena.

## <a name="materials-tab"></a>Scheda Materials (Materiali)

Dopo che gli oggetti sono stati contrassegnati, fare clic sul pulsante **Materials** (Materiali) per passare alla scheda Materials (Materiali).

### <a name="parts-of-the-materials-tab"></a>Parti della scheda Materials (Materiali)

![Dettaglio della scheda Materials (Materiali)](media/MaterialsTabDetail.png)

1. Pulsante della scheda **Materials** (Materiali), usato per visualizzare questa pagina.
2. Breve descrizione di che cosa fare quando si usa questa pagina.
3. Se selezionata, verranno elencati solo i materiali usati dagli oggetti contrassegnati come **Acoustics Geometry** (Geometria acustica). In caso contrario, verranno elencati tutti i materiali usati nella scena.
4. Usare queste opzioni per modificare l'ordine del menu a discesa che viene visualizzato quando si fa clic su un elenco a discesa nella colonna Acoustics (Acustica) sotto (n. 6). **Name** (Nome) ordina i materiali acustici per nome. "Absorptivity" (Assorbività) li elenca in ordine crescente di assorbività.
5. Elenco dei materiali usati nella scena, in ordine alfabetico. Se la casella di controllo **Show Marked Only** (Mostra solo contrassegnati) viene selezionata (n. 3), vengono visualizzati solo i materiali usati dagli oggetti contrassegnati come**Acoustics Geometry** (Geometria acustica). Facendo clic su un materiale qui, verranno selezionati tutti gli oggetti della scena che usano tale materiale.
6. Mostra il materiale acustico a cui è stato assegnato il materiale della scena. Fare clic su un elenco a discesa per riassegnare un materiale della scena a un materiale acustico diverso. È possibile modificare l'ordinamento del menu visualizzato quando si fa clic su uno di questi elementi usando le opzioni **Sort Acoustics By:** (Ordina acustica per) precedenti (n. 4).
7. Mostra il coefficiente di assorbimento acustico del materiale selezionato nella colonna precedente. Un valore pari a zero indica un materiale perfettamente riflettente (nessun assorbimento), mentre un valore pari a 1 indica un materiale perfettamente assorbente (nessun riflesso). Il coefficiente di assorbimento può essere modificato solo se il materiale selezionato è "Custom" (Personalizzato).
8. Per un materiale assegnato a "Custom" (Personalizzato), il dispositivo di scorrimento non viene più disabilitato ed è possibile scegliere il coefficiente di assorbimento usando il dispositivo di scorrimento o digitando un valore. Per altre informazioni sulle proprietà dei materiali, vedere [Panoramica del processo di progettazione per l'acustica](design-process.md).

### <a name="guidelines-for-assigning-materials-or-absorption-values"></a>Linee guida per l'assegnazione dei materiali (o i valori di assorbimento)

In questa scheda viene ipotizzato l'assorbimento dei materiali in base al nome. Se ad esempio il nome del materiale della scena è LivingRoom_WoodTable, il materiale acustico iniziale assegnato sarà "wood" (legno). Ai materiali in cui non è possibile determinare un materiale noto viene assegnato il materiale "Default" (Predefinito), che ha un assorbimento simile a quello del calcestruzzo.

È possibile riassegnare i materiali acustici a ogni materiale della scena. Se ad esempio il suono di una stanza è troppo riverberante, cambiare il materiale acustico dei muri, del pavimento o del soffitto con un altro più assorbente. L'assegnazione del materiale acustico si applica a tutti gli oggetti che usano tale materiale di scena.

## <a name="probes-tab"></a>Scheda Probes (Probe)

Dopo aver assegnato i materiali, passare alla scheda **Probes** (Probe).

### <a name="parts-of-the-probes-tab"></a>Parti della scheda Probes (Probe)

![Dettaglio della scheda Probes (Probe)](media/ProbesTabDetail.png)

1. Pulsante della scheda **Probes** (Probe) usato per visualizzare questa pagina
2. Breve descrizione di che cosa fare quando si usa questa pagina
3. Usarli per scegliere una risoluzione per la simulazione grossolana o fine. Quella grossolana è più veloce, ma presenta alcuni svantaggi. Per informazioni dettagliate, vedere["Risoluzione grossolana e fine"](#Coarse-vs-Fine-Resolution) più avanti.
4. Questo campo consente di scegliere la posizione in cui i file di dati di acustica devono essere inseriti. Fare clic sul pulsante con "..." per usare la selezione cartelle. La posizione predefinita è **Assets/AcousticsData**. In questa posizione verrà creata anche una sottocartella **Editor**. Per altre informazioni sui file di dati, vedere ["File di dati"](#Data-Files) più avanti.
5. I file di dati per questa scena verranno denominati usando il prefisso fornito qui. Il prefisso predefinito è "Acoustics_[Nome scena]".
6. Dopo aver calcolato i probe, i controlli precedenti verranno disabilitati. Fare clic sul pulsante **Clear** (Cancella) per cancellare i calcoli e abilitare i controlli per poter eseguire di nuovo i calcoli con nuove impostazioni.
7. Fare clic sul pulsante **Calculate...** (Calcola) per voxelizzare la scena e calcolare le posizioni dei punti di probe. Questa operazione viene eseguita in locale sul computer e deve essere eseguita prima di creare un bake.

In questa versione di Project Acoustics i probe non possono essere inseriti manualmente, ma devono essere inseriti tramite il processo automatizzato fornito nella scheda **Probes** (Probe).

### <a name="what-the-calculate-button-calculates"></a>Calcoli eseguiti con il pulsante "Calculate..." (Calcola)

Il pulsante **Calculate...** (Calcola) usa tutti i dati specificati finora (geometria, navigazione, materiali e l'impostazione grossolana/fine) per eseguire diversi passaggi:

1. Usa la geometria delle mesh della scena per calcolare un volume in voxel. Il volume in voxel è un volume tridimensionale che racchiude l'intera scena ed è costituito da piccoli "voxel" cubici. Le dimensioni dei voxel sono determinate dalla frequenza della simulazione, che dipende dall'impostazione **Simulation Resolution** (Risoluzione simulazione). Ogni voxel viene contrassegnato come "open air" o contenente la geometria della scena. Se un voxel contiene la geometria, viene contrassegnato con il coefficiente di assorbimento del materiale assegnato a tale geometria.
2. Usa quindi i dati di navigazione per calcolare le posizioni interessanti dal punto di vista dell'acustica in cui il giocatore potrebbe andare. Prova a trovare un set relativamente ridotto di queste posizioni, che include aree più piccole, ad esempio entrate e corridoi e quindi stanze, per aprire gli spazi. Per le scene di piccole dimensioni si tratta in genere di meno di 100 posizioni, mentre le scene di grandi dimensioni possono comprenderne fino a mille.
3. Per ognuna delle posizioni finali dell'ascoltatore calcolate, determina un numero di parametri, ad esempio quanto è "aperto" lo spazio, le dimensioni della stanza contenuta e così via.
4. I risultati di questi calcoli vengono archiviati in file nella posizione specificata. Vedere ["File di dati"](#Data-Files) più avanti.

A seconda delle dimensioni della scena e della velocità del computer, questi calcoli possono richiedere alcuni minuti.

Dopo aver completato questi calcoli, è possibile visualizzare in anteprima sia i dati dei voxel che le posizioni dei punti di probe per assicurarsi che il bake dia buoni risultati. Ad esempio, una mesh di navigazione non valida o una geometria mancante/aggiuntiva sarà in genere rapidamente visibile nell'anteprima e sarà possibile correggerla.

### <a name="scene-rename"></a>Ridenominazione della scena

Il nome della scena viene usato per connettere la scena ai file in cui sono archiviate la voxelizzazione e la posizione dei punti di probe. Se la scena viene rinominata dopo che i punti di probe sono stati calcolati, i dati di posizionamento e di assegnazione dei materiali vengono persi e devono essere eseguiti di nuovo.

## <a name="debug-display-through-gizmos"></a>Eseguire il debug della visualizzazione tramite gizmo

Per impostazione predefinita, sono attivati entrambi i gizmo, **probe** e **voxel**, che mostrano i voxel e le posizioni dei punti di probe calcolate. Possono essere abilitati o disabilitati usando il menu Gizmos:

![Menu Gizmos](media/GizmosMenu.png)

### <a name="voxels"></a>Voxel

I voxel vengono visualizzati nella finestra della scena come cubi verdi intorno alla geometria presente. I voxel contenenti solo aria non vengono visualizzati. Intorno all'intera scena è presente una grande casella verde corrispondente al volume in voxel completo che verrà usato nella simulazione.
Spostarsi nella scena e verificare che tutti gli elementi che devono costituire la geometria siano formati da voxel. Per visualizzare i voxel, la telecamera della scena deve trovarsi a non più di circa 5 metri dall'oggetto.

Se si confrontano i voxel creati con la risoluzione grossolana con quelli creati con la risoluzione fine, si noterà che i voxel grossolani sono due volte più grandi.

![Anteprima dei voxel](media/VoxelCubesPreview.png)

### <a name="probe-points"></a>Punti di probe

I punti di probe corrispondono alle possibili posizioni del giocatore. Quando si crea un bake, l'acustica per una sorgente sonora in qualsiasi punto della scena viene calcolata per ognuno di questi punti di probe. Se il giocatore non si trova esattamente in una posizione di un punto di probe, vengono usati i dati dei punti di probe più vicini al giocatore per interpolare i parametri in tale posizione.

È quindi importante verificare che esistano punti di probe ovunque il giocatore possa andare nella scena e che tali piccole aree ed entrate siano rappresentate in modo adeguato. I punti di probe vengono inseriti dal motore di Project Acoustics in base all'interpretazione della geometria della scena e non possono essere spostati o modificati in questa versione Designer Preview. Usarli invece per verificare la correttezza dei contrassegni della geometria e i dati di hint per la navigazione.

![Anteprima dei probe](media/ProbesPreview.png)

### <a name="Coarse-vs-Fine-Resolution"></a>Risoluzione grossolana e fine

L'unica differenza tra le impostazioni di risoluzione Coarse (Grossolana) e Fine (Fine) è la frequenza con cui viene eseguita la simulazione. La frequenza usata da quella fine è il doppio di quella grossolana.
Anche se può sembrare semplice, le implicazioni per la simulazione acustica sono diverse:

* La lunghezza d'onda per quella grossolana è il doppio di quella fine e quindi i voxel sono due volte più grandi.
* Il tempo di simulazione è direttamente proporzionale alle dimensioni dei voxel, quindi un bake grossolano è circa 16 volte più veloce di un bake fine.
* I portali (ad esempio, porte o finestre) con dimensioni inferiori a quelle dei voxel non possono essere simulati. L'impostazione Coarse (Grossolana) può impedire la simulazione di alcuni di questi portali più piccoli, che quindi non lasceranno passare l'audio in fase di esecuzione. Per verificare se ciò accade, visualizzare i voxel.
* La frequenza di simulazione più bassa comporta meno diffrazione sui bordi e agli angoli.
* Le sorgenti sonore non possono trovarsi all'interno di voxel "pieni", ovvero voxel che contengono una geometria, perché il suono non viene udito. Collocare le sorgenti sonore in modo che non siano all'interno dei voxel più grandi dell'impostazione Coarse (Grossolana) è più difficile che usare l'impostazione Fine (Fine).
* I voxel più grandi si estenderanno maggiormente nei portali, come illustrato di seguito. La prima immagine è stata creata con la risoluzione grossolana, mentre la seconda rappresenta la stessa entrata con la risoluzione fine. Come indicato dai contrassegni di colore rosso, l'intrusione è considerevolmente minore nell'entrata con l'impostazione Fine (Fine). La linea blu è l'entrata definita dalla geometria, mentre la linea rossa è il portale acustico effettivo definito dalle dimensioni dei voxel. Le conseguenze di questa intrusione in una determinata situazione dipendono interamente da come i voxel sono allineati alla geometria del portale, il che è determinato dalle dimensioni e dalle posizioni degli oggetti nella scena.

![Entrata grossolana](media/CoarseVoxelDoorway.png)

![Entrata fine](media/FineVoxelDoorway.png)

## <a name="bake-tab"></a>Scheda Bake

Quando si è soddisfatti dei dati di anteprima, usare la scheda **Bake** per inviare il bake al cloud.

### <a name="parts-of-the-bake-tab"></a>Parti della scheda Bake

![Dettaglio della scheda Bake](media/BakeTabDetails.png)

1. Pulsante della scheda Bake usato per visualizzare questa pagina.
2. Breve descrizione di che cosa fare in questa pagina.
3. Campi in cui immettere le credenziali di Azure dopo aver creato l'account Azure. Per altre informazioni, vedere [Creare un account Azure Batch](create-azure-account.md).
4. Tag di immagine docker per il set di strumenti di acustica.
5. Avviare il portale di Azure per gestire le sottoscrizioni, monitorare l'utilizzo, visualizzare le informazioni di fatturazione e così via. 
6. Tipo di nodo di calcolo di Azure Batch da usare per il calcolo. Il tipo di nodo deve essere supportato dalla località del data center di Azure. Se non si è sicuri, lasciare **Standard_F8s_v2**.
7. Numero di nodi da usare per questo calcolo. Il numero immesso in questo campo influisce sul tempo necessario per completare il bake ed è limitato dall'allocazione di core di Azure Batch. L'allocazione predefinita consente solo due nodi da 8 core o un nodo da 16 core, ma può essere espansa. Per altre informazioni sui vincoli delle allocazioni di core, vedere [Creare un account Azure Batch](create-azure-account.md).
8. Selezionare questa casella di controllo per configurare il pool di calcolo per l'uso di [nodi a priorità bassa](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). I nodi di calcolo a priorità bassa hanno un costo molto minore ma potrebbero non essere sempre disponibili o essere interrotti in qualsiasi momento.
9. Numero di probe per la scena, calcolato nella scheda **Probes** (Probe). Il numero di probe determina il numero di simulazioni che devono essere eseguite nel cloud. Non è possibile specificare più nodi che probe.
10. Intervallo di tempo trascorso previsto per l'esecuzione del processo nel cloud. Non include il tempo di avvio dei nodi. Dopo l'avvio dell'esecuzione del processo, indica quanto tempo deve trascorrere prima di ottenere i risultati. Si noti che questa è solo una stima.
11. Tempo di elaborazione totale necessario per eseguire le simulazioni. Corrisponde alla quantità totale di tempo di calcolo dei nodi che verrà usata in Azure. Per altre informazioni sull'uso di questo valore, vedere [Stima del costo del bake](#Estimating-bake-cost).
12. Questo messaggio indica dove verranno salvati i risultati del bake dopo il completamento del processo.
13. (Solo uso avanzato) Se per qualche motivo è necessario fare in modo che Unity non tenga più in considerazione un bake inviato (ad esempio perché i risultati sono stati scaricati usando un altro computer), fare clic sul pulsante **Clear State** (Cancella stato) per ignorare il processo inviato. Si noti che in tal modo il file dei risultati, quando sarà pronto, **non** verrà scaricato, ma **questo non equivale ad annullare il processo**. Il processo, se in esecuzione, continuerà a essere eseguito nel cloud.
14. Fare clic sul pulsante Bake per inviare il bake al cloud. Mentre un processo è in esecuzione, questo pulsante è invece **Cancel Job** (Annulla processo).
15. Prepara l'elaborazione della simulazione acustica in un computer locale. Per altre informazioni, vedere [Local bake](#Local-bake) (Bake locale).  
16. Quest'area mostra lo stato del bake. Al termine, verrà visualizzato **Downloaded** (Scaricato).

È sempre possibile ottenere informazioni complete su processi attivi, pool di calcolo e spazio di archiviazione nel [portale di Azure](https://portal.azure.com).

Mentre un processo è in esecuzione, il pulsante **Bake** viene sostituito da **Cancel Job** (Annulla processo). Usare questo pulsante per annullare il processo in corso. Verrà chiesta conferma prima che il processo venga annullato. L'annullamento di un processo non può essere cancellato, non saranno disponibili risultati e il tempo di calcolo di Azure usato verrà ugualmente addebitato.

Dopo aver avviato un bake, è possibile chiudere Unity. A seconda del progetto, del tipo di nodo e del numero di nodi, un bake cloud può richiedere diverse ore. Lo stato del processo bake verrà aggiornato quando si ricarica il progetto e si apre la finestra Acoustics (Acustica). Se il processo è stato completato, verrà scaricato il file di output.

Le credenziali di Azure vengono archiviate in modo sicuro nel computer locale e associate all'editor Unity. Vengono usate esclusivamente per stabilire una connessione sicura ad Azure.

### <a name="Estimating-bake-cost"></a> Stima dei costi del bake di Azure

Per calcolare quanto costerà un determinato bake, moltiplicare il valore visualizzato di **Estimated Compute Cost** (Costo di calcolo stimato), che indica una durata, per il costo orario nella valuta locale del **tipo di nodo di VM** selezionato. Il risultato non includerà il tempo necessario per avere i nodi operativi. Ad esempio, se come tipo di nodo si seleziona **Standard_F8s_v2**, che ha un costo di $ 0,40/ora e il costo di calcolo stimato è di 3 ore e 57 minuti, il costo stimato per l'esecuzione del processo sarà $ 0,40 * 4 ore circa = all'incirca $ 1,60. Il costo effettivo sarà probabilmente di poco superiore a causa del tempo aggiuntivo necessario per avviare i nodi. È possibile trovare il costo orario del nodo nella pagina [Prezzi di Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Come categoria selezionare "Con ottimizzazione per il calcolo" o "High Performance Computing".

### <a name="reviewing-the-bake-results"></a>Analisi dei risultati bake

Dopo aver completato il bake, verificare che i voxel e i punti di probe siano nelle posizioni previste eseguendo il plug-in di runtime. Per altre informazioni, vedere [Panoramica del processo di progettazione per l'acustica](design-process.md).

## <a name="Local-bake"></a>Bake locale
Il bake locale esegue la simulazione acustica sul PC anziché eseguirne l'offload nel cluster di calcolo di Azure Batch. Questa può essere una soluzione valida per sperimentare con l'acustica senza bisogno di una sottoscrizione di Azure. Si noti che la simulazione acustica può richiedere molta elaborazione e molto tempo in base alle dimensioni della scena, la configurazione di simulazione e la potenza di calcolo del computer su cui si esegue l'elaborazione.

### <a name="minimum-hardware-requirements"></a>Requisiti hardware minimi
Processore Intel a 64 bit con almeno 8 core e 32 GB di RAM o superiore.

Ad esempio, in un computer con 8 core con Intel Xeon E5-1660 a 3GHz e 32 GB di RAM:
* Una piccola scena con 100 probe richiede circa 2 ore per un bake grossolano e circa 32 ore per un bake con risoluzione fine.
* Una scena più grande con 1000 probe può richiedere all'incirca fino a 20 ore per un bake grossolano e 21 giorni circa per un bake con risoluzione fine.

### <a name="setup-docker"></a>Configurare Docker
Installare e configurare Docker nel computer che elaborerà la simulazione:
1. Installare il [set di strumenti di Docker](https://www.docker.com/products/docker-desktop).
2. Accedere alle impostazioni di Docker, passare alle opzioni "Avanzate" e configurare le risorse con almeno 8 GB di RAM. Più CPU si riesce ad allocare a Docker, più velocemente sarà completato il bake. ![Impostazioni di Docker di esempio](media/DockerSettings.png)
3. Passare a "Shared Drives" ("Unità condivise") e attivare la condivisione per l'unità utilizzata per l'elaborazione.![DockerDriveSharing](media/DockerSharedDrives.png)

### <a name="run-local-bake"></a>Eseguire il bake locale
1. Fare clic sul pulsante "Prepare Local Bake" ("Prepara bake locale") nella scheda Bake e selezionare la cartella in cui verranno salvati i file di input e gli script di esecuzione. Quindi è possibile eseguire il bake in qualsiasi computer, purché soddisfi i requisiti hardware minimi e abbia Docker installato, copiando la cartella in tale computer.
2. Avviare la simulazione usando lo script "runlocalbake.bat". Lo script recupera l'immagine Docker Project Acoustics con il set di strumenti necessario per l'elaborazione di simulazione e avvia la simulazione. 
3. Al termine della simulazione copiare il file .ace risultante nel progetto Unity. Per assicurarsi che Unity lo riconosca come file binario, aggiungere ".bytes" all'estensione del file (ad esempio "Scene1.ace.bytes"). I log dettagliati per la simulazione sono archiviati in "AcousticsLog.txt." In caso di problemi, condividere questo file per facilitare la diagnosi.

## <a name="Data-Files"></a>File di dati

Sono quattro i file di dati creati da questo plug-in in punti diversi. Solo uno di essi è necessario in fase di esecuzione, quindi gli altri tre sono all'interno di cartelle denominate "Editor" in modo che non vengano compilati nel progetto.

* **Assets/Editor/[NomeScena]\_AcousticsParameters.asset**: questo file archivia i dati immessi nei campi dell'interfaccia utente di acustica. Il percorso e il nome di questo file non possono essere modificati. In questo file sono archiviati altri valori che interessano il bake, ma sono per utenti avanzati e non devono essere modificati.
* **Assets/AcousticsData/Acoustics\_[NomeScena].ace.bytes**: questo file viene creato durante la simulazione del bake e contiene i dati di ricerca usati dal runtime per eseguire il rendering dell'acustica della scena. Il percorso e il nome di questo file possono essere modificati usando i campi nella scheda **Probes** (Probe).
* **Assets/AcousticsData/Editor/Acoustics_[NomeScena].vox**: questo file archivia la geometria dell'acustica voxelizzata e le proprietà dei materiali. Il calcolo viene eseguito usando il pulsante **Calculate...** (Calcola) nella scheda Probes (Probe). Il percorso e il nome di questo file possono essere modificati usando i campi nella scheda **Probes** (Probe).
* **Assets/AcousticsData/Editor/Acoustics\_[NomeScena]\_config.xml**: questo file archivia i parametri calcolati usando il pulsante **Calculate...** (Calcola) nella scheda **Probes** (Probe). Il percorso e il nome di questo file possono essere modificati usando i campi nella scheda **Probes** (Probe).

Prestare attenzione a non eliminare il file *.ace.bytes scaricato dal bake. Questo file non è ripristinabile se non effettuando di nuovo il bake della scena.

## <a name="next-steps"></a>Passaggi successivi
* Applicare i risultati del bake alle sorgenti sonore in [Panoramica del processo di progettazione per l'acustica](design-process.md).

