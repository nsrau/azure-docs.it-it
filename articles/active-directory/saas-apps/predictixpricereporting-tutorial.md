---
title: 'Esercitazione: Integrazione di Azure Active Directory con Predictix Price Reporting | Microsoft Docs'
description: In questa esercitazione si apprenderà come configurare l'accesso Single Sign-On tra Azure Active Directory e Predictix Price Reporting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 691d0c43-3aa1-4220-9e46-e7a88db234ad
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 8074919a61ed4f2e3d0b127c13e733b0c485bf62
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65890670"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Esercitazione: Integrazione di Azure Active Directory con Predictix Price Reporting

In questa esercitazione si apprenderà come integrare Predictix Price Reporting con Azure Active Directory (Azure AD).

Questa integrazione offre i vantaggi seguenti:

* È possibile usare Azure AD per controllare chi può accedere a Predictix Price Reporting.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Predictix Price Reporting con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Predictix Price Reporting occorre:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile iscriversi per ottenere una [versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Una sottoscrizione di Predictix Price Reporting con l'accesso Single Sign-On abilitato.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Predictix Price Reporting supporta l'accesso Single Sign-On avviato da provider di servizi.

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Aggiunta di Predictix Price Reporting dalla raccolta

Per configurare l'integrazione di Predictix Price Reporting in Azure AD, è necessario aggiungere Predictix Price Reporting dalla raccolta al proprio elenco di app SaaS gestite.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro sinistro:

    ![Selezionare Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali** > **Tutte le applicazioni**:

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere un'applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra:

    ![Selezionare Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Predictix Price Reporting**. Selezionare **Predictix Price Reporting** nei risultati della ricerca e quindi selezionare **Aggiungi**.

     ![Search Results](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione verrà configurato e testato l'accesso Single Sign-On di Azure AD con Predictix Price Reporting usando un utente di test di nome Britta Simon.
Per abilitare l'accesso Single Sign-On, è necessario stabilire una relazione tra un utente di Azure AD e l'utente corrispondente in Predictix Price Reporting.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Predictix Price Reporting, è necessario completare la procedura seguente:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** per abilitare questa funzionalità per gli utenti.
2. **[Configurare l'accesso Single Sign-On di Predictix Price Reporting](#configure-predictix-price-reporting-single-sign-on)** sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare l'accesso Single Sign-On di Azure AD per tale utente.
5. **[Creare un utente di test di Predictix Price Reporting](#create-a-predictix-price-reporting-test-user)** collegato alla rappresentazione di Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione verrà abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Predictix Price Reporting, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Predictix Price Reporting** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**:

    ![Selezionare Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On:

    ![Selezionare un metodo di accesso Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**:

    ![Icona Modifica](common/edit-urls.png)

4. Nella finestra di dialogo **Configurazione SAML di base** completare la procedura seguente.

    ![Finestra di dialogo Configurazione SAML di base](common/sp-identifier.png)

    1. Nella casella **URL di accesso** immettere un URL nel formato seguente:

       `https://<companyname-pricing>.predictix.com/sso/request`

    1. Nella casella **Identificatore (ID entità)** immettere un URL nel formato seguente:

        | |
        |--|
        | `https://<companyname-pricing>.predictix.com` |
        | `https://<companyname-pricing>.dev.predictix.com` |
        | |

    > [!NOTE]
    > Questi valori sono segnaposto. Usare l'identificatore e l'URL di accesso effettivi. Per ottenere i valori, contattare il [team di supporto di Predictix Price Reporting](https://www.infor.com/company/customer-center/). È anche possibile fare riferimento ai modelli disponibili nella finestra di dialogo **Configurazione SAML di base** del portale di Azure.

5. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il collegamento **Scarica** accanto a **Certificato (Base64)** , in base alle esigenze, quindi salvare il certificato nel computer:

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Predictix Price Reporting** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    1. **URL di accesso**.

    1. **Identificatore Azure AD**.

    1. **URL di disconnessione**.

### <a name="configure-predictix-price-reporting-single-sign-on"></a>Configurare l'accesso Single Sign-On di Predictix Price Reporting

Per configurare l'accesso Single Sign-On sul lato Predictix Price Reporting, è necessario inviare il certificato scaricato e gli URL copiati dal portale di Azure al [team di supporto di Predictix Price Reporting](https://www.infor.com/company/customer-center/). Il team verificherà che la connessione Single Sign-On SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**:

    ![Selezionare Tutti gli utenti](common/users.png)

2. Selezionare **Nuovo utente** nella parte superiore della schermata:

    ![Selezionare Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo **Utente** seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    1. Nella casella **Nome** immettere **BrittaSimon**.
  
    1. Nella casella **Nome utente** immettere **BrittaSimon@\<dominioaziendale.\<estensione>** , ad esempio BrittaSimon@contoso.com.

    1. Selezionare **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon verrà abilitata per l'uso dell'accesso Single Sign-On di Azure AD concedendole l'accesso a Predictix Price Reporting.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Predictix Price Reporting**.

    ![Applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Predictix Price Reporting**.

    ![Elenco delle applicazioni](common/all-applications.png)

3. Nel riquadro sinistro selezionare **Utenti e gruppi**:

    ![Selezionare Utenti e gruppi](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Selezionare Aggiungi utente](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco di utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-predictix-price-reporting-test-user"></a>Creare un utente test di Predictix Price Reporting

A questo punto occorre creare un utente di nome Britta Simon in Predictix Price Reporting. Collaborare con il [team di supporto di Predictix Price Reporting](https://www.infor.com/company/customer-center/) per aggiungere utenti. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Il passaggio finale consiste nel testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Predictix Price Reporting nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza dell'applicazione Predictix Price Reporting per cui si è configurato l'accesso Single Sign-On. Per altre informazioni, vedere [Accedere e usare le app nel portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)