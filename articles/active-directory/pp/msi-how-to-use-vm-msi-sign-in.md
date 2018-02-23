---
title: "Come usare un'identità del servizio gestito di una macchina virtuale di Azure per l'accesso"
description: "Istruzioni dettagliate ed esempi relativi all'uso di un'identità del servizio gestito di una macchina virtuale di Azure per l'accesso al client di script e alle risorse."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/05/2018
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c5c1be01947dba8b7f4ef8aa54aa6aedfb191d32
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="sign-in-using-a-vm-user-assigned-managed-service-identity-msi"></a>Accedere usando un'identità del servizio gestito assegnata dall'utente per una macchina virtuale

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]
Questo articolo contiene esempi di script dell'interfaccia della riga di comando per accedere usando l'entità servizio di un'identità del servizio gestito assegnata dall'utente, insieme alle linee guida su argomenti importanti come la gestione degli errori.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Per usare gli esempi dell'interfaccia della riga di comando di Azure forniti in questo articolo, assicurarsi di installare la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Tutti gli esempi di script in questo articolo presuppongono che il client da riga di comando sia in esecuzione su una macchina virtuale abilitata per l'identità del servizio gestito. Usare la funzionalità di connessione alla macchina virtuale nel portale di Azure per connettersi in remoto alla macchina virtuale. Per informazioni dettagliate sull'abilitazione di un'identità del servizio gestito in una macchina virtuale, vedere [Configure a VM Managed Service Identity (MSI) using Azure CLI](msi-qs-configure-cli-windows-vm.md) (Configurare l'identità del servizio gestito di una macchina virtuale tramite l'interfaccia della riga di comando di Azure) o una delle varianti dell'articolo (tramite PowerShell, il portale, un modello o Azure SDK). 
> - Per evitare errori durante l'accesso utente e alle risorse, è necessario che all'identità del servizio gestito venga assegnato almeno l'accesso "in lettura" nell'ambito appropriato (a livello di macchina virtuale o superiore) per consentire le operazioni di Azure Resource Manager sulla macchina virtuale. Per informazioni dettagliate, vedere [Assign a Managed Service Identity (MSI) access to a resource using Azure CLI](msi-howto-assign-access-cli.md) (Assegnare a un'identità del servizio gestito l'accesso a una risorsa tramite l'interfaccia della riga di comando di Azure).

## <a name="overview"></a>Panoramica

Un'identità del servizio gestito fornisce un'[entità servizio](~/articles/active-directory/develop/active-directory-dev-glossary.md#service-principal-object), [creata con l'abilitazione dell'identità del servizio gestito](msi-overview.md#how-does-it-work) nella macchina virtuale. All'entità servizio è possibile concedere l'accesso a risorse di Azure, usandola come identità tramite client di script/da riga di comando per l'accesso utente e alle risorse. In genere, per poter accedere a risorse protette con la propria identità, un client di script deve:  

   - essere registrato e autorizzato da Azure AD come applicazione client Web/riservata
   - accedere con la propria entità servizio usando le credenziali dell'app (probabilmente integrate nello script)

Con l'identità del servizio gestito, il client non deve più soddisfare questi requisiti e può accedere con l'entità servizio dell'identità del servizio gestito. 

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Lo script seguente illustra come:

1. Accedere ad Azure AD con l'entità servizio dell'identità del servizio gestito assegnata dall'utente.  
2. Chiamare Azure Resource Manager e ottenere la posizione dell'area di Azure per una macchina virtuale. L'interfaccia della riga di comando si occupa di gestire automaticamente l'acquisizione/uso dei token. Assicurarsi di sostituire il nome della macchina virtuale per `<VM NAME>` e l'ID risorsa dell'identità del servizio gestito assegnata dall'utente per `<MSI ID>`. L'ID risorsa dell'identità del servizio gestito viene restituito nella proprietà `id` durante la creazione di un'identità del servizio gestito assegnata dall'utente. Per esempi del comando `az identity create`, vedere [Configure a user-assigned Managed Service Identity (MSI) for a VM, using Azure CLI](msi-qs-configure-cli-windows-vm.md) (Configurare un'identità del servizio gestito assegnata dall'utente per una macchina virtuale tramite l'interfaccia utente di Azure).

    ```azurecli
    az login --msi –u <MSI ID>
   
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

Le risposte seguenti possono indicare che l'identità del servizio gestito non è stata configurata correttamente:

- Interfaccia della riga di comando: *MSI: Failed to retrieve a token from 'http://localhost:50342/oauth2/token' with an error of 'HTTPConnectionPool(host='localhost', port=50342)* (Identità del servizio gestito: Impossibile recuperare un token da 'http://localhost:50342/oauth2/token' con un errore 'HTTPConnectionPool(host='localhost', port=50342)) 

Se si riceve uno di questi errori, tornare alla macchina virtuale di Azure nel [portale di Azure](https://portal.azure.com) e:

- Andare alla pagina **Configurazione** e assicurarsi che l'opzione "Identità del servizio gestito" sia impostata su "Sì".
- Andare alla pagina **Estensioni** e verificare che l'estensione dell'identità del servizio gestito sia stata distribuita correttamente.

Se una delle due opzioni è errata, è necessario riassegnare l'identità del servizio gestito alla risorsa oppure risolvere il problema di distribuzione. Per informazioni sulla configurazione della macchina virtuale, vedere [Configure a VM Managed Service Identity (MSI) using Azure CLI](msi-qs-configure-cli-windows-vm.md) (Configurare l'identità del servizio gestito di una macchina virtuale tramite l'interfaccia della riga di comando di Azure).

## <a name="next-steps"></a>Passaggi successivi

- Per abilitare l'identità del servizio gestito in una macchina virtuale di Azure, vedere [Configure a VM Managed Service Identity (MSI) using Azure CLI](msi-qs-configure-cli-windows-vm.md) (Configurare l'identità del servizio gestito di una macchina virtuale tramite l'interfaccia della riga di comando di Azure).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.








