---
title: 'Creare un host Bastion di Azure:portaleCreate an Azure Bastion host: portal'
description: In questo articolo viene illustrato come creare un host di Azure Bastion usando il portaleIn this article, learn how to create an Azure Bastion host using the portal
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 14a596d78fb1f560c62013e7e439ed60d3a29b8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366144"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Creare un host Bastion di Azure usando il portaleCreate an Azure Bastion host using the portal

Questo articolo illustra come creare un host di Azure Bastion usando il portale di Azure.This article shows you how to create an Azure Bastion host using the Azure portal. Dopo aver eseguito il provisioning del servizio Bastion di Azure nella rete virtuale, l'esperienza RDP/SSH senza interruzioni è disponibile per tutte le macchine virtuali nella stessa rete virtuale. La distribuzione di Azure Bastion viene effettuata per rete virtuale e non per sottoscrizione/account o macchina virtuale.

È possibile creare una nuova risorsa host bastion nel portale specificando tutte le impostazioni manualmente o usando le impostazioni che corrispondono a una macchina virtuale esistente. Per creare un host bastion usando le impostazioni della macchina virtuale, vedere l'articolo [della guida introduttiva.](quickstart-host-portal.md) Facoltativamente, è possibile usare Azure PowerShell per creare un host Bastion di Azure.Optionally, you can use [Azure PowerShell](bastion-create-host-powershell.md) to create an Azure Bastion host.

## <a name="before-you-begin"></a>Prima di iniziare

Bastion è disponibile nelle seguenti aree pubbliche di Azure:Bastion is available in the following Azure public regions:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Creare un host Bastion

Questa sezione consente di creare una nuova risorsa Bastion di Azure dal portale di Azure.This section helps you create a new Azure Bastion resource from the Azure portal.

1. Nel menu [del portale](https://portal.azure.com) di Azure o nella **home** page selezionare Crea **una risorsa.**

1. Nel campo Cerca nel *Marketplace* della pagina **Nuovo** digitare **Bastion**, quindi fare clic su **Invio** per visualizzare i risultati della ricerca.

1. Dai risultati, fare clic su **Bastione**. Verificare che l'editore sia *Microsoft* e che la categoria sia *Rete*.

1. Nella pagina **Bastione** fare clic su **Crea** per aprire la pagina **Crea bastione.**

1. Nella pagina **Crea bastione** configurare una nuova risorsa Bastion. Specificare le impostazioni di configurazione per la risorsa Bastion.

    ![creare un bastione](./media/bastion-create-host-portal/settings.png)

    * **Sottoscrizione:** la sottoscrizione di Azure che si vuole usare per creare una nuova risorsa Bastion.Subscription : The Azure subscription you want to create a new Bastion resource.
    * **Gruppo di risorse:** il gruppo di risorse di Azure in cui verrà creata la nuova risorsa Bastion.Resource Group: The Azure resource group in which the new Bastion resource will be created in. Se non si dispone di un gruppo di risorse esistente, è possibile crearne uno nuovo.
    * **Nome**: Il nome della nuova risorsa Bastion
    * **Area:** l'area pubblica di Azure in cui verrà creata la risorsa.
    * **Rete virtuale:** rete virtuale in cui verrà creata la risorsa Bastion. È possibile creare una nuova rete virtuale nel portale durante questo processo oppure usare una rete virtuale esistente. Se si usa una rete virtuale esistente, assicurarsi che la rete virtuale esistente disponga di spazio di indirizzi libero sufficiente per soddisfare i requisiti della subnet Bastion.
    * **Subnet:** subnet nella rete virtuale in cui verrà distribuita la nuova risorsa host Bastion. È necessario creare una subnet usando il valore name **AzureBastionSubnet**. Questo valore consente ad Azure di sapere in quale subnet distribuire le risorse Bastion.This value lets Azure know which subnet to deploy the Bastion resources to. Questo è diverso da una subnet del gateway. È necessario utilizzare una subnet di almeno /27 o superiore (/27, /26 e così via).
    
       Creare **AzureBastionSubnet** senza tabelle di route o deleghe. Se si usano gruppi di sicurezza di rete in **AzureBastionSubnet**, vedere l'articolo Usare i gruppi di [sicurezza di rete.](bastion-nsg.md)
    * **Indirizzo IP pubblico**: L'indirizzo IP pubblico della risorsa Bastion su cui si accederà RDP/SSH (tramite la porta 443). Creare un nuovo indirizzo IP pubblico o utilizzarne uno esistente. L'indirizzo IP pubblico deve trovarsi nella stessa area della risorsa Bastion che si sta creando.
    * **Nome indirizzo IP pubblico**: il nome della risorsa indirizzo IP pubblico.
    * **SKU indirizzo IP pubblico**: questa impostazione è prepopolata per impostazione predefinita su **Standard**. Azure Bastion usa/supporta solo lo SKU IP pubblico standard.
    * **Assegnazione**: Questa impostazione viene prepopolata per impostazione predefinita su **Static**.

1. Dopo aver specificato le impostazioni, fare clic su **Revisione e creazione**. In questo modo vengono convalidati i valori. Una volta superata la convalida, è possibile iniziare il processo di creazione.
1. Nella pagina **Crea bastione** fare clic su **Crea**.
1. Verrà visualizzato un messaggio che informa che la distribuzione è in corso. Lo stato verrà visualizzato in questa pagina quando vengono create le risorse. La creazione e la distribuzione della risorsa Bastion richiede circa 5 minuti.

## <a name="next-steps"></a>Passaggi successivi

* Leggi le domande frequenti sul [Bastion](bastion-faq.md) per ulteriori informazioni.

* Per usare i gruppi di sicurezza di rete con la subnet di Azure Bastion, vedere [Usare i gruppi](bastion-nsg.md)di sicurezza di rete.