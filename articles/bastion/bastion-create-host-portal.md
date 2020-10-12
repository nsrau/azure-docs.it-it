---
title: 'Creare un host Bastion di Azure: portale'
description: In questo articolo viene illustrato come creare un host di Azure Bastion, che fornisce la connettività RDP/SSH a tutte le macchine virtuali in una rete virtuale.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: 1e0bc9a17a12afac6d91867b19aba0f90b7f6edb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88270751"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Creare un host di Azure Bastion usando il portale

Questo articolo illustra come creare un host Bastion di Azure usando il portale di Azure. Dopo aver effettuato il provisioning del servizio Azure Bastion nella rete virtuale, l'esperienza RDP/SSH trasparente è disponibile per tutte le macchine virtuali nella stessa rete virtuale. La distribuzione di Azure Bastion viene effettuata per rete virtuale e non per sottoscrizione/account o macchina virtuale.

È possibile creare una nuova risorsa host Bastion nel portale specificando tutte le impostazioni manualmente oppure usando le impostazioni che corrispondono a una macchina virtuale esistente. Per creare un host Bastion usando le impostazioni della macchina virtuale, vedere l'articolo [introduttivo](quickstart-host-portal.md) . Facoltativamente, è possibile usare [Azure PowerShell](bastion-create-host-powershell.md) per creare un host Bastion di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Bastion è disponibile nelle aree pubbliche di Azure seguenti:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Creare un host bastion

Questa sezione consente di creare una nuova risorsa di Azure Bastion dalla portale di Azure.

1. Nel menu del [portale di Azure](https://portal.azure.com) o nella **home page** selezionare **Crea una risorsa**.

1. Nella **nuova** pagina, nel campo *Cerca nel Marketplace* digitare **Bastion**, quindi premere **invio** per ottenere i risultati della ricerca.

1. Nei risultati fare clic su **Bastion**. Verificare che l'editore sia *Microsoft* e che la categoria sia *Rete*.

1. Nella pagina **Bastion** fare clic su **Crea** per aprire la pagina **creare un Bastion** .

1. Nella pagina **creare un Bastion** configurare una nuova risorsa Bastion. Specificare le impostazioni di configurazione per la risorsa Bastion.

    ![creare un Bastion](./media/bastion-create-host-portal/settings.png)

    * **Sottoscrizione**: la sottoscrizione di Azure che si vuole usare per creare una nuova risorsa Bastion.
    * **Gruppo di risorse**: il gruppo di risorse di Azure in cui verrà creata la nuova risorsa Bastion. Se non si dispone di un gruppo di risorse esistente, è possibile crearne uno nuovo.
    * **Nome**: il nome della nuova risorsa Bastion
    * **Region**: area pubblica di Azure in cui verrà creata la risorsa.
    * **Rete virtuale**: la rete virtuale in cui verrà creata la risorsa Bastion. È possibile creare una nuova rete virtuale nel portale durante questo processo oppure usare una rete virtuale esistente. Se si usa una rete virtuale esistente, assicurarsi che la rete virtuale esistente disponga di spazio di indirizzi libero sufficiente per soddisfare i requisiti della subnet Bastion.
    * **Subnet**: la subnet nella rete virtuale in cui verrà distribuito il nuovo host Bastion. La subnet verrà dedicata all'host Bastion e deve essere denominata **AzureBastionSubnet**. Questa subnet deve essere almeno/27 o superiore.
    
       **AzureBastionSubnet** non supporta le [route definite dall'utente](../virtual-network/virtual-networks-udr-overview.md#custom-routes), ma supporta i [gruppi di sicurezza di rete](bastion-nsg.md).
    * **Indirizzo IP pubblico: Indirizzo**IP pubblico della risorsa Bastion a cui verrà eseguito l'accesso a RDP/SSH (sulla porta 443). Creare un nuovo indirizzo IP pubblico o usarne uno esistente. L'indirizzo IP pubblico deve trovarsi nella stessa area della risorsa Bastion che si sta creando.
    * **Nome indirizzo IP pubblico**: nome della risorsa indirizzo IP pubblico.
    * **Indirizzo IP pubblico SKU**: questa impostazione viene prepopolata per impostazione predefinita in **standard**. Azure Bastion USA/supporta solo lo SKU IP pubblico standard.
    * **Assegnazione**: questa impostazione viene prepopolata per impostazione predefinita su **static**.

1. Al termine della specifica delle impostazioni, fare clic su **Verifica + crea**. Vengono convalidati i valori. Al termine della convalida, è possibile avviare il processo di creazione.
1. Nella pagina **Crea Bastion** fare clic su **Crea**.
1. Verrà visualizzato un messaggio che informa che la distribuzione è in corso. Lo stato verrà visualizzato in questa pagina durante la creazione delle risorse. Per la creazione e la distribuzione della risorsa Bastion sono necessari circa 5 minuti.

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni, vedere le [domande frequenti su Bastion](bastion-faq.md) .

* Per usare i gruppi di sicurezza di rete con la subnet di Azure Bastion, vedere [Usare i gruppi di sicurezza di rete](bastion-nsg.md).
