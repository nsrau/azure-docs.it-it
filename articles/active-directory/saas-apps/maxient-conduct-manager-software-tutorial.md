---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Maxient Conduct Manager Software | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Maxient Conduct Manager Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.openlocfilehash: dd6872fd8dca3f29b61c6f1dffb5f219abac5cb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88518892"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Maxient Conduct Manager Software

Questa esercitazione descrive come integrare Maxient Conduct Manager Software con Azure Active Directory (Azure AD). Integrando Maxient Conduct Manager Software con Azure AD, è possibile:

* Usare Azure AD per autenticare gli utenti per Maxient Conduct Manager Software.
* Abilitare gli utenti per l'accesso automatico a Maxient Conduct Manager Software con gli account Azure AD personali.


Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Maxient Conduct Manager Software abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione si configurerà Azure AD per l'uso con Maxient Conduct Manager Software.


* Maxient Conduct Manager Software supporta l'accesso SSO avviato da **SP e IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>Aggiunta di Maxient Conduct Manager Software dalla raccolta

Per configurare l'integrazione di Maxient Conduct Manager Software in Azure AD, è necessario aggiungere Maxient Conduct Manager Software dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Maxient Conduct Manager Software** nella casella di ricerca.
1. Selezionare **Maxient Conduct Manager Software** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Maxient Conduct Manager Software

Configurare e testare l'accesso SSO di Azure AD con Maxient Conduct Manager Software. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire un collegamento tra Azure AD e Maxient Conduct Manager Software.

Per configurare e testare l'accesso SSO di Azure AD con Maxient Conduct Manager Software, completare le procedure di base seguenti:

1. **[Configurare l'accesso SSO di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di eseguire l'autenticazione per l'uso con Maxient Conduct Manager Software.
    1. **[Assegnare tutti gli utenti per l'uso di Maxient](#assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software)** : per consentire a tutti gli utenti dell'organizzazione di eseguire l'autenticazione.
1. **[configurazione di Azure AD con Maxient](#test-with-maxient)** : per verificare il funzionamento della configurazione e il rilascio degli attributi corretti.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Maxient Conduct Manager Software** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'applicazione è preconfigurata in modalità avviata da **IDP** e gli URL necessari sono già prepopolati con Azure. L'utente deve salvare la configurazione facendo clic sul pulsante **Salva**. 

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://cm.maxient.com/<SCHOOLCODE>`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, rivolgersi al rappresentante dell'implementazione o del supporto di Maxient.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.  Sarà necessario fornire questo UEL al rappresentante dell'implementazione o del supporto tecnico di Maxient.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software"></a>Assegnare tutti gli utenti per consentire l'autenticazione per Maxient Conduct Manager Software

In questa sezione si concederà l'accesso a tutti gli account per consentire l'autenticazione per il software di gestione di Maxient tramite il sistema Azure.  È importante notare che questo passaggio è **OBBLIGATORIO** per il corretto funzionamento di Maxient.  Maxient sfrutta il sistema Azure AD per *autenticare* gli utenti. L'*autorizzazione* viene eseguita all'interno del sistema Maxient per la funzione specifica che gli utenti stanno provando a eseguire. Maxient non usa gli attributi dalla directory per prendere tali decisioni.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Maxient Conduct Manager Software**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare tutti gli utenti (o i gruppi appropriati) e **assegnarli** per consentirne l'autenticazione con Maxient.

## <a name="test-with-maxient"></a>Eseguire test con Maxient 

Se non è stato ancora aperto un ticket di supporto con un rappresentante dell'implementazione o del supporto Maxient, inviare un messaggio di posta elettronica a [support@maxient.com](mailto:support@maxient.com) con oggetto "Campus Based Authentication/Azure Setup - \<\<School Name\>\>". Nel corpo del messaggio di posta elettronica specificare l'**URL dei metadati di federazione dell'app**. Il personale di Maxient risponderà con un collegamento di test per verificare che gli attributi rilasciati siano corretti.  
    
## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Maxient Conduct Manager Software con Azure AD](https://aad.portal.azure.com/)

