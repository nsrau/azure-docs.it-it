<properties
	pageTitle="Accedere a una macchina virtuale che esegue Windows Server"
	description="Informazioni su come usare il portale di Azure per accedere a una macchina virtuale che esegue Windows Server."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>


# Come accedere a una macchina virtuale che esegue Windows Server#

Si userà il pulsante **Connetti** nel portale di anteprima di Azure per avviare una sessione di Desktop Remoto. Effettuare la connessione alla macchina virtuale, quindi accedere al sistema.

Si desidera effettuare la connessione a una macchina virtuale Linux? Vedere [Come accedere a una macchina virtuale che esegue Linux](virtual-machines-linux-how-to-log-on.md).

## Connettersi alla macchina virtuale

Di seguito è riportata una procedura dettagliata dei passaggi in questa esercitazione.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]

1. Accedere al [portale di Azure](http://manage.windowsazure.com), se questa operazione non è già stata eseguita.

2. Fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale appropriata.

3. Nella barra dei comandi fare clic su **Connect**.

	![Accesso alla macchina virtuale](./media/virtual-machines-log-on-windows-server/connectwindows.png)

## Accesso alla macchina virtuale

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Suggerimenti per la risoluzione dei problemi

Ecco alcuni aspetti da provare rapidamente nel portale:

-	Per problemi di connessione al Desktop remoto, provare a reimpostare la configurazione. Dal dashboard della macchina virtuale, in **Riepilogo rapido**, fare clic su **Reimposta configurazione remota**.
-	Per problemi con la password, provare a reimpostarla. Dal dashboard della macchina virtuale, in **Riepilogo rapido**, fare clic su **Reimposta password**.

Se le precedenti istruzioni non sono sufficienti o non sono quelle necessarie, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-troubleshoot-remote-desktop-connections.md). In questo articolo viene illustrato come diagnosticare e risolvere i problemi più comuni.

<!---HONumber=Sept15_HO3-->