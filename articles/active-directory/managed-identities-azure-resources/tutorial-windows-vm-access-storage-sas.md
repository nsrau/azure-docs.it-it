---
title: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Archiviazione di Azure tramite credenziali di firma di accesso condiviso
description: Esercitazione che illustra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Archiviazione di Azure tramite credenziali di firma di accesso condiviso anziché tramite una chiave di accesso dell'account di archiviazione.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/24/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23ec4d2a67beb9b5f903aa0b7f03196b47db3f78
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66226445"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Archiviazione di Azure tramite credenziali di firma di accesso condiviso

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come usare un'identità assegnata dal sistema per una macchina virtuale Windows per ottenere credenziali di firma di accesso condiviso di archiviazione. In particolare, [credenziali di firma di accesso condiviso del servizio](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

La firma di accesso condiviso del servizio offre la possibilità di concedere accesso limitato agli oggetti in un account di archiviazione per un periodo di tempo limitato e per un servizio specifico (in questo caso, il servizio BLOB) senza esporre la chiave di accesso di un account. È possibile usare credenziali di firma di accesso condiviso come di consueto durante le operazioni di archiviazione, ad esempio quando si usa Storage SDK. In questa esercitazione viene illustrato come caricare e scaricare un oggetto BLOB tramite PowerShell di Archiviazione di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Creare un account di archiviazione
> * Concedere alla macchina virtuale l'accesso alla firma di accesso condiviso dell'account di archiviazione in Resource Manager 
> * Ottenere un token di accesso tramite l'identità della macchina virtuale e usarlo per recuperare la firma di accesso condiviso da Resource Manager 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>Creare un account di archiviazione 

Se non ne è già disponibile uno, creare un account di archiviazione. È anche possibile ignorare questo passaggio e concedere all'identità gestita assegnata dal sistema della macchina virtuale l'accesso alle credenziali di firma di accesso condiviso di un account di archiviazione esistente. 

1. Fare clic sul pulsante **+/Crea nuovo servizio** nell'angolo superiore sinistro del portale di Azure.
2. Fare clic su **Archiviazione**, quindi **Account di archiviazione**. Viene visualizzato un nuovo pannello "Crea account di archiviazione".
3. Immettere un nome per l'account di archiviazione, che verrà usato in un secondo momento.  
4. **Modello di distribuzione** e **Tipologia account** devono essere impostati su "Gestione di risorse" e "Utilizzo generico". 
5. Verificare che le impostazioni in **Sottoscrizione** e **Gruppo di risorse** corrispondano a quelle specificate al momento della creazione della macchina virtuale nel passaggio precedente.
6. Fare clic su **Create**(Crea).

    ![Creare un nuovo account di archiviazione](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Creare un contenitore BLOB nell'account di archiviazione

Successivamente verrà caricato e scaricato un file per il nuovo account di archiviazione. Poiché i file richiedono l'archiviazione BLOB, è necessario creare un contenitore BLOB in cui archiviare il file.

1. Tornare all'account di archiviazione appena creato.
2. Fare clic sul collegamento **Contenitori** nel pannello a sinistra, in "Servizio BLOB".
3. Fare clic su **+ Contenitore** nella parte superiore della pagina e verrà visualizzato il pannello "Nuovo contenitore".
4. Assegnare un nome al contenitore, selezionare un livello di accesso, quindi fare clic su **OK**. Il nome specificato verrà usato più avanti nell'esercitazione. 

    ![Creare un contenitore di archiviazione](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>Concedere l'accesso per l'uso di una firma di accesso condiviso di archiviazione all'identità gestita assegnata dal sistema della macchina virtuale 

Archiviazione di Azure non supporta l'autenticazione di Azure AD in modo nativo.  È tuttavia possibile usare un'identità gestita per recuperare una firma di accesso condiviso di archiviazione da Gestione risorse e quindi usarla per accedere alle risorse di archiviazione.  In questo passaggio si concede l'accesso alla firma di accesso condiviso dell'account di archiviazione all'identità gestita assegnata dal sistema della macchina virtuale.   

1. Tornare all'account di archiviazione appena creato.   
2. Fare clic sul collegamento **Controllo di accesso (IAM)** nel pannello di sinistra.  
3. Fare clic su **+ Aggiungi assegnazione di ruolo** nella parte superiore della pagina per aggiungere una nuova assegnazione di ruolo per la macchina virtuale.
4. Impostare **Ruolo** su "Collaboratore Account di archiviazione" sul lato destro della pagina.  
5. Nell'elenco a discesa successivo impostare **Assegna accesso a** sulla risorsa "Macchina virtuale".  
6. Assicurarsi quindi che la sottoscrizione appropriata sia presente nell'elenco a discesa **Sottoscrizione** e quindi impostare **Gruppo di risorse** su "Tutti i gruppi di risorse".  
7. In **Seleziona** scegliere infine la macchina virtuale Windows nell'elenco a discesa e quindi fare clic su **Salva**. 

    ![Testo immagine alt](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure Resource Manager 

Il resto dell'esercitazione prevede che le operazioni vengano svolte dalla macchina virtuale creata in precedenza.

In questa sezione è necessario usare i cmdlet PowerShell di Azure Resource Manager.  Se non è già stato installato, [scaricare la versione più recente](https://docs.microsoft.com/powershell/azure/overview) prima di continuare.

1. Nel portale di Azure passare a **Macchine virtuali** selezionare la propria VM Windows, quindi nella pagina **Panoramica** fare clic su **Connetti** nella parte superiore.
2. In **Nome utente** e **Password** immettere i valori specificati al momento della creazione della macchina virtuale Windows. 
3. Ora che si è creata una **connessione Desktop remoto** con la macchina virtuale, aprire PowerShell nella sessione remota. 
4. Usando Invoke-WebRequest di PowerShell, eseguire una richiesta all'endpoint locale dell'identità gestita per le risorse di Azure per ottenere un token di accesso per Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Il valore del parametro "risorsa" deve corrispondere esattamente a quello previsto da Azure AD. Quando si usa l'ID risorsa di Azure Resource Manager, è necessario includere la barra finale nell'URI.
    
    Estrarre quindi l'elemento "Content", archiviato come stringa in formato JSON (JavaScript Object Notation) nell'oggetto $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Estrarre poi il token di accesso dalla risposta.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Ottenere credenziali di firma di accesso condiviso da Azure Resource Manager per eseguire chiamate al servizio di archiviazione 

Di seguito viene usato PowerShell per eseguire una chiamata a Resource Manager tramite il token di accesso recuperato nella sezione precedente e creare credenziali di firma di accesso condiviso di archiviazione. Dopo aver creato le credenziali di firma di accesso condiviso, è possibile chiamare operazioni di archiviazione.

Per creare le credenziali di firma di accesso condiviso in questa richiesta verranno usati i parametri della richiesta HTTP seguente:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Questi parametri sono inclusi nel corpo del comando POST della richiesta di credenziali di firma di accesso condiviso. Per altre informazioni sui parametri per la creazione di credenziali di firma di accesso condiviso, vedere le [informazioni di riferimento REST per la firma di accesso condiviso del servizio List](/rest/api/storagerp/storageaccounts/listservicesas).

Prima convertire i parametri in formato JSON, quindi chiamare l'endpoint `listServiceSas` di archiviazione per creare le credenziali di firma di accesso condiviso:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> L'URL fa distinzione tra maiuscole e minuscole. Accertarsi quindi di usare la stessa combinazione di maiuscole e minuscole usata in precedenza per il nome del gruppo di risorse, facendo attenzione alla maiuscola "G" in "resourceGroup". 

È ora possibile estrarre le credenziali di firma di accesso condiviso dalla risposta:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Se si esaminano le credenziali di firma di accesso condiviso è possibile vedere qualcosa di simile a quanto segue:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Verrà quindi creato un file denominato "test.txt". Usare quindi le credenziali di firma di accesso condiviso per eseguire l'autenticazione con il cmdlet `New-AzStorageContent`, caricare il file nel contenitore BLOB e quindi scaricare il file.

```bash
echo "This is a test text file." > test.txt
```

Assicurarsi di installare prima i cmdlet di Archiviazione di Azure tramite `Install-Module Azure.Storage`. Caricare quindi il BLOB appena creato, usando il cmdlet PowerShell `Set-AzStorageBlobContent`:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Risposta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

È anche possibile scaricare il BLOB appena caricato con il cmdlet `Get-AzStorageBlobContent` di PowerShell:

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Risposta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Archiviazione di Azure tramite credenziali di firma di accesso condiviso.  Per altre informazioni su Archiviazione di Azure SAS, consultare:

> [!div class="nextstepaction"]
>[Uso delle firme di accesso condiviso](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)


