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
ms.date: 1/16/2017
ms.author: jeedes
ms.openlocfilehash: 8d77215fd2923e22a9cc87e469cb135d035d22d9
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Esercitazione: Integrazione di Azure Active Directory con Amazon Web Service (AWS)

Questa esercitazione descrive come integrare Amazon Web Services (AWS) con Azure Active Directory (Azure AD).

L'integrazione di Amazon Web Service (AWS) con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Amazon Web Services (AWS).
- È possibile abilitare gli utenti per l'accesso automatico ad Amazon Web Services (AWS) (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Amazon Web Service (AWS), sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione ad Amazon Web Services (AWS) abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Amazon Web Service (AWS) dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Aggiunta di Amazon Web Service (AWS) dalla raccolta
Per configurare l'integrazione di Amazon Web Service (AWS) in Azure AD, è necessario aggiungere Amazon Web Service (AWS) dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Amazon Web Service (AWS) dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Amazon Web Services (AWS)**selezionare **Amazon Web Services (AWS)** nel riquadro dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Amazon Web Services (AWS) nell'elenco dei risultati](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Amazon Web Services (AWS) usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Amazon Web Service (AWS) che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l’utente correlato in Amazon Web Service (AWS).

In Amazon Web Services (AWS) assegnare il valore del **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Amazon Web Service (AWS), è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test in Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user)**: per avere una controparte di Britta Simon in Amazon Web Services (AWS) collegata alla rappresentazione in Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Amazon Web Services (AWS).

**Per configurare l'accesso Single Sign-On di Azure AD con Amazon Web Service (AWS), seguire la procedura seguente:**

1. Nella pagina di integrazione dell'applicazione **Amazon Web Services (AWS)** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Nella sezione **URL e dominio Amazon Web Services (AWS)** l'utente non deve eseguire alcuna operazione perché l'applicazione è già preintegrata in Azure.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. L'applicazione Amazon Web Services (AWS) si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

    ![Configurare l'attributo Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)   

5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:
    
    | Nome attributo  | Valore attributo | Spazio dei nomi |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://AWS.Amazon.com/SAML/Attributes |
    | Ruolo            | user.assignedroles |  https://AWS.Amazon.com/SAML/Attributes |
    
    >[!TIP]
    >È necessario configurare il provisioning dell'utente in Azure AD per recuperare tutti i ruoli dalla Console AWS. Vedere la procedura di provisioning seguente.

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configurare l'aggiunta dell'accesso Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Configurare l'aggiunta dell'attributo Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Nella casella di testo **Spazio dei nomi**, digitare il valore dello spazio dei nomi indicato per la riga.
    
    d. Fare clic su **OK**.

6. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. In un'altra finestra del Web browser accedere al sito aziendale di Amazon Web Service (AWS) come amministratore.

9. Fare clic su **Console Home**.
   
    ![Configurare la home page dell'accesso Single Sign-On][11]

10. Fare clic su **Identity and Access Management**. 
   
    ![Configurare l'identità dell'accesso Single Sign-On][12]

11. Fare clic su **Provider di identità** e quindi fare clic su **Create Provider** (Crea provider). 
   
    ![Configurare il provider dell'accesso Single Sign-On][13]

12. Nella pagina **Configure Provider** seguire questa procedura: 
   
    ![Configurare la finestra di dialogo dell'accesso Single Sign-On][14]
 
    a. In **Tipo provider** selezionare **SAML**.

    b. Nella casella di testo **Nome provider** digitare un nome di provider, ad esempio *WAAD*.

    c. Per caricare il **file di metadati** scaricato dal portale di Azure, fare clic su **Scegli file**.

    d. Fare clic su **Next Step**.

13. Nella pagina della finestra di dialogo **Verify Provider Information** (Verifica informazioni provider) fare clic su **Crea**. 
    
    ![Configurare la verifica dell'accesso Single Sign-On][15]

14. Fare clic su **Ruoli** e quindi fare clic su **Crea nuovo ruolo**. 
    
    ![Configurare i ruoli dell'accesso Single Sign-On][16]

15. Nella finestra di dialogo **Set Role Name** seguire questa procedura: 
    
    ![Configurare il nome dell'accesso Single Sign-On][17] 

    a. Nella casella di testo **Nome ruolo** digitare un nome di ruolo, ad esempio *TestUser*. 

    b. Fare clic su **Next Step**.

16. Nella finestra di dialogo **Select Role Type** seguire questa procedura: 
    
    ![Configurare il tipo di ruolo dell'accesso Single Sign-On][18] 

    a. Selezionare **Role For Identity Provider Access**. 

    b. Nella sezione **Grant Web Single Sign-On (WebSSO) access to SAML providers** (Concedi accesso Web Single Sign-On (WebSSO) a provider SAML) fare clic su **Seleziona**.

17. Nella finestra di dialogo **Establish Trust** seguire questa procedura:  
    
    ![Configurare una relazione di trust dell'accesso Single Sign-On][19] 

    a. Come provider SAML selezionare quello creato in precedenza, ad esempio *WAAD* 
  
    b. Fare clic su **Next Step**.

18. Nella finestra di dialogo **Verify Role Trust** (Verifica attendibilità ruolo) fare clic su **Passaggio successivo**. 
    
    ![Configurare una relazione di trust per un ruolo dell'accesso Single Sign-On][32]

19. Nella finestra di dialogo **Attach Policy** (Allega criteri) fare clic su **Passaggio successivo**.  
    
    ![Configurare criteri dell'accesso Single Sign-On][33]

20. Nella finestra di dialogo **Review** seguire questa procedura:   
    
    ![Riepilogo della configurazione dell'accesso Single Sign-On][34] 

    a. Fare clic su **Crea ruolo**.

    b. Creare tutti i ruoli necessari in base alle esigenze ed eseguirne il mapping per il Provider di identità.

21. Usare le credenziali dell'account del servizio AWS per recuperare i ruoli dall'account AWS nel provisioning utenti di Azure AD. A tale scopo, aprire la pagina home della console AWS.

22. Fare clic su **Servizi**  -> **Security, Identity & Compliance** (Sicurezza, identità e conformità)  -> **IAM**.

    ![Recupero dei ruoli dall'account AWS](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. Selezionare la scheda **Criteri** nella sezione IAM.

    ![Recupero dei ruoli dall'account AWS](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. Creare un nuovo criterio facendo clic su **Crea criteri**.

    ![Creazione di un nuovo criterio](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. Creare un criterio personalizzato per recuperare tutti i ruoli dagli account AWS tramite i passaggi seguenti:

    ![Creazione di un nuovo criterio](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. Nella sezione **"Crea criterio"**, fare clic sulla scheda **"JSON"**.

    b. Nel documento del criterio aggiungere il codice JSON seguente.
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam: ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```

    c. Fare clic sul pulsante **Verifica criteri** per convalidare il criterio.

    ![Definire il nuovo criterio](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)

26. Definire il **nuovo criterio** seguendo questa procedura:

    ![Definire il nuovo criterio](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. In **Nome criteri** immettere **AzureAD_SSOUserRole_Policy**.

    b. In **Descrizione** è possibile digitare **Questo criterio consente di recuperare i ruoli dagli account AWS**.
    
    c. Fare clic sul pulsante **"Crea criterio"**.
        
27. Creare un nuovo account utente nel servizio AWS IAM seguendo questa procedura:

    a. Fare clic sulla voce **Utenti** nella console AWS IAM.

    ![Definire il nuovo criterio](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b. Fare clic sul pulsante **Aggiungi utente** per creare un nuovo utente.

    ![Add user](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. Nella sezione **Aggiungi utente** seguire questa procedura:
    
    ![Add user](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * In Nome utente immettere **AzureADRoleManager**.
    
    * Per Tipo di accesso selezionare l'opzione **Programmatic access** (Accesso a livello di programmazione). In questo modo l'utente può chiamare le API e recuperare i ruoli dall'account AWS.
    
    * Fare clic sul pulsante **Next Permissions** (Autorizzazioni successive) nell'angolo inferiore destro.

28. Ora creare un nuovo criterio per l'utente seguendo questa procedura:

    ![Add user](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. Fare clic sul pulsante **Attach existing policies directly** (Collega direttamente i criteri esistenti).

    b. Cercare il nuovo criterio creato nella sezione filtro, **AzureAD_SSOUserRole_Policy**.
    
    c. Selezionare il **criterio** e quindi fare clic sul pulsante **Next: Review** (Avanti: Riepilogo).

29. Controllare il criterio per l'utente collegato seguendo questa procedura:

    ![Add user](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. Controllare il nome utente, il tipo di accesso e il criterio associati all'utente.
    
    b. Fare clic sul pulsante **Crea utente** nell'angolo inferiore destro per creare l'utente.

30. Scaricare le credenziali dell'utente seguendo questa procedura:

    ![Add user](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. Copiare l'**ID chiave di accesso** e la **chiave di accesso segreta** dell'utente.
    
    b. Immettere queste credenziali nella sezione del provisioning utenti di Azure AD per recuperare i ruoli dalla console AWS.
    
    c. Fare clic sul pulsante **Chiudi** in basso.

31. Passare alla sezione **Provisioning utente** dell'app Amazon Web Services nel portale di gestione di Azure AD.

    ![Add user](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. Immettere la **chiave di accesso** e il **segreto** rispettivamente nei campi **Segreto client** e **Token segreto**.

    ![Add user](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. Immettere la chiave di accesso utente AWS nel campo **Segreto client**.
    
    b. Immettere il segreto utente AWS nel campo **Token segreto**.
    
    c. Fare clic sul pulsante **Prova connessione**. La prova della connessione dovrebbe riuscire.

    d. Salvare l'impostazione facendo clic sul pulsante **Salva** nella parte superiore.
 
33. Assicurarsi che Stato provisioning sia impostato su **Attivato** nella sezione Impostazioni e fare clic sul pulsante **Salva** nella parte superiore.

    ![Add user](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Creare un utente di test di Amazon Web Services (AWS)

Questa sezione descrive come creare un utente chiamato Britta Simon in Amazon Web Service (AWS). Per Amazon Web Services (AWS) non è necessario creare un utente nel sistema per ottenere l'accesso SSO, pertanto non è necessario eseguire nessuna di queste operazioni.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso ad Amazon Web Services (AWS).

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon ad Amazon Web Services (AWS), seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Amazon Web Service (AWS)**.

    ![Il collegamento Amazon Web Services (AWS) nell'elenco Applicazioni](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Amazon Web Service (AWS) nel riquadro di accesso, si dovrebbe accedere automaticamente all'applicazione Amazon Web Service (AWS).
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

