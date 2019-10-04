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
ms.openlocfilehash: 0544ed0ff217b6e37cca22a1fc1e0048b30da462
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694227"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Autenticazione basata su intestazione per l'accesso Single Sign-On con il proxy di applicazione e PingAccess

Il proxy dell'applicazione Azure Active Directory (Azure AD) ha collaborato con PingAccess in modo che i clienti Azure AD possano accedere a più applicazioni. PingAccess espande le [offerte esistenti del proxy di applicazione](application-proxy.md) in modo da includere l'accesso Single Sign-On alle applicazioni che usano intestazioni per l'autenticazione.

## <a name="whats-pingaccess-for-azure-ad"></a>Che cos'è PingAccess per Azure AD?

Con PingAccess per Azure AD, è possibile concedere agli utenti l'accesso e Single Sign-on (SSO) alle applicazioni che usano le intestazioni per l'autenticazione. Application Proxy tratta queste applicazioni come qualsiasi altra, usando Azure AD per autenticare l'accesso e quindi passando il traffico attraverso il servizio del connettore. PingAccess si trova davanti alle applicazioni e converte il token di accesso da Azure AD in un'intestazione. L'applicazione riceve quindi l'autenticazione nel formato che può leggere.

Gli utenti non noteranno nulla di diverso quando eseguono l'accesso per usare le applicazioni aziendali. Possono comunque lavorare da qualsiasi luogo e dispositivo. I connettori del proxy di applicazione indirizzano il traffico remoto a tutte le app indipendentemente dal tipo di autenticazione, in modo che i carichi vengano comunque bilanciati automaticamente.

## <a name="how-do-i-get-access"></a>Come si ottiene l'accesso?

Poiché questo scenario deriva da una relazione tra Azure Active Directory e PingAccess, sono necessarie le licenze per entrambi i servizi. Le sottoscrizioni Premium di Azure Active Directory, tuttavia, includono una licenza PingAccess di base che copre fino a 20 applicazioni. Se è necessario pubblicare più di 20 applicazioni basate su intestazione, è possibile acquistare una licenza aggiuntiva da PingAccess.

Per altre informazioni, vedere [Edizioni di Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Pubblicare l'applicazione in Azure

Questo articolo consente agli utenti di pubblicare un'applicazione con questo scenario per la prima volta. Oltre a illustrare in dettaglio i passaggi di pubblicazione, viene illustrato come iniziare a usare sia il proxy di applicazione che PingAccess. Se sono già stati configurati entrambi i servizi ma si vuole un aggiornamento dei passaggi di pubblicazione, passare alla sezione [aggiungere un'applicazione a Azure ad con il proxy di applicazione](#add-your-application-to-azure-ad-with-application-proxy) .

> [!NOTE]
> Poiché questo scenario è il risultato di una partnership fra Azure AD e PingAccess, alcune delle istruzioni sono presenti sul sito di Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Installare un connettore proxy di applicazione

Se è stato abilitato il proxy di applicazione e si è già installato un connettore, è possibile ignorare questa sezione e passare a [aggiungere l'applicazione a Azure ad con il proxy di applicazione](#add-your-application-to-azure-ad-with-application-proxy).

Il connettore del proxy di applicazione è un servizio di Windows Server che indirizza il traffico dai dipendenti remoti alle applicazioni pubblicate. Per istruzioni di installazione più dettagliate, [vedere Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in](application-proxy-add-on-premises-application.md)Azure Active Directory.

1. Accedere al portale di [Azure Active Directory](https://aad.portal.azure.com/) come amministratore dell'applicazione. Viene visualizzata la pagina **centro di amministrazione Azure Active Directory** .
1. Selezionare **Azure Active Directory** > **servizio del connettore di download**del proxy > di**applicazione**. Viene visualizzata la pagina di **download del connettore del proxy di applicazione** .

   ![Download del connettore del proxy di applicazione](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Seguire le istruzioni di installazione.

Il download del connettore dovrebbe abilitare automaticamente il proxy di applicazione per la directory. in caso contrario, è possibile selezionare **Abilita proxy di applicazione**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Aggiungere l'applicazione a Azure AD con il proxy di applicazione

Nel portale di Azure è necessario eseguire due azioni. Per prima cosa, si pubblica l'applicazione con il proxy di applicazione, Quindi, è necessario raccogliere alcune informazioni sull'applicazione che è possibile usare durante la procedura PingAccess.

#### <a name="publish-your-application"></a>Pubblicare l'applicazione

Per prima cosa è necessario pubblicare l'applicazione. Questa azione comporta:

- Aggiunta dell'applicazione locale a Azure AD
- Assegnazione di un utente per il test dell'applicazione e scelta di SSO basato su intestazione
- Impostazione dell'URL di reindirizzamento dell'applicazione
- Concessione di autorizzazioni per utenti e altre applicazioni per l'uso dell'applicazione locale

Per pubblicare l'applicazione locale:

1. Se non è stato fatto nell'ultima sezione, accedere al [portale di Azure Active Directory](https://aad.portal.azure.com/) come amministratore dell'applicazione.
1. Selezionare **applicazioni** > aziendali**nuova** > applicazione applicazione**locale**. Viene visualizzata la pagina **Aggiungi applicazione locale** .

   ![Aggiungi applicazione locale personalizzata](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Compilare i campi obbligatori con le informazioni sulla nuova applicazione. Usare le linee guida seguenti per le impostazioni.

   > [!NOTE]
   > Per una procedura dettagliata più dettagliata di questo passaggio, vedere [aggiungere un'app locale a Azure ad](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **URL interno**: Normalmente si fornisce l'URL che porta alla pagina di accesso dell'app quando si è nella rete aziendale. Per questo scenario, il connettore deve trattare il proxy PingAccess come pagina iniziale dell'applicazione. Usare il formato seguente: `https://<host name of your PingAccess server>:<port>`. La porta 3000 per impostazione predefinita, ma è possibile configurarla in PingAccess.

      > [!WARNING]
      > Per questo tipo di Single Sign-on, l'URL interno deve usare `https` e non può `http`usare.

   1. **Metodo di autenticazione preliminare**: Scegliere **Azure Active Directory**.
   1. **Convertire l'URL nelle intestazioni**: Scegliere **No**.

   > [!NOTE]
   > Se si tratta della prima applicazione, usare inizialmente la porta 3000 e aggiornare questa impostazione se viene modificata la configurazione di PingAccess. Per le applicazioni successive, sarà necessario che la porta corrisponda al listener configurato in PingAccess. Altre informazioni sui [listener in PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Selezionare **Aggiungi**. Verrà visualizzata la pagina Panoramica per la nuova applicazione.

A questo punto assegnare un utente per il test dell'applicazione e scegliere Single Sign-on basato su intestazione:

1. Dalla barra laterale dell'applicazione selezionare **utenti e gruppi** > Aggiungi utenti e gruppi**utente** >  **(\<numero > selezionati)** . Viene visualizzato un elenco di utenti e gruppi tra cui scegliere.

   ![Mostra l'elenco di utenti e gruppi](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Selezionare un utente per il test dell'applicazione e selezionare **Seleziona**. Assicurarsi che questo account di test abbia accesso all'applicazione locale.
1. Selezionare **Assegna**.
1. Dalla barra laterale dell'applicazione selezionare **Single Sign-on basato su** > **intestazione**.

   > [!TIP]
   > Se è la prima volta che si usa l'accesso Single Sign-On basato su intestazione, è necessario installare PingAccess. Per assicurarsi che la sottoscrizione di Azure venga associata automaticamente all'installazione di PingAccess, usare il collegamento presente nella pagina dell'accesso Single Sign-On per scaricare PingAccess. È possibile aprire il sito di download ora o in un secondo momento.

   ![Mostra la schermata di accesso basata su intestazione e PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Selezionare **Salva**.

Assicurarsi quindi che l'URL di reindirizzamento sia impostato sull'URL esterno:

1. Dalla barra laterale di **Amministrazione Azure Active Directory** selezionare **Azure Active Directory** > **registrazioni app**. Viene visualizzato un elenco di applicazioni.
1. Selezionare l'applicazione.
1. Selezionare il collegamento accanto a **URI di reindirizzamento**, che mostra il numero di URI di reindirizzamento impostati per client Web e pubblici. Viene visualizzata la  **\<pagina Nome applicazione >-Authentication** .
1. Controllare se l'URL esterno assegnato all'applicazione in precedenza si trova nell'elenco **URI di reindirizzamento** . In caso contrario, aggiungere ora l'URL esterno utilizzando un tipo di URI di Reindirizzamento **Web**e selezionare **Salva**.

Infine, configurare l'applicazione locale in modo che gli utenti dispongano dell'accesso in lettura e che altre applicazioni dispongano dell'accesso in lettura/scrittura:

1. Dalla barra **laterale registrazioni app** per l'applicazione, selezionare **autorizzazioni** > API**Aggiungi un'autorizzazione** > **Microsoft API** > **Microsoft Graph**. Viene visualizzata la pagina **autorizzazioni API richiesta** per **Microsoft Graph** , che contiene le API per Windows Azure Active Directory.

   ![Mostra la pagina autorizzazioni API richiesta](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Selezionare **autorizzazioni** > delegate > utente utente **. Read**.
1.  > Selezionare Application Permissions > Application **. ReadWrite. All**.
1. Selezionare **Aggiungi autorizzazioni**.
1. Nella pagina **autorizzazioni API** selezionare **concedi il consenso dell'amministratore per \<il nome della directory >** .

#### <a name="collect-information-for-the-pingaccess-steps"></a>Raccogliere informazioni per la procedura PingAccess

È necessario raccogliere queste tre informazioni (tutti i GUID) per configurare l'applicazione con PingAccess:

| Nome del campo Azure AD | Nome del campo PingAccess | Formato dati |
| --- | --- | --- |
| **ID applicazione (client)** | **ID client** | GUID |
| **ID della directory (tenant)** | **Emittente** | GUID |
| `PingAccess key` | **Segreto client** | Stringa casuale |

Per raccogliere queste informazioni:

1. Dalla barra laterale di **Amministrazione Azure Active Directory** selezionare **Azure Active Directory** > **registrazioni app**. Viene visualizzato un elenco di applicazioni.
1. Selezionare l'applicazione. Viene visualizzata la pagina **registrazioni app** per l'applicazione.

   ![Panoramica della registrazione per un'applicazione](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Accanto al valore **ID applicazione (client)** selezionare l'icona **copia negli Appunti** , quindi copia e Salva. Questo valore viene specificato successivamente come ID client di PingAccess.
1. Successivamente, selezionare **copia negli Appunti**, quindi copiare e salvare il valore di **ID directory (tenant)** . Questo valore viene specificato successivamente come emittente di PingAccess.
1. Dalla barra laterale del **registrazioni app** per l'applicazione, selezionare **certificati e segreti** > **nuovo segreto client**. Viene visualizzata la pagina **Aggiungi un segreto client** .

   ![Mostra la pagina Aggiungi un segreto client](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. In **Descrizione**Digitare `PingAccess key`.
1. In **scadenza**scegliere come impostare la chiave PingAccess: **In 1 anno**, **in 2 anni**o **mai**.
1. Selezionare **Aggiungi**. La chiave PingAccess viene visualizzata nella tabella dei segreti client, con una stringa casuale che riempie automaticamente il campo **valore** .
1. Accanto al campo **valore** della chiave PingAccess selezionare l'icona **copia negli Appunti** , quindi copia e Salva. Questo valore viene specificato successivamente come segreto client di PingAccess.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>Aggiornare GraphAPI per l'invio di campi personalizzati (facoltativo)

Se è necessaria un'attestazione personalizzata che invia altri token all'interno del access_token utilizzato da PingAccess, impostare `acceptMappedClaims` il campo applicazione `True`su. Per apportare questa modifica, è possibile usare Graph Explorer o il manifesto dell'applicazione del portale di Azure AD.

**Questo esempio USA Graph Explorer:**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**Questo esempio usa il [portale di Azure Active Directory](https://aad.portal.azure.com/) per aggiornare `acceptMappedClaims` il campo:**

1. Accedere al portale di [Azure Active Directory](https://aad.portal.azure.com/) come amministratore dell'applicazione.
1. Passare ad **Azure Active Directory** > **Registrazioni per l'app**. Viene visualizzato un elenco di applicazioni.
1. Selezionare l'applicazione.
1. Dalla barra laterale della pagina **registrazioni app** per l'applicazione, selezionare **manifesto**. Viene visualizzato il codice JSON del manifesto per la registrazione dell'applicazione.
1. Cercare il `acceptMappedClaims` campo e modificare il valore in `True`.
1. Selezionare **Salva**.

### <a name="use-of-optional-claims-optional"></a>Uso di attestazioni facoltative (facoltativo)

Attestazioni facoltative consente di aggiungere attestazioni standard, ma non incluse, per impostazione predefinita, per ogni utente e tenant. È possibile configurare attestazioni facoltative per l'applicazione modificando il manifesto dell'applicazione. Per altre informazioni, vedere l' [articolo informazioni sul manifesto dell'applicazione Azure ad](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Esempio per includere l'indirizzo di posta elettronica nel access_token che PingAccess utilizzerà:
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

### <a name="use-of-claims-mapping-policy-optional"></a>Uso dei criteri di mapping delle attestazioni (facoltativo)

[Criteri di mapping delle attestazioni (anteprima)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) per gli attributi che non esistono in AzureAD. Il mapping delle attestazioni consente di eseguire la migrazione di app locali obsolete nel cloud aggiungendo attestazioni personalizzate aggiuntive supportate dagli oggetti ADFS o utente

Per fare in modo che l'applicazione usi un'attestazione personalizzata e includa campi aggiuntivi, assicurarsi [di avere creato anche un criterio personalizzato di mapping delle attestazioni e di assegnarlo all'applicazione](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Per usare un'attestazione personalizzata, è anche necessario avere un criterio personalizzato definito e assegnato all'applicazione. Questo criterio deve includere tutti gli attributi personalizzati necessari.
>
> È possibile eseguire la definizione e l'assegnazione dei criteri tramite PowerShell, Azure AD Graph Explorer o Microsoft Graph. Se li si sta eseguendo in PowerShell, potrebbe essere necessario usare `New-AzureADPolicy` prima di tutto e quindi assegnarlo all'applicazione con. `Add-AzureADServicePrincipalPolicy` Per ulteriori informazioni, vedere [assegnazione dei criteri di mapping](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)delle attestazioni.

Esempio:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Abilitare PingAccess per l'uso di attestazioni personalizzate

L'abilitazione di PingAccess per l'utilizzo di attestazioni personalizzate è facoltativa, ma obbligatoria se si prevede che l'applicazione utilizzi altre attestazioni.

Quando si configura PingAccess nel passaggio seguente, la sessione Web che si creerà (Impostazioni-> sessioni Web di accesso->) deve avere deselezionato il **profilo richiesta** e **aggiornare gli attributi utente** impostati su **No**

## <a name="download-pingaccess-and-configure-your-application"></a>Scaricare PingAccess e configurare l'applicazione

Dopo aver completato la procedura di installazione di Azure Active Directory, è possibile ora passare alla configurazione di PingAccess.

I passaggi dettagliati per la parte PingAccess di questo scenario continuano nella documentazione sull'identità del ping. Seguire le istruzioni in [configurare PingAccess per Azure ad per proteggere le applicazioni pubblicate con Microsoft Azure ad proxy di applicazione](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) nel sito Web di Ping Identity.

Questi passaggi consentono di installare PingAccess e configurare un account PingAccess (se non ne è già presente uno). Quindi, per creare una connessione Azure AD OpenID Connect (OIDC), è necessario configurare un provider di token con il valore di **ID directory (tenant)** copiato dal portale di Azure ad. Successivamente, per creare una sessione Web in PingAccess, è possibile usare l' **ID dell'applicazione (client)** e `PingAccess key` i valori. Successivamente è possibile impostare i mapping delle identità e creare l'host virtuale, il sito e l'applicazione.

### <a name="test-your-application"></a>Testare l'applicazione

Dopo aver completato tutti questi passaggi, l'applicazione deve essere in esecuzione. Per testarlo, aprire un browser e passare all'URL esterno creato al momento della pubblicazione dell'applicazione in Azure. Accedere con l'account di test assegnato all'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare PingAccess per Azure AD per proteggere le applicazioni pubblicate con Microsoft Azure AD proxy di applicazione](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](what-is-single-sign-on.md)
- [Risolvere i problemi e i messaggi di errore del proxy dell'applicazione](application-proxy-troubleshoot.md)
