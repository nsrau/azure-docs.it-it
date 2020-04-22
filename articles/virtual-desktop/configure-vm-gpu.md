---
title: Configurare GPU per Windows Virtual Desktop - AzureConfigure GPU for Windows Virtual Desktop - Azure
description: Come abilitare il rendering e la codifica con accelerazione GPU in Windows Virtual Desktop.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 8b675a78041b68210fa7583510582783c506c720
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767035"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Configurare l'accelerazione dell'unità di elaborazione grafica (GPU) per Windows Virtual Desktop

Windows Virtual Desktop supporta il rendering e la codifica con accelerazione GPU per migliorare le prestazioni e la scalabilità delle app. L'accelerazione GPU è particolarmente importante per le app che richiedono un uso intensivo della grafica.

Seguire le istruzioni in questo articolo per creare una macchina virtuale di Azure ottimizzata per GPU, aggiungerla al pool host e configurarla per l'uso dell'accelerazione GPU per il rendering e la codifica. In questo articolo si presuppone che sia già configurato un tenant di Windows Virtual Desktop.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Selezionare una dimensione della macchina virtuale di Azure ottimizzata per GPUSelect a GPU optimized Azure virtual machine size

Azure offre una serie di dimensioni di [macchine virtuali ottimizzate per GPU.](/azure/virtual-machines/windows/sizes-gpu) La scelta giusta per il pool host dipende da una serie di fattori, tra cui i carichi di lavoro specifici dell'app, la qualità dell'esperienza utente desiderata e i costi. In generale, le GPU più grandi e più capaci offrono una migliore esperienza utente a una determinata densità di utenti.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Creare un pool host, eseguire il provisioning della macchina virtuale e configurare un gruppo di appCreate a host pool, provision your virtual machine, and configure an app group

Creare un nuovo pool host usando una macchina virtuale delle dimensioni selezionate. Per istruzioni, vedere [Esercitazione: Creare un pool host con Azure Marketplace.](/azure/virtual-desktop/create-host-pools-azure-marketplace)

Windows Virtual Desktop supporta il rendering e la codifica con accelerazione GPU nei seguenti sistemi operativi:

* Windows 10 versione 1511 o successiva
* Windows Server 2016 o versioni successive

È inoltre necessario configurare un gruppo di app o utilizzare il gruppo di app desktop predefinito (denominato "Gruppo di applicazioni desktop") creato automaticamente quando si crea un nuovo pool host. Per istruzioni, vedere [Esercitazione: Gestire i gruppi](/azure/virtual-desktop/manage-app-groups)di app per Windows Virtual Desktop .

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Installare i driver grafici supportati nella macchina virtualeInstall supported graphics drivers in your virtual machine

Per sfruttare le funzionalità GPU delle macchine virtuali di Serie N di Azure in Windows Virtual Desktop, è necessario installare i driver di grafica appropriati. Seguire le istruzioni in [Sistemi operativi e driver supportati](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) per installare i driver dal fornitore di grafica appropriato, manualmente o usando un'estensione della macchina virtuale di Azure.Follow the instructions at Supported operating systems and drivers to install drivers from the appropriate graphics vendor, either manually or using an Azure VM extension.

Solo i driver distribuiti da Azure sono supportati per Windows Virtual Desktop. Inoltre, per le macchine virtuali di Azure con GPU NVIDIA, solo [i driver NVIDIA GRID](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) sono supportati per Windows Virtual Desktop.

Dopo l'installazione del driver, è necessario riavviare la macchina virtuale. Utilizzare la procedura di verifica descritta nelle istruzioni precedenti per verificare che i driver di grafica siano stati installati correttamente.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurare il rendering dell'app con accelerazione GPU

Per impostazione predefinita, le app e i desktop in esecuzione in configurazioni multisessione vengono sottoposti a rendering con la CPU e non sfruttano le GPU disponibili per il rendering. Configurare Criteri di gruppo per l'host di sessione per abilitare il rendering accelerato da GPU:

1. Connettersi al desktop della macchina virtuale usando un account con privilegi di amministratore locale.
2. Aprire il menu Start e digitare "gpedit.msc" per aprire l'Editor criteri di gruppo.
3. Spostarsi nell'albero in**Modelli amministrativi** > di **Configurazione** > computer Componenti amministrativi Componenti di**Windows Componenti** > **Desktop remoto di Servizi** > **Desktop remoto Host** > **sessione Host ambiente sessione remota**.
4. Selezionare il criterio **Utilizzare la scheda grafica predefinita hardware per tutte le sessioni** di Servizi Desktop remoto e impostare questo criterio su **Abilitato** per abilitare il rendering GPU nella sessione remota.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Configurare la codifica dei frame con accelerazione GPU

Desktop remoto codifica tutti gli elementi grafici sottoposti a rendering da app e desktop (sia con GPU che con CPU) per la trasmissione ai client Di Desktop remoto. Per impostazione predefinita, Desktop remoto non sfrutta le GPU disponibili per questa codifica. Configurare Criteri di gruppo per l'host di sessione per abilitare la codifica dei frame con accelerazione GPU. Continuando i passaggi precedenti:

1. Selezionare il criterio **Priorità H.264/AVC 444 Modalità grafica per** le connessioni Desktop remoto e impostare questo criterio su **Abilitato** per forzare il codec H.264/AVC 444 nella sessione remota.
2. Selezionare il criterio **Configurare la codifica hardware H.264/AVC per** le connessioni Desktop remoto e impostare questo criterio su **Abilitato** per abilitare la codifica hardware per AVC/H.264 nella sessione remota.

    >[!NOTE]
    >In Windows Server 2016, impostare l'opzione **Preferisci codifica hardware AVC** per **tentare sempre**.

3. Ora che i criteri di gruppo sono stati modificati, forzare un aggiornamento dei criteri di gruppo. Aprire il prompt dei comandi e digitare:

    ```batch
    gpupdate.exe /force
    ```

4. Disconnettersi dalla sessione di Desktop remoto.

## <a name="verify-gpu-accelerated-app-rendering"></a>Verificare il rendering dell'app con accelerazione GPU

Per verificare che le app utilizzino la GPU per il rendering, prova a eseguire una delle operazioni seguenti:

* Per le macchine virtuali di Azure con una `nvidia-smi` GPU NVIDIA, usare l'utilità come descritto in [Verificare l'installazione](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) del driver per verificare l'utilizzo della GPU durante l'esecuzione delle app.
* Nelle versioni supportate del sistema operativo, è possibile utilizzare Task Manager per verificare l'utilizzo della GPU. Seleziona la GPU nella scheda "Prestazioni" per vedere se le app utilizzano la GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Verificare la codifica dei fotogrammi con accelerazione GPU

Per verificare che Desktop remoto utilizzi la codifica accelerata da GPU:

1. Connettersi al desktop della macchina virtuale utilizzando il client Windows Virtual Desktop.
2. Avviare il Visualizzatore eventi e passare al nodo seguente: **Registri applicazioni e servizi** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **operativo**
3. Per determinare se viene utilizzata la codifica con accelerazione GPU, cerca l'ID evento 170. Se viene visualizzato il parametro "AVC hardware encoder enabled: 1", viene utilizzata la codifica GPU.
4. Per determinare se viene utilizzata la modalità AVC 444, cercare l'ID evento 162. Se viene visualizzato "AVC Disponibile: 1 Profilo iniziale: 2048", viene utilizzato AVC 444.

## <a name="next-steps"></a>Passaggi successivi

Queste istruzioni dovrebbero avere l'utente operativo con l'accelerazione GPU in un host di sessione (una macchina virtuale). Alcune considerazioni aggiuntive per abilitare l'accelerazione GPU in un pool host più grande:Some additional considerations for enabling GPU acceleration across a larger host pool:

* Prendere in considerazione [l'uso di un'estensione di macchina virtuale](/azure/virtual-machines/extensions/overview) per semplificare l'installazione e gli aggiornamenti dei driver in un numero di macchine virtuali. Usare l'estensione del driver [NVIDIA GPU](/azure/virtual-machines/extensions/hpccompute-gpu-windows) per le macchine virtuali con GPU NVIDIA e usare l'estensione del driver AMD GPU (disponibile a breve) per le macchine virtuali con GPU AMD.
* Prendere in considerazione l'utilizzo di Criteri di gruppo di Active Directory per semplificare la configurazione dei criteri di gruppo in un numero di macchine virtuali. Per informazioni sulla distribuzione di Criteri di gruppo nel dominio di Active Directory, vedere [Utilizzo degli oggetti Criteri di gruppo](https://go.microsoft.com/fwlink/p/?LinkId=620889).
