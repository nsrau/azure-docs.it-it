---
title: Reimpostare un&quot;istanza di gateway VPN di Azure per ristabilire tunnel IPsec | Documentazione Microsoft
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
ms.date: 02/07/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1c93a8900ea5fae8abe0d2d47f632a067736ac56
ms.openlocfilehash: 7aef9360ab341dd7d4932a6e9c2d8ed1d7bf1163


---
# <a name="reset-a-vpn-gateway"></a>Reimpostare un gateway VPN

La reimpostazione del gateway VPN di Azure è utile se si perde la connettività VPN cross-premise in uno o più tunnel VPN da sito a sito. In questa situazione tutti i dispositivi VPN funzionano correttamente, ma non sono in grado di stabilire tunnel IPsec con i gateway VPN di Azure. Questo articolo illustra in modo dettagliato la reimpostazione del gateway VPN di Azure. 

Ogni gateway VPN di Azure è un gateway di rete virtuale costituito da due istanze di macchine virtuali in esecuzione in una configurazione attiva/standby. Quando si reimposta il gateway, quest'ultimo viene riavviato e gli vengono riapplicate le configurazioni cross-premise. Il gateway mantiene l'indirizzo IP pubblico già disponibile. Non sarà quindi necessario aggiornare la configurazione del router VPN con un nuovo indirizzo IP pubblico per il gateway VPN di Azure.  

Dopo l'emissione del comando, l'istanza attualmente attiva del gateway VPN di Azure viene riavviata immediatamente. Si verificherà un breve intervallo durante il failover dall'istanza attiva (in fase di riavvio) all'istanza di standby. L'intervallo dovrebbe essere inferiore a un minuto.

Se la connessione non viene ripristinata dopo il primo riavvio, emettere di nuovo lo stesso comando per riavviare la seconda istanza della VM, ovvero il nuovo gateway attivo. Se vengono richiesti due riavvii uno dopo l'altro, il periodo necessario per il riavvio di entrambe le istanze della VM (attiva e di standby) sarà leggermente più lungo. Si verificherà quindi un intervallo più lungo nella connettività VPN, dai 2 ai 4 minuti, in attesa del completamento dei riavvii.

Dopo due riavvii, se si verificano ancora problemi di connettività cross-premise, aprire una richiesta di supporto dal portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare
Prima di reimpostare il gateway, verificare gli elementi chiave elencati di seguito per ogni tunnel VPN da sito a sito (S2S) IPsec. Eventuali mancate corrispondenze negli elementi provocherà la disconnessione dei tunnel VPN S2S. La verifica e la correzione delle configurazioni per i gateway locali e VPN di Azure evitano riavvii non necessari e interruzioni per le altre connessioni funzionanti nei gateway.

Verificare gli elementi seguenti prima di reimpostare il gateway:

* Gli indirizzi IP virtuali per il gateway VPN di Azure e il gateway VPN locale devono essere configurati correttamente nei criteri VPN di Azure e locali.
* La chiave precondivisa deve essere uguale nei gateway VPN di Azure e locali.
* Se si applica una configurazione IPsec/IKE specifica, ad esempio la crittografia, gli algoritmi hash e PFS (Perfect Forward Secrecy), assicurarsi che i gateway VPN di Azure e locali abbiano le stesse configurazioni.

## <a name="reset-a-vpn-gateway-using-the-azure-portal"></a>Reimpostare un gateway VPN nel portale di Azure

È possibile reimpostare un gateway VPN di Resource Manager tramite il portale di Azure. Se si desidera reimpostare un gateway classico, vedere la procedura di [PowerShell](#resetclassic).

### <a name="resource-manager-deployment-model"></a>Modello di distribuzione di Gestione risorse

1. Aprire il portale di Azure e passare al gateway di rete virtuale di Resource Manager che si desidera reimpostare.
2. Nel pannello per il gateway di rete virtuale fare clic su "Reimposta".

    ![Pannello di reimpostazione gateway VPN](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)

3. Nel pannello Reimposta, fare clic sul pulsante ![Pannello di reimpostazione gateway VPN](./media/vpn-gateway-howto-reset-gateway/reset-button.png) .


## <a name="reset-a-vpn-gateway-using-powershell"></a>Reimpostare un gateway VPN mediante PowerShell

### <a name="resource-manager-deployment-model"></a>Modello di distribuzione di Gestione risorse

È necessario scaricare la versione più recente dei cmdlet di PowerShell. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) . Il cmdlet di PowerShell per Resource Manager per la reimpostazione del gateway è `Reset-AzureRmVirtualNetworkGateway`. Nell'esempio seguente viene reimpostato il gateway VPN di Azure, "VNet1GW", nel gruppo di risorse "TestRG1".

    $gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
    Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

### <a name="a-nameresetclassicaclassic-deployment-model"></a><a name="resetclassic"></a>Modello di distribuzione classica

È necessario scaricare la versione più recente dei cmdlet di PowerShell. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) . Il cmdlet PowerShell per la reimpostazione di un gateway VPN di Azure è `Reset-AzureVNetGateway`. L'esempio seguente reimposta il gateway VPN di Azure per la rete virtuale denominata "ContosoVNet".

    Reset-AzureVNetGateway –VnetName “ContosoVNet” 

Risultato:

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK





<!--HONumber=Feb17_HO2-->


