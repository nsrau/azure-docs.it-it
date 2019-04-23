---
title: Valutare le opzioni di migrazione ai dati da StorSimple serie 5000-7000 | Microsoft Docs
description: Viene fornita una panoramica delle opzioni per la migrazione dei dati da StorSimple serie 5000-7000.
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
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 1e637f58b392b2de67a5ead9d57a6a87ab705b93
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60007239"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Opzioni di migrazione dei dati da un dispositivo StorSimple serie 5000-7000 

> [!IMPORTANT]
> 9 luglio 2019 della serie StorSimple 5000/7000 terminerà lo stato di supporto (May). È consigliabile che i clienti della serie StorSimple 5000/7000 eseguano la migrazione a una delle alternative descritte nel documento.

A luglio 2019, StorSimple serie 5000-7000 volgerà al [termine del supporto](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series). I clienti che stanno eseguendo StorSimple serie 5000-7000 possono per eseguire l'aggiornamento ad altri servizi ibridi prodotti direttamente da Azure. Questo articolo descrive le opzioni ibride di Azure disponibili per la migrazione dei dati. 

## <a name="migration-options"></a>Opzioni di migrazione

I clienti che usano StorSimple serie 5000-7000 dispongono di opzioni di Azure o di terze parti.

### <a name="azure-options"></a>Opzioni di Azure

#### <a name="upgrade-to-storsimple-8000-series"></a>Eseguire l'aggiornamento a StorSimple serie 8000

Eseguire l'aggiornamento a StorSimple serie 8000 e continuare sulla piattaforma di StorSimple.  Questo percorso di aggiornamento richiede ai clienti di sostituire i propri dispositivi serie 5000-7000 con un dispositivo serie 8000. Viene eseguita la migrazione dei dati dal dispositivo serie 5000-7000 mediante lo strumento di migrazione. Una volta completata la migrazione, i dispositivi StorSimple serie 8000 continueranno a suddividere i dati in livelli di Archiviazione BLOB di Azure. 

Per altre informazioni sulle modalità di esecuzione dei dati usando una dispositivo StorSimple serie 8000, passare a [Eseguire la migrazione dei dati da StorSimple serie 5000-7000 a un dispositivo serie 8000](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Eseguire la migrazione a Sincronizzazione file di Azure

Questa opzione di migrazione completamente nuova consente ai clienti di archiviare le condivisioni file dell'organizzazione in File di Azure. Queste condivisioni file sono quindi centralizzate per l'accesso in locale tramite l'uso di Sincronizzazione file di Azure (AFS). AFS può essere distribuito in un host server di Windows. La migrazione effettiva dei dati viene quindi eseguita come una copia host o tramite lo strumento di migrazione.

Per altre informazioni sulle modalità di esecuzione della migrazione dei dati a Sincronizzazione file di Azure, passare a [Eseguire la migrazione dei dati da StorSimple serie 5000-7000 a una Sincronizzazione file di Azure](storsimple-5000-7000-afs-migration.md).

### <a name="third-party-options"></a>Opzioni di terze parti

#### <a name="migrate-to-panzura-freedom-nas"></a>Eseguire la migrazione a Panzura Freedom NAS

I clienti di StorSimple 5000-7000 possono scegliere di eseguire la migrazione a Panzura Freedom NAS per mantenere i dati in Azure. Panzura Freedom offre una soluzione NAS che si estende a data center, uffici e cloud pubblici e privati. La soluzione supporta flussi di lavoro relativi a dati locali, ibridi e nel cloud per client NFS e SMB e client per dispositivi mobili. 

Questa migrazione è supportata da Panzura e per iniziare i clienti possono richiedere supporto per la migrazione dal [sito Web Panzura](https://panzura.com/storsimple-migration/).

#### <a name="migrate-to-cohesity"></a>Eseguire la migrazione a Cohesity

Cohesity consente di eseguire la migrazione dei dati da StorSimple 5000-7000 alla piattaforma dati Cohesity in Azure. La piattaforma dati Cohesity è una soluzione sul Web software-defined che consente di consolidare file, backup, oggetti e macchine virtuali in un'unica soluzione nativa nel cloud. Dopo la migrazione alla piattaforma dati, è possibile gestire, proteggere ed eseguire il provisioning dei dati e delle app da cloud a core tramite un unico riquadro. Con Cohesity, iniziare con non più di tre nodi. 

Altre informazioni, vedere [migrazione alla piattaforma di dati di Cohesity](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>Eseguire la migrazione a Nasuni

Nasuni semplifica ai clienti di StorSimple 5000-7000 di eseguire la migrazione e conservano i dati in Azure.  Nasuni è un'importante soluzione di archiviazione NAS basato su Azure, fornendo ai clienti prestazioni e sicurezza che si aspettano da soluzioni in locale, con costi del cloud e la scala.  Oltre ad archiviazione file ad alte prestazioni, Nasuni e Azure handle di backup e ripristino di emergenza, consentendo di condividere e collaborare sui dati in tutto il mondo con la gestione archiviazione centralizzata dei file. 

Nasuni offre l'esperienza per facilitare la migrazione: inizia subito: https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Eseguire la migrazione a Talon FAST

Talon semplifica ai clienti di StorSimple 5000-7000 di continuare a sfruttare i vantaggi che sono con valori di gran parte della piattaforma di StorSimple (piccolo footprint sul posto supportati da risorse cloud senza limiti) con la funzione ancora maggiore.  Con la soluzione FAST Talon, i clienti possono eseguire la migrazione e mantenere i propri dati in Azure, mentre ora avere un footprint in sede solo software anche più piccolo e l'aggiunta di vantaggi, ad esempio file globale il blocco per lo spazio dei nomi globale e la collaborazione di più siti.  Talon è un'importante soluzione ecosistema di Azure, con i clienti globali per eseguire la migrazione dei carichi di lavoro server file dall'istanza locale di un footprint consolidato, basato su Azure senza compromessi in termini di flusso di lavoro utente o esperienza.  

Altre informazioni sull'evoluzione di un'azienda consolidata cloud in https://www.talonstorage.com/alliances/microsoft-storsimple.


## <a name="migration---frequently-asked-questions"></a>Migrazioni: domande frequenti

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>D: Quando raggiungono il termine del servizio i dispositivi StorSimple 5000 e i dispositivi serie 7000? 

R. StorSimple serie 5000-7000 raggiungerà il [termine del servizio](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) a luglio 2019. Il termine del servizio implica che Microsoft non sarà in grado di fornire supporto per hardware e software a questi dispositivi dopo luglio 2019. Si consiglia di iniziare adesso la preparazione di un piano per la migrazione dei dati dai dispositivi.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>D: Cosa accade ai dati archiviati in Azure?  

R. È possibile continuare a usare i dati in Azure quando si esegue la migrazione a un servizio più recente. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>D: Cosa accade ai dati archiviati localmente nel dispositivo StorSimple? 

R. Possono essere copiati i dati nel dispositivo locale al servizio più recente, come descritto nei documenti di migrazione.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>D: Cosa accade se vuole mantenere l'appliance di StorSimple serie 5000/7000? 

R. Mentre i servizi potrebbero continuare a funzionare, Microsoft non sarà in grado di fornire supporto per hardware e software. La migrazione è fortemente consigliata per la continuità aziendale.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>D: Quali sono le opzioni disponibili per la migrazione dei dati dai dispositivi StorSimple serie 5000-7000? 

R. A seconda dello scenario, gli utenti di StorSimple serie 5000-7000 dispongono delle opzioni di migrazione seguenti. 

 - **Eseguire l'aggiornamento alla serie 8000**: usare questa opzione quando si desidera continuare le operazioni sulla piattaforma di StorSimple. 
 - **Eseguire la migrazione a Sincronizzazione file di Azure**: usare questa opzione quando si desidera passare al formato nativo di Azure. È possibile usare Sincronizzazione file di Azure per la gestione centralizzata delle condivisioni file. 

È possibile contattare il supporto tecnico Microsoft per valutare le opzioni di migrazione non elencate qui.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>D: La migrazione ad altre soluzioni di archiviazione è supportata?

R. Sì. La migrazione da altre soluzioni di archiviazione tramite copia host dei dati è supportata.

### <a name="q-is-migration-supported-by-microsoft"></a>D: La migrazione è supportata da Microsoft? 

R. La migrazione dalla serie 5000 o 7000 è un'operazione completamente supportata. Infatti, Microsoft consiglia di rivolgersi al supporto tecnico prima di iniziare la migrazione. La migrazione è attualmente un'operazione assistita. Se si prevede di migrare i dati dal dispositivo StorSimple serie 5000-7000 [Aprire un ticket di supporto](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>D: Qual è il modello di determinazione prezzi per entrambe le opzioni di migrazione?

R. Il costo della migrazione varia in base all'opzione scelta. Mentre la migrazione è gratuita, se si decide di eseguire l'aggiornamento a una serie StorSimple 8000, sarà aggiunto il costo del dispositivo hardware. 

Allo stesso modo, quando si usa Sincronizzazione file di Azure, la sottoscrizione per il servizio potrebbe essere a pagamento. In ogni caso, i clienti saranno tenuti a pagare i costi di archiviazione attuali. Vedere gli argomenti seguenti per una stima: 
- [Prezzi di StorSimple](https://azure.microsoft.com/pricing/details/storsimple/)  
- [Prezzi di AFS]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>D:  Quanto tempo ci vuole per completare una certificazione?

R. Il tempo necessario per la migrazione dei dati varia a seconda della quantità di dati e dall'opzione di aggiornamento selezionata. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>D: Qual è la data di fine supporto per StorSimple serie 8000?

R. La data di fine supporto per StorSimple serie 8000 è pubblicata [qui](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>Passaggi successivi
 - [Eseguire la migrazione dei dati da un dispositivo StorSimple serie 5000/7000 a un dispositivo serie 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Eseguire la migrazione dei dati da un dispositivo StorSimple serie 5000/7000 a Sincronizzazione file di Azure](storsimple-5000-7000-afs-migration.md)
