<properties
   pageTitle="Aggiungere un gateway VNet a una rete virtuale per ExpressRoute con Resource Manager e PowerShell | Microsoft Azure"
   description="Questo articolo spiega come aggiungere un gateway VNet a una rete virtuale di Resource Manager creata per ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/22/2016"
   ms.author="charwen"/>  

# Configurare un gateway di rete virtuale per ExpressRoute con Resource Manager e PowerShell


> [AZURE.SELECTOR]
- [PowerShell - Gestione risorse](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell - Classico](expressroute-howto-add-gateway-classic.md)


Questo articolo illustra le procedure di aggiunta,ridimensionamento e rimozione di un gateway di rete virtuale (VNet) per una rete virtuale già esistente. I passaggi di questa configurazione sono specifici per le reti virtuali create usando il **modello di distribuzione di Resource Manager** e che verranno usate in una configurazione ExpressRoute.

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Prima di iniziare

Verificare di avere installato i cmdlet di Azure PowerShell richiesti per questa configurazione (1.0.2 o versione successiva). Se i cmdlet non sono stati installati, è necessario effettuare l’operazione prima di iniziare i passaggi di configurazione. Per altre informazioni sull'installazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

	
## Passaggi successivi

Dopo avere creato il gateway VNet, è possibile collegare la rete virtuale a un circuito ExpressRoute. Vedere [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

<!---HONumber=AcomDC_0928_2016-->