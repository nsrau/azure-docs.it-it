---
title: Diagnosi dei problemi di prestazioni della grafica in Desktop remoto-Azure
description: Questo articolo descrive come usare i contatori grafici RemoteFX nelle sessioni Remote Desktop Protocol per diagnosticare problemi di prestazioni con la grafica nel desktop virtuale di Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: v-chjenk
ms.openlocfilehash: 8cd24861b9d7432a582d1b635b8ffcf0d8d2b9e6
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233630"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnosticare problemi di prestazioni grafica in Desktop remoto

Per diagnosticare problemi di qualità dell'esperienza con le sessioni remote, i contatori sono stati forniti nella sezione grafica RemoteFX di performance monitor. Questo articolo consente di individuare e correggere i colli di bottiglia delle prestazioni correlati alla grafica durante le sessioni di Remote Desktop Protocol (RDP) usando questi contatori.

## <a name="find-your-remote-session-name"></a>Trovare il nome della sessione remota

È necessario il nome della sessione remota per identificare i contatori delle prestazioni della grafica. Seguire le istruzioni riportate in questa sezione per identificare l'istanza di ogni contatore.

1. Aprire il prompt dei comandi di Windows dalla sessione remota.
2. Eseguire il comando **qwinsta** e trovare il nome della sessione.
    - Se la sessione è ospitata in una macchina virtuale (VM) con più sessioni: L'istanza di ogni contatore è configurata con il suffisso dello stesso numero che consente di suffissare il nome della sessione, ad esempio "RDP-TCP 37".
    - Se la sessione è ospitata in una VM che supporta vGPU (Virtual Graphics Processing Unit): L'istanza di ogni contatore viene archiviata nel server anziché nella macchina virtuale. Le istanze del contatore includono il nome della macchina virtuale anziché il numero nel nome della sessione, ad esempio "Win8 Enterprise VM".

>[!NOTE]
> Mentre i contatori hanno RemoteFX nei nomi, includono anche la grafica di desktop remoto in scenari vGPU.

## <a name="access-performance-counters"></a>Contatori delle prestazioni di accesso

Dopo aver determinato il nome della sessione remota, seguire queste istruzioni per raccogliere i contatori delle prestazioni della grafica RemoteFX per la sessione remota.

1. Selezionare **Avvia** > strumentidi > amministrazione**monitoraggio prestazioni**.
2. Nella finestra di dialogo **Performance Monitor** espandere **strumenti di monitoraggio**, selezionare **Performance Monitor**e quindi selezionare **Aggiungi**.
3. Nella finestra di dialogo **Aggiungi contatori** , nell'elenco **contatori disponibili** , espandere la sezione per RemoteFX graphics.
4. Selezionare i contatori da monitorare.
5. Nell'elenco **istanze dell'oggetto selezionato** selezionare le istanze specifiche da monitorare per i contatori selezionati e quindi selezionare **Aggiungi**. Per selezionare tutte le istanze del contatore disponibili, selezionare **tutte le istanze**.
6. Dopo aver aggiunto i contatori, fare clic su **OK**.

I contatori delle prestazioni selezionati verranno visualizzati nella schermata Performance Monitor.

>[!NOTE]
>Ogni sessione attiva in un host dispone di una propria istanza di ogni contatore delle prestazioni.

## <a name="diagnose-issues"></a>Diagnosticare i problemi

I problemi di prestazioni correlati alla grafica in genere rientrano in quattro categorie:

- Frequenza minima di frame
- Blocchi casuali
- Latenza di input elevata
- Qualità del frame scadente

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Risoluzione di bassa frequenza di frame, blocchi casuali e latenza di input elevata

Controllare prima di tutto il contatore frame di output/secondo. Misura il numero di frame resi disponibili per il client. Se questo valore è inferiore al contatore frame di input/secondo, i frame verranno ignorati. Per identificare il collo di bottiglia, utilizzare i contatori dei frame ignorati/secondo.

Sono disponibili tre tipi di frame ignorati/secondo contatori:

- Frame ignorati/secondo (risorse del server insufficienti)
- Frame ignorati/secondo (risorse di rete insufficienti)
- Frame ignorati/secondo (risorse client insufficienti)

Un valore elevato per uno dei contatori ignorati/secondo implica che il problema è correlato alla risorsa che il contatore tiene traccia. Se, ad esempio, il client non decodifica e presenta frame con la stessa frequenza con cui il server fornisce i frame, il contatore frame ignorati al secondo (risorse client insufficienti) sarà elevato.

Se il contatore frame di output/secondo corrisponde al contatore frame di input/secondo, ma si nota comunque un ritardo o un blocco insolito, il tempo di codifica medio potrebbe essere il colpevole. La codifica è un processo sincrono che si verifica nel server nello scenario a sessione singola (vGPU) e nella macchina virtuale nello scenario con più sessioni. Il tempo di codifica medio dovrebbe essere inferiore a 33 ms. Se il tempo medio di codifica è inferiore a 33 MS ma si verificano ancora problemi di prestazioni, è possibile che si verifichi un problema con l'app o il sistema operativo in uso.

Per altre informazioni sulla diagnosi dei problemi correlati all'app, vedere [contatori delle prestazioni dei ritardi di input utente](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters).

Poiché RDP supporta un tempo di codifica medio di 33 ms, supporta una frequenza di fotogrammi di input fino a 30 frame al secondo. Si noti che 33 ms è la frequenza dei fotogrammi massima supportata. In molti casi, la frequenza dei fotogrammi riscontrata dall'utente sarà inferiore, a seconda della frequenza con cui un frame viene fornito a RDP dall'origine. Ad esempio, le attività come il controllo di un video richiedono una frequenza di fotogrammi di input completa di 30 fotogrammi al secondo, ma le attività a elevato utilizzo di calcolo, come la modifica non frequente di un documento, comportano un valore molto più basso per i fotogrammi di input al secondo senza riduzione delle prestazioni dell'utente qualità dell'esperienza.

### <a name="addressing-poor-frame-quality"></a>Risoluzione della scarsa qualità del frame

Usare il contatore qualità del frame per diagnosticare i problemi di qualità dei frame. Questo contatore esprime la qualità del frame di output come percentuale della qualità del frame di origine. La perdita di qualità può essere dovuta a RemoteFX o può essere insita nell'origine grafica. Se RemoteFX ha causato la perdita di qualità, il problema potrebbe essere dovuto alla mancanza di risorse di rete o del server per l'invio di contenuti con maggiore fedeltà.

## <a name="mitigation"></a>Mitigazione

Se le risorse del server causano il collo di bottiglia, provare uno degli approcci seguenti per migliorare le prestazioni:

- Ridurre il numero di sessioni per host.
- Aumentare la memoria e le risorse di calcolo nel server.
- Elimina la risoluzione della connessione.

Se le risorse di rete causano il collo di bottiglia, provare uno degli approcci seguenti per migliorare la disponibilità di rete per sessione:

- Ridurre il numero di sessioni per host.
- Usare una rete con larghezza di banda superiore.
- Elimina la risoluzione della connessione.

Se le risorse client causano il collo di bottiglia, provare uno degli approcci seguenti per migliorare le prestazioni:

- Installare il client di Desktop remoto più recente.
- Aumentare la memoria e le risorse di calcolo nel computer client.

> [!NOTE]
> Attualmente non è supportato il contatore frame di origine/secondo. Per il momento, il contatore frame di origine/secondo visualizzerà sempre 0.

## <a name="next-steps"></a>Passaggi successivi

- Per creare una macchina virtuale di Azure ottimizzata per la GPU, vedere [configurare l'accelerazione GPU (Graphics Processing Unit) per l'ambiente di anteprima del desktop virtuale di Windows](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu).
- Per una panoramica delle tracce di risoluzione dei problemi e di escalation, vedere [panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview).
- Per ulteriori informazioni sul servizio di anteprima, vedere [ambiente Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
