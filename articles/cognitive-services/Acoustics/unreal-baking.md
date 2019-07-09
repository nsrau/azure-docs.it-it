---
title: Esercitazione sul bake in Unreal con Progetto Acustica
titlesuffix: Azure Cognitive Services
description: Questo documento descrive il processo di invio di un bake dell'acustica con l'estensione dell'editor Unreal.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: michem
ms.openlocfilehash: 6b49a6b9e235414cd63eacdbad523bbda8646963
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304301"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Esercitazione sul bake in Unreal con Progetto Acustica
Questo documento descrive il processo di invio di un bake dell'acustica con l'estensione dell'editor Unreal.

Per creare un bake, sono necessari cinque passaggi:

1. Creare o taggare la mesh di navigazione del giocatore
2. Taggare la geometria dell'acustica
3. Assegnare le proprietà dei materiali acustici alla geometria
4. Visualizzare in anteprima il posizionamento del probe
5. Effettuare il bake

## <a name="open-the-project-acoustics-editor-mode"></a>Aprire la modalità di Progetto Acustica nell'editor

Importare il pacchetto del plug-in Progetto Acustica nel progetto. Per informazioni, vedere l'argomento relativo all'[integrazione in Unreal](unreal-integration.md). Dopo aver integrato il plug-in, aprire l'interfaccia utente per l'acustica facendo clic sulla nuova icona della modalità di Acustica.

![Screenshot dell'opzione Acoustics Mode dell'editor Unreal](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Taggare gli attori per l'acustica

La scheda Objects (Oggetti) è la prima scheda che viene visualizzata quando si apre la modalità di Acustica. Usare questa scheda per taggare gli attori nel livello, aggiungendo i tag **AcousticsGeometry** o **AcousticsNavigation** agli attori.

Selezionare uno o più oggetti in World Outliner (Struttura globale) oppure usare la sezione **Bulk Selection** (Selezione in blocco) per selezionare tutti gli oggetti di una categoria specifica. Dopo aver selezionato gli oggetti, usare la sezione **Tagging** (Assegnazione tag) per applicare il tag desiderato agli oggetti selezionati.

Se un oggetto non ha né il tag **AcousticsGeometry** né il tag **AcousticsNavigation**, verrà ignorato nella simulazione. Sono supportate solo le mesh statiche, le mesh di navigazione e gli orientamenti orizzontali. Se si taggano altri oggetti, verranno ignorati.

### <a name="for-reference-the-objects-tab-parts"></a>Informazioni di riferimento: parti della scheda Objects (Oggetti)

![Screenshot della scheda Objects della finestra Acoustics in Unreal](media/unreal-objects-tab-details.png)

1. Pulsanti di selezione della scheda, con la scheda **Objects** (Oggetti) selezionata. Usare questi pulsanti per scorrere i vari passaggi per effettuare un bake dell'acustica, dall'alto verso il basso.
2. Breve descrizione di che cosa fare quando si usa questa pagina.
3. Selettori disponibili per gli attori nel livello.
4. Facendo clic su **Select** (Seleziona) si selezioneranno tutti gli oggetti nel livello che corrispondono ad almeno uno dei tipi di attore selezionati.
5. Facendo clic su **Deselect all** (Deseleziona tutto) verrà annullata la selezione corrente, così come premendo il tasto ESC.
6. Usare questi pulsanti di opzione per scegliere se applicare il tag Geometry (Geometria) o Navigation (Navigazione) agli attori selezionati.
7. Facendo clic su **Tag** si aggiungerà il tag selezionato a tutti gli attori attualmente selezionati.
8. Facendo clic su **Untag** (Rimuovi tag) si rimuoverà il tag selezionato da tutti gli attori attualmente selezionati.
9. Facendo clic su **Select Tagged** (Seleziona oggetti taggati) verrà annullata la selezione corrente e verranno selezionati tutti gli attori con il tag attualmente selezionato.
10. Queste statistiche mostrano il numero di attori a cui è stato assegnato ogni tipo di tag.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Taggare la geometria di occlusione e riflessione acustica

Aprire la scheda Objects (Oggetti) della finestra Acoustics (Acustica). Contrassegnare come Acoustics Geometry (Geometria acustica) tutti gli oggetti che devono occludere, riflettere o assorbire il suono. La geometria acustica può includere elementi come suolo, pareti, tetti, finestre, vetrate, tappeti e mobili di grandi dimensioni. È possibile usare qualsiasi livello arbitrario di complessità per questi oggetti. Dato che la scena viene voxelizzata prima della simulazione, il bake di mesh estremamente dettagliate, come ad esempio alberi con molte foglie di piccole dimensioni, non è più costoso di quello di oggetti semplificati.

Non includere elementi che non influiranno sull'acustica, come mesh di collisione invisibili.

La trasformazione di un oggetto in fase di calcolo dei probe, tramite la scheda Probes (Probe) riportata di seguito, è fissa nei risultati del bake. Se si sposta uno qualsiasi degli oggetti contrassegnati nella scena, sarà necessario ripetere il calcolo dei probe ed effettuare di nuovo il bake della scena.

### <a name="create-or-tag-a-navigation-mesh"></a>Creare o taggare una mesh di navigazione

Una mesh di navigazione viene usata per inserire i punti di probe per la simulazione. È possibile usare il [volume di delimitazione della mesh di navigazione](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html) di Unreal oppure specificare una mesh di navigazione personalizzata. È necessario taggare almeno un oggetto come **Acoustics Navigation** (Navigazione acustica). Se si usa la mesh di navigazione di Unreal, assicurarsi di crearla prima.

### <a name="acoustics-volumes"></a>Volumi di Acoustics ###

È possibile personalizzare ulteriormente in modo avanzato le aree di navigazione con i **Volumi di Acoustics**. I **Volumi di Acoustics** sono attori che si possono aggiungere alla scena per consentire la selezione di aree da includere e ignorare nella mesh di navigazione. L'attore espone una proprietà che può essere alternata tra "Include" ed "Exclude". I volumi "Include" garantiscono l'inclusione solo delle aree della mesh navigazione all'interno e i volumi "Exclude" contrassegnano tali aree come da ignorare. I volumi "Exclude" vengono sempre applicati dopo i volumi "Include". Assicurarsi di taggare i **Volumi di Acoustics** come **Acoustics Navigation** usando la procedura consueta nella scheda Objects. Questi attori ***non*** vengono taggati automaticamente.

![Screenshot delle proprietà del Volume di Acoustics in Unreal](media/unreal-acoustics-volume-properties.png)

L'obiettivo dei volumi "Exclude" è principalmente conferire il controllo con granularità fine su dove non inserire probe per limitare l'utilizzo delle risorse.

![Screenshot di Volume "Exclude" di Acoustics in Unreal](media/unreal-acoustics-volume-exclude.png)

I volumi "Include" sono utili per creare sezioni manuali di una scena, ad esempio se si vuole suddividere una scena in più zone acustiche. Ad esempio, nel caso di una grande scena di molti chilometri quadrati, con due aree di interesse in cui effettuare il bake dell'acustica, è possibile trascinare due grandi volumi "Include" e produrre file ACE per ciascuno di essi, uno alla volta. Quindi, nel gioco, sarà possibile usare volumi trigger combinati con chiamate al progetto per caricare il file ACE appropriato quando il giocatore si avvicina a ciascun riquadro.

I **Volumi di Acoustics** limitano solo la navigazione, ***non*** la geometria. Ogni probe all'interno di un **Volume di Acoustics** "Include" permette comunque di ottenere tutta la geometria necessaria all'esterno del volume durante l'esecuzione di simulazioni delle onde. Pertanto, non devono essere presenti eventuali discontinuità in occlusione o altra acustica risultanti dall'attraversamento da una sezione all'altra da parte del giocatore.

## <a name="select-acoustic-materials"></a>Selezionare i materiali acustici

Dopo aver taggato gli oggetti, fare clic sul pulsante **Materials** (Materiali) per passare alla scheda Materials (Materiali). Questa scheda verrà usata per specificare le proprietà di ogni materiale nel livello. Prima dell'applicazione di tag agli attori, sarà vuota.

I materiali acustici controllano la quantità di energia sonora riflessa da ogni superficie. Il materiale acustico predefinito ha un assorbimento simile al cemento. Progetto Acustica suggerisce i materiali in base al nome dei materiali della scena.

Il tempo di riverbero di un determinato materiale in una stanza è inversamente proporzionale al coefficiente di assorbimento e la maggior parte dei materiali ha valori di assorbimento compresi tra 0,01 e 0,20. I materiali con coefficienti di assorbimento superiori a questo intervallo sono molto assorbenti. Se ad esempio il suono di una stanza è troppo riverberante, cambiare il materiale acustico dei muri, del pavimento o del soffitto con un altro più assorbente. L'assegnazione del materiale acustico si applica a tutti gli attori che usano tale materiale della scena.

![Grafico che mostra la correlazione negativa del tempo di riverbero con il coefficiente di assorbimento](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Informazioni di riferimento: Parti della scheda Materials (Materiali)

![Screenshot della scheda Objects della finestra Acoustics in Unreal](media/unreal-materials-tab-details.png)

1. Pulsante della scheda **Materials** (Materiali), usato per visualizzare questa pagina.
2. Breve descrizione di che cosa fare quando si usa questa pagina.
3. Elenco dei materiali usati nel livello, ricavato dagli attori con tag **AcousticsGeometry**. Facendo clic su un materiale qui, verranno selezionati tutti gli oggetti della scena che usano tale materiale.
4. Mostra il materiale acustico a cui è stato assegnato il materiale della scena. Fare clic su un elenco a discesa per riassegnare un materiale della scena a un materiale acustico diverso.
5. Mostra il coefficiente di assorbimento acustico del materiale selezionato nella colonna precedente. Un valore pari a zero indica un materiale perfettamente riflettente (nessun assorbimento), mentre un valore pari a 1 indica un materiale perfettamente assorbente (nessun riflesso). Se si modifica questo valore, il materiale acustico (passaggio 4) verrà aggiornato in **Custom** (Personalizzato).

Se si apportano modifiche ai materiali nella scena, sarà necessario passare da una scheda all'altra nel plug-in Progetto Acustica per visualizzare le modifiche applicate nella scheda **Materials** (Materiali).

## <a name="calculate-and-review-listener-probe-locations"></a>Calcolare ed esaminare le posizioni dei probe per l'ascoltatore

Dopo aver assegnato i materiali, passare alla scheda **Probes** (Probe).

### <a name="for-reference-parts-of-the-probes-tab"></a>Informazioni di riferimento: Parti della scheda Probes (Probe)

![Screenshot della scheda Probes della finestra Acoustics in Unreal](media/unreal-probes-tab-details.png)

1. Pulsante della scheda **Probes** (Probe) usato per visualizzare questa pagina
2. Breve descrizione di che cosa fare quando si usa questa pagina
3. Usare questa opzione per scegliere una risoluzione di simulazione grossolana o fine. Quella grossolana è più veloce, ma presenta alcuni svantaggi. Per informazioni dettagliate, vedere[Risoluzione del bake](bake-resolution.md) più avanti.
4. Questo campo consente di scegliere la posizione in cui i file di dati di acustica devono essere inseriti. Fare clic sul pulsante con "..." per usare la selezione cartelle. Per altre informazioni sui file di dati, vedere [File di dati](#Data-Files) più avanti.
5. I file di dati per questa scena verranno denominati usando il prefisso fornito qui. L'impostazione predefinita è "[NomeLivello]_AcousticsData".
6. Fare clic sul pulsante **Calculate** (Calcola) per voxelizzare la scena e calcolare le posizioni dei punti di probe. Questa operazione viene eseguita in locale sul computer e deve essere eseguita prima di creare un bake. Dopo il calcolo dei probe, i controlli precedenti verranno disabilitati e il testo di questo pulsante verrà modificato in **Clear** (Cancella). Fare clic sul pulsante **Clear** (Cancella) per cancellare i calcoli e abilitare i controlli per poter eseguire di nuovo i calcoli con nuove impostazioni.

I probe devono essere inseriti tramite il processo automatizzato disponibile nella scheda **Probes** (Probe).


### <a name="what-the-calculate-button-calculates"></a>Calcoli eseguiti con il pulsante "Calculate" (Calcola)

Il pulsante **Calculate** (Calcola) usa tutti i dati finora specificati (geometria, navigazione, materiali e impostazione di risoluzione grossolana/fine) per eseguire diversi passaggi:

1. Usa la geometria delle mesh della scena per calcolare un volume in voxel. Il volume in voxel è un volume tridimensionale che racchiude l'intera scena ed è costituito da piccoli "voxel" cubici. Le dimensioni dei voxel sono determinate dalla frequenza della simulazione, che dipende dall'impostazione **Simulation Resolution** (Risoluzione simulazione). Ogni voxel viene contrassegnato come "open air" o contenente la geometria della scena. Se un voxel contiene la geometria, viene contrassegnato con il coefficiente di assorbimento del materiale assegnato a tale geometria.
2. Usa quindi i dati di navigazione per calcolare le posizioni interessanti dal punto di vista dell'acustica in cui il giocatore potrebbe andare. Prova a trovare un set relativamente ridotto di queste posizioni, che include aree più piccole, ad esempio entrate e corridoi e quindi stanze, per aprire gli spazi. Per scene di piccole dimensioni si tratta in genere di meno di 100 posizioni, mentre scene di grandi dimensioni possono includerne fino a mille.
3. Per ognuna delle posizioni finali dell'ascoltatore calcolate, determina un numero di parametri, ad esempio quanto è "aperto" lo spazio, le dimensioni della stanza contenuta e così via.
4. I risultati di questi calcoli vengono archiviati in file nel percorso specificato (vedere [File di dati](#Data-Files) più avanti).

A seconda delle dimensioni della scena e della velocità del computer, questi calcoli possono richiedere alcuni minuti.

Dopo aver completato questi calcoli, è possibile visualizzare in anteprima sia i dati dei voxel che le posizioni dei punti di probe per assicurarsi che il bake dia buoni risultati. Ad esempio, una mesh di navigazione non valida o una geometria mancante/aggiuntiva sarà in genere rapidamente visibile nell'anteprima e sarà possibile correggerla.


## <a name="debug-display"></a>Visualizzazione del debug

Al termine del calcolo dei probe, in World Outliner (Struttura globale) verrà visualizzato un nuovo attore denominato **AcousticsDebugRenderer**. Selezionando le caselle di controllo **Render Probes** (Esegui rendering probe) e **Render Voxels** (Esegui rendering voxel) si abiliterà la visualizzazione del debug all'interno del riquadro di visualizzazione dell'editor.

![Screenshot dell'attore Debug Renderer della finestra Acoustics nell'editor Unreal](media/acoustics-debug-renderer.png)

Se non vengono visualizzati voxel o probe sovrapposti sul livello, verificare che nel riquadro di visualizzazione sia abilitato il rendering in tempo reale.

![Screenshot dell'opzione di rendering in tempo reale in Unreal](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxel

I voxel vengono visualizzati nella finestra della scena come cubi verdi intorno alla geometria presente. I voxel contenenti solo aria non vengono visualizzati. Intorno all'intera scena è presente una grande casella verde corrispondente al volume in voxel completo che verrà usato nella simulazione.
Spostarsi nella scena e verificare la presenza di voxel per la geometria che determina occlusione acustica. Controllare inoltre che gli oggetti non acustici, come le mesh di collisione, non siano stati voxelizzati. Per visualizzare i voxel, la telecamera della scena deve trovarsi a non più di circa 5 metri dall'oggetto.

Se si confrontano i voxel creati con risoluzione grossolana con quelli creati con risoluzione fine, si noterà che nel primo caso i voxel sono due volte più grandi.

![Screenshot dell'anteprima dei voxel di Progetto Acustica nell'editor Unreal](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Punti di probe

I punti di probe corrispondono alle possibili posizioni del giocatore. Durante il bake, la simulazione calcola l'acustica connettendo tutte le possibili posizioni delle sorgenti a ogni punto di probe. In fase di esecuzione, la posizione dell'ascoltatore viene interpolata tra i punti di probe nelle vicinanze.

È importante controllare che esistano punti di probe ovunque è previsto che il giocatore si sposti nella scena. I punti di probe vengono inseriti nella mesh di navigazione dal motore di Progetto Acustica e non possono essere spostati o modificati. Verificare quindi che la mesh di navigazione copra tutte le possibili posizioni del giocatore esaminando i punti di probe.

![Screenshot dell'anteprima dei probe di Progetto Acustica nell'editor Unreal](media/unreal-probes-preview.png)

Per informazioni più dettagliate sulla risoluzione grossolana o fine, vedere [Risoluzione del bake](bake-resolution.md).

## <a name="bake-your-level-using-azure-batch"></a>Effettuare il bake del livello con Azure Batch

È possibile effettuare il bake della scena con un cluster di elaborazione nel cloud usando il servizio Azure Batch. Il plug-in Progetto Acustica di Unreal si connette direttamente ad Azure Batch per creare un'istanza di un cluster di Azure Batch per ogni bake e gestire ed eliminare tale cluster. Nella scheda Bake immettere le credenziali di Azure, selezionare un tipo e una dimensione di computer cluster e fare clic su Bake.

### <a name="for-reference-parts-of-the-bake-tab"></a>Informazioni di riferimento: Parti della scheda Bake

![Screenshot della scheda Bake della finestra Acoustics in Unreal](media/unreal-bake-tab-details.png)

1. Pulsante della scheda Bake usato per visualizzare questa pagina.
2. Breve descrizione di che cosa fare in questa pagina.
3. Campi in cui immettere le credenziali di Azure dopo aver creato l'account Azure. Per altre informazioni, vedere [Creare un account Azure Batch](create-azure-account.md).
4. Avviare il portale di Azure per gestire le sottoscrizioni, monitorare l'utilizzo, visualizzare le informazioni di fatturazione e così via. 
5. Tipo di nodo di calcolo di Azure Batch da usare per il calcolo. Il tipo di nodo deve essere supportato dalla località del data center di Azure. Se non si è sicuri, lasciare **Standard_F8s_v2**.
6. Numero di nodi da usare per questo calcolo. Il numero immesso in questo campo influisce sul tempo necessario per completare il bake ed è limitato dall'allocazione di core di Azure Batch. L'allocazione predefinita consente solo due nodi da 8 core o un nodo da 16 core, ma può essere espansa. Per altre informazioni sui vincoli delle allocazioni di core, vedere [Creare un account Azure Batch](create-azure-account.md).
7. Selezionare questa casella di controllo per configurare il pool di calcolo per l'uso di [nodi a priorità bassa](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). I nodi di calcolo a priorità bassa hanno un costo molto minore ma potrebbero non essere sempre disponibili o essere interrotti in qualsiasi momento.
8. Intervallo di tempo trascorso previsto per l'esecuzione del processo nel cloud. Non include il tempo di avvio dei nodi. Dopo l'avvio dell'esecuzione del processo, indica quanto tempo deve trascorrere prima di ottenere i risultati. Si noti che questa è solo una stima.
9. Tempo di elaborazione totale necessario per eseguire le simulazioni. Corrisponde alla quantità totale di tempo di calcolo dei nodi che verrà usata in Azure. Per altre informazioni sull'uso di questo valore, vedere [Stima del costo del bake](#Estimating-bake-cost).
10. Fare clic sul pulsante Bake per inviare il bake al cloud. Mentre un processo è in esecuzione, questo pulsante è invece **Cancel Job** (Annulla processo). Se si verificano errori in questa scheda o se il flusso di lavoro della scheda **Probes** (Probe) non è stato completato, questo pulsante sarà disabilitato.
11. Numero di probe per la scena, calcolato nella scheda **Probes** (Probe). Il numero di probe determina il numero di simulazioni che devono essere eseguite nel cloud. Non è possibile specificare più nodi che probe.
12. Questo messaggio indica lo stato corrente del processo oppure gli errori nella scheda, se presenti.

È sempre possibile ottenere informazioni complete su processi attivi, pool di calcolo e spazio di archiviazione nel [portale di Azure](https://portal.azure.com).

Mentre un processo è in esecuzione, il pulsante **Bake** viene sostituito da **Cancel Job** (Annulla processo). Usare questo pulsante per annullare il processo in corso. L'annullamento di un processo non può essere cancellato e il tempo di calcolo di Azure usato prima dell'annullamento verrà comunque addebitato.

Dopo aver avviato un bake, è possibile chiudere Unreal. A seconda del progetto, del tipo di nodo e del numero di nodi, un bake cloud può richiedere diverse ore. Lo stato del processo bake verrà aggiornato quando si ricarica il progetto e si apre la finestra Acoustics (Acustica). Se il processo è stato completato, verrà scaricato il file di output.

Le credenziali di Azure vengono archiviate in modo sicuro nel computer locale e associate al progetto Unreal. Vengono usate esclusivamente per stabilire una connessione sicura ad Azure.

### <a name="Estimating-bake-cost"></a> Stima dei costi del bake di Azure

Per calcolare quanto costerà un determinato bake, moltiplicare il valore visualizzato di **Estimated Compute Cost** (Costo di calcolo stimato), che indica una durata, per il costo orario nella valuta locale del **tipo di nodo di VM** selezionato. Il risultato non includerà il tempo necessario per avere i nodi operativi. Ad esempio, se come tipo di nodo si seleziona **Standard_F8s_v2**, che ha un costo di $ 0,40/ora e il costo di calcolo stimato è di 3 ore e 57 minuti, il costo stimato per l'esecuzione del processo sarà $ 0,40 * 4 ore circa = all'incirca $ 1,60. Il costo effettivo sarà probabilmente di poco superiore a causa del tempo aggiuntivo necessario per avviare i nodi. È possibile trovare il costo orario del nodo nella pagina [Prezzi di Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Come categoria selezionare "Con ottimizzazione per il calcolo" o "High Performance Computing".

### <a name="reviewing-the-bake-results"></a>Analisi dei risultati bake

Dopo aver completato il bake, verificare che i voxel e i punti di probe siano nelle posizioni previste eseguendo il plug-in di runtime.

## <a name="Data-Files"></a>File di dati

Sono quattro i file di dati creati da questo plug-in in punti diversi. Solo uno di essi è necessario in fase di esecuzione e viene inserito nella cartella Content/Acoustics del progetto, aggiunta automaticamente al percorso di creazione pacchetto del progetto. Gli altri tre si trovano nella cartella dei dati di acustica e non vengono inclusi nel pacchetto.

* **[Progetto]/Config/ProjectAcoustics.cfg**: questo file contiene i dati immessi nei campi nell'interfaccia utente della modalità di Acustica. Il percorso e il nome di questo file non possono essere modificati. In questo file sono archiviati altri valori che interessano il bake, ma sono per utenti avanzati e non devono essere modificati.
* **[Progetto]/Content/Acoustics/[NomeLivello]\_AcousticsData.ace**: questo file viene creato durante la simulazione del bake e contiene i dati di ricerca usati dal runtime per eseguire il rendering dell'acustica della scena. Il percorso e il nome di questo file possono essere modificati usando i campi nella scheda **Probes** (Probe). Se si desidera rinominare questo file dopo che è stato creato, eliminare il UAsset dal progetto Unreal, rinominare il file al di fuori di Unreal in Esplora File e quindi importare nuovamente questo file in Unreal per produrre un nuovo UAsset. La ridenominazione di UAsset da sola non funzionerà.
* **[Progetto]/Plugins/ProjectAcoustics/AcousticsData/[NomeLivello]\_AcousticsData.vox**: questo file contiene la geometria di acustica voxelizzata e le proprietà del materiale. Il calcolo viene eseguito usando il pulsante **Calculate** (Calcola) nella scheda **Probes** (Probe). Il percorso e il nome di questo file possono essere modificati usando i campi nella scheda **Probes** (Probe).
* **[Progetto]/Plugins/ProjectAcoustics/AcousticsData/[NomeLivello]\_AcousticsData\_config.xml**: questo file contiene i parametri calcolati usando il pulsante **Calculate** (Calcola) nella scheda **Probes** (Probe). Il percorso e il nome di questo file possono essere modificati usando i campi nella scheda **Probes** (Probe).

Prestare attenzione a non eliminare il file con estensione ace scaricato da Azure. Questo file non è ripristinabile se non effettuando di nuovo il bake della scena.

## <a name="next-steps"></a>Passaggi successivi
* Esplorare i [controlli di progettazione per Unreal](unreal-workflow.md)
* Esplorare i [concetti relativi alla progettazione per Progetto Acustica](design-process.md)

