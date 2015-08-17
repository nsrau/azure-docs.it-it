<properties 
    pageTitle="Creare un'immagine di Azure RemoteApp basata su una macchina virtuale di Azure"
    description="Informazioni su come creare un'immagine per RemoteApp iniziando con una macchina virtuale di Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/28/2015" 
    ms.author="elizapo" />



# Creare un'immagine di Azure RemoteApp basata su una macchina virtuale di Azure

È possibile creare immagini di RemoteApp (che contengono le app condivise nella raccolta) da una macchina virtuale di Azure. Inoltre, è stata aggiunta un'immagine della macchina virtuale alla raccolta immagini della macchina virtuale di Azure che soddisfa tutti i requisiti per le immagini di RemoteApp: se si desidera, l'immagine della macchina virtuale può essere usata come punto di partenza per la propria macchina virtuale. È sufficiente cercare l'immagine "Host sessione Desktop remoto di Windows Server" nella libreria.

Sono necessari due passaggi: la creazione dell'immagine e il caricamento dalla raccolta di macchine virtuali di Azure ad Azure RemoteApp.

## Creare un'immagine personalizzata basata su una macchina virtuale di Azure

Usare questi passaggi per creare un'immagine basata su una macchina virtuale di Azure.

1. Creare una macchina virtuale di Azure. È possibile usare l'immagine “Host sessione Desktop remoto di Windows Server” dalla raccolta immagini della macchina virtuale di Azure. Questa immagine soddisfa tutti i requisiti dell'immagine modello di Azure RemoteApp. 

	Per i dettagli, vedere [Creare una macchina virtuale che esegue Windows](virtual-machines-windows-tutorial.md).

2. Connettersi alla macchina virtuale e installare e configurare le app da condividere mediante RemoteApp. Assicurarsi di eseguire tutte le configurazioni aggiuntive di Windows richieste dalle app.

	Per informazioni dettagliate, vedere [Come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-log-on-windows-server.md).

3. Se si usa l'immagine Host sessione Desktop remoto di Windows Server, viene fornito uno script di valutazione che assicura che la macchina virtuale soddisfi i prerequisiti di RemoteApp. Per eseguire lo script, fare doppio clic su **ValidateRemoteAppImage** nel desktop. Assicurarsi di risolvere tutti gli errori rilevati dallo script prima di procedere al passaggio successivo.

4. Generalizzare l'immagine con SYSPREP e acquisirla. Per istruzioni, vedere [Come acquisire una macchina virtuale Windows da usare come modello](../virtual-machines-capture-image-windows-server.md).

 

## Importare l'immagine nella raccolta immagini di RemoteApp

Usare questi passaggi per importare la nuova immagine RemoteApp:

1. Nella scheda **Immagini modello**:
	- Se non sono disponibili immagini, fare clic su **Caricare o importare un'immagine modello**. 
	- Se è presente almeno un'immagine, fare clic su **+** per aggiungere una nuova immagine.

2. Selezionare **Importare un'immagine dalla raccolta di macchine virtuali** e fare clic su **Avanti**.

3. Nella pagina successiva, selezionare l'immagine personalizzata dall'elenco e confermare che sono stati seguiti i passaggi elencati durante la creazione dell'immagine. Fare clic su **Next**.
4. Immettere un nome per la nuova immagine di RemoteApp e scegliere il percorso, quindi fare clic sul segno di spunta per avviare il processo di importazione.

> [AZURE.NOTE]È possibile importare le immagini da qualsiasi percorso di Azure supportato dalle macchine virtuali di Azure in qualsiasi percorso di Azure supportato da Azure RemoteApp. A seconda dei percorsi, l'importazione può richiedere fino a 25 minuti.

Ora è possibile creare la nuova raccolta, [cloud](remoteapp-create-cloud-deployment.md) o [ibrida](remoteapp-create-hybrid-deployment.md), in base alle proprie esigenze.
 

<!---HONumber=August15_HO6-->