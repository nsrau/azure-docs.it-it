---
title: "Avvio rapido: Aggiungere l'accesso con Microsoft a un'app Web Python | Azure"
titleSuffix: Microsoft identity platform
description: Questa guida di avvio rapido illustra come un'app Web Python può concedere l'accesso agli utenti, ottenere un token di accesso da Microsoft Identity Platform e chiamare l'API Microsoft Graph.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 1a8d851d2e70850155950786c6aa67c1d5086eb2
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95993874"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Guida introduttiva: Aggiungere l'accesso con Microsoft a un'app Web Python

In questa guida di avvio rapido si scarica e si esegue un esempio di codice di un'applicazione Web Python che consente agli utenti di accedere e ottenere un token di accesso per chiamare l'API Microsoft Graph. Possono accedere all'applicazione gli utenti con account Microsoft o un account di qualsiasi organizzazione Azure Active Directory (Azure AD).

Per un'illustrazione, vedere [Funzionamento dell'esempio](#how-the-sample-works).

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7+](https://www.python.org/downloads/release/python-2713) o [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-Session](https://pypi.org/project/Flask-Session/), [requests](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrare e scaricare l'app della guida introduttiva
>
> Per avviare l'applicazione della guida di avvio rapido sono disponibili due opzioni: rapida (Opzione 1) e manuale (Opzione 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice
>
> 1. Passare a [Registrazioni app nel portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs).
> 1. Immettere un nome per l'applicazione e fare clic su **Registra**.
> 1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio
>
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
>
> Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:
>
> 1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
> 1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
> 1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
> 1. Selezionare **Nuova registrazione**.
> 1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione.
>      - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `python-webapp`.
>      - In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
>      - Selezionare **Registra**.
>      - Nella pagina **Panoramica** dell'app prendere nota del valore del campo **ID applicazione (client)** per uso successivo.
> 1. Selezionare **Autenticazione** dal menu e quindi aggiungere le informazioni seguenti:
>    - Aggiungere la configurazione della piattaforma **Web**. Aggiungere `http://localhost:5000/getAToken` come valore di **URI di reindirizzamento**.
>    - Selezionare **Salva**.
> 1. Nel menu a sinistra scegliere **Certificati e segreti** e fare clic su **Nuovo segreto client** nella sezione **Segreti client**:
>
>      - Digitare una descrizione della chiave, ad esempio app secret.
>      - Selezionare **Tra 1 anno** per la durata della chiave.
>      - Quando si fa clic su **Aggiungi**, verrà visualizzato il valore della chiave.
>      - Copiare il valore della chiave. Sarà necessario più avanti.
> 1. Selezionare la sezione **Autorizzazioni API**
>
>      - Fare clic sul pulsante **Aggiungi un'autorizzazione** e quindi
>      - Verificare che la scheda **API Microsoft** sia selezionata
>      - Nella sezione *API Microsoft più usate* fare clic su **Microsoft Graph**
>      - Nella sezione **Autorizzazioni delegate** verificare che siano selezionate le autorizzazioni appropriate: **User.ReadBasic.All**. Se necessario, usare la casella di ricerca.
>      - Selezionare il pulsante **Aggiungi autorizzazioni**
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
>
> Per il corretto funzionamento del codice di esempio di questo argomento di avvio rapido, è necessario:
>
> 1. Aggiungere `http://localhost:5000/getAToken` come URL di risposta.
> 1. Creare un segreto client.
> 1. Aggiungere l'autorizzazione delegata User.ReadBasic.All dell'API Microsoft Graph.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta queste modifiche per me]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-aspnet-webapp/green-check.png) L'applicazione è configurata con questo attributo

#### <a name="step-2-download-your-project"></a>Passaggio 2: Scaricare il progetto
> [!div renderon="docs"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Scaricare il progetto ed estrarre il file con estensione zip in una cartella locale vicina alla cartella radice, ad esempio **C:\Azure-Samples**
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Passaggio 3: Configurare l'applicazione
>
> 1. Estrarre il file con estensione zip in una cartella locale vicina alla cartella radice, ad esempio **C:\Azure-Samples**
> 1. Se si usa un IDE (Integrated Development Environment), aprire l'esempio al suo interno.
> 1. Aprire il file **app_config.py**, presente nella cartella radice e sostituirne il contenuto con il frammento di codice seguente:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Dove:
>
> - `Enter_the_Application_Id_here` è l'ID applicazione dell'applicazione registrata.
> - `Enter_the_Client_Secret_Here` corrisponde al valore di **Segreto client** creato in **Certificati e segreti** per l'applicazione registrata.
> - `Enter_the_Tenant_Name_Here` - è il valore dell'**ID directory (tenant)** dell'applicazione registrata.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Passaggio 3: Eseguire il codice di esempio

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Passaggio 4: Eseguire il codice di esempio

1. È necessario installare la libreria MSAL Python, il framework Flask, Flask-Sessions per la gestione delle sessioni sul lato server e requests con pip come descritto di seguito:

    ```Shell
    pip install -r requirements.txt
    ```

2. Eseguire app.py dalla shell o dalla riga di comando:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Questa applicazione della guida introduttiva usa un segreto client per identificarsi come client riservato. Poiché il segreto client viene aggiunto come testo normale ai file di progetto, per motivi di sicurezza è consigliabile usare un certificato anziché un segreto client prima di considerare l'applicazione come applicazione di produzione. Per altre informazioni sull'uso di un certificato, vedere [queste istruzioni](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Ulteriori informazioni

### <a name="how-the-sample-works"></a>Funzionamento dell'esempio
![Mostra come funziona l'app di esempio generata da questo avvio rapido](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Recupero di MSAL
MSAL è la libreria usata per l'accesso degli utenti e per richiedere i token usati per accedere a un'API protetta da Microsoft Identity Platform.
È possibile aggiungere MSAL Python all'applicazione tramite PIP.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Inizializzazione della libreria MSAL
È possibile inserire il riferimento a MSAL Python aggiungendo il codice seguente all'inizio del file in cui verrà usato MSAL:

```Python
import msal
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle app Web che concedono l'accesso agli utenti in una serie di scenari in più parti.

> [!div class="nextstepaction"]
> [Scenario: App Web per l'accesso degli utenti](scenario-web-app-sign-user-overview.md)
