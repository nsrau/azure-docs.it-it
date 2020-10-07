---
title: Guida introduttiva - Creare un'istanza di Azure Key Vault tramite l'interfaccia della riga di comando di Azure
description: Questa guida introduttiva mostra come creare un'istanza di Azure Key Vault usando l'interfaccia della riga di comando di Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: f3b9758d52c4be8e4d85c398f5ef1d0b3fae7e86
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87541805"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Avvio rapido: Creare un insieme di credenziali delle chiavi usando l'interfaccia della riga di comando di Azure

Azure Key Vault è un servizio cloud che offre uno spazio di archiviazione protetto per [chiavi](../keys/index.yml), [segreti](../secrets/index.yml) e [certificati](../certificates/index.yml). Per altre informazioni sul servizio Key Vault, vedere [Informazioni su Azure Key Vault](overview.md). Per altre informazioni sugli elementi che è possibile archiviare in un insieme di credenziali delle chiavi, vedere [Informazioni su chiavi, segreti e certificati](about-keys-secrets-certificates.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Questa guida introduttiva mostra come creare un insieme di credenziali delle chiavi tramite l'[interfaccia della riga di comando di Azure](/cli/azure/). L'interfaccia della riga di comando di Azure consente di creare e gestire risorse di Azure usando comandi o script.  Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Per accedere ad Azure usando l'interfaccia della riga di comando è possibile digitare:

```azurecli
az login
```

Per altre informazioni sulle opzioni di accesso con l'interfaccia della riga di comando, vedere [Accedere con l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *ContosoResourceGroup* nella posizione *eastus*.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Creare un insieme di credenziali delle chiavi nel gruppo di risorse generato nel passaggio precedente. Sarà necessario specificare alcune informazioni:

- Nome dell'insieme di credenziali delle chiavi: Stringa composta da 3 a 24 caratteri che può contenere solo numeri (0-9), lettere (a-z, A-Z) e trattini (-)

  > [!Important]
  > Ogni insieme di credenziali delle chiavi deve avere un nome univoco. Negli esempi seguenti sostituire <your-unique-keyvault-name> con il nome dell'insieme di credenziali delle chiavi in uso.

- Nome del gruppo di risorse: **myResourceGroup**.
- Posizione: **EastUS**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

L'output di questo cmdlet mostra le proprietà dell'istanza di Key Vault appena creata. Prendere nota delle due proprietà elencate di seguito:

- **Vault Name**: Il nome specificato per il parametro --name precedente.
- **Vault URI** (URI dell'insieme di credenziali): In questo esempio è https://&lt;your-unique-keyvault-name&gt;.vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide introduttive ed esercitazioni della raccolta si basano su questa. Se si prevede di usare le guide introduttive e le esercitazioni successive, è consigliabile non cancellare le risorse create.

Quando non sono più necessari, è possibile rimuovere il gruppo di risorse e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group) dell'interfaccia della riga di comando di Azure:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva viene creata e quindi eliminata un'istanza del servizio Key Vault. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](overview.md)
- Vedere le informazioni di riferimento sui [comandi az keyvault dell'interfaccia della riga di comando di Azure](/cli/azure/keyvault?view=azure-cli-latest)
- Esaminare le [procedure consigliate per Azure Key Vault](best-practices.md)
