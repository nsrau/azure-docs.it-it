---
title: Come autorizzare l'accesso ai dati della coda con l'interfaccia della riga di comando di Azure
titleSuffix: Azure Storage
description: Specificare come autorizzare le operazioni sui dati della coda con l'interfaccia della riga di comando di Azure. È possibile autorizzare le operazioni sui dati usando Azure AD credenziali, con la chiave di accesso dell'account o con un token di firma di accesso condiviso (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: e753f5b09b6cd03744ba8520c668a8227e56e8a1
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637524"
---
# <a name="choose-how-to-authorize-access-to-queue-data-with-azure-cli"></a>Come autorizzare l'accesso ai dati della coda con l'interfaccia della riga di comando di Azure

Archiviazione di Azure fornisce estensioni per l'interfaccia della riga di comando di Azure che consentono di specificare come si desidera autorizzare le operazioni sui dati della coda. È possibile autorizzare le operazioni sui dati nei modi seguenti:

- Con un'entità di sicurezza Azure Active Directory (Azure AD). Microsoft consiglia di utilizzare le credenziali Azure AD per una maggiore sicurezza e semplicità d'uso.
- Con la chiave di accesso dell'account o un token di firma di accesso condiviso (SAS).

## <a name="specify-how-data-operations-are-authorized"></a>Specificare come vengono autorizzate le operazioni sui dati

I comandi dell'interfaccia della riga di comando di Azure per la lettura e scrittura dei dati della coda includono il `--auth-mode` parametro Specificare questo parametro per indicare come deve essere autorizzata un'operazione sui dati:

- Impostare il `--auth-mode` parametro su `login` per accedere usando un'entità di sicurezza Azure ad (scelta consigliata).
- Impostare il `--auth-mode` parametro sul valore legacy `key` per tentare di recuperare la chiave di accesso dell'account da usare per l'autorizzazione. Se si omette il `--auth-mode` parametro, l'interfaccia della riga di comando di Azure tenterà anche di recuperare la chiave di accesso.

Per usare il `--auth-mode` parametro, verificare di avere installato l'interfaccia della riga di comando di Azure versione 2.0.46 o successiva. Eseguire `az --version` per controllare la versione installata.

> [!IMPORTANT]
> Se si omette il `--auth-mode` parametro o lo si imposta su `key` , l'interfaccia della riga di comando di Azure tenterà di usare la chiave di accesso dell'account per l'autorizzazione. In questo caso, Microsoft consiglia di specificare la chiave di accesso nel comando o nella variabile di ambiente **AZURE_STORAGE_KEY** . Per ulteriori informazioni sulle variabili di ambiente, vedere la sezione [impostare le variabili di ambiente per i parametri di autorizzazione](#set-environment-variables-for-authorization-parameters).
>
> Se non si specifica la chiave di accesso, l'interfaccia della riga di comando di Azure tenterà di chiamare il provider di risorse di archiviazione di Azure per recuperarla per ogni operazione. L'esecuzione di molte operazioni sui dati che richiedono una chiamata al provider di risorse può causare la limitazione delle richieste. Per altre informazioni sui limiti dei provider di risorse, vedere [obiettivi di scalabilità e prestazioni per il provider di risorse di archiviazione di Azure](../common/scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Autorizzare con Azure AD credenziali

Quando si accede all'interfaccia della riga di comando di Azure con Azure AD credenziali, viene restituito un token di accesso OAuth 2,0. Il token viene usato automaticamente dall'interfaccia della riga di comando di Azure per autorizzare le operazioni sui dati successive sull'archiviazione di BLOB o code. Per le operazioni supportate, non è più necessario passare un chiave dell'account o un token di firma di accesso condiviso con il comando.

È possibile assegnare autorizzazioni per accodare i dati a un'entità di sicurezza Azure AD tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Per altre informazioni sui ruoli di Azure in archiviazione di Azure, vedere [gestire i diritti di accesso ai dati di archiviazione di Azure con RBAC di Azure](../common/storage-auth-aad-rbac-portal.md).

### <a name="permissions-for-calling-data-operations"></a>Autorizzazioni per la chiamata di operazioni sui dati

Le estensioni di archiviazione di Azure sono supportate per le operazioni sui dati della coda. Le operazioni che è possibile chiamare dipendono dalle autorizzazioni concesse all'entità di sicurezza Azure AD con cui si accede all'interfaccia della riga di comando di Azure. Le autorizzazioni per le code di archiviazione di Azure vengono assegnate tramite RBAC di Azure. Se, ad esempio, viene assegnato il ruolo **lettore dati della coda di archiviazione** , è possibile eseguire i comandi di scripting per leggere i dati da una coda. Se viene assegnato il ruolo di **collaboratore dei dati della coda di archiviazione** , è possibile eseguire i comandi di scripting per la lettura, la scrittura o l'eliminazione di una coda o dei dati in essi contenuti.

Per informazioni dettagliate sulle autorizzazioni necessarie per ogni operazione di archiviazione di Azure in una coda, vedere [chiamare le operazioni di archiviazione con token OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-queue-with-azure-ad-credentials"></a>Esempio: autorizzare un'operazione a creare una coda con credenziali Azure AD

L'esempio seguente illustra come creare una coda dall'interfaccia della riga di comando di Azure usando le credenziali Azure AD. Per creare la coda, è necessario accedere all'interfaccia della riga di comando di Azure e sono necessari un gruppo di risorse e un account di archiviazione.

1. Prima di creare la coda, assegnare il ruolo di [collaboratore dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni sui dati nell'account di archiviazione. Per altre informazioni sull'assegnazione di ruoli di Azure, vedere [usare la portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di Accodamento](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > La propagazione delle assegnazioni dei ruoli può richiedere alcuni minuti.

1. Chiamare il comando [AZ storage Queue create](/cli/azure/storage/queue#az-storage-queue-create) con il `--auth-mode` parametro impostato su `login` per creare la coda usando le credenziali Azure ad. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

    ```azurecli
    az storage queue create \
        --account-name <storage-account> \
        --name sample-queue \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autorizzare con la chiave di accesso dell'account

Se si possiede la chiave dell'account, è possibile chiamare qualsiasi operazione relativa ai dati di archiviazione di Azure. In generale, l'uso della chiave dell'account è meno sicuro. Se la chiave dell'account è compromessa, è possibile che tutti i dati dell'account siano compromessi.

Nell'esempio seguente viene illustrato come creare una coda usando la chiave di accesso dell'account. Specificare la chiave dell'account e specificare il `--auth-mode` parametro con il `key` valore:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autorizzare con un token di firma di accesso condiviso

Se si dispone di un token di firma di accesso condiviso, è possibile chiamare le operazioni sui dati consentite dalla firma di accesso condiviso. L'esempio seguente illustra come creare una coda usando un token SAS:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Impostare le variabili di ambiente per i parametri di autorizzazione

È possibile specificare i parametri di autorizzazione nelle variabili di ambiente per evitarne l'inclusione a ogni chiamata a un'operazione sui dati di archiviazione di Azure. Nella tabella seguente vengono descritte le variabili di ambiente disponibili.

| Variabile di ambiente                  | Descrizione                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    nome dell'account di archiviazione. Questa variabile deve essere usata in combinazione con la chiave dell'account di archiviazione o con un token di firma di accesso condiviso. Se non sono presenti, l'interfaccia della riga di comando di Azure tenta di recuperare la chiave di accesso dell'account di archiviazione usando l'account Azure AD autenticato. Se un numero elevato di comandi viene eseguito in una sola volta, è possibile che venga raggiunto il limite di limitazione del provider di risorse di archiviazione di Azure. Per altre informazioni sui limiti dei provider di risorse, vedere [obiettivi di scalabilità e prestazioni per il provider di risorse di archiviazione di Azure](../common/scalability-targets-resource-provider.md).             |
|    AZURE_STORAGE_KEY                  |    Chiave dell'account di archiviazione. Questa variabile deve essere usata insieme al nome dell'account di archiviazione.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Stringa di connessione che include la chiave dell'account di archiviazione o un token di firma di accesso condiviso. Questa variabile deve essere usata insieme al nome dell'account di archiviazione.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Token di firma di accesso condiviso (SAS). Questa variabile deve essere usata insieme al nome dell'account di archiviazione.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Modalità di autorizzazione con cui eseguire il comando. I valori consentiti sono `login` (scelta consigliata) o `key` . Se si specifica `login` , l'interfaccia della riga di comando di Azure usa le credenziali Azure ad per autorizzare l'operazione sui dati. Se si specifica la `key` modalità legacy, l'interfaccia della riga di comando di Azure tenterà di eseguire una query per la chiave di accesso dell'account e di autorizzare il comando con la chiave.    |

## <a name="next-steps"></a>Passaggi successivi

- [Usare l'interfaccia della riga di comando di Azure per assegnare un ruolo di Azure per l'accesso ai dati di Accodamento](../common/storage-auth-aad-rbac-cli.md)
- [Autorizzare l'accesso ai dati BLOB e di Accodamento con le identità gestite per le risorse di Azure](../common/storage-auth-aad-msi.md)