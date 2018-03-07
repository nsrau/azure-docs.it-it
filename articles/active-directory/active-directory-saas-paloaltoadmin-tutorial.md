---
title: 'Esercitazione: Integrazione di Azure Active Directory con Palo Alto Networks - Admin UI | Microsoft Docs'
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
ms.openlocfilehash: 60430f08f54232db619efd054ca3a7d9a44f4cdc
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Esercitazione: Integrazione di Azure Active Directory con Palo Alto Networks - Admin UI

Questa esercitazione descrive come integrare Palo Alto Networks - Admin UI con Azure Active Directory (Azure AD).

L'integrazione di Palo Alto Networks - Admin UI con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Palo Alto Networks - Admin UI.
- È possibile abilitare gli utenti per l'accesso automatico a Palo Alto Networks - Admin UI (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Palo Alto Networks - Admin UI, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Un firewall di nuova generazione Palo Alto Networks o Panorama (sistema di gestione centralizzata per firewall)

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Palo Alto Networks - Admin UI dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Aggiunta di Palo Alto Networks - Admin UI dalla raccolta
Per configurare l'integrazione di Palo Alto Networks - Admin UI in Azure AD, è necessario aggiungere Palo Alto Networks - Admin UI dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Palo Alto Networks - Admin UI dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Palo Alto Networks - Admin UI**, selezionare **Palo Alto Networks - Admin UI** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Palo Alto Networks - Admin UI nell'elenco dei risultati](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Admin UI usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Palo Alto Networks - Admin UI corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Palo Alto Networks - Admin UI.

Per stabilire la relazione di collegamento, in Palo Alto Networks - Admin UI assegnare il valore del **nome utente** di Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Admin UI, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Palo Alto Networks - Admin UI](#create-a-palo-alto-networks---admin-ui-test-user)**: per avere una controparte di Britta Simon in Palo Alto Networks - Admin UI collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Palo Alto Networks - Admin UI.

**Per configurare l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Admin UI, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Palo Alto Networks - Admin UI** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. Nella sezione **URL e dominio Palo Alto Networks - Admin UI** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Palo Alto Networks - Admin UI](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<Customer Firewall FQDN>/php/login.php`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<Customer Firewall FQDN>:443/SAML20/SP`
    
    c. Nella casella di testo **URL di risposta** digitare l'URL del servizio consumer di asserzione (ACS) usando il modello seguente: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`
    

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Palo Alto Networks - Admin UI](https://support.paloaltonetworks.com/support). 
 
4. L'applicazione Palo Alto Networks - Admin UI prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.
    
    ![Configure Single Sign-On](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura. Si noti che i valori degli attributi sono solo riportati a titolo di esempio. Eseguire il mapping dei valori appropriati per username e adminrole. Esiste un altro attributo facoltativo, "accessdomain", che viene usato per limitare l'accesso dell'amministratore a specifici sistemi virtuali sul firewall.
        
    | Nome attributo | Valore attributo |
    | --- | --- |    
    | username | user.userprincipalname |
    | adminrole | customadmin |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **Ok**

    > [!NOTE]
    > Per altre informazioni sugli attributi, è possibile fare riferimento agli articoli seguenti.
    > 1. Profilo del ruolo amministrativo per Admin UI (adminrole): https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile
    > 2. Dominio di accesso al dispositivo per Admin UI (accessdomain): https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain

6. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_400.png)

8. Aprire l'interfaccia utente di amministrazione del firewall Palo Alto Networks come amministratore in un'altra finestra del browser.

9. Fare clic su **Device** (Dispositivo).

    ![Configurare l'accesso Single Sign-On per Palo Alto](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

10. Selezionare **SAML Identity Provider** (Provider di identità SAML) nella barra di spostamento a sinistra e fare clic su Import (Importa) per importare il file di metadati.

    ![Configurare l'accesso Single Sign-On per Palo Alto](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

11. Eseguire le operazioni seguenti nella finestra di importazione

    ![Configurare l'accesso Single Sign-On per Palo Alto](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Nella casella di testo **Profile Name** (Nome profilo) specificare un nome, ad esempio Azure AD Admin UI.
    
    b. In **Identity Provider Metadata** (Metadati provider di identità) fare clic su **Browse** (Sfoglia) e selezionare il file metadata.xml scaricato dal portale di Azure.
    
    c. Deselezionare "**Validate Identity Provider Certificate**" (Convalida certificato provider di identità).
    
    d. Fare clic su **OK**.
    
    e. Eseguire il commit delle configurazioni del firewall selezionando il pulsante **Commit**.

12. Selezionare **SAML Identity Provider** (Provider di identità SAML) nella barra di spostamento a sinistra e fare clic sul profilo del provider di identità SAML (ad esempio, AzureAD Admin UI) creato nel passaggio precedente. 
    
  ![Configurare l'accesso Single Sign-On per Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

13. Eseguire le azioni seguenti nella finestra **SAML Identity Provider Server Profile** (Profilo server provider di identità SAML).

  ![Configurare il processo Single Log-Out per Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
  a. Nella casella di testo **Identity Provider SLO URL** (URL Single Sign-On provider di identità) rimuovere il precedente URL Single Sign-On e aggiungere l'URL seguente: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
  b. Fare clic su **OK**.


14. Nell'interfaccia utente di amministrazione del firewall di Palo Alto Networks fare clic su **Device** (Dispositivo) e quindi selezionare **Admin Roles** (Ruoli amministrazione).

15. Fare clic su **Add** . Nella finestra Admin Role Profile (Profilo ruolo amministratore) specificare un nome per il ruolo di amministratore, ad esempio fwadmin. Questo nome del ruolo di amministratore deve corrispondere al nome dell'attributo del ruolo di amministratore SAML inviato dal provider di identità. Nel passaggio 5 sono stati creati il nome e il valore del ruolo di amministratore. 

  ![Configurare il ruolo di amministratore per Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
16. Nell'interfaccia utente di amministrazione del firewall fare clic su **Device** (Dispositivo) e selezionare **Authentication Profile** (Profilo di autenticazione).

17. Fare clic su **Add** . Nella finestra Authentication Profile (Profilo di autenticazione) eseguire le azioni seguenti: 

 ![Configurare il profilo di autenticazione per Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

   a. Nella casella di testo **Name** (Nome) specificare un nome, ad esempio AzureSAML_Admin_AuthProfile.
    
   b. Nell'elenco a discesa **Type** (Tipo) selezionare **SAML** 
   
   c. Nell'elenco a discesa IdP Server Profile (Profilo server provider di identità) selezionare il profilo per il server del provider di identità SAML appropriato, ad esempio AzureAD Admin UI.
   
   c. Selezionare la casella di controllo "**Enable Single Logout**" (Abilita Single Log-Out).
    
   d. Immettere il nome dell'attributo, ad esempio adminrole, nella casella di testo Admin Role Attribute (Attributo ruolo di amministratore). 
   
   e. Selezionare la scheda Advanced (Avanzate) e quindi fare clic sul pulsante **Add** (Aggiungi) nel riquadro Allow List (Elenco Consentiti). Selezionare tutti gli utenti e gruppi o gli utenti e gruppi specifici che possono eseguire l'autenticazione con questo profilo. Quando un utente esegue l'autenticazione, il firewall trova la corrispondenza tra il nome utente o il gruppo associato e le voci incluse nell'elenco. Se non vengono aggiunte voci, nessun utente può eseguire l'autenticazione.
   
   ![Configurare il profilo di autenticazione per Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
   
   f. Fare clic su **OK**.

18. Per consentire agli amministratori di usare l'accesso SSO SAML mediante Azure, fare clic su **Device** (Dispositivo) e selezionare **Setup** (Imposta). Nel riquadro Setup (Imposta) selezionare la scheda **Management** (Gestione) e fare clic sull'icona a forma di ingranaggio in **Authentication Settings** (Impostazioni autenticazione). 

 ![Configurare le impostazioni di autenticazione per Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. Selezionare il profilo di autenticazione SAML creato nel passaggio 17. (ad esempio, AzureSAML_Admin_AuthProfile)

 ![Configurare le impostazioni di autenticazione per Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. Fare clic su **OK**.

21. Eseguire il commit delle configurazioni selezionando il pulsante **Commit**.


> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Creare un utente di test di Palo Alto Networks - Admin UI

Palo Alto Networks - Admin UI supporta il provisioning utenti JIT (Just-In-Time). In questo modo, nel sistema viene creato automaticamente un utente dopo l'autenticazione, se non è già presente. In questa sezione non è necessario eseguire alcuna operazione.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Palo Alto Networks - Admin UI.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Palo Alto Networks - Admin UI, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Palo Alto Networks - Admin UI**.

    ![Collegamento di Palo Alto Networks - Admin UI nell'elenco delle applicazioni](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Palo Alto Networks - Admin UI nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Palo Alto Networks - Admin UI.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
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

