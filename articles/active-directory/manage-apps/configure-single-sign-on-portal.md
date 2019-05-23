---
title: Configurare l'accesso Single Sign-On - Azure Active Directory | Microsoft Docs
description: Questa esercitazione usa il portale di Azure per configurare l'accesso Single Sign-On basato su SAML per un'applicazione con Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa18bc637ec31a1f83b5cab090e008715c5e0c2a
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825022"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>Esercitazione: Configurare l'accesso Single Sign-On basato su SAML per un'applicazione con Azure Active Directory

Questa esercitazione usa il [portale di Azure](https://portal.azure.com) per configurare l'accesso Single Sign-On basato su SAML per un'applicazione con Azure Active Directory (Azure AD). Usare questa esercitazione se non è disponibile un’[esercitazione specifica per l’applicazione](../saas-apps/tutorial-list.md). 

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

1. Se l'applicazione non è stata aggiunta al tenant di Azure AD, vedere [Avvio rapido: Aggiungere un'applicazione al tenant di Azure AD](add-application-portal.md).

2. Richiedere le informazioni descritte in [Configurare le opzioni SAML di base](#configure-basic-saml-options) al fornitore dell'applicazione.

3. Per testare i passaggi di questa esercitazione, usare un ambiente non di produzione. Se non è disponibile un ambiente non di produzione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

4. Accedere al [portale di Azure](https://portal.azure.com) come amministratore dell’applicazione cloud o amministratore dell’applicazione per il proprio tenant di Azure AD.

## <a name="select-a-single-sign-on-mode"></a>Selezionare una modalità Single Sign-On

Dopo aver aggiunto un'applicazione al tenant di Azure AD, è possibile configurare l'accesso Single Sign-On all'applicazione.

Per aprire le impostazioni dell'accesso Single Sign-On:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento sinistro. 

2. In **Gestisci** nel pannello di spostamento di **Azure Active Directory** visualizzato selezionare **Applicazioni aziendali**. Viene visualizzato un esempio casuale delle applicazioni del tenant di Azure AD. 

3. Nel menu **Tipo di applicazione** selezionare **Tutte le applicazioni** e quindi **Applica**.

4. Immettere il nome dell'applicazione per cui si vuole configurare l'accesso Single Sign-On. Ad esempio, è possibile immettere **GitHub-test** per configurare l'applicazione aggiunta durante l'avvio rapido [aggiungere un'applicazione](add-application-portal.md).  

     ![Screenshot che mostra la barra di ricerca dell'applicazione.](media/configure-single-sign-on-portal/azure-portal-application-search.png)

5. Scegliere l'applicazione per cui si vuole configurare l'accesso Single Sign-on.

6. Nella sezione **Gestisci** selezionare **Single Sign-On**. 

7. Selezionare **SAML** per configurare l'accesso Single Sign-On. Viene visualizzata la pagina **Configura l'accesso Single Sign-On con SAML - Anteprima**.

## <a name="configure-basic-saml-options"></a>Configurare le opzioni SAML di base

Per configurare il dominio e gli URL:

1. Contattare il fornitore dell'applicazione per ottenere le informazioni corrette per le impostazioni seguenti.

    | Impostazione di configurazione | SSO avviato da provider di servizi | SSO avviato da IdP | DESCRIZIONE |
    |:--|:--|:--|:--|
    | Identificatore (ID entità) | Obbligatoria per alcune app | Obbligatoria per alcune app | Identifica in modo univoco l'applicazione per cui viene configurato l'accesso Single Sign-On. Azure AD restituisce l'identificatore all'applicazione come parametro Audience del token SAML. L'applicazione dovrebbe convalidarlo. Questo valore viene inoltre visualizzato come ID entità in tutti i metadati SAML forniti dall'applicazione.|
    | URL di risposta | Facoltativo | Obbligatoria | Specifica dove l'applicazione prevede di ricevere il token SAML. L'URL di risposta è denominato anche URL del servizio consumer di asserzione. |
    | URL di accesso | Obbligatoria | Non specificare | Quando un utente apre questo URL, il provider di servizi esegue il reindirizzamento ad Azure AD per l'autenticazione e l'accesso dell'utente. Azure AD usa l'URL per avviare l'applicazione da Office 365 o dal pannello di accesso di Azure AD. Se si omette, Azure AD si basa sul provider di identità per avviare l'accesso Single Sign-On quando un utente avvia l'applicazione.|
    | Stato dell'inoltro | Facoltativo | Facoltativo | Comunica all'applicazione dove reindirizzare l'utente al termine dell'autenticazione. In genere il valore è un URL valido per l'applicazione. Tuttavia, alcune applicazioni usano questo campo in modo diverso. Per altre informazioni, rivolgersi al fornitore dell'applicazione.
    | URL di chiusura sessione | Facoltativo | Facoltativo | Usato per restituire una risposta di disconnessione SAML all'applicazione.


2. Per modificare le opzioni di configurazione SAML di base, selezionare l'icona **Modifica** (a forma di matita) nell'angolo in alto a destra della sezione **Configurazione SAML di base**.

     ![Configurare i certificati](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

3. Nei campi appropriati della pagina immettere le informazioni fornite dal fornitore dell'applicazione nel passaggio 1.

4. Nella parte superiore della pagina selezionare **Salva**.

## <a name="configure-user-attributes-and-claims"></a>Configurare attributi e attestazioni utente 

È possibile controllare le informazioni inviate da Azure AD all'applicazione nel token SAML quando un utente effettua l'accesso. Per controllare queste informazioni, configurare gli attributi utente. Ad esempio, è possibile configurare Azure AD per inviare all'applicazione il nome utente, l'indirizzo di posta elettronica e l'ID dipendente quando un utente effettua l'accesso. 

Per il funzionamento corretto dell'accesso Single Sign-On, questi attributi possono essere obbligatori o facoltativi. Per altre informazioni, vedere l'[esercitazione specifica dell'applicazione](../saas-apps/tutorial-list.md) oppure rivolgersi al fornitore dell'applicazione.

1. Per modificare gli attributi e le attestazioni utente, selezionare l'icona **Modifica** (a forma di matita) nell'angolo in alto a destra della sezione **Attributi e attestazioni utente** .

   Il **Valore identificatore nome** è impostato con il valore predefinito *user.principalname*. L'ID utente identifica in modo univoco ogni utente all'interno dell'applicazione. Se l'indirizzo di posta elettronica è sia il nome utente che l'identificatore univoco, ad esempio, impostare il valore su *user.mail*.

2. Per modificare il **Valore identificatore nome**, selezionare l'icona **Modifica** (a forma di matita) per il campo **Valore identificatore nome** campo. Apportare le modifiche appropriate al formato e all'origine dell'identificatore in base alle esigenze. Al termine, salvare le modifiche. Per altre informazioni su come personalizzare le attestazioni, vedere l'articolo [Personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](../develop/active-directory-saml-claims-customization.md).

3. Per aggiungere un'attestazione, selezionare **Aggiungi nuova attestazione** nella parte superiore della pagina. Immettere il **Nome** e selezionare l'origine appropriata. Se si seleziona l'origine **Attributo**, è necessario scegliere l'**Attributo di origine** da usare. Se si seleziona l'origine **Traduzione**, è necessario scegliere la **Trasformazione** e il **Parametro 1** da usare.

4. Selezionare **Salva**. Nella tabella viene visualizzata la nuova attestazione.
 
## <a name="generate-a-saml-signing-certificate"></a>Generare un certificato di firma SAML

Azure AD usa un certificato per firmare i token SAML che invia all'applicazione. 

1. Per generare un nuovo certificato, selezionare l'icona **Modifica** (a forma di matita) nell'angolo in alto a destra della sezione **Certificato di firma SAML**.

2. Nella sezione **Certificato di firma SAML** selezionare **Nuovo certificato**.

3. Nella riga del nuovo certificato visualizzata impostare la **Data di scadenza**. Per altre informazioni sulle opzioni di configurazione disponibili, vedere l'articolo [Opzioni avanzate di firma del certificato](certificate-signing-options.md).

4. Selezionare **Salva** nella parte superiore della sezione **Certificato di firma SAML**. 

## <a name="assign-users-to-the-application"></a>Assegnare utenti all'applicazione

Prima di implementare l'applicazione nell'organizzazione, è consigliabile testare l'accesso Single Sign-On con diversi utenti o gruppi.

> [!NOTE]
>
> Questi passaggi portano alla sezione di configurazione **Utenti e gruppi** del portale. Al termine, è necessario tornare alla sezione **Single sign-on** per completare l'esercitazione.

Per assegnare un utente o un gruppo all'applicazione:

1. Aprire l'applicazione nel portale, se non è già aperta.
2. Nel riquadro di spostamento sinistro dell'applicazione selezionare **Utenti e gruppi**.
3. Selezionare **Aggiungi utente**.
4. Nella sezione **Aggiungi assegnazione** selezionare **Utenti e gruppi**.
5. Per trovare un utente specifico, digitare il nome utente nella casella **Selezionare un membro o invitare un utente esterno**. Selezionare quindi la foto o il logo del profilo dell'utente e quindi scegliere **Seleziona**. 
6. Nella sezione **Aggiungi assegnazione** selezionare **Assegna**. Al termine, gli utenti selezionati verranno visualizzati nell'elenco **Utenti e gruppi**.

## <a name="set-up-the-application-to-use-azure-ad"></a>Configurare l'applicazione per l'uso di Azure AD

La procedura è quasi terminata.  Come passaggio finale, è necessario configurare l'applicazione per l'uso di Azure AD come provider di identità SAML. 

1. Scorrere verso il basso fino alla sezione **Configurare <applicationName>**. Per questa esercitazione, tale sezione viene chiamata **Configurare GitHub-test**. 
2. Copiare il valore di ogni riga di questa sezione. Incollare quindi ogni valore nella riga appropriata della sezione **Configurazione SAML di base**. Ad esempio, copiare il valore **URL di accesso** dalla sezione **Configurare GitHub-test** e incollarlo nel campo **URL di accesso** della sezione **Configurazione SAML di base** e così via.
3. Dopo avere incollato tutti i valori nei campi appropriati, selezionare **Salva**.

## <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

È ora possibile testare le impostazioni.  

1. Aprire le impostazioni dell'accesso Single Sign-On per l'applicazione. 
2. Scorrere fino alla sezione **Verificare l'accesso Single Sign-On con <applicationName>**. Per questa esercitazione, tale sezione viene chiamata **Configurare GitHub-test**.
3. Selezionare **Test**. Verranno visualizzate le opzioni di test.
4. Selezionare **Accedi con l'account utente corrente**. Questo test consente di verificare prima di tutto il funzionamento dell'accesso Single Sign-On per l'amministratore.

In caso di errore, verrà visualizzato un messaggio di errore. Completare questi passaggi:

1. Copiare e incollare le specifiche nella casella **Qual è l'errore riscontrato?**.

    ![Ottieni procedure per la risoluzione](media/configure-single-sign-on-portal/error-guidance.png)

2. Selezionare **Ottieni procedure per la risoluzione**. Verranno visualizzate indicazioni relative alla causa radice e alla risoluzione.  In questo esempio, l'utente non è stato assegnato all'applicazione.

3. Leggere le procedure per la risoluzione e quindi, se possibile, risolvere il problema.

4. Eseguire di nuovo il test finché non viene completato correttamente.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato configurato l’accesso Single Sign-On per un'applicazione. Al termine della configurazione, si è assegnato un utente all'applicazione e si è configurata l'applicazione per l'uso dell'accesso Single Sign-On basato su SAML. Dopo aver completato tutti questi passaggi, è stato verificato il funzionamento corretto dell'accesso SAML.

Sono state eseguite queste operazioni:
> [!div class="checklist"]
> * Selezione di SAML come modalità Single Sign-On
> * Configurazione del dominio e degli URL contattando il fornitore dell'applicazione
> * Configurazione degli attributi utente
> * Creazione di un certificato di firma SAML
> * Assegnazione manuale di utenti o gruppi all'applicazione
> * Configurazione dell'applicazione per l'uso di Azure AD come provider di identità SAML
> * Test dell'accesso Single Sign-On basato su SAML

Per implementare l'applicazione per altri utenti dell'organizzazione, usare il provisioning utenti automatico.

> [!div class="nextstepaction"]
> [Informazioni su come assegnare utenti con il provisioning automatico](configure-automatic-user-provisioning-portal.md)


