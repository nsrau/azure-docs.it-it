---
title: Autorizzare l'accesso ai dati BLOB o queue con l'interfaccia della riga di comando di AzureAuthorize access to blob or queue data with Azure CLI
titleSuffix: Azure Storage
description: Specificare come autorizzare le operazioni sui dati dei dati nei dati BLOB o della coda con l'interfaccia della riga di comando di Azure.Specify how to authorize data operations against blob or queue data with the Azure CLI. È possibile autorizzare le operazioni sui dati usando le credenziali di Azure AD, con la chiave di accesso dell'account o con un token di firma di accesso condiviso.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c7091592f8806b6f6655315ae1faace286c2c1f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207693"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>Autorizzare l'accesso ai dati BLOB o queue con l'interfaccia della riga di comando di AzureAuthorize access to blob or queue data with Azure CLI

Archiviazione di Azure offre estensioni per l'interfaccia della riga di comando di Azure che consentono di specificare come si desidera autorizzare le operazioni sui dati BLOB o sulle code. È possibile autorizzare le operazioni sui dati nei modi seguenti:You can authorize data operations in the following ways:

- Con un'entità di sicurezza di Azure Active Directory (Azure AD). Microsoft consiglia di usare le credenziali di Azure AD per una sicurezza e una maggiore facilità d'uso.
- Con la chiave di accesso dell'account o un token di firma di accesso condiviso.

## <a name="specify-how-data-operations-are-authorized"></a>Specificare la modalità di autorizzazione delle operazioni sui dati

I comandi dell'interfaccia della riga di `--auth-mode` comando di Azure per la lettura e la scrittura dei dati BLOB e coda includono il parametro facoltativo. Specificare questo parametro per indicare come deve essere autorizzata un'operazione sui dati:

- Impostare `--auth-mode` il `login` parametro su per accedere usando un'entità di sicurezza di Azure AD (scelta consigliata).
- Impostare `--auth-mode` il parametro sul valore legacy `key` per tentare di recuperare la chiave di accesso all'account da utilizzare per l'autorizzazione. Se si ometti `--auth-mode` il parametro, anche l'interfaccia della riga di comando di Azure tenta di recuperare la chiave di accesso.

Per usare `--auth-mode` il parametro, assicurarsi di aver installato l'interfaccia della riga di comando di Azure versione 2.0.46 o successiva. Eseguire `az --version` per controllare la versione installata.

> [!IMPORTANT]
> Se si ometti `--auth-mode` il parametro `key`o lo si imposta su , l'interfaccia della riga di comando di Azure tenta di usare la chiave di accesso dell'account per l'autorizzazione. In questo caso, Microsoft consiglia di fornire il tasto di scelta al comando o nella variabile di ambiente **AZURE_STORAGE_KEY.** Per ulteriori informazioni sulle variabili di ambiente, vedere la sezione relativa all'impostazione delle variabili di ambiente per i parametri di [autorizzazione.](#set-environment-variables-for-authorization-parameters)
>
> Se non si fornisce la chiave di accesso, l'interfaccia della riga di comando di Azure tenta di chiamare il provider di risorse di Archiviazione di Azure per recuperarla per ogni operazione. L'esecuzione di molte operazioni sui dati che richiedono una chiamata al provider di risorse può comportare la limitazione delle richieste. Per altre informazioni sui limiti dei provider di risorse, vedere Obiettivi di scalabilità e prestazioni per il provider di risorse di Archiviazione di Azure.For more information about resource provider limits, see [Scalability and performance targets for the Azure Storage resource provider.](scalability-targets-resource-provider.md)

## <a name="authorize-with-azure-ad-credentials"></a>Autorizzare con le credenziali di Azure ADAuthorize with Azure AD credentials

Quando si accede all'interfaccia della riga di comando di Azure con le credenziali di Azure AD, viene restituito un token di accesso OAuth 2.0.When you sign in to Azure CLI with Azure AD credentials, an OAuth 2.0 access token is returned. Tale token viene usato automaticamente dall'interfaccia della riga di comando di Azure per autorizzare le operazioni di dati successive sull'archiviazione BLOB o sulla coda. Per le operazioni supportate, non è più necessario passare un chiave dell'account o un token di firma di accesso condiviso con il comando.

È possibile assegnare autorizzazioni ai dati BLOB e coda a un'entità di sicurezza di Azure AD tramite il controllo degli accessi in base al ruolo. Per altre informazioni sui ruoli RBAC in Archiviazione di Azure, vedere Gestire i diritti di accesso ai dati di Archiviazione di [Azure con rbac.](storage-auth-aad-rbac.md)

### <a name="permissions-for-calling-data-operations"></a>Autorizzazioni per chiamare operazioni sui dati

Le estensioni di Archiviazione di Azure sono supportate per le operazioni sui dati BLOB e coda. Which operations you may call depends on the permissions granted to the Azure AD security principal with which you sign in to Azure CLI. Le autorizzazioni per i contenitori o le code di Archiviazione di Azure vengono assegnate tramite controllo degli accessi in base al ruolo. Ad esempio, se viene assegnato il ruolo **Lettore dati BLOB,** è possibile eseguire comandi di script che leggono i dati da un contenitore o da una coda. Se viene assegnato il ruolo **Collaboratore dati BLOB,** è possibile eseguire comandi di script che leggono, scrivono o eliminano un contenitore o una coda o i dati in essi contenuti.

Per informazioni dettagliate sulle autorizzazioni necessarie per ogni operazione di Archiviazione di Azure in un contenitore o in una coda, vedere [Operazioni di archiviazione delle chiamate con token OAuth.](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Esempio: Autorizzare un'operazione per creare un contenitore con credenziali di Azure ADExample: Authorize an operation to create a container with Azure AD credentials

L'esempio seguente mostra come creare un contenitore dall'interfaccia della riga di comando di Azure usando le credenziali di Azure AD. Per creare il contenitore, è necessario accedere all'interfaccia della riga di comando di Azure e sono necessari un gruppo di risorse e un account di archiviazione. Per informazioni su come creare queste risorse, vedere [Guida introduttiva: Creare, scaricare ed elencare BLOB con l'interfaccia della riga](../blobs/storage-quickstart-blobs-cli.md)di comando di Azure.To learn how to create these resources, see Quickstart: Create, download, and list blobs with Azure CLI.

1. Prima di creare il contenitore, assegnare il ruolo [Collaboratore dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni sui dati sull'account di archiviazione. Per altre informazioni sull'assegnazione di ruoli RBAC, vedere [Concedere l'accesso ai dati BLOB e coda](storage-auth-aad-rbac.md)di Azure con il controllo degli accessi in base al ruolo nel portale di Azure.For more information about assigning RBAC roles, see Grant access to Azure blob and queue data with RBAC in the Azure portal.

    > [!IMPORTANT]
    > La propagazione delle assegnazioni di ruolo RBAC può richiedere alcuni minuti.

1. Chiamare il comando az storage `--auth-mode` container `login` [create](/cli/azure/storage/container#az-storage-container-create) con il parametro impostato su per creare il contenitore usando le credenziali di Azure AD. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autorizzare con la chiave di accesso dell'account

Se si possiede la chiave dell'account, è possibile chiamare qualsiasi operazione sui dati di Archiviazione di Azure.If you possess the account key, you can call any Azure Storage data operation. In generale, l'utilizzo della chiave dell'account è meno sicuro. Se la chiave dell'account viene compromessa, tutti i dati del tuo account potrebbero essere compromessi.

Nell'esempio seguente viene illustrato come creare un contenitore usando la chiave di accesso dell'account. Specificare la chiave dell'account `key` e fornire al parametro il `--auth-mode` valore:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autorizzare con un token di firma di accesso condivisoAuthorize with a SAS token

Se si possiede un token di firma di accesso condiviso, è possibile chiamare le operazioni sui dati consentite dalla firma di accesso condiviso. L'esempio seguente mostra come creare un contenitore usando un token di firma di accesso condiviso:The following example shows how to create a container using a SAS token:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Impostare le variabili di ambiente per i parametri di autorizzazioneSet environment variables for authorization parameters

È possibile specificare i parametri di autorizzazione nelle variabili di ambiente per evitare di includerli a ogni chiamata a un'operazione dati di Archiviazione di Azure.You can specify authorization parameters in environment variables to avoid including them on every call to an Azure Storage data operation. Nella tabella seguente vengono descritte le variabili di ambiente disponibili.

| Variabile di ambiente                  | Descrizione                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    nome dell'account di archiviazione. Questa variabile deve essere usata insieme alla chiave dell'account di archiviazione o a un token di firma di accesso condiviso. Se non è presente alcuna volta, l'interfaccia della riga di comando di Azure tenta di recuperare la chiave di accesso dell'account di archiviazione usando l'account di Azure AD autenticato. Se viene eseguito un numero elevato di comandi contemporaneamente, è possibile che venga raggiunto il limite di limitazione del provider di risorse di Archiviazione di Azure.If a large number of commands are executed at one time, the Azure Storage resource provider throttling limit may be reached. Per altre informazioni sui limiti dei provider di risorse, vedere Obiettivi di scalabilità e prestazioni per il provider di risorse di Archiviazione di Azure.For more information about resource provider limits, see [Scalability and performance targets for the Azure Storage resource provider.](scalability-targets-resource-provider.md)             |
|    AZURE_STORAGE_KEY                  |    Chiave dell'account di archiviazione. Questa variabile deve essere usata insieme al nome dell'account di archiviazione.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Stringa di connessione che include la chiave dell'account di archiviazione o un token di firma di accesso condiviso. Questa variabile deve essere usata insieme al nome dell'account di archiviazione.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Token di firma di accesso condiviso. Questa variabile deve essere usata insieme al nome dell'account di archiviazione.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Modalità di autorizzazione con cui eseguire il comando. I valori `login` consentiti `key`sono (consigliati) o . Se si `login`specifica , l'interfaccia della riga di comando di Azure usa le credenziali di Azure AD per autorizzare l'operazione sui dati. Se si specifica `key` la modalità legacy, l'interfaccia della riga di comando di Azure tenta di eseguire una query per la chiave di accesso dell'account e di autorizzare il comando con la chiave.    |

## <a name="next-steps"></a>Passaggi successivi

- [Usare l'interfaccia della riga di comando di Azure per assegnare un ruolo Controllo degli accessi al controllo degli accessi ai dati BLOB e codaUse Azure CLI to assign an RBAC role for access to blob and queue data](storage-auth-aad-rbac-cli.md)
- [Autorizzare l'accesso ai dati BLOB e queue con identità gestite per le risorse di AzureAuthorize access to blob and queue data with managed identities for Azure resources](storage-auth-aad-msi.md)
