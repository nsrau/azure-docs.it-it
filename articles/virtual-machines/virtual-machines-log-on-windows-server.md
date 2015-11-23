<properties
	pageTitle="Come accedere a una VM | Microsoft Azure"
	description="Usare il portale di Azure per accedere a una macchina virtuale di Windows creata con il modello di distribuzione classico."
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
	ms.date="10/05/2015"
	ms.author="cynthn"/>


# Accedere a una macchina virtuale di Windows tramite il portale di Azure



Nel portale di Azure, si utilizza il pulsante **Connetti** per avviare una sessione di Desktop remoto e accedere a una macchina virtuale Windows.

Si desidera effettuare la connessione a una macchina virtuale Linux? Vedere [Come accedere a una macchina virtuale che esegue Linux](virtual-machines-linux-how-to-log-on.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


## Video con la procedura dettagliata

Di seguito è riportata una procedura video dettagliata dei passaggi in questa esercitazione. Vengono inoltre descritti gli endpoint e le porte pubbliche e private utilizzate per la connessione a una macchina virtuale di Windows in Azure.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## Connettersi alla macchina virtuale

1. Accedere al portale di Azure.

2. Fare clic su **Macchine virtuali**, quindi selezionare la macchina virtuale.

3. In fondo alla pagina, nella barra dei comandi, fare clic su **Connetti**.

	![Accesso alla macchina virtuale](./media/virtual-machines-log-on-windows-server/connectwindows.png)
	
> [AZURE.TIP]Se il pulsante di connessione non è disponibile, vedere i suggerimenti sulla risoluzione dei problemi alla fine di questo articolo.

## Accesso alla macchina virtuale

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Suggerimenti per la risoluzione dei problemi

Ecco alcuni aspetti da provare:

-	Se il pulsante **Connetti** non è attivo o si verificano altri problemi di connessione Desktop remoto, provare a reimpostare la configurazione. Dal dashboard della macchina virtuale, in **Riepilogo rapido**, fare clic su **Reimposta configurazione remota**.
-	Per problemi con la password, provare a reimpostarla. Dal dashboard della macchina virtuale, in **Riepilogo rapido**, fare clic su **Reimposta password**.

Se le precedenti istruzioni non sono sufficienti o non sono quelle necessarie, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-troubleshoot-remote-desktop-connections.md). In questo articolo viene illustrato come diagnosticare e risolvere i problemi più comuni.

<!---HONumber=Nov15_HO3-->