---
title: Eseguire i comandi dell'interfaccia della riga di comando di Azure o di PowerShell con un'identità di Azure AD per accedere ad Archiviazione di Azure (anteprima) | Microsoft Docs
description: L'interfaccia della riga di comando di Azure e PowerShell supportano l'accesso con un'identità di Azure AD per eseguire comandi sui contenitori e le code di Archiviazione di Azure e i relativi dati. Un token di accesso viene fornito per la sessione e usato per autorizzare la chiamata delle operazioni. Le autorizzazioni variano in base al ruolo assegnato all'identità di Azure AD.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/30/2018
ms.author: tamram
ms.openlocfilehash: 98af46707485d1ab49e7d8c6fb1729e6edc6b2ff
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235866"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Usare un'identità di Azure AD per accedere ad Archiviazione di Azure con l'interfaccia della riga di comando o PowerShell (anteprima)

Archiviazione di Azure offre estensioni in anteprima per l'interfaccia della riga di comando di Azure e PowerShell, che consentono di accedere ed eseguire comandi di scripting in un'identità di Azure Active Directory (Azure AD). L'identità di Azure AD può essere un utente, un gruppo o un'entità servizio dell'applicazione oppure può essere un'[identità del servizio gestito](../../active-directory/managed-service-identity/overview.md). È possibile assegnare autorizzazioni per accedere alle risorse di archiviazione per l'identità di Azure AD tramite il controllo degli accessi in base al ruolo (RBAC). Per altre informazioni sui ruoli RBAC in Archiviazione di Azure, vedere [Gestire i diritti di accesso a dati di Archiviazione di Azure con il controllo degli accessi in base al ruolo (anteprima)](storage-auth-aad-rbac.md).

Quando si accede all'interfaccia della riga di comando di Azure o a PowerShell con un'identità di Azure AD, viene restituito un token di accesso per l'accesso ad Archiviazione di Azure con questa identità. Tale token viene quindi usato automaticamente dall'interfaccia della riga di comando o da PowerShell per autorizzare le operazioni su Archiviazione di Azure. Per le operazioni supportate, non è più necessario passare un chiave dell'account o un token di firma di accesso condiviso con il comando.

> [!IMPORTANT]
> Questa versione di anteprima è destinata solo per l'uso in ambienti non di produzione. Non saranno disponibili contratti di servizio per ambienti di produzione finché l'integrazione di Azure AD per Archiviazione di Azure non verrà dichiarata disponibile a livello generale. Se l'integrazione di Azure AD non è ancora supportata per il proprio scenario, continuare a usare l'autorizzazione basata su chiave condivisa o token di firma di accesso condiviso nelle applicazioni. Per altre informazioni sulla versione di anteprima, vedere [Autenticare l'accesso ad Archiviazione di Azure tramite Azure Active Directory (anteprima)](storage-auth-aad.md).
>
> Durante l'anteprima, la propagazione delle assegnazioni dei ruoli di controllo degli accessi in base al ruolo può richiedere fino a cinque minuti.
>
> L'integrazione di Azure AD con Archiviazione di Azure richiede l'uso di HTTPS per le operazioni di Archiviazione di Azure.

## <a name="supported-operations"></a>Operazioni supportate

Le estensioni in anteprima sono supportate per le operazioni su contenitori e code. Le operazioni che è possibile chiamare dipendono dalle autorizzazioni concesse all'identità di Azure AD usata per accedere all'interfaccia della riga di comando di Azure o a PowerShell. Le autorizzazioni per i contenitori o le code di Archiviazione di Azure vengono assegnate tramite il controllo degli accessi in base al ruolo (RBAC). Ad esempio, se all'identità viene assegnato un ruolo di Lettore dei dati, è possibile eseguire i comandi di scripting che leggono i dati da un contenitore o una coda. Se all'identità viene assegnato un ruolo di Collaboratore ai dati, è possibile eseguire i comandi di scripting per la lettura, scrittura o eliminazione di un contenitore o coda o dei dati in essi contenuti. 

Per informazioni dettagliate sulle autorizzazioni necessarie per ogni operazione di Archiviazione di Azure su un contenitore o una coda, vedere [Permissions for calling REST operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations) (Autorizzazioni per la chiamata di operazioni REST).  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>Chiamare i comandi dell'interfaccia della riga di comando con un'identità di Azure AD

Per installare l'estensione in anteprima per l'interfaccia della riga di comando di Azure:

1. Verificare che sia installata l'interfaccia della riga di comando di Azure versione 2.0.32 o successiva. Eseguire `az --version` per controllare la versione installata.
2. Eseguire il comando seguente per installare l'estensione in anteprima: 

    ```azurecli
    az extension add -n storage-preview
    ```

L'estensione in anteprima aggiunge un nuovo parametro `--auth-mode` ai comandi supportati:

- Impostare il parametro `--auth-mode` su `login` per eseguire l'accesso con un'identità di Azure AD.
- Impostare il parametro `--auth-mode` sul valore legacy `key` per tentare di eseguire una query per ottenere una chiave dell'account se non vengono forniti parametri di autenticazione per l'account. 

Ad esempio, per scaricare un BLOB nell'interfaccia della riga di comando di Azure usando un'identità di Azure AD, eseguire prima `az login` e quindi chiamare il comando con `--auth-mode` impostato su `login`:

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

La variabile di ambiente associata al parametro `--auth-mode` è `AZURE_STORAGE_AUTH_MODE`.

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>Chiamare i comandi di PowerShell con un'identità di Azure AD

Per usare Azure PowerShell per eseguire l'accesso con un'identità di Azure AD:

1. Assicurarsi di avere la versione più recente di PowerShellGet installata. Eseguire il comando seguente per installare la versione più recente:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

2. Disinstallare eventuali installazioni precedenti di Azure PowerShell.
3. Installare AzureRM:

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

4. Installare il modulo di anteprima:

    ```powershell
    Install-Module -Name Azure.Storage -AllowPrerelease –AllowClobber 
    ```

5. Chiamare il cmdlet [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) per creare un contesto e includere il parametro `-UseConnectedAccount`. 
6. Per chiamare un cmdlet con un'identità di Azure AD, passare il contesto al cmdlet.

L'esempio seguente mostra come elencare i BLOB in un contenitore da Azure PowerShell usando un'identità di Azure AD: 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -UseConnectedAccount 
Get-AzureStorageBlob -Container $sample-container -Context $ctx 
```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui ruoli RBAC per Archiviazione di Azure, vedere [Manage access rights to storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Gestire i diritti di accesso ai dati di archiviazione con il controllo degli accessi in base al ruolo - anteprima).
- Per informazioni sull'uso dell'identità del servizio gestita con Archiviazione di Azure, vedere [Eseguire l'autenticazione con Azure AD da un'identità del servizio gestita di Azure (anteprima)](storage-auth-aad-msi.md).
- Per informazioni su come autorizzare l'accesso a contenitori e code all'interno delle applicazioni di archiviazione, vedere [Usare Azure AD con applicazioni di archiviazione](storage-auth-aad-app.md).
- Per altre informazioni sull'integrazione di Azure AD per BLOB e code di Azure, vedere il post di blog del team di Archiviazione di Azure sull'[annuncio della versione di anteprima di Autenticazione di Azure AD per Archiviazione di Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
