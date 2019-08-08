---
title: 'Esercitazione: Integrazione di Azure Active Directory con Amazon Web Services (AWS) | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Amazon Web Services.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6640708905abc266b07b7b66f5da09aeb890f01
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823883"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Esercitazione: Integrare Amazon Web Services (AWS) con Azure Active Directory

Questa esercitazione descrive come integrare Amazon Web Services (AWS) con Azure Active Directory (Azure AD). Integrando Amazon Web Services (AWS) con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad Amazon Web Services (AWS).
* Abilitare gli utenti per l'accesso automatico ad Amazon Web Services (AWS) con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

È possibile configurare più identificatori per più istanze, come descritto di seguito.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Con questi valori Azure AD rimuove il valore di **#** e invia il valore corretto `https://signin.aws.amazon.com/saml` come URL destinatario nel token SAML.

**È consigliabile usare questo approccio per i motivi seguenti:**

a. Ogni applicazione fornirà un certificato X509 univoco. Ogni istanza dell'app AWS può quindi avere una data di scadenza del certificato diversa che può essere gestita sulla base di un singolo account AWS. In questo caso il rollover generale dei certificati sarà più semplice.

b. È possibile abilitare il provisioning degli utenti con l'app AWS in Azure AD e il servizio recupererà tutti i ruoli dall'account AWS. Non è necessario aggiungere o aggiornare manualmente i ruoli AWS nell'app.

c. È possibile assegnare individualmente il proprietario dell'app che può gestire l'app direttamente in Azure AD.

> [!Note]
> Assicurarsi di usare solo app della raccolta

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Amazon Web Services (AWS) abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Amazon Web Services (AWS) supporta l'accesso SSO avviato da **SP e IDP**.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Aggiunta di Amazon Web Service (AWS) dalla raccolta

Per configurare l'integrazione di Amazon Web Service (AWS) in Azure AD, è necessario aggiungere Amazon Web Service (AWS) dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Amazon Web Services (AWS)** nella casella di ricerca.
1. Selezionare **Amazon Web Service (AWS)** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso SSO di Azure AD con Amazon Web Services (AWS) usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Amazon Web Services (AWS).

Per configurare e testare l'accesso SSO di Azure AD con Amazon Web Service (AWS), completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
2. **[Configurare Amazon Web Services (AWS)](#configure-amazon-web-services-aws)** per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** per avere una controparte di B. Simon in Amazon Web Services (AWS) collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Amazon Web Services (AWS)** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata e gli URL necessari sono già prepopolati con Azure. L'utente deve salvare la configurazione facendo clic sul pulsante **Salva**.

5. Quando si configura più di un'istanza, specificare un valore dell'identificatore. A partire dalla seconda istanza, specificare il valore dell'identificatore nel formato seguente. Usare un simbolo **#** per specificare un valore SPN univoco.

    `https://signin.aws.amazon.com/saml#2`

6. L'applicazione Amazon Web Services (AWS) prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su  **Modifica** per aprire la finestra di dialogo Attributi utente.

    ![image](common/edit-attribute.png)

7. Oltre a quelli elencati in precedenza, l'applicazione Amazon Web Services (AWS) prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:

    | Nome  | Attributo di origine  | Spazio dei nomi |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Ruolo            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "inserire un valore compreso tra 900 secondi (15 minuti) e 43200 secondi (12 ore)" |  https://aws.amazon.com/SAML/Attributes |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nella casella di testo **Spazio dei nomi** digitare il valore dello spazio dei nomi indicato per la riga.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Salva**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **XML metadati federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Amazon Web Services (AWS)** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-amazon-web-services-aws"></a>Configurare Amazon Web Services (AWS)

1. In un'altra finestra del Web browser accedere al sito aziendale di Amazon Web Service (AWS) come amministratore.

2. Fare clic su **AWS** nella home page della console.

    ![Configurare la home page dell'accesso Single Sign-On][11]

3. Fare clic su **Identity and Access Management**.

    ![Configurare l'identità dell'accesso Single Sign-On][12]

4. Fare clic su **Provider di identità** e quindi fare clic su **Create Provider** (Crea provider).

    ![Configurare il provider dell'accesso Single Sign-On][13]

5. Nella pagina **Configure Provider** seguire questa procedura:

    ![Configurare la finestra di dialogo dell'accesso Single Sign-On][14]

    a. In **Tipo provider** selezionare **SAML**.

    b. Nella casella di testo **Nome provider** digitare un nome di provider, ad esempio *WAAD*.

    c. Per caricare il **file di metadati** scaricato dal portale di Azure, fare clic su **Scegli file**.

    d. Fare clic su **Next Step**.

6. Nella pagina della finestra di dialogo **Verify Provider Information** (Verifica informazioni provider) fare clic su **Crea**.

    ![Configurare la verifica dell'accesso Single Sign-On][15]

7. Fare clic su **Roles** (Ruoli) e quindi su **Create role** (Crea ruolo).

    ![Configurare i ruoli dell'accesso Single Sign-On][16]

8. Nella pagina **Create role** (Crea ruolo) seguire questa procedura:  

    ![Configurare una relazione di trust dell'accesso Single Sign-On][19]

    a. Selezionare **SAML 2.0 federation** (Federazione SAML 2.0) in **Select type of trusted entity** (Selezionare un tipo di entità attendibile).

    b. Nella sezione **Choose a SAML 2.0 Provider** (Scegliere un provider SAML 2.0) selezionare il **provider SAML** creato in precedenza, ad esempio *WAAD*.

    c. Selezionare **Allow programmatic and AWS Management Console access** (Consenti accesso a livello di codice e tramite AWS Management Console).
  
    d. Fare clic su **Avanti: Permissions** (Avanti: Autorizzazioni).

9. Nella finestra di dialogo **Attach Permissions Policies** (Collega i criteri di autorizzazione) collegare i criteri appropriati in base all'organizzazione. Fare clic su **Avanti: Review** (Avanti: Rivedi).  

    ![Configurare criteri dell'accesso Single Sign-On][33]

10. Nella finestra di dialogo **Review** seguire questa procedura:

    ![Riepilogo della configurazione dell'accesso Single Sign-On][34]

    a. Nella casella di testo **Role name** (Nome ruolo) immettere il nome del ruolo.

    b. Nella casella di testo **Role description** (Descrizione ruolo) immettere la descrizione.

    c. Fare clic su **Crea ruolo**.

    d. Creare tutti i ruoli necessari in base alle esigenze ed eseguirne il mapping per il Provider di identità.

11. Usare le credenziali dell'account del servizio AWS per recuperare i ruoli dall'account AWS nel provisioning utenti di Azure AD. A tale scopo, aprire la pagina home della console AWS.

12. Fare clic su **Servizi**  -> **Security, Identity & Compliance** (Sicurezza, identità e conformità)  -> **IAM**.

    ![Recupero dei ruoli dall'account AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Selezionare la scheda **Criteri** nella sezione IAM.

    ![Recupero dei ruoli dall'account AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Creare un nuovo criterio facendo clic su **Create policy** (Crea criterio) per recuperare i ruoli dell'account AWS nel servizio di provisioning utenti di Azure AD.

    ![Creazione di un nuovo criterio](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Creare un criterio personalizzato per recuperare tutti i ruoli dagli account AWS tramite i passaggi seguenti:

    ![Creazione di un nuovo criterio](./media/amazon-web-service-tutorial/policy1.png)

    a. Nella sezione **"Crea criterio"** , fare clic sulla scheda **"JSON"** .

    b. Nel documento del criterio aggiungere il codice JSON seguente.

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Fare clic sul pulsante **Verifica criteri** per convalidare il criterio.

    ![Definire il nuovo criterio](./media/amazon-web-service-tutorial/policy5.png)

16. Definire il **nuovo criterio** seguendo questa procedura:

    ![Definire il nuovo criterio](./media/amazon-web-service-tutorial/policy2.png)

    a. In **Nome criteri** immettere **AzureAD_SSOUserRole_Policy**.

    b. In **Descrizione** è possibile digitare **Questo criterio consente di recuperare i ruoli dagli account AWS**.

    c. Fare clic sul pulsante **"Crea criterio"** .

17. Creare un nuovo account utente nel servizio AWS IAM seguendo questa procedura:

    a. Fare clic sulla voce **Utenti** nella console AWS IAM.

    ![Definire il nuovo criterio](./media/amazon-web-service-tutorial/policy3.png)

    b. Fare clic sul pulsante **Aggiungi utente** per creare un nuovo utente.

    ![Add user](./media/amazon-web-service-tutorial/policy4.png)

    c. Nella sezione **Aggiungi utente** seguire questa procedura:

    ![Add user](./media/amazon-web-service-tutorial/adduser1.png)

    * In Nome utente immettere **AzureADRoleManager**.

    * Per Tipo di accesso selezionare l'opzione **Programmatic access** (Accesso a livello di programmazione). In questo modo l'utente può chiamare le API e recuperare i ruoli dall'account AWS.

    * Fare clic sul pulsante **Next Permissions** (Autorizzazioni successive) nell'angolo inferiore destro.

18. Ora creare un nuovo criterio per l'utente seguendo questa procedura:

    ![Add user](./media/amazon-web-service-tutorial/adduser2.png)

    a. Fare clic sul pulsante **Attach existing policies directly** (Collega direttamente i criteri esistenti).

    b. Cercare il nuovo criterio creato nella sezione filtro, **AzureAD_SSOUserRole_Policy**.

    c. Selezionare il **criterio** e quindi fare clic sul pulsante **Next: Review** (Avanti: Riepilogo).

19. Controllare il criterio per l'utente collegato seguendo questa procedura:

    ![Add user](./media/amazon-web-service-tutorial/adduser3.png)

    a. Controllare il nome utente, il tipo di accesso e il criterio associati all'utente.

    b. Fare clic sul pulsante **Crea utente** nell'angolo inferiore destro per creare l'utente.

20. Scaricare le credenziali dell'utente seguendo questa procedura:

    ![Add user](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copiare l'**ID chiave di accesso** e la **chiave di accesso segreta** dell'utente.

    b. Immettere queste credenziali nella sezione del provisioning utenti di Azure AD per recuperare i ruoli dalla console AWS.

    c. Fare clic sul pulsante **Chiudi** in basso.

21. Passare alla sezione **Provisioning utente** dell'app Amazon Web Services nel portale di gestione di Azure AD.

    ![Add user](./media/amazon-web-service-tutorial/provisioning.png)

22. Immettere la **chiave di accesso** e il **segreto** rispettivamente nei campi **Segreto client** e **Token segreto**.

    ![Add user](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Immettere la chiave di accesso utente AWS nel campo **Segreto client**.

    b. Immettere il segreto utente AWS nel campo **Token segreto**.

    c. Fare clic sul pulsante **Prova connessione**. La prova della connessione dovrebbe riuscire.

    d. Salvare l'impostazione facendo clic sul pulsante **Salva** nella parte superiore.

23. Assicurarsi che Stato provisioning sia impostato su **Attivato** nella sezione Impostazioni e fare clic sul pulsante **Salva** nella parte superiore.

    ![Add user](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Amazon Web Services (AWS).

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Amazon Web Service (AWS)** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-amazon-web-services-aws-test-user"></a>Creare un utente di test di Amazon Web Services (AWS)

Questa sezione descrive come creare un utente chiamato B. Simon in Amazon Web Service (AWS). Per Amazon Web Services (AWS) non è necessario creare un utente nel sistema per ottenere l'accesso SSO, pertanto non è necessario eseguire nessuna di queste operazioni.

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Amazon Web Services (AWS) nel Pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Amazon Web Services (AWS) per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Problemi noti

 * Nella sottosezione **Mapping** della sezione **Provisioning** verrà visualizzato il messaggio "Caricamento..." e i mapping degli attributi non verranno mai visualizzati. L'unico flusso di lavoro di provisioning supportato oggi è l'importazione di ruoli da AWS in Azure AD per la selezione durante l'assegnazione di utenti/gruppi. I mapping di attributi per questo flusso di lavoro sono predeterminati e non configurabili.
 
 * Nella sezione **Provisioning** è possibile immettere un solo set di credenziali per un unico tenant AWS alla volta. Tutti i ruoli importati vengono scritti nella proprietà appRoles dell'[oggetto servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) di Azure AD per il tenant AWS. È possibile aggiungere più tenant AWS (rappresentati da oggetti servicePrincipal) ad Azure AD dalla raccolta per il provisioning, ma è presente un problema noto che impedisce la scrittura automatica di tutti i ruoli importati da più oggetti servicePrincipal AWS usati per il provisioning nell'unico oggetto servicePrincipal usato per l'accesso Single Sign-On. Per ovviare al problema, è possibile usare l'[API Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) per estrarre tutti gli oggetti appRole importati in ogni oggetto servicePrincipal AWS in cui è configurato il provisioning. Queste stringhe di ruolo possono essere aggiunte successivamente all'oggetto servicePrincipal AWS in cui è configurato l'accesso Single Sign-On.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
