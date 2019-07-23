---
title: Configurazione dell'accesso Single Sign-On (SSO) con un clic dell'applicazione di Azure Marketplace | Microsoft Docs
description: Procedura per la configurazione dell'accesso SSO con un clic per l'applicazione di Azure Marketplace.
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
ms.openlocfilehash: a83d27af4fd783b95c53ef3a9169cb72bfc29d34
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872434"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Configurazione dell'accesso Single Sign-On con un clic per l'app

 Questa esercitazione descrive come eseguire la configurazione dell'accesso Single Sign-On (SSO) con un clic per le applicazioni di Azure Active Directory (Azure AD) che supportano SAML da Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Introduzione all'accesso SSO con un clic

La funzionalità di accesso SSO con un clic è progettata in modo da consentire la configurazione dell'accesso Single Sign-On per le app di Azure Marketplace che supportano il protocollo SAML. Nella pagina di configurazione dell'accesso SSO di Azure AD questa opzione permette di configurare automaticamente i metadati di Azure AD sul lato applicazione. In questo modo è possibile configurare rapidamente l'accesso SSO con poche operazioni manuali.

## <a name="advantages-of-one-click-sso"></a>Vantaggi dell'accesso SSO con un clic

- Configurazione rapida dell'accesso SSO per le applicazioni di Azure Marketplace che richiedono l'installazione manuale sul lato applicazione.
- Configurazione dell'accesso SSO più efficiente e accurata.
- Nessuna necessità di comunicare con il partner o l'assistenza per completare la configurazione. Interfaccia utente per la configurazione SAML inclusa nell'applicazione.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione attiva per l'applicazione da configurare con l'accesso SSO. Sono necessarie anche le credenziali dell'amministratore.
- **Estensione del browser My Apps Secure Sign-in** Microsoft installata nel browser. Per altre informazioni, vedere [Accedere e usare le app nel portale App personali](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-configuration-steps"></a>Procedura per la configurazione dell'accesso SSO con un clic

1. Aggiungere l'applicazione da Azure Marketplace.

2. Selezionare **Single Sign-On**.

3. Selezionare **Abilita Single Sign-On**.

4. Inserire i valori di configurazione obbligatori nella sezione **Configurazione SAML di base**.

    > [!NOTE]
    > Se per l'applicazione sono disponibili attestazioni personalizzate che è necessario configurare, gestirle prima di eseguire l'accesso SSO con un clic.

5. Se la funzionalità di accesso SSO con un clic è disponibile per l'applicazione di Azure Marketplace, viene visualizzata la schermata seguente. Potrebbe essere necessario installare l'**estensione del browser My Apps Secure Sign-in** selezionando **Installa estensione**.

   ![Installare l'estensione del browser per l'accesso sicuro alle app personali](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Dopo aver aggiunto l'estensione al browser, selezionare **Configura \<nome applicazione\>** . Dopo essere stati reindirizzati al portale di amministrazione dell'applicazione, accedere come amministratore.

   ![Configura <Nome applicazione>](./media/one-click-sso-tutorial/setup-sso.png)

7. L'estensione del browser configura automaticamente l'accesso SSO nell'applicazione. Per confermare, selezionare **Sì**.

   ![Salvataggio dei dati popolati automaticamente](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Se la configurazione per l'accesso SSO dell'applicazione richiede passaggi aggiuntivi, seguire le istruzioni visualizzate per completarli.

8. Al termine della configurazione, selezionare **OK** per salvare le modifiche.

   ![Salvare i dati popolati automaticamente](./media/one-click-sso-tutorial/save-data.png)

9. Viene visualizzata una finestra di conferma per informare che le impostazioni per l'accesso SSO sono state configurate correttamente.

   ![Accesso SSO configurato](./media/one-click-sso-tutorial/sso-configured.png)

10. Al termine della configurazione, si verrà disconnessi dall'applicazione e verrà visualizzato di nuovo il portale di Azure.

11. È possibile selezionare **Test** per testare l'accesso Single Sign-on.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Informazioni sull'estensione del browser My Apps Secure Sign-in](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
