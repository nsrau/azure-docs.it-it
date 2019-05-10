---
title: Configurare l'ambiente di sviluppo per Azure Red Hat OpenShift | Microsoft Docs
description: Ecco i prerequisiti per l'uso di Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: configurazione il programma di installazione di Red hat openshift
author: TylerMSFT
ms.author: twhitney
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: openshift
manager: jeconnoc
ms.openlocfilehash: 3c265d6695af7ba1bc5833db59966a626cb29cb9
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416056"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Configurare l'ambiente di sviluppo Azure Red Hat OpenShift

Per compilare ed eseguire applicazioni di Microsoft Azure Red Hat OpenShift, è necessario:

* Acquistare istanze di macchina virtuale di Azure sono riservato.
* Installare la versione 2.0.64 (o versione successiva) del comando di Azure (o usare Azure Cloud Shell).
* Registrarsi per il `openshiftmanagedcluster` funzionalità e i provider di risorse associati.
* Creare un tenant di Azure Active Directory (Azure AD).
* Creare un oggetto di applicazione di Azure AD.
* Creare un utente di Azure AD.

Le istruzioni seguenti illustrano le fasi di tutti questi prerequisiti.

## <a name="purchase-azure-virtual-machine-reserved-instances"></a>Acquista le istanze di macchina virtuale di Azure sono riservato

Prima di poter usare Azure Red Hat OpenShift, è necessario per l'acquisto di istanze di macchina virtuale di Azure sono riservato.

Se sei un cliente di Azure, procedura [macchina virtuale di Azure di acquisto di istanze riservate](https://aka.ms/openshift/buy). Una prenotazione consente di ridurre la spesa correlata al pagamento anticipato per servizi di Azure completamente gestiti. Fare riferimento a [ *cosa sono Azure prenotazioni* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) per altre informazioni su prenotazioni e come essi risparmiare denaro.

Se non si è clienti di Azure [contatta il reparto vendite](https://aka.ms/openshift/contact-sales) e compilare il modulo nella parte inferiore della pagina per avviare il processo di vendita.

## <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure

Azure Red Hat OpenShift richiede la versione 2.0.64 o versione successiva della riga di comando di Azure. Se è già stato installato l'interfaccia CLI di Azure, è possibile controllare quale versione in uso eseguendo:

```bash
az --version
```

La prima riga di output avrà la versione dell'interfaccia della riga, ad esempio `azure-cli (2.0.64)`.

Ecco le istruzioni per [installare CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se è necessaria una nuova installazione o aggiornamento.

In alternativa, è possibile usare la [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Quando si usa Azure Cloud Shell, assicurarsi di selezionare il **Bash** ambiente se si intende seguire il [creare e gestire un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) serie di esercitazioni.

## <a name="register-providers-and-features"></a>Registrare i provider e funzionalità

Il `Microsoft.ContainerService openshiftmanagedcluster` funzionalità `Microsoft.Solutions`, e `Microsoft.Network` provider devono essere registrati nella sottoscrizione manualmente prima di distribuire il primo cluster di Azure Red Hat OpenShift.

Per registrare manualmente questi provider e funzionalità, usare le istruzioni seguenti da una shell Bash, se è stata installata l'interfaccia della riga di comando o dalla sessione di Azure Cloud Shell (Bash) nel portale di Azure:.
1. Se hai più sottoscrizioni di Azure, specificare l'ID sottoscrizione da usare:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

2. Registrare la funzionalità di openshiftmanagedcluster containerservice:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n openshiftmanagedcluster
    ```

3. Registrare il provider Microsoft.Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

4. Registrare il provider di Microsoft. Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

5. Registrare il provider di Microsoft. keyvault:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

6. Aggiornare la registrazione del provider di risorse containerservice:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Creare un tenant di Azure Active Directory (Azure AD)

Il servizio Azure Red Hat OpenShift richiede un tenant di Azure Active Directory (Azure AD) associato che rappresenta l'organizzazione e la relativa relazione a Microsoft. Tenant di Azure AD consente di registrare, compilare, gestire le app, nonché usare altri servizi di Azure.

Se non si dispone di un'istanza di AD Azure da usare come tenant per il cluster di Azure Red Hat OpenShift, o si vuole creare un tenant di test, seguire le istruzioni in [creare un tenant di Azure AD per il cluster di Azure Red Hat OpenShift](howto-create-tenant.md) prima continuare con questa Guida.

## <a name="create-an-azure-ad-application-object-and-user"></a>Creare un oggetto di applicazione di Azure AD e l'utente

Azure Red Hat OpenShift richiede le autorizzazioni per eseguire attività nel cluster, ad esempio la configurazione di archiviazione. Queste autorizzazioni vengono rappresentate tramite una [entità servizio](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) e vengono creati quando si registra un'applicazione di Azure AD che rappresenta il carico di lavoro si intende ospitare su Azure Red Hat OpenShift. È anche opportuno creare un nuovo utente di Active Directory per il test delle App in esecuzione nel cluster Azure Red Hat OpenShift.

Seguire le istruzioni in [creare un oggetto di app di Azure AD e l'utente](howto-aad-app-configuration.md) per informazioni su come creare un'entità servizio, generare un URL di callback autenticazione e il segreto client per l'app e creare un nuovo utente di Active Directory per il test.

## <a name="next-steps"></a>Passaggi successivi

Questo punto tutto pronto per l'uso di Azure Red Hat OpenShift.

Provare l'esercitazione:
> [!div class="nextstepaction"]
> [Creare un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli