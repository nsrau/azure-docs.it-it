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
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3fb94629262519f8cfa5da72ee343726aa7d1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367989"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Autenticazione basata su intestazione per l'accesso Single Sign-On con il proxy di applicazione e PingAccess

Il proxy di applicazione di Azure Active Directory (Azure AD) ha collaborato con PingAccess in modo che i clienti di Azure AD possano accedere a più applicazioni. PingAccess espande le [offerte esistenti del proxy di applicazione](application-proxy.md) in modo da includere l'accesso Single Sign-On alle applicazioni che usano intestazioni per l'autenticazione.

## <a name="whats-pingaccess-for-azure-ad"></a>Che cos'è PingAccess per Azure AD?

Con PingAccess per Azure AD, è possibile concedere agli utenti l'accesso e l'accesso Single Sign-On (SSO) alle applicazioni che usano intestazioni per l'autenticazione. Application Proxy tratta queste applicazioni come qualsiasi altra, usando Azure AD per autenticare l'accesso e quindi passando il traffico attraverso il servizio del connettore. PingAccess si trova davanti alle applicazioni e traduce il token di accesso da Azure AD in un'intestazione. L'applicazione riceve quindi l'autenticazione nel formato che può leggere.

Gli utenti non noteranno nulla di diverso quando eseguono l'accesso per usare le applicazioni aziendali. Possono comunque lavorare da qualsiasi luogo e dispositivo. I connettori proxy di applicazione indirizzano il traffico remoto a tutte le app indipendentemente dal tipo di autenticazione, in modo che compiano comunque i carichi automaticamente.

## <a name="how-do-i-get-access"></a>Come si ottiene l'accesso?

Poiché questo scenario deriva da una partnership tra Azure Active Directory e PingAccess, sono necessarie licenze per entrambi i servizi. Le sottoscrizioni Premium di Azure Active Directory, tuttavia, includono una licenza PingAccess di base che copre fino a 20 applicazioni. Se è necessario pubblicare più di 20 applicazioni basate su intestazione, è possibile acquistare una licenza aggiuntiva da PingAccess.

Per altre informazioni, vedere Edizioni di Azure Active Directory .For more information, see [Azure Active Directory editions](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Pubblicare l'applicazione in Azure

Questo articolo è per gli utenti di pubblicare un'applicazione con questo scenario per la prima volta. Oltre a descrivere in dettaglio i passaggi di pubblicazione, viene illustrato come iniziare a utilizzare proxy di applicazione e PingAccess. Se entrambi i servizi sono già stati configurati ma si vuole un aggiornamento nei passaggi di pubblicazione, passare alla sezione [Aggiungere l'applicazione ad Azure AD con proxy di applicazione.](#add-your-application-to-azure-ad-with-application-proxy)

> [!NOTE]
> Poiché questo scenario è il risultato di una partnership fra Azure AD e PingAccess, alcune delle istruzioni sono presenti sul sito di Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Installare un connettore proxy di applicazione

Se il proxy di applicazione è stato abilitato e già installato un connettore, è possibile ignorare questa sezione e passare [a Aggiungere l'applicazione ad Azure AD con il proxy di applicazione](#add-your-application-to-azure-ad-with-application-proxy).

Il connettore proxy di applicazione è un servizio di Windows Server che indirizza il traffico dai dipendenti remoti alle applicazioni pubblicate. Per istruzioni di installazione più dettagliate, vedere [Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory.](application-proxy-add-on-premises-application.md)

1. Accedere al [portale](https://aad.portal.azure.com/) di Azure Active Directory come amministratore dell'applicazione. Viene visualizzata la pagina **dell'interfaccia** di amministrazione di Azure Active Directory.The Azure Active Directory admin center page appears.
1. Selezionare**Servizio connettore**di download**proxy** > applicazione **di Azure Active Directory** > . Viene visualizzata la pagina **Download del connettore proxy dell'applicazione.**

   ![Download del connettore proxy dell'applicazione](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Seguire le istruzioni di installazione.

Il download del connettore dovrebbe abilitare automaticamente il proxy di applicazione per la directory, ma in caso contrario, è possibile selezionare **Abilita proxy di applicazione**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Aggiungere l'applicazione ad Azure AD con il proxy di applicazioneAdd your application to Azure AD with Application Proxy

Nel portale di Azure è necessario eseguire due azioni. Per prima cosa, si pubblica l'applicazione con il proxy di applicazione, Quindi, è necessario raccogliere alcune informazioni sull'applicazione che è possibile utilizzare durante i passaggi di PingAccess.

#### <a name="publish-your-application"></a>Pubblicare l'applicazione

È innanzitutto necessario pubblicare l'applicazione. Questa azione comporta:

- Aggiunta dell'applicazione locale ad Azure ADAdding your on-premises application to Azure AD
- Assegnazione di un utente per il test dell'applicazione e la scelta di SSO basato sull'intestazioneAssigning a user for testing the application and choosing header-based SSO
- Impostazione dell'URL di reindirizzamento dell'applicazione
- Concessione di autorizzazioni per gli utenti e altre applicazioni per l'utilizzo dell'applicazione locale

Per pubblicare la propria applicazione locale:

1. Se non è stato fatto nell'ultima sezione, accedere al portale di [Azure Active Directory](https://aad.portal.azure.com/) come amministratore dell'applicazione.
1. Selezionare **Applicazioni** > aziendali**Nuova applicazione** > **Aggiungere un'applicazione locale**. Viene visualizzata la pagina **Aggiungi applicazione locale.**

   ![Aggiungere un'applicazione locale personalizzataAdd your own on-premises application](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Compilare i campi obbligatori con le informazioni sulla nuova applicazione. Utilizzare le indicazioni riportate di seguito per le impostazioni.

   > [!NOTE]
   > Per una procedura dettagliata più dettagliata di questo passaggio, vedere [Aggiungere un'app locale ad Azure AD.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)

   1. **URL interno**: In genere fornisci l'URL che ti porta alla pagina di accesso dell'app quando sei nella rete aziendale. Per questo scenario, il connettore deve considerare il proxy PingAccess come prima pagina dell'applicazione. Usare il formato seguente: `https://<host name of your PingAccess server>:<port>`. La porta 3000 per impostazione predefinita, ma è possibile configurarla in PingAccess.

      > [!WARNING]
      > Per questo tipo di Single Sign-On, `https` l'URL `http`interno deve utilizzare e non può utilizzare .

   1. **Metodo di pre-autenticazione**: scegliere **Azure Active Directory**.
   1. **Traduci URL nelle intestazioni**: scegliere **No**.

   > [!NOTE]
   > Se si tratta della prima applicazione, usare inizialmente la porta 3000 e aggiornare questa impostazione se viene modificata la configurazione di PingAccess. Per le applicazioni successive, la porta dovrà corrispondere al listener configurato in PingAccess. Altre informazioni sui [listener in PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Selezionare **Aggiungi**. Viene visualizzata la pagina di panoramica della nuova applicazione.

Assegnare ora un utente per il test dell'applicazione e scegliere l'accesso Single Sign-On basato sull'intestazione:Now assign a user for application testing and choose header-based single sign-on:

1. Dalla barra laterale dell'applicazione, seleziona **Utenti e gruppi** > **Aggiungi utenti** > **e gruppi (Numero\<> Selezionato)**. Viene visualizzato un elenco di utenti e gruppi tra cui scegliere.

   ![Mostra l'elenco di utenti e gruppi](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Selezionare un utente per il test dell'applicazione e selezionare **Seleziona**. Assicurarsi che questo account di test abbia accesso all'applicazione locale.
1. Selezionare **Assegna**.
1. Dalla barra laterale dell'applicazione, seleziona **Single Sign-on** > **basato sull'intestazione**.

   > [!TIP]
   > Se è la prima volta che si usa l'accesso Single Sign-On basato su intestazione, è necessario installare PingAccess. Per assicurarsi che la sottoscrizione di Azure venga associata automaticamente all'installazione di PingAccess, usare il collegamento presente nella pagina dell'accesso Single Sign-On per scaricare PingAccess. È possibile aprire il sito di download ora o in un secondo momento.

   ![Mostra lo schermo di accesso basato sull'intestazione e PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Selezionare **Salva**.

Quindi assicurati che l'URL di reindirizzamento sia impostato sull'URL esterno:

1. Nella barra laterale dell'interfaccia di amministrazione di **Azure Active Directory** selezionare**Registrazioni**app **di Azure Active Directory** > . Viene visualizzato un elenco di applicazioni.
1. Selezionare l'applicazione.
1. Selezionare il collegamento accanto a URI di **reindirizzamento**, che mostra il numero di URI di reindirizzamento impostati per i client Web e pubblici. Viene visualizzata la pagina ** \<nome applicazione> - Autenticazione.**
1. Verificare se l'URL esterno assegnato in precedenza all'applicazione è presente nell'elenco URI di **reindirizzamento.** In caso contrario, aggiungere subito l'URL esterno, utilizzando un tipo URI di reindirizzamento **Web**, quindi selezionare **Salva**.

Infine, configurare l'applicazione locale in modo che gli utenti dispongano dell'accesso in lettura e che altre applicazioni dispongano dell'accesso in lettura/scrittura:Finally, set up your on-premises application so that users have read access and other applications have read/write access:

1. Dalla barra **laterale Registrazioni app** per l'applicazione selezionare **Autorizzazioni** > API**Aggiungere un'autorizzazione** > **Microsoft** > API**Microsoft Graph**. Viene visualizzata la pagina **Richiedi autorizzazioni API** per Microsoft **Graph,** che contiene le API per Windows Azure Active Directory.

   ![Pagina Autorizzazioni API richiesta](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Selezionare **Autorizzazioni** > delegate > **Utente.Lettura**.**User**
1. Selezionare **Autorizzazioni** > applicazione**Application** > **Application.ReadWrite.All**.
1. Selezionare **Aggiungi autorizzazioni**.
1. Nella pagina **Autorizzazioni API** selezionare **Concedi \<il consenso amministrativo per il nome **della directory>.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Raccogliere informazioni per la procedura PingAccess

È necessario raccogliere queste tre informazioni (tutti i GUID) per configurare l'applicazione con PingAccess:You need to collect these three pieces of information (all GUIDs) to set up your application with PingAccess:

| Nome del campo di Azure AD | Nome del campo PingAccess | Formato dati |
| --- | --- | --- |
| **ID applicazione (client)** | **Client ID** | GUID |
| **ID della directory (tenant)** | **Emittente** | GUID |
| `PingAccess key` | **Segreto client** | Stringa casuale |

Per raccogliere queste informazioni:

1. Nella barra laterale dell'interfaccia di amministrazione di **Azure Active Directory** selezionare**Registrazioni**app **di Azure Active Directory** > . Viene visualizzato un elenco di applicazioni.
1. Selezionare l'applicazione. Viene visualizzata la pagina **Registrazioni app** per l'applicazione.

   ![Panoramica della registrazione per un'applicazione](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Accanto al valore **ID applicazione (client),** selezionare l'icona **Copia negli Appunti,** quindi copiarla e salvarla. Questo valore viene specificato in un secondo momento come ID client di PingAccess.
1. Successivamente il valore **ID directory (tenant),** selezionare anche **Copia negli Appunti**, quindi copiarlo e salvarlo. Questo valore viene specificato in un secondo momento come emittente di PingAccess.
1. Dalla barra laterale delle **registrazioni dell'app** per l'applicazione, selezionare **Certificati e segreti** > **Nuovo segreto client**. Viene visualizzata la pagina **Aggiungi segreto client.**

   ![Pagina Aggiungi segreto client](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. In **Descrizione** `PingAccess key`digitare .
1. In Scadenza scegliere come **impostare**la chiave PingAccess: **In 1 anno**, In 2 **anni**o **Mai**.
1. Selezionare **Aggiungi**. La chiave PingAccess viene visualizzata nella tabella dei segreti client, con una stringa casuale che compila automaticamente nel campo **VALUE.**
1. Accanto al campo **VALORE** della chiave PingAccess selezionare l'icona **Copia negli Appunti,** quindi copiarla e salvarla. Questo valore viene specificato in un secondo momento come segreto client di PingAccess.

**Aggiornare `acceptMappedClaims` il campo:**

1. Accedere al [portale](https://aad.portal.azure.com/) di Azure Active Directory come amministratore dell'applicazione.
1. Selezionare**Registrazioni app** **di Azure Active Directory** > . Viene visualizzato un elenco di applicazioni.
1. Selezionare l'applicazione.
1. Dalla barra laterale della pagina **Registrazioni app** per l'applicazione, selezionare **Manifesto**. Viene visualizzato il codice JSON del manifesto per la registrazione dell'applicazione.
1. Cercare il `acceptMappedClaims` campo e modificare `True`il valore in .
1. Selezionare **Salva**.

### <a name="use-of-optional-claims-optional"></a>Utilizzo di attestazioni facoltative (facoltativo)Use of optional claims (optional)

Le attestazioni facoltative consentono di aggiungere attestazioni standard ma non incluse per impostazione predefinita di ogni utente e tenant. È possibile configurare attestazioni facoltative per l'applicazione modificando il manifesto dell'applicazione. Per altre informazioni, vedere [l'articolo Informazioni sul manifesto dell'applicazione Azure ADFor](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/) more info, see the Understanding the Azure AD application manifest article

Esempio per includere l'indirizzo di posta elettronica nel access_token che PingAccess utilizzerà:Example to include email address into the access_token that PingAccess will consume:
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

### <a name="use-of-claims-mapping-policy-optional"></a>Utilizzo dei criteri di mapping delle attestazioni (facoltativo)

[Criteri di mapping delle attestazioni (anteprima)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) per gli attributi che non esistono in AzureAD. Il mapping delle attestazioni consente di eseguire la migrazione delle vecchie app locali nel cloud aggiungendo attestazioni personalizzate aggiuntive supportate da ADFS o dagli oggetti utente

Per fare in modo che l'applicazione utilizzi un'attestazione personalizzata e includa campi aggiuntivi, assicurarsi di aver creato anche un criterio di [mapping delle attestazioni personalizzato e di averlo assegnato all'applicazione.](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)

> [!NOTE]
> Per usare un'attestazione personalizzata, è anche necessario avere un criterio personalizzato definito e assegnato all'applicazione. Questo criterio deve includere tutti gli attributi personalizzati necessari.
>
> È possibile eseguire la definizione e l'assegnazione dei criteri tramite PowerShell o Microsoft Graph.You can do policy definition and assignment through PowerShell or Microsoft Graph. Se si esegue in PowerShell, potrebbe essere `New-AzureADPolicy` necessario utilizzarle prima e `Add-AzureADServicePrincipalPolicy`quindi assegnarle all'applicazione con . Per ulteriori informazioni, vedere [Assegnazione di criteri di mapping delle attestazioni](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Esempio:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Abilitare PingAccess per l'utilizzo di attestazioni personalizzateEnable PingAccess to use custom claims

L'abilitazione di PingAccess all'uso di attestazioni personalizzate è facoltativa, ma è necessaria se si prevede che l'applicazione utilizzi attestazioni aggiuntive.

Quando si configurerà PingAccess nel passaggio seguente, la sessione Web che verrà creata (Sessioni Web >Access->) deve avere **L'opzione Richiedi profilo** deselezionata e Aggiorna attributi **utente** impostata su **No**

## <a name="download-pingaccess-and-configure-your-application"></a>Scaricare PingAccess e configurare l'applicazione

Dopo aver completato la procedura di installazione di Azure Active Directory, è possibile ora passare alla configurazione di PingAccess.

I passaggi dettagliati per la parte PingAccess di questo scenario continuano nella documentazione relativa all'identità del ping. Seguire le istruzioni in [Configurare PingAccess per Azure AD per proteggere le applicazioni pubblicate usando](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) il proxy di applicazione di Microsoft Azure AD nel sito Web Identità ping.

Questi passaggi consentono di installare PingAccess e configurare un account PingAccess (se non ne è già installato uno). Quindi, per creare una connessione OpenID Connect (OIDC) di Azure AD, impostare un provider di token con il valore **ID directory (tenant)** copiato dal portale di Azure AD. Successivamente, per creare una sessione Web su PingAccess, utilizzare `PingAccess key` l'ID **dell'applicazione (client)** e i valori. Successivamente è possibile impostare i mapping delle identità e creare l'host virtuale, il sito e l'applicazione.

### <a name="test-your-application"></a>Testare l'applicazione

Dopo aver completato tutti questi passaggi, l'applicazione deve essere in esecuzione. To test it, open a browser and navigate to the external URL that you created when you published the application in Azure. Accedere con l'account di test assegnato all'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare PingAccess per Azure AD per proteggere le applicazioni pubblicate con il proxy di applicazione di Microsoft Azure ADConfigure PingAccess for Azure AD to protect applications published using Microsoft Azure AD Application Proxy](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](what-is-single-sign-on.md)
- [Risolvere i problemi e i messaggi di errore del proxy dell'applicazione](application-proxy-troubleshoot.md)
