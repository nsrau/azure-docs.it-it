---
title: 'Esercitazione: configurazione di Google Apps per il provisioning utenti automatico in Azure | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Google Apps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b061f0ddad70be4a5ca48d48d1a737d6af8afa8d
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="tutorial-configuring-google-apps-for-automatic-user-provisioning"></a>Esercitazione: configurazione di Google Apps per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Google Apps e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Google Apps.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   È necessario disporre di un tenant valido per Google Apps for Work o Google Apps for Education. È possibile usare un account della versione di valutazione gratuita per entrambi i servizi.
*   Account utente in Google Apps con autorizzazioni di amministratore di team.

## <a name="assigning-users-to-google-apps"></a>Assegnazione di utenti a Google Apps

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app Google Apps. Dopo aver deciso, è possibile assegnare questi utenti all'app Google Apps seguendo le istruzioni riportate di seguito: [Assegnare un utente o un gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

> [!IMPORTANT]
>*   È consigliabile assegnare un singolo utente di Azure AD a Google Apps per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.
>*   Quando si assegna un utente a Google Apps, è necessario selezionare il ruolo Utente o "Gruppo" nella finestra di dialogo di assegnazione. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="enable-automated-user-provisioning"></a>Abilitare il provisioning utenti automatico

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di Google Apps e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in Google Apps in base all'assegnazione di utenti e gruppi in Azure AD.

>[!Tip]
>Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Google Apps, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="configure-automatic-user-account-provisioning"></a>Configurare il provisioning automatico degli account utente

> [!NOTE]
> Un'altra opzione valida per automatizzare il provisioning dell’utente in Google Apps consiste nell'utilizzare [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en) che fornisce le identità di Active Directory locale in Google Apps. Al contrario, la soluzione in questa esercitazione esegue il provisioning per gli utenti e i gruppi abilitati alla posta elettronica in Google Apps Azure Active Directory (cloud). 

1. Accedere alla [console di amministrazione di Google Apps](http://admin.google.com/) con l'account amministratore e fare clic su **Security**. Se non viene visualizzato il collegamento, può essere nascosto sotto il menu **More Controls** nella parte inferiore della schermata.
   
    ![Fare clic su sicurezza.][10]

2. Nella pagina **Security** (Sicurezza) fare clic su **API Reference** (Riferimento API).
   
    ![Fare clic su Informazioni di riferimento sulle API.][15]

3. Selezionare **Enable API access**.
   
    ![Fare clic su Informazioni di riferimento sulle API.][16]

    > [!IMPORTANT]
    > Per ogni utente per cui si intende eseguire il provisioning in Google Apps, si *deve* associare il relativo nome utente in Azure Active Directory a un dominio personalizzato. Ad esempio, nomi utente simili a bob@contoso.onmicrosoft.com non verranno accettati da Google Apps, mentre bob@contoso.com verranno accettati. È possibile modificare il dominio di un utente esistente modificandone le proprietà in Azure AD. Le istruzioni su come impostare un dominio personalizzato per Azure Active Directory e Google Apps vengono illustrate nella procedura seguente.
      
4. Se ancora non è stato aggiunto un nome di dominio personalizzato ad Azure Active Directory, attenersi alla procedura seguente:
  
    a. Nel [portale di Azure ](https://portal.azure.com)fare clic su **Active Directory** nel riquadro di spostamento sinistro. Nell'elenco visualizzato, selezionare la directory. 

    b. Fare clic su **Nome di dominio** nel riquadro di spostamento a sinistra e quindi fare clic su **Aggiungi**.
     
     ![dominio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_1.png)

     ![aggiunta del dominio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_2.png)

    c. Digitare il nome di dominio nel campo **Nome di dominio** . Questo deve essere lo stesso nome di dominio che si intende usare per Google Apps. Quando si è pronti, fare clic sul pulsante **Aggiungi dominio**.
     
     ![nome di dominio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_3.png)

    d. Fare clic su **Avanti** per passare alla pagina di verifica. Per verificare che si è proprietari di tale dominio, è necessario modificare i record DNS del dominio in base ai valori forniti in questa pagina. È possibile verificare tramite **record MX** o **record TXT** a seconda di ciò che viene selezionato per l'opzione **Tipo di record**. Per istruzioni più dettagliate su come verificare il nome di dominio con Azure AD, vedere [Aggiungere il proprio nome di dominio ad Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
     ![dominio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_4.png)

    e. Ripetere i passaggi precedenti per tutti i domini che si desidera aggiungere alla directory.

5. Ora che tutti i domini sono stati verificati con Azure AD, è necessario verificarli nuovamente con Google Apps. Per ogni dominio che non sia già registrato con Google Apps, procedere come segue:
   
    a. Nella [console di amministrazione di Google Apps](http://admin.google.com/) fare clic su **Domains** (Domini).
     
     ![Fare clic su Domini][20]

    b. Fare clic su **Add a domain or a domain alias**.
     
     ![Aggiungere un nuovo dominio][21]

    c. Selezionare **Add another domain**e digitare il nome del dominio che si desidera aggiungere.
     
     ![Digitare il nome di dominio][22]

    d. Fare clic su **Continue and verify domain ownership** (Continua e verifica la proprietà del dominio). Seguire i passaggi per verificare che si è proprietari del nome di dominio. Per istruzioni dettagliate su come verificare il dominio con Google Apps, vedere [Verificare la proprietà del sito con Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Ripetere i passaggi precedenti per tutti i domini aggiuntivi che si desidera aggiungere a Google Apps.
     
     > [!WARNING]
     > Se si modifica il dominio primario per il tenant di Google Apps e se è già stato configurato Single Sign-On con Azure AD, sarà necessario ripetere il passaggio 3 in [Passaggio 2: Abilitare il servizio Single Sign-On](#step-two-enable-single-sign-on).
       
6. Nella [console di amministrazione di Google Apps](http://admin.google.com/) fare clic su **Admin Roles** (Ruoli amministratore).
   
     ![Fare clic su Google Apps][26]

7. Determinare quale account di amministratore si desidera utilizzare per gestire il provisioning dell'utente. Per il **ruolo di amministratore** di tale account, modificare i **privilegi** per tale ruolo. Assicurarsi che tutti i **privilegi dell'API di amministratore** siano abilitati in modo che questo account possa essere usato per il provisioning.
   
     ![Fare clic su Google Apps][27]
   
    > [!NOTE]
    > Se si sta configurando un ambiente di produzione, la procedura consigliata consiste nel creare un nuovo account di amministratore in Google Apps specificatamente per questo passaggio. Questi account devono disporre di un ruolo amministratore associato che abbia i privilegi API necessari.
     
8. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

9. Se Google Apps è già stato configurato per l'accesso Single Sign-On, cercare l'istanza di Google Apps usando il campo di ricerca. Altrimenti selezionare **Aggiungi** e cercare **Google Apps** nella raccolta di applicazioni. Selezionare Google Apps nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

10. Selezionare l'istanza di Google Apps e quindi la scheda **Provisioning**.

11. Impostare **Modalità di provisioning** su **Automatico**. 

     ![provisioning](./media/active-directory-saas-google-apps-provisioning-tutorial/provisioning.png)

12. Nella sezione **Credenziali amministratore** fare clic su **Autorizza**. Verrà aperta una finestra di dialogo di autorizzazione di Google Apps in una nuova finestra del browser.

13. Confermare che si desidera concedere l'autorizzazione Azure Active Directory per apportare modifiche al tenant di Google Apps. Fare clic **Accept**.
    
     ![Verificare le autorizzazioni.][28]

14. Nel portale di Azure fare clic su **Verifica connessione** per verificare che Azure AD possa connettersi all'app Google Apps. Se la connessione non riesce, verificare che l'account Google Apps abbia autorizzazioni di amministratore di team e ripetere il passaggio **"Authorize"** (Autorizza).

15. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo.

16. Fare clic su **Salva**.

17. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Google Apps** (Sincronizza utenti di Azure Active Directory in Google Apps).

18. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Google Apps. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Google Apps per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

19. Per abilitare il servizio di provisioning di Azure AD per Google Apps, impostare **Stato del provisioning** su **Attivato** nella sezione Impostazioni

20. Fare clic su **Salva**.

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a Google Apps nella sezione Utenti e gruppi. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app Google Apps.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configura accesso Single Sign-On](active-directory-saas-google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-auth.png
