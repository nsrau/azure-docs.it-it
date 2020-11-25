---
title: Creare e recuperare gli attributi di una chiave in Azure Key Vault - Interfaccia della riga di comando di Azure
description: Avvio rapido che illustra come impostare e recuperare una chiave da Azure Key Vault con l'interfaccia della riga di comando di Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 69801c9b70ea3d34838ba0b35deab3e6802ecb1f
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515779"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Avvio rapido: Impostare e recuperare una chiave da Azure Key Vault con l'interfaccia della riga di comando di Azure

In questo argomento di avvio rapido viene creato un insieme di credenziali delle chiavi in Azure Key Vault con l'interfaccia della riga di comando di Azure. Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. Per altre informazioni su Key Vault, vedere la [panoramica](../general/overview.md). L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure con comandi o script. Successivamente verrà archiviata una chiave.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa guida di avvio rapido richiede l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *ContosoResourceGroup* nella posizione *eastus*.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Verrà quindi creata un'istanza di Key Vault nel gruppo di risorse creato nel passaggio precedente. Sarà necessario specificare alcune informazioni:

- Per questa guida di avvio rapido si usa **Contoso-vault2**. È necessario specificare un nome univoco nel test.
- Nome del gruppo di risorse **ContosoResourceGroup**.
- Località **Stati Uniti orientali**.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

L'output di questo cmdlet mostra le proprietà dell'istanza di Key Vault appena creata. Prendere nota delle due proprietà elencate di seguito:

- **Vault Name**: nell'esempio corrisponde a **Contoso-Vault2**. Questo nome verrà usato per altri comandi di Key Vault.
- **Vault URI** (URI dell'insieme di credenziali): in questo esempio corrisponde a https://contoso-vault2.vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="add-a-key-to-key-vault"></a>Aggiungere una chiave a Key Vault

Per aggiungere una chiave a Key Vault, sono sufficienti un paio di passaggi aggiuntivi. Questa chiave può essere usata da un'applicazione. 

Digitare i comandi seguenti per creare una chiave denominata **ExampleKey**:

```azurecli
az keyvault key create --vault-name "Contoso-Vault2" -n ExampleKey --protection software
```

È ora possibile fare riferimento a questa chiave aggiunta ad Azure Key Vault usando il relativo URI. Usare **'https://Contoso-Vault2.vault.azure.net/keys/ExampleKey '** per ottenere la versione corrente. 

Per visualizzare la chiave archiviata in precedenza:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "Contoso-Vault2"
```

A questo punto è stata creata un'istanza di Key Vault nella quale è stata archiviata e recuperata una chiave.

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide introduttive ed esercitazioni della raccolta si basano su questa. Se si prevede di usare le guide di avvio rapido e le esercitazioni successive, è consigliabile non cancellare le risorse create.
Quando non sono più necessari, è possibile rimuovere il gruppo di risorse e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group). È possibile eliminare le risorse in questo modo:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata creata un'istanza di Key Vault in cui è stata archiviata una chiave. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere le informazioni di riferimento sui [comandi az keyvault dell'interfaccia della riga di comando di Azure](/cli/azure/keyvault?view=azure-cli-latest)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)
