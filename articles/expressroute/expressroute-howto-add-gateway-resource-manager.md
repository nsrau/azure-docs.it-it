<properties
   pageTitle="Aggiungere un Gateway VPN a una rete virtuale per ExpressRoute tramite Gestione risorse e PowerShell | Microsoft Azure"
   description="Questo articolo illustra l'aggiunta di un gateway VPN a una rete virtuale di Gestione risorse già creata per ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/26/2016"
   ms.author="cherylmc"/>

# Aggiungere un gateway VPN a una rete virtuale di Gestione risorse per una configurazione ExpressRoute 

In questo articolo saranno illustrati i passaggi per aggiungere una subnet gateway e creare un gateway VPN per una rete virtuale già esistente. I passaggi di questa configurazione sono specifici per le reti virtuali create sulla base del **modello di distribuzione di Gestione risorse** e che verranno usate in una configurazione ExpressRoute. Per altre informazioni sulla creazione di gateway per reti virtuali basate sul modello di distribuzione classico, vedere [Configurare una rete virtuale per ExpressRoute usando il portale classico](expressroute-howto-vnet-portal-classic.md).

## Prima di iniziare

Verificare di avere installato i cmdlet di Azure PowerShell richiesti per questa configurazione (1.0.2 o versione successiva). Se i cmdlet non sono stati installati, è necessario effettuare l’operazione prima di iniziare i passaggi di configurazione. Per altre informazioni sull’installazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## Verificare che il gateway sia stato creato

Utilizzare il comando seguente per verificare che il gateway sia stato creato.

	Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
	
## Passaggi successivi

Dopo avere creato il gateway VPN, è possibile collegare la rete virtuale a un circuito ExpressRoute. Vedere [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

<!---HONumber=AcomDC_0309_2016-->