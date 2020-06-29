---
title: "Esercitazione: Abilitare l'autenticazione in un'applicazione Web Python"
titleSuffix: Azure AD B2C
description: Questa esercitazione illustra come usare Azure Active Directory B2C per consentire l'accesso degli utenti a un'applicazione Web Python Flask.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 06/12/2020
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.custom: tracking-python
ms.openlocfilehash: 331f38f899c77f451fd43b81d7e83d73b3d5bcc1
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84782419"
---
# <a name="tutorial-enable-authentication-in-a-python-web-application-with-azure-ad-b2c"></a>Esercitazione: Abilitare l'autenticazione in un'applicazione Web Python con Azure AD B2C

Questa esercitazione illustra come usare Azure Active Directory B2C (Azure AD B2C) per l'iscrizione e l'accesso degli utenti in un'applicazione Web Python Flask.

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Aggiungere un URL di risposta a un'applicazione registrata nel tenant di Azure AD B2C
> * Scaricare il codice di esempio da GitHub
> * Modificare il codice dell'applicazione di esempio in modo che funzioni con il proprio tenant
> * Eseguire l'iscrizione usando il flusso utente di iscrizione/accesso

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare con i passaggi in questa esercitazione è necessario implementare le seguenti risorse di Azure AD B2C:

* [Tenant di Azure AD B2C](tutorial-create-tenant.md)
* L'[applicazione registrata](tutorial-register-applications.md) nel tenant e relativi *ID applicazione (client)* e *segreto client*
* [Flussi utente creati](tutorial-create-user-flows.md) nel tenant

In più, nell'ambiente di sviluppo locale devono anche essere installati gli elementi seguenti:

* [Visual Studio Code](https://code.visualstudio.com/) o un altro editor di codice
* [Python](https://nodejs.org/en/download/) 2.7 o 3 e versioni successive

## <a name="add-a-redirect-uri"></a>Aggiungere un URI di reindirizzamento

Nella seconda esercitazione completata come parte dei prerequisiti è stata registrata un'applicazione Web in Azure AD B2C. Per consentire la comunicazione con l'esempio in questa esercitazione, aggiungere un URL di risposta (anche noto come URI di reindirizzamento) alla registrazione dell'applicazione.

Per aggiornare un'applicazione nel tenant di Azure AD B2C, è possibile usare la nuova esperienza unificata **Registrazioni app** oppure l'esperienza legacy **Applicazioni (legacy)** . [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Registrazioni per l'app](#tab/app-reg-ga/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app**, la scheda **Applicazioni di cui si è proprietari** e quindi l'applicazione *webapp1*.
1. In **Gestisci** selezionare **Autenticazione**.
1. In **Web** selezionare il collegamento **Aggiungi un URI** e quindi immettere `http://localhost:5000/getAToken` nella casella di testo.
1. Selezionare **Salva**.

#### <a name="applications-legacy"></a>[Applicazioni (legacy)](#tab/applications-legacy/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Selezionare **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni (legacy)** e quindi l'applicazione *webapp1*.
1. In **URL di risposta** aggiungere `http://localhost:5000/getAToken`.
1. Selezionare **Salva**.
* * *

## <a name="get-the-sample-code"></a>Scaricare il codice di esempio

In questa esercitazione verrà configurato un esempio di codice che si scarica da GitHub per usare il proprio tenant B2C. L'esempio dimostra come un'applicazione Web Python Flask può usare Azure AD B2C per l'iscrizione e l'accesso degli utenti.

[Scaricare un file di archivio ZIP](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip) o clonare il [repository di esempi di codice](https://github.com/Azure-Samples/ms-identity-python-webapp) di GitHub.

```console
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

## <a name="update-the-sample"></a>Aggiornare l'esempio

Una volta ottenuto l'esempio, configurare l'applicazione in modo che usi il tenant di Azure AD B2C, la registrazione dell'applicazione e i flussi utente.

Nella directory radice del progetto:

1. Rinominare il file *app_config.py* in *app_config.py.OLD*
1. Rinominare il file *app_config_b2c.py* in *app_config.py*

Aggiornare il file *app_config.py* appena rinominato con i valori per il tenant di Azure AD B2C e per la registrazione dell'applicazione creati come parte dei prerequisiti.

1. Aprire il file *app_config.py* nell'editor.
1. Aggiornare il valore di `b2c_tenant` con il nome del tenant di Azure AD B2C, ad esempio *contosob2c*.
1. Aggiornare ogni valore di `*_user_flow` in base ai nomi dei flussi utente creati come parte dei prerequisiti.
1. Aggiornare il valore di `CLIENT_ID` con l'**ID applicazione (clienti)** dell'applicazione Web registrata come parte dei prerequisiti.
1. Aggiornare il valore di `CLIENT_SECRET` con il valore del **segreto client** creato nei prerequisiti. Per una maggiore sicurezza, valutare se archiviarlo all'interno di una **variabile di ambiente** come consigliato nei commenti.

La sezione superiore del file *app_config.py* sarà ora simile al frammento di codice seguente:

```python
import os

b2c_tenant = "contosob2c"
signupsignin_user_flow = "B2C_1_signupsignin1"
editprofile_user_flow = "B2C_1_profileediting1"
resetpassword_user_flow = "B2C_1_passwordreset1"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "22222222-2222-2222-2222-222222222222" # Placeholder - for use ONLY during testing.
# In a production app, we recommend you use a more secure method of storing your secret,
# like Azure Key Vault. Or, use an environment variable as described in Flask's documentation:
# https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
# CLIENT_SECRET = os.getenv("CLIENT_SECRET")
# if not CLIENT_SECRET:
#     raise ValueError("Need to define CLIENT_SECRET environment variable")
```

> [!WARNING]
> Come indicato nei commenti del frammento di codice, è consigliabile **non archiviare segreti in testo non crittografato** nel codice dell'applicazione. La variabile hardcoded viene usata nell'esempio di codice *solo per praticità*. Valutare se usare una variabile di ambiente o un archivio segreti come Azure Key Vault.

## <a name="run-the-sample"></a>Eseguire l'esempio

1. Nella console o nel terminale passare alla directory che contiene l'esempio. Ad esempio:

    ```console
    cd ms-identity-python-webapp
    ```
1. Eseguire i comandi seguenti per installare i pacchetti necessari da PyPi ed eseguire l'app Web nel computer locale:

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    La finestra della console visualizza il numero di porta dell'applicazione in esecuzione in locale:

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

1. Passare a `http://localhost:5000` per visualizzare l'applicazione Web in esecuzione nel computer locale.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-01.png" alt-text="Web browser che mostra un'applicazione Web Python Flask eseguita in locale":::

### <a name="sign-up-using-an-email-address"></a>Iscriversi usando un indirizzo di posta elettronica

Questa applicazione di esempio supporta l'iscrizione, l'accesso e la reimpostazione della password. In questa esercitazione si esegue l'iscrizione con un indirizzo di posta elettronica.

1. Selezionare **Accedi** per avviare il flusso utente *B2C_1_signupsignin1* specificato in un passaggio precedente.
1. Azure AD B2C presenta una pagina di accesso con un collegamento di iscrizione. Non avendo ancora un account, selezionare il collegamento **Iscriversi adesso**.
1. Il flusso di lavoro per l'iscrizione presenta una pagina per raccogliere e verificare l'identità dell'utente usando un indirizzo di posta elettronica. Il flusso di lavoro per l'iscrizione raccoglie anche la password dell'utente e gli attributi richiesti definiti nel flusso utente.

    Usare un indirizzo e-mail valido ed eseguire la convalida usando un codice di verifica. Impostare una password. Immettere i valori per gli attributi richiesti.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-02.png" alt-text="Pagina di iscrizione visualizzata dal flusso utente di Azure AD B2C":::

1. Selezionare **Crea** per creare un account locale nella directory di Azure AD B2C.

Quando si seleziona **Crea**, l'applicazione visualizza il nome dell'utente che ha eseguito l'accesso.

:::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-03.png" alt-text="Web browser che mostra un'applicazione Web Python Flask con l'utente connesso":::

Se si vuole testare l'accesso, selezionare il collegamento **Disconnetti**, quindi selezionare **Accedi** e accedere con l'indirizzo di posta elettronica e la password immessi al momento dell'iscrizione.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata configurata un'applicazione Web Python Flask per l'uso di un flusso utente nel tenant di Azure AD B2C per fornire le funzionalità di iscrizione e accesso. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * È stato aggiunto un URL di risposta a un'applicazione registrata nel tenant di Azure AD B2C
> * È stato scaricato il codice di esempio da GitHub
> * È stato modificato il codice dell'applicazione di esempio in modo che funzioni con il proprio tenant
> * È stata eseguita l'iscrizione usando il flusso utente di iscrizione/accesso

Successivamente, leggere informazioni su come personalizzare l'interfaccia utente delle pagine del flusso utente visualizzate agli utenti da Azure AD B2C:

> [!div class="nextstepaction"]
> [Esercitazione: Personalizzare l'interfaccia delle esperienze utente in Azure AD B2C >](tutorial-customize-ui.md)
