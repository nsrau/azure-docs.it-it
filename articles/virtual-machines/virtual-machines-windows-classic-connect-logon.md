<properties
	pageTitle="Accedere a una VM di Azure classico | Microsoft Azure"
	description="Usare il portale di Azure classico per accedere a una macchina virtuale di Windows creata con il modello di distribuzione classico."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="cynthn"/>


# Accedere a una macchina virtuale di Windows tramite il portale di Azure classico

Nel portale di Azure classico, si utilizza il pulsante **Connetti** per avviare una sessione di Desktop remoto e accedere a una macchina virtuale Windows.

Si desidera effettuare la connessione a una macchina virtuale Linux? Vedere [Come accedere a una macchina virtuale che esegue Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Vedere come [eseguire questi passaggi tramite il nuovo portale di Azure](virtual-machines-windows-connect-logon.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## Procedura dettagliata video

Di seguito è riportata una procedura video dettagliata dei passaggi in questa esercitazione. Vengono inoltre descritti gli endpoint e le porte pubbliche e private utilizzate per la connessione a una macchina virtuale di Windows in Azure.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## Connettersi alla macchina virtuale

1. Accedere al portale di Microsoft Azure classico.

2. Fare clic su **Macchine virtuali**, quindi selezionare la macchina virtuale.

3. Nella barra dei comandi in fondo alla pagina, fare clic su **Connetti**.

	![Accesso alla macchina virtuale](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)
	
> [AZURE.TIP] Se il **pulsante di connessione** non è disponibile, vedere i suggerimenti sulla risoluzione dei problemi alla fine di questo articolo.

## Accesso alla macchina virtuale

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Passaggi successivi

-	Se il pulsante **Connetti** non è attivo o si verificano altri problemi di connessione per il Desktop remoto, provare a reimpostare la configurazione. Dal dashboard della macchina virtuale, in **Riepilogo rapido**, fare clic su **Reimposta configurazione remota**.
-	Per problemi con la password, provare a reimpostarla. Dal dashboard della macchina virtuale, in **Riepilogo rapido**, fare clic su **Reimposta password**.

Se le precedenti istruzioni non sono sufficienti o non sono quelle necessarie, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-windows-troubleshoot-rdp-connection.md). In questo articolo viene illustrato come diagnosticare e risolvere i problemi più comuni.

<!---HONumber=AcomDC_0824_2016-->