---
title: Configurare l'accesso SSO con un clic a un'applicazione della raccolta di app di Azure AD | Microsoft Docs
description: Passaggi per configurare l'accesso SSO con un clic a un'applicazione della raccolta di app di Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 358240823da469551e254356fc0613bea20d78c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064815"
---
# <a name="one-click-sso-feature-for-azure-ad-gallery-applications"></a>Funzionalità SSO con un clic per le applicazioni della raccolta di Azure AD

 In questa esercitazione viene descritto come eseguire l'accesso SSO con un clic per tutte le applicazioni SAML che forniscono l'interfaccia utente per la configurazione di SSO.

## <a name="introduction-to-one-click-sso"></a>Introduzione alla funzionalità SSO con un clic

La funzionalità SSO con un clic è stata introdotta per configurare l'accesso Single Sign-On per le app della raccolta di Azure AD che supportano il protocollo SAML. Nella pagina di configurazione SSO di Azure AD è stata resa disponibile questa opzione per consentire ai clienti di configurare automaticamente i metadati di Azure AD sul lato applicazione. L'obiettivo è quello di consentire ai clienti di configurare la funzionalità SSO rapidamente con un intervento manuale minimo. 

## <a name="advantages-of-the-one-click-sso"></a>Vantaggi della funzionalità SSO con un clic

- Configurazione rapida della funzionalità SSO per le applicazioni della raccolta in cui i clienti devono eseguire la configurazione manuale sul lato applicazione.
- Configurazione più efficace e accurata.
- Nessuna necessità di comunicazione con i partner o assistenza per la configurazione in quanto l'applicazione fornisce l'interfaccia utente per la configurazione SAML.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione attiva con le credenziali di amministratore dell'applicazione da configurare con la funzionalità SSO con un clic.
- **Estensione per l'accesso sicuro alle app personali** Microsoft installata nel browser. Per altre informazioni su questa estensione, vedere questo [collegamento](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-feature-step-by-step-details"></a>Procedura dettagliata per la funzionalità SSO con un clic

1. Aggiungere l'applicazione dalla raccolta di app di Azure AD.

2. Fare clic su Single Sign-On.

3. Fare clic su Abilita Single Sign-On.

4. Popolare i valori di configurazione obbligatori nella sezione Configurazione SAML di base.

    > [!NOTE] 
    > Se l'applicazione richiede la configurazione di attestazioni personalizzate, configurarle prima di eseguire la funzionalità SSO con un clic.

5. Se la funzionalità SSO con un clic viene implementata per un'applicazione della raccolta, verrà visualizzata la schermata seguente. Se non è ancora stata eseguita l'installazione dell'**estensione del browser per l'accesso sicuro alle app personali**, fare clic su **Installa l'estensione**.

    ![Installare l'estensione del browser per l'accesso sicuro alle app personali](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura <Nome applicazione>** per passare al portale di amministrazione dell'applicazione. È necessario effettuare l'accesso come amministratore per accedere all'applicazione.

    ![Configura <Nome applicazione>](./media/one-click-sso-tutorial/setup-sso.png)

7. L'estensione del browser configurerà automaticamente l'applicazione. Verrà prima di tutto chiesto di confermare se si vuole procedere. Fare clic su **Sì**.

    ![Salvataggio dei dati popolati automaticamente](./media/one-click-sso-tutorial/save-autopopulate.png)

    > [!NOTE]
    > Se ci sono applicazioni che richiedono passaggi o comandi di spostamento aggiuntivi, dovrebbero venire visualizzati i messaggi appropriati in cui viene richiesto di eseguire questi passaggi. 

8. Una volta completata la configurazione, fare clic su **OK** per salvare le modifiche.

    ![Salvare i dati popolati automaticamente](./media/one-click-sso-tutorial/save-data.png)

9. Viene visualizzato un messaggio popup di conferma di esito positivo e le impostazioni SSO sono configurate correttamente. È quindi possibile testare l'applicazione.

    ![Accesso SSO configurato](./media/one-click-sso-tutorial/sso-configured.png)

10. Una volta completata la configurazione, l'applicazione verrà disconnessa e verrà visualizzato di nuovo il portale di Azure.

11. È possibile fare clic sul pulsante Test per testare l'accesso Single Sign-On.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Informazioni sull'estensione del browser per l'accesso sicuro alle app personali](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 