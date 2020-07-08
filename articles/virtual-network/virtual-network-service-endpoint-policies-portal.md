---
title: Creare e associare criteri degli endpoint di servizio - Portale di Azure
titlesuffix: Azure Virtual Network
description: In questo articolo viene illustrato come configurare e associare i criteri degli endpoint di servizio usando il portale di Azure.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: bdf0e87c92a55d0dbb5bbe34334a6de4580cb350
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708179"
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
2. Nel riquadro di ricerca digitare "criteri endpoint di servizio" e selezionare **criteri endpoint servizio** e quindi selezionare **Crea**.

![Crea criterio endpoint servizio](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Immettere o selezionare le informazioni seguenti in **Informazioni di base** 

   - Sottoscrizione: selezionare la sottoscrizione per i criteri
   - Gruppo di risorse: selezionare **Crea nuovo** e immettere *myResourceGroup*
   - Nome: myEndpointPolicy
   - Località: Stati Uniti centrali
 
   ![Creare le informazioni di base per un criterio di endpoint di servizio](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Selezionare **+ Aggiungi** in **risorse** e immettere o selezionare le informazioni seguenti nel riquadro **Aggiungi una risorsa**

   - Servizio: solo **Microsoft. storage** è disponibile con i criteri dell'endpoint di servizio
   - Ambito: selezionare un **singolo account**, **tutti gli account nella sottoscrizione** e **tutti gli account nel gruppo di risorse**
   - Sottoscrizione: selezionare la propria sottoscrizione per l'account di archiviazione. I criteri e gli account di archiviazione possono essere in sottoscrizioni diverse.
   - Gruppo di risorse: selezionare il gruppo di risorse. Questa operazione è obbligatoria se l'ambito è impostato come "Tutti gli account nel gruppo di risorse" o "Account singolo".  
   - Risorsa: selezionare la risorsa di archiviazione di Azure nella sottoscrizione o nel gruppo di risorse selezionato
   - Fare clic sul pulsante **Aggiungi** nella parte inferiore per completare l'aggiunta della risorsa

   ![Definizione dei criteri dell'endpoint di servizio-risorsa](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Aggiungere altre risorse ripetendo i passaggi precedenti in base alle esigenze

5. Facoltativo: immettere o selezionare le informazioni seguenti in **Tag**:
   
   - Chiave: selezionare la chiave per il criterio, ad esempio Rep.     
   - Valore: immettere la coppia chiave-valore, ad esempio Finanza.

6. Selezionare **Rivedi e crea**. Convalidare le informazioni e fare clic su **Crea**. Per apportare altre modifiche, fare clic su **Indietro**. 

   ![Convalide finali della creazione del criterio di endpoint di servizio](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Visualizzare i criteri degli endpoint 

1. Nella casella *Tutti i servizi* del portale, iniziare a digitare *criteri per l'endpoint servizio*. Selezionare **criteri endpoint di servizio**.
2. In **Sottoscrizioni** selezionare la sottoscrizione e il gruppo di risorse, come illustrato nella figura seguente

   ![Visualizzare i criteri](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Selezionare i criteri e fare clic su **Definizioni dei criteri** per visualizzare o aggiungere altre definizioni dei criteri.

   ![Visualizzare le definizioni dei criteri](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Selezionare **Subnet associate** per visualizzare le subnet a cui i criteri sono associati. Se non è ancora associata alcuna subnet, seguire le istruzioni nel passaggio successivo.

   ![Subnet associate](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Associare un criterio a una subnet

>[!WARNING] 
> Assicurarsi che tutte le risorse a cui si accede dalla subnet vengano aggiunte alla definizione dei criteri prima di associare i criteri alla subnet specificata. Dopo aver associato il criterio, sarà consentito l'accesso solo alle risorse *elencate in Consenti* sugli endpoint di servizio. 
>
> Assicurarsi inoltre che non esistano servizi di Azure gestiti nella subnet associata ai criteri dell'endpoint di servizio

- Prima di poter associare un criterio a una subnet, è necessario creare una rete virtuale e una subnet. Per informazioni, vedere l'articolo [creare una rete virtuale](./quick-create-portal.md) .

- Dopo aver configurato la rete virtuale e la subnet, è necessario configurare gli endpoint del servizio rete virtuale per archiviazione di Azure. Nel pannello rete virtuale selezionare **endpoint servizio**e nel riquadro successivo selezionare **Microsoft. storage** e in **subnet** selezionare la VNet o la subnet desiderata

- A questo punto, è possibile scegliere di selezionare i criteri dell'endpoint di servizio dall'elenco a discesa nel riquadro precedente se sono già stati creati i criteri dell'endpoint di servizio prima di configurare l'endpoint di servizio per la subnet, come illustrato di seguito.

    ![Associa subnet durante la creazione dell'endpoint servizio](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- In alternativa, se si associano i criteri dell'endpoint di servizio dopo aver configurato gli endpoint di servizio, è possibile scegliere di associare la subnet all'interno del pannello criteri dell'endpoint di servizio passando al riquadro **subnet associato** come illustrato di seguito.

    ![Associa subnet tramite SEP](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>L'accesso alle risorse di archiviazione di Azure in tutte le aree verrà limitato in base ai criteri dell'endpoint di servizio da questa subnet.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato creato un criterio di endpoint di servizio per poi associarlo a una subnet. Per altre informazioni sui criteri degli endpoint di servizio, vedere [Panoramica dei criteri degli endpoint di servizio](virtual-network-service-endpoint-policies-overview.md).
