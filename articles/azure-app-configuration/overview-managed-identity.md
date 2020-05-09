---
title: Configurare identità gestite con la configurazione di app Azure
description: Informazioni sul funzionamento delle identità gestite in app Azure configurazione e su come configurare un'identità gestita
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: 560a2df5946b9cfe1160864752595e86cc6eeb65
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82981222"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Come usare le identità gestite per la configurazione di app Azure

In questo argomento viene illustrato come creare un'identità gestita per la configurazione app Azure. Un'identità gestita da Azure Active Directory (AAD) consente app Azure configurazione di accedere facilmente ad altre risorse protette da AAD, ad esempio Azure Key Vault. L'identità viene gestita dalla piattaforma Azure. Non richiede il provisioning o la rotazione di segreti. Per altre informazioni sulle identità gestite in AAD, vedere [Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

All'applicazione possono essere concessi due tipi di identità:

- Un' **identità assegnata dal sistema** è associata all'archivio di configurazione. Viene eliminato se l'archivio di configurazione viene eliminato. Un archivio di configurazione può avere una sola identità assegnata dal sistema.
- Un' **identità assegnata dall'utente** è una risorsa di Azure autonoma che può essere assegnata all'archivio di configurazione. Un archivio di configurazione può avere più identità assegnate dall'utente.

## <a name="adding-a-system-assigned-identity"></a>Aggiunta di un'identità assegnata dal sistema

La creazione di un archivio di configurazione dell'app con un'identità assegnata dal sistema richiede l'impostazione di una proprietà aggiuntiva nell'archivio.

### <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

Per configurare un'identità gestita usando l'interfaccia della riga di comando di Azure, usare il comando [AZ appconfig Identity Assign] per un archivio di configurazione esistente. Sono disponibili tre opzioni per l'esecuzione degli esempi di questa sezione:

- Usare [Azure Cloud Shell](../cloud-shell/overview.md) dal portale di Azure.
- Usare il Azure Cloud Shell incorporato tramite il pulsante "prova", che si trova nell'angolo superiore destro di ogni blocco di codice riportato di seguito.
- [Installare la versione più recente dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2,1 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale.

La procedura seguente illustra la creazione di un archivio di configurazione dell'app e l'assegnazione di un'identità usando l'interfaccia della riga di comando:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login]. Usare un account associato alla sottoscrizione di Azure:

    ```azurecli-interactive
    az login
    ```

1. Creare un archivio di configurazione dell'app usando l'interfaccia della riga di comando. Per altri esempi su come usare l'interfaccia della riga di comando con la configurazione di app Azure, vedere [esempi di configurazione dell'app CLI](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Eseguire il comando [AZ appconfig Identity Assign] per creare l'identità assegnata dal sistema per questo archivio di configurazione:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Aggiunta di un'identità assegnata dall'utente

Per creare un archivio di configurazione dell'app con un'identità assegnata dall'utente, è necessario creare l'identità e quindi assegnare il relativo identificatore di risorsa all'archivio.

### <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

Per configurare un'identità gestita usando l'interfaccia della riga di comando di Azure, usare il comando [AZ appconfig Identity Assign] per un archivio di configurazione esistente. Sono disponibili tre opzioni per l'esecuzione degli esempi di questa sezione:

- Usare [Azure Cloud Shell](../cloud-shell/overview.md) dal portale di Azure.
- Usare il Azure Cloud Shell incorporato tramite il pulsante "prova", che si trova nell'angolo superiore destro di ogni blocco di codice riportato di seguito.
- [Installare la versione più recente dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale.

La procedura seguente illustra come creare un'identità assegnata dall'utente e un archivio di configurazione dell'app e quindi assegnare l'identità all'archivio usando l'interfaccia della riga di comando:

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login]. Usare un account associato alla sottoscrizione di Azure:

    ```azurecli-interactive
    az login
    ```

1. Creare un archivio di configurazione dell'app usando l'interfaccia della riga di comando. Per altri esempi su come usare l'interfaccia della riga di comando con la configurazione di app Azure, vedere [esempi di configurazione dell'app CLI](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Creare un'identità assegnata dall'utente `myUserAssignedIdentity` chiamata usando l'interfaccia della riga di comando.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Nell'output di questo comando, prendere nota del valore della `id` proprietà.

1. Eseguire il comando [AZ appconfig Identity Assign] per assegnare la nuova identità assegnata dall'utente a questo archivio di configurazione. Usare il valore della `id` proprietà annotato nel passaggio precedente.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Rimozione di un'identità

Un'identità assegnata dal sistema può essere rimossa disabilitando la funzionalità usando il comando [AZ appconfig Identity Remove](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) nell'interfaccia della riga di comando di Azure. Le identità assegnate dall'utente possono essere rimosse separatamente. La rimozione di un'identità assegnata dal sistema in questo modo ne comporterà l'eliminazione anche da AAD. Le identità assegnate dal sistema vengono rimosse automaticamente anche da AAD quando viene eliminata la risorsa app.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app ASP.NET Core con la configurazione app Azure](quickstart-aspnet-core-app.md)

[AZ appconfig Identity Assign]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[AZ login]: /cli/azure/reference-index#az-login
