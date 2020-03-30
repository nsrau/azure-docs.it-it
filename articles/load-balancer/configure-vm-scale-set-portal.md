---
title: Configurare il set di scalabilità di macchine virtuali con un servizio di bilanciamento del carico di Azure esistente - Portale di AzureConfigure virtual machine scale set with an existing Azure Load Balancer - Azure portal
description: Informazioni su come configurare un set di scalabilità di macchine virtuali con un servizio di bilanciamento del carico di Azure esistente.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: b2a83b226a4b2ddbbd554783de6a2b5c85c92f0a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349717"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Configurare un set di scalabilità di macchine virtuali con un servizio di bilanciamento del carico di Azure esistente usando il portale di AzureConfigure a virtual machine scale set with an existing Azure Load Balancer using the Azure portal

In questo articolo verrà illustrato come configurare un set di scalabilità di macchine virtuali con un servizio di bilanciamento del carico di Azure esistente. 

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure.
- Un servizio di bilanciamento del carico sku standard esistente nella sottoscrizione in cui verrà distribuito il set di scalabilità della macchina virtuale.
- Una rete virtuale di Azure per il set di scalabilità della macchina virtuale.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Distribuire il set di scalabilità di macchine virtuali con il servizio di bilanciamento del carico esistenteDeploy virtual machine scale set with existing load balancer

In questa sezione verrà creato un set di scalabilità di macchine virtuali nel portale di Azure con un servizio di bilanciamento del carico di Azure esistente.

> [!NOTE]
> I passaggi seguenti presuppongono che una rete virtuale denominata **myVNet** e un servizio di bilanciamento del carico di Azure denominato **myLoadBalancer** sia stato distribuito in precedenza.

1. Nella parte superiore sinistra dello schermo fare clic su **Crea un** > set di scalabilità**macchina di** **calcolo** > risorse o cercare Set di **scalabilità macchina virtuale** nella ricerca del marketplace.

2. Selezionare **Crea**.

3. In Crea un set di **scalabilità di macchine virtuali**immettere o selezionare queste informazioni nella scheda **Nozioni di base:In** Create a virtual machine scale set , enter or select this information in the Basics tab:

    | Impostazione                        | valore                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Dettagli del progetto**            |                                                                                                       |
    | Subscription                   | Selezionare la sottoscrizione ad Azure                                                                        |
    | Gruppo di risorse                 | Selezionare Crea nuovo, immettere **myResourceGroup**, quindi scegliere OK o selezionare un gruppo di risorse esistente. |
    | **Dettagli del set di scalabilitàScale set details**          |                                                                                                       |
    | Nome del set di scalabilità di macchine virtuali | Inserisci **myVMSS**                                                                                      |
    | Region                         | Selezionare **Stati Uniti orientali 2**                                                                                    |
    | Zona di disponibilità              | Selezionare **Nessuno**                                                                                       |
    | **Dettagli istanza**           |                                                                                                       |
    | Immagine                          | Selezionare **Ubuntu Server 18.04 LTS**                                                                    |
    | Istanza di Azure Spot            | Selezionare **No**.                                                                                         |
    | Dimensione                           | Lascia l'impostazione predefinita                                                                                      |
    | **Account amministratore**      |                                                                                                       |
    | Tipo di autenticazione            | Seleziona **password**                                                                                   |
    | Username                       | Inserisci il tuo nome utente amministratore        |
    | Password                       | Immettere la password di amministratore    |
    | Conferma password               | Immettere nuovamente la password di amministratore |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Creare il set di scalabilità delle macchine virtuali." border="true":::

4. Selezionare la scheda **Rete.Select** the Networking tab.

5. Immettere o selezionare queste informazioni nella scheda **Rete:**

     Impostazione                           | valore                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Configurazione della rete virtuale** |                                                          |
    | Rete virtuale                   | Selezionare **myVNet** o la rete virtuale esistente.      |
    | **Bilanciamento del carico**                |                                                          |
    | Usare un servizio di bilanciamento del caricoUse a load balancer               | Selezionare **Sì**                                           |
    | **Impostazioni di bilanciamento del carico**       |                                                          |
    | Opzioni di bilanciamento del carico            | Selezionare il servizio di bilanciamento del carico di **AzureSelect Azure load**                           |
    | Selezionare un servizio di bilanciamento del caricoSelect a load balancer            | Selezionare myLoadBalancer o il servizio di bilanciamento del carico esistenteSelect **myLoadBalancer** or your existing load balancer |
    | Selezionare un pool back-end             | Selezionare **myBackendPool** o il pool back-end esistente.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Creare il set di scalabilità delle macchine virtuali." border="true":::

6. Selezionare la scheda **Gestione.**

7. Nella scheda **Gestione** impostare **Diagnostica di avvio** su **Disattivato**.

8. Selezionare il pulsante blu **Revisione e creazione.**

9. Rivedere le impostazioni e selezionare il pulsante **Crea.**

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato distribuito un set di scalabilità di macchine virtuali con un servizio di bilanciamento del carico di Azure esistente.  Per altre informazioni sui set di scalabilità delle macchine virtuali e sul servizio di bilanciamento del carico, vedere:To learn more about virtual machine scale sets and load balancer, see:

- [Informazioni su Azure Load Balancer](load-balancer-overview.md)
- [Informazioni sui set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/overview.md)
