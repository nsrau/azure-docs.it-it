---
title: Configurare l'ambiente di sviluppo Azure Red Hat OpenShiftSet up your Azure Red Hat OpenShift development environment
description: Ecco i prerequisiti per l'uso di Microsoft Azure Red Hat OpenShift.Here are the prerequisites for working with Microsoft Azure Red Hat OpenShift.
keywords: cappello rosso setup openshift impostare
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: e7396ce9fbed46688d59b582f246e5454d063fb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477035"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Configurare l'ambiente di sviluppo di Azure Red Hat OpenShift

Per compilare ed eseguire applicazioni Microsoft Azure Red Hat OpenShift, è necessario:To build and run Microsoft Azure Red Hat OpenShift applications, you'll need to:

* Installare la versione 2.0.65 (o successiva) dell'interfaccia della riga di comando di Azure (o usare Azure Cloud Shell).
* Eseguire la `AROGA` registrazione per la funzionalità e i provider di risorse associati.
* Creare un tenant di Azure Active Directory (Azure AD).
* Creare un oggetto applicazione di Azure AD.
* Creare un utente di Azure AD.

Le istruzioni seguenti illustrano tutti questi prerequisiti.

## <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure

Azure Red Hat OpenShift requires version 2.0.65 or higher of the Azure CLI. Se l'interfaccia della riga di comando di Azure è già stata installata, è possibile verificare la versione in uso eseguendo:If you've already installed the Azure CLI, you can check which version you have by running:

```azurecli
az --version
```

La prima riga di output avrà `azure-cli (2.0.65)`la versione CLI, ad esempio .

Ecco le istruzioni per [l'installazione dell'interfaccia della riga di](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) comando di Azure se è necessaria una nuova installazione o un aggiornamento.

In alternativa, è possibile usare [Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview) Quando si usa Azure Cloud Shell, assicurarsi di selezionare l'ambiente **Bash** se si prevede di seguire la serie di esercitazioni sul [cluster Create and manage di Azure Red Hat OpenShift.](tutorial-create-cluster.md)

## <a name="register-providers-and-features"></a>Registrare provider e funzionalità

La `Microsoft.ContainerService AROGA` funzionalità `Microsoft.Solutions` `Microsoft.Compute`, `Microsoft.Storage` `Microsoft.KeyVault` , `Microsoft.Network` e i provider devono essere registrati manualmente nella sottoscrizione prima di distribuire il primo cluster Azure Red Hat OpenShift.

Per registrare manualmente questi provider e funzionalità, usare le istruzioni seguenti da una shell Bash se è stata installata l'interfaccia della riga di comando o dalla sessione di Azure Cloud Shell (Bash) nel portale di Azure:To register these providers and features manually, use the following instructions from a Bash shell if you've installed the CLI, or from the Azure Cloud Shell (Bash) session in your Azure portal:

1. Se si hanno più sottoscrizioni di Azure, specificare l'ID sottoscrizione pertinente:If you have multiple Azure subscriptions, specify the relevant subscription ID:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registrare la funzionalità Microsoft.ContainerService AROGA:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registrare il provider Microsoft.Storage:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registrare il provider Microsoft.Compute:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Registrare il provider Microsoft.Solutions:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registrare il provider Microsoft.Network:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Registrare il provider Microsoft.KeyVault:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Aggiornare la registrazione del provider di risorse Microsoft.ContainerService:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Creare un tenant di Azure Active Directory (Azure AD)Create an Azure Active Directory (Azure AD) tenant

Il servizio Azure Red Hat OpenShift richiede un tenant di Azure Active Directory (Azure AD) associato che rappresenta l'organizzazione e la relativa relazione con Microsoft.The Azure Red Hat OpenShift service requires an associated Azure Active Directory (Azure AD) tenant that represents your organization and its relationship to Microsoft. Il tenant di Azure AD consente di registrare, compilare e gestire le app, nonché di usare altri servizi di Azure.Your Azure AD tenant enables you to register, build, and manage apps, as well as use other Azure services.

Se non si dispone di azure AD da usare come tenant per il cluster Azure Red Hat OpenShift o si vuole creare un tenant per il test, seguire le istruzioni in Creare un tenant di [Azure AD per il cluster Azure Red Hat OpenShift](howto-create-tenant.md) prima di continuare con questa guida.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Creare un oggetto utente, gruppo di sicurezza e applicazione di Azure ADCreate an Azure AD user, security group and application object

Azure Red Hat OpenShift richiede autorizzazioni per eseguire attività nel cluster, ad esempio la configurazione dell'archiviazione. Queste autorizzazioni sono rappresentate tramite [un'entità servizio](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). È inoltre consigliabile creare un nuovo utente di Active Directory per testare le app in esecuzione nel cluster Azure Red Hat OpenShift.You'll also want to create a new Active Directory user for testing apps running on your Azure Red Hat OpenShift cluster.

Seguire le istruzioni in Creare un oggetto e un [utente dell'app Azure AD](howto-aad-app-configuration.md) per creare un'entità servizio, generare un segreto client e un URL di callback di autenticazione per l'app e creare un nuovo gruppo di sicurezza di Azure AD e un nuovo utente per accedere al cluster.

## <a name="next-steps"></a>Passaggi successivi

Ora sei pronto per usare Azure Red Hat OpenShift!

Prova il tutorial:
> [!div class="nextstepaction"]
> [Creare un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
