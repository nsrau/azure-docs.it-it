---
title: "Usare un'identità del servizio gestito per una macchina virtuale Windows per accedere ad Archiviazione di Azure"
description: "Questa esercitazione illustra come usare un'identità del servizio gestito per una macchina virtuale Windows per accedere ad Archiviazione di Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: bryanla
ms.openlocfilehash: e162134f52ceca9d77735893b847782d06e72cfe
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Usare un'identità del servizio gestito per una macchina virtuale Windows per accedere ad Archiviazione di Azure tramite una chiave di accesso

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come abilitare Identità del servizio gestito (MSI, Managed Service Identity) per una macchina virtuale Windows e quindi usare l'identità per recuperare le chiavi di accesso dell'account di archiviazione. È possibile usare le chiavi di accesso alle risorse di archiviazione come di consueto durante le operazioni di archiviazione, ad esempio quando si usa Storage SDK. In questa esercitazione si caricheranno e scaricheranno oggetti BLOB usando PowerShell in Archiviazione di Azure. Si apprenderà come:


> [!div class="checklist"]
> * Abilitare Identità del servizio gestita in una macchina virtuale Windows 
> * Concedere alla macchina virtuale l'accesso alle chiavi di accesso all'account di archiviazione in Resource Manager 
> * Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per ottenere le chiavi di accesso alle risorse di archiviazione da Resource Manager 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Windows in un nuovo gruppo di risorse

Per questa esercitazione si creerà una nuova macchina virtuale Windows, ma è anche possibile abilitare l'identità del servizio gestito in una macchina virtuale esistente.

1.  Fare clic sul pulsante **+/Crea nuovo servizio** nell'angolo superiore sinistro del portale di Azure.
2.  Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**. 
3.  Immettere le informazioni relative alla macchina virtuale. I valori di **Nome utente** e **Password** creati in questa schermata sono le credenziali usate per accedere alla macchina virtuale.
4.  Dall'elenco a discesa **Sottoscrizione** selezionare la sottoscrizione corretta per la macchina virtuale.
5.  Per selezionare un nuovo **Gruppo di risorse** in cui creare la macchina virtuale, scegliere **Crea nuovo**. Al termine fare clic su **OK**.
6.  Selezionare la dimensione della macchina virtuale. Per visualizzare altre dimensioni, selezionare **Visualizza tutto** o modificare il filtro **Supported disk type** (Tipo di disco supportato). Nel pannello delle impostazioni mantenere le impostazioni predefinite e fare clic su **OK**.

    ![Testo immagine alt](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Abilitare identità del servizio gestito nella macchina virtuale

Un'identità del servizio gestita per una macchina virtuale consente di ottenere i token di accesso da Azure AD senza dover inserire le credenziali nel codice. A livello sottostante, quando si abilita Identità del servizio gestita, si eseguono due operazioni, ovvero si installa l'estensione di Identità del servizio gestita nella macchina virtuale e si abilita la funzionalità per tale macchina.  

1. Passare al gruppo di risorse della nuova macchina virtuale e selezionare la macchina virtuale creata nel passaggio precedente.
2. Nella sezione "Impostazioni" della macchina virtuale a sinistra fare clic su **Configurazione**.
3. Per registrare e abilitare identità del servizio gestita, scegliere **Sì**. Se si vuole disabilitare questa funzionalità, scegliere No.
4. Assicurarsi di fare clic su **Salva** per salvare la configurazione.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Per verificare le estensioni installate nella macchina virtuale, fare clic su **Estensioni**. Se Identità del servizio gestito è abilitata, nell'elenco sarà inclusa la voce **ManagedIdentityExtensionforWindows**.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>Creare un account di archiviazione 

Se non ne è già disponibile uno, creare un account di archiviazione. È anche possibile ignorare questo passaggio e concedere alla macchina virtuale l'accesso Identità del servizio gestito alle chiavi di un account di archiviazione esistente. 

1. Fare clic sul pulsante **+/Crea nuovo servizio** nell'angolo superiore sinistro del portale di Azure.
2. Fare clic su **Archiviazione**, quindi **Account di archiviazione**. Viene visualizzato un nuovo pannello "Crea account di archiviazione".
3. Immettere un nome per l'account di archiviazione, che verrà usato in un secondo momento.  
4. **Modello di distribuzione** e **Tipologia account** devono essere impostati su "Gestione di risorse" e "Utilizzo generico". 
5. Verificare che le impostazioni in **Sottoscrizione** e **Gruppo di risorse** corrispondano a quelle specificate al momento della creazione della macchina virtuale nel passaggio precedente.
6. Fare clic su **Crea**.

    ![Creare un nuovo account di archiviazione](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Creare un contenitore BLOB nell'account di archiviazione

Successivamente verrà caricato e scaricato un file per il nuovo account di archiviazione. Poiché i file richiedono l'archiviazione BLOB, è necessario creare un contenitore BLOB in cui archiviare il file.

1. Tornare all'account di archiviazione appena creato.
2. Fare clic sul collegamento **Contenitori** a sinistra, in "Servizio BLOB".
3. Fare clic su **+ Contenitore** nella parte superiore della pagina e verrà visualizzato il pannello "Nuovo contenitore".
4. Assegnare un nome al contenitore, selezionare un livello di accesso, quindi fare clic su **OK**. Il nome specificato verrà usato più avanti nell'esercitazione. 

    ![Creare un contenitore di archiviazione](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-storage-account-access-keys"></a>Concedere alla macchina virtuale l'accesso con Identità del servizio gestito per l'utilizzo delle chiavi di accesso dell'account di archiviazione 

Archiviazione di Azure non supporta l'autenticazione di Azure AD in modo nativo.  È tuttavia possibile usare un'Identità del servizio gestito per recuperare le chiavi di accesso dell'account di archiviazione da Resource Manager e usare una chiave per accedere alle risorse di archiviazione.  In questo passaggio, alla macchina virtuale viene concesso l'accesso con Identità di servizio gestito alle chiavi dell'account di archiviazione.   

1. Tornare all'account di archiviazione appena creato.  
2. Fare clic sul collegamento **Controllo di accesso (IAM)** nel pannello di sinistra.  
3. Fare clic su **+Aggiungi** in cima alla pagina per aggiungere una nuova assegnazione di ruolo alla macchina virtuale.
4. Impostare **Ruolo** su "Ruolo del servizio dell'operatore della chiave dell'account di archiviazione", sul lato destro della pagina. 
5. Nell'elenco a discesa impostare **Assegna accesso a** sulla risorsa "Macchina virtuale".  
6. Assicurarsi quindi che la sottoscrizione appropriata sia presente nell'elenco a discesa **Sottoscrizione** e quindi impostare **Gruppo di risorse** su "Tutti i gruppi di risorse".  
7. In **Seleziona** scegliere infine la macchina virtuale Windows nell'elenco a discesa e quindi fare clic su **Salva**. 

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure Resource Manager 

Il resto dell'esercitazione prevede che le operazioni vengano svolte dalla macchina virtuale creata in precedenza. 

In questa sezione è necessario usare i cmdlet PowerShell di Azure Resource Manager.  Se non è già stato installato, [scaricare la versione più recente](https://docs.microsoft.com/powershell/azure/overview) prima di continuare.

1. Nel portale di Azure passare a **Macchine virtuali** selezionare la propria VM Windows, quindi nella pagina **Panoramica** fare clic su **Connetti** nella parte superiore. 
2. In **Nome utente** e **Password** immettere i valori specificati al momento della creazione della macchina virtuale Windows. 
3. Ora che si è creata una **connessione Desktop remoto** con la macchina virtuale, aprire PowerShell nella sessione remota.
4. Usando Invoke-WebRequest di PowerShell, eseguire una richiesta all'endpoint locale di Identità del servizio gestito per ottenere un token di accesso per Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
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
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Ottenere le chiavi di accesso dell'account di archiviazione da Azure Resource Manager per eseguire le chiamate al servizio di archiviazione  

Di seguito viene usato PowerShell per eseguire una chiamata a Resource Manager usando il token di accesso recuperato nella sezione precedente, per recuperare la chiave di accesso alle risorse di archiviazione. Una volta recuperata la chiave di accesso alle risorse di archiviazione, è possibile chiamare le operazioni di caricamento/scaricamento.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> L'URL fa distinzione tra maiuscole e minuscole. Accertarsi quindi di usare la stessa combinazione di maiuscole e minuscole usata in precedenza per il nome del gruppo di risorse, facendo attenzione alla maiuscola "G" in "resourceGroup". 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Verrà quindi creato un file denominato "test.txt". Usare quindi la chiave di accesso alle risorse di archiviazione per eseguire l'autenticazione con il cmdlet di `New-AzureStorageContent`, caricare il file nel contenitore BLOB, quindi scaricare il file.

```bash
echo "This is a test text file." > test.txt
```

Assicurarsi di installare i cmdlet di Archiviazione di Azure usando `Install-Module Azure.Storage`. Caricare quindi il BLOB appena creato, usando il cmdlet PowerShell `Set-AzureStorageBlobContent`:

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Risposta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

È anche possibile scaricare il BLOB appena caricato con il cmdlet `Get-AzureStorageBlobContent` di PowerShell:

```powershell
Get-AzureStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Risposta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```


## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'identità di servizio gestito, vedere [Panoramica dell'identità di servizio gestito](../active-directory/msi-overview.md).
- Per informazioni su come eseguire questa esercitazione usando le credenziali della firma di accesso condiviso allo spazio di archiviazione, vedere [Use a Windows VM Managed Service Identity to access Azure Storage via a SAS credential](msi-tutorial-windows-vm-access-storage-sas.md) (Usare un'identità del servizio gestito della macchina virtuale Windows per accedere ad Archiviazione di Azure tramite le credenziali della firma di accesso condiviso).
- Per altre informazioni sulla funzionalità della firma di accesso condiviso dell'account di Archiviazione di Azure, vedere:
  - [Uso delle firme di accesso condiviso](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Creazione di una firma di accesso condiviso del servizio](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Usare la sezione dei commenti seguente per offrire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.


