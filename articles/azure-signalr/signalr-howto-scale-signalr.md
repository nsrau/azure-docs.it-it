---
title: Ridimensionare un'istanza del servizio Azure SignalR
description: Informazioni su come ridimensionare un'istanza del servizio Azure SignalR per aggiungere o ridurre la capacità, tramite portale di Azure o l'interfaccia della riga di comando di Azure.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: zhshang
ms.openlocfilehash: 0c4f91ee9cea5e8b13ecfedafffdc1715fc242c2
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464178"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Come ridimensionare un'istanza del servizio Azure SignalR?
Questo articolo illustra come ridimensionare l'istanza del servizio Azure SignalR. Esistono due scenari per il ridimensionamento, la scalabilità verticale e orizzontale.

* [Scalabilità verticale](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Ottieni altre unità, connessioni, messaggi e altro ancora. Per aumentare il livello di prestazioni, è possibile modificare il piano tariffario da gratuito a standard.
* [Scale out](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): aumentare il numero di unità SignalR. È possibile scalare in orizzontale fino a un massimo di 100 unità.

Sono necessari alcuni minuti per applicare le impostazioni di scalabilità. Non è necessario modificare il codice o ridistribuire l'applicazione server.

Per informazioni sui prezzi e le capacità del servizio Single SignalR, vedere [Dettagli prezzi del servizio Azure SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).  

> [!NOTE]
> Cambiando il servizio SignalR dal livello **gratuito** al livello **standard** o viceversa, l'indirizzo IP del servizio pubblico verrà modificato e richiede in genere 3-60 minuti per propagare la modifica ai server DNS sull'intera Internet. Il servizio potrebbe non essere raggiungibile prima che il DNS venga aggiornato. In genere, non è consigliabile modificare il piano tariffario troppo spesso.


## <a name="scale-on-azure-portal"></a>Scalabilità in portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) nel browser.

2. Nel menu a sinistra della pagina del servizio SignalR selezionare **Ridimensiona**.
   
3. Scegliere il piano tariffario e quindi fare clic su **Seleziona**. È necessario impostare il numero di unità per il livello **standard** .
   
    ![Scalabilità nel portale](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Fare clic su **Save**.

## <a name="scale-using-azure-cli"></a>Ridimensionare la cache tramite l'interfaccia della riga di comando di Azure

Questo script crea una nuova risorsa del servizio SignalR di livello **gratuito** e un nuovo gruppo di risorse e la scalabilità fino al livello **standard** . 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

Annotare il nome effettivo generato per il nuovo gruppo di risorse. Il nome del gruppo di risorse viene usato quando si vuole eliminare tutte le risorse di gruppo.

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>Confrontare i piani tariffari

Per informazioni dettagliate, ad esempio i messaggi e le connessioni inclusi per ogni piano tariffario, vedere [Dettagli prezzi del servizio SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).

Per una tabella di limiti, quote e vincoli del servizio in ogni livello, vedere [limiti del servizio SignalR](../azure-subscription-service-limits.md#azure-signalr-service-limits).

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato illustrato come ridimensionare l'istanza del servizio Single SignalR.

Sono supportati anche più endpoint per la scalabilità, il partizionamento orizzontale e scenari tra aree diverse.

> [!div class="nextstepaction"]
> [ridimensionare il servizio SignalR con più istanze](./signalr-howto-scale-multi-instances.md)
