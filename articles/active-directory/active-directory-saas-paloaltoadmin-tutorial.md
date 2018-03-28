---
title: 'Esercitazione: Integrare Azure Active Directory con Palo Alto Networks - Admin UI | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Palo Alto Networks - Admin UI.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: c5be53f06e009cb2d5180e43318c8670139a68db
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>Integrare Azure Active Directory con Palo Alto Networks - Admin UI

Questa esercitazione descrive come integrare Azure Active Directory (Azure AD) con Palo Alto Networks - Admin UI.

L'integrazione di Azure AD con Palo Alto Networks - Admin UI offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Palo Alto Networks - Admin UI.
- È possibile abilitare l'accesso automatico degli utenti a Palo Alto Networks - Admin UI (Single Sign-On) con i loro account Azure AD.
- È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Palo Alto Networks - Admin UI, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Un firewall di nuova generazione Palo Alto Networks o Panorama (sistema di gestione centralizzata per firewall)

> [!NOTE]
> Per testare i passaggi di questa esercitazione, è consigliabile *non* usare un ambiente di produzione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

* Aggiunta di Palo Alto Networks - Admin UI dalla raccolta
* Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>Aggiungere Palo Alto Networks - Admin UI dalla raccolta
Per configurare l'integrazione di Azure AD con Palo Alto Networks - Admin UI, è necessario aggiungere Palo Alto Networks - Admin UI dalla raccolta al proprio elenco di app SaaS gestite eseguendo queste operazioni:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Finestra "Applicazioni aziendali"][2]
    
3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore della finestra.

    ![Pulsante "Nuova applicazione"][3]

4. Nella casella di ricerca digitare **Palo Alto Networks - Admin UI**, selezionare **Palo Alto Networks - Admin UI** nel pannello dei risultati e quindi selezionare il pulsante **Aggiungi**.

    ![Palo Alto Networks - Admin UI nell'elenco dei risultati](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Admin UI usando un utente di test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve poter identificare quale sia la controparte di un utente di Azure AD in Palo Alto Networks - Admin UI. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e lo stesso utente in Palo Alto Networks - Admin UI.

Per stabilire la relazione di collegamento, assegnare a *Username* (Nome utente) in Palo Alto Networks - Admin UI il valore del *nome utente* in Azure AD.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Admin UI, completare i blocchi predefiniti nelle cinque sezioni seguenti.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

Abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure e configurarlo nell'applicazione Palo Alto Networks - Admin UI eseguendo queste operazioni:

1. Nella pagina di integrazione dell'applicazione **Palo Alto Networks - Admin UI** del portale di Azure selezionare **Single Sign-On**.

    ![Collegamento "Single Sign-On"][4]

2. Nella finestra **Single Sign-On** selezionare **Accesso basato su SAML** nella casella **Modalità Single Sign-On**.
 
    ![Finestra "Single Sign-On"](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. In **URL e dominio Palo Alto Networks - Admin UI** eseguire queste operazioni:

    ![Informazioni su URL e dominio di Palo Alto Networks - Admin UI per l'accesso Single Sign-On](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. Nella casella **URL accesso** digitare un URL in questo formato: *https://\<FQDN del firewall del cliente>/php/login.php*.

    b. Nella casella **Identificatore** digitare un URL in questo formato: *https://\<FQDN del firewall del cliente>:443/SAML20/SP*.
    
    c. Nella casella **URL di risposta** digitare l'URL del servizio consumer di asserzione in questo formato: *https://\<FQDN del firewall del cliente>:443/SAML20/SP/ACS*.
    
    > [!NOTE] 
    > I valori precedenti non sono valori reali. Aggiornarli con l'URL e l'identificatore di accesso effettivi. Per ottenere questi valori, contattare il [team del supporto clienti di Palo Alto Networks - Admin UI](https://support.paloaltonetworks.com/support). 
 
4. Poiché l'applicazione Palo Alto Networks - Admin UI si aspetta un formato specifico per le asserzioni SAML, configurare le attestazioni come mostrato nell'immagine seguente. Gestire i valori degli attributi nella sezione **Attributi utente** della pagina **Application Integration** (Integrazione applicazioni) eseguendo queste operazioni:
    
    ![Elenco Attributi token SAML](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > Poiché i valori degli attributi sono solo esempi, associare i valori appropriati per *username* e *adminrole*. Esiste un altro attributo facoltativo, *accessdomain*, che viene usato per limitare l'accesso dell'amministratore a sistemi virtuali specifici sul firewall.
   >
        
    | Nome attributo | Valore attributo |
    | --- | --- |    
    | username | user.userprincipalname |
    | adminrole | customadmin |

    a. Selezionare **Aggiungi attributo**.  
    
    ![Pulsante "Aggiungi attributo"](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_04.png)

    Viene visualizzata la finestra **Aggiungi attributo**.

    ![Finestra "Aggiungi attributo"](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nella casella **Valore** digitare il valore dell'attributo specificato per la riga.
    
    d. Selezionare **OK**.

    > [!NOTE]
    > Per altre informazioni sugli attributi, vedere gli articoli seguenti:
    > * [Administrative role profile for Admin UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile) (Profilo del ruolo amministrativo per Admin UI - adminrole)
    > * [Device access domain for Admin UI (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain) (Dominio di accesso al dispositivo per Admin UI - accessdomain)
    >

5. In **Certificato di firma SAML** selezionare **XML metadati** e quindi **Salva**.

    ![Collegamento per il download del file XML dei metadati](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![Pulsante Salva](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_400.png)

6. Aprire l'interfaccia utente di amministrazione del firewall di Palo Alto Networks come amministratore in una nuova finestra.

7. Selezionare la scheda **Device** (Dispositivo).

    ![Scheda Device (Dispositivo)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

8. Nel riquadro a sinistra selezionare **SAML Identity Provider** (Provider di identità SAML) e quindi **Import** (Importa) per importare il file di metadati.

    ![Pulsante di importazione del file di metadati](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

9. Nella finestra **SAML Identity Provider Server Profile Import** (Importazione profilo server provider di identità SAML) eseguire queste operazioni:

    ![Finestra "SAML Identity Provider Server Profile Import" (Importazione profilo server provider di identità SAML)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Nella casella di testo **Profile Name** (Nome profilo) specificare un nome, ad esempio **AzureAD Admin UI**.
    
    b. In **Identity Provider Metadata** (Metadati provider di identità) selezionare **Browse** (Sfoglia) e quindi selezionare il file metadata.xml scaricato in precedenza dal portale di Azure.
    
    c. Deselezionare la casella di controllo **Validate Identity Provider Certificate** (Convalida certificato provider di identità).
    
    d. Selezionare **OK**.
    
    e. Per eseguire il commit delle configurazioni nel firewall, selezionare **Commit**.

10. Nel riquadro a sinistra selezionare **SAML Identity Provider** (Provider di identità SAML) e quindi selezionare il profilo del provider di identità SAML, ad esempio **AzureAD Admin UI**, creato nel passaggio precedente. 

    ![Profilo del provider di identità SAML](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

11. Nella finestra **SAML Identity Provider Server Profile** (Profilo server provider di identità SAML) eseguire queste operazioni:

    ![Finestra "SAML Identity Provider Server Profile" (Profilo server provider di identità SAML)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Nella casella di testo **Identity Provider SLO URL** (URL SLO provider di identità) sostituire l'URL SLO precedentemente importato con questo URL: **https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**.
  
    b. Selezionare **OK**.

12. Nell'interfaccia utente di amministrazione del firewall di Palo Alto Networks selezionare **Device** (Dispositivo) e quindi selezionare **Admin Roles** (Ruoli amministrazione).

13. Fare clic sul pulsante **Aggiungi**. 

14. Nella casella **Name** (Nome) nella finestra **Admin Role Profile** (Profilo ruolo amministratore) specificare un nome per il ruolo di amministratore, ad esempio **fwadmin**.  
    Il nome del ruolo di amministratore deve corrispondere al nome dell'attributo del ruolo di amministratore SAML inviato dal provider di identità. Il nome e il valore del ruolo di amministratore sono stati creati nel passaggio 4.

    ![Configurare il ruolo di amministratore per Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
15. Nell'interfaccia utente di amministrazione del firewall selezionare **Device** (Dispositivo) e quindi **Authentication Profile** (Profilo di autenticazione).

16. Fare clic sul pulsante **Aggiungi**. 

17. Nella finestra **Authentication Profile** (Profilo di autenticazione) eseguire queste operazioni: 

    ![Finestra "Authentication Profile" (Profilo di autenticazione)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Nella casella di testo **Name** (Nome) specificare un nome, ad esempio **AzureSAML_Admin_AuthProfile**.
    
    b. Nell'elenco a discesa **Type** (Tipo) selezionare **SAML**. 
   
    c. Nell'elenco a discesa **IdP Server Profile** (Profilo server provider di identità) selezionare il profilo per il server del provider di identità SAML appropriato, ad esempio **AzureAD Admin UI**.
   
    c. Selezionare la casella di controllo **Enable Single Logout** (Abilita punto di disconnessione singolo).
    
    d. Nella casella **Admin Role Attribute** (Attributo ruolo di amministratore) immettere il nome dell'attributo, ad esempio **adminrole**. 
    
    e. Selezionare la scheda **Advanced** (Avanzate) e quindi selezionare **Add** (Aggiungi) in **Allow List** (Elenco nomi consentiti). 
    
    ![Pulsante Add (Aggiungi) nella scheda Advanced (Avanzate)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
    
    f. Selezionare la casella di controllo **All** (Tutti) oppure selezionare gli utenti e i gruppi che possono autenticarsi con questo profilo.  
    Quando un utente esegue l'autenticazione, il firewall trova la corrispondenza tra il nome utente o il gruppo associato e le voci incluse nell'elenco. Se non vengono aggiunte voci, nessun utente può eseguire l'autenticazione.

    g. Selezionare **OK**.

18. Per consentire agli amministratori di usare l'accesso SSO SAML tramite Azure, selezionare **Device** (Dispositivo) > **Setup** (Imposta). Nel riquadro **Setup** (Imposta) selezionare la scheda **Management** (Gestione) e quindi in **Authentication Settings** (Impostazioni autenticazione) selezionare il pulsante **Settings** (Impostazioni) raffigurato da un ingranaggio. 

 ![Pulsante Settings (Impostazioni)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. Selezionare il profilo di autenticazione SAML creato nel passaggio 17, ad esempio **AzureSAML_Admin_AuthProfile**.

 ![Campo Authentication Profile (Profilo di autenticazione)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. Selezionare **OK**.

21. Per eseguire il commit della configurazione, selezionare **Commit**.


> [!TIP]
> Durante la configurazione dell'app, nel [portale di Azure](https://portal.azure.com) è disponibile un riepilogo delle istruzioni precedenti. Dopo aver aggiunto l'app nella sezione **Active Directory** > **Applicazioni aziendali**, selezionare la scheda **Single Sign-On** e quindi accedere alla documentazione integrata nella sezione **Configurazione** nella parte inferiore. Per altre informazioni sulla funzionalità di documentazione integrata vedere [Documentazione integrata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione si crea un utente di test Britta Simon nel portale di Azure seguendo questa procedura:

![Creare un utente test di Azure AD][100]

1. Nel portale di Azure fare clic su **Azure Active Directory** nel riquadro sinistro.

    ![Collegamento Azure Active Directory](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. Per visualizzare un elenco degli utenti correnti, selezionare **Utenti e gruppi** > **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. Nella parte superiore della finestra **Tutti gli utenti** selezionare **Aggiungi**.

    ![Pulsante Aggiungi](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Viene visualizzata la finestra **Utente**.

4. Nella finestra **Utente** seguire questa procedura:

    ![Finestra Utente](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Creare un utente di test di Palo Alto Networks - Admin UI

Palo Alto Networks - Admin UI supporta il provisioning utenti JIT. Se non esiste già, un utente viene automaticamente creato nel sistema dopo un tentativo di autenticazione riuscito. Non è necessario creare manualmente l'utente.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Palo Alto Networks - Admin UI. A tale scopo, seguire questa procedura:

![Assegnare il ruolo utente][200] 

1. Nel portale di Azure aprire la visualizzazione **Applicazioni**, passare alla visualizzazione **Directory** e quindi selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Collegamenti "Applicazioni aziendali" e "Tutte le applicazioni"][201] 

2. Nell'elenco **Applicazioni** selezionare **Palo Alto Networks - Admin UI**.

    ![Collegamento Palo Alto Networks - Admin UI](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. Nel riquadro sinistro fare clic su **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic su **Aggiungi** e quindi nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Fare clic sul pulsante **Seleziona**.

7. Nella finestra **Aggiungi assegnazione** selezionare **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando seleziona il riquadro Palo Alto Networks - Admin UI nel pannello di accesso, viene automaticamente eseguito l'accesso all'applicazione Palo Alto Networks - Admin UI.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sull'integrazione di app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_203.png

