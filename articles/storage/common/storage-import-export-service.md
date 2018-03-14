---
title: Uso di Importazione/Esportazione di Azure per trasferire i dati da e verso Archiviazione di Azure | Documentazione Microsoft
description: Informazioni su come creare processi di importazione ed esportazione nel portale di Azure per trasferire dati da e verso Archiviazione di Azure.
author: muralikk
manager: syadav
services: storage
ms.service: storage
ms.topic: article
ms.date: 02/28/2018
ms.author: muralikk
ms.openlocfilehash: e9fce2530bc4e654304b946cea1715ac8e2ce6fa
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-azure-storage"></a>Usare il servizio Importazione/Esportazione di Microsoft Azure per trasferire i dati in Archiviazione di Azure
Questo articolo fornisce istruzioni dettagliate sull'uso del servizio Importazione/Esportazione di Azure per trasferire in modo sicuro grandi quantità di dati in Archiviazione di Azure e in File di Azure tramite la spedizione delle unità disco a un data center di Azure. È anche possibile usare questo servizio per trasferire i dati da Archiviazione di Azure a unità disco rigido per la spedizione al sito locale. È possibile importare i dati da un'unica unità disco SATA interna ad Archiviazione BLOB di Azure o a File di Azure. 

> [!IMPORTANT] 
> Questo servizio accetta solo unità HDD o SSD SATA interne. Non sono supportati altri dispositivi. Non inviare unità HDD esterne, dispositivi NAS e così via perché verranno restituiti, se possibile, o ignorati.
>
>

Seguire la procedura seguente se è necessario importare i dati sul disco in Archiviazione di Azure.
### <a name="step-1-prepare-the-drives-using-waimportexport-tool-and-generate-journal-files"></a>Passaggio 1: preparare l'unità usando lo strumento WAImportExport e generare file journal.

1.  Identificare i dati da importare in Archiviazione di Azure. È possibile importare directory e file autonomi in un server locale o in una condivisione di rete.
2.  In base alle dimensioni totali dei dati, procurare il numero necessario di unità SSD da 2,5 pollici o dischi rigidi SATA II/III da 2,5 o 3,5 pollici.
3.  Collegare i dischi rigidi a un computer Windows usando direttamente SATA o gli adattatori USB esterni.
1.  Creare un singolo volume NTFS in ogni disco rigido e assegnare una lettera di unità al volume. Nessun punto di montaggio.
2.  Per abilitare la crittografia nel computer Windows, è necessario abilitare la crittografia BitLocker sul volume NTFS. Per eseguire questa operazione, seguire le istruzioni riportate nell'articolo all'indirizzo https://technet.microsoft.com/en-us/library/cc731549(v=ws.10).aspx.
3.  Copiare tutti i dati nei volumi NTFS crittografati sui dischi usando le funzionalità di copia e incolla o di trascinamento, oppure usare uno strumento come Robocopy.
7.  Scaricare WAImportExport V1 da https://www.microsoft.com/en-us/download/details.aspx?id=42659
8.  Decomprimere il file nella cartella waimportexportv1 predefinita. Ad esempio, C:\WaImportExportV1  
9.  Eseguire come amministratore, aprire PowerShell o la riga di comando e modificare la directory con la cartella decompressa. Ad esempio, cd C:\WaImportExportV1
10. Copiare la riga di comando seguente in un editor di testo e modificarla per creare una riga di comando:

    ```
    ./WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:***== /t:D /bk:*** /srcdir:D:\ /dstdir:ContainerName/ 
    ```
    
    Queste opzioni della riga di comando sono descritte nella tabella seguente:

    |Opzione  |DESCRIZIONE  |
    |---------|---------|
    |/j:     |Nome del file journal, con estensione jrn. Viene generato un file journal per ogni unità. È consigliabile usare il numero di serie del disco come nome del file journal.         |
    |/sk:     |Chiave dell'account di Archiviazione di Azure.         |
    |/t:     |Lettera di unità del disco da spedire. Ad esempio, l'unità `D`.         |
    |/bk:     |Chiave di BitLocker per l'unità.         |
    |/srcdir:     |Lettera di unità del disco da spedire seguita da `:\`. Ad esempio, `D:\`.         |
    |/dstdir:     |Nome del contenitore di destinazione in Archiviazione di Azure         |

1. Ripetere il passaggio 10 per tutti i dischi da spedire.
2. Ogni volta che viene eseguita la riga di comando, viene creato un file journal con il nome fornito con il parametro /j:.

### <a name="step-2-create-an-import-job-on-azure-portal"></a>Passaggio 2: creare un processo di importazione nel portale di Azure.

1. Accedere a https://portal.azure.com/. In Altri servizi -> ARCHIVIAZIONE -> "Processi di importazione/esportazione" fare clic su **Crea Processi di importazione/esportazione**.

2. Nella sezione Informazioni di base selezionare "Importa in Azure", immettere una stringa per il nome del processo, selezionare una sottoscrizione e immettere o selezionare un gruppo di risorse. Immettere un nome descrittivo per il processo di importazione. Il nome immesso può contenere solo lettere minuscole, numeri, trattini e caratteri di sottolineatura, deve iniziare con una lettera e non può contenere spazi. Il nome scelto viene usato per tenere traccia dei processi mentre sono in corso e dopo che sono stati completati.

3. Nella sezione Dettagli processo caricare i file journal dell'unità ottenuti durante il passaggio di preparazione dell'unità. Se è stato usato waimportexport.exe versione 1, è necessario caricare un file per ogni unità preparata. Selezionare l'account di archiviazione in cui verranno importati i dati nella sezione Account di archiviazione di "Destinazione importazione". La località di consegna viene popolata automaticamente in base all'area geografica dell'account di archiviazione selezionato.
   
   ![Creare il processo di importazione - Passaggio 3](./media/storage-import-export-service/import-job-03.png)
4. Nella sezione Informazioni sul mittente della spedizione selezionare il vettore dall'elenco a discesa e immettere un numero di account valido creato con tale vettore. Microsoft usa questo account per restituire le unità al cliente al termine del processo di importazione. Specificare un nome di contatto completo e valido, oltre a numero di telefono, e-mail, indirizzo, città, CAP, stato/provincia e paese/area geografica.
   
5. Nella sezione di riepilogo viene fornito l'indirizzo di spedizione del data center di Azure da usare per la spedizione dei dischi al data center di Azure. Verificare che il nome del processo e l'indirizzo completo siano riportati sull'etichetta per la spedizione. 

6. Fare clic su OK nella pagina di riepilogo per completare la creazione del processo di importazione.

### <a name="step-3-ship-the-drives-to-the-azure-datacenter-shipping-address-provided-in-step-2"></a>Passaggio 3: spedire le unità all'indirizzo di spedizione del data center di Azure fornito nel passaggio 2.
Per spedire il pacco al data center di Azure è possibile usare FedEx, UPS o DHL.

### <a name="step-4-update-the-job-created-in-step2-with-tracking-number-of-the-shipment"></a>Passaggio 4: aggiornare il processo creato nel passaggio 2 con il numero di tracciabilità della spedizione.
Dopo la spedizione dei dischi, tornare alla pagina **Importazione/Esportazione** nel portale di Azure per aggiornare il numero di tracciabilità usando i passaggi seguenti, a) Selezionare e fare clic sul processo di importazione b) Fare clic su **Aggiorna lo stato del processo e le informazioni di tracciabilità dopo la spedizione delle unità**. c) Selezionare casella di controllo "Contrassegna come spedito" d) Specificare il vettore e il numero di tracciabilità.
Se il numero di tracciabilità non viene aggiornato entro due settimane dalla creazione del processo, il processo scadrà. È possibile monitorare l'avanzamento del processo nel dashboard del portale. Vedere il significato di ogni stato del processo nella sezione [Visualizzazione dello stato dei processi](#viewing-your-job-status)precedente.

## <a name="when-should-i-use-the-azure-importexport-service"></a>Quando usare il servizio Importazione/Esportazione di Azure
È consigliabile usare il servizio Importazione/Esportazione di Azure quando l'upload o il download dei dati attraverso la rete è troppo lento o l'acquisizione di maggiore larghezza di banda di rete comporta costi proibitivi.

Il servizio può essere usato in scenari simili ai seguenti:

* Migrazione di dati al cloud: spostamento di grandi quantità di dati in Azure in modo veloce e a costi contenuti.
* Distribuzione del contenuto: invio rapido di dati ai siti dei clienti.
* Backup: esecuzione di backup dei dati locali da memorizzare in Archiviazione di Azure.
* Ripristino di dati: ripristino di una grande quantità di dati memorizzati nell'archivio perché vengano recapitati al percorso locale.

## <a name="prerequisites"></a>prerequisiti
In questa sezione sono elencati i prerequisiti per l'uso di questo servizio. Leggerli attentamente prima di spedire le unità.

### <a name="storage-account"></a>Account di archiviazione
Per usare il servizio Importazione/Esportazione di Azure, sono necessari una sottoscrizione di Azure esistente e uno o più account di archiviazione . Importazione/esportazione di Azure supporta solo gli account classico, di archiviazione BLOB e gli account di archiviazione di utilizzo generico v1. Ogni processo può essere usato per trasferire dati da o verso un solo account di archiviazione. In altre parole, un singolo processo di importazione/esportazione non può estendersi su più account di archiviazione. Per informazioni sulla creazione di un nuovo account di archiviazione, vedere [Come creare un account di archiviazione](storage-create-storage-account.md#create-a-storage-account).

> [!IMPORTANT] 
> Il servizio Importazione/Esportazione di Azure non supporta gli account di archiviazione in cui la funzionalità degli [endpoint del servizio Rete virtuale](../../virtual-network/virtual-network-service-endpoints-overview.md) è stata abilitata. 
> 
> 

### <a name="data-types"></a>Tipi di dati
È possibile usare il servizio Importazione/Esportazione di Azure per copiare dati in BLOB in **blocchi**, in BLOB di **pagine** o in **file**. Al contrario, usando questo servizio è possibile esportare solo BLOB in **blocchi**, BLOB di **pagine** o BLOB di **aggiunta** da Archiviazione di Azure. Il servizio supporta solo l'importazione di dati di File di Azure in Archiviazione di Azure. L'esportazione di dati di File di Azure non è attualmente supportato.

### <a name="job"></a>Processo
Per avviare la procedura di importazione o di esportazione, creare innanzitutto un processo, che potrà essere un processo di importazione o un processo di esportazione:

* Creare un processo di importazione quando si desidera trasferire i dati locali nell'account di archiviazione di Azure.
* Creare un processo di esportazione quando si vogliono trasferire su dischi rigidi da spedire a Microsoft i dati attualmente archiviati nell'account di archiviazione. Quando si crea un processo, si notifica al servizio Importazione/Esportazione che si spedirà uno o più dischi rigidi a un data center di Azure.

* Per un processo di importazione, si spediranno dischi rigidi contenenti i dati.
* Per un processo di esportazione, si spediranno dischi rigidi vuoti.
* È possibile spedire fino a 10 unità disco rigido per ogni processo.

Per creare un processo di importazione o di esportazione è possibile usare il portale di Azure o l'[API REST del servizio Importazione/Esportazione di Archiviazione di Azure](/rest/api/storageimportexport).

> [!Note]
> Le API RDFE non saranno più supportate a partire dal 28 febbraio 2018. Per continuare a usare il servizio, eseguire la migrazione alle [API REST di importazione/esportazione ARM](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/storageimportexport/resource-manager/Microsoft.ImportExport/stable/2016-11-01/storageimportexport.json). 

### <a name="waimportexport-tool"></a>Strumento WAImportExport
Il primo passaggio nella creazione di un processo di **importazione** consiste nel preparare le unità che verranno spedita per l'importazione. Per preparare le unità, è necessario connetterle a un server locale ed eseguire lo strumento WAImportExport nel server locale. Questo strumento facilita la copia dei dati sull'unità, crittografando i dati sull'unità con BitLocker e generando i file journal dell'unità.

I file journal contengono le informazioni di base sul processo e sull'unità, come il numero di serie dell'unità e il nome dell'account di archiviazione. Questo file journal non è archiviato nell'unità. Viene usato durante la creazione del processo di importazione. La procedura dettagliata sulla creazione dei processi è riportata più avanti in questo articolo.

Lo strumento WAImportExport è compatibile solo con il sistema operativo Windows a 64 bit. Vedere la sezione [Sistema operativo](#operating-system) per le versioni specifiche del sistema operativo supportate.

Scaricare la versione più recente dello [strumento WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip). Per ulteriori dettagli sull'uso dello strumento WAImportExport, vedere [Uso dello strumento WAImportExport](storage-import-export-tool-how-to.md).

>[!NOTE]
>**Versione precedente:** è possibile [scaricare la versione WAImportExport V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) dello strumento e fare riferimento alla [guida all'uso di WAImportExport V1](storage-import-export-tool-how-to-v1.md). La versione WAImportExport V1 dello strumento offre supporto per la **preparazione di dischi quando i dati sono già scritti in precedenza sul disco**. Se l'unica chiave disponibile è SAS-Key, è necessario usare lo strumento WAImportExport V1.

>

### <a name="hard-disk-drives"></a>Unità disco rigido
Con il servizio Importazione/Esportazione sono supportati solo le unità disco rigido da 2,5 pollici o i dischi rigidi interni SATA II/III da 2,5 o 3,5 pollici. Un singolo processo di importazione/esportazione può interessare un massimo di 10 unità disco rigido o SSD e ogni unità disco rigido o SSD può avere qualsiasi dimensione. Un numero elevato di unità può essere distribuito tra più processi e non esistono limiti al numero di processi che è possibile creare. 

Per i processi di importazione, viene elaborato solo il primo volume di dati sull'unità. Il volume di dati deve essere formattato con NTFS.

> [!IMPORTANT]
> Questo servizio non supporta i dischi rigidi esterni dotati di un adattatore USB incorporato. Non possono essere usati o inviati neanche dischi inseriti nell'involucro di un'unità disco rigido esterna.
> 
> 

Di seguito è riportato un elenco di adattatori USB esterni usati per copiare i dati in dischi rigidi interni. Anker 68UPSATAA - 02BU Anker 68UPSHHDS BU Startech SATADOCK22UE Orico 6628SUS3-C-BK (serie 6628) Alloggiamento di espansione dell'unità rigida esterna Thermaltake BlacX Hot Swap SATA (USB 2.0 ed eSATA)

### <a name="encryption"></a>Crittografia
I dati sull'unità devono essere crittografati mediante Crittografia unità BitLocker, che protegge i dati mentre sono in transito.

Per i processi di importazione, la crittografia può essere eseguita in due modi. Il primo modo consiste nello specificare l'opzione tramite file CSV durante l'esecuzione dello strumento WAImportExport nella preparazione dell'unità. Il secondo consiste invece nell'abilitare manualmente la crittografia BitLocker nell'unità e specificare la chiave di crittografia nel file CSV del driveset durante l'esecuzione della riga di comando dello strumento WAImportExport nella preparazione dell'unità.

Per i processi di esportazione, dopo la copia dei dati nelle unità, il servizio applica la crittografia BitLocker all'unità prima di rispedirla all'utente. La chiave di crittografia viene indicata all'utente tramite il portale di Azure.  

### <a name="operating-system"></a>Sistema operativo
Per preparare il disco rigido con lo strumento WAImportExport prima della spedizione dell'unità ad Azure, è possibile usare uno dei sistemi operativi a 64 bit seguenti:

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Tutti questi sistemi operativi supportano la funzionalità Crittografia unità BitLocker.

### <a name="locations"></a>Località
Il servizio Importazione/Esportazione di Azure supporta la copia dei dati da e verso tutti gli account di archiviazione di Azure pubblici. È possibile spedire le unità disco rigido a una delle località seguenti. Se l'account di archiviazione si trova in una località di Azure pubblica non specificata qui, quando si crea il processo con il portale di Azure o tramite l'API REST del servizio Importazione/Esportazione verrà indicata una località di spedizione alternativa.

Località di spedizione supportate:

* Stati Uniti orientali
* Stati Uniti occidentali
* Stati Uniti orientali 2
* Stati Uniti occidentali 2
* Stati Uniti centrali
* Stati Uniti centro-settentrionali
* Stati Uniti centro-meridionali
* Stati Uniti centro-occidentali
* Europa settentrionale
* Europa occidentale
* Asia orientale
* Asia sudorientale
* Australia orientale
* Australia sudorientale
* Giappone occidentale
* Giappone orientale
* India centrale
* India meridionale
* India occidentale
* Canada centrale
* Canada orientale
* Brasile meridionale
* Corea centrale
* US Gov Virginia
* Governo degli Stati Uniti - Iowa
* Dipartimento della difesa Stati Uniti orientali
* Dipartimento della difesa Stati Uniti centrali
* Cina orientale
* Cina settentrionale
* Regno Unito meridionale
* Germania centrale
* Germania nord-orientale

### <a name="shipping"></a>Spedizione
**Spedizione di unità al data center:**

Quando si crea un processo di importazione o esportazione, viene fornito un indirizzo di spedizione di una delle località supportate per spedire le unità. L'indirizzo di spedizione fornito dipende dalla località in cui si trova l'account di archiviazione, ma potrebbe non corrispondere a tale località.

Per spedire le unità all'indirizzo di spedizione è possibile usare FedEx, UPS o DHL.

**Spedizione di unità dal data center:**

Quando si crea un processo di importazione o esportazione, è necessario fornire a Microsoft un indirizzo mittente a cui rispedire le unità al termine del processo. Assicurarsi di fornire un indirizzo di restituzione valido per evitare ritardi nell'elaborazione.

Il gestore deve disporre di strumenti di tracciabilità appropriati al fine di mantenere la catena di custodia. È necessario fornire un numero di account del vettore FedEx, UPS o DHL valido che Microsoft possa usare per restituire le unità. Per la restituzione di unità da località negli Stati Uniti e in Europa è necessario un numero di account FedEx, UPS o DHL. Per la restituzione di unità da località in Asia e in Australia è necessario un numero di account DHL. Se non è disponibile, è possibile creare un account [FedEx](http://www.fedex.com/us/oadr/), per Stati Uniti ed Europa, o [DHL](http://www.dhl.com/), per Asia e Australia. Se si dispone già di un numero di account vettore, verificare che sia valido.

Durante la spedizione dei pacchetti, seguire le condizioni di [Condizioni per l’Uso dei Servizi di Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [!IMPORTANT]
> È possibile che i supporti fisici spediti debbano superare i confini internazionali. È responsabilità del cliente assicurarsi che i supporti fisici e i dati siano importati e/o esportati conformemente alle leggi vigenti. Prima di spedire i supporti fisici, rivolgersi ai consulenti per verificare che i supporti e i dati possano essere spediti ai data center specificati in modo lecito. In questo modo si ha la sicurezza che la spedizione raggiunga Microsoft in modo tempestivo. Ad esempio, i pacchetti che superano i confini internazionali necessitano di fattura commerciale da allegare al pacchetto (tranne i confini all'interno dell'Unione Europea). È possibile stampare una copia della fattura commerciale compilata dal sito Web del vettore. Esempi di fattura commerciale sono [fattura commerciale DHL](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) e [fattura commerciale FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Assicurarsi che Microsoft non sia stato indicato come l'esportatore.
> 
> 

## <a name="how-does-the-azure-importexport-service-work"></a>Come funziona il servizio Importazione/Esportazione di Azure
È possibile trasferire dati tra il sito locale e Archiviazione di Azure con il servizio Importazione/Esportazione di Azure attraverso la creazione di processi e la spedizione di unità disco rigido a un data center di Azure. Ogni unità disco rigido spedita è associata a un singolo processo. Ogni processo è associato a un singolo account di archiviazione. Per informazioni sulle specifiche di questo servizio, come i tipi di dati e i tipi di disco supportati, le località e la spedizione, leggere attentamente la sezione [Prerequisiti](#pre-requisites) .

In questa sezione viene presentata una descrizione generale dei passaggi necessari nei processi di importazione ed esportazione. La [sezione introduttiva](#quick-start) riportata più avanti include istruzioni dettagliate per creare un processo di importazione ed esportazione.

### <a name="inside-an-import-job"></a>Analisi di un processo di importazione
In generale, un processo di importazione prevede i passaggi seguenti:

* Determinare i dati da importare e il numero di unità necessarie.
* Identificare il BLOB di destinazione o la posizione dei file per i dati nell'archiviazione di Azure.
* Usare lo strumento WAImportExport per copiare i dati in una o più unità disco rigido e crittografarli con BitLocker.
* Creare un processo di importazione nell'account di archiviazione di destinazione usando il portale di Azure o l'API REST del servizio Importazione/Esportazione. Se si usa il portale di Azure, caricare i file journal dell'unità.
* Specificare l'indirizzo mittente e il numero di account del vettore da usare per la restituzione.
* Spedire le unità disco rigido all'indirizzo di spedizione fornito durante la creazione del processo.
* Aggiornare il numero di tracciabilità della consegna nei dettagli del processo di importazione e inviare il processo di importazione.
* Le unità vengono ricevute ed elaborate nel data center di Azure.
* Le unità vengono restituite usando l'account del vettore all'indirizzo mittente specificato nel processo di importazione.
  
    ![Figura 1: importazione del flusso di processo](./media/storage-import-export-service/importjob.png)

### <a name="inside-an-export-job"></a>Analisi di un processo di esportazione
> [!IMPORTANT]
> Il servizio supporta solo l'esportazione dei BLOB di Azure e non supporta l'esportazione di File di Azure.
> 
>

In generale, un processo di esortazione prevede i passaggi seguenti:

* Determinare i dati da esportare e il numero di unità necessarie.
* Identificare i BLOB di origine o i percorsi dei contenitori dei dati nell'archiviazione BLOB.
* Creare un processo di esportazione nell'account di archiviazione di origine usando il portale di Azure o l'API REST del servizio Importazione/Esportazione.
* Specificare i BLOB di origine o i percorsi dei contenitori dei dati nel processo di esportazione.
* Specificare l'indirizzo mittente e il numero di account del vettore da usare per la restituzione.
* Spedire le unità disco rigido all'indirizzo di spedizione fornito durante la creazione del processo.
* Aggiornare il numero di tracciabilità della consegna nei dettagli del processo di esportazione e inviare il processo di esportazione.
* Le unità vengono ricevute ed elaborate presso il data center di Azure.
* Le unità vengono crittografate con BitLocker e le chiavi sono disponibili tramite il portale di Azure.  
* Le unità vengono restituite usando l'account del vettore all'indirizzo mittente specificato nel processo di importazione.
  
    ![Figura 2: esportazione del flusso di processo](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-and-drive-status"></a>Visualizzazione dello stato dei processi e delle unità
È possibile tenere traccia dello stato dei processo di importazione o esportazione dal portale di Azure. Fare clic sulla scheda **Importazione/Esportazione**. Nella pagina viene visualizzato un elenco dei processi.

![Visualizzare lo stato dei processi](./media/storage-import-export-service/jobstate.png)

Viene visualizzato uno degli stati di processo seguenti, in base al punto in cui si trova l'unità nel processo.

| Stato processo | DESCRIZIONE |
|:--- |:--- |
| Creating | Dopo aver creato un processo, lo stato è impostato su Creazione. Mentre il processo si trova nello stato Creazione, il servizio Importazione/Esportazione presuppone che le unità non siano state spedite al data center. Un processo può rimanere nello stato Creazione fino a due settimane; al termine di questo periodo viene automaticamente eliminato dal servizio. |
| Spedizione | Dopo aver spedito il pacchetto, è consigliabile aggiornare le informazioni di tracciabilità nel portale di Azure.  Il processo passerà allo stato "Spedizione". Il processo rimarrà in tale stato per un massimo di due settimane. 
| Ricevuto | Dopo che tutte le unità sono state ricevute nel data center, il processo verrà impostato sullo stato Ricevuto. |
| Transferring | Dopo l'avvio dell'elaborazione per almeno un'unità, il processo passerà allo stato Trasferimento. Vedere la sezione Stato delle unità di seguito per informazioni dettagliate. |
| Packaging | Una volta completata l'elaborazione di tutte le unità, il processo verrà inserito nello stato Finalizzazione fino a quando le unità non vengono spedite di nuovo all'utente. |
| Completed | Dopo che tutte le unità sono state rispedite al cliente, il processo verrà impostato sullo stato Completato se non si sono verificati errori. Il processo verrà eliminato automaticamente dopo 90 giorni in cui si trova nello stato Completato. |
| Chiuso | Dopo che tutte le unità sono state rispedite al cliente, se si sono verificati errori durante l'elaborazione del processo, il processo verrà impostato sullo stato Chiuso. Il processo verrà eliminato automaticamente dopo 90 giorni in cui si trova nello stato Chiuso. |

La tabella di seguito descrive il ciclo di vita di una singola unità attraverso un processo di importazione o esportazione. Lo stato attuale di ogni unità in un processo è ora visibile dal portale di Azure.
La tabella seguente descrive ogni stato in cui può trovarsi un'unità in un processo.

| Stato dell'unità | DESCRIZIONE |
|:--- |:--- |
| Specificata | Per un processo di importazione, quando il processo viene creato dal portale di Azure, lo stato iniziale di un'unità è Specificata. Per un processo di esportazione, lo stato iniziale dell'unità è Ricevuta perché non è stata specificata un'unità in fase di creazione del processo. |
| Ricevuto | Le unità passano allo stato Ricevuta quando l'operatore del servizio Importazione/Esportazione elabora le unità ricevute dallo spedizioniere per un processo di importazione. Per un processo di esportazione, lo stato iniziale dell'unità è Ricevuta. |
| MaiRicevuta | L'unità passa allo stato MaiRicevuta se il pacchetto di un processo viene ricevuto ma senza che contenga l'unità. Un'unità può inoltre passare a questo stato se sono trascorse due settimane da quando il servizio ha ricevuto le informazioni sulla spedizione, ma il pacchetto non è ancora stato ricevuto nel data center. |
| Transferring | Un'unità passa allo stato Trasferimento quando il servizio inizia a trasferire i dati dall'unità in Archiviazione di Microsoft Azure. |
| Completed | Un'unità passa allo stato Completata se il servizio trasferisce correttamente tutti i dati senza che si verifichino errori.
| CompletataPiùInformazioni | Un'unità passa allo stato CompletataPiùInformazioni se il servizio ha riscontrato dei problemi durante la copia dei dati da o verso l'unità. Le informazioni possono includere errori, avvisi o messaggi informativi sulla sovrascrittura dei BLOB.
| Rispedita | L'unità passa allo stato Rispedita quando viene spedita dal data center all'indirizzo di restituzione. |

Questa immagine dal portale di Azure consente di visualizzare lo stato dell'unità di un processo di esempio:

![Visualizza stato dell'unità](./media/storage-import-export-service/drivestate.png)

La tabella seguente descrive gli stati di errore delle unità e le azioni intraprese corrispondenti.

| Stato dell'unità | Event | Risoluzione/Passaggio successivo |
|:--- |:--- |:--- |
| MaiRicevuta | Un'unità contrassegnata come MaiRicevuta (perché non è stata ricevuta come parte della spedizione del processo) viene ricevuta con un'altra spedizione. | Il team operativo sposterà l'unità nello stato Ricevuta. |
| N/D | Un'unità che non fa parte di alcun processo arriva al data center come parte di un altro processo. | L'unità verrà contrassegnata come unità aggiuntiva e verrà restituita al cliente una volta completato il processo associato al pacchetto originale. |

### <a name="time-to-process-job"></a>Tempo di elaborazione del processo
Il tempo impiegato per elaborare un processo di importazione/esportazione varia in base a diversi fattori, ad esempio i tempi di spedizione, il tipo di processo, il tipo e la dimensione dei dati da copiare e la dimensione dei dischi forniti. Il servizio di importazione/esportazione non dispone di un contratto di servizio, ma dopo la ricezione dei dischi il servizio tenta di completare la copia in un intervallo compreso tra i 7 e i 10 giorni. È possibile usare l'API REST per tenere traccia più da vicino dell'avanzamento del processo. È disponibile un parametro indicante la percentuale di completamento nell'operazione Elencare i processi che fornisce un'indicazione dell'avanzamento della copia. In caso di processi di importazione/esportazione che richiedono una precisa tempistica, invitiamo gli utenti a contattarci per avere una stima dei tempi di completamento.

### <a name="pricing"></a>Prezzi
**Tariffa di gestione delle unità**

Esiste una tariffa di gestione per ogni unità elaborata come parte del processo di importazione o esportazione. Vedere i dettagli in [Prezzi di Importazione/Esportazione](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Costi di spedizione**

Quando si spediscono unità ad Azure, si paga il costo di spedizione al vettore. Il costo della spedizione delle unità restituite da Microsoft all'utente viene addebitato sull'account del vettore specificato al momento della creazione del processo.

**Costi di transazione**

Non vi sono costi per le transazioni oltre a quelli della transazione di archiviazione standard quando si importano dati in archiviazione di Azure. Sono applicabili spese di uscita standard quando si esportano dati dall'archiviazione BLOB. Per altre informazioni sui costi della transazione, vedere [Dettagli prezzi dei trasferimenti di dati.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="how-to-import-data-into-azure-file-storage-using-internal-sata-hdds-and-ssds"></a>Come importare i dati in Archiviazione file di Azure usando le unità HDD o SSD SATA interne?
Seguire la procedura seguente se è necessario importare i dati sul disco in un'Archiviazione file di Azure.
Il primo passaggio nell'importazione di dati tramite il servizio Importazione/Esportazione di Azure consiste nel preparare le unità usando lo strumento WAImportExport. Per preparare le unità, seguire questa procedura.

1. Identificare i dati da importare nell'Archiviazione file di Azure. Potrebbe trattarsi di directory e file autonomi nel server locale o in una condivisione di rete.  
2. Determinare il numero di unità che serviranno in base alla dimensione totale dei dati. Procurare il numero necessario di unità SSD da 2,5 pollici o dischi rigidi SATA II/III da 2,5 o 3,5 pollici.
4. Determinare le directory e/o i file autonomi che verranno copiati in ciascuna unità disco rigido.
5. Creare i file CSV per set di dati e driveset.
    
  Di seguito è riportato un esempio di file CSV del set di dati per l'importazione di dati come File di Azure:
  
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","fileshare/100M_1.csv.txt",file,rename,"None",None
    "F:\50M_original\","fileshare/",file,rename,"None",None 
    ```
   Nell'esempio precedente, il file 100M_1.csv.txt verrà copiato nella radice della condivisione file. Se la condivisione file non esiste, verrà creata. Tutti i file e le cartelle in 50M_original verranno copiati nella condivisione file in modo ricorsivo. La struttura delle cartelle verrà mantenuta.

    Ulteriori informazioni su [come preparare un file CSV del set di dati](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    


    **File CSV del driveset**

    Il valore del flag driveset è un file CSV che contiene l'elenco dei dischi a cui viene eseguito il mapping delle lettere di unità, in modo che lo strumento possa scegliere correttamente l'elenco dei dischi da preparare. 

    Di seguito è riportato l'esempio di un file CSV del driveset:
    
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    H,Format,SilentMode,Encrypt,
    ```

    Nell'esempio precedente, si presuppone che siano associati due dischi e che siano stati creati volumi NTFS di base con lettere G:\ e H:\. Lo strumento formatterà e crittograferà il disco con il volume H:\, ma non quello con il volume G:\.

    Ulteriori informazioni su [come preparare un file CSV del driveset](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file).

6.  Usare lo [strumento WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) per copiare i dati in uno o più dischi rigidi.
7.  È possibile specificare il parametro "Encrypt" nel campo di crittografia del CSV del driveset per abilitare la crittografia BitLocker sull'unità disco rigido. In alternativa è possibile anche abilitare la crittografia BitLocker manualmente nell'unità disco rigido, specificare "AlreadyEncrypted" e indicare la chiave nel CSV del driveset durante l'esecuzione dello strumento.

8. Non modificare i dati sulle unità disco rigido o sul file journal dopo aver completato la preparazione del disco.

> [!IMPORTANT]
> Ogni unità disco rigido preparata darà luogo a un file journal. Quando si crea il processo di importazione usando il portale di Azure, è necessario caricare tutti i file journal delle unità che fanno parte del processo di importazione. Le unità senza i file journal non saranno elaborate.
> 
>

Di seguito sono riportati i comandi e gli esempi per preparare l'unità disco rigido usando lo strumento WAImportExport.

Comando PrepImport dello strumento WAImportExport per la prima sessione di copia per copiare directory e/o file con una nuova sessione di copia:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Esempio 1 di importazione**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Per **aggiungere ulteriori unità**, è possibile creare un nuovo file del driveset ed eseguire il comando come indicato di seguito. Per le sessioni di copia successive in unità disco diverse rispetto a quelle specificate nel file .csv InitialDriveset, specificare un nuovo file CSV del driveset e indicarlo come valore nel parametro "AdditionalDriveSet". Usare il **medesimo file journal** e indicare un **nuovo ID di sessione**. Il formato del file CSV AdditionalDriveset corrisponde a quello del file InitialDriveSet.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

**Esempio 2 di importazione**
```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```

Per aggiungere ulteriori dati allo stesso driveset, è possibile chiamare il comando PrepImport dello strumento WAImportExport per sessioni di copia successive, al fine di copiare file o directory aggiuntivi: per le sessioni di copia successive sulle medesime unità disco rigido specificate nel file InitialDriveset.csv, specificare il nome del **medesimo file journal** e indicare un **nuovo ID di sessione**. Non è necessario indicare una chiave dell'account di archiviazione.

```
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

**Esempio 3 di importazione**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Per altre informazioni sull'uso dello strumento WAImportExport, vedere [Preparazione dei dischi rigidi per un processo di importazione](storage-import-export-tool-preparing-hard-drives-import.md).

Per istruzioni più dettagliate, vedere anche [Flusso di lavoro campione per preparare i dischi rigidi per un processo di importazione](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md).  



## <a name="create-an-export-job"></a>Creare un processo di esportazione
Creare un processo di esportazione per notificare al servizio Importazione/Esportazione che una o più unità vuote verranno spedite al data center. In questo modo i dati potranno essere esportati dall'account di archiviazione alle unità e le unità potranno essere spedite all'utente.

### <a name="prepare-your-drives"></a>Preparare le unità
Per preparare le unità per un processo di esportazione si consiglia di eseguire i controlli preliminari seguenti:

1. Verificare il numero di dischi richiesti usando il comando PreviewExport dello strumento WAImportExport. Per altre informazioni, vedere l'articolo sull' [anteprima dell'uso del disco per un processo di esportazione](https://msdn.microsoft.com/library/azure/dn722414.aspx). Consente di visualizzare l'anteprima dell'utilizzo di unità per il Blob è selezionato, in base alla dimensione delle unità che si desidera utilizzare.
2. Controllare che sia possibile leggere/scrivere sul disco rigido che verrà inviato per il processo di esportazione.

### <a name="create-the-export-job"></a>Creare il processo di esportazione
1. Per creare un processo di esportazione, passare ad Altri servizi -> ARCHIVIAZIONE -> "Processi di importazione/esportazione" nel portale di Azure. Fare clic su **Crea Processi di importazione/esportazione**.
2. Nel passaggio 1 Informazioni di base selezionare "Esporta da Azure", immettere una stringa per il nome del processo, selezionare una sottoscrizione e immettere o selezionare un gruppo di risorse. Immettere un nome descrittivo per il processo di importazione. Il nome immesso può contenere solo lettere minuscole, numeri, trattini e caratteri di sottolineatura, deve iniziare con una lettera e non può contenere spazi. Il nome scelto sarà usato per tenere traccia dei processi mentre sono in corso e dopo che sono stati completati. Fornire le informazioni di contatto per la persona responsabile di questo processo di esportazione. 

3. Nel passaggio 2 Dettagli processo selezionare l'account di archiviazione da cui verranno esportati i dati nella sezione Account di archiviazione. La località di consegna verrà popolata automaticamente in base all'area geografica dell'account di archiviazione selezionato. Specificare i dati BLOB da esportare dall'account di archiviazione a una o più unità vuote. È possibile scegliere di esportare tutti i dati Blob nell'account di archiviazione o specificare singoli Blob o set di Blob.
   
   Per specificare un BLOB da esportare, utilizzare il selettore **Equal To** e specificare il percorso relativo del BLOB, iniziando con il nome del contenitore. Utilizzare *$root* per specificare il contenitore radice.
   
   Per specificare tutti i BLOB che iniziano con un prefisso, utilizzare il selettore **Starts With** e specificare il prefisso, iniziando con una barra "/". Il prefisso può essere il prefisso del nome del contenitore, il nome del contenitore completo o il nome del contenitore completo seguito dal prefisso del nome BLOB.
   
   La tabella seguente mostra alcuni esempi di percorsi BLOB validi:
   
   | Selettore | Percorso BLOB | DESCRIZIONE |
   | --- | --- | --- |
   | Starts With |/ |Esporta tutti i BLOB nell'account di archiviazione |
   | Starts With |/$root/ |Esporta tutti i BLOB nel contenitore radice |
   | Starts With |/book |Esporta tutti i BLOB in qualsiasi contenitore che inizia con il prefisso **book** |
   | Starts With |/music/ |Esporta tutti i BLOB nel contenitore **music** |
   | Starts With |/music/love |Esporta nel contenitore **music** tutti i BLOB che iniziano con il prefisso **love** |
   | Equal To |$root/logo.bmp |Esporta il BLOB **logo.bmp** nel contenitore radice |
   | Equal To |videos/story.mp4 |Esporta il BLOB **story.mp4** nel contenitore **video** |
   
   I percorsi BLOB devono essere specificati in formati validi per evitare errori durante l'elaborazione, come mostra lo screenshot seguente.
   
   ![Creare il processo di esportazione - Passaggio 3](./media/storage-import-export-service/export-job-03.png)

4. Nel passaggio 3 Informazioni sul mittente della spedizione selezionare il vettore dall'elenco a discesa e immettere un numero di account valido creato con tale vettore. Microsoft userà questo account per restituire le unità al cliente al termine del processo di importazione. Specificare un nome di contatto completo e valido, oltre a numero di telefono, e-mail, indirizzo, città, CAP, stato/provincia e paese/area geografica.
   
 5. Nella pagina di riepilogo viene fornito l'indirizzo di spedizione del data center di Azure da usare per la spedizione dei dischi al data center di Azure. Verificare che il nome del processo e l'indirizzo completo siano riportati sull'etichetta per la spedizione. 

6. Fare clic su OK nella pagina di riepilogo per completare la creazione del processo di esportazione.

7. Dopo la spedizione dei dischi, tornare alla pagina **Importazione/Esportazione** nel portale di Azure, a) Selezionare e fare clic sul processo di importazione b) Fare clic su **Aggiorna lo stato del processo e le informazioni di tracciabilità dopo la spedizione delle unità**. 
     c) Selezionare casella di controllo "Contrassegna come spedito" d) Specificare il vettore e il numero di tracciabilità.
    
   Se il numero di tracciabilità non viene aggiornato entro due settimane dalla creazione del processo, il processo scadrà.
   
8. È possibile monitorare l'avanzamento del processo nel dashboard del portale. Vedere il significato di ogni stato del processo nella sezione [Visualizzazione dello stato dei processi](#viewing-your-job-status)precedente.

   > [!NOTE]
   > Se il blob da esportare è in uso al momento della copia sul disco rigido, servizio di Importazione/Esportazione di Azure creare uno snapshot del blob e copiare lo snapshot.
   > 
   > 
 
9. Dopo aver ricevuto le unità con i dati esportati, è possibile visualizzare e copiare le chiavi BitLocker generate dal servizio per l'unità. Passare al processo di esportazione nel portale di Azure e fare clic sulla scheda Importazione/Esportazione. Selezionare il processo di esportazione nell'elenco e fare clic sull'opzione Chiavi BitLocker. Le chiavi BitLocker vengono visualizzate come illustrato:
   
   ![Visualizzare le chiavi BitLocker per il processo di esportazione](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Vedere la sezione delle domande frequenti che riporta le domande più comuni relative all'uso del servizio.

## <a name="frequently-asked-questions"></a>Domande frequenti

**È possibile copiare Archiviazione file di Azure usando il servizio Importazione/Esportazione di Azure?**

Sì, il servizio di Importazione/Esportazione di Azure supporta l'importazione in Archiviazione file di Azure. Al momento non supporta l'esportazione di File di Azure.

**Il servizio Importazione/Esportazione di Azure è disponibile per le sottoscrizioni CSP?**

Il servizio di importazione/esportazione di Azure supporta le sottoscrizioni CSP.

**È possibile saltare il passaggio di preparazione dell'unità per un processo di importazione o preparare un disco senza copiare?**

Qualsiasi unità da spedire per l'importazione di dati deve essere preparata con lo strumento WAImportExport. È necessario usare lo strumento WAImportExport per copiare i dati nell'unità.

**È necessario eseguire la preparazione del disco durante la creazione di un processo di esportazione?**

No, ma alcuni controlli preliminari sono consigliati Verificare il numero di dischi richiesti usando il comando PreviewExport dello strumento WAImportExport. Per altre informazioni, vedere l'articolo sull' [anteprima dell'uso del disco per un processo di esportazione](https://msdn.microsoft.com/library/azure/dn722414.aspx). Consente di visualizzare l'anteprima dell'utilizzo di unità per il Blob è selezionato, in base alla dimensione delle unità che si desidera utilizzare. Controllare anche che sia possibile eseguire operazioni di lettura e scrittura sul disco rigido da spedire per il processo di esportazione.

**Cosa accade se si invia per errore un disco rigido non conforme ai requisiti supportati?**

Il data center di Azure restituirà all'utente l'unità non conforme ai requisiti supportati. Se solo alcune delle unità nel pacchetto soddisfano i requisiti per il supporto, tali unità verranno elaborate e le unità che non soddisfano i requisiti verranno restituite all'utente.

**È possibile annullare il processo?**

È possibile annullare un processo quando lo stato è Creating o Shipping.

**Per quanto tempo è possibile visualizzare lo stato dei processi completati nel portale di Azure?**

Lo stato dei processi completati può essere visualizzato per un massimo di 90 giorni. I processi completi vengono eliminati dopo 90 giorni.

**Che cosa si deve fare, se si desidera importare o esportare più di 10 unità?**

Un unico processo di importazione o esportazione può fare riferimento solo a 10 unità in un singolo processo del servizio di importazione/esportazione. Se si desidera spedire più di 10 unità, è possibile creare più processi. Le unità associate con lo stesso processo devono essere spedite insieme nello stesso pacco.
Microsoft offre informazioni e assistenza nel caso in cui la capacità venga distribuita su più processi di importazione di dischi. Per altre informazioni, contattare bulkimport@microsoft.com

**Il servizio formatta le unità prima di restituirle?**

di serie Tutte le unità vengono crittografate con BitLocker.

**È possibile acquistare da Microsoft unità per i processi di importazione/esportazione?**

di serie Sarà necessario spedire le unità per i processi sia di importazione che di esportazione.

* * Come è possibile accedere ai dati importati da questo servizio? * *

Si può accedere ai dati nell'account di archiviazione di Azure tramite il portale di Azure o usando uno strumento indipendente denominato Storage Explorer. https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer 

**Al termine del processo di importazione, come si presenteranno i dati nell'account di archiviazione? Verrà mantenuta la gerarchia delle directory?**

Quando si prepara un disco rigido per un processo di importazione, la destinazione viene specificata dal campo DstBlobPathOrPrefix nel file CSV del set di dati. Si tratta del contenitore di destinazione nell'account di archiviazione in cui vengono copiati i dati dal disco rigido. In questo contenitore di destinazione vengono create directory virtuali per le cartelle del disco rigido e BLOB per i file. 

**Se l'unità contiene file già presenti nell'account di archiviazione, il servizio sovrascriverà i BLOB o i file esistenti nell'account di archiviazione?**

Quando si prepara l'unità, è possibile specificare se i file di destinazione devono essere sovrascritti o ignorati usando il campo denominato Disposition:<rename|no-overwrite|overwrite> nel file CSV del set di dati. Per impostazione predefinita, il servizio rinomina i nuovi file anziché sovrascrivere i BLOB o i file esistenti.

**Lo strumento WAImportExport è compatibile con i sistemi operativi a 32 bit?**
di serie Lo strumento WAImportExport è compatibile solo con i sistemi operativo Windows a 64 bit. Per un elenco completo delle versioni del sistema operativo supportate, vedere la sezione Sistema operativo in [Prerequisiti](#pre-requisites) .

**È consigliabile includere qualcos'altro oltre all'unità disco rigido nel pacco?**

Spedire solo i dischi rigidi. Non inserire oggetti come cavi di alimentazione o cavi USB.

**È necessario spedire le unità tramite FedEx o DHL?**

Per spedire le unità al data center è possibile servirsi di uno dei vettori più conosciuti, come FedEx, DHL, UPS o il servizio postale nazionale. Tuttavia, per la restituzione delle unità restituite dal data center negli Stati Uniti e nell'Unione europea è necessario fornire un numero di account FedEx, mentre in Asia e in Australia è necessario fornire un numero di account DHL.

**Vi sono restrizioni in merito alla spedizione internazionale delle unità?**

È possibile che i supporti fisici spediti debbano passare i confini internazionali. È responsabilità del cliente assicurarsi che i supporti fisici e i dati siano importati e/o esportati conformemente alle leggi vigenti. Prima di spedire i supporti fisici, rivolgersi ai consulenti per verificare che i supporti e i dati possano essere spediti ai data center specificati in modo conforme alle normative. Ciò assicura che la spedizione raggiunga Microsoft in modo tempestivo.

**Durante la creazione di un processo, l'indirizzo di spedizione è una località diversa rispetto alla posizione dell'account di archiviazione. Cosa devo fare?**

Alcuni percorsi dell'account di archiviazione sono mappati a indirizzi di spedizione alternativi. Le località di spedizione disponibili in precedenza possono essere temporaneamente mappate a posizioni alternative. Controllare sempre l'indirizzo di spedizione fornito durante la creazione del processo prima di spedire le unità.

**Al momento della spedizione dell'unità, il vettore richiede l'indirizzo e il numero di telefono di contatto del data center. Cosa devo fornire?**

Il numero di telefono e l'indirizzo del controller di dominio vengono forniti come parte della creazione del processo.

**È possibile usare il servizio Importazione/Esportazione di Azure per copiare cassette postali PST e dati di SharePoint in Office 365?**

Vedere [Importare file PST o dati di SharePoint in Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**È possibile usare il servizio Importazione/Esportazione di Azure per copiare i backup offline nel servizio Backup di Azure?**

Vedere [Flusso di lavoro di Backup offline in Backup di Azure](../../backup/backup-azure-backup-import-export.md).

**Qual è il numero massimo di unità disco rigido per una spedizione?**

In una spedizione può essere presente un numero qualsiasi di unità disco rigido. Se i dischi appartengono a più processi, è consigliabile: a) Etichettare i dischi con i corrispondenti nomi dei processi. b) Aggiornare i processi con un numero di tracciabilità seguito da -1, -2 e così via.
  
**Qual è la dimensione massima dei BLOB in blocchi e dei BLOB di pagine supportata dalle operazioni di importazione/esportazione di dischi?**

La dimensione massima dei BLOB in blocchi è circa 4768 TB o 5.000.000 MB.
La dimensione massima di un BLOB di pagine è 1 TB.

**Importazione/Esportazione di Microsoft Azure supporta la crittografia AES 256?**

Per impostazione predefinita, il servizio Importazione/Esportazione di Azure usa la crittografia bitlocker AES 128, ma può usare AES 256 eseguendo la crittografia manuale con bitlocker prima che i dati vengono copiati. 

Se si usa [WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), di seguito viene indicato un comando di esempio
```
WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
```
Se si usa lo strumento [WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) specificare "AlreadyEncrypted" e immettere la chiave nel file CSV del driveset.
```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
```
## <a name="next-steps"></a>Passaggi successivi

* [Setting up the WAImportExport tool](storage-import-export-tool-how-to.md) (Configurazione dello strumento WAImportExport)
* [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)
* [Esempio di API REST del servizio Importazione/Esportazione di Azure](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

