---
title: Autenticazione basata su intestazione con PingAccess per il proxy dell'applicazione di Azure AD | Microsoft Docs
description: Pubblicare le applicazioni con PingAccess e il proxy dell'applicazione per supportare l'autenticazione basata su intestazione.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab08c93662988655154cf300ac4ee3758fbc7872
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66472812"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Autenticazione basata su intestazione per l'accesso Single Sign-On con il proxy di applicazione e PingAccess

Azure Active Directory (Azure AD) Application Proxy ha collaborato con PingAccess in modo che i clienti di Azure AD possono accedere a più applicazioni in uso. PingAccess espande le [offerte esistenti del proxy di applicazione](application-proxy.md) in modo da includere l'accesso Single Sign-On alle applicazioni che usano intestazioni per l'autenticazione.

## <a name="whats-pingaccess-for-azure-ad"></a>Che cos'è PingAccess per Azure AD?

Con PingAccess per Azure AD, è possibile assegnare agli utenti l'accesso e single sign-on (SSO) alle applicazioni che usano intestazioni per l'autenticazione. Application Proxy tratta queste applicazioni come qualsiasi altra, usando Azure AD per autenticare l'accesso e quindi passando il traffico attraverso il servizio del connettore. PingAccess sta davanti alle applicazioni e converte il token di accesso da Azure AD in un'intestazione. L'applicazione riceve quindi l'autenticazione nel formato che può leggere.

Gli utenti non noteranno nulla di diverso quando eseguono l'accesso per usare le applicazioni aziendali. Possono comunque lavorare da qualsiasi luogo e dispositivo. I connettori del Proxy dell'applicazione indirizzano il traffico remoto a tutte le app indipendentemente dal loro tipo di autenticazione, in modo che verrà comunque bilanciare i carichi automaticamente.

## <a name="how-do-i-get-access"></a>Come si ottiene l'accesso?

Poiché questo scenario proveniente da una relazione tra Azure Active Directory e PingAccess, è necessario licenze per entrambi i servizi. Le sottoscrizioni Premium di Azure Active Directory, tuttavia, includono una licenza PingAccess di base che copre fino a 20 applicazioni. Se è necessario pubblicare più di 20 applicazioni basate su intestazione, è possibile acquistare una licenza aggiuntiva da PingAccess.

Per altre informazioni, vedere [Edizioni di Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Pubblicare l'applicazione in Azure

Questo articolo è destinato agli utenti di pubblicare un'applicazione con questo scenario per la prima volta. Oltre a descrivere nel dettaglio i passaggi di pubblicazione, viene illustrata la Guida introduttiva con Proxy dell'applicazione sia PingAccess. Se sono già configurati entrambi i servizi ma si desidera rivedere i passaggi di pubblicazione, ignorare il [aggiungere l'applicazione ad Azure AD con il Proxy di applicazione](#add-your-application-to-azure-ad-with-application-proxy) sezione.

> [!NOTE]
> Poiché questo scenario è il risultato di una partnership fra Azure AD e PingAccess, alcune delle istruzioni sono presenti sul sito di Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Installare un connettore proxy di applicazione

Se è stato abilitato il Proxy di applicazione abilitato e già installato un connettore, è possibile ignorare questa sezione e andare al [aggiungere l'applicazione ad Azure AD con il Proxy di applicazione](#add-your-application-to-azure-ad-with-application-proxy).

Il connettore del Proxy di applicazione è un servizio di Windows Server che indirizza il traffico dai dipendenti remoti alle applicazioni pubblicate. Per istruzioni di installazione più dettagliate, vedere [esercitazione: Aggiungere un'applicazione in locale per l'accesso remoto tramite il Proxy di applicazione in Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Accedi per il [portale di Azure Active Directory](https://aad.portal.azure.com/) come amministratore dell'applicazione. Il **interfaccia di amministrazione di Azure Active Directory** verrà visualizzata la pagina.
2. Selezionare **Azure Active Directory** > **proxy dell'applicazione** > **Scarica servizio connettore**. Il **Application Proxy Connector Download** verrà visualizzata la pagina.

   ![Download del connettore proxy dell'applicazione](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)
3. Seguire le istruzioni di installazione.

Scaricando il connettore deve abilitare automaticamente il Proxy di applicazione per la directory, ma in caso contrario, è possibile selezionare **abilitare il Proxy di applicazione**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Aggiungere l'applicazione ad Azure AD con il Proxy di applicazione

Nel portale di Azure è necessario eseguire due azioni. Per prima cosa, si pubblica l'applicazione con il proxy di applicazione, Quindi, è necessario raccogliere alcune informazioni sull'applicazione che è possibile usare durante la procedura PingAccess.

#### <a name="publish-your-application"></a>Pubblicare l'applicazione

Prima di tutto è possibile pubblicare l'applicazione. Questa azione include:

- Aggiunta dell'applicazione in locale ad Azure AD
- Assegna un utente di test dell'applicazione e scegliendo l'accesso SSO basato su intestazione
- Configurazione di URL di reindirizzamento dell'applicazione
- Concessione di autorizzazioni per gli utenti e ad altre applicazioni di usare l'applicazione in locale

Per pubblicare la propria applicazione in locale:

1. Se non è stato fatto nell'ultima sezione, accedere al [portale di Azure Active Directory](https://aad.portal.azure.com/) come amministratore dell'applicazione.
2. Selezionare **applicazioni aziendali** > **nuova applicazione** > **applicazione On-premises**. Il **aggiungere la propria applicazione in locale** verrà visualizzata la pagina.

   ![Aggiungere la propria applicazione in locale](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
3. Compilare i campi obbligatori con le informazioni relative alla nuova applicazione. Usare le indicazioni fornite di seguito per le impostazioni.

   > [!NOTE]
   > Per una procedura più dettagliata di questo passaggio, vedere [aggiungere un'app da sito locale ad Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **URL interno**: In genere si specifica l'URL che porta alla pagina di accesso dell'app quando si è nella rete aziendale. Per questo scenario, il connettore deve trattare il proxy PingAccess come prima pagina dell'applicazione. Usare il formato seguente: `https://<host name of your PingAccess server>:<port>`. La porta 3000 per impostazione predefinita, ma è possibile configurarla in PingAccess.

      > [!WARNING]
      > Per questo tipo di accesso single sign-on, è necessario usare l'URL interno `https` e non è possibile usare `http`.

   2. **Metodo di autenticazione preliminare**: Scegli **Azure Active Directory**.
   3. **Convertire l'URL nelle intestazioni**: Scegli **No**.

   > [!NOTE]
   > Se si tratta della prima applicazione, usare inizialmente la porta 3000 e aggiornare questa impostazione se viene modificata la configurazione di PingAccess. Per le applicazioni successive, la porta sarà necessario in modo che corrisponda il Listener è configurato in PingAccess. Altre informazioni sui [listener in PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).
4. Selezionare **Aggiungi**. Viene visualizzata la pagina di panoramica per la nuova applicazione.

A questo punto, assegnare un utente per test dell'applicazione e scegliere basato su intestazione single sign-on:

1. L'intestazione laterale dell'applicazione, selezionare **utenti e gruppi** > **Add user** > **gli utenti e gruppi (\<numero > selezionati)** . Viene visualizzato un elenco di utenti e gruppi per cui scegliere.

   ![Utenti e gruppi](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)
2. Selezionare un utente per test dell'applicazione e selezionare **seleziona**. Assicurarsi che questo account di test abbia accesso all'applicazione locale.
3. Selezionare **Assegna**.
4. L'intestazione laterale dell'applicazione, selezionare **l'accesso Single sign-on** > **basato su intestazione**.

   > [!TIP]
   > Se è la prima volta che si usa l'accesso Single Sign-On basato su intestazione, è necessario installare PingAccess. Per assicurarsi che la sottoscrizione di Azure venga associata automaticamente all'installazione di PingAccess, usare il collegamento presente nella pagina dell'accesso Single Sign-On per scaricare PingAccess. È possibile aprire il sito di download ora o in un secondo momento.

   ![Accesso basato su intestazione](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)
5. Selezionare **Salva**.

Quindi verificare che l'URL è impostato per l'URL esterno di reindirizzamento:

1. Dal **interfaccia di amministrazione di Azure Active Directory** barra laterale selezionare **Azure Active Directory** > **registrazioni per l'App**. Viene visualizzato un elenco delle applicazioni.

   ![Registrazioni per l'app](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)
2. Selezionare l'applicazione.
3. Selezionare il collegamento accanto a **URI di reindirizzamento**, che mostra il numero di redirect URI impostato per web e client pubblici. Il  **\<nome applicazione >-autenticazione** verrà visualizzata la pagina.
4. Verificare se l'URL esterno assegnato all'applicazione in precedenza si trova il **URI di reindirizzamento** elenco. Se non lo è, aggiungere l'URL esterno a questo punto, usando un tipo URI di reindirizzamento del **Web**e selezionare **salvare**.

Infine, configurare l'applicazione in locale in modo che gli utenti hanno accesso in lettura e altre applicazioni hanno accesso in lettura/scrittura:

1. Dal **registrazioni per l'App** nella barra laterale per l'applicazione, selezionare **autorizzazioni delle API** > **aggiungere un'autorizzazione**  >   **Le API di Microsoft** > **Microsoft Graph**. Il **le autorizzazioni API Request** pagina **Microsoft Graph** viene visualizzato, che contiene le API per Windows Azure Active Directory.

   ![Richiedere le autorizzazioni API](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)
2. Selezionare **autorizzazioni delegate** > **utente** > **User. Read**.
3. Selezionare **autorizzazioni applicazione** > **applicazione** > **Application.ReadWrite.All**.
4. Selezionare **aggiungere autorizzazioni**.
5. Nel **le autorizzazioni API** pagina, selezionare **concedere il consenso dell'amministratore per \<il nome della directory >** .

#### <a name="collect-information-for-the-pingaccess-steps"></a>Raccogliere informazioni per la procedura PingAccess

È necessario raccogliere questi tre tipi di informazioni (tutti i GUID) per configurare l'applicazione con PingAccess:

| Nome del campo di Azure AD | Nome del campo di PingAccess | Formato dati |
| --- | --- | --- |
| **ID applicazione (client)** | **ID client** | GUID |
| **ID di directory (tenant)** | **Issuer** | GUID |
| `PingAccess key` | **Segreto client** | Stringa casuale |

Per raccogliere queste informazioni:

1. Dal **interfaccia di amministrazione di Azure Active Directory** barra laterale selezionare **Azure Active Directory** > **registrazioni per l'App**. Viene visualizzato un elenco delle applicazioni.
2. Selezionare l'applicazione. Il **registrazioni per l'App** pagina per l'applicazione viene visualizzato.

   ![Panoramica di registrazione per un'applicazione](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)
3. Accanto al **ID applicazione (client)** valore, selezionare la **copia negli Appunti** icona, quindi copiare e salvare il file. Si specifica questo valore in un secondo momento come ID del client. di PingAccess
4. Avanti il **ID di Directory (tenant)** di valore, selezionare anche **copia negli Appunti**, quindi copiare e salvare il file. Specificare questo valore in un secondo momento come autorità di certificazione di PingAccess.
5. Dalla barra laterale del **registrazioni per l'App** per l'applicazione, selezionare **i certificati e i segreti** > **nuovo segreto client**. Il **aggiungere un segreto client** verrà visualizzata la pagina.

   ![Aggiungere un segreto client](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)
6. Nelle **Description**, tipo `PingAccess key`.
7. Sotto **Expires**, scegliere come impostare la chiave di PingAccess: **Tra 1 anno**, **In 2 anni**, o **Never**.
8. Selezionare **Aggiungi**. Di PingAccess chiave viene visualizzata nella tabella dei segreti client, con uno casuale che viene inserita automaticamente nella stringa di **valore** campo.
9. Accanto alla chiave di PingAccess **valore** campi, selezionare la **copia negli Appunti** icona, quindi copiare e salvare il file. Specificare questo valore in un secondo momento come segreto client di PingAccess.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>Aggiornare GraphAPI per inviare campi personalizzati (facoltativo)

Se necessaria un'attestazione personalizzata che invia altri token entro il token di accesso utilizzato da PingAccess, impostare il `acceptMappedClaims` campo applicazione `True`. È possibile usare Graph Explorer o manifesto dell'applicazione del portale di Azure AD per apportare questa modifica.

**In questo esempio viene usato Graph Explorer:**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**Questo esempio Usa la [portale di Azure Active Directory](https://aad.portal.azure.com/) per aggiornare il `acceptMappedClaims` campo:**

1. Accedi per il [portale di Azure Active Directory](https://aad.portal.azure.com/) come amministratore dell'applicazione.
2. Passare ad **Azure Active Directory** > **Registrazioni per l'app**. Viene visualizzato un elenco delle applicazioni.
3. Selezionare l'applicazione.
4. Dalla barra laterale del **registrazioni per l'App** pagina relativa all'applicazione, seleziona **manifesto**. Viene visualizzato il codice JSON del manifesto per la registrazione dell'applicazione.
5. Cercare il `acceptMappedClaims` campo e modificare il valore in `True`.
6. Selezionare **Salva**.


### <a name="use-of-optional-claims-optional"></a>Utilizzo di attestazioni facoltative (facoltative)
Attestazioni facoltative consente di aggiungere attestazioni standard-but-not-included-by-default contenente tutti gli utenti e tenant. È possibile configurare attestazioni facoltative per l'applicazione modificando il manifesto dell'applicazione. Per altre informazioni, vedere il [comprendere l'articolo del manifesto dell'applicazione di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Ad esempio includere indirizzo di posta elettronica nel token di accesso che utilizzano PingAccess:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Uso di criteri (facoltativo) di mapping delle attestazioni
[Criteri di Mapping (anteprima) di attestazioni](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties/) per gli attributi che non esistono in Azure ad. Mapping delle attestazioni consente di migrare le app precedenti dall'ambiente locale al cloud aggiungendo altre attestazioni personalizzate che sono supportate da oggetti di ad FS o un utente

Per rendere l'applicazione usi un'attestazione personalizzata e includa campi aggiuntivi, assicurarsi aver anche [creati criteri di mapping delle attestazioni personalizzate e averlo assegnato all'applicazione](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Per usare un'attestazione personalizzata, è anche necessario avere un criterio personalizzato definito e assegnato all'applicazione. Questo criterio deve includere tutti gli attributi personalizzati necessari.
>
> È possibile eseguire la definizione dei criteri e l'assegnazione tramite PowerShell, Azure AD Graph Explorer o Microsoft Graph. Se si sta eseguendo li in PowerShell, potrebbe essere necessario utilizzare innanzitutto `New-AzureADPolicy` e quindi assegnarlo all'applicazione con `Add-AzureADServicePrincipalPolicy`. Per altre informazioni, vedere [assegnazione di criteri di mapping di attestazioni](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Esempio:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id 
```

### <a name="enable-pingaccess-to-use-custom-claims-optional-but-required-if-you-expect-the-application-to-consume-additional-claims"></a>Abilitare PingAccess usare le attestazioni personalizzate (facoltativa, ma obbligatorio se si prevede che l'applicazione di utilizzare le attestazioni aggiuntive)
Quando si configurerà PingAccess nel passaggio seguente, la sessione Web viene creata (Impostazioni -> accesso -> Web sessioni) deve avere **richieste profilo** deselezionata e **aggiornare gli attributi utente** Impostare su **No**

## <a name="download-pingaccess-and-configure-your-application"></a>Scaricare PingAccess e configurare l'applicazione

Dopo aver completato la procedura di installazione di Azure Active Directory, è possibile ora passare alla configurazione di PingAccess.

I passaggi dettagliati per la parte PingAccess di questo scenario continuano nella documentazione di Ping Identity. Seguire le istruzioni in [configurare PingAccess per Azure AD proteggere le applicazioni pubblicate con Microsoft Azure AD Application Proxy](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) sul sito web di Ping Identity.

Tali passaggi consentono di installare PingAccess e configurare un account PingAccess (se non si ha già uno). Quindi per creare una connessione di Azure AD OpenID Connect (OIDC), configura un provider di token con la **ID di Directory (tenant)** valore copiato dal portale di Azure AD. Successivamente, per creare una sessione web in PingAccess, usare il **ID applicazione (client)** e `PingAccess key` valori. Successivamente è possibile impostare i mapping delle identità e creare l'host virtuale, il sito e l'applicazione.

### <a name="test-your-application"></a>Testare l'applicazione

Dopo aver completato tutti questi passaggi, l'applicazione deve essere in esecuzione. Per testarla, aprire un browser e passare all'URL esterno creato quando è stata pubblicata l'applicazione in Azure. Accedere con l'account di test che è assegnato all'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare PingAccess per Azure AD proteggere le applicazioni pubblicate con Proxy applicazione di Microsoft Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](what-is-single-sign-on.md)
- [Risolvere i problemi e i messaggi di errore del proxy dell'applicazione](application-proxy-troubleshoot.md)
