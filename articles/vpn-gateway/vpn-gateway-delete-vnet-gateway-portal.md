---
title: 'Eliminare un gateway di rete virtuale: Portale di Azure: Resource Manager | Microsoft Docs'
description: Eliminare un gateway di rete virtuale usando PowerShell nel modello di distribuzione Resource Manager.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b4949db62ccd31cf6ce3d19df5459367cac99b59
ms.lasthandoff: 03/31/2017


---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Eliminare un gateway di rete virtuale usando il portale
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Classica: PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Esistono due diversi approcci quando si desidera eliminare un gateway di rete virtuale per una configurazione di gateway VPN.

- Se si desidera eliminare tutto e ricominciare da capo, come nel caso di un ambiente di test, è possibile eliminare l'intero gruppo di risorse. Quando si elimina un gruppo di risorse, vengono eliminate tutte le risorse all'interno del gruppo. Questo metodo è consigliato solo se non si desidera mantenere nessuna delle risorse nel gruppo di risorse. Con questo approccio non è possibile eliminare in modo selettivo solo alcune risorse.

- Se si desidera mantenere alcune delle risorse nel gruppo di risorse, eliminare un gateway di rete virtuale diventa leggermente più complicato. Per poter eliminare il gateway di rete virtuale prima è necessario eliminare tutte le risorse che dipendono dal gateway. I passaggi variano a seconda del tipo di connessioni che sono state create e delle risorse dipendenti per ogni connessione.

##<a name="deletegw"></a>Eliminare un gateway VPN

Per eliminare un gateway di rete virtuale, è necessario innanzitutto eliminare ogni risorsa relativa al gateway di rete virtuale. Le risorse devono essere eliminate in un determinato ordine a causa delle dipendenze.

###<a name="step-1-navigate-to-the-virtual-network-gateway"></a>Passaggio 1: Passare al gateway di rete virtuale

Nel [portale di Azure](https://portal.azure.com) in **Tutte le risorse**, fare clic sul gateway di rete virtuale che si desidera eliminare. Il grafico di un gateway è:

![Individuare il gateway di rete virtuale](./media/vpn-gateway-delete-vnet-gateway-portal/gw.png)

###<a name="step-2-delete-connections"></a>Passaggio 2: Eliminare le connessioni

1. Nel pannello del gateway di rete virtuale fare clic su **Connessioni** per visualizzare tutte le connessioni del gateway.
2. Fare clic su **...** sulla riga del nome della connessione, quindi selezionare **Elimina** nell'elenco a discesa.
3. Fare clic su **Sì** per confermare l'eliminazione della connessione. Se si dispone di più connessioni, eliminarle tutte.

###<a name="step-3-delete-the-local-network-gateways"></a>Passaggio 3: Eliminare i gateway di rete locale
1. In **Tutte le risorse**, individuare i gateway di rete locale associati a ogni connessione. Il grafico di un gateway di rete locale è:

    ![Individuare il gateway di rete locale](./media/vpn-gateway-delete-vnet-gateway-portal/lng.png)
2. Nel pannello **Informazioni generali** del gateway di rete locale, fare clic su **Elimina**.

###<a name="step-4-delete-the-virtual-network-gateway"></a>Passaggio 4: Eliminare il gateway di rete virtuale
1. In **Tutte le risorse**, individuare il gateway di rete virtuale che si desidera eliminare.
2. Nel pannello **Informazioni generali**, fare clic su **Elimina**.

>[!NOTE]
> Se in aggiunta alla configurazione S2S si dispone di una configurazione P2S per questa rete virtuale, l'eliminazione del gateway di rete virtuale disconnette automaticamente tutti i client P2S senza alcun avviso.
>
>

###<a name="step-5-delete-the-public-ip-address-for-the-gateway"></a>Passaggio 5: Eliminare l'indirizzo IP pubblico del gateway

1. In **Tutte le risorse**, individuare l'indirizzo IP pubblico assegnato al gateway. Se il gateway di rete virtuale è attivo-attivo, compaiono due indirizzi IP pubblici. Il grafico di un indirizzo IP pubblico è:

    ![Indirizzo IP pubblico](./media/vpn-gateway-delete-vnet-gateway-portal/pip.png)

2. Nella pagina **Panoramica** dell'indirizzo IP pubblico fare clic su **Elimina** quindi su **Sì** per confermare.

###<a name="step-6-delete-the-gateway-subnet"></a>Passaggio 6: Eliminare la subnet del gateway

1. In **Tutte le risorse**, individuare la rete virtuale. 
2. Nel pannello **Subnet**, fare clic su **GatewaySubnet** quindi fare clic su **Elimina**. 
3. Fare clic su **Sì** per confermare l'eliminazione della subnet del gateway.

##<a name="deleterg"></a>Eliminare un gateway VPN eliminando il gruppo di risorse

Se non si ha il problema di dover conservare le risorse nel gruppo di risorse e si desidera solo ricominciare da capo, è possibile eliminare un intero gruppo di risorse. Questo è un modo rapido per rimuovere tutto. La procedura seguente si applica solo al modello di distribuzione di Azure Resource Manager.

1. In **Tutte le risorse**, individuare il gruppo di risorse e fare clic per aprire il pannello.
2. Fare clic su **Elimina**. Nel pannello Elimina visualizzare le risorse interessate. Assicurarsi che si desidera eliminare tutte le risorse. In caso contrario, usare i passaggi in [Eliminare un gateway VPN](#deletegw) all'inizio di questo articolo.
3. Per continuare, digitare il nome del gruppo di risorse che si desidera eliminare, quindi fare clic su **Elimina**.
