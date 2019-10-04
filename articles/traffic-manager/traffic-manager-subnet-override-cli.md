---
title: Override della subnet di gestione traffico di Azure con l'interfaccia della riga di comando Microsoft Docs
description: Questo articolo illustra come usare l'override della subnet di gestione traffico per eseguire l'override del metodo di routing di un profilo di gestione traffico per indirizzare il traffico a un endpoint in base all'indirizzo IP dell'utente finale tramite un intervallo IP predefinito per i mapping degli endpoint.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: d285db22ef92128e025a677ea7f86d623dfe130c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351174"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Sostituzione della subnet di gestione traffico con l'interfaccia della riga

L'override della subnet di gestione traffico consente di modificare il metodo di routing di un profilo.  L'aggiunta di una sostituzione consente di indirizzare il traffico in base all'indirizzo IP dell'utente finale con un intervallo di indirizzi IP predefinito al mapping degli endpoint. 

## <a name="how-subnet-override-works"></a>Funzionamento dell'override della subnet

Quando si aggiungono sostituzioni di subnet a un profilo di gestione traffico, gestione traffico verificherà prima di tutto se è presente una subnet sostitutiva per l'indirizzo IP dell'utente finale. Se ne viene trovato uno, la query DNS dell'utente viene indirizzata all'endpoint corrispondente.  Se non viene trovato alcun mapping, gestione traffico eseguirà il fallback al metodo di routing originale del profilo. 

Gli intervalli di indirizzi IP possono essere specificati come intervalli CIDR (ad esempio, 1.2.3.0/24) o come intervalli di indirizzi (ad esempio, 1.2.3.4-5.6.7.8). Gli intervalli IP associati a ogni endpoint devono essere univoci per tale endpoint. Qualsiasi sovrapposizione degli intervalli IP tra endpoint diversi causerà il rifiuto del profilo da parte di gestione traffico.

Sono disponibili due tipi di profili di routing che supportano le sostituzioni delle subnet:

* **Geografico** : se Gestione traffico trova una sostituzione della subnet per l'indirizzo IP della query DNS, la query verrà indirizzata all'endpoint indipendentemente dall'integrità dell'endpoint.
* **Prestazioni** : se Gestione traffico trova una sostituzione della subnet per l'indirizzo IP della query DNS, eseguirà il routing del traffico all'endpoint solo se è integro.  Gestione traffico eseguirà il fallback all'euristica di routing delle prestazioni se l'endpoint di override della subnet non è integro.

## <a name="create-a-traffic-manager-subnet-override"></a>Creare un'override della subnet di gestione traffico

Per creare una sostituzione della subnet di gestione traffico, è possibile usare l'interfaccia della riga di comando di Azure per aggiungere le subnet per la sostituzione all'endpoint di gestione traffico.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Aggiornare l'endpoint di gestione traffico con l'override della subnet.
Usare l'interfaccia della riga di comando di Azure per aggiornare l'endpoint con [AZ Network Traffic-Manager endpoint Update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

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

È possibile rimuovere gli intervalli di indirizzi IP eseguendo il comando [AZ Network Traffic-Manager endpoint Update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) con l'opzione **--Remove** .

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

Informazioni sul [metodo di routing del traffico della subnet](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)