---
title: "Come usare un'identità del servizio gestito per una macchina virtuale Windows per accedere ad Azure Data Lake Store"
description: "Esercitazione che illustra come usare un'identità del servizio gestito per una macchina virtuale Windows per accedere ad Azure Data Lake Store."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: e637870ec830895a2baa545c46e39fb3494b956b
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Usare un'identità del servizio gestito per una macchina virtuale Windows per accedere ad Azure Data Lake Store

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come usare un'identità del servizio gestito per una macchina virtuale Windows per accedere a un'istanza di Azure Data Lake Store. Le Identità del servizio gestito vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice. Si apprenderà come:

> [!div class="checklist"]
> * Abilitare Identità del servizio gestito in una macchina virtuale Windows 
> * Concedere alla VM l'accesso a un'istanza di Azure Data Lake Store
> * Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per accedere a un'istanza di Azure Data Lake Store

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Windows in un nuovo gruppo di risorse

Per questa esercitazione si creerà una nuova macchina virtuale Windows,  ma è anche possibile abilitare l'identità del servizio gestito in una macchina virtuale esistente.

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**. 
3. Immettere le informazioni relative alla macchina virtuale. I valori di **Nome utente** e **Password** creati in questa schermata sono le credenziali usate per accedere alla macchina virtuale.
4. Dall'elenco a discesa **Sottoscrizione** selezionare la sottoscrizione corretta per la macchina virtuale.
5. Per selezionare un nuovo **Gruppo di risorse** in cui creare la macchina virtuale, scegliere **Crea nuovo**. Al termine fare clic su **OK**.
6. Selezionare la dimensione della macchina virtuale. Per visualizzare altre dimensioni, selezionare **Visualizza tutto** o modificare il filtro **Supported disk type** (Tipo di disco supportato). Nella pagina delle impostazioni mantenere le impostazioni predefinite e fare clic su **OK**.

   ![Testo immagine alt](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Abilitare identità del servizio gestito nella macchina virtuale 

Un'Identità del servizio gestito per una macchina virtuale consente di ottenere i token di accesso da Azure AD senza dover inserire le credenziali nel codice. L'abilitazione dell'Identità del servizio gestito indica ad Azure di creare un'identità gestita per la macchina virtuale. A livello sottostante, quando si abilita Identità del servizio gestito, si eseguono due operazioni, ovvero si installa l'estensione della macchina virtuale per l'Identità del servizio gestito nella macchina virtuale e si abilita l'Identità del servizio gestito in Azure Resource Manager.

1. Selezionare la **macchina virtuale** in cui si vuole abilitare identità del servizio gestito.  
2. Nella barra di spostamento a sinistra fare clic su **Configurazione**. 
3. Viene visualizzato **Managed Service Identity** (identità del servizio gestito). Per registrare e abilitare identità del servizio gestita, scegliere **Sì**. Se si vuole disabilitare questa funzionalità, scegliere No. 
4. Assicurarsi di fare clic su **Salva** per salvare la configurazione.  
   ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Per verificare le estensioni installate nella macchina virtuale, fare clic su **Estensioni**. Se identità del servizio gestito è abilitata, nell'elenco sarà inclusa la voce **ManagedIdentityExtensionforWindows**.

   ![Testo immagine alt](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Concedere alla VM l'accesso ad Azure Data Lake Store

È ora possibile concedere alla VM l'accesso a file e cartelle in un'istanza di Azure Data Lake Store.  Per questo passaggio, è possibile usare un'istanza di Data Lake Store esistente o crearne una nuova.  Per creare una nuova istanza di Data Lake Store usando il portale di Azure, seguire questa [guida introduttiva ad Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Nella [documentazione di Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) sono disponibili anche guide introduttive che usano l'interfaccia della riga di comando di Azure e Azure PowerShell.

Nell'istanza di Data Lake Store creare una nuova cartella e concedere alla VM l'autorizzazione di identità del servizio gestito per leggere, scrivere ed eseguire file in tale cartella:

1. Nel portale di Azure fare clic su **Data Lake Store** nel riquadro di spostamento sinistro.
2. Fare clic sull'istanza di Data Lake Store che si vuole usare per questa esercitazione.
3. Fare clic su **Esplora dati** sulla barra dei comandi.
4. Viene selezionata la cartella radice dell'istanza di Data Lake Store.  Fare clic su **Accedi** sulla barra dei comandi.
5. Fare clic su **Aggiungi**.  Nel campo **Seleziona** immettere il nome della VM, ad esempio **DevTestVM**.  Fare clic per selezionare la VM nei risultati della ricerca, quindi fare clic su **Seleziona**.
6. Fare clic su **Selezionare le autorizzazioni**.  Selezionare **Lettura** ed **Esecuzione**, aggiungere a **Questa cartella** e aggiungere come **An access permission only** (Solo autorizzazione di accesso).  Fare clic su **OK**.  L'autorizzazione verrà aggiunta correttamente.
7. Chiudere il pannello **Accesso**.
8. Per questa esercitazione creare una nuova cartella.  Fare clic su **Nuova cartella** sulla barra dei comandi e assegnare un nome alla nuova cartella, ad esempio **TestFolder**.  Fare clic su **OK**.
9. Fare clic sulla cartella creata e quindi su **Accedi** sulla barra dei comandi.
10. Come nel passaggio 5, fare clic su **Aggiungi**, nel campo **Seleziona** immettere il nome della VM, selezionarla e fare clic su **Seleziona**.
11. Come nel passaggio 6, fare clic su **Selezionare le autorizzazioni**, selezionare **Lettura**, **Scrittura** ed **Esecuzione**, aggiungere a **Questa cartella** e aggiungere come **Una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**.  Fare clic su **OK**.  L'autorizzazione verrà aggiunta correttamente.

L'identità del servizio gestito della VM può ora eseguire tutte le operazioni sui file nella cartella creata.  Per altre informazioni sulla gestione dell'accesso a Data Lake Store, vedere [Controllo di accesso in Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Ottenere un token di accesso usando l'identità del servizio gestito della macchina virtuale e usarlo per chiamare il file system di Azure Data Lake Store

Azure Data Lake Store supporta in modo nativo l'autenticazione di Azure AD, per poter accettare direttamente i token di accesso ottenuti usando l'identità del servizio gestito.  Per eseguire l'autenticazione al file system di Data Lake Store, inviare un token di accesso rilasciato da Azure AD all'endpoint del file system di Data Lake Store, in un'intestazione dell'autorizzazione con il formato "Bearer <ACCESS_TOKEN_VALUE>".  Per altre informazioni sul supporto di Data Lake Store per l'autenticazione di Azure AD, vedere [Autenticazione con Data Lake Store usando Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)

> [!NOTE]
> Gli SDK client del file system di Data Lake Store non supportano ancora l'identità del servizio gestito.  Questa esercitazione verrà aggiornata quando verrà aggiunto il supporto all'SDK.

In questa esercitazione si esegue l'autenticazione all'API REST del file system di Data Lake Store usando PowerShell per creare richieste REST. Per usare l'identità del servizio gestito della VM per l'autenticazione, è necessario eseguire le richieste dalla VM.

1. Nel portale passare a **Macchine virtuali**, selezionare la VM Windows e in **Panoramica** fare clic su **Connetti**.
2. In **Nome utente** e **Password** immettere i valori specificati al momento della creazione della macchina virtuale Windows. 
3. Ora che si è creata una **connessione Desktop remoto** con la macchina virtuale, aprire **PowerShell** nella sessione remota. 
4. Con `Invoke-WebRequest` di PowerShell, creare una richiesta all'endpoint locale di identità del servizio gestito per ottenere un token di accesso per Azure Data Lake Store.  L'identificatore della risorsa per Data Lake Store è "https://datalake.azure.net/".  La barra finale è importante perché Data Lake stabilisce una corrispondenza esatta con l'identificatore della risorsa.

   ```powershell
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://datalake.azure.net/"} -Headers @{Metadata="true"}
   ```
    
   Convertire la risposta da un oggetto JSON a un oggetto di PowerShell. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Estrarre il token di accesso dalla risposta.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Usando `Invoke-WebRequest' di PowerShell, creare una richiesta all'endpoint REST di Data Lake Store per elencare le cartelle nella cartella radice.  È un modo semplice per controllare che tutto sia configurato correttamente.  È importante che la stringa "Bearer" nell'intestazione dell'autorizzazione abbia una lettera maiuscola "B".  È possibile trovare il nome dell'istanza di Data Lake Store nella sezione **Panoramica** del pannello Data Lake Store nel portale di Azure.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Una risposta con esito positivo ha un aspetto simile al seguente:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. È ora possibile provare a caricare un file nell'istanza di Data Lake Store.  Creare prima di tutto un file da caricare.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Usando `Invoke-WebRequest` di PowerShell, creare una richiesta all'endpoint REST dell'istanza di Data Lake Store per caricare il file nella cartella creata prima.  Questa richiesta prevede due passaggi.  Nel primo passaggio si crea una richiesta e si ottiene un reindirizzamento alla posizione in cui deve essere caricato il file.  Nel secondo passaggio si carica effettivamente il file.  Ricordare di impostare il nome della cartella e del file in modo appropriato se sono stati usati valori diversi da quelli di questa esercitazione. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Se si osserva il valore di `$HdfsRedirectResponse`, dovrà essere simile alla risposta seguente:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Completare il caricamento inviando una richiesta all'endpoint di reindirizzamento:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Una risposta con esito positivo ha un aspetto simile al seguente:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

Usando altre API del file system di Data Lake Store, è possibile accodare ai file, scaricare i file ed eseguire altre operazioni.

Congratulazioni.  È stata eseguita l'autenticazione al file system di Data Lake Store usando un'identità del servizio gestito della VM.

## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'identità di servizio gestito, vedere [Panoramica dell'identità di servizio gestito](../active-directory/msi-overview.md).
- Per le operazioni di gestione Data Lake Store usa Azure Resource Manager.  Per altre informazioni su un'identità del servizio gestito della VM per l'autenticazione in Resource Manager, vedere [Usare un'identità del servizio gestito con una macchina virtuale Linux per accedere a Resource Manager](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm).
- Altre informazioni sull'[autenticazione con Data Lake Store tramite Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).
- Altre informazioni sulle [operazioni del file system in Azure Data Lake Store tramite l'API REST](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api) o sulle [API del file system WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Altre informazioni sul [controllo di accesso in Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.