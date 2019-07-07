---
title: 'Esercitazione: Integrazione di Azure Active Directory con Alibaba Cloud Service (Role-based SSO) | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Alibaba Cloud Service (Role-based SSO).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdfd19d9a0e928e26ad6f01ba4b9c3f493aacb0c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107158"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>Esercitazione: Integrazione di Azure Active Directory con Alibaba Cloud Service (Role-based SSO)

Questa esercitazione descrive come integrare Alibaba Cloud Service (Role-based SSO) con Azure Active Directory (Azure AD).
L'integrazione di Alibaba Cloud Service (Role-based SSO) con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere ad Alibaba Cloud Service (Role-based SSO).
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) ad Alibaba Cloud Service (Role-based SSO) con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Alibaba Cloud Service (Role-based SSO), sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Alibaba Cloud Service (Role-based SSO) abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Alibaba Cloud Service (Role-based SSO) supporta l'accesso SSO avviato da **IDP**

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Aggiunta di Alibaba Cloud Service (Role-based SSO) dalla raccolta

Per configurare l'integrazione di Alibaba Cloud Service (Role-based SSO) in Azure AD, è necessario aggiungere Alibaba Cloud Service (Role-based SSO) dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Alibaba Cloud Service (Role-based SSO) dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Alibaba Cloud Service (Role-based SSO)** selezionare **Alibaba Cloud Service (Role-based SSO)** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Alibaba Cloud Service (Role-based SSO) nell'elenco risultati](common/search-new-app.png)

5. Nella pagina **Alibaba Cloud Service (Role-based SSO)** fare clic su **Proprietà** nel riquadro di spostamento a sinistra, copiare l'**ID oggetto** e salvarlo nel computer per usarlo in seguito.

    ![Configurazione delle proprietà](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)
    
## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Alibaba Cloud Service (Role-based SSO) usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Alibaba Cloud Service (Role-based SSO).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Alibaba Cloud Service (Role-based SSO), è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On in base al ruolo in Alibaba Cloud Service](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Alibaba Cloud Service (Role-based SSO)](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Alibaba Cloud Service (Role-based SSO)](#create-alibaba-cloud-service-role-based-sso-test-user)** : per avere una controparte di Britta Simon in Alibaba Cloud Service (Role-based SSO) collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Alibaba Cloud Service (Role-based SSO), seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), nella pagina di integrazione dell'applicazione **Alibaba Cloud Service (Role-based SSO)** , selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    >[!NOTE]
    >Si otterranno i metadati del provider di servizi da questo [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Fare clic su **Carica il file di metadati**.

    ![image](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![image](common/browse-upload-metadata.png)

    c. Al termine del caricamento del file di metadati, i valori di **Identificatore** e **URL di risposta** vengono inseriti automaticamente nella casella di testo della sezione di Alibaba Cloud Service (Role-based SSO):

    ![image](common/idp-intiated.png)

    > [!Note]
    > Se i valori di **Identificatore** e **URL di risposta** non vengono popolati automaticamente, inserirli manualmente in base alle proprie esigenze.

5. L'applicazione Alibaba Cloud Service (Role-based SSO) prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

6. Oltre a quelli elencati in precedenza, l'applicazione Alibaba Cloud Service (Role-based SSO) prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:

    | NOME | Spazio dei nomi | Attributo di origine|
    | ---------------| ------------| --------------- |
    | Ruolo | https:\//www.aliyun.com/SAML-Role/Attribute | user.assignedroles |
    | RoleSessionName | https:\//www.aliyun.com/SAML-Role/Attribute | user.userprincipalname |

    > [!NOTE]
    > Fare clic [qui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) per sapere come configurare **Ruolo** in Azure AD.

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Save**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

8. Nella sezione **Configura Alibaba Cloud Service (Role-based SSO)** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Configurare l'accesso Single Sign-On in base al ruolo in Alibaba Cloud Service

1. Accedere alla [console RAM](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) di Alibaba Cloud usando Account1.

2. Nel pannello di navigazione a sinistra selezionare **SSO**.

3. Nella scheda **Role-based SSO** (SSO in base al ruolo) fare clic su **Create IdP** (Crea IdP).

4. Nella pagina visualizzata immettere `AAD` nel campo IdP Name (Nome IdP), immettere una descrizione nel campo **Note** (Nota), fare clic su **Upload** (Carica) per caricare il file di metadati federativi scaricato in precedenza e fare clic su **OK**.

5. Dopo aver creato il provider di identità, fare clic su **Create RAM Role** (Crea ruolo RAM).

6. Nel campo **RAM Role Name** (Nome del ruolo RAM) immettere `AADrole`, selezionare `AAD` dall'elenco a discesa **Select IdP** (Seleziona IdP) e fare clic su OK.

    >[!NOTE]
    >È possibile concedere l'autorizzazione al ruolo in base alle esigenze. Dopo aver creato il provider di identità e il ruolo corrispondente, si consiglia di salvare i numeri ARN del provider di identità e del ruolo per un uso successivo. È possibile ottenere i numeri ARN nella pagina delle informazioni sul provider di identità e nella pagina delle informazioni sul ruolo.

7. Associare il ruolo Alibaba Cloud RAM (AADrole) all'utente di Azure AD (u2): Per associare il ruolo RAM all'utente di Azure AD, è necessario creare un ruolo in Azure AD seguendo questa procedura:

    a. Accedere ad [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer?spm=a2c63.p38356.879954.9.7d904e167h6Yg9).

    b. Fare clic su **autorizzazioni di modifica** per ottenere le autorizzazioni necessarie per creare un ruolo.

    ![Configurazione di Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Selezionare le autorizzazioni seguenti dall'elenco e fare clic su **Autorizzazioni di modifica**, come illustrato nella figura seguente.

    ![Configurazione di Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Dopo che le autorizzazioni sono state concesse, ripetere l'accesso a Graph Explorer.

    d. Nella pagina di Graph Explorer selezionare **GET** dal primo elenco a discesa e **beta** dal secondo elenco a discesa. Immettere quindi `https://graph.microsoft.com/beta/servicePrincipals` nel campo accanto agli elenchi a discesa e fare clic su **Esegui query**.

    ![Configurazione di Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Se si usano più directory, è possibile immettere `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` nel campo della query.

    e. Nella sezione **Response Preview** (Anteprima della risposta) estrarre la proprietà appRoles dall'entità servizio per un uso successivo.

    ![Configurazione di Graph](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Per individuare la proprietà appRoles, immettere `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` nel campo della query. Si noti che `objectID` è l'ID dell'oggetto copiato dalla pagina **Proprietà** di Azure AD.

    f. Tornare a Graph Explorer, sostituire il metodo **GET** con **PATCH**, incollare il contenuto seguente nella sezione **Corpo della richiesta** e fare clic su **Esegui query**:
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > `value` rappresenta i numeri ARN del provider di identità e del ruolo creati nella console RAM. È possibile aggiungere più ruoli in base alle esigenze. Azure AD invierà il valore di questi ruoli come valore attestazione nella risposta SAML. È tuttavia possibile aggiungere i nuovi ruoli solo dopo la parte `msiam_access` per l'operazione patch. Per semplificare il processo di creazione, è consigliabile usare un generatore di ID, ad esempio Generatore di GUID, per generare gli ID in tempo reale.

    g. Dopo che all'entità servizio è stata applicata la patch con il ruolo necessario, collegare il ruolo all'utente di Azure AD (u2) seguendo la procedura indicata nella sezione **Assegnare l'utente test di Azure AD** dell'esercitazione.

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>Configurare l'accesso Single Sign-On di Alibaba Cloud Service (Role-based SSO)

Per configurare l'accesso Single Sign-On sul lato **Alibaba Cloud Service (Role-based SSO)** , è necessario inviare il file **XML di metadati della federazione** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di Alibaba Cloud Service (Role-based SSO)](https://www.aliyun.com/service/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Alibaba Cloud Service (Role-based SSO).

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Alibaba Cloud Service (Role-based SSO)** .

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Alibaba Cloud Service (Role-based SSO)** .

    ![Collegamento di Alibaba Cloud Service (Role-based SSO) nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella scheda **Utenti e gruppi** selezionare u2 dall'elenco di utenti e fare clic su **Seleziona**. Fare quindi clic su **Assegna**.

    ![Configurazione di test](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

6. Visualizzare il ruolo assegnato e testare Alibaba Cloud Service (Role-based SSO).

    ![Configurazione di test](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Dopo aver assegnato l'utente (u2), il ruolo creato viene collegato automaticamente all'utente. Se sono stati creati più ruoli, è necessario collegare il ruolo appropriato all'utente in base alle esigenze. Per implementare l'accesso SSO in base al ruolo da Azure AD a più account Alibaba Cloud, ripetere i passaggi precedenti.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Creare l'utente di test di Alibaba Cloud Service (Role-based SSO)

In questa sezione viene creato un utente chiamato Britta Simon in Alibaba Cloud Service (Role-based SSO). Collaborare con il [team di supporto di Alibaba Cloud Service (Role-based SSO)](https://www.aliyun.com/service/) per aggiungere gli utenti nella piattaforma Alibaba Cloud Service (Role-based SSO). Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

Dopo aver completato le configurazioni precedenti, testare Alibaba Cloud Service (Role-based SSO) seguendo questa procedura:

1. Nel portale di Azure passare alla pagina **Alibaba Cloud Service (Role-based SSO)** , selezionare **Single Sign-On** e fare clic su **Test**.

    ![Configurazione di test](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Fare clic su **Accedi con l'account utente corrente**.

    ![Configurazione di test](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Nella pagina di selezione dell'account selezionare u2.

    ![Configurazione di test](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. Viene visualizzata la pagina seguente, che indica che l'accesso SSO in base al ruolo ha avuto esito positivo.

    ![Configurazione di test](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

