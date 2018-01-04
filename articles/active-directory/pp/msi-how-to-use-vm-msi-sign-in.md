---
title: "Come usare un'identità del servizio gestito di una macchina virtuale di Azure per l'accesso"
description: "Istruzioni dettagliate ed esempi relativi all'uso di un'identità del servizio gestito di una macchina virtuale di Azure per l'accesso al client di script e alle risorse."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 74d732709e1cc3c97b485cc45e3a4e2c8e3cd11e
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="sign-in-using-a-vm-user-assigned-managed-service-identity-msi"></a>Accedere utilizzando una macchina virtuale assegnati dall'utente del servizio identità gestite (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]In questo articolo vengono forniti esempi di script CLI per l'accesso usando le entità di servizio MSI assegnati dall'utente e informazioni aggiuntive su argomenti importanti, ad esempio la gestione degli errori.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Per utilizzare gli esempi di CLI di Azure in questo articolo, assicurarsi di installare la versione più recente di [CLI di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Tutti gli esempi di script in questo articolo presuppongono che il client da riga di comando sia in esecuzione su una macchina virtuale abilitata per l'identità del servizio gestito. Usare la funzionalità di connessione alla macchina virtuale nel portale di Azure per connettersi in remoto alla macchina virtuale. Per informazioni dettagliate sull'abilitazione di estensione MSI in una macchina virtuale, vedere [configurare una macchina virtuale gestita servizio identità (MSI) mediante Azure CLI](msi-qs-configure-cli-windows-vm.md), o uno degli articoli variant (tramite PowerShell, portale, un modello o un SDK di Azure). 
> - Per evitare errori durante l'accesso di accesso e la risorsa, è necessario assegnare almeno il file MSI all'ambito appropriato di accesso "Lettura" (la macchina virtuale o una versione successiva) per consentire operazioni di gestione risorse di Azure nella macchina virtuale. Vedere [assegnare un'identità di servizio gestiti (MSI) l'accesso a una risorsa tramite l'interfaccia CLI di Azure](msi-howto-assign-access-cli.md) per informazioni dettagliate.

## <a name="overview"></a>Panoramica

Un'identità del servizio gestito fornisce un'[entità servizio](~/articles/active-directory/develop/active-directory-dev-glossary.md#service-principal-object), [creata con l'abilitazione dell'identità del servizio gestito](msi-overview.md#how-does-it-work) nella macchina virtuale. L'entità servizio può essere concesso l'accesso alle risorse di Azure e utilizzato come identità dai client di script o della riga di comando per l'accesso di accesso e la risorsa. In genere, per poter accedere a risorse protette con la propria identità, un client di script deve:  

   - essere registrato e autorizzato da Azure AD come applicazione client Web/riservata
   - accedere con la propria entità servizio usando le credenziali dell'app (probabilmente integrate nello script)

Con l'identità del servizio gestito, il client non deve più soddisfare questi requisiti e può accedere con l'entità servizio dell'identità del servizio gestito. 

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Lo script seguente illustra come:

1. Accedi ad Azure AD in un'entità servizio l'utente assegnato file MSI.  
2. Chiamare Gestione risorse di Azure e ottenere il percorso di area di Azure per una macchina virtuale. L'interfaccia della riga di comando si occupa di gestire automaticamente l'acquisizione/uso dei token. Assicurarsi di sostituire il nome della macchina virtuale per `<VM NAME>`e id di risorsa MSI assegnati dall'utente per `<MSI ID>`. Viene restituito l'id di risorsa file MSI nel `id` proprietà durante la creazione di un file MSI assegnati dall'utente (vedere [configurare un utente assegnato gestiti servizio identità (MSI) per una macchina virtuale con Azure CLI](msi-qs-configure-cli-windows-vm.md) per esempi del `az identity create` comando ).

    ```azurecli
    az login -–msi –u <MSI ID>
   
    vmLocation=$(az resource list -n <VM NAME> --query [*].location --out tsv)
    echo The VM region location is $vmLocation
    ```

    Risposte di esempio:
   
    ```bash
    user@vmLinux:~$ az login --msi -u /subscriptions/80c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName
    [
      {
        "environmentName": "AzureCloud",
        "id": "90c696ff-5efa-4909-a64d-z1b616f423bl",
        "isDefault": true,
        "name": "MSIResource-/subscriptions/90c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName@50342",
        "state": "Enabled",
        "tenantId": "933a8f0e-ec41-4e69-8ad8-971zc4b533ll",
        "user": {
          "name": "userAssignedIdentity",
          "type": "servicePrincipal"
        }
      }
    ]  
    user@vmLinux:~$ vmLocation=$(az resource list -n vmLinux --query [*].location --out tsv)
    user@vmLinux:~$ echo The VM region location is $vmLocation
    The VM region location is westcentralus
    ```

## <a name="resource-ids-for-azure-services"></a>ID di risorsa per i servizi di Azure

Per un elenco di risorse che supportano Azure AD e che sono state testate con l'identità del servizio gestito e i relativi ID di risorsa, vedere [Servizi di Azure che supportano l'autenticazione di Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication).

## <a name="error-handling-guidance"></a>Istruzioni per la gestione degli errori 

Le risposte seguenti possono indicare che non è stato configurato correttamente il file MSI:

- Interfaccia della riga di comando: *MSI: Failed to retrieve a token from 'http://localhost:50342/oauth2/token' with an error of 'HTTPConnectionPool(host='localhost', port=50342)* (Identità del servizio gestito: Impossibile recuperare un token da 'http://localhost:50342/oauth2/token' con un errore 'HTTPConnectionPool(host='localhost', port=50342)) 

Se si riceve uno di questi errori, tornare alla macchina virtuale di Azure nel [portale di Azure](https://portal.azure.com) e:

- Andare alla pagina **Configurazione** e assicurarsi che l'opzione "Identità del servizio gestito" sia impostata su "Sì".
- Andare alla pagina **Estensioni** e verificare che l'estensione dell'identità del servizio gestito sia stata distribuita correttamente.

Se una è errato, è necessario riassegnare il file MSI per la risorsa nuovamente o risolvere il problema di distribuzione. Vedere [configurare una macchina virtuale gestita servizio identità (MSI) mediante Azure CLI](msi-qs-configure-cli-windows-vm.md) se occorre assistenza nella configurazione della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

- Per abilitare MSI in una macchina virtuale di Azure, vedere [configurare una macchina virtuale gestita servizio identità (MSI) mediante Azure CLI](msi-qs-configure-cli-windows-vm.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.








