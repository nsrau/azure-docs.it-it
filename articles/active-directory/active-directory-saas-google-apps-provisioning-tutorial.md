---
title: 'Esercitazione: Configurare Google Apps per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Google Apps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 0a0dd38fda35b1eee1fdce196a3ed15f6e73dde6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-google-apps-for-automatic-user-provisioning"></a>Esercitazione: Configurare Google Apps per il provisioning utenti automatico

Questa esercitazione descrive come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure Active Directory (Azure AD) a Google Apps.

## <a name="prerequisites"></a>prerequisiti

Nello scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   Un tenant valido per Google Apps for Work o Google Apps for Education. È possibile usare un account della versione di valutazione gratuita per entrambi i servizi.
*   Account utente in Google Apps con autorizzazioni di amministratore di team.

## <a name="assign-users-to-google-apps"></a>Assegnare utenti a Google Apps

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti o gruppi in Azure AD devono accedere all'app Google Apps. Dopo aver deciso, è possibile assegnare questi utenti all'app Google Apps seguendo le istruzioni riportate in [Assegnare un utente o un gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> È consigliabile assegnare un singolo utente di Azure AD a Google Apps per testare la configurazione del provisioning. Sarà possibile assegnare altri utenti e gruppi in un secondo momento.

> Quando si assegna un utente a Google Apps, selezionare il ruolo **Utente** o **Gruppo** nella finestra di dialogo di assegnazione. Il ruolo **Accesso predefinito** non è applicabile per il provisioning.

## <a name="enable-automated-user-provisioning"></a>Abilitare il provisioning utenti automatico

Questa sezione illustra il processo di connessione di Azure AD all'API di provisioning degli account utente di Google Apps. Consente inoltre di configurare il servizio di provisioning per creare, aggiornare e disabilitare gli account utente assegnati in Google Apps in base all'assegnazione di utenti e gruppi in Azure AD.

>[!TIP]
>Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per Google Apps, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="configure-automatic-user-account-provisioning"></a>Configurare il provisioning automatico degli account utente

> [!NOTE]
> Un'altra soluzione valida per l'automazione del provisioning utenti in Google Apps consiste nell'usare [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en). GADS esegue il provisioning in Google Apps delle identità locali di Active Directory. Al contrario, la soluzione in questa esercitazione esegue il provisioning in Google Apps dei gruppi abilitati alla posta elettronica e degli utenti (cloud) di Azure Active Directory. 

1. Accedere alla [console di amministrazione di Google Apps](http://admin.google.com/) con l'account amministratore e selezionare **Security** (Sicurezza). Se il collegamento non è visibile, è possibile che sia nascosto sotto il menu **More Controls** (Altri controlli) nella parte inferiore della schermata.
   
    ![Selezionare Security (Sicurezza).][10]

2. Nella pagina **Security** (Sicurezza) fare clic su **API Reference** (Riferimento API).
   
    ![Selezionare API Reference (Riferimento API).][15]

3. Selezionare **Enable API access**.
   
    ![Selezionare API Reference (Riferimento API).][16]

    > [!IMPORTANT]
    > Per ogni utente per cui si intende eseguire il provisioning in Google Apps, si *deve* associare il relativo nome utente in Azure Active Directory a un dominio personalizzato. Ad esempio, nomi utente simili a bob@contoso.onmicrosoft.com non vengono accettati da Google Apps, mentre bob@contoso.com viene accettato. È possibile modificare il dominio di un utente esistente modificandone le proprietà in Azure AD. Nella procedura seguente sono illustrate le istruzioni su come impostare un dominio personalizzato per Azure Active Directory e Google Apps.
      
4. Se non si è ancora aggiunto un nome di dominio personalizzato ad Azure Active Directory, seguire questa procedura:
  
    a. Nel [portale di Azure](https://portal.azure.com) selezionare **Active Directory** nel riquadro di spostamento a sinistra. Nell'elenco visualizzato, selezionare la directory. 

    b. Selezionare **Nome di dominio** nel riquadro di spostamento a sinistra e quindi fare clic su **Aggiungi**.
     
     ![Domain](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_1.png)

     ![Aggiunta del dominio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_2.png)

    c. Digitare il nome di dominio nel campo **Nome di dominio** . Questo deve essere lo stesso nome di dominio che si intende usare per Google Apps. Selezionare quindi il pulsante **Aggiungi dominio**.
     
     ![Nome di dominio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_3.png)

    d. Selezionare **Avanti** per passare alla pagina di verifica. Per verificare che si è proprietari di questo dominio, modificare i record DNS del dominio in base ai valori riportati in questa pagina. È possibile eseguire la verifica usando **record MX** o **record TXT**, a seconda dell'opzione selezionata come **Tipo di record**. 
    
    Per istruzioni più dettagliate su come verificare i nomi di dominio con Azure AD, vedere [Aggiungere il proprio nome di dominio ad Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
     ![Domain](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_4.png)

    e. Ripetere i passaggi precedenti per tutti i domini che si desidera aggiungere alla directory.

    > [!NOTE]
    Per il provisioning utenti, il dominio personalizzato Google Apps deve corrispondere al nome di dominio di Azure AD di origine. Se non corrispondono, per risolvere il problema può essere utile implementare la personalizzazione del mapping degli attributi.


5. Ora che tutti i domini sono stati verificati con Azure AD, è necessario verificarli nuovamente con Google Apps. Per ogni dominio non ancora registrato con Google Apps, procedere come segue:
   
    a. Nella [console di amministrazione di Google Apps](http://admin.google.com/) selezionare **Domains** (Domini).
     
     ![Selezionare i domini][20]

    b. Selezionare **Add a domain or a domain alias** (Aggiungi un dominio o un alias di dominio).
     
     ![Aggiungere un nuovo dominio][21]

    c. Selezionare **Add another domain** (Aggiungi un altro dominio) e quindi digitare il nome del dominio che si vuole aggiungere.
     
     ![Digitare il nome di dominio][22]

    d. Selezionare **Continue and verify domain ownership** (Continua e verifica la proprietà del dominio). Seguire i passaggi per verificare che si è proprietari del nome di dominio. Per istruzioni dettagliate su come verificare il dominio con Google Apps, vedere [Verificare il proprietario del sito con Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Ripetere i passaggi precedenti per tutti i domini aggiuntivi che si desidera aggiungere a Google Apps.
     
     > [!WARNING]
     > Se si modifica il dominio primario per il tenant di Google Apps e si è già configurato l'accesso Single Sign-On con Azure AD, è necessario ripetere il passaggio 3 in [Passaggio 2: Abilitare il servizio Single Sign-On](#step-two-enable-single-sign-on).
       
6. Nella [console di amministrazione di Google Apps](http://admin.google.com/) selezionare **Admin Roles** (Ruoli amministratore).
   
     ![Selezionare Google Apps][26]

7. Determinare l'account di amministratore da usare per gestire il provisioning utenti. Per il **ruolo di amministratore** di tale account, modificare i **privilegi** per tale ruolo. Assicurarsi di abilitare tutti i **privilegi dell'API di amministratore** in modo che l'account possa essere usato per il provisioning.
   
     ![Selezionare Google Apps][27]
   
    > [!NOTE]
    > Se si sta configurando un ambiente di produzione, la procedura consigliata consiste nel creare un nuovo account di amministratore in Google Apps specificatamente per questo passaggio. Questi account devono avere un ruolo di amministratore associato che abbia i privilegi API necessari.
     
8. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory** > **App aziendali** > **Tutte le applicazioni**.

9. Se Google Apps è già stato configurato per l'accesso Single Sign-On, cercare l'istanza di Google Apps usando il campo di ricerca. In alternativa, selezionare **Aggiungi** e cercare **Google Apps** nella raccolta delle applicazioni. Selezionare **Google Apps** nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

10. Selezionare l'istanza di Google Apps e quindi la scheda **Provisioning**.

11. Impostare **Modalità di provisioning** su **Automatico**. 

     ![Provisioning](./media/active-directory-saas-google-apps-provisioning-tutorial/provisioning.png)

12. Nella sezione **Credenziali amministratore** selezionare **Autorizza**. Verrà aperta una finestra di dialogo di autorizzazione di Google Apps in una nuova finestra del browser.

13. Confermare che si vuole concedere l'autorizzazione di Azure Active Directory per apportare modifiche al tenant di Google Apps. Selezionare **Accetto**.
    
     ![Verificare le autorizzazioni.][28]

14. Nel portale di Azure selezionare **Test connessione** per verificare che Azure AD possa connettersi all'app Google Apps. Se la connessione non riesce, verificare che l'account di Google Apps disponga delle autorizzazioni di amministratore di team. Ripetere quindi il passaggio per l'**autorizzazione**.

15. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica**. Selezionare quindi la casella di controllo.

16. Selezionare **Salva**.

17. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Google Apps** (Sincronizza utenti di Azure Active Directory in Google Apps).

18. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Google Apps. Gli attributi che risultano proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Google Apps per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

19. Per abilitare il servizio di provisioning di Azure AD per Google Apps, impostare **Stato del provisioning** su **Attivato** in **Impostazioni**.

20. Selezionare **Salva**.

Questo processo avvia la sincronizzazione iniziale di tutti gli utenti o i gruppi assegnati a Google Apps nella sezione Utenti e gruppi. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti mentre il servizio è in esecuzione. 

È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning. Questi report descrivono tutte le azioni eseguite dal servizio di provisioning nell'app Google Apps.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare l'accesso Single Sign-On](active-directory-saas-google-apps-tutorial.md)



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
