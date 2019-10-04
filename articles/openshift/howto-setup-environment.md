---
title: Configurare l'ambiente di sviluppo per Azure Red Hat OpenShift | Microsoft Docs
description: Ecco i prerequisiti per l'uso di Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: configurazione il programma di installazione di Red hat openshift
author: jimzim
ms.author: jzim
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: container-service
manager: jeconnoc
ms.openlocfilehash: a31655e8c8805505bdcc5e90bf25191590d35c18
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672526"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Configurare l'ambiente di sviluppo di Azure Red Hat OpenShift

Per compilare ed eseguire applicazioni di Microsoft Azure Red Hat OpenShift, è necessario:

* Acquistare istanze di macchina virtuale di Azure sono riservato.
* Installare la versione 2.0.65 (o versione successiva) del comando di Azure (o usare Azure Cloud Shell).
* Registrarsi per il `AROGA` funzionalità e i provider di risorse associati.
* Creare un tenant di Azure Active Directory (Azure AD).
* Creare un oggetto di applicazione di Azure AD.
* Creare un utente di Azure AD.

Le istruzioni seguenti illustrano le fasi di tutti questi prerequisiti.

## <a name="purchase-azure-red-hat-openshift-application-nodes-reserved-instances"></a>Acquistare istanze riservate di Azure Red Hat OpenShift applicazione nodi

Prima di poter usare Azure Red Hat OpenShift, è necessario acquistare un minimo di 4 nodi di applicazione Azure Red Hat OpenShift riservato, dopodiché sarà in grado di provisioning di cluster.

Se si è clienti di Azure [acquistare istanze riservate di Azure Red Hat OpenShift](https://aka.ms/openshift/buy) tramite il portale di Azure. Dopo l'acquisto, la sottoscrizione verrà attivata entro 24 ore.

Se non si è clienti di Azure [contatta il reparto vendite](https://aka.ms/openshift/contact-sales) e compilare il modulo nella parte inferiore della pagina per avviare il processo di vendita.

Vedere le [pagina dei prezzi Azure Red Hat OpenShift](https://aka.ms/openshift/pricing) per altre informazioni.

## <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure

Azure Red Hat OpenShift richiede la versione 2.0.65 o versione successiva della riga di comando di Azure. Se è già stato installato l'interfaccia CLI di Azure, è possibile controllare quale versione in uso eseguendo:

```bash
az --version
```

La prima riga di output avrà la versione dell'interfaccia della riga, ad esempio `azure-cli (2.0.65)`.

Ecco le istruzioni per [installare CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se è necessaria una nuova installazione o aggiornamento.

In alternativa, è possibile usare la [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Quando si usa Azure Cloud Shell, assicurarsi di selezionare il **Bash** ambiente se si intende seguire il [creare e gestire un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) serie di esercitazioni.

## <a name="register-providers-and-features"></a>Registrare i provider e funzionalità

Il `Microsoft.ContainerService AROGA` funzionalità `Microsoft.Solutions`, `Microsoft.Compute`, `Microsoft.Storage`, `Microsoft.KeyVault` e `Microsoft.Network` provider devono essere registrati nella sottoscrizione manualmente prima di distribuire il primo cluster di Azure Red Hat OpenShift.

Per registrare manualmente questi provider e funzionalità, usare le istruzioni seguenti da una shell Bash, se è stata installata l'interfaccia della riga di comando o dalla sessione di Azure Cloud Shell (Bash) nel portale di Azure:

1. Se hai più sottoscrizioni di Azure, specificare l'ID sottoscrizione da usare:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registrare la funzionalità containerservice AROGA:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registrare il provider di Microsoft. Storage:

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registrare il provider di Microsoft. COMPUTE:

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. Registrare il provider Microsoft.Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registrare il provider di Microsoft. Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. Registrare il provider di Microsoft. keyvault:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Aggiornare la registrazione del provider di risorse containerservice:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Creare un tenant di Azure Active Directory (Azure AD)

Il servizio Azure Red Hat OpenShift richiede un tenant di Azure Active Directory (Azure AD) associato che rappresenta l'organizzazione e la relativa relazione a Microsoft. Tenant di Azure AD consente di registrare, compilare, gestire le app, nonché usare altri servizi di Azure.

Se non si dispone di un'istanza di AD Azure da usare come tenant per il cluster di Azure Red Hat OpenShift, o si vuole creare un tenant di test, seguire le istruzioni in [creare un tenant di Azure AD per il cluster di Azure Red Hat OpenShift](howto-create-tenant.md) prima continuare con questa Guida.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Creare un utente, gruppo di sicurezza e applicazione di Azure AD oggetti

Azure Red Hat OpenShift richiede le autorizzazioni per eseguire attività nel cluster, ad esempio la configurazione di archiviazione. Queste autorizzazioni vengono rappresentate tramite una [entità servizio](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). È anche opportuno creare un nuovo utente di Active Directory per il test delle App in esecuzione nel cluster Azure Red Hat OpenShift.

Seguire le istruzioni in [creare un oggetto di app di Azure AD e l'utente](howto-aad-app-configuration.md) per creare un'entità servizio, generare un URL di callback autenticazione e il segreto client per l'app e creare un nuovo gruppo di sicurezza di Azure AD e l'utente per accedere la grappolo.

## <a name="next-steps"></a>Passaggi successivi

Questo punto tutto pronto per l'uso di Azure Red Hat OpenShift.

Provare l'esercitazione:
> [!div class="nextstepaction"]
> [Creare un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
