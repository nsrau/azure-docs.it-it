---
title: Avvio rapido per l'uso di Microsoft Identity Platform con un'app Web Python | Azure
description: Informazioni su come implementare l'accesso Microsoft in un'app Web Python usando Auth2
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
editor: ''
ms.assetid: 9551f0b5-04f2-44d7-87b5-756409180fe9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 587e7a82e2a9cde8ff6d08274928ab22aa969061
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309616"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Guida introduttiva: Aggiungere l'accesso con Microsoft a un'app Web Python

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Questa guida di avvio rapido descrive come integrare un'app Web Python con Microsoft Identity Platform. L'app consentirà l'accesso di un utente, otterrà un token di accesso per chiamare l'API Microsoft Graph e creerà una richiesta all'API Microsoft Graph.

Al termine della guida, l'applicazione accetterà accessi di account Microsoft personali (ad esempio outlook.com, live.com e di altro tipo) e di account aziendali o dell'istituto di istruzione di qualsiasi azienda o organizzazione che usa Azure Active Directory.

![Mostra come funziona l'app di esempio generata da questo avvio rapido](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>Prerequisiti

Ecco i prerequisiti per eseguire questo esempio:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) o [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-Session](https://pythonhosted.org/Flask-Session/), [requests](https://2.python-requests.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) 
- Un tenant di Azure Active Directory (Azure AD). Per altre informazioni su come ottenere un tenant di Azure AD, vedere la [Come ottenere un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrare e scaricare l'app della guida introduttiva
>
> Per avviare l'applicazione della guida di avvio rapido sono disponibili due opzioni: rapida (Opzione 1) e manuale (Opzione 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice
>
> 1. Passare a [Registrazioni app nel portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Selezionare **Nuova registrazione**.
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
>      - Nell'elenco a discesa nella sezione **URI di reindirizzamento** selezionare la piattaforma **Web** e quindi impostare il valore su `http://localhost:5000/getAToken`.
>      - Selezionare **Registra**. Nella pagina **Panoramica** dell'app prendere nota del valore del campo **ID applicazione (client)** per uso successivo.
> 1. Nel menu a sinistra scegliere **Certificati e segreti** e fare clic su **Nuovo segreto client** nella sezione **Segreti client**:
>
>      - Digitare una descrizione della chiave, ad esempio app secret.
>      - Selezionare **Tra 1 anno** per la durata della chiave.
>      - Quando si fa clic su **Aggiungi**, verrà visualizzato il valore della chiave.
>      - Copiare il valore della chiave. Sarà necessario più avanti.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
>
> Per il corretto funzionamento del codice di esempio di questo argomento di avvio rapido, è necessario:
>
> 1. Aggiungere `http://localhost:5000/getAToken` come URL di risposta.
> 1. Creare un segreto client.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta questa modifica per me]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-aspnet-webapp/green-check.png) L'applicazione è configurata con questo attributo

#### <a name="step-2-download-your-project"></a>Passaggio 2: Scaricare il progetto

[Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>Passaggio 3: Configurare l'applicazione

1. Estrarre il file con estensione zip in una cartella locale vicina alla cartella radice, ad esempio **C:\Azure-Samples**
1. Se si usa un IDE (Integrated Development Environment), aprire l'esempio al suo interno.
1. Aprire il file **app_config.py**, presente nella cartella radice e sostituirne il contenuto con il frammento di codice seguente:

```python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
```

> [!div renderon="docs"]
> Dove:
>
> - `Enter_the_Application_Id_here` è l'ID applicazione dell'applicazione registrata.
> - `Enter_the_Client_Secret_Here` corrisponde al valore di **Segreto client** creato in **Certificati e segreti** per l'applicazione registrata.

#### <a name="step-4-run-the-code-sample"></a>Passaggio 4: Eseguire il codice di esempio

1. È necessario installare la libreria MSAL Python, il framework Flask, Flask-Sessions per la gestione delle sessioni sul lato server e requests con pip come descritto di seguito:

   ```Shell
   pip install -r requirements.txt
   ```

2. Eseguire app.py dalla shell o dalla riga di comando:

   ```Shell
   python app.py
   ```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle app Web che effettuano l'accesso degli utenti e che chiamano le API Web:

> [!div class="nextstepaction"]
> [Scenario: App Web che effettuano l'accesso degli utenti](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
