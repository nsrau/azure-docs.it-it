---
title: Eseguire i comandi dell'interfaccia della riga di comando di Azure con Azure AD credenziali per accedere ai dati di BLOB o
titleSuffix: Azure Storage
description: L'interfaccia della riga di comando di Azure supporta l'accesso con Azure AD credenziali per eseguire i comandi nei BLOB di archiviazione di Azure e accodare i dati. Un token di accesso viene fornito per la sessione e usato per autorizzare la chiamata delle operazioni. Le autorizzazioni dipendono dal ruolo RBAC assegnato alla Azure AD entità di sicurezza.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 4133c081823f1cc319480c1bc847b672df0374d4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660615"
---
# <a name="run-azure-cli-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Eseguire i comandi dell'interfaccia della riga di comando di Azure con Azure AD credenziali per accedere ai dati di BLOB o

Archiviazione di Azure fornisce estensioni per l'interfaccia della riga di comando di Azure che consentono di accedere ed eseguire comandi di script con credenziali Azure Active Directory (Azure AD). Quando si accede all'interfaccia della riga di comando di Azure con Azure AD credenziali, viene restituito un token di accesso OAuth 2,0. Il token viene usato automaticamente dall'interfaccia della riga di comando di Azure per autorizzare le operazioni sui dati successive sull'archiviazione di BLOB o code. Per le operazioni supportate, non è più necessario passare un chiave dell'account o un token di firma di accesso condiviso con il comando.

È possibile assegnare autorizzazioni per i dati BLOB e di Accodamento a un Azure AD entità di sicurezza tramite il controllo degli accessi in base al ruolo (RBAC). Per altre informazioni sui ruoli RBAC in archiviazione di Azure, vedere [gestire i diritti di accesso ai dati di archiviazione di Azure con RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Operazioni supportate

Le estensioni di archiviazione di Azure sono supportate per le operazioni sui dati BLOB e di Accodamento. Le operazioni che è possibile chiamare dipendono dalle autorizzazioni concesse all'entità di sicurezza Azure AD con cui si accede all'interfaccia della riga di comando di Azure. Le autorizzazioni per i contenitori o le code di archiviazione di Azure vengono assegnate tramite RBAC. Se ad esempio viene assegnato il ruolo **lettore dati BLOB** , è possibile eseguire i comandi di scripting per la lettura di dati da un contenitore o una coda. Se viene assegnato il ruolo di **collaboratore dati BLOB** , è possibile eseguire i comandi di scripting per la lettura, la scrittura o l'eliminazione di un contenitore o di una coda o dei dati in essi contenuti.

Per informazioni dettagliate sulle autorizzazioni necessarie per ogni operazione di archiviazione di Azure in un contenitore o in una coda, vedere [chiamare le operazioni di archiviazione con token OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-azure-cli-commands-using-azure-ad-credentials"></a>Chiamare i comandi CLI di Azure usando credenziali Azure AD

L'interfaccia della riga di comando di Azure supporta il parametro `--auth-mode` per le operazioni di dati BLOB e Queue:

- Impostare il parametro `--auth-mode` su `login` per accedere usando un'entità di sicurezza Azure AD.
- Impostare il parametro `--auth-mode` sul valore legacy `key` per tentare di eseguire una query per ottenere una chiave dell'account se non vengono forniti parametri di autenticazione per l'account.

L'esempio seguente illustra come creare un contenitore in un nuovo account di archiviazione dall'interfaccia della riga di comando di Azure usando le credenziali Azure AD. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

1. Assicurarsi di aver installato l'interfaccia della riga di comando di Azure versione 2.0.46 o successiva. Eseguire `az --version` per controllare la versione installata.

1. Eseguire `az login` ed eseguire l'autenticazione nella finestra del browser:

    ```azurecli
    az login
    ```

1. Specificare la sottoscrizione desiderata. Creare un gruppo di risorse usando [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Creare un account di archiviazione all'interno di tale gruppo di risorse usando [AZ storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create):

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_ZRS \
        --encryption-services blob
    ```

1. Prima di creare il contenitore, assegnare il ruolo di [collaboratore dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni sui dati nell'account di archiviazione. Per altre informazioni sull'assegnazione di ruoli RBAC, vedere [concedere l'accesso ai dati di Accodamento e BLOB di Azure con RBAC nel portale di Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Le assegnazioni di ruolo RBAC potrebbero richiedere alcuni minuti per la propagazione.

1. Chiamare il comando [AZ Storage container create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) con il parametro `--auth-mode` impostato su `login` per creare il contenitore usando le credenziali Azure ad:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

La variabile di ambiente associata al parametro `--auth-mode` è `AZURE_STORAGE_AUTH_MODE`. È possibile specificare il valore appropriato nella variabile di ambiente per evitare di includerlo a ogni chiamata a un'operazione sui dati di archiviazione di Azure.

## <a name="next-steps"></a>Passaggi successivi

- [Usare l'interfaccia della riga di comando di Azure per assegnare un ruolo RBAC per l'accesso ai dati BLOB e Queue](storage-auth-aad-rbac-cli.md)
- [Autorizzare l'accesso ai dati BLOB e di Accodamento con le identità gestite per le risorse di Azure](storage-auth-aad-msi.md)
