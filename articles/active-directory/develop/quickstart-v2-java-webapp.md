---
title: Avvio rapido per l'uso di Microsoft Identity Platform con un'app Web Java | Azure
description: Informazioni su come implementare l'accesso Microsoft in un'app Web Java usando OpenID Connect
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java
ms.openlocfilehash: 1a5401ee76f60c9b9c2a75561cad8f6571d3b308
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920702"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Guida introduttiva: Aggiungere l'accesso con Microsoft a un'app Web Java

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Questo argomento di avvio rapido descrive come integrare un'app Web Java con Microsoft Identity Platform. L'app consentirà l'accesso di un utente, otterrà un token di accesso per chiamare l'API Microsoft Graph e creerà una richiesta all'API Microsoft Graph.

Al termine dell'avvio rapido, l'applicazione accetterà accessi di account Microsoft personali (ad esempio outlook.com, live.com e di altro tipo) e di account aziendali o dell'istituto di istruzione di qualsiasi azienda e organizzazione che usa Azure Active Directory.

![Mostra come funziona l'app di esempio generata da questo avvio rapido](media/quickstart-v2-java-webapp/java-quickstart.svg)

## <a name="prerequisites"></a>Prerequisiti

Ecco i prerequisiti per eseguire questo esempio:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 o versione successiva e [Maven](https://maven.apache.org/).
- Un tenant di Azure Active Directory (Azure AD). Per altre informazioni su come ottenere un tenant di Azure AD, vedere [Come ottenere un tenant di Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-howto-tenant/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrare e scaricare l'app della guida introduttiva
> Per avviare l'applicazione della guida di avvio rapido sono disponibili due opzioni: rapida (Opzione 1) o manuale (Opzione 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice
>
> 1. Passare a [Registrazioni app nel portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
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
>
> 1. Passare alla pagina [Registrazioni app](/azure/active-directory/develop/) di Microsoft Identity Platform per sviluppatori.
> 1. Selezionare **Nuova registrazione**.
> 1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione.
>    - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `java-webapp`.
>    - Lasciare vuoto il campo **URI di reindirizzamento** per il momento e selezionare **Registra**.
> 1. Nella pagina **Informazioni generali** individuare i valori di **ID applicazione (client)** e **ID della directory (tenant)** dell'applicazione. Copiare questi valori per un utilizzo successivo.
> 1. Selezionare **Autenticazione** dal menu e quindi aggiungere le informazioni seguenti:
>    - In **URI di reindirizzamento** aggiungere `http://localhost:8080/msal4jsamples/secure/aad` e `http://localhost:8080/msal4jsamples/graph/me`.
>    - Selezionare **Salva**.
> 1. Nel menu a sinistra scegliere **Certificati e segreti** e nella sezione **Segreti client** fare clic su **Nuovo segreto client**:
>
>    - Digitare una descrizione della chiave (ad esempio, segreto dell'app).
>    - Selezionare **Tra 1 anno** per la durata della chiave.
>    - Il valore della chiave viene visualizzato quando si seleziona **Aggiungi**.
>    - Copiare il valore della chiave per un utilizzo successivo. Il valore della chiave non viene visualizzato di nuovo, né può essere recuperato in altro modo, di conseguenza è opportuno prenderne nota non appena viene visualizzato nel portale di Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
>
> Per il corretto funzionamento del codice di esempio di questo argomento di avvio rapido, è necessario:
>
> 1. Aggiungere gli URL di risposta, come `http://localhost:8080/msal4jsamples/secure/aad` e `http://localhost:8080/msal4jsamples/graph/me`.
> 1. Creare un segreto client.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta queste modifiche per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-aspnet-webapp/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-the-code-sample"></a>Passaggio 2: Scaricare il codice di esempio

 [Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

#### <a name="step-3-configure-the-code-sample"></a>Passaggio 3: Configurare il codice di esempio

 1. Estrarre il file ZIP in una cartella locale.
 1. Se si usa un IDE (Integrated Development Environment), aprire l'esempio al suo interno.

 1. Aprire il file application.properties, disponibile nella cartella src/main/resources/, e sostituire il valore dei campi *aad.clientId*, *aad.authority* e *aad.secretKey* con i rispettivi valori di **ID applicazione**, **ID tenant** e **Segreto client** come segue:

    ```file
    aad.clientId=Enter_the_Application_Id_here
    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
    aad.secretKey=Enter_the_Client_Secret_Here
    aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
    aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
    ```

> [!div renderon="docs"]
> Dove:
>
> - `Enter_the_Application_Id_here` è l'ID applicazione dell'applicazione registrata.
> - `Enter_the_Client_Secret_Here` corrisponde al valore di **Segreto client** creato in **Certificati e segreti** per l'applicazione registrata.
> - `Enter_the_Tenant_Info_Here` - è il valore dell'**ID directory (tenant)** dell'applicazione registrata.

#### <a name="step-4-run-the-code-sample"></a>Passaggio 4: Eseguire il codice di esempio

Per eseguire il progetto, è possibile effettuare una delle operazioni seguenti:

Eseguirlo direttamente dall'ambiente IDE usando il server Spring Boot incorporato o comprimerlo in un file WAR mediante [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) e distribuirlo in una soluzione contenitore J2EE, ad esempio [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Esecuzione dall'ambiente IDE

Se si esegue l'applicazione Web da un ambiente IDE, fare clic su Esegui, quindi passare alla home page del progetto. Per questo esempio, l'URL dell'home page standard è http://localhost:8080

1. Nella pagina iniziale selezionare il pulsante **Login** (Accedi) per essere reindirizzati ad Azure Active Directory e richiedere le credenziali all'utente.

1. Dopo l'autenticazione, l'utente viene reindirizzato a *http://localhost:8080/msal4jsamples/secure/aad* . Una volta eseguito l'accesso, nella pagina vengono visualizzate le informazioni sull'account. L'interfaccia utente di esempio presenta i pulsanti seguenti:
    - *Disconnetti*: disconnette l'utente corrente dall'applicazione e lo reindirizza alla home page.
    - *Show User Info* (Mostra informazioni utente): acquisisce un token per Microsoft Graph e chiama Microsoft Graph con una richiesta contenente il token, che restituisce informazioni di base sull'utente che ha eseguito l'accesso.

> [!IMPORTANT]
> Questa applicazione della guida introduttiva usa un segreto client per identificarsi come client riservato. Poiché il segreto client viene aggiunto come testo normale ai file di progetto, per motivi di sicurezza è consigliabile usare un certificato anziché un segreto client prima di considerare l'applicazione come applicazione di produzione. Per altre informazioni sull'utilizzo di un certificato, vedere [Credenziali del certificato per l'autenticazione dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Altre informazioni

### <a name="getting-msal"></a>Recupero di MSAL

MSAL per Java (MSAL4J) è la libreria Java usata per far accedere gli utenti e richiedere i token usati per accedere a un'API protetta da Microsoft Identity Platform.

Aggiungere MSAL4J all'applicazione usando Maven o Gradle per gestire le dipendenze apportando le modifiche seguenti al file pom.xml (Maven) o build.gradle (Gradle) dell'applicazione.

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Inizializzazione della libreria MSAL

Aggiungere un riferimento a MSAL per Java aggiungendo il codice seguente all'inizio del file in cui verrà usato MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle autorizzazioni e sul consenso:

> [!div class="nextstepaction"]
> [Autorizzazioni e consenso](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Per altre informazioni sul flusso di autenticazione per questo scenario, vedere il flusso del codice di autorizzazione Oauth 2.0:

> [!div class="nextstepaction"]
> [Flusso Oauth del codice di autorizzazione](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Contribuire al miglioramento di Microsoft Identity Platform. Completare un breve sondaggio di due domande per condividere la propria opinione.

> [!div class="nextstepaction"]
> [Sondaggio su Microsoft Identity Platform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
