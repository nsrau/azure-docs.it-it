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
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: 8f34d5a38f09f015547f52cc4b44819b780932bb
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818859"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Opzioni di migrazione dei dati da un dispositivo StorSimple serie 5000-7000 

> [!IMPORTANT]
> Il 31 luglio 2019, la serie StorSimple 5000/7000 raggiungerà lo stato di fine del supporto (EOS). È consigliabile che i clienti della serie StorSimple 5000/7000 eseguano la migrazione a una delle alternative descritte nel documento.

A luglio 2019, StorSimple serie 5000-7000 volgerà al [termine del supporto](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series). I clienti che stanno eseguendo StorSimple serie 5000-7000 possono per eseguire l'aggiornamento ad altri servizi ibridi prodotti direttamente da Azure. Questo articolo descrive le opzioni ibride di Azure disponibili per la migrazione dei dati. 

## <a name="migration-options"></a>Opzioni di migrazione

I clienti che usano StorSimple serie 5000-7000 dispongono delle due opzioni principali seguenti:

- **Eseguire l'aggiornamento a StorSimple serie 8000** : eseguire l'aggiornamento a StorSimple serie 8000 e continuare l'uso sulla piattaforma di StorSimple.  Questo percorso di aggiornamento richiede ai clienti di sostituire i propri dispositivi serie 5000-7000 con un dispositivo serie 8000. Viene eseguita la migrazione dei dati dal dispositivo serie 5000-7000 mediante lo strumento di migrazione. Una volta completata la migrazione, i dispositivi StorSimple serie 8000 continueranno a suddividere i dati in livelli di Archiviazione BLOB di Azure. 

    Per altre informazioni sulle modalità di esecuzione dei dati usando una dispositivo StorSimple serie 8000, passare a [Eseguire la migrazione dei dati da StorSimple serie 5000-7000 a un dispositivo serie 8000](storsimple-8000-migrate-from-5000-7000.md).

- **Eseguire la migrazione a Sincronizzazione file di Azure**: questa opzione di migrazione completamente nuova consente ai clienti di archiviare le condivisioni file dell'organizzazione nei file di Azure. Queste condivisioni file sono quindi centralizzate per l'accesso in locale tramite l'uso di Sincronizzazione file di Azure (AFS). AFS può essere distribuito in un host server di Windows. La migrazione effettiva dei dati viene quindi eseguita come una copia host o tramite lo strumento di migrazione.

    Per altre informazioni sulle modalità di esecuzione della migrazione dei dati a Sincronizzazione file di Azure, passare a [Eseguire la migrazione dei dati da StorSimple serie 5000-7000 a una Sincronizzazione file di Azure](https://aka.ms/StorSimpleMigrationAFS).

## <a name="migration---frequently-asked-questions"></a>Migrazioni: domande frequenti

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>D: Quando raggiungono il termine del servizio i dispositivi StorSimple 5000 e i dispositivi serie 7000? 

R. StorSimple serie 5000-7000 raggiungerà il [termine del servizio](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) a luglio 2019. Il termine del servizio implica che Microsoft non sarà in grado di fornire supporto per hardware e software a questi dispositivi dopo luglio 2019. Si consiglia di iniziare adesso la preparazione di un piano per la migrazione dei dati dai dispositivi.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>D: Cosa accade ai dati archiviati in Azure?  

R. È possibile continuare a usare i dati in Azure quando si esegue la migrazione a un servizio più recente. 


### <a name="q--what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>D:  Cosa accade ai dati archiviati localmente nel dispositivo StorSimple? 

R. Possono essere copiati i dati nel dispositivo locale al servizio più recente, come descritto nei documenti di migrazione.

### <a name="what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>Cosa accade se vuole mantenere l'appliance di StorSimple serie 5000/7000? 

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

### <a name="q-how-does-the-cost-compare-for-the-two-listed-migrations-to-azure-hybrid-services"></a>D: Quali differenze di costo vi sono per le due migrazioni elencate nei servizi ibridi di Azure? 

R. Il costo della migrazione varia in base all'opzione scelta. Mentre la migrazione è gratuita, se si decide di eseguire l'aggiornamento a una serie StorSimple 8000, sarà aggiunto il costo del dispositivo hardware. Allo stesso modo, quando si usa Sincronizzazione file di Azure, la sottoscrizione per il servizio potrebbe essere a pagamento. In ogni caso, i clienti saranno tenuti a pagare i costi di archiviazione attuali. Fare riferimento a [Calcolatore dei prezzi di Microsoft per i rispettivi servizi](https://azure.microsoft.com/pricing/#product-picker) allo scopo di ricevere una stima.  

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>D:  Quanto tempo ci vuole per completare una certificazione?

R. Il tempo necessario per la migrazione dei dati varia a seconda della quantità di dati e dall'opzione di aggiornamento selezionata. 

## <a name="next-steps"></a>Passaggi successivi
 - [Eseguire la migrazione dei dati da un dispositivo StorSimple serie 5000/7000 a un dispositivo serie 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Eseguire la migrazione dei dati da un dispositivo StorSimple serie 5000/7000 a Sincronizzazione file di Azure](storsimple-5000-7000-afs-migration.md)
