---
title: Configurare l'ambiente di sviluppo di Azure Red Hat OpenShift | Microsoft Docs
description: Ecco i prerequisiti per l'uso di Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: impostazione del programma di installazione di Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
manager: jeconnoc
ms.openlocfilehash: fa09ed90258a62d37dafeea5f4760e1fabdc210b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581609"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Configurare l'ambiente di sviluppo di Azure Red Hat OpenShift

Per compilare ed eseguire Microsoft Azure applicazioni Red Hat OpenShift, è necessario:

* Installare la versione 2.0.65 (o versione successiva) dell'interfaccia della riga di comando di Azure oppure usare la Azure Cloud Shell.
* Eseguire la registrazione per la funzionalità di `AROGA` e i provider di risorse associati.
* Creare un tenant di Azure Active Directory (Azure AD).
* Creare un Azure AD oggetto applicazione.
* Creare un utente Azure AD.

Nelle istruzioni seguenti vengono illustrati tutti questi prerequisiti.

## <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure

Azure Red Hat OpenShift richiede la versione 2.0.65 o successiva dell'interfaccia della riga di comando di Azure. Se è già stata installata l'interfaccia della riga di comando di Azure, è possibile verificare la versione in esecuzione:

```bash
az --version
```

La prima riga di output avrà la versione dell'interfaccia della riga di comando, ad esempio `azure-cli (2.0.65)`.

Ecco le istruzioni per l' [installazione dell'interfaccia della](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) riga di comando di Azure se è necessaria una nuova installazione o un aggiornamento.

In alternativa, è possibile usare la [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Quando si usa la Azure Cloud Shell, assicurarsi di selezionare l'ambiente **bash** se si prevede di seguire la serie di esercitazioni su come [creare e gestire un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="register-providers-and-features"></a>Registrare provider e funzionalità

I provider della funzionalità `Microsoft.ContainerService AROGA`, `Microsoft.Solutions`, `Microsoft.Compute`, `Microsoft.Storage`, `Microsoft.KeyVault` e `Microsoft.Network` devono essere registrati manualmente nella sottoscrizione prima di distribuire il primo cluster Azure Red Hat OpenShift.

Per registrare manualmente questi provider e funzionalità, usare le istruzioni seguenti in una shell bash se è stata installata l'interfaccia della riga di comando o dalla sessione Azure Cloud Shell (bash) nel portale di Azure:

1. Se si dispone di più sottoscrizioni di Azure, specificare l'ID sottoscrizione pertinente:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registrare la funzionalità Microsoft. servizio contenitore AROGA:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registrare il provider Microsoft. storage:

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registrare il provider Microsoft. Compute:

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. Registrare il provider Microsoft. Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registrare il provider Microsoft. Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. Registrare il provider Microsoft. Vault:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Aggiornare la registrazione del provider di risorse Microsoft. servizio contenitore:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Creare un tenant di Azure Active Directory (Azure AD)

Il servizio Azure Red Hat OpenShift richiede un tenant di Azure Active Directory (Azure AD) associato che rappresenta l'organizzazione e la relativa relazione con Microsoft. Il tenant di Azure AD consente di registrare, compilare e gestire le app, nonché di usare altri servizi di Azure.

Se non si dispone di un Azure AD da usare come tenant per il cluster Azure Red Hat OpenShift o si vuole creare un tenant per il test, seguire le istruzioni in [creare un tenant Azure ad per il cluster Azure Red Hat OpenShift](howto-create-tenant.md) prima di continuare con questa guida .

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Creare un Azure AD utente, un gruppo di sicurezza e un oggetto applicazione

Azure Red Hat OpenShift richiede le autorizzazioni per eseguire attività nel cluster, ad esempio la configurazione dell'archiviazione. Queste autorizzazioni sono rappresentate tramite un' [entità servizio](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). È anche possibile creare un nuovo utente Active Directory per testare le app in esecuzione nel cluster Azure Red Hat OpenShift.

Seguire le istruzioni riportate in [creare un oggetto Azure ad app e un utente](howto-aad-app-configuration.md) per creare un'entità servizio, generare un segreto client e un URL di callback di autenticazione per l'app e creare un nuovo gruppo di sicurezza Azure ad e un utente per accedere al cluster.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è pronti per usare Azure Red Hat OpenShift!

Provare l'esercitazione:
> [!div class="nextstepaction"]
> [Creare un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
