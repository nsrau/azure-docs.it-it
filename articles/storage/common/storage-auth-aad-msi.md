---
title: Eseguire l'autenticazione con Azure AD da un'identità del servizio gestita di una macchina virtuale di Azure (anteprima) | Microsoft Docs
description: Eseguire l'autenticazione con Azure AD da un'identità del servizio gestita di una macchina virtuale di Azure (anteprima).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 080cb3ee536227e5ddce3fac856de79b2b061dcf
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970769"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>Eseguire l'autenticazione con Azure AD da un'identità del servizio gestita di una macchina virtuale di Azure (anteprima)

Archiviazione di Azure supporta l'autenticazione di Azure Active Directory (Azure AD) con un'[identità del servizio gestita](../../active-directory/managed-service-identity/overview.md). L'identità del servizio gestita fornisce un'identità gestita automaticamente in Azure Active Directory (Azure AD). È possibile usare l'identità del servizio gestita per eseguire l'autenticazione in Archiviazione di Azure da applicazioni in esecuzione in macchine virtuali, app per le funzioni, set di scalabilità di macchine virtuali di Azure e altro ancora. Usando l'identità del servizio gestita e sfruttando tutte le potenzialità dell'autenticazione di Azure AD, è possibile evitare di archiviare le credenziali con le applicazioni in esecuzione nel cloud.  

Per concedere le autorizzazioni a un'identità del servizio gestita per contenitori o code di archiviazione, è necessario assegnare un ruolo di controllo degli accessi in base al ruolo che comprenda le autorizzazioni di archiviazione per l'identità del servizio gestita. Per altre informazioni sui ruoli di controllo degli accessi in base al ruolo per l'archiviazione, vedere [Gestire i diritti di accesso a dati di archiviazione con il controllo degli accessi in base al ruolo (anteprima)](storage-auth-aad-rbac.md). 

> [!IMPORTANT]
> Questa versione di anteprima è destinata solo per l'uso in ambienti non di produzione. Non saranno disponibili contratti di servizio per ambienti di produzione finché l'integrazione di Azure AD per Archiviazione di Azure non verrà dichiarata disponibile a livello generale. Se l'integrazione di Azure AD non è ancora supportata per il proprio scenario, continuare a usare l'autorizzazione con chiave condivisa o token di firma di accesso condiviso nelle applicazioni. Per altre informazioni sulla versione di anteprima, vedere [Autenticare l'accesso ad Archiviazione di Azure tramite Azure Active Directory (anteprima)](storage-auth-aad.md).
>
> Durante l'anteprima, la propagazione delle assegnazioni dei ruoli di controllo degli accessi in base al ruolo può richiedere fino a cinque minuti.

Questo articolo mostra come eseguire l'autenticazione in Archiviazione di Azure con un'identità del servizio gestita da una macchina virtuale di Azure.  

## <a name="enable-msi-on-the-vm"></a>Abilitare l'identità del servizio gestita nella macchina virtuale

Prima di poter usare l'identità del servizio gestita per eseguire l'autenticazione in Archiviazione di Azure dalla macchina virtuale, è necessario abilitare l'identità del servizio gestita nella macchina virtuale. Per informazioni su come abilitare l'identità del servizio gestita, vedere uno di questi articoli:

- [Portale di Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-service-identity/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](../../active-directory/managed-service-identity/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../../active-directory/managed-service-identity/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-service-identity/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>Ottenere un token di accesso per l'identità del servizio gestita

Per eseguire l'autenticazione con l'identità del servizio gestita, l'applicazione o lo script deve acquisire un token di accesso per l'identità del servizio gestita. Per altre informazioni su come acquisire un token di accesso, vedere [Come usare un'identità del servizio gestita di una macchina virtuale di Azure per l'acquisizione di token](../../active-directory/managed-service-identity/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Esempio di codice .NET: creare un BLOB in blocchi

L'esempio di codice presuppone la disponibilità di un token di accesso per l'identità del servizio gestita. Il token di accesso viene usato per autorizzare l'identità del servizio gestita per la creazione di un BLOB in blocchi.

### <a name="add-references-and-using-statements"></a>Aggiungere riferimenti e istruzioni using  

In Visual Studio installare la versione di anteprima della libreria client di Archiviazione di Azure. Scegliere **Gestione pacchetti NuGet** dal menu **Strumenti** e quindi fare clic su **Console di Gestione pacchetti**. Digitare il comando seguente nella console:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Aggiungere le istruzioni using seguenti al codice:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>Creare credenziali dal token di accesso dell'identità del servizio gestita

Per creare il BLOB in blocchi, usare la classe **TokenCredentials** fornita dal pacchetto di anteprima. Creare una nuova istanza di **TokenCredentials**, passando il token di accesso dell'identità del servizio gestita ottenuto in precedenza:

```dotnet
// Create storage credentials from your MSI access token.
TokenCredential tokenCredential = new TokenCredential(msiAccessToken);
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
