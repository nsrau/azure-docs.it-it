---
title: Montare l'archiviazione BLOB di Azure usando il protocollo NFS 3,0 (anteprima) | Microsoft Docs
description: Informazioni su come montare un contenitore nell'archiviazione BLOB da una macchina virtuale (VM) di Azure o da un client in esecuzione in locale usando il protocollo NFS 3,0.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 09206b8189f03a37f8bd7d073238609a3f1bd3ad
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816100"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>Montare l'archiviazione BLOB usando il protocollo NFS (Network File System) 3,0 (anteprima)

È possibile montare un contenitore nell'archiviazione BLOB da una macchina virtuale (VM) di Azure basata su Windows o Linux o un sistema Windows o Linux eseguito in locale usando il protocollo NFS 3,0. Questo articolo fornisce istruzioni dettagliate. Per altre informazioni sul supporto del protocollo NFS 3,0 nell'archivio BLOB, vedere [supporto del protocollo NFS (Network File System) 3,0 nell'archivio BLOB di Azure (anteprima)](network-file-system-protocol-support.md).

> [!NOTE]
> Il supporto del protocollo NFS 3,0 nell'archivio BLOB di Azure è disponibile in anteprima pubblica ed è disponibile nelle aree seguenti: Stati Uniti orientali, Stati Uniti centrali, Stati Uniti centro-occidentali, Australia sudorientale, Europa settentrionale, Regno Unito occidentale, Corea centrale, Corea meridionale e Canada centrale.

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>Passaggio 1: registrare la funzionalità del protocollo NFS 3,0 con la sottoscrizione

1. Aprire una finestra di comando di PowerShell. 

2. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate.

   ```powershell
   Connect-AzAccount
   ```

3. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

4. Registrare la `AllowNFSV3` funzionalità usando il comando seguente.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. Registrare la `PremiumHns` funzionalità anche usando il comando seguente.

   ```powershell
   Register-AzProviderFeature -FeatureName PremiumHns -ProviderNamespace Microsoft.Storage  
   ```

6. Registrare il provider di risorse usando il comando seguente.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>Passaggio 2: verificare che la funzionalità sia registrata 

L'approvazione della registrazione può richiedere fino a un'ora. Per verificare che la registrazione sia stata completata, usare i comandi seguenti.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName PremiumHns  
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>Passaggio 3: creare una rete virtuale di Azure (VNet)

L'account di archiviazione deve essere contenuto all'interno di un VNet. Un VNet consente ai client di connettersi in modo sicuro all'account di archiviazione. Per ulteriori informazioni su VNet e su come crearne uno, vedere la [documentazione relativa alla rete virtuale](https://docs.microsoft.com/azure/virtual-network/).

> [!NOTE]
> I client nella stessa VNet possono montare i contenitori nell'account. È anche possibile montare un contenitore da un client in esecuzione in una rete locale, ma è necessario prima connettere la rete locale alla VNet. Vedere [connessioni di rete supportate](network-file-system-protocol-support.md#supported-network-connections).

## <a name="step-4-configure-network-security"></a>Passaggio 4: configurare la sicurezza di rete

L'unico modo per proteggere i dati nell'account consiste nell'usare un VNet e altre impostazioni di sicurezza di rete. Qualsiasi altro strumento utilizzato per proteggere i dati, inclusi l'autorizzazione della chiave dell'account, la sicurezza Azure Active Directory (AD) e gli elenchi di controllo di accesso (ACL), non sono ancora supportati negli account in cui è abilitato il supporto del protocollo NFS 3,0. 

Per proteggere i dati nell'account, vedere questi consigli: [consigli sulla sicurezza di rete per l'archiviazione BLOB](security-recommendations.md#networking).

## <a name="step-5-create-and-configure-a-storage-account"></a>Passaggio 5: creare e configurare un account di archiviazione

Per montare un contenitore usando NFS 3,0, è necessario creare un account di archiviazione **dopo aver** registrato la funzionalità con la sottoscrizione. Non è possibile abilitare gli account esistenti prima della registrazione della funzionalità. 

Nella versione di anteprima di questa funzionalità il protocollo NFS 3,0 è supportato solo negli account [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) .

Quando si configura l'account, scegliere i valori seguenti:

|Impostazione | Valore|
|----|---|
|Location|Una delle aree seguenti: Stati Uniti orientali, Stati Uniti centrali, Stati Uniti centro-occidentali, Australia sudorientale, Europa settentrionale, Regno Unito occidentale, Corea centrale, Corea meridionale e Canada centrale |
|Prestazioni|Premium|
|Tipo di account|BlockBlobStorage|
|Replica|Archiviazione con ridondanza locale|
|Metodo di connettività|Endpoint pubblico (reti selezionate) o endpoint privato|
|Trasferimento sicuro obbligatorio|Disabled|
|Spazio dei nomi gerarchico|Abilitato|
|NFS V3|Abilitato|

È possibile accettare i valori predefiniti per tutte le altre impostazioni. 

## <a name="step-6-create-a-container"></a>Passaggio 6: Creare un contenitore

Creare un contenitore nell'account di archiviazione usando uno di questi strumenti o SDK:

|Strumenti|SDK|
|---|---|
|[Azure portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-blobs.md#create-a-container)|[Java](data-lake-storage-directory-file-acl-java.md#create-a-container)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Interfaccia della riga di comando di Azure](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](https://docs.microsoft.com/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>Passaggio 7: montare il contenitore

Creare una directory nel sistema Windows o Linux e quindi montare un contenitore nell'account di archiviazione.

### <a name="linux"></a>[Linux](#tab/linux)

1. In un sistema Linux creare una directory.

   ```
   mkdir -p /mnt/test
   ```

2. Montare un contenitore usando il comando seguente.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Sostituire il `<storage-account-name>` segnaposto visualizzato in questo comando con il nome dell'account di archiviazione.  

   - Sostituire il `<container-name>` segnaposto con il nome del contenitore.


### <a name="windows"></a>[Windows](#tab/windows)

1. Aprire la finestra di dialogo **funzionalità Windows** e quindi attivare la funzionalità **client per NFS** . 

   ![Funzionalità client per file System di rete](media/network-file-system-protocol-how-to/client-for-network-files-system-feature.png)

2. Montare un contenitore usando il comando [Mount](https://docs.microsoft.com/windows-server/administration/windows-commands/mount) .

   ```
   mount -o nolock <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name> *
   ```

   - Sostituire il `<storage-account-name>` segnaposto visualizzato in questo comando con il nome dell'account di archiviazione.  

   - Sostituire il `<container-name>` segnaposto con il nome del contenitore.

3. Se sono necessarie autorizzazioni di scrittura, potrebbe essere necessario modificare l'UID e il GID predefiniti usati da Windows per connettersi alla condivisione. A tale scopo, eseguire i seguenti comandi di PowerShell come amministratore:

   ```
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousUid -PropertyType DWord -Value 0
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousGid -PropertyType DWord -Value 0
   ```
   
   - Riavviare il servizio client NFS o riavviare il server dopo avere apportato questa modifica.

---

## <a name="resolve-common-issues"></a>Risolvere i problemi comuni

|Problema/errore | Risoluzione|
|---|---|
|`Access denied by server while mounting`|Verificare che il client sia in esecuzione all'interno di una subnet supportata. Vedere i [percorsi di rete supportati](network-file-system-protocol-support.md#supported-network-connections).|
|`No such file or directory`| Verificare che il contenitore che si sta montando sia stato creato dopo aver verificato la registrazione della funzionalità. Vedere [passaggio 2: verificare che la funzionalità sia registrata](#step-2-verify-that-the-feature-is-registered). Assicurarsi anche di digitare il comando di montaggio e i relativi parametri direttamente nel terminale. Se si copiano e si incollano tutte le parti di questo comando nel terminale da un'altra applicazione, i caratteri nascosti nelle informazioni incollate potrebbero causare la visualizzazione di questo errore.|

## <a name="see-also"></a>Vedere anche

[Supporto del protocollo NFS (Network File System) 3,0 nell'archivio BLOB di Azure (anteprima)](network-file-system-protocol-support.md)







