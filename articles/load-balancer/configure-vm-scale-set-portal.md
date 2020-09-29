---
title: Configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente-portale di Azure
description: Informazioni su come configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente usando il portale di Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: cb7bfb9ac4b10b807ac186d087b0037953abd559
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439523"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente usando il portale di Azure

Questo articolo illustra come configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente. 

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure.
- Un servizio di bilanciamento del carico SKU standard esistente nella sottoscrizione in cui verrà distribuito il set di scalabilità di macchine virtuali.
- Una rete virtuale di Azure per il set di scalabilità di macchine virtuali.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Distribuire un set di scalabilità di macchine virtuali con il servizio di bilanciamento del carico esistente

In questa sezione verrà creato un set di scalabilità di macchine virtuali nel portale di Azure con un servizio di bilanciamento del carico di Azure esistente.

> [!NOTE]
> I passaggi seguenti presuppongono che una rete virtuale denominata **myVNet** e un servizio di bilanciamento del carico di Azure denominato **myLoadBalancer** siano stati distribuiti in precedenza.

1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa**  >  **calcolo**  >  **set di scalabilità di macchine virtuali** o cercare set di **scalabilità di macchine virtuali** nella ricerca nel Marketplace.

2. Selezionare **Create** (Crea).

3. In **creare un set di scalabilità di macchine virtuali**immettere o selezionare queste informazioni nella scheda **nozioni di base** :

    | Impostazione                        | valore                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Dettagli del progetto**            |                                                                                                       |
    | Subscription                   | Selezionare la sottoscrizione ad Azure                                                                        |
    | Gruppo di risorse                 | Selezionare Crea nuovo, immettere **myResourceGroup**, quindi selezionare OK oppure selezionare un gruppo di risorse esistente. |
    | **Dettagli del set di scalabilità**          |                                                                                                       |
    | Nome del set di scalabilità di macchine virtuali | Immettere **myVMSS**                                                                                      |
    | Region                         | Seleziona **Stati Uniti orientali 2**                                                                                    |
    | Zona di disponibilità              | Selezionare **Nessuno**                                                                                       |
    | **Dettagli istanza**           |                                                                                                       |
    | Image                          | Selezionare **Ubuntu Server 18,04 LTS**                                                                    |
    | Istanza Spot di Azure            | Selezionare **No**                                                                                         |
    | Dimensione                           | Lascia il valore predefinito                                                                                      |
    | **Account amministratore**      |                                                                                                       |
    | Tipo di autenticazione            | Seleziona **password**                                                                                   |
    | Nome utente                       | Immettere il nome utente dell'amministratore        |
    | Password                       | Immettere la password amministratore    |
    | Conferma password               | Immettere nuovamente la password amministratore |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Screenshot mostra la scheda creare i concetti di base del set di scalabilità di macchine virtuali." border="true":::

4. Selezionare la scheda **Rete**.

5. Immettere o selezionare queste informazioni nella scheda **rete** :

     Impostazione                           | valore                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Configurazione della rete virtuale** |                                                          |
    | Rete virtuale                   | Selezionare **myVNet** o la rete virtuale esistente.      |
    | **Bilanciamento del carico**                |                                                          |
    | Usare un servizio di bilanciamento del carico               | Selezionare **Sì**                                           |
    | **Impostazioni di bilanciamento del carico**       |                                                          |
    | Opzioni di bilanciamento del carico            | Selezionare il servizio di **bilanciamento del carico di Azure**                           |
    | Selezionare un servizio di bilanciamento del carico            | Selezionare **myLoadBalancer** o il servizio di bilanciamento del carico esistente |
    | Selezionare un pool back-end             | Selezionare **myBackendPool** o il pool back-end esistente.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Screenshot mostra la scheda creare i concetti di base del set di scalabilità di macchine virtuali." border="true":::

6. Selezionare la scheda **gestione** .

7. Nella scheda **gestione** impostare diagnostica di **avvio** su **disattivato**.

8. Selezionare il pulsante **Verifica blu + crea** .

9. Verificare le impostazioni e selezionare il pulsante **Crea** .

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato distribuito un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente.  Per altre informazioni sui set di scalabilità di macchine virtuali e sul servizio di bilanciamento del carico, vedere:

- [Informazioni su Azure Load Balancer](load-balancer-overview.md)
- [Informazioni sui set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/overview.md)
