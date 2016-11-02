<properties
   pageTitle="Modificare i prefissi degli indirizzi IP del gateway di rete locale e l'indirizzo IP del gateway | Microsoft Azure"
   description="Questo articolo illustra in modo dettagliato la modifica dei prefissi degli indirizzi IP per il gateway di rete locale"
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
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# Modificare le impostazioni del gateway di rete locale usando PowerShell

In alcuni casi le impostazioni per il valore AddressPrefix o GatewayIPAddress del gateway di rete locale subiscono modifiche. Le istruzioni seguenti sono utili per la modifica delle impostazioni del gateway di rete locale. È anche possibile modificare queste impostazioni nel portale di Azure.

## Prima di iniziare
	
È necessario installare la versione più recente dei cmdlet di PowerShell per Gestione risorse di Azure. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## Per modificare i prefissi degli indirizzi IP

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## Per modificare l'indirizzo IP del gateway

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## Passaggi successivi

È possibile verificare la connessione al gateway. Vedere [Verificare una connessione al gateway](vpn-gateway-verify-connection-resource-manager.md).

<!---HONumber=AcomDC_0810_2016-->