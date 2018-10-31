---
title: 'Esercitazione: Integrazione di Azure Active Directory con Amazon Web Service (AWS) | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Amazon Web Services.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.openlocfilehash: 8e91fbf0befaef9088e9afaa6e69c0cb29ad4858
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363760"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Esercitazione: Integrazione di Azure Active Directory con Amazon Web Service (AWS)

Questa esercitazione descrive come integrare Amazon Web Services (AWS) con Azure Active Directory (Azure AD).

L'integrazione di Amazon Web Service (AWS) con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Amazon Web Services (AWS).
- È possibile abilitare gli utenti per l'accesso automatico ad Amazon Web Services (AWS) (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

È possibile configurare più identificatori per più istanze, come descritto di seguito. 

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Con questi valori Azure AD rimuove il valore di **#** e invia il valore corretto `https://signin.aws.amazon.com/saml` come URL destinatario nel token SAML.

**È consigliabile usare questo approccio per i motivi seguenti:**

a. Ogni applicazione fornisce il certificato univoco X509, quindi ogni istanza può avere una data di scadenza dei certificati diversa ed è possibile gestire questo scenario in base al singolo account AWS. In questo caso il rollover generale dei certificati sarà semplice.

b. È possibile abilitare il provisioning degli utenti con l'app AWS in Azure AD e il servizio recupererà tutti i ruoli dall'account AWS. Non è necessario aggiungere o aggiornare manualmente i ruoli AWS nell'app.

c. È possibile assegnare individualmente il proprietario dell'app che può gestire l'app direttamente in Azure AD.

> [!Note]
> Assicurarsi di usare solo app della raccolta

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Amazon Web Service (AWS), sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione ad Amazon Web Services (AWS) abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

> [!Note]
> Per integrare più account AWS in un singolo account Azure per Single Sign-on, vedere [questo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) articolo.

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Amazon Web Service (AWS) dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Aggiunta di Amazon Web Service (AWS) dalla raccolta
Per configurare l'integrazione di Amazon Web Service (AWS) in Azure AD, è necessario aggiungere Amazon Web Service (AWS) dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Amazon Web Service (AWS) dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/amazon-web-service-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/amazon-web-service-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/amazon-web-service-tutorial/a_new_app.png)

4. Nella casella di ricerca digitare **Amazon Web Services (AWS)** selezionare **Amazon Web Services (AWS)** nel riquadro dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Amazon Web Services (AWS) usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Amazon Web Service (AWS) che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l’utente correlato in Amazon Web Service (AWS).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Amazon Web Service (AWS), è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test in Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user)**: per avere una controparte di Britta Simon in Amazon Web Services (AWS) collegata alla rappresentazione in Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Amazon Web Services (AWS).

**Per configurare l'accesso Single Sign-On di Azure AD con Amazon Web Service (AWS), seguire la procedura seguente:**

1. Nella pagina di integrazione dell'applicazione **Amazon Web Services (AWS)** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![image](./media/amazon-web-service-tutorial/B1_B2_Select_SSO.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML** per abilitare il Single Sign-On.

    ![image](./media/amazon-web-service-tutorial/b1_b2_saml_sso.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/amazon-web-service-tutorial/b1-domains_and_urlsedit.png)

4. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. Quando si configura più di un'istanza, specificare un valore dell'identificatore. A partire dalla seconda istanza, specificare il valore dell'identificatore nel formato seguente. Usare un simbolo **#** per specificare un valore SPN univoco. 

    `https://signin.aws.amazon.com/saml#2`

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_identifier.png)

6. L'applicazione Amazon Web Services (AWS) si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente e attestazioni** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente e attestazioni**.

    ![image](./media/amazon-web-service-tutorial/i4-attribute.png)

7. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente e attestazioni** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:
    
    | NOME  | Source Attribute  | Spazio dei nomi |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Ruolo            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "inserire un valore compreso tra 900 secondi (15 minuti) e 43200 secondi (12 ore)" |  https://aws.amazon.com/SAML/Attributes |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./media/amazon-web-service-tutorial/i2-attribute.png)

    ![image](./media/amazon-web-service-tutorial/i3-attribute.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Immettere il valore **Nome spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Save**.

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML dei metadati federazione** e salvarlo nel computer in uso.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

9. In un'altra finestra del Web browser accedere al sito aziendale di Amazon Web Service (AWS) come amministratore.

10. Fare clic su **AWS** nella home page della console.

    ![Configurare la home page dell'accesso Single Sign-On][11]

11. Fare clic su **Identity and Access Management**.

    ![Configurare l'identità dell'accesso Single Sign-On][12]

12. Fare clic su **Provider di identità** e quindi fare clic su **Create Provider** (Crea provider).

    ![Configurare il provider dell'accesso Single Sign-On][13]

13. Nella pagina **Configure Provider** seguire questa procedura:

    ![Configurare la finestra di dialogo dell'accesso Single Sign-On][14]

    a. In **Tipo provider** selezionare **SAML**.

    b. Nella casella di testo **Nome provider** digitare un nome di provider, ad esempio *WAAD*.

    c. Per caricare il **file di metadati** scaricato dal portale di Azure, fare clic su **Scegli file**.

    d. Fare clic su **Next Step**.

14. Nella pagina della finestra di dialogo **Verify Provider Information** (Verifica informazioni provider) fare clic su **Crea**.

    ![Configurare la verifica dell'accesso Single Sign-On][15]

15. Fare clic su **Roles** (Ruoli) e quindi su **Create role** (Crea ruolo).

    ![Configurare i ruoli dell'accesso Single Sign-On][16]

16. Nella pagina **Create role** (Crea ruolo) seguire questa procedura:  

    ![Configurare una relazione di trust dell'accesso Single Sign-On][19]

    a. Selezionare **SAML 2.0 federation** (Federazione SAML 2.0) in **Select type of trusted entity** (Selezionare un tipo di entità attendibile).

    b. Nella sezione **Choose a SAML 2.0 Provider** (Scegliere un provider SAML 2.0) selezionare il **provider SAML** creato in precedenza, ad esempio *WAAD*.

    c. Selezionare **Allow programmatic and AWS Management Console access** (Consenti accesso a livello di codice e tramite AWS Management Console).
  
    d. Fare clic su **Next: Permissions** (Avanti: Autorizzazioni).

17. Nella finestra di dialogo **Attach Permissions Policies** (Collega i criteri di autorizzazione) non è necessario associare alcun criterio. Fare clic su **Next: Review** (Avanti: Riepilogo).  

    ![Configurare criteri dell'accesso Single Sign-On][33]

18. Nella finestra di dialogo **Review** seguire questa procedura:

    ![Riepilogo della configurazione dell'accesso Single Sign-On][34]

    a. Nella casella di testo **Role name** (Nome ruolo) immettere il nome del ruolo.

    b. Nella casella di testo **Role description** (Descrizione ruolo) immettere la descrizione.

    c. Fare clic su **Crea ruolo**.

    d. Creare tutti i ruoli necessari in base alle esigenze ed eseguirne il mapping per il Provider di identità.

19. Usare le credenziali dell'account del servizio AWS per recuperare i ruoli dall'account AWS nel provisioning utenti di Azure AD. A tale scopo, aprire la pagina home della console AWS.

20. Fare clic su **Servizi**  -> **Security, Identity & Compliance** (Sicurezza, identità e conformità)  -> **IAM**.

    ![Recupero dei ruoli dall'account AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

21. Selezionare la scheda **Criteri** nella sezione IAM.

    ![Recupero dei ruoli dall'account AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

22. Creare un nuovo criterio facendo clic su **Create policy** (Crea criterio) per recuperare i ruoli dell'account AWS nel servizio di provisioning utenti di Azure AD.

    ![Creazione di un nuovo criterio](./media/amazon-web-service-tutorial/fetchingrole3.png)

23. Creare un criterio personalizzato per recuperare tutti i ruoli dagli account AWS tramite i passaggi seguenti:

    ![Creazione di un nuovo criterio](./media/amazon-web-service-tutorial/policy1.png)

    a. Nella sezione **"Crea criterio"**, fare clic sulla scheda **"JSON"**.

    b. Nel documento del criterio aggiungere il codice JSON seguente.

    ```

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

24. Definire il **nuovo criterio** seguendo questa procedura:

    ![Definire il nuovo criterio](./media/amazon-web-service-tutorial/policy2.png)

    a. In **Nome criteri** immettere **AzureAD_SSOUserRole_Policy**.

    b. In **Descrizione** è possibile digitare **Questo criterio consente di recuperare i ruoli dagli account AWS**.

    c. Fare clic sul pulsante **"Crea criterio"**.

25. Creare un nuovo account utente nel servizio AWS IAM seguendo questa procedura:

    a. Fare clic sulla voce **Utenti** nella console AWS IAM.

    ![Definire il nuovo criterio](./media/amazon-web-service-tutorial/policy3.png)

    b. Fare clic sul pulsante **Aggiungi utente** per creare un nuovo utente.

    ![Add user](./media/amazon-web-service-tutorial/policy4.png)

    c. Nella sezione **Aggiungi utente** seguire questa procedura:

    ![Add user](./media/amazon-web-service-tutorial/adduser1.png)

    * In Nome utente immettere **AzureADRoleManager**.

    * Per Tipo di accesso selezionare l'opzione **Programmatic access** (Accesso a livello di programmazione). In questo modo l'utente può chiamare le API e recuperare i ruoli dall'account AWS.

    * Fare clic sul pulsante **Next Permissions** (Autorizzazioni successive) nell'angolo inferiore destro.

26. Ora creare un nuovo criterio per l'utente seguendo questa procedura:

    ![Add user](./media/amazon-web-service-tutorial/adduser2.png)

    a. Fare clic sul pulsante **Attach existing policies directly** (Collega direttamente i criteri esistenti).

    b. Cercare il nuovo criterio creato nella sezione filtro, **AzureAD_SSOUserRole_Policy**.

    c. Selezionare il **criterio** e quindi fare clic sul pulsante **Next: Review** (Avanti: Riepilogo).

27. Controllare il criterio per l'utente collegato seguendo questa procedura:

    ![Add user](./media/amazon-web-service-tutorial/adduser3.png)

    a. Controllare il nome utente, il tipo di accesso e il criterio associati all'utente.

    b. Fare clic sul pulsante **Crea utente** nell'angolo inferiore destro per creare l'utente.

28. Scaricare le credenziali dell'utente seguendo questa procedura:

    ![Add user](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copiare l'**ID chiave di accesso** e la **chiave di accesso segreta** dell'utente.

    b. Immettere queste credenziali nella sezione del provisioning utenti di Azure AD per recuperare i ruoli dalla console AWS.

    c. Fare clic sul pulsante **Chiudi** in basso.

29. Passare alla sezione **Provisioning utente** dell'app Amazon Web Services nel portale di gestione di Azure AD.

    ![Add user](./media/amazon-web-service-tutorial/provisioning.png)

30. Immettere la **chiave di accesso** e il **segreto** rispettivamente nei campi **Segreto client** e **Token segreto**.

    ![Add user](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Immettere la chiave di accesso utente AWS nel campo **Segreto client**.

    b. Immettere il segreto utente AWS nel campo **Token segreto**.

    c. Fare clic sul pulsante **Prova connessione**. La prova della connessione dovrebbe riuscire.

    d. Salvare l'impostazione facendo clic sul pulsante **Salva** nella parte superiore.

31. Assicurarsi che Stato provisioning sia impostato su **Attivato** nella sezione Impostazioni e fare clic sul pulsante **Salva** nella parte superiore.

    ![Add user](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Azure Active Directory**, selezionare **Utenti** e quindi selezionare **Tutti gli utenti**.

    ![image](./media/amazon-web-service-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/amazon-web-service-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/amazon-web-service-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Creare un utente di test di Amazon Web Services (AWS)

Questa sezione descrive come creare un utente chiamato Britta Simon in Amazon Web Service (AWS). Per Amazon Web Services (AWS) non è necessario creare un utente nel sistema per ottenere l'accesso SSO, pertanto non è necessario eseguire nessuna di queste operazioni.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso ad Amazon Web Services (AWS).

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/amazon-web-service-tutorial/d_all_applications.png)

2. Nell'elenco delle applicazioni selezionare **Amazon Web Service (AWS)**.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/amazon-web-service-tutorial/d_leftpaneusers.png)

4. Selezionare il pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/amazon-web-service-tutorial/d_assign_user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_users.png)

6. Nella finestra di dialogo **Seleziona ruolo** scegliere il ruolo utente appropriato dall'elenco e fare clic sul pulsante **Seleziona** in basso nella schermata.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_roles.png)

    >[!NOTE]
    >Dopo aver abilitato il provisioning degli utenti con l'applicazione, sarà necessario attendere 30 minuti prima di ricevere tutti i ruoli da Amazon Web Services (AWS). Sarà quindi necessario aggiornare la pagina. Quando si assegnerà l'applicazione agli utenti e ai gruppi, verranno visualizzati i ruoli per l'utente.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_assign.png)
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Amazon Web Service (AWS) nel riquadro di accesso, si dovrebbe accedere automaticamente all'applicazione Amazon Web Service (AWS).
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

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
