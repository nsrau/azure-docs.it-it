---
title: Configurare l'ambiente di sviluppo di Azure Red Hat OpenShift
description: Ecco i prerequisiti per l'uso di Microsoft Azure Red Hat OpenShift.
keywords: impostazione del programma di installazione di Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.custom: devx-track-azurecli
ms.openlocfilehash: c189c0902e694dc49d81a48433e3269e9f2a438c
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216879"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Configurare l'ambiente di sviluppo di Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 verrà ritirato il 30 giugno 2022. Il supporto per la creazione di nuovi cluster Azure Red Hat OpenShift 3,11 continua fino al 30 novembre 2020. Dopo il ritiro, i cluster di Azure Red Hat OpenShift 3,11 rimanenti verranno arrestati per evitare vulnerabilità di sicurezza.
> 
> Seguire questa guida per [creare un cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Per domande specifiche, [Contattaci](mailto:arofeedback@microsoft.com).

Per compilare ed eseguire Microsoft Azure applicazioni Red Hat OpenShift, è necessario:

* Installare la versione 2.0.65 (o versione successiva) dell'interfaccia della riga di comando di Azure oppure usare la Azure Cloud Shell.
* Eseguire la registrazione per la `AROGA` funzionalità e i provider di risorse associati.
* Creare un tenant di Azure Active Directory (Azure AD).
* Creare un Azure AD oggetto applicazione.
* Creare un utente Azure AD.

Nelle istruzioni seguenti vengono illustrati tutti questi prerequisiti.

## <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure

Azure Red Hat OpenShift richiede la versione 2.0.65 o successiva dell'interfaccia della riga di comando di Azure. Se è già stata installata l'interfaccia della riga di comando di Azure, è possibile verificare la versione in esecuzione:

```azurecli
az --version
```

La prima riga di output avrà la versione dell'interfaccia della riga di comando, ad esempio `azure-cli (2.0.65)` .

Ecco le istruzioni per l' [installazione dell'interfaccia della](/cli/azure/install-azure-cli?view=azure-cli-latest) riga di comando di Azure se è necessaria una nuova installazione o un aggiornamento.

In alternativa, è possibile usare la [Azure cloud Shell](../cloud-shell/overview.md). Quando si usa la Azure Cloud Shell, assicurarsi di selezionare l'ambiente **bash** se si prevede di seguire la serie di esercitazioni su come [creare e gestire un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="register-providers-and-features"></a>Registrare provider e funzionalità

La `Microsoft.ContainerService AROGA` funzionalità,,, `Microsoft.Solutions` `Microsoft.Compute` e i `Microsoft.Storage` `Microsoft.KeyVault` `Microsoft.Network` provider devono essere registrati manualmente nella sottoscrizione prima di distribuire il primo cluster Azure Red Hat OpenShift.

Per registrare manualmente questi provider e funzionalità, usare le istruzioni seguenti in una shell bash se è stata installata l'interfaccia della riga di comando o dalla sessione Azure Cloud Shell (bash) nel portale di Azure:

1. Se sono presenti più sottoscrizioni di Azure, specificare l'ID sottoscrizione pertinente:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registrare la funzionalità Microsoft. servizio contenitore AROGA:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registrare il provider Microsoft. storage:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registrare il provider Microsoft. Compute:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Registrare il provider Microsoft. Solutions:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registrare il provider Microsoft. Network:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Registrare il provider Microsoft. Vault:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Aggiornare la registrazione del provider di risorse Microsoft. servizio contenitore:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Creare un tenant di Azure Active Directory (Azure AD)

Il servizio Azure Red Hat OpenShift richiede un tenant di Azure Active Directory (Azure AD) associato che rappresenta l'organizzazione e la relativa relazione con Microsoft. Il tenant di Azure AD consente di registrare, compilare e gestire le app, nonché di usare altri servizi di Azure.

Se non si dispone di un Azure AD da usare come tenant per il cluster Azure Red Hat OpenShift o si vuole creare un tenant per il test, seguire le istruzioni in [creare un tenant Azure ad per il cluster Azure Red Hat OpenShift](howto-create-tenant.md) prima di continuare con questa guida.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Creare un Azure AD utente, un gruppo di sicurezza e un oggetto applicazione

Azure Red Hat OpenShift richiede le autorizzazioni per eseguire attività nel cluster, ad esempio la configurazione dell'archiviazione. Queste autorizzazioni sono rappresentate tramite un' [entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object). È anche possibile creare un nuovo utente Active Directory per testare le app in esecuzione nel cluster Azure Red Hat OpenShift.

Seguire le istruzioni riportate in [creare un oggetto Azure ad app e un utente](howto-aad-app-configuration.md) per creare un'entità servizio, generare un segreto client e un URL di callback di autenticazione per l'app e creare un nuovo gruppo di sicurezza Azure ad e un utente per accedere al cluster.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è pronti per usare Azure Red Hat OpenShift!

Provare l'esercitazione:
> [!div class="nextstepaction"]
> [Creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: /cli/azure/install-azure-cli