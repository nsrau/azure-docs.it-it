<properties
	pageTitle="Accedere a una macchina virtuale di Windows Server | Microsoft Azure"
	description="Informazioni su come accedere a una macchina virtuale Windows Server tramite il portale di anteprima di Azure e il modello di distribuzione di gestione delle risorse."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>

# Come accedere a una macchina virtuale che esegue Windows Server 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrato l'accesso a una macchina virtuale Windows utilizzando il modello di distribuzione di gestione delle risorse. È anche possibile creare una risorsa con il [modello di distribuzione classica](virtual-machines-log-on-windows-server.md).

Si userà il pulsante **Connetti** nel portale di anteprima di Azure per avviare una sessione di Desktop Remoto. Effettuare la connessione alla macchina virtuale, quindi accedere al sistema.

## Connettersi alla macchina virtuale

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com/) se questa operazione non è già stata eseguita.

2.	Nel menu Hub fare clic su **Sfoglia**.

3.	Nel pannello di ricerca, scorrere verso il basso e fare clic su **Macchine virtuali**.

	![Cercare macchine virtuali](./media/virtual-machines-log-on-windows-server-preview/search-blade-preview-portal.png)

4.	Selezionare la macchina virtuale dall'elenco.

5. Nel pannello della macchina virtuale fare clic su **Connetti**.

	![Connettersi alla macchina virtuale](./media/virtual-machines-log-on-windows-server-preview/preview-portal-connect.png)

## Accesso alla macchina virtuale

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Risoluzione dei problemi

Se le istruzioni non sono sufficienti o non sono quelle necessarie, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-troubleshoot-remote-desktop-connections.md). In questo articolo viene illustrato come diagnosticare e risolvere i problemi più comuni.

<!---HONumber=Sept15_HO4-->