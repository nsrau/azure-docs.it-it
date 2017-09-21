---
title: "Usare un'Identità del servizio gestito di una macchina virtuale Linux per accedere ad Archiviazione di Azure"
description: "Esercitazione che illustra come usare un'identità del servizio gestito per una macchina virtuale Linux per accedere ad Archiviazione di Azure."
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
ms.openlocfilehash: 9fec51528d1203a7107558408ced42fa2b2b884a
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---


# <a name="use-managed-service-identity-msi-with-a-linux-vm-to-access-storage-credentials"></a>Usare un'Identità del servizio gestito con una macchina virtuale Linux per accedere alle credenziali di archiviazione

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come abilitare Identità del servizio gestito (MSI, Managed Service Identity) in una macchina virtuale Linux e quindi usare l'identità per accedere alle chiavi di archiviazione. È possibile usare le chiavi di archiviazione come di consueto durante le operazioni di archiviazione, ad esempio quando si usa Storage SDK. In questa esercitazione si caricheranno e scaricheranno oggetti BLOB usando l'interfaccia della riga di comando di Azure. Si apprenderà come:


> [!div class="checklist"]
> * Abilitare Identità del servizio gestito in una macchina virtuale Linux 
> * Creare un nuovo account di archiviazione
> * Concedere alla macchina virtuale l'accesso ad Archiviazione
> * Ottenere un token di accesso per il proprio account di archiviazione usando l'identità della macchina virtuale per accedere alle chiavi di archiviazione 


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Linux in un nuovo gruppo di risorse

Per questa esercitazione si creerà una nuova macchina virtuale Linux, ma è anche possibile abilitare l'Identità del servizio gestito in una macchina virtuale esistente.

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS**.
3. Immettere le informazioni relative alla macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH** o **Password**. Le credenziali create consentiranno di effettuare l'accesso alla macchina virtuale.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Dall'elenco a discesa **Sottoscrizione** selezionare una sottoscrizione per la macchina virtuale.
5. Per selezionare un nuovo **Gruppo di risorse** in cui creare la macchina virtuale, scegliere **Crea nuovo**. Al termine fare clic su **OK**.
6. Selezionare la dimensione della macchina virtuale. Per visualizzare altre dimensioni, selezionare **View all** (Visualizza tutto) o modificare il filtro Tipo di disco supportato. Nel pannello delle impostazioni mantenere le impostazioni predefinite e fare clic su **OK**.

## <a name="enable-msi-on-your-vm"></a>Abilitare Identità del servizio gestito nella macchina virtuale

Un'Identità del servizio gestito per una macchina virtuale consente di ottenere i token di accesso da Azure AD senza dover inserire le credenziali nel codice. A livello sottostante, quando si abilita Identità del servizio gestito, si eseguono due operazioni, ovvero si installa l'estensione di Identità del servizio gestito nella macchina virtuale e si abilita la funzionalità per tale macchina.  

1. Selezionare la **macchina virtuale** in cui si vuole abilitare l'Identità del servizio gestito.
2. Nella barra di spostamento a sinistra fare clic su **Configurazione**.
3. Verrà visualizzata la schermata **Managed Service Identity** (Identità del servizio gestito). Per registrare e abilitare l'Identità del servizio gestito, scegliere **Sì**. Se si vuole disabilitare questa funzionalità, scegliere No.
4. Assicurarsi di fare clic su **Salva** per salvare la configurazione.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Per verificare le estensioni installate nella **macchina virtuale Linux**, fare clic su **Estensioni**. Se Identità del servizio gestito è abilitata, nell'elenco sarà inclusa la voce **ManagedIdentityExtensionforLinux**.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="create-a-new-storage-account"></a>Creare un nuovo account di archiviazione 

È possibile usare le chiavi di archiviazione come di consueto per le operazioni di archiviazione. Questo esempio illustra in particolare la procedura per caricare e scaricare oggetti BLOB usando l'interfaccia della riga di comando di Azure. 

1. Passare alla barra laterale e selezionare **Archiviazione**.  
2. Creare un nuovo **account di archiviazione**.  
3. In **Modello di distribuzione** selezionare **Gestione risorse** e per **Tipologia account** selezionare **Utilizzo generico**.  
4. Verificare che i valori di **Sottoscrizione** e **Gruppo di risorse** corrispondano a quelli usati per creare la **macchina virtuale Linux** nel passaggio precedente.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="grant-your-vm-identity-access-to-use-storage-keys"></a>Concedere all'identità della macchina virtuale l'accesso per l'uso delle chiavi di archiviazione 

Usando Identità del servizio gestito, il codice può ottenere i token di accesso per autenticarsi nelle risorse che supportano l'autenticazione di Azure AD.   

1. Passare alla scheda **Archiviazione**.  
2. Selezionare l'**account di archiviazione** specifico creato in precedenza.   
3. Passare a **Controllo di accesso (IAM)** nel pannello a sinistra.  
4. Fare quindi clic su **Aggiungi** per aggiungere una nuova assegnazione di ruolo per la macchina virtuale e selezionare **Ruolo del servizio dell'operatore della chiave dell'account di archiviazione** come **Ruolo**.  
5. Dall'elenco a discesa **Assegna accesso a** selezionare la risorsa **Macchina virtuale**.  
6. Verificare quindi che nell'elenco a discesa **Sottoscrizione** sia elencata la sottoscrizione corretta. In **Gruppo di risorse** selezionare **Tutti i gruppi di risorse**.  
7. Scegliere infine la macchina virtuale Linux nell'elenco a discesa **Seleziona** e fare clic su **Salva**. 
    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure Resource Manager

È necessario usare il terminale Bash, scegliere e quindi scaricare la distribuzione di Linux [qui](https://msdn.microsoft.com/commandline/wsl/install_guide).


1. Nel portale, passare a una macchina virtuale Linux e in **Panoramica** fare clic su **Connetti**. Verrà richiesto di usare Bash, prendere nota delle SSH e dell'indirizzo IP della macchina virtuale indicati nell'avviso. 
2. Aprire e connettersi a Bash.  
3. Nella finestra terminale immettere l'**SSH** e la **Macchina virtuale** a cui si desidera connettersi, ad esempio, "ssh admin@12.61.219.35".  
4. Successivamente, verrà richiesto di immettere la **Password** aggiunta durante la creazione della **macchina virtuale Linux**. A questo punto l'accesso è stato eseguito correttamente.  
5. È quindi possibile effettuare una richiesta usando CURL per ottenere il token di autorizzazione per la macchina virtuale Linux a cui si è connessi. L'endpoint di **Azure Resource Manager** è https://management.azure.com.  

    La richiesta CURL per il token di accesso è mostrata di seguito:
    
    ```bash
    curl --data "authority= https://login.microsoftonline.com/<TENANT ID>&&resource=https://management.azure.com/"  -H Metadata:true http://localhost:50432/oauth2/token   
    ```
    
    > [!NOTE]
    > Verificare che l'URL per la risorsa per cui si sta tentando di richiedere l'accesso contenga la corretta formattazione con una barra alla fine, ad esempio "https:<RESOURCE>/"
    
    ```powershell
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEP"}
     ```
    
## <a name="the-curl-request-to-get-storage-keys-from-azure-resource-manager"></a>Richiesta CURL per ottenere le chiavi di archiviazione da Azure Resource Manager  

> [!NOTE]
> Il testo nell'URL fa distinzione tra maiuscole e minuscole. Assicurarsi quindi di usare la stessa combinazione di maiuscole e minuscole usata per i gruppi di risorse. Inoltre, è importante sapere che si tratta di una richiesta POST, non di una richiesta GET, e verificare di passare un valore per acquisire un limite di lunghezza con -d che può essere NULL.  

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 –request POST -d"" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

La risposta CURL restituisce l'elenco di chiavi:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNtXcluBiFSV2Fs+JTj6c+VK5wWB4RWUVYw2Y5BkRQu21cRpAztt3S19bAowhGPqJrAfMr3gn89Ui9VPbtA=="},{"keyName":"key2","permissions":"Full","value":"U+uI0B1Iygw1bDK6Ic1VWKMMY77ac0VV3kqYaCW7FcUFiuw4UkLfEKbnF2sZpIfFR9zUWniLBhlt+eUn/TeSJg=="}]} 
```


Creare un file da caricare, ovvero un file BLOB di esempio che è possibile caricare con le chiavi di archiviazione nell'account di archiviazione all'interno del contenitore creato. 

A tale scopo, in una macchina virtuale Linux eseguire il comando seguente. 

```bash
echo "This is a test file." > test.txt
```
 Successivamente, è possibile caricare il file usando l'interfaccia della riga di comando di Azure ed eseguendo l'autenticazione con la chiave di archiviazione.
 

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name 
                        [--account-name] 
                        [--account-key] 
```

Risposta: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

È anche possibile scaricare il file usando l'interfaccia della riga di comando di Azure ed eseguendo l'autenticazione con la chiave di archiviazione. 

Richiesta: 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         [--account-name]
                         [--account-key]  
```

Risposta: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-12T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="related-content"></a>Contenuti correlati

Per una panoramica dell'Identità di servizio gestito, vedere [Panoramica dell'Identità di servizio gestito](../active-directory/msi-overview.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.


