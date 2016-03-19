<properties 
   pageTitle="Note sulla versione di StorSimple Virtual Array| Microsoft Azure"
   description="Vengono illustrati i principali problemi in sospeso e le soluzioni per StorSimple Virtual Array."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/13/2016"
   ms.author="alkohli" />

# Note sulla versione di StorSimple Virtual Array (anteprima) 

## Panoramica

Le note sulla versione seguenti identificano i problemi in sospeso principali della versione in anteprima pubblica di dicembre 2015 di Microsoft Azure StorSimple Virtual Array (noto anche come dispositivo virtuale locale StorSimple o dispositivo virtuale StorSimple).

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire il dispositivo virtuale StorSimple, esaminare attentamente le informazioni contenute nelle note sulla versione.

>[AZURE.IMPORTANT]
>
>- StorSimple Virtual Array è disponibile in anteprima ed è destinato solo a scopi di valutazione e pianificazione della distribuzione. L'installazione di questa versione di anteprima in un ambiente di produzione non è supportata. 
>
>- Se si riscontrano problemi con StorSimple Virtual Array, pubblicare un post al riguardo nel [forum MSDN per StorSimple](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple).

Nella tabella seguente viene fornito un riepilogo dei problemi noti in questa versione.

| No.| Funzionalità | Problema | Soluzione alternativa/commenti |
|----|---------|-------|---------------------|                                                                                                                                                                                                                                                      
| **1.** | Aggiornamenti | È impossibile aggiornare i dispositivi virtuali creati nella versione di anteprima in una versione supportata di disponibilità generale.| Questi dispositivi virtuali devono essere sottoposti a failover per la versione di disponibilità generale tramite un flusso di lavoro di ripristino di emergenza.|
| **2.** | Volumi o condivisioni a livelli | Il blocco dell'intervallo di byte per le applicazioni che operano con i volumi a livelli di StorSimple non è supportato. Se il blocco dell'intervallo di byte è abilitato, la suddivisione in livelli di StorSimple non funziona. | Le misure consigliate includono:<ul><li>Disattivare il blocco dell'intervallo di byte nella logica dell'applicazione.</li><li> Scegliere di inserire i dati per questa applicazione in volumi aggiunti in locale anziché a livelli.</li>*Avvertenza*: se l'uso di volumi aggiunti in locale e il blocco dell'intervallo di byte sono abilitati, tenere presente che il volume aggiunto in locale può essere online anche prima del completamento del ripristino. In questi casi, se è in corso un ripristino, è necessario attenderne il completamento.|
| **3.** | Condivisioni a livelli | Lavorare con file di grandi dimensioni può comportare una suddivisione in livelli lenta. | Quando si lavora con file di grandi dimensioni, assicurarsi che il file più grande sia inferiore al 3% delle dimensioni della condivisione.|
| **4.** | Volumi o condivisioni aggiunti in locale | La capacità utilizzabile di un volume o di una condivisione aggiunti in locale è varia dall'85 al 90% della capacità sottoposta a provisioning. L'intervallo di 10-15% è riservato ai metadati. | È necessario eseguire il provisioning di un volume di dimensioni maggiori per ottenere la capacità utilizzabile desiderata. Ad esempio, per una capacità utilizzabile di 1 TB, è necessario effettuare il provisioning di un volume di 1,15 TB.|
| **5.** | Interfaccia utente Web locale | Se sono abilitate le funzionalità di protezione avanzata di Internet Explorer (IE ESC), alcune pagine dell'interfaccia utente Web locale come **Risoluzione dei problemi** o **Manutenzione** potrebbero non funzionare correttamente. Anche i pulsanti di queste pagine potrebbero non funzionare. | Disattivare le funzionalità di protezione avanzata di Internet Explorer.|
| **6.** | Interfaccia utente Web locale | La localizzazione dell'interfaccia utente Web locale non è supportata per questa versione. | Questo aspetto sarà implementato in una versione successiva.|
| **7.** | Interfaccia utente Web locale | In una macchina virtuale Hyper-V, le interfacce di rete nell'interfaccia utente Web vengono visualizzate come interfacce da 10 Gbps. | Questo comportamento è una reflection di Hyper-V. Hyper-V visualizza sempre 10 Gbps per le schede di rete virtuale.|                                                                                                 
| **8.** | Ripristino di emergenza | È possibile eseguire solo il ripristino di emergenza di un file server nello stesso dominio del dispositivo di origine. Il ripristino di emergenza su un dispositivo di destinazione in un dominio diverso non è supportato in questa versione. | Questo aspetto sarà implementato in una versione successiva.|
| **9.**| Capacità usata per le condivisioni| È possibile riscontrare il consumo di condivisioni in assenza di dati nella condivisione. Questo accade perché la capacità usata per le condivisioni include i metadati. | |                                                                                                                                                                                                                                                         
| **10.** | Impostazioni del fuso orario| Se il fuso orario viene modificato per un dispositivo registrato, la modifica non si riflette nei test diagnostici eseguiti tramite l'interfaccia utente Web locale. | Questo problema verrà risolto in una versione successiva.|
| **11.** | Impostazioni di rete | Quando si configura un indirizzo IP statico per un'interfaccia di rete tramite l'interfaccia utente Web, l'IP viene modificato. Tuttavia, anche l'IP del server DNS viene modificato in un indirizzo locale del sito di IPv6. | Questo problema verrà risolto in una versione successiva.|
| **12.** | Azure PowerShell | I dispositivi virtuali StorSimple non possono essere gestiti tramite Azure PowerShell in questa versione. | La gestione dei dispositivi virtuali deve essere eseguita tramite il portale di Azure classico e l'interfaccia utente Web locale.|
| **13.** | Processi | Lo stato del processo non è granulare. Lo stato del processo può passare direttamente da 0 a 100%. | Questo problema sarà affrontato in una versione successiva.|
| **14.** | Disco dati sottoposto a provisioning | Dopo il provisioning di un disco dati di una determinata dimensione specificata e la creazione di un dispositivo virtuale StorSimple corrispondente, non si deve espandere o compattare il disco dati. Il tentativo di eseguire questa operazione può comportare una perdita di tutti i dati nei livelli locali del dispositivo. | |
| **15.** | Pannello della Guida | Gli argomenti del pannello della Guida non saranno disponibili.| Tutta la documentazione è disponibile tramite l'Area download Microsoft e il sito di documentazione di Azure. |   

<!---HONumber=AcomDC_0121_2016-->