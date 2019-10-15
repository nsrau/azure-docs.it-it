---
title: Esercitazione sul bake in Unity con Progetto Acustica
titlesuffix: Azure Cognitive Services
description: Questa esercitazione descrive il bake con Progetto Acustica in Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 310decf8053ea16ba46250ba3aabe81c9c254e5e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243116"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Esercitazione sul bake in Unity con Progetto Acustica
Questa esercitazione descrive il bake dell'acustica con Progetto Acustica in Unity.

Requisiti software:
* [Unity 2018.2+](https://unity3d.com) per Windows o MacOS
* [Plug-in di Progetto Acustica integrato nel progetto Unity](unity-integration.md) o [contenuto di esempio di Progetto acustica per Unity](unity-quickstart.md)
* *Facoltativo:* un [account Azure Batch](create-azure-account.md) per accelerare il bake tramite cloud computing

## <a name="open-the-project-acoustics-bake-window"></a>Aprire la finestra di bake di Progetto Acustica nell'editor
In Unity scegliere **Acoustics** (Acustica) dal menu **Window** (Finestra).

![Editor Unity con l'opzione Acoustics evidenziata nel menu Window](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Creare una mesh di navigazione
Progetto Acustica usa una mesh di navigazione per posizionare i punti di probe dell'ascoltatore per la simulazione. È possibile usare il [flusso di lavoro della mesh di navigazione](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) di Unity o un altro pacchetto di modellazione 3D per progettare la mesh.

## <a name="mark-acoustic-scene-objects"></a>Contrassegnare oggetti acustici della scena
Progetto Acustica si basa su due tipi di oggetti scena per la simulazione:
- Gli oggetti che riflettono e occludono il suono nella simulazione
- La mesh di navigazione del lettore, che vincola i punti di probe dell'ascoltatore nella simulazione

Entrambi i tipi di oggetto vengono contrassegnati usando la scheda **Objects** (Oggetti).

Dato che contrassegnando gli oggetti si aggiungono semplicemente i componenti *AcousticsGeometry* o *AcousticsNavigation* all'oggetto, è anche possibile usare il [flusso di lavoro dei componenti Unity standard](https://docs.unity3d.com/Manual/UsingComponents.html) per contrassegnare o rimuovere il contrassegno degli oggetti. È possibile contrassegnare solo renderer di mesh e terreni. Tutti gli altri tipi di oggetto verranno ignorati. Le caselle di controllo selezionano o deselezionano tutti gli oggetti interessati.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Contrassegnare la geometria di occlusione e riflessione acustica
Aprire la scheda **Objects** (Oggetti) della finestra **Acoustics** (Acustica). Contrassegnare come *Acoustics Geometry* (Geometria acustica) tutti gli oggetti che devono occludere, riflettere o assorbire il suono. La geometria acustica può includere elementi come suolo, pareti, tetti, finestre, vetrate, tappeti e mobili di grandi dimensioni. È possibile usare qualsiasi livello arbitrario di complessità per questi oggetti. Dato che la scena viene voxelizzata prima della simulazione, il bake di mesh estremamente dettagliate, come ad esempio alberi con molte foglie, non è più costoso di quello di oggetti semplificati.

Non includere elementi che non influiranno sull'acustica, come mesh di collisione invisibili.

La trasformazione di un oggetto durante il calcolo dei probe, tramite la scheda **Probes** (Probe), viene fissata nei risultati del bake. Se uno o più oggetti contrassegnati nella scena vengono spostati in un secondo momento, è necessario ripetere il calcolo dei probe e il bake.

### <a name="mark-the-navigation-mesh"></a>Contrassegnare la mesh di navigazione
Le mesh di navigazione create con il flusso di lavoro di Unity verranno selezionate dal sistema Acoustics. Per usare le proprie mesh, contrassegnarle dalla scheda **Objects** (Oggetti).

### <a name="for-reference-the-objects-tab-parts"></a>Informazioni di riferimento: parti della scheda Objects (Oggetti)
Le parti della scheda (illustrate dopo le descrizioni) sono:

1. Pulsanti di selezione della scheda (con la scheda **Objects** (Oggetti) selezionata). Usare questi pulsanti per spostarsi tra i vari passaggi di un bake di acustica, da sinistra a destra.
1. Breve descrizione di cosa è possibile fare con questa scheda.
1. Filtri disponibili per la finestra della gerarchia. Usare queste opzioni per filtrare nella finestra della gerarchia gli oggetti del tipo specificato in modo da poterli contrassegnare facilmente. Se non è ancora stato selezionato alcun valore per l'acustica, selezionando le ultime due opzioni non verrà visualizzato nulla. Queste opzioni sono però utili per trovare gli oggetti dopo che sono stati contrassegnati.
1. Quando non è selezionato alcun oggetto, questa sezione mostra lo stato di tutti gli oggetti nella scena.
    * Totale: numero totale di oggetti attivi non nascosti nella scena.
    * Ignored (Ignorati): numero di oggetti diversi da renderer di mesh o terreni.
    * Mesh: numero di oggetti renderer di mesh.
    * Terrain (Terreno): numero di oggetti terreno.
    * Geometry (Geometria): numero di oggetti mesh o terreno contrassegnati come **Acoustics Geometry** (Geometria acustica).
    * Navigation (Navigazione): numero di oggetti mesh o terreno contrassegnati come **Acoustics Navigation** (Navigazione acustica). Questo numero non include gli elementi NavMesh di Unity.
1. Numero totale di oggetti contrassegnabili nella scena, ovvero solo i renderer di mesh e i terreni. Usare le caselle di controllo per contrassegnare (aggiungere il componente appropriato a) tali oggetti come geometria o navigazione per l'acustica.
1. Quando non viene selezionato nulla, questa nota ricorda di selezionare gli oggetti per contrassegnarli, se necessario. È anche possibile selezionare una o entrambe le caselle di controllo per contrassegnare tutti gli oggetti nella scena.
1. Quando sono presenti oggetti selezionati, questa sezione mostra lo stato solo degli oggetti selezionati.
1. Numero totale di oggetti selezionati contrassegnabili. Selezionando o deselezionando le caselle di controllo viene aggiunto o rimosso il contrassegno solo sugli oggetti selezionati.

Se nella scena non sono selezionati elementi, l'aspetto della scheda **Objects** (Oggetti) è simile all'immagine seguente.

![Scheda Objects della finestra Acoustics senza alcun elemento selezionato](media/objects-tab-no-selection-detail.png)

Se nella finestra della scena o della gerarchia sono selezionati elementi, l'aspetto della scheda è simile a quello dell'immagine seguente.

![Scheda Objects della finestra Acoustics con elementi selezionati](media/objects-tab-selection-detail.png)

Se alcuni oggetti sono contrassegnati e altri no, le caselle di controllo appropriate mostrano un valore "misto", come nell'immagine seguente.

![Scheda Objects della finestra Acoustics con una selezione mista di icone evidenziata](media/mixed-object-selection-detail.png)

Selezionare la casella di controllo per contrassegnare tutti gli elementi. Deselezionarla per rimuovere il contrassegno da tutti gli oggetti.

Gli oggetti possono essere contrassegnati sia per la geometria che per la navigazione.

## <a name="select-acoustic-materials"></a>Selezionare i materiali acustici
Dopo aver contrassegnato gli oggetti, selezionare il pulsante **Materials** (Materiali). Quindi assegnare i materiali acustici. Il sistema dei materiali di Progetto Acustica è legato al sistema dei materiali visivi di Unity. Affinché due oggetti abbiano materiali acustici separati, è necessario che abbiano materiali visivi separati.

La selezione dei materiali acustici determina la quantità di energia sonora riflessa da ogni superficie. Il materiale acustico predefinito ha un assorbimento simile all'acciaio. Progetto Acustica suggerisce i materiali in base al nome dei materiali visivi. È anche possibile assegnare il materiale acustico **Custom** (Personalizzato) a un materiale per attivare un dispositivo di scorrimento regolabile del coefficiente di assorbimento.

Il tempo di riverbero di un determinato materiale in una stanza è inversamente proporzionale al coefficiente di assorbimento. La maggior parte dei materiali ha valori di assorbimento compresi tra 0,01 e 0,20. I materiali con coefficienti di assorbimento non compresi in questo intervallo sono molto assorbenti.

![Grafico che mostra la correlazione negativa fra il tempo di riverbero e il coefficiente di assorbimento.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Informazioni di riferimento: Parti della scheda Materials (Materiali)
![Scheda Materials della finestra Acoustics in Unity](media/materials-tab-detail.png)
1. Il pulsante **Materials** (Materiali) visualizza questa scheda.
2. Breve descrizione di cosa è possibile fare con questa scheda.
3. Se questa casella di controllo è selezionata, vengono elencati solo i materiali usati dagli oggetti contrassegnati come **Acoustics Geometry** (Geometria acustica). In caso contrario, vengono elencati tutti i materiali usati nella scena.
4. Usare queste opzioni per cambiare l'ordine delle opzioni in un menu nella colonna **Acoustics** (Acustica) (6). Ordinare i materiali acustici per **Name** (Nome) o **Absorptivity** (Assorbività), in ordine crescente.
5. Elenco dei materiali usati nella scena, in ordine alfabetico. Se la casella di controllo **Show Marked Only** (Mostra solo contrassegnati) è selezionata (3), vengono visualizzati solo i materiali usati dagli oggetti contrassegnati come **Acoustics Geometry** (Geometria acustica). Se si seleziona un materiale qui vengono selezionati tutti gli oggetti della scena che usano quel materiale.
6. Materiale acustico a cui è stato assegnato il materiale della scena. Selezionare un elemento qualsiasi per cambiare il materiale acustico assegnato al materiale della scena. Per cambiare l'ordinamento di questi menu, usare le opzioni di **Sort Acoustics By** (Ordina acustica per) (4).
7. Coefficiente di assorbimento acustico del materiale selezionato nella colonna a sinistra (6). Il valore 0 (zero) indica un materiale perfettamente riflettente (nessun assorbimento), mentre 1 indica un materiale perfettamente assorbente (nessun riflesso). Il coefficiente di assorbimento può essere modificato solo se il materiale selezionato è **Custom** (Personalizzato).
8. Per un materiale contrassegnato come **Custom** (Personalizzato) è attivato il dispositivo di scorrimento. È possibile spostare il dispositivo di scorrimento o digitare un valore per assegnare un coefficiente di assorbimento.

## <a name="calculate-and-review-listener-probe-locations"></a>Calcolare ed esaminare le posizioni dei probe per l'ascoltatore
Dopo aver assegnato i materiali, passare alla scheda **Probes** (Probe). Selezionare **Calculate** (Calcola) per posizionare i punti di probe dell'ascoltatore per la simulazione.

### <a name="what-the-calculate-button-does"></a>Cosa fa il pulsante "Calculate" (Calcola)
Il pulsante **Calculate** (Calcola) usa la geometria della scena acustica selezionata per preparare la scena per la simulazione:

- Usa la geometria delle mesh della scena per calcolare un *volume in voxel*. Il volume in voxel è il volume dell'intera scena ed è costituito da piccoli "voxel" cubici. Le dimensioni dei voxel sono determinate dalla frequenza della simulazione, che è controllata dall'impostazione **Simulation Resolution** (Risoluzione simulazione). Ogni voxel viene contrassegnato come "open air" o contenente la geometria della scena. Se un voxel contiene la geometria, viene contrassegnato con il coefficiente di assorbimento del materiale assegnato a tale geometria.
- Usa le mesh di navigazione per posizionare i punti di probe dell'ascoltatore. L'algoritmo bilancia il conflitto tra copertura spaziale e durata e dimensioni dei file della simulazione. Mira a garantire che corridoi stretti e piccoli spazi abbiano sempre una copertura. Il numero di punti di probe varia generalmente da 100 per scene piccole a poche migliaia per scene grandi.

A seconda delle dimensioni della scena e della velocità del computer, questi calcoli possono richiedere alcuni minuti.

### <a name="review-voxel-and-probe-placement"></a>Esaminare il posizionamento di voxel e probe
Visualizzare in anteprima il posizionamento dei dati voxel e dei punti di probe per verificare che sia possibile effettuare il bake della scena. Una mesh di navigazione incompleta o una geometria acustica assente o in eccesso è in genere facilmente visibile nell'anteprima. Il posizionamento di voxel e probe può essere abilitato o disabilitato usando il menu **Gizmos**.

![Menu Gizmos in Unity](media/gizmos-menu.png)

I voxel che contengono la geometria acustica sono visualizzati come cubi verdi. Esplorare la scena e verificare che tutti gli elementi che devono costituire la geometria siano formati da voxel. Per visualizzare i voxel, la telecamera della scena deve trovarsi a non più di circa 5 metri dall'oggetto.

Se si confrontano i voxel creati con risoluzione grossolana con quelli creati con risoluzione fine, si noterà che nel primo caso i voxel sono grandi il doppio.

![Anteprima dei voxel con risoluzione grossolana nell'editor Unity](media/voxel-cubes-preview.png)

I risultati della simulazione vengono interpolati tra le posizioni dei punti di probe dell'ascoltatore in runtime. Verificare che siano presenti punti di probe nei pressi di qualsiasi posizione in cui è previsto che il lettore si sposti nella scena.

![Anteprima dei probe nell'editor Unity](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Prestare attenzione ai nomi delle scene
Il nome della scena viene usato per connettere la scena ai file in cui sono archiviate la voxelizzazione e la posizione dei punti di probe. Se si rinomina la scena dopo che i punti di probe sono stati calcolati, i dati di posizionamento e di assegnazione dei materiali vengono persi e devono essere ottenuti di nuovo.

### <a name="for-reference-parts-of-the-probes-tab"></a>Informazioni di riferimento: Parti della scheda Probes (Probe)
![Scheda Probes della finestra Acoustics in Unity](media/probes-tab-detail.png)

1. Il pulsante **Probes** (Probe) visualizza questa scheda.
2. Breve descrizione di cosa è possibile fare in questa scheda.
3. Usare queste opzioni per impostare una risoluzione grossolana o fine per la simulazione. Quella grossolana è più veloce, ma presenta alcuni svantaggi. Per informazioni dettagliate, vedere[Risoluzione del bake](bake-resolution.md).
4. Specifica la posizione in cui inserire i file di dati acustici. Selezionare il pulsante " **...** " per accedere alla selezione cartelle. La posizione predefinita è *Assets/AcousticsData*. In questa posizione viene creata anche una sottocartella *Editor*. Per altre informazioni, vedere [File di dati aggiunti dal processo di bake](#Data-Files) più avanti in questo articolo.
5. Il prefisso specificato qui viene usato per assegnare un nome ai file di dati per questa scena. Il prefisso predefinito è "Acoustics_ *[Nome scena]* ".
6. Una volta calcolati i probe, i controlli appena descritti vengono disabilitati. Selezionare il pulsante **Clear** (Cancella) per cancellare i calcoli e abilitare i controlli per poter eseguire di nuovo i calcoli con nuove impostazioni.
7. Selezionare **Calculate** (Calcola) per voxelizzare la scena e calcolare le posizioni dei punti di probe. Il calcolo viene eseguito localmente nel computer. Deve essere eseguito prima del bake.

In questa versione di Progetto Acustica i probe non possono essere posizionati manualmente. Usare il processo automatizzato nella scheda **Probes**.

Per altre informazioni sulla risoluzione grossolana rispetto a quella fine, vedere [Risoluzione del bake](bake-resolution.md).

## <a name="bake-your-scene-by-using-azure-batch"></a>Effettuare il bake della scena con Azure Batch
È possibile effettuare il bake della scena in un cluster di elaborazione nel cloud usando il servizio Azure Batch. Il plug-in Progetto Acustica di Unity si connette direttamente ad Azure Batch per creare un'istanza di un cluster di Azure Batch per ogni bake e gestire ed eliminare tale cluster. Nella scheda **Bake** immettere le credenziali di Azure, selezionare un tipo e una dimensione di computer cluster e quindi selezionare **Bake**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Informazioni di riferimento: parti della scheda Bake
![Scheda Bake della finestra Acoustics in Unity](media/bake-tab-details.png)

1. Il pulsante **Bake** visualizza questa scheda.
2. Breve descrizione di cosa è possibile fare in questa scheda.
3. Immettere le credenziali di Azure in questi campi dopo aver creato l'account Azure. Per altre informazioni, vedere [Creare un account Azure Batch](create-azure-account.md).
4. Campo di tag immagine Docker per il set di strumenti di acustica.
5. Apre il portale di Azure per gestire le sottoscrizioni, monitorare l'utilizzo e visualizzare le informazioni di fatturazione.
6. Specifica il tipo di nodo di calcolo di Azure Batch da usare per il calcolo. Il tipo di nodo deve essere supportato dalla località del data center di Azure. Se non si è sicuri, lasciare **Standard_F8s_v2**.
7. Numero di nodi da usare per il calcolo. Questo numero influisce sul tempo di esecuzione del bake. È limitato dall'allocazione dei core di Azure Batch. L'allocazione predefinita consente solo due nodi da 8 core o un nodo da 16 core, ma può essere espansa. Per altre informazioni sui vincoli delle allocazioni di core, vedere [Creare un account Azure Batch](create-azure-account.md).
8. Selezionare questa casella di controllo per configurare il pool di calcolo per l'uso di [nodi a priorità bassa](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). I nodi di calcolo a priorità bassa hanno un costo molto inferiore, ma potrebbero non essere sempre disponibili o essere sostituiti in qualsiasi momento.
9. Numero di probe per la scena, calcolato nella scheda **Probes** (Probe). Il numero di probe determina il numero di simulazioni che devono essere eseguite nel cloud. Non è possibile specificare un numero di nodi superiore ai probe.
10. Stima del tempo necessario per l'esecuzione del processo nel cloud, escluso il tempo di avvio del nodo. Dopo l'avvio dell'esecuzione del processo, questo campo mostra una stima del tempo necessario per la restituzione dei risultati.
11. Tempo di elaborazione totale necessario per eseguire le simulazioni. Questo valore corrisponde alla quantità totale di tempo di calcolo dei nodi che verrà usata in Azure. Per altre informazioni, vedere [Stimare il costo del bake di Azure](#Estimating-bake-cost) più avanti in questo articolo.
12. Questo messaggio indica dove verranno salvati i risultati del bake al termine del processo.
13. *(Solo uso avanzato)* Questo pulsante impone a Unity di non tenere traccia di un bake inviato dall'utente. Ad esempio, se i risultati sono stati scaricati usando un altro computer, selezionare il pulsante **Clear State** (Cancella stato) per non salvare quel processo. Il file dei risultati, una volta pronto, *non* verrà scaricato. *La cancellazione dello stato non è equivalente all'annullamento del processo. Il processo, se in esecuzione, continuerà a essere eseguito nel cloud.*
14. Selezionare questo pulsante per inviare il bake al cloud. Mentre un processo è in esecuzione, questo pulsante diventa **Cancel Job** (Annulla processo).
15. Selezionare questo pulsante per preparare l'elaborazione della [simulazione acustica nel PC](#Local-bake).
16. Quest'area mostra lo stato del bake. Dopo il completamento del bake, lo stato diventa "Downloaded" (Scaricato).

È sempre possibile ottenere informazioni complete su processi attivi, pool di calcolo e spazio di archiviazione nel [portale di Azure](https://portal.azure.com).

Mentre un processo è in esecuzione, l'etichetta del pulsante **Bake** diventa **Cancel Job** (Annulla processo). Usare questo pulsante per annullare il processo in corso. Verrà richiesta conferma. L'annullamento di un processo è irreversibile. Quando si annulla un processo non saranno disponibili risultati, ma il tempo di calcolo di Azure usato verrà ugualmente addebitato.

Dopo aver avviato un bake, è possibile chiudere Unity. A seconda del progetto, del tipo di nodo e del numero di nodi, un bake cloud può richiedere diverse ore. Lo stato del processo bake verrà aggiornato quando si ricarica il progetto e si apre la finestra Acoustics (Acustica). Se il processo è stato completato, verrà scaricato il file di output.

Per garantire la sicurezza, le credenziali di Azure vengono archiviate nel computer locale e associate all'editor Unity. Vengono usate esclusivamente per stabilire una connessione sicura ad Azure.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Trovare lo stato di un processo in esecuzione nel portale di Azure

1. Trovare l'ID del processo bake nella scheda **Bake**.

    ![ID del processo bake nella scheda Bake di Unity](media/unity-job-id.png)  

2. Aprire il [portale di Azure](https://portal.azure.com), passare all'account Batch usato per il bake e selezionare **Processi**.

    ![Collegamento Processi nel portale di Azure](media/azure-batch-jobs.png)  

3. Cercare l'ID processo nell'elenco dei processi.

   ![Stato del processo bake evidenziato nel portale di Azure](media/azure-bake-job-status.png)  

4. Selezionare l'ID processo per visualizzare lo stato delle attività correlate e lo stato generale del processo.

   ![Stato dell'attività di bake](media/azure-batch-task-state.png)  


### <a name="Estimating-bake-cost"></a> Stimare il costo del bake di Azure

Per stimare quanto costerà un bake, iniziare con il valore di **Estimated Compute Cost** (Costo di calcolo stimato), che indica una durata. Moltiplicare questo valore per il costo orario nella valuta locale per il tipo di nodo di macchina virtuale selezionato in **VM Node Type**. Tenere presente che il risultato non includerà il tempo necessario per l'avvio dei nodi.

Supponiamo ad esempio di selezionare **Standard_F8s_v2** come tipo di nodo, che ha un costo orario di € 0,40. Se il valore di **Estimated Compute Cost** (Costo di calcolo stimato) è pari a 3 ore e 57 minuti, il costo stimato per l'esecuzione del processo sarà pari a € 0,40 * ~4 ore = ~€ 1,60. Il costo effettivo sarà probabilmente di poco superiore a causa del tempo aggiuntivo necessario per avviare i nodi.

Per informazioni sui costi orari dei nodi, vedere [Prezzi di Macchine virtuali Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Selezionare **Ottimizzate per il calcolo** o **High Performance Computing (HPC)** come categoria.

## <a name="Local-bake"></a> Effettuare il bake della scena nel PC
È possibile effettuare il bake della scena anche nel proprio PC. Questo metodo può essere utile per eseguire esperimenti con l'acustica in scene di piccole dimensioni prima di creare un account Azure Batch. Tenere presente, però, che la simulazione dell'acustica locale può richiedere molto tempo, a seconda delle dimensioni della scena.

### <a name="minimum-hardware-requirements"></a>Requisiti hardware minimi
* Un processore x86-64 con almeno 8 core e 32 GB di RAM
* [Hyper-V abilitato](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) per l'esecuzione di Docker

Ad esempio, nei test di Microsoft su un computer a 8 core, Intel Xeon E5-1660 a 3 GHz e 32 GB di RAM:
* Una piccola scena con 100 probe ha richiesto circa 2 ore per un bake grossolano o 32 ore per un bake fine.
* Una scena di medie dimensioni con 1.000 probe ha richiesto circa 20 ore per un bake grossolano o 21 giorni per un bake fine.

### <a name="set-up-docker"></a>Configurare Docker
Installare e configurare Docker nel computer che elaborerà la simulazione:
1. Installare [Docker Desktop](https://www.docker.com/products/docker-desktop).
2. Aprire la finestra delle impostazioni di Docker, passare ad **Advanced** (Avanzate) e configurare le risorse per almeno 8 GB di RAM. Più CPU si riesce ad allocare a Docker, più velocemente sarà completato il bake.  
![Impostazioni di Docker di esempio](media/docker-settings.png)
1. Passare a **Shared Drives** (Unità condivise) e attivare la condivisione per l'unità usata per l'elaborazione.  
![Opzioni di Docker per le unità condivise](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>Eseguire il bake locale
1. Selezionare il pulsante **Prepare Local Bake** (Prepara bake locale) nella scheda **Bake**. Selezionare quindi il percorso di una cartella in cui salvare i file di input e gli script di esecuzione. Quindi è possibile eseguire il bake in qualsiasi computer, purché soddisfi i requisiti hardware minimi e vi si installi Docker copiando la cartella in tale computer.
2. Per avviare la simulazione, eseguire lo script *runlocalbake.bat* in Windows o lo script *runlocalbake.sh* in MacOS. Lo script recupera l'immagine Docker di Progetto Acustica con il set di strumenti necessario per l'elaborazione della simulazione e avvia la simulazione.
3. Al termine della simulazione, copiare il file con estensione *ace* risultante nel progetto Unity. Per assicurarsi che Unity lo riconosca come file binario, aggiungere ".bytes" all'estensione del file (ad esempio "Scene1.ace.bytes"). I log dettagliati della simulazione sono archiviati in *AcousticsLog.txt*. Se si verificano problemi, esaminare questo file per facilitare la diagnosi del problema.

## <a name="Data-Files"></a> File di dati aggiunti dal processo di bake

Durante il processo di bake vengono creati i quattro file di dati seguenti. Uno contiene i risultati della simulazione e ha il titolo specificato dall'utente. Gli altri archiviano i dati correlati all'editor Unity.

Risultato della simulazione:
* *Assets/AcousticsData/Acoustics\_[NomeScena].ace.bytes*: Questo file è la tabella di ricerca di runtime. Contiene i risultati della simulazione e gli elementi della scena acustica voxelizzati. È possibile cambiare il nome e il percorso del file nella scheda **Probes**.

   *Prestare attenzione a non eliminare il file dei risultati della simulazione. Questo file non è recuperabile se non effettuando di nuovo il bake della scena.*

File di dati dell'editor:
* *Assets/Editor/[NomeScena]\_AcousticsParameters.asset*: in questo file vengono archiviati i dati immessi nei campi nell'interfaccia utente di Acoustics. Non è possibile cambiare il nome e il percorso del file.
* *Assets/AcousticsData/Editor/Acoustics_[NomeScena].vox*: questo file archivia la geometria dell'acustica voxelizzata e le proprietà dei materiali che vengono calcolate quando si seleziona il pulsante **Calculate** (Calcola) nella scheda **Probes** (Probe). È possibile cambiare il nome e il percorso del file nella scheda **Probes**.
* *Assets/AcousticsData/Editor/Acoustics\_[NomeScena]\_config.xml*: in questo file vengono archiviati i parametri della simulazione che vengono calcolati quando si seleziona **Calculate** (Calcola). È possibile cambiare il nome e il percorso del file nella scheda **Probes**.

## <a name="set-up-the-acoustics-lookup-table"></a>Configurare la tabella di ricerca dell'acustica
Trascinare l'oggetto Prefab **Progetto Acustica** dal pannello del progetto alla scena:

![Scheda Prefab della finestra Acoustics in Unity](media/acoustics-prefab.png)

Selezionare l'oggetto gioco **ProjectAcoustics** e passare al relativo pannello di ispezione. Specificare la posizione dei risultati del bake (il file con estensione ace in *Assets/AcousticsData*): Trascinarlo nello script Acoustics Manager o selezionare il pulsante circolare accanto alla casella di testo.

![Scheda Manager Prefab della finestra Acoustics in Unity](media/acoustics-manager.png)

## <a name="next-steps"></a>Passaggi successivi
* Esaminare i [controlli di progettazione di Unity](unity-workflow.md).
* Esplorare i [concetti relativi alla progettazione di Progetto Acustica](design-process.md)
