---
title: 'Esercitazione: Integrazione di Azure Active Directory con Egnyte | Documentazione Microsoft'
description: Informazioni su come usare Egnyte con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 682fa3f4ce1387f20b065af88b54c3cde3f2b242


---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Esercitazione: Integrazione di Azure Active Directory con Egnyte
Questa esercitazione descrive l'integrazione di Azure ed Egnyte.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Egnyte abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Egnyte potranno accedere all'applicazione tramite il sito aziendale di Egnyte (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Egnyte
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-egnyte-tutorial/IC787812.png "Scenario")

## <a name="enabling-the-application-integration-for-egnyte"></a>Abilitazione dell'integrazione dell'applicazione per Egnyte
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Egnyte.

### <a name="to-enable-the-application-integration-for-egnyte-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Egnyte, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-egnyte-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-egnyte-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-egnyte-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-egnyte-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **egnyte**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-egnyte-tutorial/IC787813.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Egnyte** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![egnyte](./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Egnyte tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
Per eseguire questa procedura, è necessario creare un certificato con codifica Base 64.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Egnyte** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-egnyte-tutorial/IC787815.png "Configure Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Egnyte** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-egnyte-tutorial/IC787816.png "Configure Single Sign-On")
3. Nella casella di testo **URL accesso Egnyte** della pagina **Configura URL app** digitare l'URL usando il modello "*https://company.egnyte.com*" e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-egnyte-tutorial/IC787817.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in Egnyte** fare clic su **Scarica certificato** e quindi salvare il file di certificato nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-egnyte-tutorial/IC787818.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Egnyte come amministratore.
6. Fare clic su **Settings**.
   
   ![Settings](./media/active-directory-saas-egnyte-tutorial/IC787819.png "Settings")
7. Scegliere **Settings**dal menu.
   
   ![Impostazioni](./media/active-directory-saas-egnyte-tutorial/IC787820.png "Settings")
8. Fare clic sulla scheda **Configuration** (Configurazione) e quindi fare clic su **Security** (Sicurezza).
   
   ![Sicurezza](./media/active-directory-saas-egnyte-tutorial/IC787821.png "Security")
9. Nella sezione **Single Sign-On Authentication** seguire questa procedura:
   
   ![Single Sign On Authentication](./media/active-directory-saas-egnyte-tutorial/IC787822.png "Single Sign On Authentication")
   
   1. In **Single sign-on authentication** (Autenticazione Single Sign-On) selezionare **SAML 2.0**.
   2. In **Identity provider** (Provider di identità) selezionare **AzureAD**.
   3. Nella pagina **Configura accesso Single Sign-On in Egnyte** del portale di Azure classico copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **Identity provider login URL ** (URL di accesso provider di identità).
   4. Nella finestra di dialogo **Configura accesso Single Sign-On in Egnyte** del portale di Azure classico copiare il valore di **ID entità** e quindi incollarlo nella casella di testo **Identity provider entity ID** (ID entità provider di identità).
   5. Creare un file **con codifica Base 64** dal certificato scaricato.  
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   6. Aprire il certificato con codifica base 64 nel blocco note, copiarne il contenuto negli appunti e quindi incollarlo nella casella di testo **Identity provider certificate** .
   7. In **Default user mapping** (Mapping utente predefinito) selezionare **Email address** (Indirizzo posta elettronica).
   8. In **Use domain-specific issuer value** (Usa valore autorità emittente specifica del dominio) selezionare **disabled** (disabilitato).
   9. Fare clic su **Save**.
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-egnyte-tutorial/IC787823.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Egnyte, è necessario eseguirne il provisioning in Egnyte.  
Nel caso di Egnyte, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:
1. Accedere al sito aziendale di **Egnyte** come amministratore.
2. Passare a **Settings (Impostazioni) \> Users & Groups (Utenti e gruppi)**.
3. Fare clic su **Add New User**e quindi selezionare il tipo di utente da aggiungere.
   
   ![Utenti](./media/active-directory-saas-egnyte-tutorial/IC787824.png "Users")
4. Nella sezione **New Standard User** seguire questa procedura:
   
   ![New Standard User](./media/active-directory-saas-egnyte-tutorial/IC787825.png "New Standard User")
   
   1. Immettere i valori **Email** (Posta elettronica), **Username** (Nome utente) e altri dettagli di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
   2. Fare clic su **Save**.
   
   > [!NOTE]
   > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica di notifica.
   > 
   > 

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Egnyte per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-egnyte-perform-the-following-steps"></a>Per assegnare gli utenti a Egnyte, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Egnyte ** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-egnyte-tutorial/IC787826.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-egnyte-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


