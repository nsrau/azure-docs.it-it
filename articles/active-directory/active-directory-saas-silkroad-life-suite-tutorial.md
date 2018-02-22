---
title: 'Esercitazione: Integrazione di Azure Active Directory con SilkRoad Life Suite | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SilkRoad Life Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 0d6af7af7d6b28ff3ea9d518a65b8267a83b71b4
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Esercitazione: Integrazione di Azure Active Directory con SilkRoad Life Suite

Questa esercitazione descrive come integrare SilkRoad Life Suite con Azure Active Directory (Azure AD).

L'integrazione di SilkRoad Life Suite con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SilkRoad Life Suite.
- È possibile abilitare gli utenti per l'accesso automatico a SilkRoad Life Suite (Single Sign-On) con gli account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con SilkRoad Life Suite, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di SilkRoad Life Suite abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di SilkRoad Life Suite dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Aggiunta di SilkRoad Life Suite dalla raccolta
Per configurare l'integrazione di SilkRoad Life Suite in Azure AD, è necessario aggiungere SilkRoad Life Suite dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SilkRoad Life Suite dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **SilkRoad Life Suite**, selezionare **SilkRoad Life Suite** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SilkRoad Life Suite nell'elenco risultati](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SilkRoad Life Suite con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di SilkRoad Life Suite che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SilkRoad Life Suite.

Per stabilire la relazione di collegamento, in SilkRoad Life Suite assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con SilkRoad Life Suite, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di SilkRoad Life Suite](#create-a-silkroad-life-suite-test-user)**: per avere una controparte di Britta Simon in SilkRoad Life Suite collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SilkRoad Life Suite.

**Per configurare Single Sign-On di Azure AD con SilkRoad Life Suite, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SilkRoad Life Suite** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

3. Nella sezione **URL e dominio SilkRoad Life Suite** seguire questa procedura:

    ![Informazioni su URL e dominio per Single Sign-On di SilkRoad Life Suite](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Nella casella di testo **Identificatore** digitare un URL usando il criterio seguente: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di SilkRoad Life Suite](https://www.silkroad.com/locations/). 

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_400.png)
    
6. Nella sezione **Configurazione di SilkRoad Life Suite** fare clic su **Configura SilkRoad Life Suite** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di SilkRoad Life Suite](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

7. Accedere al sito aziendale di SilkRoad come amministratore. 
 
    >[!NOTE] 
    > Per ottenere l'accesso all'applicazione SilkRoad Life Suite Authentication per configurare la federazione con Microsoft Azure AD, contattare il supporto SilkRoad o il rappresentante dei servizi SilkRoad.

8. Passare a **Service Provider** (Provider di servizi) e quindi fare clic su **Federation Details** (Dettagli federazione). 
   
    ![Single Sign-On di Microsoft Azure AD][10]

9. Fare clic su **Download Federation Metadata**(Scarica metadati federazione) e quindi salvare il file di metadati nel computer.
   
    ![Single Sign-On di Microsoft Azure AD][11] 

10. Nell'applicazione **SilkRoad**, fare clic su **Authentication Sources** (Origini autenticazione).
   
    ![Single Sign-On di Microsoft Azure AD][12] 

11. Fare clic su **Add Authentication Source**(Aggiungi origine autenticazione). 
   
    ![Single Sign-On di Microsoft Azure AD][13] 

12. Nella sezione **Add Authentication Source** (Aggiungi origine autenticazione) seguire questa procedura: 
   
    ![Single Sign-On di Microsoft Azure AD][14]
  
    a. In **Option 2 - Metadata File** (Opzione 2 - File di metadati) fare clic su **Browse** (Sfoglia) per caricare il file di metadati scaricato dal portale di Azure.
  
    b. Fare clic su **Create Identity Provider using File Data**.

13. Nella sezione **Authentication Sources** (Origini autenticazione) fare clic su **Edit** (Modifica). 
    
     ![Single Sign-On di Microsoft Azure AD][15] 

14. Nella finestra di dialogo **Edit Authentication Source** (Modifica origine autenticazione) seguire questa procedura: 
    
     ![Single Sign-On di Microsoft Azure AD][16] 

    a. In **Enabled** (Attivo) selezionare **Yes** (Sì).

    b. Nella casella di testo **EntityId** (ID entità) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.
   
    c. Nella casella di testo **IdP Description** (Descrizione IdP) digitare una descrizione per la configurazione, ad esempio *Azure AD SSO*.

    d. Nella casella di testo **Metadata File** (File di metadati) caricare il file di **metadati** scaricato dal portale di Azure.
  
    e. Nella casella di testo **IdP Name** (Nome IdP) digitare un nome specifico per la configurazione, ad esempio *Azure SP*.
  
    f. Nella casella di testo **Logout Service URL** (URL servizio di disconnessione) incollare il valore di **Sign-Out URL** (URL di disconnessione) copiato dal portale di Azure.

    g. Nella casella di testo **sign-on service URL** (URL servizio di accesso) incollare il valore **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure.

    h. Fare clic su **Save**.

15. Disabilitare tutte le altre origini di autenticazione. 
    
     ![Single Sign-On di Microsoft Azure AD][17]

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-silkroad-life-suite-test-user"></a>Creare un utente test di SilkRoad Life Suite

In questa sezione viene creato un utente chiamato Britta Simon in SilkRoad Life Suite. Contattare il [team di supporto clienti di SilkRoad Life Suite](https://www.silkroad.com/locations/) per aggiungere gli utenti nella piattaforma SilkRoad Life Suite. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SilkRoad Life Suite.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a SilkRoad Life Suite, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **SilkRoad Life Suite**.

    ![Collegamento SilkRoad Life Suite nell'elenco delle applicazioni](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SilkRoad Life Suite nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SilkRoad Life Suite.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
