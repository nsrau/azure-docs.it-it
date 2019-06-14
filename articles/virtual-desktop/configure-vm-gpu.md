---
title: Configurare GPU per l'anteprima legata ai Desktop virtuali Windows - Azure
description: Come abilitare con accelerazione GPU per il rendering e la codifica in anteprima di Desktop virtuale Windows.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: a6a67c89253a1b16f9266d7917655d1b1104022e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159571"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-preview"></a>Configurare l'accelerazione GPU (unit) per l'anteprima di Desktop virtuale Windows per l'elaborazione grafica

Anteprima di Desktop virtuale Windows supporta con accelerazione GPU per il rendering e la codifica per le prestazioni delle app migliorata e la scalabilità. L'accelerazione GPU è particolarmente importante per le app a elevato utilizzo di grafica.

Seguire le istruzioni in questo articolo per creare una macchina virtuale di Azure ottimizzate per la GPU, aggiungerlo al pool di host e configurarlo per usare l'accelerazione GPU per il rendering e la codifica. Questo articolo presuppone che si dispone già di un tenant di Windows Desktop virtuale configurato.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Selezionare una dimensione di macchina virtuale di Azure ottimizzate per la GPU

Azure offre numerosi [dimensioni delle macchine virtuali ottimizzate per la GPU](/azure/virtual-machines/windows/sizes-gpu). La scelta giusta per il pool di host dipende da diversi fattori, tra cui i carichi di lavoro di app specifica, qualità di esperienza utente e i costi. In generale, più grandi e più in grado di supportare GPU offrono un'esperienza utente migliore a densità un determinato utente.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Creare un pool di host, il provisioning della macchina virtuale e configurare un gruppo di app

Creare un nuovo pool di host usando una macchina virtuale della dimensione selezionata. Per istruzioni, vedere [esercitazione: Creare un pool di host con Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Windows Virtual Desktop Preview supporta con accelerazione GPU per il rendering e la codifica in sistemi operativi seguenti:

* Windows 10 versione 1511 o versioni successive
* Windows Server 2016 o versioni successive

È anche necessario configurare un gruppo di app, oppure usare l'app desktop gruppo predefinito (denominato "Gruppo di applicazioni Desktop") che viene creato automaticamente quando si crea un nuovo pool di host. Per istruzioni, vedere [esercitazione: Gestire i gruppi di app per Windows Virtual Desktop Preview](/azure/virtual-desktop/manage-app-groups).

>[!NOTE]
>Windows Virtual Desktop Preview supporta solo il tipo di gruppo "Desktop" app per i pool di host abilitate per GPU. I gruppi di App di tipo "RemoteApp" non sono supportati per i pool di host abilitate per GPU.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Installare i driver di grafica supportata nella macchina virtuale

Per sfruttare i vantaggi delle funzionalità GPU delle macchine virtuali serie N di Azure in anteprima di Desktop virtuale Windows, è necessario installare i driver di grafica NVIDIA. Seguire le istruzioni in [i driver GPU NVIDIA installare nelle macchine virtuali serie N che eseguono Windows](/azure/virtual-machines/windows/n-series-driver-setup) per installare i driver, manualmente o tramite il [estensione di Driver GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows).

Si noti che solo [driver NVIDIA GRID](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) distribuita da Azure sono supportati per l'anteprima di Desktop virtuale Windows.

Dopo l'installazione di driver, è necessario un riavvio della macchina virtuale. Usare i passaggi di verifica nelle istruzioni precedenti per verificare che i driver grafici sono stati installati correttamente.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurare per il rendering con accelerazione GPU di app

Per impostazione predefinita, le App e i desktop vengono eseguiti in configurazioni di sessione a più il rendering viene eseguito con l'utilizzo della CPU e sfrutta le GPU disponibili per il rendering. Configurare criteri di gruppo per l'host di sessione attivare il rendering con accelerazione GPU:

1. Connettersi al desktop della macchina virtuale usando un account con privilegi di amministratore locale.
2. Aprire l'avvio dal menu e il tipo "gpedit. msc" per aprire Editor criteri di gruppo.
3. Esplorare l'albero per **configurazione Computer** > **modelli amministrativi** > **i componenti di Windows**  >   **Servizi Desktop remoto** > **Host sessione Desktop remoto** > **sessione remota ambiente**.
4. Selezionare i criteri **usare l'adattatore di grafica hardware predefinito per tutte le sessioni di Servizi Desktop remoto** e impostare questi criteri su **abilitato** per attivare il rendering GPU nella sessione remota.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Configurare la codifica con accelerazione GPU di frame

Desktop remoto consente di codificare tutti gli elementi grafici viene eseguito il rendering da App e i desktop (se viene eseguito il rendering con GPU o CPU) per la trasmissione per i client Desktop remoto. Per impostazione predefinita, Desktop remoto non sfrutta le GPU disponibili per la codifica. Configurare criteri di gruppo per l'host di sessione abilitare la codifica con accelerazione GPU di frame. Continuare la procedura descritta sopra:

1. Selezionare i criteri **modalità di assegnare priorità video H.264/AVC 444 grafica per le connessioni Desktop remoto** e impostare questi criteri su **abilitato** per forzare il codec video H.264/AVC 444 nella sessione remota.
2. Selezionare i criteri **video H.264/AVC configurare hardware codifica per le connessioni Desktop remoto** e impostare questi criteri su **abilitato** per abilitare hardware codifica per AVC/H.264 nella sessione remota.

    >[!NOTE]
    >In Windows Server 2016, impostare l'opzione **preferisce codifica Hardware AVC** al **prova sempre**.

3. Ora che sono stati modificati i criteri di gruppo, forzare un aggiornamento di criteri di gruppo. Aprire il prompt dei comandi e digitare:

    ```batch
    gpupdate.exe /force
    ```

4. Disconnettersi dalla sessione Desktop remoto.

## <a name="verify-gpu-accelerated-app-rendering"></a>Verificare il rendering con accelerazione GPU di app

Per verificare che le app usano la GPU per il rendering, provare a eseguire una delle operazioni seguenti:

* Usare la `nvidia-smi` utilità come descritto in [verificare l'installazione del driver](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) da controllare per l'utilizzo della GPU durante l'esecuzione delle app.
* Nelle versioni di sistema operativo supportato, è possibile utilizzare Gestione attività per verificare la presenza di utilizzo della GPU. Selezionare la GPU nella scheda "Prestazioni" per vedere se le app sono la GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Verificare la codifica con accelerazione GPU di frame

Per verificare che Desktop remoto viene utilizzata la codifica con accelerazione GPU:

1. Connettersi al desktop della macchina virtuale usando il client di Desktop virtuali di Windows.
2. Avviare il Visualizzatore eventi e passare al nodo seguente: **Registri applicazioni e servizi** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS**  >  **Operativa**
3. Per determinare se viene utilizzata la codifica con accelerazione GPU, cercare l'evento ID 170. Se viene visualizzato "codificatore hardware AVC abilitata: viene utilizzato 1", quindi la codifica di GPU.
4. Per determinare se viene utilizzata la modalità AVC 444, individuare l'evento ID 162. Se viene visualizzato "AVC disponibile: 1 profilo iniziale: viene usato 2048" quindi 444 AVC.

## <a name="next-steps"></a>Passaggi successivi

Queste istruzioni devono avere è attivo e in esecuzione con accelerazione GPU in una macchina virtuale host singola sessione. Alcune considerazioni aggiuntive per abilitare l'accelerazione GPU in un pool più ampio di host:

* È consigliabile usare la [estensione di Driver GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) per semplificare l'installazione di driver e gli aggiornamenti in un numero di macchine virtuali.
* È consigliabile usare criteri di gruppo Active Directory per semplificare la configurazione dei criteri di gruppo in un numero di macchine virtuali. Per informazioni sulla distribuzione di criteri di gruppo nel dominio di Active Directory, vedere [funziona con oggetti Criteri di gruppo](https://go.microsoft.com/fwlink/p/?LinkId=620889).
