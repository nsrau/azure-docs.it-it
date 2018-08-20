---
title: Configurare l'accesso Single Sign-On - Azure Active Directory | Microsoft Docs
description: Questa esercitazione usa il portale di Azure per configurare l'accesso Single Sign-On basato su SAML per un'applicazione con Azure Active Directory (Azure AD).
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 08/09/2018
ms.author: barbkess
ms.reviewer: arvinh,luleon
ms.openlocfilehash: b0180f162996c5fc4647071feaf02d42320b7c9a
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036381"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>Esercitazione: Configurare l'accesso Single Sign-On basato su SAML per un'applicazione con Azure Active Directory

Questa esercitazione usa il [portale di Azure](https://portal.azure.com) per configurare l'accesso Single Sign-On basato su SAML per un'applicazione con Azure Active Directory (Azure AD). Usare questa esercitazione per configurare le applicazioni per cui non è disponibile un'[esercitazione specifica dell'applicazione](../saas-apps/tutorial-list.md). 


Questa esercitazione usa il portale di Azure per eseguire queste operazioni:

> [!div class="checklist"]
> * Selezionare la modalità Single Sign-On basata su SAML
> * Configurare il dominio e gli URL specifici dell'applicazione
> * Configurare gli attributi utente
> * Creare un certificato di firma SAML
> * Assegnare utenti all'applicazione
> * Configurare l'applicazione per l'accesso Single Sign-On basato su SAML
> * Testare le impostazioni SAML

## <a name="before-you-begin"></a>Prima di iniziare

1. Se l'applicazione non è stata aggiunta al tenant di Azure AD, vedere [Guida introduttiva: Aggiungere un'applicazione al tenant di Azure Active Directory](add-application-portal.md).

2. Richiedere le informazioni descritte in [Configurare il dominio e gli URL](#configure-domain-and-urls) al fornitore dell'applicazione.

3. Per testare i passaggi di questa esercitazione è consigliabile usare un ambiente non di produzione. Se non è disponibile un ambiente non di produzione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

4. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale per il tenant di Azure AD, amministratore applicazione cloud o amministratore applicazione.

## <a name="select-a-single-sign-on-mode"></a>Selezionare una modalità Single Sign-On

Dopo che un'applicazione è stata aggiunta al tenant di Azure AD, è possibile configurare l'accesso Single Sign-On per l'applicazione.

Per aprire le impostazioni dell'accesso Single Sign-On:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel pannello di spostamento a sinistra. 

2. Nel pannello **Azure Active Directory** fare clic su **Applicazioni aziendali**. Verrà visualizzato il pannello **Tutte le applicazioni**, contenente un campione casuale delle applicazioni nel tenant di Azure AD. 

3. Nel menu **Tipo di applicazione** selezionare **Tutte le applicazioni** e fare clic su **Applica**.

4. Immettere il nome dell'applicazione per cui si vuole configurare l'accesso Single Sign-On. Scegliere l'applicazione oppure usare l'applicazione GitHub-test che è stata aggiunta nella guida introduttiva su come [aggiungere un'applicazione](add-application-portal.md).

5. Fare clic su **Single Sign-On**. L'opzione predefinita visualizzata in **Modalità Single Sign-On** è **Accesso basato su SAML**. 

    ![Opzioni di configurazione](media/configure-single-sign-on-portal/config-options.png)

6. Fare clic su **Salva** nella parte superiore del pannello. 

## <a name="configure-domain-and-urls"></a>Configurare il dominio e gli URL

Per configurare il dominio e gli URL:

1. Contattare il fornitore dell'applicazione per ottenere le informazioni corrette per le impostazioni seguenti.

    | Impostazione di configurazione | SSO avviato da provider di servizi | SSO avviato da IdP | DESCRIZIONE |
    |:--|:--|:--|:--|
    | URL di accesso | Obbligatoria | Non specificare | Quando un utente apre questo URL, il provider di servizi esegue il reindirizzamento ad Azure AD per l'autenticazione e l'accesso dell'utente. Azure AD usa l'URL per avviare l'applicazione da Office 365 e dal pannello di accesso di Azure AD. Se questo campo viene lasciato vuoto e un utente avvia l'applicazione da Office 365, dal pannello di accesso di Azure AD o dall'URL Single Sign-On di Azure AD, Azure AD esegue l'accesso Single Sign-On avviato da IdP.|
    | Identificatore (ID entità) | Obbligatoria per alcune app | Obbligatoria per alcune app | Identifica in modo univoco l'applicazione per cui viene configurato l'accesso Single Sign-On. Azure AD restituisce l'identificatore all'applicazione come parametro Audience del token SAML in modo che l'applicazione lo convalidi. Questo valore viene inoltre visualizzato come ID entità in tutti i metadati SAML forniti dall'applicazione.|
    | URL di risposta | Facoltativo | Obbligatoria | Specifica dove l'applicazione prevede di ricevere il token SAML. L'URL di risposta è denominato anche URL del servizio consumer di asserzione. |
    | Stato dell'inoltro | Facoltativo | Facoltativo | Comunica all'applicazione dove reindirizzare l'utente al termine dell'autenticazione. Questo valore è in genere un URL valido per l'applicazione, ma alcune applicazioni usano questo campo in modo diverso. Per altre informazioni, rivolgersi al fornitore dell'applicazione.

2. Immettere le informazioni. Per visualizzare tutte le impostazioni, fare clic su **Mostra impostazioni URL avanzate**.

    ![Opzioni di configurazione](media/configure-single-sign-on-portal/config-urls.png)

3. Nella parte superiore del pannello fare clic su **Salva**.

4. In questa sezione è presente un pulsante **Test impostazioni SAML**. Eseguire questo test più avanti nell'esercitazione, nella sezione [Testare l'accesso Single Sign-On](#test-single-sign-on).

## <a name="configure-user-attributes"></a>Configurare gli attributi utente

Gli attributi utente consentono di controllare le informazioni inviate da Azure AD all'applicazione. Azure AD potrebbe inviare all'applicazione, ad esempio, il nome, l'indirizzo di posta elettronica e l'ID dipendente dell'utente. Azure AD invia gli attributi utente all'applicazione nel token SAML a ogni accesso dell'utente. 

Per il funzionamento corretto dell'accesso Single Sign-On, questi attributi possono essere obbligatori o facoltativi. Per altre informazioni, vedere l'[esercitazione specifica dell'applicazione](../saas-apps/tutorial-list.md) oppure rivolgersi al fornitore dell'applicazione.

1. Per visualizzare tutte le opzioni, fare clic su **Visualizza e modifica tutti gli altri attributi utente**.

    ![Configurare gli attributi utente](media/configure-single-sign-on-portal/config-user-attributes.png)

2. Immettere un valore in **Identificatore utente**.

    L'ID utente identifica in modo univoco ogni utente all'interno dell'applicazione. Se l'indirizzo di posta elettronica è sia il nome utente che l'identificatore univoco, ad esempio, impostare il valore su *user.mail*.

3. Per gli altri attributi del token SAML, fare clic su **Visualizza e modifica tutti gli altri attributi utente**.

4. Per aggiungere un attributo ad **Attributi token SAML**, fare clic su **Aggiungi attributo**. Immettere il **nome** e selezionare il **valore** nel menu.

5. Fare clic su **Save**. Il nuovo attributo verrà visualizzato nella tabella.
 
## <a name="create-a-saml-signing-certificate"></a>Creare un certificato di firma SAML

Azure AD usa un certificato per firmare i token SAML che invia all'applicazione. 

1. Per visualizzare tutte le opzioni, fare clic su **Mostra impostazioni avanzate per la firma di certificati**.

    ![Configurare i certificati](media/configure-single-sign-on-portal/config-certificate.png)

2. Per configurare un certificato, fare clic su **Crea nuovo certificato**.

3. Nel pannello **Crea nuovo certificato** impostare la data di scadenza e fare clic su **Salva**.

4. Fare clic su **Imposta il nuovo certificato come attivo**.

5. Per altre informazioni, vedere [Opzioni avanzate di firma del certificato ](certificate-signing-options.md).

6. Per mantenere le modifiche finora apportate, assicurarsi di fare clic su **Salva** nella parte superiore del pannello **Single Sign-On**. 

## <a name="assign-users-to-the-application"></a>Assegnare utenti all'applicazione

Prima di implementare l'applicazione nell'organizzazione, è consigliabile testare l'accesso Single Sign-On con diversi utenti o gruppi.

Per assegnare un utente o un gruppo all'applicazione:

1. Aprire l'applicazione nel portale, se non è già aperta.
2. Nel pannello di sinistra dell'applicazione fare clic su **Utenti e gruppi**.
3. Fare clic su **Add User**.
4. Nel pannello **Aggiungi assegnazione** fare clic su **Utenti e gruppi**.
5. Per trovare un utente specifico, digitare il nome utente nella casella **Seleziona** e quindi fare clic sulla casella di controllo accanto alla foto o al logo del profilo dell'utente e infine su **Seleziona**. 
6. Trovare il nome utente corrente e selezionarlo. Facoltativamente, è possibile selezionare altri utenti.
7. Nel pannello **Aggiungi assegnazione** fare clic su **Assegna**. Al termine, gli utenti selezionati verranno visualizzati nell'elenco **Utenti e gruppi**.

## <a name="configure-the-application-to-use-azure-ad"></a>Configurare l'applicazione per l'uso di Azure AD

La procedura è quasi terminata.  Come passaggio finale, è necessario configurare l'applicazione per l'uso di Azure AD come provider di identità SAML. 

1. Scorrere verso il basso fino alla fine del pannello **Single Sign-On** per l'applicazione. 

    ![Configurare l'applicazione](media/configure-single-sign-on-portal/configure-app.png)

2. Fare clic su **Configura applicazione** nel portale e seguire le istruzioni.
3. Creare manualmente account utente nell'applicazione allo scopo di testare l'accesso Single Sign-On. Creare gli account utente assegnati all'applicazione nella [sezione precedente](#assign-users-to-the-application).   Quando si è pronti a implementare l'applicazione nell'organizzazione, è consigliabile usare il provisioning utenti automatico per creare automaticamente gli account utente nell'applicazione.

## <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

È ora possibile testare le impostazioni.  

1. Aprire le impostazioni dell'accesso Single Sign-On per l'applicazione. 
2. Scorrere fino alla sezione in cui è possibile **configurare il dominio e gli URL**.
2. Fare clic su **Test impostazioni SAML**. Verranno visualizzate le opzioni di test.

    ![Opzioni di test per l'accesso Single Sign-On](media/configure-single-sign-on-portal/test-single-sign-on.png) 

3. Fare clic su **Accedi con l'account utente corrente**. È così possibile verificare prima di tutto il funzionamento dell'accesso Single Sign-On per l'amministratore.
4. In caso di errore, verrà visualizzato un messaggio di errore. Copiare e incollare le specifiche nella casella **Qual è l'errore riscontrato?**.

    ![Ottieni procedure per la risoluzione](media/configure-single-sign-on-portal/error-guidance.png)

5. Fare clic su **Ottieni procedure per la risoluzione**. Verranno visualizzate indicazioni relative alla causa radice e alla risoluzione.  In questo esempio, l'utente non è stato assegnato all'applicazione.

    ![Correggere l'errore](media/configure-single-sign-on-portal/fix-error.png)

6. Leggere le procedure per la risoluzione e quindi, se appropriato, fare clic su **Correggi**.

7. Eseguire di nuovo il test finché non viene completato correttamente.



## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato usato il portale di Azure per configurare un'applicazione per l'accesso Single Sign-On con Azure AD. È stata trovata la pagina di configurazione dell'accesso Single Sign-On e sono state configurate le relative impostazioni. Al termine della configurazione, si è assegnato un utente all'applicazione e si è configurata l'applicazione per l'uso dell'accesso Single Sign-On basato su SAML. Dopo aver completato tutti questi passaggi, è stato verificato il funzionamento corretto dell'accesso SAML.

Sono state eseguite queste operazioni:
> [!div class="checklist"]
> * Selezione di SAML come modalità Single Sign-On
> * Configurazione del dominio e degli URL contattando il fornitore dell'applicazione
> * Configurazione degli attributi utente
> * Creazione di un certificato di firma SAML
> * Assegnazione manuale di utenti o gruppi all'applicazione
> * Configurazione dell'applicazione per l'accesso Single Sign-On
> * Test dell'accesso Single Sign-On basato su SAML

Per implementare l'applicazione per altri utenti dell'organizzazione, è consigliabile usare il provisioning automatico.

> [!div class="nextstepaction"]
>[Informazioni su come assegnare utenti con il provisioning automatico](configure-automatic-user-provisioning-portal.md)


