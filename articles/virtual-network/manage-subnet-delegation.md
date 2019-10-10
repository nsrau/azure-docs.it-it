---
title: Aggiungere o rimuovere una delega della subnet in una rete virtuale di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su come aggiungere o rimuovere una subnet delegata per un servizio in Azure in Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2019
ms.author: kumud
ms.openlocfilehash: 5fa340fc3c839d74f292f551b73184ea4df1c0f1
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72175960"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Aggiungere o rimuovere una delega della subnet

La delega di subnet offre esplicite autorizzazioni per creare le risorse specifiche del servizio nella subnet, tramite un identificatore univoco durante la distribuzione del servizio. Questo articolo descrive come aggiungere o rimuovere una subnet delegata per un servizio di Azure.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-the-virtual-network"></a>Creare la rete virtuale

In questa sezione viene creata una rete virtuale e la subnet che verrà successivamente delegata a un servizio di Azure.

1. Nella parte superiore sinistra della schermata, selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.
1. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | NOME | Immettere *MyVirtualNetwork*. |
    | Spazio degli indirizzi | Immettere *10.0.0.0/16*. |
    | Sottoscrizione | Selezionare la propria sottoscrizione.|
    | Gruppo di risorse | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare **OK**. |
    | Location | Selezionare **eastus**.|
    | Subnet - Nome | Immettere *mySubnet*. |
    | Subnet - Intervallo di indirizzi | Immettere *10.0.0.0/24*. |
    |||
1. Lasciare l'impostazione predefinita REST e quindi selezionare **Crea**.

## <a name="permissons"></a>Autorizzazioni

Se non è stata creata la subnet da delegare a un servizio di Azure, è necessaria l'autorizzazione seguente: `Microsoft.Network/virtualNetworks/subnets/write`.

Il ruolo predefinito [collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) contiene anche le autorizzazioni necessarie.

## <a name="delegate-a-subnet-to-an-azure-service"></a>Delegare una subnet a un servizio di Azure

In questa sezione si delega la subnet creata nella sezione precedente a un servizio di Azure.

1. Nella barra di ricerca del portale, immettere *myVirtualNetwork*. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca.
2. Nei risultati della ricerca selezionare *myVirtualNetwork*.
3. Selezionare **subnet**in **Impostazioni**e quindi fare clic su subnet **.**
4. *Nella pagina subnet* , per l'elenco **delega Subnet** , selezionare tra i servizi elencati in **Delega subnet a un servizio** (ad esempio, **Microsoft. DBforPostgreSQL/serversv2**).  

## <a name="remove-subnet-delegation-from-an-azure-service"></a>Rimuovere la delega della subnet da un servizio di Azure

1. Nella barra di ricerca del portale, immettere *myVirtualNetwork*. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca.
2. Nei risultati della ricerca selezionare *myVirtualNetwork*.
3. Selezionare **subnet**in **Impostazioni**e quindi fare clic su subnet **.**
4. Nella *pagina subnet,* per l'elenco **delega Subnet** , selezionare **nessuno** dai servizi elencati in **Delega subnet a un servizio**. 

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [gestire le subnet in Azure](virtual-network-manage-subnet.md).
