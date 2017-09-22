---
title: "Usare un'identità del servizio gestita per una macchina virtuale Windows per accedere ad Archiviazione di Azure"
description: "Esercitazione che illustra come usare un'identità del servizio gestita per una macchina virtuale Windows per accedere ad Archiviazione di Azure."
services: active-directory
documentationcenter: 
author: elkuzmen
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 86d88e3d44f442171f69d0baea5e7d689b963277
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---

# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-storage"></a>Usare un'identità del servizio gestita per una macchina virtuale Windows per accedere ad Archiviazione di Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come abilitare Identità del servizio gestita (MSI, Managed Service Identity) in una macchina virtuale Windows e quindi usare l'identità per accedere alle chiavi di archiviazione. È possibile usare le chiavi di archiviazione come di consueto durante le operazioni di archiviazione, ad esempio quando si usa Storage SDK. In questa esercitazione si caricheranno e scaricheranno oggetti BLOB usando l'interfaccia della riga di comando di Azure. Si apprenderà come:


> [!div class="checklist"]
> * Abilitare Identità del servizio gestita in una macchina virtuale Windows 
> * Creare un nuovo account di archiviazione
> * Concedere alla macchina virtuale l'accesso ad Archiviazione 
> * Ottenere un token di accesso per il proprio account di archiviazione usando l'identità della macchina virtuale 


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Windows in un nuovo gruppo di risorse

Per questa esercitazione si creerà una nuova macchina virtuale Windows, ma è anche possibile abilitare Identità del servizio gestita in una macchina virtuale esistente.

1.  Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.
2.  Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**. 
3.  Immettere le informazioni relative alla macchina virtuale. I valori di **Nome utente** e **Password** definiti in questa schermata sono le credenziali usate per accedere alla macchina virtuale.
4.  Dall'elenco a discesa **Sottoscrizione** selezionare la sottoscrizione corretta per la macchina virtuale.
5.  Per selezionare un nuovo **gruppo di risorse** in cui creare la macchina virtuale, scegliere **Crea nuovo**. Al termine fare clic su **OK**.
6.  Selezionare la dimensione per la macchina virtuale. Per visualizzare altre dimensioni, selezionare **Visualizza tutto** o modificare il filtro **Tipo di disco supportato**. Nel pannello delle impostazioni mantenere le impostazioni predefinite e fare clic su **OK**.

    ![Testo immagine alt](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Abilitare Identità del servizio gestita nella macchina virtuale

Un'identità del servizio gestita per una macchina virtuale consente di ottenere i token di accesso da Azure AD senza dover inserire le credenziali nel codice. A livello sottostante, quando si abilita Identità del servizio gestita, si eseguono due operazioni, ovvero si installa l'estensione di Identità del servizio gestita nella macchina virtuale e si abilita la funzionalità per tale macchina.  

1. Selezionare la **macchina virtuale** in cui si vuole abilitare Identità del servizio gestita.
2. Nella barra di spostamento a sinistra fare clic su **Configurazione**.
3. Verrà visualizzata la schermata **Managed Service Identity** (Identità del servizio gestita). Per registrare e abilitare Identità del servizio gestita, scegliere **Sì**. Se si vuole disabilitare questa funzionalità, scegliere No.
4. Assicurarsi di fare clic su **Salva** per salvare la configurazione.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Per verificare le estensioni installate nella **macchina virtuale Windows**, fare clic su **Estensioni**. Se Identità del servizio gestita è abilitata, nell'elenco sarà inclusa la voce **ManagedIdentityExtensionforWindows**.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-new-storage-account"></a>Creare un nuovo account di archiviazione 

È possibile usare le chiavi di archiviazione come normalmente per le operazioni di archiviazione. Questo esempio illustra in particolare la procedura per caricare e scaricare oggetti BLOB usando l'interfaccia della riga di comando di Azure. 

1. Passare alla barra laterale e selezionare **Archiviazione**.  
2. Creare un nuovo **account di archiviazione**.  
3. In **Modello di distribuzione** selezionare **Gestione risorse** e per **Tipologia account** selezionare **Utilizzo generico**.  
4. Verificare che i valori di **Sottoscrizione** e **Gruppo di risorse** corrispondano a quelli usati per creare la **macchina virtuale Windows** nel passaggio precedente.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="grant-your-vm-identity-access-to-use-storage-keys"></a>Concedere all'identità della macchina virtuale l'accesso per l'uso delle chiavi di archiviazione 

Usando Identità del servizio gestita, il codice può ottenere i token di accesso per autenticarsi alle risorse che supportano l'autenticazione di Azure AD.   

1. Passare alla scheda **Archiviazione**.  
2. Selezionare l'**account di archiviazione** specifico creato in precedenza.   
3. Passare a **Controllo di accesso (IAM)** nel pannello a sinistra.  
4. Fare quindi clic su **Aggiungi** per aggiungere una nuova assegnazione di ruolo per la macchina virtuale e selezionare **Ruolo del servizio dell'operatore della chiave dell'account di archiviazione** come **Ruolo**.  
5. Dall'elenco a discesa **Assegna accesso a** selezionare la risorsa **Macchina virtuale**.  
6. Verificare quindi che nell'elenco a discesa **Sottoscrizione** sia elencata la sottoscrizione corretta. In **Gruppo di risorse** selezionare **Tutti i gruppi di risorse**.  
7. Scegliere infine la macchina virtuale Windows nell'elenco a discesa **Seleziona** e fare clic su **Salva**. 

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure Resource Manager 

In questa sezione è necessario usare **PowerShell**.  Se non è installato, scaricarlo da [qui](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1. Nel portale passare a **Macchine virtuali**, selezionare la macchina virtuale Windows e in **Panoramica** fare clic su **Connetti**. 
2. In **Nome utente** e **Password** immettere i valori specificati al momento della creazione della macchina virtuale Windows. 
3. Ora che si è creata una **connessione Desktop remoto** con la macchina virtuale, aprire **PowerShell** nella sessione remota. 
4. Usando Invoke-WebRequest di PowerShell, eseguire una richiesta all'endpoint locale di Identità del servizio gestita per ottenere un token di accesso per Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost/50342/oauth2/token -Method GET -Body @resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Il valore del parametro "resource" deve corrispondere esattamente a quello previsto da Azure AD. Quando si usa l'ID risorsa di Azure Resource Manager, è necessario includere la barra finale nell'URI.
    
    Estrarre la risposta completa, archiviata come stringa in formato JSON (JavaScript Object Notation) nell'oggetto $response. 
    
    ```powershell
    $content = $repsonse.Content | ConvertFrom-Json
    ```
    Estrarre quindi il token di accesso dalla risposta.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Chiamare infine Azure Resource Manager usando il token di accesso. In questo esempio Invoke-WebRequest di PowerShell viene usato anche per eseguire la chiamata a Azure Resource Manager e includere il token di accesso nell'intestazione Authorization.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE]
    > L'URL fa distinzione tra maiuscole e minuscole. Accertarsi quindi di usare la stessa combinazione di maiuscole e minuscole usata in precedenza per il nome del gruppo di risorse, facendo attenzione alla maiuscola "G" in "resourceGroup".
    
## <a name="get-the-storage-keys-from-azure-resource-manager"></a>Ottenere le chiavi di archiviazione da Azure Resource Manager 

```powershell
PS C:\> $keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/97f51385-2edc-4b69-bed8-7778dd4cb761/resourceGroups/SKwan_Test/providers/Microsoft.Storage/storageAccounts/skwanteststorage/listKeys/?api-version=2016-12-01 -Method POST$ -Headers @{Authorization="Bearer $ARMToken"}
```

```powershell
PS C:\> $keysContent = $keysResponse.Content | ConvertFrom-Json
```

```powershell
PS C:\> $key = $keysContent.keys[0].value
```

**Creare un file da caricare usando l'interfaccia della riga di comando di Azure**

```bash
echo "This is a test text file." > test.txt
```

**Caricare il file usando l'interfaccia della riga di comando di Azure ed eseguendo l'autenticazione con la chiave di archiviazione**

> [!NOTE]
> Ricordare innanzitutto di installare i cmdlet di archiviazione di Azure "Install-Module Azure.Storage". 

Richiesta di PowerShell:


```powershell
PS C:\> $ctx = New-AzureStorageContext -StorageAccountName skwanteststorage -StorageAccountKey $key
PS C:\> Set-AzureStorageBlobContent -File test.txt -Container testcontainer -Blob testblob -Context $ctx
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

**Scaricare il file usando l'interfaccia della riga di comando di Azure ed eseguendo l'autenticazione con la chiave di archiviazione**

Richiesta di PowerShell:

```powershell
PS C:\> Get-AzureStorageBlobContent -Blob <blob name> -Container <container name> -Destination <file> -Context $ctx
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





