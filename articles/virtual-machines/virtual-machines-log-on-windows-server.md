<properties 
	pageTitle="Accedere a una macchina virtuale che esegue Windows Server" 
	description="Informazioni su come usare il portale di gestione per accedere a una macchina virtuale che esegue Windows Server." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/12/2015" 
	ms.author="kathydav"/>


# Come accedere a una macchina virtuale che esegue Windows Server#

Per avviare una sessione di Desktop remoto si userà il pulsante **Connetti** nel portale di Azure. Per le macchine virtuali Linux, vedere [Come accedere a una macchina virtuale che esegue Linux](virtual-machines-linux-how-to-log-on.md).

## Come eseguire l'accesso

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Suggerimenti per la risoluzione dei problemi

Ecco alcuni aspetti da provare rapidamente:

Per problemi di connessione al Desktop remoto, provare a reimpostare la configurazione dal portale. Dal dashboard della macchina virtuale, in **Riepilogo rapido**, fare clic su **Reimposta configurazione remota**.

Per problemi con la password, provare a reimpostarla dal portale. Dal dashboard della macchina virtuale, in **Riepilogo rapido**, fare clic su **Reimposta password**.

Se questa procedura non funziona, sarà necessario eseguire altre procedure di risoluzione dei problemi. Per istruzioni, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-troubleshoot-remote-desktop-connections.md).
 
 

<!---HONumber=July15_HO2-->