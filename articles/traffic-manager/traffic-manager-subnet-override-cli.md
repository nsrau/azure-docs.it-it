---
title: Sostituzione della subnet di Gestione traffico di Azure usando l'interfaccia della riga di comando di Azure Documenti Microsoft
description: Questo articolo consente di comprendere in che modo l'override della subnet di Gestione traffico può essere usato per eseguire l'override del metodo di routing di un profilo di Gestione traffico per indirizzare il traffico a un endpoint in base all'indirizzo IP dell'utente finale tramite l'intervallo IP predefinito per i mapping degli endpoint.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 818b692884bd9d31efd08663a582ebcfec2032e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938478"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Sostituzione della subnet di Gestione traffico con l'interfaccia della riga di comando di AzureTraffic Manager subnet override using Azure CLI

L'override della subnet di Gestione traffico consente di modificare il metodo di routing di un profilo.  L'aggiunta di una sostituzione indirizzerà il traffico in base all'indirizzo IP dell'utente finale con un intervallo IP predefinito per il mapping degli endpoint. 

## <a name="how-subnet-override-works"></a>Funzionamento dell'override della subnet

Quando le sostituzioni di subnet vengono aggiunte a un profilo di gestione del traffico, Gestione traffico controllerà innanzitutto se è presente una sostituzione di subnet per l'indirizzo IP dell'utente finale. Se ne viene trovato uno, la query DNS dell'utente verrà indirizzata all'endpoint corrispondente.  Se non viene trovato un mapping, Gestione traffico eseguirà il rollback al metodo di routing originale del profilo. 

Gli intervalli di indirizzi IP possono essere specificati come intervalli CIDR (ad esempio, 1.2.3.0/24) o come intervalli di indirizzi (ad esempio, 1.2.3.4-5.6.7.8). Gli intervalli IP associati a ogni endpoint devono essere univoci per tale endpoint. Qualsiasi sovrapposizione di intervalli IP tra endpoint diversi causerà il rifiuto del profilo da parte di Gestione traffico.

Esistono due tipi di profili di routing che supportano le sostituzioni di subnet:

* **Geografico:** se Gestione traffico trova una sostituzione di subnet per l'indirizzo IP della query DNS, instrada la query all'endpoint indipendentemente dall'integrità dell'endpoint.
* **Prestazioni:** se Gestione traffico trova una sostituzione di subnet per l'indirizzo IP della query DNS, instrada il traffico all'endpoint solo se è integro.  Gestione traffico eseguirà il rollback all'euristica del routing delle prestazioni se l'endpoint di sostituzione della subnet non è integro.

## <a name="create-a-traffic-manager-subnet-override"></a>Creare una sostituzione della subnet di Gestione trafficoCreate a Traffic Manager subnet override

Per creare una sostituzione della subnet di Gestione traffico, è possibile usare l'interfaccia della riga di comando di Azure per aggiungere le subnet per la sostituzione all'endpoint di Gestione traffico.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If]( /cli/azure/install-azure-cli)you need to install or upgrade, see Install Azure CLI.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Aggiornare l'endpoint di Gestione traffico con l'override della subnet.
Usare l'interfaccia della riga di comando di Azure per aggiornare l'endpoint con l'aggiornamento dell'endpoint di gestione del traffico di rete az.Use Azure CLI to update your endpoint with [az network traffic-manager endpoint update.](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)

```azurecli

### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints

```

È possibile rimuovere gli intervalli di indirizzi IP eseguendo [l'aggiornamento dell'endpoint az network traffic-manager](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) con l'opzione **--remove.**

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sui [metodi di routing](traffic-manager-routing-methods.md)di Gestione traffico.

Informazioni sul metodo di routing del [traffico della subnet](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)