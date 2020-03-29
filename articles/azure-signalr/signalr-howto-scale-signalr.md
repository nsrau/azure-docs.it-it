---
title: Ridimensionare un'istanza del servizio SignalR di AzureScale an instance of Azure SignalR Service
description: Informazioni su come ridimensionare un'istanza del servizio SignalR di Azure per aggiungere o ridurre la capacità tramite il portale di Azure o l'interfaccia della riga di comando di Azure.Learn how to scale an Azure SignalR Service instance to add or reduce capacity, through Azure portal or Azure CLI.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: zhshang
ms.openlocfilehash: c8d74342e624b837c7ee803a2bcdcc12a3fb814b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659288"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Come ridimensionare un'istanza del servizio SignalR di AzureHow to scale an Azure SignalR Service instance?
Questo articolo illustra come ridimensionare l'istanza del servizio SignalR di Azure.This article shows you how to scale your instance of Azure SignalR Service. Esistono due scenari per la scalabilità verticale, la scalabilità verticale e la scalabilità orizzontale.

* [Scalabilità verticale:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)ottieni più unità, connessioni, messaggi e altro ancora. È possibile aumentare le dimensioni modificando il piano tariffario da Gratuito a Standard.
* [Scalabilità orizzontale](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Aumentare il numero di unità SignalR. È possibile scalare orizzontalmente fino a 100 unità.

L'applicazione delle impostazioni di scala richiede alcuni minuti. In rari casi, la domanda potrebbe richiedere circa 30 minuti. Non richiedono la modifica del codice o la ridistribuzione dell'applicazione server.

Per informazioni sui prezzi e le capacità dei singoli servizi SignalR, vedere Dettagli sui prezzi del servizio SignalR di Azure.For information about the pricing and capacities of individual SignalR Service, see [Azure SignalR Service Pricing Details](https://azure.microsoft.com/pricing/details/signalr-service/).  

> [!NOTE]
> Modificando il servizio SignalR dal livello **Gratuito** al livello **Standard** o viceversa, l'IP del servizio pubblico verrà modificato e in genere sono necessari 30-60 minuti per propagare la modifica ai server DNS su tutta La rete Internet. Il servizio potrebbe non essere raggiungibile prima dell'aggiornamento del DNS. In genere non è consigliabile modificare il piano tariffario troppo spesso.


## <a name="scale-on-azure-portal"></a>Scalabilità nel portale di AzureScale on Azure portal

1. Nel browser aprire il portale di [Azure](https://portal.azure.com).

2. Nella pagina Servizio SignalR, dal menu a sinistra, selezionare **Scala**.
   
3. Scegliere il piano tariffario e quindi fare clic su **Seleziona**. Impostare il numero di unità per Livello **Standard.**
   
    ![Scalabilità nel portale](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Fare clic su **Salva**.

## <a name="scale-using-azure-cli"></a>Ridimensionare la cache tramite l'interfaccia della riga di comando di Azure

Questo script crea una nuova risorsa SignalR Service di livello gratuito e un nuovo gruppo di risorse e la scala fino al livello **Standard.This** script creates a new SignalR Service resource of **Free** Tier and a new resource group, and scales up to Standard Tier. 

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

Per informazioni dettagliate, ad esempio i messaggi e le connessioni inclusi per ogni piano tariffario, vedere Dettagli sui prezzi del [servizio SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).

Per una tabella dei limiti, delle quote e dei vincoli del servizio in ogni livello, vedere [Limiti del servizio SignalR](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits).

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato illustrato come ridimensionare una singola istanza del servizio SignalR.In this guide, you learned about how to scale single SignalR Service instance.

Sono supportati più endpoint anche per scenari di scalabilità, partizionamento orizzontale e tra aree.

> [!div class="nextstepaction"]
> [servizio SignalR di scalabilità con più istanzeScale SignalR Service with multiple instances](./signalr-howto-scale-multi-instances.md)
