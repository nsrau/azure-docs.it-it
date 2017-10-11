---
title: Autenticazione basata su intestazione con PingAccess per il proxy dell'applicazione di Azure AD | Microsoft Docs
description: Pubblicare le applicazioni con PingAccess e il proxy dell'applicazione per supportare l'autenticazione basata su intestazione.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 58034ab8830cf655199875b448948ea14dc04a70
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Autenticazione basata su intestazione per l'accesso Single Sign-On con il proxy di applicazione e PingAccess

Il proxy dell'applicazione Azure Active Directory e PingAccess hanno collaborato per fornire ai clienti di Azure Active Directory l'accesso a un numero ancora maggiore di applicazioni. PingAccess espande le [offerte esistenti del proxy di applicazione](active-directory-application-proxy-get-started.md) in modo da includere l'accesso Single Sign-On alle applicazioni che usano intestazioni per l'autenticazione.

## <a name="what-is-pingaccess-for-azure-ad"></a>Che cos'è PingAccess per Azure AD?

PingAccess per Azure Active Directory è un'offerta di PingAccess che consente di mettere a disposizione degli utenti l'accesso e l'accesso Single Sign-On alle applicazioni che usano intestazioni per l'autenticazione. Il proxy dell'applicazione tratta queste app come qualsiasi altra, usando Azure AD per autenticare l'accesso e quindi passando il traffico attraverso il servizio del connettore. PingAccess sta davanti alle app e converte il token di accesso da Azure AD in un'intestazione, in modo che l'applicazione riceva l'autenticazione nel formato che è in grado di leggere.

Gli utenti non noteranno nulla di diverso quando eseguono l'accesso per usare le app aziendali. Possono comunque lavorare da qualsiasi luogo e dispositivo. 

Poiché i connettori del proxy dell'applicazione indirizzano il traffico remoto a tutte le app indipendentemente dal loro tipo di autenticazione, continueranno a bilanciare il carico automaticamente.

## <a name="how-do-i-get-access"></a>Come si ottiene l'accesso?

Poiché questo scenario è il risultato di una partnership fra Azure Active Directory e PingAccess, sono necessarie le licenze per entrambi i servizi. Le sottoscrizioni Premium di Azure Active Directory, tuttavia, includono una licenza PingAccess di base che copre fino a 20 applicazioni. Se è necessario pubblicare più di 20 applicazioni basate su intestazione, è possibile acquistare una licenza aggiuntiva da PingAccess. 

Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

## <a name="publish-your-application-in-azure"></a>Pubblicare l'applicazione in Azure

Questo articolo è destinato a chi pubblica un'app con questo scenario per la prima volta. Illustra come iniziare a usare sia l'applicazione sia PingAccess, oltre ai passaggi di pubblicazione. Se sono già stati configurati entrambi i servizi ma si vuole rivedere i passaggi di pubblicazione, è possibile ignorare l'installazione del connettore e passare a [Aggiungere l'app in Azure AD con il proxy dell'applicazione](#add-your-app-to-Azure-AD-with-Application-Proxy).

>[!NOTE]
>Poiché questo scenario è il risultato di una partnership fra Azure AD e PingAccess, alcune delle istruzioni sono presenti sul sito di Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Installare un connettore proxy di applicazione

Se il proxy di applicazione è già attivato e si ha già un connettore installato, è possibile ignorare questa sezione e passare a [Aggiungere l'app in Azure AD con il proxy dell'applicazione](#add-your-app-to-azure-ad-with-application-proxy).

Il connettore del proxy di applicazione è un servizio di Windows Server che indirizza il traffico dai dipendenti remoti alle app pubblicate. Per istruzioni di installazione più dettagliate, vedere [Abilitare il proxy di applicazione nel portale di Azure](active-directory-application-proxy-enable.md).

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.
2. Selezionare **Azure Active Directory** > **Proxy dell'applicazione**.
3. Selezionare **Download Connector** (Scarica il connettore) per avviare il download del connettore del proxy di applicazione. Seguire le istruzioni di installazione.

   ![Abilitare il proxy dell'applicazione e scaricare il connettore](./media/application-proxy-ping-access/install-connector.png)

4. Scaricando il connettore si attiverà automaticamente il proxy di applicazione per la directory, ma se così non fosse, è possibile selezionare **Abilita proxy di applicazione**.


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Aggiungere l'app ad Azure AD con il proxy dell'applicazione

Nel portale di Azure è necessario eseguire due azioni. Per prima cosa, si pubblica l'applicazione con il proxy di applicazione, quindi si raccolgono alcune informazioni sull'app che è possibile usare durante la procedura di PingAccess.

Seguire questi passaggi per pubblicare l'app. Per una descrizione più dettagliata dei passaggi 1-8, vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-publish-azure-portal.md).

1. Se non lo si è fatto nell'ultima sezione, accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.
2. Selezionare **Azure Active Directory** > **Applicazioni aziendali**.
3. Selezionare **Aggiungi** nella parte superiore del pannello.
4. Selezionare **Applicazione locale**.
5. Compilare i campi obbligatori con le informazioni della nuova app. Usare le seguenti linee guida per le impostazioni:
   - **URL interno**: normalmente si indica l'URL che porta alla pagina di accesso dell'app quando ci si trova nella rete aziendale. Per questo scenario il connettore deve trattare il proxy PingAccess come prima pagina dell'app. Usare il formato seguente: `https://<host name of your PA server>:<port>`. La porta 3000 per impostazione predefinita, ma è possibile configurarla in PingAccess.
   - **Metodo di autenticazione preliminare**: Azure Active Directory
   - **Tradurre URL nelle intestazioni**: No

   >[!NOTE]
   >Se si tratta della prima applicazione, usare inizialmente la porta 3000 e aggiornare questa impostazione se viene modificata la configurazione di PingAccess. Se si tratta almeno di una seconda app, è necessaria una corrispondenza con il listener configurato in PingAccess. Altre informazioni sui [listener in PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).

6. Selezionare **Aggiungi** nella parte inferiore del pannello. L'applicazione viene aggiunta e si apre il menu di avvio rapido.
7. Nel menu di avvio rapido selezionare **Assegna utente per il test** e aggiungere almeno un utente all'applicazione. Assicurarsi che questo account di test abbia accesso all'applicazione locale.
8. Selezionare **Assegna** per salvare l'assegnazione dell'utente di test.
9. Nel pannello di gestione dell'app selezionare **Single Sign-On**.
10. Scegliere **Header-based sign-on** (Accesso basato su intestazione) dal menu a discesa. Selezionare **Salva**.

   >[!TIP]
   >Se è la prima volta che si usa l'accesso Single Sign-On basato su intestazione, è necessario installare PingAccess. Per assicurarsi che la sottoscrizione di Azure venga associata automaticamente all'installazione di PingAccess, usare il collegamento presente nella pagina dell'accesso Single Sign-On per scaricare PingAccess. È possibile aprire il sito di download ora o in un secondo momento. 

   ![Selezionare l'accesso basato su intestazione](./media/application-proxy-ping-access/sso-header.PNG)

11. Chiudere il pannello Applicazioni aziendali o scorrere completamente a sinistra per tornare al menu di Azure Active Directory.
12. Selezionare **Registrazioni per l'app**.

   ![Selezionare Registrazioni per l'app](./media/application-proxy-ping-access/app-registrations.png)

13. Selezionare l'app appena aggiunta e quindi **URL di risposta**.

   ![Selezionare URL di risposta](./media/application-proxy-ping-access/reply-urls.png)

14. Verificare se l'URL esterno assegnato all'app al passaggio 5 è incluso nell'elenco URL di risposta. In caso contrario aggiungerlo ora.
15. Nel pannello delle impostazioni dell'app selezionare **Autorizzazioni necessarie**.

  ![Selezionare Autorizzazioni necessarie](./media/application-proxy-ping-access/required-permissions.png)

16. Selezionare **Aggiungi**. Per l'API scegliere **Windows Azure Active Directory** e quindi **Seleziona**. Per le autorizzazioni scegliere **Read and write all applications** (Leggi e scrivi in tutte le applicazioni) e **Accedi e leggi il profilo di un altro utente** e quindi **Seleziona** e **Fine**.  

  ![Autorizzazioni SELECT](./media/application-proxy-ping-access/select-permissions.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>Raccogliere informazioni per la procedura PingAccess

1. Nel pannello delle impostazioni dell'app selezionare **Proprietà**. 

  ![Selezionare Proprietà](./media/application-proxy-ping-access/properties.png)

2. Annotare il valore **ID applicazione**. Sarà usato come ID client per la configurazione di PingAccess.
3. Nel pannello delle impostazioni dell'app selezionare **Chiavi**.

  ![Selezionare Chiavi](./media/application-proxy-ping-access/Keys.png)

4. Creare una chiave immettendo una descrizione della chiave e scegliendo una data di scadenza dal menu a discesa.
5. Selezionare **Salva**. Viene visualizzato un GUID nel campo **Valore**.

  Annotare il valore adesso, in quanto non sarà più visibile dopo aver chiuso questa finestra.

  ![Creare una nuova chiave](./media/application-proxy-ping-access/create-keys.png)

6. Chiudere il pannello Registrazioni per l'app o scorrere completamente a sinistra per tornare al menu di Azure Active Directory.
7. Selezionare **Proprietà**.
8. Salvare il GUID **ID directory**.

### <a name="optional---update-graphapi-to-send-custom-fields"></a>Facoltativo: aggiornare GraphAPI per inviare campi personalizzati

Per un elenco dei token di sicurezza che Azure AD invia per l'autenticazione, vedere [Riferimento al token di Azure AD](./develop/active-directory-token-and-claims.md). Se è necessaria un'attestazione personalizzata che invia altri token, usare GraphAPI per impostare il campo app *acceptMappedClaims* su **True**. Per eseguire questa configurazione è possibile usare Azure AD Graph Explorer o Microsoft Graph. 

In questo esempio viene usato Graph Explorer:

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```

## <a name="download-pingaccess-and-configure-your-app"></a>Scaricare PingAccess e configurare l'app

Dopo aver completato la procedura di installazione di Azure Active Directory, è possibile ora passare alla configurazione di PingAccess. 

I passaggi dettagliati per la parte PingAccess di questo scenario continuano nella documentazione di Ping Identity, [Configurare PingAccess per Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html).

Questi passaggi consentono di ottenere un account PingAccess se non se ne possiede già uno, installare PingAccess Server e creare una connessione al Provider OIDC di Azure AD con l'ID directory copiato dal portale di Azure. È quindi possibile usare i valori dell'ID applicazione e della chiave per creare una sessione Web in PingAccess. Successivamente è possibile impostare i mapping delle identità e creare l'host virtuale, il sito e l'applicazione.

### <a name="test-your-app"></a>Test dell'app

Dopo aver completato tutti questi passaggi, l'app dovrebbe funzionare. Per verificarlo aprire un browser e passare all'URL esterno creato quando è stata pubblicata l'applicazione in Azure. Accedere con l'account di test assegnato all'app.

## <a name="next-steps"></a>Passaggi successivi

- [Configure PingAccess for Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) (Configurare PingAccess per Azure AD)
- [Come viene offerto l'accesso Single Sign-On dal proxy di applicazione di Azure AD?](application-proxy-sso-overview.md)
- [Risolvere i problemi del Proxy applicazione](active-directory-application-proxy-troubleshoot.md)
