---
title: Configurare le origini di distribuzione per Servizi app in Azure Stack | Documentazione Microsoft
description: "Procedura con cui un amministratore del servizio può configurare le origini di distribuzione (Git, GitHub, BitBucket, DropBox e OneDrive) per il servizio app in Azure Stack"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: anwestg
ms.openlocfilehash: 82b6002557431f87de8fd206b4d7f4a07dec08b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-deployment-sources"></a>Configurare le origini di distribuzione

Servizio app in Azure Stack supporta la distribuzione su richiesta da più provider del controllo del codice sorgente. Questa funzionalità consente agli sviluppatori di applicazioni distribuzione diretta dal loro repository del controllo codice sorgente. Se gli utenti desiderano configurare servizio App di connettersi ai propri repository, è necessario un operatore cloud innanzitutto configurare l'integrazione tra servizio App nello Stack di Azure e il provider del controllo del codice sorgente.  

I provider del controllo del codice sorgente, oltre al Git locale, sono:

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Visualizzazione di origini di distribuzione nell'amministrazione del servizio App

1. Accedere al portale di amministrazione di Azure Stack (https://adminportal.local.azurestack.external) come amministratore del servizio.
2. Passare a  **Resource Providers** (Provider di risorse) e selezionare **App Service Resource Provider Admin** (Amministrazione provider risorse servizio app).  ![App Service Resource Provider Admin (Amministrazione provider risorse servizio app)][1]
3. Fare clic su **Source Control Configuration** (Configurazione controllo codice sorgente).  Ecco un elenco di tutte le origini di distribuzione configurate.
    ![Configurazione controllo codice sorgente amministrazione dei provider di risorse servizio app][2]

## <a name="configure-github"></a>Configurare GitHub

È necessario disporre di un account GitHub per completare questa attività. Si potrebbe voler utilizzare un account per l'organizzazione, anziché un account personale.

1. Accedere a GitHub, passare a https://www.github.com/settings/developers e fare clic su **registra una nuova applicazione**.
    ![GitHub - registrare una nuova applicazione][3]
2. Immettere un **nome applicazione** , ad esempio - servizio App nello Stack di Azure.
3. Immettere l'**URL della homepage**. L'URL della home page deve essere l'indirizzo del portale di Azure dello Stack. Ad esempio, https://portal.local.azurestack.external.
4. Immettere un **la descrizione dell'applicazione**.
5. Immettere il valore relativo all'**URL di callback autorizzazione**.  In una distribuzione di Azure Stack predefinita, l'Url è https://portal.local.azurestack.external/tokenauthorize il modulo, se si esegue in un sostituto di un dominio diverso del dominio per azurestack.local.
    ![GitHub - registrare una nuova applicazione con i valori popolati][4]
6. Fare clic su **Register application**.  Verrà ora visualizzata una pagina che elenca l'**ID client** e **il segreto client** per l'applicazione.
    ![GitHub - Registrazione applicazione completata][5]
7.  In una nuova scheda del browser o finestra Accedi al portale di amministrazione di Stack (https://adminportal.local.azurestack.external) Azure come amministratore del servizio.
8.  Passare a  **Resource Providers** (Provider di risorse) e selezionare **App Service Resource Provider Admin** (Amministrazione provider risorse servizio app).
9. Fare clic su **Source Control Configuration** (Configurazione controllo codice sorgente).
10. Copiare e incollare l'**ID client** e il **segreto client** nelle caselle di input corrispondenti per GitHub.
11. Fare clic su **Salva**.

## <a name="configure-bitbucket"></a>Configurare BitBucket

È necessario disporre di un account di servizio per completare questa attività. Si potrebbe voler utilizzare un account per l'organizzazione, anziché un account personale.

1. Accedere al servizio e passare a **integrazioni** con il proprio account.
    ![Dashboard BitBucket - integrazioni][7]
2. Fare clic su **OAuth** sotto la gestione di accesso e **Aggiungi consumer**.
    ![BitBucket aggiungere Consumer OAuth][8]
3. Immettere un **nome** per il consumer, ad esempio di servizio App nello Stack di Azure.
4. Immettere un **descrizione** per l'applicazione.
5. Copiare l'**URL di callback**.  In una distribuzione di Azure Stack predefinita, l'Url Callback è https://portal.local.azurestack.external/TokenAuthorize il modulo, se si esegue in un sostituto di un dominio diverso del dominio per azurestack.local.  L'URL deve seguire le lettere maiuscole come indicato di seguito per una corretta integrazione di BitBucket.
6. Immettere il **URL** -l'Url deve essere l'URL del portale Stack Azure, ad esempio https://portal.local.azurestack.external.
7. Selezionare il **autorizzazioni** necessarie:
    - **Repository**: *lettura*
    - **Webhook**: *lettura e scrittura*
8. Fare clic su **Salva**.  Verrà ora visualizzata questa nuova applicazione, insieme alla **chiave** e al **segreto** in **OAuth consumers** (Consumer OAuth).
    ![Elenco applicazioni BitBucket][9]
9.  In una nuova scheda del browser o finestra Accedi al portale di amministrazione di Stack (https://adminportal.local.azurestack.external) Azure come amministratore del servizio.
10.  Passare a  **Resource Providers** (Provider di risorse) e selezionare **App Service Resource Provider Admin** (Amministrazione provider risorse servizio app).
11. Fare clic su **Source Control Configuration** (Configurazione controllo codice sorgente).
12. Copiare e incollare la **chiave** nella casella di input **ID client** e **la chiave privata** nella casella di input **Segreto client** per BitBucket.
13. Fare clic su **Salva**.


## <a name="configure-onedrive"></a>Configurare OneDrive

È necessario disporre di un Account Microsoft collegato a un account OneDrive per completare questa attività.  Si potrebbe voler utilizzare un account per l'organizzazione, anziché un account personale.

> [!NOTE]
> Gli account OneDrive for Business non sono attualmente supportati.

1. Passare a https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm e accedere con l'account Microsoft.
2. In **applicazioni personali**, fare clic su **aggiungere un'app**.
![Applicazioni OneDrive][10]
3. Immettere un **nome** per la registrazione della nuova applicazione, immettere **Servizio App di Azure Stack** e fare clic su **Crea applicazione**
4. Nella schermata successiva sono elencate le proprietà della nuova applicazione. Record di **Id applicazione**. ![Proprietà dell'applicazione OneDrive][11]
5. In **applicazione segreti**, fare clic su **generare una nuova Password**. Prendere nota del **nuova password generata**. Questo è il segreto dell'applicazione e non è recuperabile dopo aver fatto clic **OK** in questa fase.
6. In **piattaforme** fare clic su **aggiungere piattaforma** e selezionare **Web**.
7. Immettere l'**URI di reindirizzamento**.  In una distribuzione di Azure Stack predefinita, l'URI di reindirizzamento è in https://portal.local.azurestack.external/tokenauthorize il modulo, se si esegue in un sostituto di un dominio diverso del dominio per azurestack.local ![applicazione OneDrive - Aggiungi Piattaforma Web][12]
8. Aggiungere il **le autorizzazioni di Microsoft Graph** - **autorizzazioni delegate**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![Applicazione OneDrive - Autorizzazioni di Microsoft Graph][13]
9. Fare clic su **Salva**.
10.  In una nuova scheda del browser o finestra Accedi al portale di amministrazione di Stack (https://adminportal.local.azurestack.external) Azure come amministratore del servizio.
11.  Passare a  **Resource Providers** (Provider di risorse) e selezionare **App Service Resource Provider Admin** (Amministrazione provider risorse servizio app).
12. Fare clic su **Source Control Configuration** (Configurazione controllo codice sorgente).
13. Copiare e incollare l'**ID applicazione** nella casella di input **ID client** e **la password** nella casella di input **Segreto client** per OneDrive.
14. Fare clic su **Salva**.

## <a name="configure-dropbox"></a>Configurare Dropbox

> [!NOTE]
> È necessario disporre di un account DropBox per completare questa attività.  È consigliabile usare un account per l'organizzazione anziché un account personale.

1. Sfoglia https://www.dropbox.com/developers/apps e accedere con l'Account dell'area di sincronizzazione.
2. Fare clic su **Create app**.

    ![Applicazioni dell'area di sincronizzazione][14]

3. Selezionare **DropBox API**.
4. Impostare l'accesso di livello **cartella App**.
5. Immettere un **nome** per l'applicazione.
![Registrazione applicazione Dropbox][15]
6. Fare clic su **Crea app**.  Verrà ora visualizzata una pagina che elenca le impostazioni per l'applicazione, tra cui la **chiave** e il **segreto app**.
7. Controllare il **nome della cartella App** è impostato su **servizio App di Azure stack**.
8. Impostare l'**URI di reindirizzamento OAuth 2** e fare clic su **Aggiungi**.  In una distribuzione di Azure Stack predefinita, l'URI di reindirizzamento è https://portal.local.azurestack.external/tokenauthorize il modulo, se si esegue in un sostituto di un dominio diverso del dominio per azurestack.local.
![Configurazione dell'applicazione dell'area di sincronizzazione][16]
9.  In una nuova scheda del browser o finestra Accedi al portale di amministrazione di Stack (https://adminportal.local.azurestack.external) Azure come amministratore del servizio.
10.  Passare a  **Resource Providers** (Provider di risorse) e selezionare **App Service Resource Provider Admin** (Amministrazione provider risorse servizio app).
11. Fare clic su **Source Control Configuration** (Configurazione controllo codice sorgente).
12. Copiare e incollare la **chiave applicazione** nella casella di input **ID client** e la **chiave privata app** nella casella di input **Segreto client** per DropBox.
13. Fare clic su **Salva**.


<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png

## <a name="next-steps"></a>Passaggi successivi

Gli utenti possono ora utilizzare le origini di distribuzione per elementi quali [distribuzione continua](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-continuous-deployment), [distribuzione Git locale](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-deploy-local-git), e [cloud sincronizzazione cartella](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-deploy-content-sync).
