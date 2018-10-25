---
title: Autenticare l'accesso a BLOB e code con identità gestite di Azure Active Directory per le risorse di Azure (anteprima) - Archiviazione di Azure | Microsoft Docs
description: Archiviazione di BLOB e coda di Azure supporta l'autenticazione con identità gestite di Azure Active Directory per le risorse di Azure. È possibile usare le identità gestite per le risorse di Azure per autenticare l'accesso a BLOB e code da applicazioni in esecuzione in macchine virtuali, app per le funzioni, set di scalabilità di macchine virtuali di Azure e altro ancora. Usando le identità gestite per le risorse di Azure e sfruttando tutte le potenzialità dell'autenticazione di Azure AD, è possibile evitare di archiviare le credenziali con le applicazioni in esecuzione nel cloud.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/05/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: c06dc83f8b460e6215448fbc9e4ac42c749b5922
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49354003"
---
# <a name="authenticate-access-to-blobs-and-queues-with-managed-identities-for-azure-resources-preview"></a>Autenticare l'accesso a BLOB e code con identità gestite per Risorse di Azure (anteprima)

Archiviazione di BLOB e coda di Azure supporta l'autenticazione con [identità gestite di Azure Active Directory per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md). È possibile usare le identità gestite per le risorse di Azure per autenticare l'accesso a BLOB e code da applicazioni in esecuzione in macchine virtuali, app per le funzioni, set di scalabilità di macchine virtuali di Azure e altro ancora. Usando le identità gestite per le risorse di Azure e sfruttando tutte le potenzialità dell'autenticazione di Azure AD, è possibile evitare di archiviare le credenziali con le applicazioni in esecuzione nel cloud.  

Per concedere autorizzazioni a un'identità gestita per un contenitore blob o una coda, si assegna un ruolo di controllo degli accessi in base al ruolo all'identità gestita che include le autorizzazioni per la risorsa nell'ambito appropriato. Per altre informazioni sui ruoli di controllo degli accessi in base al ruolo per l'archiviazione, vedere [Gestire i diritti di accesso a dati di archiviazione con il controllo degli accessi in base al ruolo (anteprima)](storage-auth-aad-rbac.md). 

Questo articolo mostra come eseguire l'autenticazione in Archiviazione BLOB di Azure o Coda con un'identità gestita da una macchina virtuale di Azure.  

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="enable-managed-identities-on-a-vm"></a>Abilitare le identità gestite su una macchina virtuale

Prima di poter utilizzare le identità gestite per le risorse di Azure per autenticare l'accesso a BLOB e code dalla macchina virtuale, abilitare le identità gestite per le risorse di Azure sulla macchina virtuale. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Portale di Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="get-a-managed-identity-access-token"></a>Ottenere token di accesso a un'identità gestita

Per eseguire l'autenticazione con un'identità gestita, l'applicazione o lo script deve acquisire un token di accesso di identità gestita. Per informazioni su come acquisire un token di accesso, vedere [Come usare le identità gestite per risorse di Azure in una macchina virtuale di Azure per acquisire un token di accesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Esempio di codice .NET: creare un BLOB in blocchi

L'esempio di codice presuppone la disponibilità di un token di accesso per l'identità gestita. Il token di accesso viene usato per autorizzare l'identità gestita per la creazione di un BLOB in blocchi.

### <a name="add-references-and-using-statements"></a>Aggiungere riferimenti e istruzioni using  

In Visual Studio installare la versione di anteprima della libreria client di Archiviazione di Azure. Scegliere **Gestione pacchetti NuGet** dal menu **Strumenti** e quindi fare clic su **Console di Gestione pacchetti**. Digitare il comando seguente nella console:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

Aggiungere le istruzioni using seguenti al codice:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>Creare credenziali dal token di accesso dell'identità gestita

Per creare il BLOB in blocchi, usare la classe **TokenCredentials** fornita dal pacchetto di anteprima. Creare una nuova istanza di **TokenCredentials**, passando il token di accesso dell'identità gestita ottenuto in precedenza:

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> L'integrazione di Azure AD con Archiviazione di Azure richiede l'uso di HTTPS per le operazioni di Archiviazione di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui ruoli RBAC per Archiviazione di Azure, vedere [Manage access rights to storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Gestire i diritti di accesso ai dati di archiviazione con il controllo degli accessi in base al ruolo - anteprima).
- Per informazioni su come autorizzare l'accesso a contenitori e code all'interno delle applicazioni di archiviazione, vedere [Usare Azure AD con applicazioni di archiviazione](storage-auth-aad-app.md).
- Per informazioni su come accedere all'interfaccia della riga di comando di Azure e a PowerShell con un'identità di Azure AD, vedere [Usare un'identità di Azure AD per accedere ad Archiviazione di Azure con l'interfaccia della riga di comando o PowerShell (anteprima)](storage-auth-aad-script.md).
- Per altre informazioni sull'integrazione di Azure AD per BLOB e code di Azure, vedere il post di blog del team di Archiviazione di Azure sull'[annuncio della versione di anteprima di Autenticazione di Azure AD per Archiviazione di Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
