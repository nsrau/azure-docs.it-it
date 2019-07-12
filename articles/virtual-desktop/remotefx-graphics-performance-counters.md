---
title: Diagnosi dei problemi di prestazioni della grafica nel desktop remoto - Azure
description: Questo articolo descrive come usare i contatori grafici RemoteFX in sessioni remote desktop protocol per diagnosticare problemi di prestazioni con la grafica in Windows Desktop virtuale.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: v-chjenk
ms.openlocfilehash: a139542bf9272336784ac96d667d65caa1ed96ff
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607342"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnosticare i problemi di prestazioni della grafica in Desktop remoto

Quando il sistema non esegue come previsto, è importante identificare l'origine del problema. Questo articolo consente di identificare e risolvere i colli di bottiglia delle prestazioni correlato alla grafica durante le sessioni Remote Desktop Protocol (RDP).

## <a name="find-your-remote-session-name"></a>Trovare il nome di sessione remota

È necessario il nome della sessione remota per identificare i contatori delle prestazioni della grafica. Seguire le istruzioni in questa sezione per identificare il nome della sessione remota Windows Virtual Desktop Preview.

1. Aprire il prompt dei comandi di Windows dalla sessione remota.
2. Eseguire la **qwinsta** comando.
    - Se la sessione è ospitata in una macchina virtuale multisessione (VM): Il suffisso per ogni nome di contatore è lo stesso suffisso il nome di sessione, ad esempio "rdp-tcp 37."
    - Se la sessione è ospitata in una macchina virtuale che supporta virtual Graphics Processing unità (vGPU): I contatori vengono archiviati sul server invece che nella macchina virtuale. Le istanze del contatore includono il nome della macchina virtuale anziché il numero nel nome della sessione, ad esempio "Win8 Enterprise della macchina virtuale."

>[!NOTE]
> Mentre i contatori contengono RemoteFX nel nome, includono grafica desktop remota anche scenari vGPU.

## <a name="access-performance-counters"></a>Contatori delle prestazioni di accesso

I contatori delle prestazioni della grafica di RemoteFX consentono di rilevare i colli di bottiglia grazie alla possibilità di tenere traccia di elementi quali frame ora la codifica e ignorati i frame.

Dopo aver determinato il nome della sessione remota, seguire queste istruzioni per raccogliere i contatori delle prestazioni della grafica di RemoteFX per la sessione remota.

1. Selezionare **avviare** > **strumenti di amministrazione** > **Performance Monitor**.
2. Nel **Performance Monitor** finestra di dialogo espandere **strumenti di monitoraggio**, selezionare **Performance Monitor**e quindi selezionare **Aggiungi**.
3. Nel **Aggiungi contatori** della finestra di dialogo dalle **contatori disponibili** elencare, espandere l'oggetto contatore delle prestazioni per la grafica di RemoteFX.
4. Selezionare i contatori da monitorare.
5. Nel **istanze dell'oggetto selezionato** , selezionare le istanze specifiche da monitorare per i contatori selezionati e quindi selezionare **Add**. Per selezionare tutte le istanze di contatore disponibili, selezionare **tutte le istanze**.
6. Dopo aver aggiunto i contatori, selezionare **OK**.

I contatori delle prestazioni selezionati verranno visualizzati nella schermata di Performance Monitor.

>[!NOTE]
>Ogni sessione attiva in un host ha una propria istanza di ogni contatore delle prestazioni.

## <a name="diagnosis"></a>Diagnosi

Problemi di prestazioni correlati alla grafica rientrano generalmente in quattro categorie:

- Frequenza ridotta dei fotogrammi
- Stalli casuale
- Latenza di input elevata
- Qualità scarsa frame

Per iniziare, che punta a frequenza ridotta dei fotogrammi, gli stalli casuale e latenza di input elevata. Nella sezione successiva si indicherà a ogni categoria di contatori delle prestazioni misurano.

### <a name="performance-counters"></a>Contatori delle prestazioni

In questa sezione consente di identificare i colli di bottiglia.

Prima di tutto controllare il contatore Frames/Second di Output. Misura il numero di frame reso disponibile al client. Se questo valore è minore del contatore Frames/Second Input, sono saltati frame. Per identificare il collo di bottiglia, usare i fotogrammi i contatori ignorati al secondo.

Esistono tre tipi di fotogrammi ignorati al secondo contatori:

- Fotogrammi ignorati al secondo (risorse di rete insufficienti)
- Fotogrammi ignorati al secondo (Client insufficiente di risorse)
- Fotogrammi ignorati al secondo (risorse Server insufficienti)

Un valore elevato per uno qualsiasi dei fotogrammi ignorati al secondo contatori implica che il problema è correlato alla risorsa il contatore si tiene traccia. Ad esempio, se il client non decodifica e frame presenti alla stessa tariffa del server fornisce i frame, il contatore di fotogrammi ignorati al secondo (Client risorse insufficienti) sarà elevato.

Se il contatore Frames/Second Output corrisponde al contatore Frames/Second Input, comunque ancora lag insolite o blocco, il problema potrebbe essere il tempo medio di codifica. La codifica è un processo sincrono che si verifica sul server nello scenario di una singola sessione (vGPU) e nella macchina virtuale nello scenario multi-sessione. Tempo medio di codifica deve essere inferiore a 33 ms. Se il tempo di codifica Media è inferiore a 33 ms, ma è ancora problemi di prestazioni, potrebbe esserci un problema con l'app o un sistema operativo in che uso.

Per altre informazioni su come diagnosticare i problemi correlati all'app, vedere [contatori delle prestazioni ritardo Input utente](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters).

Poiché RDP supporta un tempo medio di codifica di ms 33, viene supportata una frequenza dei fotogrammi di input fino a 30 fotogrammi al secondo. Si noti che la frequenza massima supportata è ms 33. In molti casi, la frequenza dei fotogrammi hanno a che l'utente sarà inferiore, a seconda della frequenza viene fornito un frame RDP dall'origine. Ad esempio, le attività come guardare un video richiedono una frequenza dei fotogrammi di input completo pari a 30 fotogrammi al secondo, ma meno attività di elevati in termini di risorse, ad esempio raramente la modifica di un documento di word non richiedono tale frequenza dei fotogrammi di input al secondo per un'esperienza utente soddisfacente.

Usare il contatore di qualità dei fotogrammi per diagnosticare i problemi di qualità di frame. Questo contatore indica la qualità del frame di output come percentuale della qualità del fotogramma di origine. La perdita di qualità potrebbe essere dovuto a RemoteFX, o può essere intrinseca all'origine dei grafici. Se RemoteFX ha causato la perdita di qualità, il problema potrebbe essere una mancanza di risorse di rete o del server per inviare contenuto ad alta fedeltà superiore.

## <a name="mitigation"></a>Mitigazione

Se le risorse del server causano un collo di bottiglia, provare una delle operazioni seguenti per migliorare le prestazioni:

- Ridurre il numero di sessioni per ogni host.
- Aumentare la memoria e risorse nel server di calcolo.
- Eliminare la risoluzione della connessione.

Se le risorse di rete stanno provocando un collo di bottiglia, provare una delle operazioni seguenti per migliorare la disponibilità di rete per ogni sessione:

- Ridurre il numero di sessioni per ogni host.
- Eliminare la risoluzione della connessione.
- Usare una maggiore larghezza di banda rete.

Se le risorse del client stanno provocando un collo di bottiglia, eseguire una o entrambe le operazioni seguenti per migliorare le prestazioni:

- Installare il client Desktop remoto più recente.
- Aumentare la memoria e risorse di calcolo su computer client.

> [!NOTE]
> Il contatore Frames/Second origine non è attualmente supportata. Per ora, il contatore Frames/Second origine verrà sempre impostato su 0.

## <a name="next-steps"></a>Passaggi successivi

- Per creare una macchina virtuale di Azure ottimizzate per la GPU, vedere [configurare l'accelerazione GPU (unit) per l'ambiente di anteprima di Desktop virtuale Windows per l'elaborazione grafica](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu).
- Per una panoramica delle tracce di escalation e risoluzione dei problemi, vedere [risoluzione dei problemi di supporto, commenti e suggerimenti e panoramica](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview).
- Per altre informazioni sul servizio di anteprima, vedere [ambiente di anteprima di Desktop Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
