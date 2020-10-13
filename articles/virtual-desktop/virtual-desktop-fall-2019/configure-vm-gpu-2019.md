---
title: Configurare GPU per desktop virtuale Windows (classico)-Azure
description: Come abilitare il rendering e la codifica con accelerazione GPU in un desktop virtuale di Windows (classico).
author: gundarev
ms.topic: how-to
ms.date: 03/30/2020
ms.author: denisgun
ms.openlocfilehash: 32d5c280e80b2f21b30bb34a182070da51e21026
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008492"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-classic"></a>Configurare l'accelerazione GPU (Graphics Processing Unit) per desktop virtuale di Windows (versione classica)

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows (versione classica), che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se occorre gestire gli oggetti Azure Resource Manager di Desktop virtuale Windows, vedere [questo articolo](../configure-vm-gpu.md).

Desktop virtuale Windows supporta il rendering e la codifica con accelerazione GPU per migliorare le prestazioni e la scalabilità delle app. L'accelerazione GPU è particolarmente importante per le app a elevato utilizzo di grafica.

Per creare una macchina virtuale di Azure ottimizzata per la GPU, aggiungerla al pool di host e configurarla per l'uso dell'accelerazione GPU per il rendering e la codifica, seguire le istruzioni riportate in questo articolo. Questo articolo presuppone che sia già stato configurato un tenant di Desktop virtuale Windows.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Selezionare una dimensione della macchina virtuale di Azure ottimizzata per la GPU

Azure offre una serie di [dimensioni delle macchine virtuali ottimizzate per la GPU](/azure/virtual-machines/windows/sizes-gpu). La scelta corretta per il pool di host dipende da diversi fattori, tra cui i carichi di lavoro di app specifici, la qualità desiderata dell'esperienza utente e i costi. In generale, le GPU più grandi e più idonee offrono un'esperienza utente migliore a una determinata densità utente.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Creare un pool di host, effettuare il provisioning della macchina virtuale e configurare un gruppo di app

Creare un nuovo pool di host usando una macchina virtuale delle dimensioni selezionate. Per istruzioni, vedere [esercitazione: creare un pool di host con Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Desktop virtuale Windows supporta il rendering e la codifica con accelerazione GPU nei sistemi operativi seguenti:

* Windows 10 versione 1511 o successive
* Windows Server 2016 o versioni successive

È anche necessario configurare un gruppo di app o usare il gruppo di app desktop predefinito (denominato "gruppo di applicazioni desktop") che viene creato automaticamente quando si crea un nuovo pool di host. Per istruzioni, vedere [Esercitazione: Gestire gruppi di app per Desktop virtuale Windows](/azure/virtual-desktop/manage-app-groups).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Installare i driver grafici supportati nella macchina virtuale

Per sfruttare i vantaggi offerti dalle funzionalità della GPU delle macchine virtuali serie N di Azure in Desktop virtuale Windows, è necessario installare i driver grafici appropriati. Seguire le istruzioni in [Sistemi operativi e driver supportati](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) per installare i driver dal fornitore di grafica appropriato, manualmente o usando un'estensione della macchina virtuale di Azure.

Per Desktop virtuale Windows sono supportati solo i driver distribuiti da Azure. Per le macchine virtuali di Azure con GPU NVIDIA sono supportati solo [driver NVIDIA Grid](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) per desktop virtuale di Windows.

Dopo l'installazione, è necessario eseguire il riavvio della macchina virtuale. Per verificare che i driver grafici siano stati installati correttamente, usare i passaggi di verifica nelle istruzioni sopra indicate.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurare il rendering delle app con accelerazione GPU

Per impostazione predefinita, le app e i desktop eseguiti in configurazioni con più sessioni vengono sottoposti a rendering con la CPU e non sfruttano le GPU disponibili per il rendering. Configurare Criteri di gruppo per l'host di sessione per abilitare il rendering con accelerazione GPU:

1. Connettersi al desktop della macchina virtuale usando un account con privilegi di amministratore locale.
2. Aprire il menu Start e digitare "gpedit.msc" per aprire l'Editor Criteri di gruppo.
3. Spostarsi nell'albero **Configurazione computer** > **Modelli amministrativi** > **Componenti di Windows** > **Servizi Desktop remoto** > **Host sessione Desktop remoto** > **Ambiente sessione remota**.
4. Selezionare il criterio **Usa la scheda grafica predefinita per l'hardware per tutte le sessioni di Servizi Desktop remoto** e impostarlo su **Abilitato** per abilitare il rendering della GPU nella sessione remota.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Configurare la codifica del frame con accelerazione GPU

Desktop remoto codifica tutti i grafici sottoposti a rendering dalle app e dai desktop (se sottoposti a rendering con GPU o CPU) per la trasmissione ai client Desktop remoto. Per impostazione predefinita, Desktop remoto non sfrutta le GPU disponibili per questa codifica. Configurare Criteri di gruppo per l'host di sessione, per abilitare la codifica del frame con accelerazione GPU. Continuando la procedura precedente:

1. Selezionare il criterio **Assegna priorità alla modalità grafica H.264/AVC 444 per le connessioni Desktop remoto** e impostarlo su **Abilitato**, per forzare il codec H.264/AVC 444 nella sessione remota.
2. Selezionare il criterio **Configura codifica hardware H.264/AVC per le connessioni Desktop remoto** e impostarlo su **Abilitato** per abilitare la codifica hardware per AVC/H.264 nella sessione remota.

    >[!NOTE]
    >In Windows Server 2016, impostare l'opzione **Preferisci la codifica hardware AVC** su **Tenta sempre**.

3. Ora che i criteri di gruppo sono stati modificati, forzare un aggiornamento dei criteri di gruppo. Aprire il prompt dei comandi e digitare:

    ```batch
    gpupdate.exe /force
    ```

4. Disconnettersi dalla sessione Desktop remoto.

## <a name="verify-gpu-accelerated-app-rendering"></a>Verificare il rendering delle app con accelerazione GPU

Per verificare che le app stiano usando la GPU per il rendering, provare a eseguire una delle operazioni seguenti:

* Per le macchine virtuali di Azure con una GPU NVIDIA, usare l'utilità `nvidia-smi` come descritto in [Verificare l'installazione del driver](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) per verificare l'utilizzo della GPU durante l'esecuzione delle app.
* Nelle versioni supportate del sistema operativo, è possibile usare Gestione attività, per verificare l'utilizzo della GPU. Per vedere se le app utilizzano la GPU, selezionare la GPU nella scheda "Prestazioni".

## <a name="verify-gpu-accelerated-frame-encoding"></a>Verificare la codifica del frame con accelerazione GPU

Per verificare che Desktop remoto usi la codifica con accelerazione GPU:

1. Connettersi al desktop della macchina virtuale usando il client Desktop virtuale Windows.
2. Avviare il Visualizzatore eventi e passare al nodo seguente: **Registri applicazioni e servizi** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Operativo**
3. Per determinare se viene usata la codifica con accelerazione GPU, cercare l'ID evento 170. Se si visualizza "Codifica hardware AVC abilitata: 1 ", allora la codifica GPU viene usata.
4. Per determinare se viene usata la modalità AVC 444, cercare l'ID evento 162. Se viene visualizzato "AVC disponibile: 1 Profilo iniziale: 2048 ", allora AVC 444 viene usata.

## <a name="next-steps"></a>Passaggi successivi

Queste istruzioni dovrebbero essere in esecuzione con l'accelerazione GPU in un host di sessione (una macchina virtuale). Alcune considerazioni aggiuntive per l'abilitazione dell'accelerazione GPU in un pool di host più ampio:

* Per semplificare l'installazione e gli aggiornamenti dei driver in diverse macchine virtuali, prendere in considerazione l'uso di un'[estensione della macchina virtuale](/azure/virtual-machines/extensions/overview). Usare l'[estensione del driver GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) per le macchine virtuali con GPU NVIDIA e usare l'estensione del driver GPU AMD per le macchine virtuali con GPU AMD.
* Per semplificare la configurazione dei criteri di gruppo in diverse macchine virtuali, prendere in considerazione l'uso di Criteri di gruppo di Active Directory. Per informazioni sulla distribuzione di Criteri di gruppo nel dominio Active Directory, vedere [Working with Group Policy Objects](https://go.microsoft.com/fwlink/p/?LinkId=620889) (Utilizzo degli oggetti Criteri di gruppo).