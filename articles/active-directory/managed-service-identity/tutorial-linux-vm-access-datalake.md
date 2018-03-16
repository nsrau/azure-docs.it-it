---
title: "Usare l'identità del servizio gestito per una VM Linux per accedere ad Azure Data Lake Store"
description: "Esercitazione che illustra come usare l'identità del servizio gestito per una VM Linux per accedere ad Azure Data Lake Store."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: bef549a0cb8a876bbf8fbf281a6c2d1d489736af
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="use-managed-service-identity-for-a-linux-vm-to-access-azure-data-lake-store"></a>Usare l'identità del servizio gestito per una VM Linux per accedere ad Azure Data Lake Store

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come usare l'identità del servizio gestito per una macchina virtuale (VM) Linux per accedere ad Azure Data Lake Store. Azure gestisce automaticamente le identità create tramite l'identità del servizio gestito, che può essere usata per eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure Active Directory (Azure AD) senza dover inserire le credenziali nel codice. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare l'identità del servizio gestito in una VM Linux. 
> * Concedere alla VM l'accesso ad Azure Data Lake Store.
> * Ottenere un token di accesso usando l'identità della VM e usarlo per accedere ad Azure Data Lake Store.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Linux in un nuovo gruppo di risorse

Per questa esercitazione si creerà una nuova macchina virtuale Linux, ma è anche possibile abilitare l'identità del servizio gestito in una macchina virtuale esistente.

1. Selezionare il pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS**.
3. Immettere le informazioni relative alla macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH** o **Password**. Le credenziali create consentono di eseguire l'accesso alla macchina virtuale.

   ![Riquadro "Informazioni di base" per la creazione di una macchina virtuale](../media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Nell'elenco **Sottoscrizione** selezionare una sottoscrizione per la macchina virtuale.
5. Per selezionare un nuovo gruppo di risorse in cui creare la macchina virtuale, selezionare **Gruppo di risorse** > **Crea nuovo**. Al termine, selezionare **OK**.
6. Selezionare la dimensione della macchina virtuale. Per visualizzare altre dimensioni, selezionare **Visualizza tutto** o modificare il filtro **Supported disk type** (Tipo di disco supportato). Nel riquadro delle impostazioni mantenere le impostazioni predefinite e selezionare **OK**.

## <a name="enable-msi-on-your-vm"></a>Abilitare identità del servizio gestito nella macchina virtuale

È possibile usare l'identità del servizio gestito per una macchina virtuale per ottenere i token di accesso da Azure AD senza dover inserire le credenziali nel codice. Quando si abilita l'identità del servizio gestito, l'estensione della macchina virtuale per l'identità del servizio gestito viene installata nella VM e l'identità del servizio gestito viene abilitata in Azure Resource Manager.  

1. Per **Macchina virtuale** selezionare la macchina virtuale in cui si vuole abilitare l'identità del servizio gestito.
2. Nel riquadro sinistro selezionare **Configurazione**.
3. Verrà visualizzata l'opzione **Identità del servizio gestito**. Per registrare e abilitare l'identità del servizio gestito, selezionare **Sì**. Per disabilitarla, selezionare **No**.
   ![Selezione per "Registra con Azure Active Directory"](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)
4. Selezionare **Salva**.
5. Per verificare le estensioni presenti nella VM Linux, selezionare **Estensioni**. Se l'identità del servizio gestito è abilitata, nell'elenco sarà inclusa la voce **ManagedIdentityExtensionforLinux**.

   ![Elenco delle estensioni](../media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Concedere alla VM l'accesso ad Azure Data Lake Store

È ora possibile concedere alla VM l'accesso a file e cartelle in Azure Data Lake Store. Per questo passaggio, è possibile usare un'istanza di Data Lake Store esistente o crearne una nuova. Per creare un'istanza di Data Lake Store usando il portale di Azure, seguire la [guida introduttiva ad Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Nella [documentazione per Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) sono disponibili anche guide introduttive che usano l'interfaccia della riga di comando di Azure e Azure PowerShell.

In Data Lake Store creare una nuova cartella e concedere all'identità del servizio gestito l'autorizzazione per la lettura, la scrittura e l'esecuzione di file in tale cartella:

1. Nel portale di Azure selezionare **Data Lake Store** nel riquadro sinistro.
2. Selezionare l'istanza di Data Lake Store che si vuole usare.
3. Selezionare **Esplora dati** sulla barra dei comandi.
4. Verrà selezionata la cartella radice dell'istanza di Data Lake Store. Selezionare **Accesso** sulla barra dei comandi.
5. Selezionare **Aggiungi**.  Nella casella **Seleziona** immettere il nome della VM, ad esempio **DevTestVM**. Selezionare la VM nei risultati della ricerca e quindi fare clic su **Seleziona**.
6. Fare clic su **Selezionare le autorizzazioni**.  Selezionare **Lettura** ed **Esecuzione**, aggiungere a **Questa cartella** e aggiungere come **An access permission only** (Solo autorizzazione di accesso). Selezionare **OK**.  L'autorizzazione verrà aggiunta correttamente.
7. Chiudere il riquadro **Accesso**.
8. Per questa esercitazione creare una nuova cartella. Selezionare **Nuova cartella** sulla barra dei comandi e assegnare un nome alla nuova cartella, ad esempio **TestFolder**.  Selezionare **OK**.
9. Selezionare la cartella creata e quindi **Accesso** sulla barra dei comandi.
10. Come nel passaggio 5, selezionare **Aggiungi**. Nella casella **Seleziona** immettere il nome della VM. Selezionare la VM nei risultati della ricerca e quindi fare clic su **Seleziona**.
11. Come nel passaggio 6, selezionare **Selezionare le autorizzazioni**. Selezionare **Lettura**, **Scrittura** ed **Esecuzione**, aggiungere a **Questa cartella** e aggiungere come **Una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Selezionare **OK**.  L'autorizzazione verrà aggiunta correttamente.

L'identità del servizio gestito può ora eseguire tutte le operazioni sui file nella cartella creata. Per altre informazioni sulla gestione dell'accesso a Data Lake Store, vedere [Controllo di accesso in Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>Ottenere un token di accesso e chiamare il file system di Data Lake Store

Azure Data Lake Store supporta in modo nativo l'autenticazione di Azure AD e può quindi accettare direttamente i token di accesso ottenuti tramite l'identità del servizio gestito. Per eseguire l'autenticazione al file system di Data Lake Store, si invia un token di accesso rilasciato da Azure AD all'endpoint del file system di Data Lake Store. Il token di accesso è incluso in un'intestazione dell'autorizzazione nel formato "Bearer \<VALORE_TOKEN_ACCESSO\>".  Per altre informazioni sul supporto di Data Lake Store per l'autenticazione di Azure AD, vedere [Autenticazione con Data Lake Store usando Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

In questa esercitazione si esegue l'autenticazione all'API REST per il file system di Data Lake Store usando cURL per inviare richieste REST.

> [!NOTE]
> Gli SDK client per il file system di Data Lake Store non supportano ancora l'identità del servizio gestito.

Per completare questi passaggi, è necessario disporre di un client SSH. Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about). Per informazioni sulla configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Nel portale passare alla VM Linux. In **Panoramica** selezionare **Connetti**.  
2. Connettersi alla VM usando il client SSH di propria scelta. 
3. Nella finestra del terminale usare cURL per inviare una richiesta all'endpoint locale dell'identità del servizio gestito per ottenere un token di accesso per il file system di Data Lake Store. L'identificatore della risorsa per Data Lake Store è "https://datalake.azure.net/".  È importante includere la barra finale nell'identificatore della risorsa.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Una risposta con esito positivo restituisce il token di accesso che viene usato per l'autenticazione a Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Usando cURL, inviare una richiesta all'endpoint REST del file system di Data Lake Store per ottenere un elenco delle cartelle nella cartella radice. È un modo semplice per controllare che tutto sia configurato correttamente. Copiare il valore del token di accesso dal passaggio precedente. È importante che la stringa "Bearer" nell'intestazione dell'autorizzazione contenga una "B" maiuscola. È possibile trovare il nome dell'istanza di Data Lake Store nella sezione **Panoramica** del riquadro **Data Lake Store** nel portale di Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Una risposta con esito positivo ha un aspetto simile al seguente:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. È ora possibile provare a caricare un file nell'istanza di Data Lake Store. Creare prima di tutto un file da caricare.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Usando cURL, inviare una richiesta all'endpoint REST del file system di Data Lake Store per caricare il file nella cartella creata in precedenza. Il caricamento implica un reindirizzamento e cURL segue automaticamente il reindirizzamento. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Una risposta con esito positivo ha un aspetto simile al seguente:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Usando altre API per il file system di Data Lake Store, è possibile eseguire aggiunte ai file, download di file e altre operazioni.

Congratulazioni! È stata eseguita l'autenticazione al file system di Data Lake Store usando l'identità del servizio gestito per una VM Linux.

## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'Identità di servizio gestito, vedere [Panoramica dell'Identità di servizio gestito](overview.md).
- Per le operazioni di gestione, Data Lake Store usa Azure Resource Manager.  Per altre informazioni sull'uso dell'identità del servizio gestito per eseguire l'autenticazione a Resource Manager, vedere [Usare un'identità del servizio gestito per una macchina virtuale Linux per accedere a Resource Manager](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm).
- Altre informazioni sull'[autenticazione con Data Lake Store usando Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).
- Altre informazioni sulle [operazioni di file system in Azure Data Lake Store con l'API REST](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api) o sulle [API WebHDFS per il file system](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Altre informazioni sul [controllo di accesso in Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.