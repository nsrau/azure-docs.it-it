<properties
   pageTitle="Verificare una connessione al gateway | Microsoft Azure"
   description="Questo articolo illustra come verificare una connessione al gateway nel modello di distribuzione Resource Manager"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/03/2016"
   ms.author="cherylmc"/>  

# Verificare una connessione al gateway

È possibile verificare la connessione al gateway in diversi modi. Questo articolo illustra come verificare lo stato di una connessione al gateway di Resource Manager usando il portale di Azure e PowerShell.


## Prima di iniziare

Se si prevede di usare PowerShell, sarà necessario installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Per altre informazioni sull'uso dei cmdlet di Resource Manager, vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

1. Aprire la console di PowerShell e connettersi al proprio account.

		Login-AzureRmAccount

2. Controllare le sottoscrizioni per l'account.

		Get-AzureRmSubscription 

3. Specificare la sottoscrizione da usare.

		Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## Verifica della connessione


[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]


## Passaggi successivi

- È possibile aggiungere macchine virtuali alla rete virtuale. Per i passaggi, vedere [Creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

<!---HONumber=AcomDC_0810_2016-->