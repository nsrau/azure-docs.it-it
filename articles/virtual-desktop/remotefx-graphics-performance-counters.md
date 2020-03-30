---
title: Diagnosticare i problemi di prestazioni grafiche Desktop remoto - AzureDiagnose graphics performance issues Remote Desktop - Azure
description: In questo articolo viene descritto come utilizzare i contatori di grafica RemoteFX nelle sessioni di protocollo di desktop remoto per diagnosticare i problemi di prestazioni con la grafica in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 84cee86dbddff77f6142925eec01889cf793a466
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127563"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnosticare i problemi di prestazioni grafiche in Desktop remoto

Per diagnosticare problemi di qualità dell'esperienza con le sessioni remote, sono stati forniti contatori nella sezione Grafica RemoteFX di Performance Monitor. In questo articolo consente di individuare e correggere i colli di bottiglia delle prestazioni correlati alla grafica durante le sessioni Remote Desktop Protocol (RDP) utilizzando questi contatori.

## <a name="find-your-remote-session-name"></a>Trovare il nome della sessione remota

È necessario il nome della sessione remota per identificare i contatori delle prestazioni grafiche. Seguire le istruzioni in questa sezione per identificare l'istanza di ogni contatore.

1. Aprire il prompt dei comandi di Windows dalla sessione remota.
2. Eseguire il comando **qwinsta** e trovare il nome della sessione.
    - Se la sessione è ospitata in una macchina virtuale (VM): l'istanza di ogni contatore è con suffisso con lo stesso numero che suffixa il nome della sessione, ad esempio "rdp-tcp 37".
    - Se la sessione è ospitata in una macchina virtuale che supporta unità di elaborazione grafica virtuale (vGPU): l'istanza di ogni contatore viene archiviata nel server anziché nella macchina virtuale. Le istanze del contatore includono il nome della macchina virtuale anziché il numero nel nome della sessione, ad esempio "Win8 Enterprise VM".

>[!NOTE]
> Mentre i contatori hanno RemoteFX nei loro nomi, includono grafica desktop remoto anche in scenari vGPU.

## <a name="access-performance-counters"></a>Accedere ai contatori delle prestazioniAccess performance counters

Dopo aver determinato il nome della sessione remota, seguire queste istruzioni per raccogliere i contatori delle prestazioni di grafica RemoteFX per la sessione remota.

1. Selezionare **Avvia** > **Performance Monitor****di Strumenti** > di amministrazione .
2. Nella finestra di dialogo **Performance Monitor** espandere Strumenti di **monitoraggio**, selezionare **Performance Monitor**e quindi **Aggiungi**.
3. Nell'elenco **Contatori disponibili** della finestra di dialogo **Aggiungi contatori** espandere la sezione Per grafica RemoteFX.
4. Selezionare i contatori da monitorare.
5. Nell'elenco **Istanze dell'oggetto selezionato** selezionare le istanze specifiche da monitorare per i contatori selezionati, quindi selezionare **Aggiungi**. Per selezionare tutte le istanze di contatore disponibili, selezionare **Tutte le istanze**.
6. Dopo aver aggiunto i contatori, selezionare **OK**.

I contatori delle prestazioni selezionati verranno visualizzati nella schermata Performance Monitor.

>[!NOTE]
>Ogni sessione attiva in un host ha la propria istanza di ogni contatore delle prestazioni.

## <a name="diagnose-issues"></a>Diagnosticare i problemi

I problemi di prestazioni relativi alla grafica in genere rientrano in quattro categorie:Graphics-related performance issues generally in quattro categories:

- Frequenza fotogrammi bassa
- Stalle casuali
- Alta latenza di input
- Scarsa qualità del telaio

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Risoluzione di frame rate bassi, stalli casuali e latenza di input elevata

Controllare innanzitutto il contatore Fotogrammi di output/secondo. Misura il numero di frame messi a disposizione del cliente. Se questo valore è minore del contatore Fotogrammi di input/secondo, i fotogrammi vengono ignorati. Per identificare il collo di bottiglia, utilizzare i contatori Frame ignorati/secondi.

Esistono tre tipi di contatori Frame ignorati/secondi:

- Frame ignorati/secondi (risorse insufficienti del server)
- Frame ignorati/secondi (risorse di rete insufficienti)
- Frame ignorati/secondi (risorse client insufficienti)

Un valore elevato per uno qualsiasi dei contatori Frames Skipped/Second implica che il problema è correlato alla risorsa su cui il contatore tiene traccia. Ad esempio, se il client non decodifica e presenta i frame alla stessa velocità fornita dal server, il contatore Frame ignorati/secondo (risorse client insufficienti) sarà elevato.

Se il contatore Fotogrammi di output al secondo corrisponde al contatore Fotogrammi di input al secondo, tuttavia si nota ancora un ritardo o un blocco insolito, il colpevole del tempo medio di codifica potrebbe essere il risultato. La codifica è un processo sincrono che si verifica sul server nello scenario a sessione singola (vGPU) e nella macchina virtuale nello scenario multisessione. Tempo medio di codifica deve essere inferiore a 33 ms. Se il tempo medio di codifica è inferiore a 33 ms ma si verificano ancora problemi di prestazioni, potrebbe essersi verificato un problema con l'app o il sistema operativo in uso.

Per ulteriori informazioni sulla diagnosi dei problemi relativi alle app, vedere [Contatori delle prestazioni Ritardo input utente](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

Poiché RDP supporta un tempo medio di codifica di 33 ms, supporta una frequenza fotogrammi di input fino a 30 fotogrammi al secondo. Si noti che 33 ms è la frequenza fotogrammi massima supportata. In molti casi, la frequenza fotogrammi sperimentata dall'utente sarà inferiore, a seconda della frequenza con cui un frame viene fornito a RDP dalla fonte. Ad esempio, attività come la visione di un video richiedono una frequenza fotogrammi di input completa di 30 fotogrammi al secondo, ma attività meno computazionali come la modifica rara di un documento generano un valore molto più basso per i fotogrammi di input al secondo senza alcuna riduzione della qualità dell'esperienza dell'utente.

### <a name="addressing-poor-frame-quality"></a>Affrontare la scarsa qualità del telaio

Utilizzare il contatore Qualità telaio per diagnosticare i problemi di qualità del telaio. Questo contatore esprime la qualità del fotogramma di output come percentuale della qualità del fotogramma sorgente. La perdita di qualità può essere dovuta a RemoteFX, o può essere inerente alla sorgente grafica. Se RemoteFX ha causato la perdita di qualità, il problema potrebbe essere una mancanza di risorse di rete o del server per inviare contenuto con maggiore fedeltà.

## <a name="mitigation"></a>Strategia di riduzione del rischio

Se le risorse del server causano il collo di bottiglia, provare uno dei seguenti approcci per migliorare le prestazioni:

- Ridurre il numero di sessioni per host.
- Aumentare la memoria e le risorse di calcolo nel server.
- Eliminare la risoluzione della connessione.

Se il collo di bottiglia causa le risorse di rete, provare uno dei seguenti approcci per migliorare la disponibilità della rete per sessione:

- Ridurre il numero di sessioni per host.
- Utilizzare una rete con larghezza di banda superiore.
- Eliminare la risoluzione della connessione.

Se le risorse client causano il collo di bottiglia, provare uno dei seguenti approcci per migliorare le prestazioni:

- Installare il client Desktop remoto più recente.
- Aumentare la memoria e le risorse di calcolo nel computer client.

> [!NOTE]
> Attualmente non è supportato il contatore Frame di origine al secondo. Per ora, il contatore Frame di origine al secondo visualizzerà sempre 0.

## <a name="next-steps"></a>Passaggi successivi

- Per creare una macchina virtuale di Azure ottimizzata per GPU, vedere [Configurare l'accelerazione delle unità di elaborazione grafica (GPU) per l'ambiente Windows Virtual Desktop.](configure-vm-gpu.md)
- Per una panoramica della risoluzione dei problemi e delle tracce di escalation, vedere [Panoramica della risoluzione dei problemi, feedback e supporto](troubleshoot-set-up-overview.md).
- Per ulteriori informazioni sul servizio, vedere [Ambiente desktop di Windows](environment-setup.md).
