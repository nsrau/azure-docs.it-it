---
title: "Come usare un'identità del servizio gestito per una macchina virtuale Linux per accedere ad Azure Data Lake Store"
description: "Esercitazione che illustra come usare un'identità del servizio gestito per una macchina virtuale Linux per accedere ad Azure Data Lake Store."
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
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e2a2b885dd0dc5b240aef234ef1ff139d788de3c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Usare un'identità del servizio gestito per una macchina virtuale Linux per accedere ad Azure Data Lake Store

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Questa esercitazione illustra come usare un'identità del servizio gestito per una macchina virtuale Linux per accedere a un'istanza di Azure Data Lake Store. Le Identità del servizio gestito vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice. Si apprenderà come:

> [!div class="checklist"]
> * Abilitare l'identità del servizio gestito in una macchina virtuale Linux 
> * Concedere alla VM l'accesso a un'istanza di Azure Data Lake Store
> * Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per accedere a un'istanza di Azure Data Lake Store

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Linux in un nuovo gruppo di risorse

Per questa esercitazione si creerà una nuova macchina virtuale Linux, ma è anche possibile abilitare l'identità del servizio gestito in una macchina virtuale esistente.

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS**.
3. Immettere le informazioni relative alla macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH** o **Password**. Le credenziali create consentono di eseguire l'accesso alla macchina virtuale.

   ![Testo immagine alt](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Dall'elenco a discesa **Sottoscrizione** selezionare una sottoscrizione per la macchina virtuale.
5. Per selezionare un nuovo **Gruppo di risorse** in cui creare la macchina virtuale, scegliere **Crea nuovo**. Al termine fare clic su **OK**.
6. Selezionare la dimensione della macchina virtuale. Per visualizzare altre dimensioni, selezionare **View all** (Visualizza tutto) o modificare il filtro Tipo di disco supportato. Nel pannello delle impostazioni mantenere le impostazioni predefinite e fare clic su **OK**.

## <a name="enable-msi-on-your-vm"></a>Abilitare identità del servizio gestito nella macchina virtuale

Un'identità del servizio gestito per una macchina virtuale consente di ottenere i token di accesso da Azure AD senza dover inserire le credenziali nel codice. A livello sottostante, quando si abilita l'identità del servizio gestito, si eseguono due operazioni, ovvero si installa l'estensione della macchina virtuale per l'identità del servizio gestito nella macchina virtuale e si abilita l'identità del servizio gestito in Azure Resource Manager.  

1. Selezionare la **macchina virtuale** in cui si vuole abilitare identità del servizio gestito.
2. Nella barra di spostamento a sinistra fare clic su **Configurazione**.
3. Viene visualizzato **Managed Service Identity** (identità del servizio gestito). Per registrare e abilitare identità del servizio gestita, scegliere **Sì**. Se si vuole disabilitare questa funzionalità, scegliere No.
4. Assicurarsi di fare clic su **Salva** per salvare la configurazione.

   ![Testo immagine alt](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Per verificare le estensioni installate nella **macchina virtuale Linux**, fare clic su **Estensioni**. Se la funzione Identità del servizio gestito è abilitata, nell'elenco è inclusa la voce **ManagedIdentityExtensionforLinux**.

   ![Testo immagine alt](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Concedere alla VM l'accesso ad Azure Data Lake Store

È ora possibile concedere alla VM l'accesso a file e cartelle in un'istanza di Azure Data Lake Store.  Per questo passaggio, è possibile usare un'istanza di Data Lake Store esistente o crearne una nuova.  Per creare una nuova istanza di Data Lake Store usando il portale di Azure, seguire questa [guida introduttiva ad Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-get-started-portal.md). Nella [documentazione di Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-overview.md) sono disponibili anche guide introduttive che usano l'interfaccia della riga di comando di Azure e Azure PowerShell.

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

Azure Data Lake Store supporta in modo nativo l'autenticazione di Azure AD, per poter accettare direttamente i token di accesso ottenuti usando l'identità del servizio gestito.  Per eseguire l'autenticazione al file system di Data Lake Store, inviare un token di accesso rilasciato da Azure AD all'endpoint del file system di Data Lake Store, in un'intestazione dell'autorizzazione con il formato "Bearer \<ACCESS_TOKEN_VALUE\>".  Per altre informazioni sul supporto di Data Lake Store per l'autenticazione di Azure AD, vedere [Autenticazione con Data Lake Store usando Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

In questa esercitazione si esegue l'autenticazione all'API REST del file system di Data Lake Store usando CURL per creare richieste REST.

> [!NOTE]
> Gli SDK client del file system di Data Lake Store non supportano ancora l'identità del servizio gestito.  Questa esercitazione verrà aggiornata quando verrà aggiunto il supporto all'SDK.

Per completare questi passaggi, è necessario disporre di un client SSH. Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about). Per richiedere assistenza nella configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Nel portale, passare a una macchina virtuale Linux e in **Panoramica** fare clic su **Connetti**.  
2. **Connettersi** alla macchina virtuale usando un client SSH di propria scelta. 
3. Nella finestra del terminale usare CURL per creare una richiesta all'endpoint locale dell'identità del servizio gestito per ottenere un token di accesso per il file system di Data Lake Store.  L'identificatore della risorsa per Data Lake Store è "https://datalake.azure.net/".  È importante includere la barra finale nell'identificatore della risorsa.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Una risposta corretta restituisce il token di accesso usato per l'autenticazione in Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Usando CURL, creare una richiesta all'endpoint REST del file system di Data Lake Store per elencare le cartelle nella cartella radice.  È un modo semplice per controllare che tutto sia configurato correttamente.  Copiare il valore del token di accesso dal passaggio precedente.  È importante che la stringa "Bearer" nell'intestazione dell'autorizzazione abbia una lettera maiuscola "B".  È possibile trovare il nome dell'istanza di Data Lake Store nella sezione **Panoramica** del pannello Data Lake Store nel portale di Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Una risposta con esito positivo ha un aspetto simile al seguente:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. È ora possibile provare a caricare un file nell'istanza di Data Lake Store.  Creare prima di tutto un file da caricare.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Usando CURL, creare una richiesta all'endpoint REST del file system di Data Lake Store per caricare il file nella cartella creata prima.  Il caricamento implica un reindirizzamento e CURL segue automaticamente il reindirizzamento. 

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

Usando altre API del file system di Data Lake Store, è possibile accodare ai file, scaricare i file ed eseguire altre operazioni.

Congratulazioni!  È stata eseguita l'autenticazione al file system di Data Lake Store usando un'identità del servizio gestito della VM.

## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'Identità di servizio gestito, vedere [Panoramica dell'Identità di servizio gestito](msi-overview.md).
- Per le operazioni di gestione Data Lake Store usa Azure Resource Manager.  Per altre informazioni su un'identità del servizio gestito della VM per l'autenticazione in Resource Manager, vedere [Usare un'identità del servizio gestito con una macchina virtuale Linux per accedere a Resource Manager](msi-tutorial-linux-vm-access-arm.md).
- Altre informazioni sull'[autenticazione con Data Lake Store tramite Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- Altre informazioni sulle [operazioni del file system in Azure Data Lake Store tramite l'API REST](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) o sulle [API del file system WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis.md).
- Altre informazioni sul [controllo di accesso in Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.