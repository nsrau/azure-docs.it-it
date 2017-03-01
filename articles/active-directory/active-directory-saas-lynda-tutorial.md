---
title: 'Esercitazione: integrazione di Azure Active Directory con Lynda.com | Microsoft Docs'
description: Informazioni su come utilizzare Lynda.com con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f6c92789-8b64-4049-bac9-8cb928398433
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07b9df5ab5c7df0089f001a26200bb9c3fb1c508
ms.openlocfilehash: 7f4f1c22e0b3382a9de770800ccb733c61d4f5b0
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>Esercitazione: Integrazione di Azure Active Directory con Lynda.com
In questa esercitazione viene illustrata l'integrazione di Azure e Lynda.com.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant Lynda.com

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Lynda.com saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Lynda.com (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Lynda.com
2. Configurazione dell'accesso Single Sign-On (SSO)
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-lynda-tutorial/IC781046.png "Scenario")

## <a name="enabling-the-application-integration-for-lyndacom"></a>Abilitazione dell'integrazione dell'applicazione per Lynda.com
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Lynda.com.

**Per abilitare l'integrazione dell'applicazione per Lynda.com, eseguire la procedura seguente:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-lynda-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-lynda-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-lynda-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Lynda.com**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-lynda-tutorial/IC777524.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Lynda.com**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Lynda.com](./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
   
## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Lynda.com tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.

>[!IMPORTANT]
>Per poter configurare l’accesso Single Sign-On nel tenant di Lynda.com è necessario prima contattare il supporto tecnico di Lynda.com per abilitare questa funzionalità. 
> 

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Lynda.com** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-lynda-tutorial/IC777526.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Lynda.com** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-lynda-tutorial/IC777527.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL App**, nella casella di testo **URL di accesso a Lynda.com**, digitare l'URL del tenant Lynda.com (ad esempio: *https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target=https://shib.lynda.com/InCommon*), quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-lynda-tutorial/IC781047.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Lynda.com** fare clic su **Scarica metadati** per scaricare i metadati, quindi salvare il file di certificato localmente nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-lynda-tutorial/IC777529.png "Configurare l'accesso Single Sign-On")
5. Inviare il file di metadati scaricato al team di supporto di Lynda.com. Il team di supporto di Lynda.com esegue la configurazione dell’accesso Single Sign-On per l'utente.
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-lynda-tutorial/IC777530.png "Configurare l'accesso Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Non è richiesta alcuna operazione per configurare il provisioning degli utenti in Lynda.com.  
Quando un utente assegnato tenta di accedere a Lynda.com utilizzando il pannello di accesso, Lynda.com controlla se l'utente esiste.  

Se l’account utente non è ancora disponibile, viene creato automaticamente da Lynda.com.

>[!NOTE]
>È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Lynda.com o le API fornite da Lynda.com per effettuare il provisioning degli account utente di AAD. 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Lynda.com eseguire la procedura seguente:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Lynda.com** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-lynda-tutorial/IC777531.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-lynda-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


