---
title: Riferimenti CLI di Azure per la condivisione dati di Azure
description: Pagina di destinazione di riferimento dell'interfaccia della riga di comando di Azure
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84302643"
---
# <a name="azure-cli-for-azure-data-share"></a>INTERFACCIA della riga di comando di Azure per Azure Data Share

L'[interfaccia della riga di comando di Azure](/cli/azure/what-is-azure-cli) è un set di comandi che consente di creare e gestire le risorse di Azure.  È disponibile in molti servizi di Azure, inclusa la condivisione di dati di Azure.  Sono disponibili più di 65 comandi diversi per la condivisione di dati.  Questi comandi offrono la possibilità di lavorare in modo efficace con il servizio da una riga di comando.

## <a name="references-for-data-share"></a>Riferimenti per la condivisione di dati

Tutti i comandi dell'interfaccia della riga di comando di Azure per la condivisione di dati di Azure sono attualmente estensioni per l'interfaccia  Un'estensione consente di accedere ai comandi sperimentali e di versione non definitiva.  Altre informazioni di riferimento sull'estensione in [Usare le estensioni con l'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-extensions-overview).

|Riferimenti per l'interfaccia della riga di comando |Descrizione
|-|-|-|
| [AZ DataShare](/cli/azure/ext/datashare/datashare) | Tutti i comandi per gestire la condivisione di dati di Azure.
| [AZ DataShare-account](/cli/azure/ext/datashare/datashare/account) | Comandi per gestire gli account di condivisione dati di Azure.
| [AZ DataShare consumer](/cli/azure/ext/datashare/datashare/consumer) | Comandi che consentono agli utenti di gestire la condivisione di dati di Azure.
| [set di dati AZ DataShare](/cli/azure/ext/datashare/datashare/dataset) | Comandi per i provider per gestire i set di dati di condivisione dati di Azure.
| [invito AZ DataShare](/cli/azure/ext/datashare/datashare/invitation) | Comandi che consentono agli utenti di gestire gli inviti di condivisione dati di Azure.
| [AZ DataShare provider-Share-Subscription](/cli/azure/ext/datashare/datashare/provider-share-subscription) | Comandi per i provider per gestire le sottoscrizioni di condivisione dati di Azure.
| [sincronizzazione AZ DataShare](/cli/azure/ext/datashare/datashare/synchronization)  | Comandi per la gestione della sincronizzazione della condivisione dati di Azure.
| [impostazione della sincronizzazione AZ DataShare](/cli/azure/ext/datashare/datashare/synchronization-setting)  | Comandi per i provider per gestire le impostazioni di sincronizzazione della condivisione dati di Azure.

## <a name="reference-examples"></a>Esempi di riferimento

Sono disponibili esempi per ogni riferimento dell'interfaccia della riga di comando di Azure. È possibile completare queste attività anche con il portale di Azure, ma con l'interfaccia della riga di comando di Azure basta una singola riga di comando.  I blocchi di codice seguenti danno un'idea della facilità di utilizzo dell'interfaccia della riga di comando di Azure.

Per usare la condivisione di dati di Azure, è necessario prima di tutto un gruppo di risorse.  I gruppi di risorse di Azure sono semplici da creare e gestire con l'interfaccia della riga di comando di Azure.  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

La creazione di un account di condivisione dati è piuttosto semplice.

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>Vedere anche

* [Introduzione all'interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli) per informazioni sull'installazione e l'accesso.

* Individuare ulteriori riferimenti di [Core](/cli/azure/reference-index) ed [estensioni](/cli/azure/azure-cli-extensions-list) nella documentazione dell'interfaccia della riga di comando di Azure.
