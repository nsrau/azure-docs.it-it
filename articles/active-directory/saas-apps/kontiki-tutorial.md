---
title: 'Esercitazione: integrazione di Azure Active Directory con Kontiki | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Kontiki.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bee7454942b9214eeb1253339446df370e20fe01
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785843"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Esercitazione: integrazione di Azure Active Directory con Kontiki

Questa esercitazione descrive come integrare Kontiki con Azure Active Directory (Azure AD).

L'integrazione di Kontiki con Azure AD offre i vantaggi seguenti:

* È possibile usare Azure AD per controllare chi può accedere a Kontiki.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Kontiki con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Kontiki sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di una sottoscrizione di Azure AD, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Una sottoscrizione a Kontiki con l'accesso Single Sign-On abilitato.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test e viene integrato Kontiki con Azure AD.

Uberflip supporta le funzionalità seguenti:

* **Accesso Single Sign-On avviato da SP**
* **Provisioning JIT**

## <a name="add-kontiki-in-the-azure-portal"></a>Aggiungere Kontiki al portale di Azure

Per integrare Kontiki con Azure AD, è necessario aggiungerlo all'elenco delle app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu a sinistra, selezionare **Azure Active Directory**.

    ![Opzione Azure Active Directory](common/select-azuread.png)

1. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Per aggiungere un'applicazione, selezionare **Nuova applicazione**.

    ![Opzione Applicazione nuova](common/add-new-app.png)

1. Nella casella di ricerca immettere **Kontiki**. Nei risultati della ricerca, selezionare **Kontiki**, quindi **Aggiungi**.

    ![Kontiki nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Kontiki mediante un utente test di nome **Britta Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Kontiki.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Kontiki è necessario completare i blocchi predefiniti seguenti:

| Attività | DESCRIZIONE |
| --- | --- |
| **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** | Consente agli utenti di usare questa funzionalità. |
| **[Configurare l'accesso Single Sign-On di Kontiki](#configure-kontiki-single-sign-on)** | Consente di configurare le impostazioni di accesso Single Sign-On nell'applicazione. |
| **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** | Consente di verificare l'accesso Single Sign-On di Azure AD per un utente chiamato Britta Simon. |
| **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** | Consente a Britta Simon di usare l'accesso Single Sign-On di Azure AD. |
| **[Creare un utente test di Kontiki](#create-a-kontiki-test-user)** | Consente di creare una controparte di Britta Simon in Kontiki collegata alla rappresentazione dell'utente in Azure AD. |
| **[Testare l'accesso Single Sign-On](#test-single-sign-on)** | Verifica se la configurazione funziona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione si configura l'accesso Single Sign-On di Azure AD con Kontiki nel portale di Azure.

1. Nel riquadro di integrazione dell'applicazione **Kontiki** del [portale di Azure](https://portal.azure.com/) selezionare **Single sign-on**.

    ![Configurare l'opzione Single Sign-On](common/select-sso.png)

1. Nel riquadro **Selezionare un metodo di accesso Single Sign-On**, selezionare la modalità **SAML** o **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

1. Nel riquadro **Configura l'accesso Single Sign-On con SAML**, selezionare **Modifica** (l'icona a forma di matita) per aprire il riquadro **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella casella di testo **URL di accesso** del riquadro **Configurazione SAML di base** immettere un URL con il formato seguente: `https://<companyname>.mc.eval.kontiki.com`

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Kontiki](common/sp-signonurl.png)

    > [!NOTE]
    > Per ottenere il valore corretto, contattare il [team di supporto clienti di Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nel riquadro **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, selezionare **Scarica** accanto a **XML metadati federazione**. Selezionare un'opzione di download in base alle esigenze. Salvare il certificato nel computer.

    ![Opzione per il download del certificato XML dei metadati federazione](common/metadataxml.png)

1. Nella sezione **Configura Kontiki**, copiare gli URL seguenti in base alle esigenze:

    * URL di accesso
    * Identificatore di Azure AD
    * URL di chiusura sessione

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Configurare l'accesso Single Sign-On di Kontiki

Per configurare l'accesso Single Sign-On sul lato Kontiki, inviare il file di XML metadati federazione scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html). Il team di supporto di Kontiki usa le informazioni inviate per assicurarsi che la connessione Single Sign-On SAML venga impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

In questa sezione viene creato un utente di test chiamato Britta Simon nel portale di Azure.

1. Nel portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.

    ![Opzioni Utenti e Tutti gli utenti](common/users.png)

1. Selezionare **Nuovo utente**.

    ![Opzione Nuovo utente](common/new-user.png)

1. Nel riquadro **Utente** completare la procedura seguente:

    1. Nella casella **Nome** immettere **BrittaSimon**.
  
    1. Nella casella **Nome utente**, immettere **brittasimon\@\<dominio-azienda>.\<estensione>** . Ad esempio, **brittasimon\@contoso.com**.

    1. Selezionare la casella di controllo **Mostra password**. Prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create**.

    ![Riquadro Utente](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si concede a Britta Simon l'accesso a Kontiki in modo che possa usare l'accesso Single Sign-On di Azure.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **Kontiki**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **Kontiki**.

    ![Kontiki nell'elenco delle applicazioni](common/all-applications.png)

1. Nel menu selezionare **Utenti e gruppi**.

    ![Opzione Utenti e gruppi](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente**. Nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

1. Nel riquadro **Utenti e gruppi**, selezionare **Britta Simon** nell'elenco degli utenti. Scegliere **Seleziona**.

1. Se si prevede un valore di ruolo nell'asserzione SAML, nel riquadro **Seleziona ruolo**, selezionare il ruolo dell'utente dall'elenco. Scegliere **Seleziona**.

1. Nel riquadro **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-kontiki-test-user"></a>Creare un utente test di Kontiki

Non è necessaria alcuna attività di configurazione per il provisioning degli utenti in Kontiki. Quando un utente assegnato prova ad accedere a Kontiki usando il portale App personali, Kontiki verifica se l'utente esiste. Se non si trova nessun account utente, Kontiki lo crea automaticamente.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD tramite il portale App personali.

Dopo aver configurato l'accesso Single Sign-On, quando si seleziona **Kontiki** nel portale App personali, viene eseguito automaticamente l'accesso a Kontiki. Per altre informazioni sul portale App personali, vedere [Accedere e usare le app nel portale App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere questi articoli:

- [Elenco di esercitazioni per l'integrazione di app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
