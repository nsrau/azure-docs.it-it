---
title: Creare e associare criteri degli endpoint di servizio - Portale di Azure
titlesuffix: Azure Virtual Network
description: In questo articolo viene illustrato come configurare e associare i criteri degli endpoint di servizio usando il portale di Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 09/18/2018
ms.author: kumud
ms.openlocfilehash: b1d2d04e74828323166810d93c52a60671bf71e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64710925"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Creare, modificare o eliminare i criteri degli endpoint di servizio usando il portale di Azure

I criteri degli endpoint di servizio consentono di filtrare il traffico della rete virtuale a specifiche risorse di Azure, tramite gli endpoint di servizio. Se non si ha familiarità con i criteri degli endpoint di servizio, vedere [Panoramica dei criteri degli endpoint di servizio](virtual-network-service-endpoint-policies-overview.md) per altre informazioni.

 In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un criterio di endpoint di servizio
> * Creare una definizione di criteri di endpoint di servizio
> * Creare una rete virtuale con una subnet
> * Associare un criterio di endpoint di servizio a una subnet

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedi ad Azure 

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Creare un criterio di endpoint di servizio

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Nel riquadro di ricerca digitare "criterio per l'endpoint servizio", selezionare **Service endpoint policy (Preview)** (Criterio per l'endpoint servizio - Anteprima) e quindi selezionare **Crea**.
3. Immettere o selezionare le informazioni seguenti in **Informazioni di base** 

   - Sottoscrizione: selezionare la propria sottoscrizione per il criterio.    
   - Gruppo di risorse: Selezionare **Crea nuovo** e immettere *myResourceGroup*.     
   - Nome: myEndpointPolicy
   - Località: Stati Uniti centro-occidentali     
 
   ![Creare le informazioni di base per un criterio di endpoint di servizio](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-startpane.PNG)
   
4. Immettere o selezionare le informazioni seguenti in **Definizioni dei criteri**

   - Fare clic su **+ Aggiungi una risorsa**, immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi fare clic su **Aggiungi**.  
   - Ambito: selezionare **Account singolo**, **Tutti gli account nella sottoscrizione** o **Tutti gli account nel gruppo di risorse**.    
   - Sottoscrizione: selezionare la propria sottoscrizione per l'account di archiviazione. I criteri e gli account di archiviazione possono essere in sottoscrizioni diverse.   
   - Gruppo di risorse: Selezionare un gruppo di risorse. Questa operazione è obbligatoria se l'ambito è impostato come "Tutti gli account nel gruppo di risorse" o "Account singolo".  
   - Risorsa: mystorageaccountportal    
   - Fare clic su **+ Aggiungi una risorsa** per continuare ad aggiungere altre risorse.
   
   ![Creare definizioni di criteri di endpoint di servizio](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-policydefinitionspane.PNG)
   
5. Facoltativo: immettere o selezionare le informazioni seguenti in **Tag**:
   
   - Chiave: selezionare la chiave per il criterio, ad esempio Rep     
   - Valore: immettere la coppia chiave-valore, ad esempio Finanza

6. Selezionare **Rivedi e crea**. Convalidare le informazioni e fare clic su **Crea**. Per apportare altre modifiche, fare clic su **Indietro**. 

   ![Convalide finali della creazione del criterio di endpoint di servizio](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-finalcreatereview.PNG)
  
 
## <a name="view-endpoint-policies"></a>Visualizzare i criteri degli endpoint 

1. Nella casella *Tutti i servizi* del portale, iniziare a digitare *criteri per l'endpoint servizio*. Selezionare **Service Endpoint Policies(Preview)** (Criteri per l'endpoint servizio - Anteprima).
2. In **Sottoscrizioni** selezionare la sottoscrizione e il gruppo di risorse, come illustrato nella figura seguente

   ![Visualizzare i criteri](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicies.PNG)
       
3. Selezionare i criteri e fare clic su **Definizioni dei criteri** per visualizzare o aggiungere altre definizioni dei criteri.

   ![Visualizzare le definizioni dei criteri](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicy-adddefinitions.PNG)

4. Selezionare **Subnet associate** per visualizzare le subnet a cui i criteri sono associati. Per associare i criteri a una subnet, fare clic su "Passare alla rete virtuale nella stessa area".

   ![Visualizzare le subnet associate](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-view-associatedsubnets.PNG)
 
## <a name="associate-a-policy-to-a-subnet"></a>Associare un criterio a una subnet

>[!WARNING] 
> Assicurarsi che tutte le risorse accessibili dalla subnet, per il servizio selezionato, vengano aggiunte al criterio prima di associare il criterio. Dopo aver associato il criterio, sarà consentito solo l'accesso alle risorse elencate nel criterio, per le aree degli endpoint per il servizio. 

Per poter associare un criterio a una subnet, è necessario creare una rete virtuale e una subnet, in modo che si possa poi associare il criterio alla subnet:

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Rete** e quindi **Rete virtuale**.
3. In **Crea rete virtuale** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi selezionare **Crea**:
   - Nome: myVirtualNetwork      
   - Spazio indirizzi: 10.0.0.0/16      
   - Sottoscrizione: Selezionare la propria sottoscrizione. Il criterio deve essere nella stessa sottoscrizione della rete virtuale     
   - Gruppo di risorse: Selezionare **Usa esistente** e quindi *myResourceGroup*     
   - Località: Stati Uniti centro-occidentali     
   - Nome subnet: private     
   - Intervallo di indirizzi: 10.0.0.0/24
     
4. Nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale iniziare a digitare *myVirtualNetwork*. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca.
5. In **IMPOSTAZIONI** selezionare **Subnet** e quindi selezionare **private**.
6. Come illustrato nella figura seguente, selezionare **Endpoint servizio**, **Microsoft.Storage**, **Criteri per l'endpoint servizio**, **myEndpointPolicy** e infine **Salva**:

   ![Associare un criterio](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-associatepolicies.PNG)

>[!WARNING] 
>L'accesso alle risorse del servizio in altre aree sarà consentito da questa subnet, in base ai gruppi di sicurezza di rete (NSG). Per limitare l'accesso alle sole aree degli endpoint, limitare i gruppi di sicurezza di rete solo al traffico del servizio nelle aree degli endpoint. Per altre informazioni su come creare i gruppi di sicurezza di rete con i tag del servizio per ogni area, vedere [Tag del servizio di Azure per i gruppi di sicurezza di rete](manage-network-security-group.md?toc=%2fcreate-a-security-rule%2f.json).

Nell'esempio seguente il gruppo di sicurezza di rete può accedere solo alle risorse di Archiviazione di Azure nelle aree WestCentralUS e WestUS2, con una regola "Rifiuta tutto" come regola con priorità più bassa.

![Rifiutare tutto il gruppo di sicurezza di rete](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-nsg-rules.PNG)


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato creato un criterio di endpoint di servizio per poi associarlo a una subnet. Per altre informazioni sui criteri degli endpoint di servizio, vedere [Panoramica dei criteri degli endpoint di servizio](virtual-network-service-endpoint-policies-overview.md).

