---
title: Personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato in Azure Active Directory B2C | Microsoft Docs
description: Informazioni sulla personalizzazione di un'interfaccia utente mediante un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e79d7a4b97f010b035f5c864682b4d3882a21393
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171908"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dopo aver completato questo articolo si avrà un criterio personalizzato per l'iscrizione e l'accesso con il proprio marchio e aspetto. Con Azure Active Directory B2C (Azure AD B2C) si ottiene il controllo quasi completo del contenuto HTML e CSS presentato agli utenti. Quando si usa un criterio personalizzato, si configura la personalizzazione dell'interfaccia utente in XML anziché usare i controlli nel portale di Azure. 

## <a name="prerequisites"></a>Prerequisiti

Completare la procedura descritta in [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md). È necessario disporre di un criterio personalizzato di lavoro per l'iscrizione e l'accesso con account locali.

## <a name="page-ui-customization"></a>Personalizzazione dell'interfaccia utente della pagina

Con la funzionalità di personalizzazione dell'interfaccia utente della pagina, è possibile definire l'aspetto di qualsiasi criterio personalizzato. È anche possibile mantenere la coerenza visiva e del marchio tra l'applicazione e Azure AD B2C.

Ecco come funziona: Azure AD B2C esegue il codice nel browser del cliente e usa un approccio moderno denominato [Condivisione di risorse tra le origini (CORS)](https://www.w3.org/TR/cors/). In primo luogo, specificare un URL nel criterio personalizzato con contenuto HTML personalizzato. Azure AD B2C unisce elementi dell'interfaccia utente con il contenuto HTML caricato dall'URL e quindi visualizza la pagina al cliente.

## <a name="create-your-html5-content"></a>Creare il contenuto HTML5

Creare il contenuto HTML con il nome del marchio del prodotto nel titolo.

1. Copiare il frammento di codice HTML seguente. Si tratta di codice HTML5 ben formato con un elemento vuoto denominato *\<div id="api"\>\</div\>* posizionato tra i tag *\<body\>* . Questo elemento indica il punto in cui deve essere inserito il contenuto di Azure AD B2C.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

2. Incollare il frammento di codice copiato in un editor di testo e quindi salvare il file con il nome *customize-ui.html*.

> [!NOTE]
> Gli elementi del form HTML verranno rimossi a causa di restrizioni di sicurezza se si usa login.microsoftonline.com. Usare b2clogin.com se si vuole usare elementi del form HTML nel contenuto HTML personalizzato. Vedere [usare b2clogin.com](b2clogin.md) per altri vantaggi.

## <a name="create-an-azure-blob-storage-account"></a>Creare un account di archiviazione BLOB di Azure

>[!NOTE]
> In questo articolo verrà usato l'archivio BLOB di Azure per ospitare il contenuto. È possibile scegliere di ospitare il contenuto in un server Web, ma è necessario [abilitare la condivisione di risorse tra le origini (CORS) nel server Web](https://enable-cors.org/server.html).

Per ospitare il contenuto HTML nell'archivio Blob, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu **Hub** selezionare **Nuovo** > **Archiviazione** > **Account di archiviazione**.
3. Selezionare una **sottoscrizione** per l'account di archiviazione.
4. Creare un **gruppo di risorse** o selezionarne uno esistente.
5. Immettere un **nome** unico per l'account di archiviazione.
6. Selezionare la **posizione geografica** dell'account di archiviazione. 
7. Per **Modello di distribuzione** è possibile lasciare **Resource Manager**.
8. Per **Prestazioni** è possibile lasciare **Standard**.
9. Modificare **Tipo di Account** in **archiviazione BLOB**.
10. Per **Replica** è possibile lasciare **RA-GRS**.
11. Per **Livello di accesso** è possibile lasciare **Frequente**. 
12. Fare clic su **Verifica + crea** per creare l'account di archiviazione.  
    Quando la distribuzione è terminata, il pannello dell'**account di archiviazione** si aprirà automaticamente.

## <a name="create-a-container"></a>Creare un contenitore

Per creare un contenitore pubblico nell'archivio BLOB seguire questa procedura:

1. In **servizio BLOB** scegliere **BLOB**dal menu a sinistra.
2. Fare clic su **+ contenitore**.
3. Per **nome**immettere *radice*. Può trattarsi di un nome a scelta, ad esempio *wingtiptoys*, ma in questo esempio viene usata la *radice* per semplicità.
4. Per **livello di accesso pubblico**selezionare **BLOB**e quindi **OK**.
5. Fare clic su **radice** per aprire il nuovo contenitore.
6. Fare clic su **Upload**.
7. Fare clic sull'icona della cartella accanto a **Selezionare un file**.
8. Passare a e selezionare **Customize-UI. html** creato in precedenza nella sezione relativa alla personalizzazione dell'interfaccia utente della pagina.
9. Se si desidera caricare in una sottocartella, espandere **Avanzate** e immettere un nome di cartella in **carica in cartella**.
10. Selezionare **Carica**.
11. Selezionare il BLOB **Customize-UI. html** caricato.
12. A destra della casella di testo **URL** selezionare l'icona **copia negli Appunti** per copiare l'URL negli Appunti.
13. Nel Web browser passare all'URL copiato per verificare che il BLOB caricato sia accessibile. Se è inaccessibile, ad esempio se si verifica un `ResourceNotFound` errore, verificare che il tipo di accesso del contenitore sia impostato su **BLOB**.

## <a name="configure-cors"></a>Configurare CORS

Configurare l'archivio BLOB per la condivisione di risorse tra le origini (CORS) seguendo questa procedura:

1. Nel menu selezionare **CORS**.
2. Per **Origini consentite** immettere `https://your-tenant-name.b2clogin.com`. Sostituire `your-tenant-name` con il nome del tenant di Azure AD B2C. Ad esempio `https://fabrikam.b2clogin.com`. È necessario usare solo lettere minuscole quando si immette il nome del tenant.
3. Per **Metodi consentiti** selezionare sia `GET` che `OPTIONS`.
4. Per **Intestazioni consentite** immettere un asterisco (*).
5. Per **Intestazioni esposte** immettere un asterisco (*).
6. Per **Età massima** immettere 200.
7. Fare clic su **Save**.

## <a name="test-cors"></a>Testare CORS

Verificare che tutte le operazioni preliminari siano state completate seguendo questa procedura:

1. Passare al sito Web [www.test-cors.org](https://www.test-cors.org/) e quindi incollare l'URL nella casella **Remote URL** (URL remoto).
2. Fare clic su **Send Request** (Invia richiesta).  
    Se si riceve un messaggio d'errore, verificare che le [impostazioni CORS](#configure-cors) siano corrette. Potrebbe anche essere necessario cancellare la cache del browser o aprire una sessione di esplorazione anonima premendo Ctrl+Maiusc+P.

## <a name="modify-the-extensions-file"></a>Modificare il file delle estensioni

Per configurare la personalizzazione dell'interfaccia utente, si copia **ContentDefinition** e i relativi elementi figlio dal file di base al file delle estensioni.

1. Aprire il file di base dei criteri, ad esempio *TrustFrameworkBase.xml*.
2. Cercare e copiare l'intero contenuto dell'elemento **ContentDefinitions**.
3. Aprire il file di estensione. ad esempio *TrustFrameworkExtensions.xml*. Cercare l'elemento **BuildingBlocks**. Se l'elemento non esiste, aggiungerlo.
4. Incollare l'intero contenuto dell'elemento **ContentDefinitions** copiato come figlio dell'elemento **BuildingBlocks**. 
5. Cercare l'elemento **ContentDefinition** che contiene `Id="api.signuporsignin"` nel codice XML copiato.
6. Cambiare il valore di **LoadUri** nell'URL del file HTML caricato nell'archivio. Ad esempio `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.
    
    I criteri personalizzati dovrebbero essere simili ai seguenti:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

7. Salvare il file delle estensioni.

## <a name="upload-your-updated-custom-policy"></a>Caricare il criterio personalizzato aggiornato

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Fare clic su **Framework dell'esperienza di gestione delle identità**.
2. Fare clic su **Tutti i criteri**.
3. Fare clic su **Carica il criterio**.
4. Caricare il file delle estensioni modificato in precedenza.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testare i criteri personalizzati tramite **Esegui adesso**

1. Nel pannello **Azure AD B2C** passare a **Tutti i criteri**.
2. Selezionare il criterio personalizzato che è stato caricato e fare clic sul pulsante **Esegui adesso**.
3. Dovrebbe essere possibile iscriversi usando un indirizzo di posta elettronica.

## <a name="reference"></a>Riferimenti

### <a name="sample-templates"></a>Modelli di esempio
È possibile trovare modelli di esempio per la personalizzazione dell'interfaccia utente qui:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

La cartella sample_templates/wingtip contiene i file HTML seguenti:

| Modello HTML5 | Descrizione |
|----------------|-------------|
| *phonefactor.html* | Usare questo file come modello per una pagina di autenticazione a più fattori. |
| *resetpassword.html* | Usare questo file come modello per una pagina Password dimenticata. |
| *selfasserted.html* | Usare questo file come modello per una pagina di iscrizione all'account di social networking, una pagina di iscrizione dell'account locale o una pagina di accesso dell'account locale. |
| *unified.html* | Usare questo file come modello per una pagina unificata per l'iscrizione o l'accesso. |
| *updateprofile.html* | Usare questo file come modello per una pagina di aggiornamento del profilo. |

Ecco i passaggi per usare l'esempio. 
1. Clonare il repository nel computer locale. Scegliere una cartella modello in sample_templates. È possibile utilizzare `wingtip` o `contoso`.
2. Caricare tutti i file nelle `css`cartelle, e `images` nell'archiviazione BLOB, `fonts`come descritto nelle sezioni precedenti. 
3. Aprire quindi ogni \*file con estensione HTML nella radice `wingtip` di o `contoso` (a seconda del valore selezionato nel primo passaggio) e sostituire tutte le istanze di "http://localhost" con gli URL dei file CSS, images e fonts caricati nel passaggio 2.
4. Salvare i \*file con estensione HTML e caricarli nell'archivio BLOB.
5. Modificare ora il file delle estensioni come indicato in precedenza in [modificare il file delle estensioni](#modify-the-extensions-file).
6. Se vengono visualizzati i tipi di carattere, le immagini o i file CSS mancanti, verificare i riferimenti nei criteri \*delle estensioni e nei file con estensione HTML.

### <a name="content-defintion-ids"></a>ID definizione contenuto

Nella sezione Modificare i criteri personalizzati di iscrizione o di accesso è stata configurata la definizione del contenuto per `api.idpselections`. Il set completo di ID di definizione del contenuto riconosciuti dal framework dell'esperienza di gestione delle identità di AD B2C e le relative descrizioni sono disponibili nella tabella seguente:

| ID definizione del contenuto | DESCRIZIONE | 
|-----------------------|-------------|
| *api.error* | **Pagina di errore**. Questa pagina viene visualizzata quando viene rilevata un'eccezione o un errore. |
| *api.idpselections* | **Pagina di selezione del provider di identità**. Questa pagina contiene un elenco dei provider di identità che l'utente può scegliere durante la procedura di accesso. Si tratta di provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook e Google+, o account locali. |
| *api.idpselections.signup* | **Selezione del provider di identità per l'iscrizione**. Questa pagina contiene un elenco dei provider di identità che l'utente può scegliere durante la procedura di iscrizione. Si tratta di provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook e Google+, o account locali. |
| *api.localaccountpasswordreset* | **Pagina Password dimenticata**. Questa pagina contiene un modulo che l'utente deve compilare per avviare la reimpostazione della password.  |
| *api.localaccountsignin* | **Pagina di accesso dell'account locale**. Questa pagina contiene un modulo per eseguire l'accesso con un account locale basato su indirizzo di posta elettronica o nome utente. Il modulo può contenere una casella di input di testo e una casella di immissione della password. |
| *api.localaccountsignup* | **Pagina di iscrizione dell'account locale**. Questa pagina contiene un modulo per eseguire l'iscrizione a un account locale basato su indirizzo di posta elettronica o nome utente. Il modulo può contenere diversi controlli di input, ad esempio una casella per l'immissione di testo, una casella per l'immissione della password, un pulsante di opzione, caselle a discesa a selezione singola e caselle di controllo con selezione multipla. |
| *api.phonefactor* | **Pagina dell'autenticazione a più fattori**. In questa pagina gli utenti possono verificare il proprio numero di telefono (tramite SMS o chiamata vocale) durante la procedura di iscrizione o di accesso. |
| *api.selfasserted* | **Pagina di iscrizione dell'account locale**. Questa pagina contiene un modulo di iscrizione che l'utente deve compilare per effettuare l'iscrizione usando un account esistente di un provider di identità basato su social network, ad esempio Facebook o Google+. Questa pagina è simile alla pagina di iscrizione dell'account di social networking illustrata in precedenza, a eccezione dei campi di immissione della password. |
| *api.selfasserted.profileupdate* | **Pagina di aggiornamento del profilo**. Questa pagina contiene un modulo che gli utenti possono usare per aggiornare il profilo. Questa pagina è simile alla pagina di iscrizione dell'account di social networking, a eccezione dei campi di immissione della password. |
| *api.signuporsignin* | **Pagina unificata per l'iscrizione o l'accesso**. Questa pagina consente di gestire sia l'iscrizione che l'accesso degli utenti, che possono usare provider di identità aziendali, provider di identità basati su social network come Facebook o Google+ o account locali.  |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli elementi dell'interfaccia utente che è possibile personalizzare, vedere la [guida di riferimento per la personalizzazione dell'interfaccia utente per i criteri predefiniti](active-directory-b2c-reference-ui-customization.md).
