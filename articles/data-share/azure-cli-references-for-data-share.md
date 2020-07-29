---
title: Informazioni di riferimento sull'interfaccia della riga di comando di Azure per Condivisione dati di Azure
description: Pagina di destinazione delle informazioni di riferimento sull'interfaccia della riga di comando di Azure per Condivisione dati di Azure
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298480"
---
# <a name="azure-cli-for-azure-data-share"></a>Interfaccia della riga di comando di Azure per Condivisione dati di Azure

L'[interfaccia della riga di comando di Azure](/cli/azure/what-is-azure-cli) è un set di comandi che consente di creare e gestire le risorse di Azure.  È disponibile in molti servizi di Azure, tra cui Condivisione dati di Azure.  Per la condivisione dati sono disponibili oltre 65 comandi diversi.  Questi comandi offrono la possibilità di gestire in modo efficiente con il servizio da una riga di comando.

## <a name="references-for-data-share"></a>Informazioni di riferimento per Condivisione dati

Tutti i comandi dell'interfaccia della riga di comando di Azure per Condivisione dati di Azure sono attualmente estensioni per l'interfaccia.  Un'estensione consente di accedere ai comandi sperimentali e in versioni non definitive.  Altre informazioni di riferimento sull'estensione in [Usare le estensioni con l'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-extensions-overview).

|Informazioni di riferimento sull'interfaccia della riga di comando di Azure |Descrizione
|-|-|-|
| [az datashare](/cli/azure/ext/datashare/datashare) | Tutti i comandi per la gestione di Condivisione dati di Azure.
| [az datashare account](/cli/azure/ext/datashare/datashare/account) | Comandi per la gestione di account di Condivisione dati di Azure.
| [az datashare consumer](/cli/azure/ext/datashare/datashare/consumer) | Comandi che consentono ai consumer di gestire Condivisione dati di Azure.
| [az datashare dataset](/cli/azure/ext/datashare/datashare/dataset) | Comandi che consentono ai provider di gestire Condivisione dati di Azure.
| [az datashare invitation](/cli/azure/ext/datashare/datashare/invitation) | Comandi che consentono ai consumer di gestire inviti a Condivisione dati di Azure.
| [az datashare provider-share-subscription](/cli/azure/ext/datashare/datashare/provider-share-subscription) | Comandi che consentono ai provider di gestire inviti a Condivisione dati di Azure.
| [az datashare synchronization](/cli/azure/ext/datashare/datashare/synchronization)  | Comandi per la gestione della sincronizzazione di Condivisione dati di Azure.
| [az datashare synchronization-setting](/cli/azure/ext/datashare/datashare/synchronization-setting)  | Comandi che consentono ai provider di gestire le impostazioni di sincronizzazione di Condivisione dati di Azure.

## <a name="reference-examples"></a>Esempi di riferimento

Sono disponibili esempi per ogni riferimento dell'interfaccia della riga di comando di Azure. È possibile completare queste attività anche con il portale di Azure, ma con l'interfaccia della riga di comando di Azure basta una singola riga di comando.  I blocchi di codice seguenti danno un'idea della facilità di utilizzo dell'interfaccia della riga di comando di Azure.

Per usare Condivisione dati di Azure, è necessario prima creare un gruppo di risorse.  I gruppi di risorse di Azure sono semplici da creare e gestire con l'interfaccia della riga di comando di Azure.  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

La creazione di un account di condivisione dati è altrettanto semplice.

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>Vedere anche

* [Introduzione all'interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli) per informazioni sull'installazione e l'accesso.

* Per altri riferimenti ai [comandi di base](/cli/azure/reference-index) e alle [estensioni](/cli/azure/azure-cli-extensions-list), vedere la documentazione dell'interfaccia della riga di comando di Azure.
