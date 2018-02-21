---
title: 'Esercitazione: Integrazione di Azure Active Directory con Shmoop For Schools | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Shmoop For Schools.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jeedes
ms.openlocfilehash: 48db70834f96adbb7097457caca8489ea1a57da5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Esercitazione: Integrazione di Azure Active Directory con Shmoop For Schools

Questa esercitazione descrive come integrare Shmoop For Schools con Azure Active Directory (Azure AD).

L'integrazione di Shmoop For Schools con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Shmoop For Schools.
- È possibile consentire agli utenti l'accesso automatico a Shmoop For Schools con gli account Azure AD personali.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Shmoop For Schools, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Shmoop For Schools abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

Per testare i passaggi di questa esercitazione è consigliabile:

- Usare l'ambiente di produzione solo se necessario.
- Se non è già disponibile un ambiente di valutazione di Azure AD, [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Shmoop For Schools dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-shmoop-for-schools-from-the-gallery"></a>Aggiungere Shmoop For Schools dalla raccolta
Per configurare l'integrazione di Shmoop For Schools in Azure AD, è necessario aggiungere Shmoop For Schools dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Shmoop For Schools dalla raccolta, seguire questa procedura:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Per aggiungere una nuova applicazione selezionare il pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Shmoop For Schools**. Selezionare **Shmoop For Schools** dai risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Shmoop For Schools nell'elenco dei risultati](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Shmoop For Schools usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di Shmoop For Schools corrisponde a un utente di Azure AD. In altre parole, è necessario stabilire un collegamento tra un utente di Azure AD e l'utente correlato in Shmoop For Schools.

In Shmoop For Schools assegnare a **Username** (Nome utente) lo stesso **nome utente** di Azure AD. La relazione di collegamento è stata così stabilita.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Shmoop For Schools, completare i blocchi predefiniti seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente di test di Shmoop For Schools](#create-a-shmoop-for-schools-test-user) per avere una controparte di Britta Simon in Shmoop For Schools collegata alla rappresentazione dell'utente in Azure AD.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Shmoop For Schools.

**Per configurare Single Sign-On di Azure AD con Shmoop For Schools, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Shmoop For Schools** del portale di Azure selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** nel menu a discesa **Modalità Single Sign-On**.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. Nella sezione **URL e dominio Shmoop For Schools** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. Nella casella **URL di accesso** digitare un URL usando il modello seguente: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. Nella casella **Identificatore** digitare un URL usando il modello seguente: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con URL di accesso e identificatore effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Shmoop For Schools](mailto:support@shmoop.com). 
 
4. L'applicazione Shmoop For Schools prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Lo screenshot seguente illustra come configurare queste asserzioni:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop For Schools supporta due ruoli per gli utenti: **Teacher** (Insegnante) e **Student** (Studente). Configurare questi ruoli in Azure AD in modo che agli utenti possano essere assegnati i ruoli appropriati. Per informazioni sulla configurazione dei ruoli in Azure AD, vedere [Controllo degli accessi in base al ruolo nelle applicazioni cloud con Azure AD](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/).
    
5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come illustrato nell'immagine precedente.  Seguire quindi questa procedura:

    | Nome attributo | Valore attributo |
    | -------------- | --------------- |
    | role           | user.assignedroles |

    a. Per aprire la finestra di dialogo **Aggiungi attributo**, selezionare **Aggiungi attributo**.
    
    ![Configura accesso Single Sign-On ](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** selezionare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella **Spazio dei nomi**.
    
    e. Selezionare **OK**.

6. Fare clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_400.png)

7. Per generare l'**URL dei metadati**, seguire questa procedura:

    a. Selezionare **Registrazioni per l'app**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_appregistrations.png)
   
    b. Selezionare **Endpoint** per aprire la finestra di dialogo **Endpoint**.  
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_endpointicon.png)

    c. Selezionare il pulsante Copia per copiare l'URL del **DOCUMENTO METADATI FEDERAZIONE** e incollarlo nel Blocco note.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_endpoint.png)
     
    d. Passare alla pagina delle proprietà di **Shmoop For Schools**. Copiare quindi l'**ID applicazione** usando il pulsante **Copia**. Incollarlo nel Blocco note.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_appid.png)

    e. Generare l'**URL dei metadati** usando il modello seguente: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`.   

8. Per configurare l'accesso Single Sign-On sul lato **Shmoop For Schools**, è necessario inviare l'**URL dei metadati** al [team di supporto di Shmoop For Schools](mailto:support@shmoop.com).

> [!TIP]
> Un riepilogo di queste istruzioni è disponibile nel [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali** è sufficiente selezionare la scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Per altre informazioni sulla funzione di documentazione incorporata, vedere la [documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

L'obiettivo di questa sezione consiste nel creare l'utente di test "Britta Simon" nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente di test in Azure AD, seguire questa procedura:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_01.png)

2. Per visualizzare un elenco di utenti, passare a **Utenti e gruppi**. Selezionare quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** selezionare **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Creare un utente test di Shmoop For Schools

Questa sezione descrive come creare un utente chiamato Britta Simon in Shmoop For Schools. Shmoop For Schools supporta il provisioning just-in-time, abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante il tentativo di accesso a Shmoop For Schools viene creato un nuovo utente, se questo non esiste già.

>[!NOTE]
>Se è necessario creare un utente manualmente, contattare il [team di supporto di Shmoop For Schools](mailto:support@shmoop.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Shmoop For Schools.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Shmoop For Schools, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione applicazioni. Passare quindi ad **Applicazioni aziendali** nella vista directory.  Selezionare quindi **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Shmoop For Schools**.

    ![Collegamento di Shmoop For Schools nell'elenco delle applicazioni](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Users and groups** (Utenti e gruppi) selezionare **Britta Simon** nell'elenco di utenti.

6. Nella finestra di dialogo **Utenti e gruppi** fare clic sul pulsante **Seleziona**. 

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona **Shmoop For Schools** nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Shmoop For Schools.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_203.png

