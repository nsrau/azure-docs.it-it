<properties 
	pageTitle="Ridistribuzione delle macchine virtuali di Windows | Microsoft Azure" 
	description="Descrive come ridistribuire le macchine virtuali di Windows per mitigare i problemi di connessione RDP." 
	services="virtual-machines-windows" 
	documentationCenter="virtual-machines" 
	authors="iainfoulds" 
	manager="timlt"
	tags="azure-resource-manager,top-support-issue" 
/> 
	

<tags 
	ms.service="virtual-machines-windows" 
	ms.devlang="na" 
	ms.topic="support-article" 
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure" 
	ms.date="09/19/2016" 
	ms.author="iainfou" 
/> 


# Ridistribuzione della macchina virtuale su un nuovo nodo di Azure

Se si stanno riscontrando difficoltà nella risoluzione dei problemi relativi a connessione di desktop remoto (RDP) o accesso delle applicazioni a una macchina virtuale (VM) di Azure basata su Windows, potrebbe essere utile la ridistribuzione. Quando si ridistribuisce una VM, quest'ultima viene spostata su un nuovo nodo dell'infrastruttura di Azure, quindi viene riattivata conservando tutte le opzioni di configurazione e le risorse associate. In questo articolo viene illustrato come ridistribuire una VM con Azure PowerShell o il portale di Azure.

> [AZURE.NOTE] Dopo la ridistribuzione di una VM, il disco temporaneo viene perso e gli indirizzi IP dinamici associati all'interfaccia di rete virtuale vengono aggiornati.

## Uso di Azure PowerShell

Assicurarsi che sia installata la versione più recente di Azure PowerShell 1.x. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Utilizzare questo comando di Azure PowerShell per ridistribuire la macchina virtuale:

	Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## Passaggi successivi
In caso di difficoltà di connessione alla VM, è possibile trovare assistenza specifica sulla [risoluzione dei problemi delle connessioni RDP](virtual-machines-windows-troubleshoot-rdp-connection.md) o [passaggi dettagliati sulla risoluzione dei problemi RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md). Se non si riesce ad accedere a un'applicazione in esecuzione sulla VM, è possibile leggere l'articolo sulle [difficoltà nella risoluzione dei problemi relativi alle applicazioni](virtual-machines-windows-troubleshoot-app-connection.md).

<!---HONumber=AcomDC_0921_2016-->