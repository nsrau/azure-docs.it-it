<properties
	pageTitle="Connettersi a una macchina virtuale Windows Server | Microsoft Azure"
	description="Informazioni su come connettersi e accedere a una VM Windows Server usando il portale di Azure e il modello di distribuzione Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/28/2016"
	ms.author="cynthn"/>

# Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows Server 


Per avviare una sessione di Desktop remoto (RDP), si userà il pulsante **Connetti** nel portale di Azure. Effettuare la connessione alla macchina virtuale, quindi accedere al sistema.

## Connettersi alla macchina virtuale

1. Accedere al [portale di Azure](https://portal.azure.com/), se questa operazione non è già stata eseguita.

2.	Scegliere **Macchine virtuali** dal menu Hub.

3.	Selezionare la macchina virtuale dall'elenco.

4. Nel pannello della macchina virtuale fare clic su **Connetti**.

	![Screenshot del portale di Azure che illustra come connettersi alla VM.](./media/virtual-machines-windows-connect-logon/connect.png)

## Accesso alla macchina virtuale

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]


## Passaggi successivi

In caso di problemi quando si cerca di eseguire la connessione, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-windows-troubleshoot-rdp-connection.md). In questo articolo viene illustrato come diagnosticare e risolvere i problemi più comuni.

<!---HONumber=AcomDC_0504_2016-->