---
title: Eseguire la migrazione dei dati da StorSimple serie 5000-7000 a Sincronizzazione file di Azure | Microsoft Docs
description: Illustra come eseguire la migrazione dei dati da StorSimple serie 5000-7000 a Sincronizzazione file di Azure (AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b46e9ee8fc3e14981a01cc2425a8ce55d06c5a9a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65150744"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Eseguire la migrazione dei dati da StorSimple serie 5000/7000 a Sincronizzazione file di Azure

> [!IMPORTANT]
> 9 luglio 2019 della serie StorSimple 5000/7000 terminerà lo stato di supporto (May). È consigliabile che i clienti della serie StorSimple 5000/7000 eseguano la migrazione a una delle alternative descritte nel documento.

La migrazione dei dati è il processo di trasferimento dei dati da una posizione di archiviazione a un'altra. Questo comporta la creazione di una copia esatta dei dati attuali di un'organizzazione da un dispositivo a un altro, preferibilmente senza interrompere o disabilitare le applicazioni attive, reindirizzando quindi l'attività di tutti gli input/output (I/O) al nuovo dispositivo. 

I servizi di archiviazione StorSimple serie 5000 e 7000 termineranno il servizio a luglio 2019. Ciò significa che Microsoft non sarà più in grado di supportare hardware e software per la serie StorSimple 5000-7000 dopo luglio 2019. I clienti che usano questi dispositivi devono eseguire la migrazione dei dati di StorSimple ad altre soluzioni di archiviazione ibrida di Azure. Questo articolo illustra la migrazione dei dati da un dispositivo StorSimple serie 5000 o 7000 a Sincronizzazione file di Azure (AFS).

## <a name="intended-audience"></a>Destinatari

Questo articolo è destinato a professionisti informatici (IT) e a knowledge worker responsabili della distribuzione e della gestione dei dispositivi della serie StorSimple 5000-7000 del data center. I clienti, usando i dispositivi StorSimple per carichi di lavoro di file server (con Windows Server), possono trovare questo percorso di migrazione particolarmente interessante. Se si ritiene che le funzionalità di Sincronizzazione file di Azure funzionino anche per l'organizzazione, questo articolo aiuterà a comprendere come spostare tali soluzioni da StorSimple.

## <a name="migration-considerations"></a>Considerazioni sulla migrazione

Questo processo funziona per i clienti che hanno configurato una condivisione di file di Windows con un volume StorSimple per l'archiviazione. La migrazione dei dati da StorSimple 5000 o 7000 a Sincronizzazione file di Azure comporta la conversione di tale percorso della condivisione file a un [Endpoint Server](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) e quindi l'utilizzo di unità collegate in locale come endpoint alternativo che diventerà in seguito la nuova posizione. 

Quando si passa a AFS, è necessario considerare gli aspetti seguenti:

1. File di Azure attualmente ha un limite di 5 TB di condivisione. È possibile superare questa limitazione usando Sincronizzazione file di Azure con i dati distribuiti tra più condivisioni di File di Azure. Per altre informazioni, vedere il [modello di aumento delle dimensioni dei dati per la distribuzione di File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Questa migrazione scarica l'intero set di dati primario in un dispositivo in locale dal momento che la copia dei dati avviene dal dispositivo locale. Assicurarsi di avere larghezza di banda sufficiente per supportare questo trasferimento.
3. Questo processo non mantiene gli snapshot che sono già stati creati. Effettua esclusivamente la migrazione di dati primari. Il processo non mantiene inoltre i modelli di larghezza di banda associati o i criteri di backup. [Usare Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-files) per configurare i criteri di backup dopo la migrazione dei dati nella condivisione di File di Azure.
4. StorSimple fornisce hardware first party. Tuttavia, con Sincronizzazione file di Azure o File di Azure, si usa il proprio hardware di Windows Server locale come cache locale. È necessario assicurarsi di avere sufficiente capacità di archiviazione per mantenere in locale il set di dati di propria scelta. Per altre informazioni sulla suddivisione in livelli e l'impostazione senza requisiti dello spazio di destinazione, vedere come [Creare un endpoint server quando si distribuisce Sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Rivedere [Prezzi per Sincronizzazione file di Azure](https://azure.microsoft.com/pricing/details/storage/files/) perché variano in base a StorSimple. AFS non comprende deduplicazione e compressione come StorSimple.

## <a name="migration-prerequisites"></a>Prerequisiti per la migrazione

Di seguito sono elencati i prerequisiti per la migrazione di un dispositivo legacy serie 5000 o 7000 a Sincronizzazione file di Azure. Prima di iniziare, assicurarsi di disporre di:

- Accesso a StorSimple serie 5000-7000 con software di versione 2.1.1.518 o successiva. Le versioni precedenti non sono supportate. L'angolo superiore destro dell'interfaccia utente Web del dispositivo StorSimple deve visualizzare la versione del software che è in esecuzione.  
    Se il dispositivo attivo non esegue la versione 2.1.1.518, aggiornare il sistema alla versione minima richiesta. Per istruzioni dettagliate, vedere [Upgrade your system to v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518) (Aggiornare il sistema alla versione 2.1.1.518).
- Verificare se sul dispositivo di origine sono in esecuzione processi di backup attivi. inclusi quelli sull'host di StorSimple Data Protection Console. Attendere che i processi in corso siano completati. 
- Accesso a un host Windows Server connesso a dispositivo StorSimple serie 5000-7000. L'host deve avere in esecuzione una versione supportata di Windows Server come descritto in [Interoperabilità di Sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- I volumi StorSimple vengono montati nell'host e contengono condivisioni file.
- L'host dispone di spazio di archiviazione locale sufficiente per contenere i dati memorizzati nella cache locale.
- Livello di accesso come proprietario della sottoscrizione di Azure che si userà per distribuire Sincronizzazione file di Azure. Si potrebbero riscontrare problemi durante la creazione di un endpoint cloud per il gruppo di sincronizzazione se non si dispone di autorizzazioni a livello di amministratore o proprietario.
- Accesso a un [account di archiviazione per utilizzo generico v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) con una condivisione di File di Azure di cui si vuole eseguire la sincronizzazione. Per altre informazioni, vedere [creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - Come [Creare una condivisione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Processo di migrazione

La migrazione dei dati da StorSimple 5000-7000 a AFS è un processo che si compone di due passaggi:
1.  Replicare i dati dal file server in locale in cui vengono montati i volumi StorSimple a una condivisione File di Azure.  La replica viene eseguita tramite un agente AFS da installare.
2.  Disconnettere il dispositivo StorSimple. I dischi locali fungono quindi da cache locale.

### <a name="migration-steps"></a>Passaggi della migrazione

Eseguire la procedura seguente per effettuare la migrazione di condivisione file di Windows configurata nei volumi StorSimple a una condivisione di Sincronizzazione file di Azure. 
1.  Eseguire questi passaggi nello stesso host di Windows Server in cui i volumi StorSimple vengono montati o usano un sistema diverso. 
    - [Preparare Windows Server per l'uso con Sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Installare l'agente Sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Distribuire il servizio di sincronizzazione archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Registrare Windows Server con il servizio di sincronizzazione archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Creare un gruppo di sincronizzazione e un endpoint cloud](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). La sincronizzazione dei gruppi è necessaria per ogni file di condivisione Windows di cui bisogna eseguire la migrazione dall'host.
    - [Creare un endpoint server](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Specificare il percorso come percorso del volume StorSimple contenente i dati della condivisione file. Ad esempio, se il volume di StorSimple è unità `J`, e i dati si trovano in `J:/<myafsshare>`, aggiungere questo percorso come un endpoint server. Lasciare la **Suddivisione in livelli** su **Disabilitata**.
2.  Attendere fino a quando non è stata completata la sincronizzazione del file server. Per ogni server in un gruppo di sincronizzazione specificato, assicurarsi che:
    - I timestamp dell'ultimo tentativo di sincronizzazione per il caricamento e il download siano recenti.
    - Lo stato sia verde per il caricamento e per il download.
    - L'**attività di sincronizzazione** visualizzi un numero molto ridotto di file o nessun file rimanente da sincronizzare.
    - Il numero di **file non sincronizzati** sia 0 sia per il caricamento che per il download.
    Per altre informazioni su quando è stata completata la sincronizzazione del server, consultare [Risolvere i problemi di Sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). La sincronizzazione potrebbe richiedere da diverse ore a giorni, a seconda delle dimensioni dei dati e della larghezza della banda. Una volta completata la sincronizzazione, tutti i dati sono al sicuro nella condivisione File di Azure. 
3.  Passare alle condivisioni sui volumi StorSimple. Fare clic con il pulsante destro del mouse su una condivisione e scegliere **Proprietà**. Tenere presente le autorizzazioni della condivisione sotto **Sicurezza**. Queste autorizzazioni dovranno essere applicate manualmente alla nuova condivisione nel passaggio successivo.
4.  A seconda che si usi lo stesso host di Windows Server o un altro, i passaggi successivi saranno diversi.

    Ignorare questo passaggio e andare al passaggio successivo, se si usa un altro host di Windows Server. Se si usa lo stesso Server di file di Windows per AFS, si verificheranno alcuni minuti di inattività. 
    - **Il tempo di inattività inizia** -eliminare l'endpoint del server che è stato creato nel *passaggio 1F*. 
    - Creare un nuovo endpoint di server con il percorso in cui si desidera che verranno trovati i dati successivamente.
    - Quando l'endpoint del server viene mostrato come Integro (l'operazione potrebbe richiedere alcuni minuti), si visualizzeranno i dati nella nuova posizione. È ora possibile configurare l'host di Windows Server per rendere disponibili i file da questo nuovo percorso. - **Tempo di inattività finisce**.
5.  Se si usa un altro file Server di Windows per Sincronizzazione file di Azure, non si verificheranno tempi di inattività. 
    - Aggiungere un altro endpoint server con il percorso della risorsa di archiviazione locale che si è pronti a usare come cache anziché il dispositivo StorSimple. 
    - Sarà possibile visualizzare i file nel nuovo server in pochi minuti. Sarà possibile passare dal dispositivo StorSimple al nuovo percorso nell'host in qualsiasi momento.

    > [!TIP] 
    > Si consiglia di configurare questa nuova condivisione di file con lo stesso nome e percorso di quella che sostituisce per ridurre al minimo le interruzioni. Se si usa DFS-N, potrebbe essere necessario apportare modifiche alla configurazione.
6.  Riconfigurare le autorizzazioni di condivisione come indicato nel *passaggio 3*.

Se si riscontrano problemi durante la migrazione dei dati, [contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Passaggi successivi

Se AFS non è la soluzione ideale per l'utente, vedere l'articolo sulla [Migrazione dei dati da StorSimple 5000-7000 serie a un dispositivo 8000 serie](storsimple-8000-migrate-from-5000-7000.md).

