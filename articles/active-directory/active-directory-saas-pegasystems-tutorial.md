---
title: 'Esercitazione: Integrazione di Azure Active Directory con Pegasystems | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Pegasystems.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 924abe287b5e39bb77a418b56b5c33195095e07a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Esercitazione: Integrazione di Azure Active Directory con Pegasystems

Questa esercitazione descrive come integrare Pegasystems con Azure Active Directory (Azure AD).

L'integrazione di Pegasystems con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Pegasystems.
- È possibile abilitare gli utenti per l'accesso automatico a Pegasystems (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Pegasystems, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione di Pegasystems abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Pegasystems dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-pega-systems-from-the-gallery"></a>Aggiunta di Pegasystems dalla raccolta
Per configurare l'integrazione di Pegasystems in Azure AD, è necessario aggiungere Pegasystems dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Pegasystems dalla raccolta, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Pegasystems**, selezionare **Pegasystems** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Pegasystems nell'elenco risultati](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Pegasystems usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Pegasystems che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Pegasystems.

Per stabilire la relazione di collegamento, in Pegasystems assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Pegasystems, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Pegasystems](#create-a-pega-systems-test-user)**: per avere una controparte di Britta Simon in Pegasystems collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Pegasystems.

**Per configurare l'accesso Single Sign-On di Azure AD con Pegasystems, eseguire la procedura seguente:**

1. Nella pagina di integrazione dell'applicazione **Pegasystems** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

3. Nella sezione **URL e dominio Pegasystems** eseguire la procedura seguente se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni sull'accesso Single Sign-On di URL e dominio di Pegasystems](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni sull'accesso Single Sign-On di URL e dominio di Pegasystems](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url1.png)

    Nella casella di testo **Stato dell'inoltro** digitare un URL usando il modello seguente: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornare i valori con l'identificatore, l'URL di risposta, l'URL dello stato dell'inoltro effettivi. È possibile trovare i valori dell'identificatore e dell'URL di risposta nell'applicazione Pegasystems illustrata più avanti in questa esercitazione. Per ottenere il valore dello stato dell'inoltro, contattare il [team di supporto clienti di Pegasystems](https://www.pega.com/contact-us). 

5. L'applicazione Pegasystems prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Queste attestazioni sono specifiche del cliente e dipendono dai requisiti. Di seguito sono descritte alcune attestazioni facoltative che è possibile configurare per l'applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. 

    ![Configure Single Sign-On](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente ed eseguire i passaggi descritti di seguito:
    
    | Nome attributo | Valore attributo |
    | ------------------- | -------------------- |    
    | uid | *********** |
    | cn  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | Organizzazione | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | workgroup | *********** |
    | Telefono | *********** |

    > [!NOTE]
    > Si tratta di valori specifici del cliente. Specificare i valori appropriati.

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **OK**.

7. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
8. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-pegasystems-tutorial/tutorial_general_400.png)
    
9. Per configurare l'accesso Single Sign-On sul lato **Pegasystems**, aprire il **portale Pega** usando l'account amministratore in un'altra finestra del browser.

10. Selezionare **Create** -> **SysAdmin** -> **Authentication Service** (Crea > Amministrazione del sistema > Servizio di autenticazione).

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
11. Eseguire le operazioni seguenti nella schermata **Create Authentication Service** (Crea servizio di autenticazione):

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Selezionare **SAML 2.0** nella casella Type (Tipo)

    b. Nella casella di testo **Name** (Nome) digitare un nome qualsiasi, ad esempio Azure AD SSO

    c. Nella casella di testo **Short Description** (Descrizione breve) digitare una descrizione  

    d. Fare clic su **Create and open** (Crea e apri) 
    
12. Nella sezione **Identity Provider (IdP) information** (Informazioni sul provider di identità) fare clic su **Import IdP metadata** (Importa dati IdP) e cercare il file di metadati precedentemente scaricato dal portale di Azure. Fare clic su **Submit** (Invia) per caricare i metadati.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
13. Questa operazione inserirà i dati del provider di identità come illustrato di seguito.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
14. Eseguire le azioni seguenti nella sezione **Service Provider (SP) settings** (Impostazioni del provider del servizio):

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Copiare il valore del campo **Entity Identification** (Identificatore entità) e incollarlo nella casella di testo **Identificatore** del portale di Azure.

    b.  Copiare il valore del campo **Assertion Consumer Service (ACS) location** (Posizione del servizio consumer di asserzione) e incollarlo nella casella di testo **URL di risposta** del portale di Azure.

    c. Selezionare **Disable request signing** (Disabilita firma richiesta).

15. Fare clic su **Save**
    
> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-pega-systems-test-user"></a>Creare un utente di test per Pegasystems

Questa sezione descrive come creare un utente chiamato Britta Simon in Pegasystems. Rivolgersi [team di supporto clienti di Pegasystems](https://www.pega.com/contact-us) per creare utenti in Pegasystems.


### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Pegasystems.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Pegasystems, eseguire la procedura seguente:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Pegasystems**.

    ![Collegamento Pegasystems nell'elenco Applicazioni](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Pegasystems nel pannello di accesso, si accederà automaticamente all'applicazione Pegasystems.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_203.png

