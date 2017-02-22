---
title: Reimpostare un&quot;istanza di Gateway VPN di Azure per ristabilire tunnel IPsec - PowerShell | Documentazione Microsoft
description: Questo articolo descrive la reimpostazione di Gateway VPN di Azure per ristabilire i tunnel IPsec. L&quot;articolo riguarda i gateway VPN nei modelli di distribuzione classica e Resource Manager.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 79d77cb8-d175-4273-93ac-712d7d45b1fe
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 691afc6aca6efe074e76eab5129d109cb2e7163f
ms.openlocfilehash: ce6d77b8ad4a5b81f7a1237267accaa617f0dcf7


---
# <a name="reset-an-azure-vpn-gateway-using-powershell"></a>Reimpostare un gateway VPN di Azure mediante PowerShell

La reimpostazione del gateway VPN di Azure è utile se si perde la connettività VPN cross-premise in uno o più tunnel VPN da sito a sito. In questa situazione tutti i dispositivi VPN funzionano correttamente, ma non sono in grado di stabilire tunnel IPsec con i gateway VPN di Azure.

Questo articolo illustra in modo dettagliato la reimpostazione del gateway VPN di Azure con i cmdlet di PowerShell. Queste istruzioni includono sia il modello di distribuzione classica che il modello di distribuzione Resource Manager.

Ogni gateway VPN di Azure è un gateway di rete virtuale costituito da due istanze di macchine virtuali in esecuzione in una configurazione attiva/standby. Quando si usa il cmdlet PowerShell per reimpostare il gateway, quest'ultimo viene riavviato e gli vengono riapplicate le configurazioni cross-premise. Il gateway mantiene l'indirizzo IP pubblico già disponibile. Non sarà quindi necessario aggiornare la configurazione del router VPN con un nuovo indirizzo IP pubblico per il gateway VPN di Azure.  

Dopo l'emissione del comando, l'istanza attualmente attiva del gateway VPN di Azure viene riavviata immediatamente. Si verificherà un breve intervallo durante il failover dall'istanza attiva (in fase di riavvio) all'istanza di standby. L'intervallo dovrebbe essere inferiore a un minuto.

Se la connessione non viene ripristinata dopo il primo riavvio, emettere di nuovo lo stesso comando per riavviare la seconda istanza della VM, ovvero il nuovo gateway attivo. Se vengono richiesti due riavvii uno dopo l'altro, il periodo necessario per il riavvio di entrambe le istanze della VM (attiva e di standby) sarà leggermente più lungo. Si verificherà quindi un intervallo più lungo nella connettività VPN, dai 2 ai 4 minuti, in attesa del completamento dei riavvii.

Dopo due riavvii, se si verificano ancora problemi di connettività cross-premise, aprire una richiesta di supporto dal portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare
Prima di reimpostare il gateway, verificare gli elementi chiave elencati di seguito per ogni tunnel VPN da sito a sito (S2S) IPsec. Eventuali mancate corrispondenze negli elementi provocherà la disconnessione dei tunnel VPN S2S. La verifica e la correzione delle configurazioni per i gateway locali e VPN di Azure evitano riavvii non necessari e interruzioni per le altre connessioni funzionanti nei gateway.

Verificare gli elementi seguenti prima di reimpostare il gateway:

* Gli indirizzi IP virtuali per il gateway VPN di Azure e il gateway VPN locale devono essere configurati correttamente nei criteri VPN di Azure e locali.
* La chiave precondivisa deve essere uguale nei gateway VPN di Azure e locali.
* Se si applica una configurazione IPsec/IKE specifica, ad esempio la crittografia, gli algoritmi hash e PFS (Perfect Forward Secrecy), assicurarsi che i gateway VPN di Azure e locali abbiano le stesse configurazioni.

## <a name="reset---resource-manager"></a>Reimpostazione - Modello di distribuzione Resource Manager

È necessario scaricare la versione più recente dei cmdlet di PowerShell. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) . Il cmdlet di PowerShell per Resource Manager per la reimpostazione del gateway è `Reset-AzureRmVirtualNetworkGateway`. Nell'esempio seguente viene reimpostato il gateway VPN di Azure, "VNet1GW", nel gruppo di risorse "TestRG1".

    $gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
    Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

## <a name="reset---classic"></a>Reimpostazione - Modello di distribuzione classica

È necessario scaricare la versione più recente dei cmdlet di PowerShell. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) . Il cmdlet PowerShell per la reimpostazione di un gateway VPN di Azure è `Reset-AzureVNetGateway`. L'esempio seguente reimposta il gateway VPN di Azure per la rete virtuale denominata "ContosoVNet".

    Reset-AzureVNetGateway –VnetName “ContosoVNet” 

Risultato:

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere [PowerShell Service Management cmdlet reference](https://msdn.microsoft.com/library/azure/mt617104.aspx) (Informazioni di riferimento sui cmdlet di PowerShell per la gestione dei servizi) e [PowerShell Resource Manager cmdlet reference](http://go.microsoft.com/fwlink/?LinkId=828732) (Informazioni di riferimento sui cmdlet di PowerShell per Resource Manager).




<!--HONumber=Jan17_HO4-->


