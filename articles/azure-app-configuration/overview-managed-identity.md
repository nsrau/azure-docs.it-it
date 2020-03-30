---
title: Configurare le identità gestite con La configurazione delle app di AzureConfigure managed identities with Azure App Configuration
description: Informazioni sul funzionamento delle identità gestite in Configurazione app di Azure e su come configurare un'identità gestita
author: jpconnock
ms.topic: article
ms.date: 02/25/2020
ms.author: jeconnoc
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: fe66466395a100221e6a3cdebdef870bdf195afc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623034"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Come usare le identità gestite per la configurazione delle app di AzureHow to use managed identities for Azure App Configuration

Questo argomento illustra come creare un'identità gestita per la configurazione delle app di Azure.This topic shows you how to create a managed identity for Azure App Configuration. A managed identity from Azure Active Directory (AAD) allows Azure App Configuration to easily access other AAD-protected resources, such as Azure Key Vault. L'identità è gestita dalla piattaforma Azure.The identity is managed by the Azure platform. Non richiede il provisioning o la rotazione di segreti. Per altre informazioni sulle identità gestite in AAD, vedere [Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

All'applicazione possono essere concessi due tipi di identità:

- **Un'identità assegnata dal sistema** è legata all'archivio di configurazione. Viene eliminato se l'archivio di configurazione viene eliminato. Un archivio di configurazione può avere una sola identità assegnata dal sistema.
- **Un'identità assegnata dall'utente** è una risorsa autonoma di Azure che può essere assegnata all'archivio di configurazione. Un archivio di configurazione può avere più identità assegnate dall'utente.

## <a name="adding-a-system-assigned-identity"></a>Aggiunta di un'identità assegnata dal sistema

La creazione di un archivio di configurazione app con un'identità assegnata dal sistema richiede l'impostazione di una proprietà aggiuntiva nell'archivio.

### <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

Per configurare un'identità gestita tramite l'interfaccia della riga di comando di Azure, usare il comando [az appconfig identity assign] su un archivio di configurazione esistente. Sono disponibili tre opzioni per l'esecuzione degli esempi di questa sezione:

- Usare [Azure Cloud Shell](../cloud-shell/overview.md) dal portale di Azure.
- Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", situato nell'angolo superiore destro di ogni blocco di codice sottostante.
- [Installare la versione più recente dell'interfaccia della riga di](https://docs.microsoft.com/cli/azure/install-azure-cli) comando di Azure (2.1 o versione successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale.

The following steps will walk you through creating an App Configuration store and assigning it an identity using the CLI:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login]. Usare un account associato alla sottoscrizione di Azure:Use an account that is associated with your Azure subscription:

    ```azurecli-interactive
    az login
    ```

1. Creare un archivio di configurazione app usando l'interfaccia della riga di comando. Per altri esempi su come usare l'interfaccia della riga di comando con la configurazione delle app di Azure, vedere Esempi dell'interfaccia della riga di comando per la configurazione delle app:For more examples of how to use the CLI with Azure App Configuration, see [App Configuration CLI samples:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Eseguire il comando [az appconfig identity assign] per creare l'identità assegnata dal sistema per questo archivio di configurazione:Run the az appconfig identity assign command to create the system-assigned identity for this configuration store:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Aggiunta di un'identità assegnata dall'utente

Per creare un archivio di configurazione app con un'identità assegnata dall'utente è necessario creare l'identità e quindi assegnarne l'identificatore di risorsa all'archivio.

### <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

Per configurare un'identità gestita tramite l'interfaccia della riga di comando di Azure, usare il comando [az appconfig identity assign] su un archivio di configurazione esistente. Sono disponibili tre opzioni per l'esecuzione degli esempi di questa sezione:

- Usare [Azure Cloud Shell](../cloud-shell/overview.md) dal portale di Azure.
- Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", situato nell'angolo superiore destro di ogni blocco di codice sottostante.
- [Installare la versione più recente dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale.

I passaggi seguenti illustrano come creare un'identità assegnata dall'utente e un archivio di configurazione app, quindi assegnare l'identità all'archivio tramite l'interfaccia della riga di comando:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login]. Usare un account associato alla sottoscrizione di Azure:Use an account that is associated with your Azure subscription:

    ```azurecli-interactive
    az login
    ```

1. Creare un archivio di configurazione app usando l'interfaccia della riga di comando. Per altri esempi su come usare l'interfaccia della riga di comando con la configurazione delle app di Azure, vedere Esempi dell'interfaccia della riga di comando per la configurazione delle app:For more examples of how to use the CLI with Azure App Configuration, see [App Configuration CLI samples:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Creare un'identità `myUserAssignedIdentity` assegnata dall'utente denominata tramite l'interfaccia della riga di comando.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Nell'output di questo comando prendere `id` nota del valore della proprietà.

1. Eseguire il comando [az appconfig identity assign] per assegnare la nuova identità assegnata dall'utente a questo archivio di configurazione. Utilizzare il valore `id` della proprietà annotata nel passaggio precedente.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Rimozione di un'identità

Un'identità assegnata dal sistema può essere rimossa disabilitando la funzionalità usando il comando [az appconfig identity remove](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) nell'interfaccia della riga di comando di Azure.A system-assigned identity can be removed by disabling the feature by using the az appconfig identity remove command in the Azure CLI. Le identità assegnate dall'utente possono essere rimosse separatamente. La rimozione di un'identità assegnata dal sistema in questo modo ne comporterà l'eliminazione anche da AAD. Le identità assegnate dal sistema vengono rimosse automaticamente anche da AAD quando viene eliminata la risorsa app.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app ASP.NET Core con la configurazione dell'app di AzureCreate an ASP.NET Core app with Azure App Configuration](quickstart-aspnet-core-app.md)

[az appconfig identità assegnare]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login (accesso az)]: /cli/azure/reference-index#az-login
