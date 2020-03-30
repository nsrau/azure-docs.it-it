---
title: Creare e associare criteri degli endpoint di servizio - Portale di Azure
titlesuffix: Azure Virtual Network
description: In questo articolo viene illustrato come configurare e associare i criteri degli endpoint di servizio usando il portale di Azure.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: d26fd2fec5f9d5ab8e9d82ff2c6bd83b11c72e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651279"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Creare, modificare o eliminare i criteri degli endpoint di servizio usando il portale di Azure

I criteri degli endpoint di servizio consentono di filtrare il traffico della rete virtuale a specifiche risorse di Azure, tramite gli endpoint di servizio. Se non si ha familiarità con i criteri degli endpoint di servizio, vedere [Panoramica dei criteri degli endpoint di servizio](virtual-network-service-endpoint-policies-overview.md) per altre informazioni.

 In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un criterio di endpoint di servizio
> * Creare una definizione di criteri di endpoint di servizio
> * Creare una rete virtuale con una subnet
> * Associare un criterio di endpoint di servizio a una subnet

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Creare un criterio di endpoint di servizio

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Nel riquadro di ricerca digitare "criteri endpoint servizio" e selezionare **Criteri endpoint servizio** e quindi selezionare **Crea**.

![Creare criteri endpoint del servizioCreate service endpoint policy](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Immettere o selezionare le informazioni seguenti in **Informazioni di base** 

   - Sottoscrizione : Selezionare l'abbonamento per i criteriSubscription : Select your subscription for policy
   - Gruppo di risorse: selezionare **Crea nuovo** e immettere *myResourceGroup*
   - Nome: myEndpointPolicy
   - Località : Stati Uniti centrali
 
   ![Creare le informazioni di base per un criterio di endpoint di servizio](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Selezionare **Aggiungi** in **Risorse** e immettere o selezionare le informazioni seguenti in Aggiungere un riquadro **delle risorse**

   - Servizio : Solo Microsoft.Storage è disponibile con i criteri degli endpoint del servizioService : Only **Microsoft.Storage** is available with Service Endpoint Policies
   - Ambito : Selezionare un **account singolo**, Tutti gli account **nella sottoscrizione** e Tutti gli account nel gruppo **di risorse**
   - Sottoscrizione: selezionare la propria sottoscrizione per l'account di archiviazione. I criteri e gli account di archiviazione possono essere in sottoscrizioni diverse.
   - Gruppo di risorse: selezionare il gruppo di risorse. Questa operazione è obbligatoria se l'ambito è impostato come "Tutti gli account nel gruppo di risorse" o "Account singolo".  
   - Risorsa: selezionare la risorsa di Archiviazione di Azure nella sottoscrizione o nel gruppo di risorse selezionato
   - Fare clic sul pulsante **Aggiungi** in basso per completare l'aggiunta della risorsa

   ![Definizione dei criteri dell'endpoint del servizio - risorsaService endpoint policy definition - resource](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Aggiungere altre risorse ripetendo i passaggi precedenti in base alle esigenze

5. Facoltativo: immettere o selezionare le informazioni seguenti in **Tag**:
   
   - Chiave: selezionare la chiave per il criterio, ad esempio Rep.     
   - Valore: immettere la coppia chiave-valore, ad esempio Finanza.

6. Selezionare **Revisione e creazione**. Convalidare le informazioni e fare clic su **Crea**. Per apportare altre modifiche, fare clic su **Indietro**. 

   ![Convalide finali della creazione del criterio di endpoint di servizio](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Visualizzare i criteri degli endpoint 

1. Nella casella *Tutti i servizi* del portale, iniziare a digitare *criteri per l'endpoint servizio*. Selezionare **Criteri endpoint servizio**.
2. In **Sottoscrizioni** selezionare la sottoscrizione e il gruppo di risorse, come illustrato nella figura seguente

   ![Visualizzare i criteri](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Selezionare i criteri e fare clic su **Definizioni dei criteri** per visualizzare o aggiungere altre definizioni dei criteri.

   ![Visualizzare le definizioni dei criteri](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Selezionare **Subnet associate** per visualizzare le subnet a cui i criteri sono associati. Se non è ancora associata alcuna subnet, seguire le istruzioni nel passaggio successivo.

   ![Subnet associate](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Associare un criterio a una subnet

>[!WARNING] 
> Assicurarsi che tutte le risorse a cui si accede dalla subnet vengano aggiunte alla definizione dei criteri prima di associare il criterio alla subnet specificata. Una volta che il criterio è associato, sarà consentito solo l'accesso alle risorse *elencate consentite* sugli endpoint del servizio. 
>
> Assicurarsi inoltre che nella subnet associata ai criteri dell'endpoint del servizio non esistano servizi di Azure gestiti

- Prima di poter associare un criterio a una subnet, è necessario creare una rete virtuale e una subnet. Per informazioni su questo articolo, fare riferimento all'articolo [Creare una rete virtuale.](./quick-create-portal.md)

- Dopo aver configurato la rete virtuale e la subnet, è necessario configurare gli endpoint del servizio di rete virtuale per Archiviazione di Azure.After you have the virtual network and subnet are setup, you need to configure Virtual Network Service Endpoints for Azure Storage. Nel pannello Rete virtuale selezionare **Endpoint del servizio**e nel riquadro successivo selezionare **Microsoft.Storage** e in **Subnet** selezionare la rete virtuale o la subnet desiderata

- A questo punto, è possibile scegliere di selezionare i criteri dell'endpoint del servizio dall'elenco a discesa nel riquadro precedente se sono già stati creati criteri dell'endpoint del servizio prima di configurare l'endpoint del servizio per la subnet, come illustrato di seguito

    ![Associare la subnet durante la creazione dell'endpoint del servizioAssociate subnet while creating service endpoint](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- OPPURE se si associano i criteri degli endpoint del servizio dopo che gli endpoint del servizio sono già configurati, è possibile scegliere di associare la subnet dal pannello Criteri endpoint servizio passando al riquadro **Subnet associate** come illustrato di seguito

    ![Associare la subnet tramite SEP](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>L'accesso alle risorse di Archiviazione di Azure in tutte le aree verrà limitato in base ai criteri dell'endpoint del servizio da questa subnet.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato creato un criterio di endpoint di servizio per poi associarlo a una subnet. Per altre informazioni sui criteri degli endpoint di servizio, vedere [Panoramica dei criteri degli endpoint di servizio](virtual-network-service-endpoint-policies-overview.md).
