---
title: 'Esercitazione: Integrazione di Azure Active Directory con Google Apps | Microsoft Docs'
description: Informazioni su come usare Google Apps con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2016
ms.author: asmalser-msft

---
# <a name="tutorial:-how-to-integrate-google-apps-with-azure-active-directory"></a>Esercitazione: Come integrare Google Apps ad Azure Active Directory
Questa esercitazione illustrerà come connettere l'ambiente di Google Apps ad Azure Active Directory (Azure AD). Si apprenderà come configurare l'accesso Single Sign-On in Google Apps, come abilitare il provisioning automatico dell'utente e come assegnare agli utenti l'accesso a Google Apps. 

## <a name="prerequisites"></a>Prerequisiti
1. Per accedere ad Azure Active Directory tramite il [portale di Azure classico](https://manage.windowsazure.com), prima di tutto è necessario avere una sottoscrizione di Azure valida.
2. È necessario disporre di un tenant valido per [Google Apps per il lavoro](https://www.google.com/work/apps/) o [Google Apps per la didattica](https://www.google.com/edu/products/productivity-tools/). È possibile usare un account della versione di valutazione gratuita per entrambi i servizi.

## <a name="video-tutorial"></a>Esercitazione video
Come abilitare Single Sign-On in Google Apps in 2 minuti:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enable-single-sign-on-to-Google-Apps-in-2-minutes-with-Azure-AD/player]
> 
> 

## <a name="frequently-asked-questions"></a>Domande frequenti
1. **D: I dispositivi Chromebooks e altri dispositivi Chrome sono compatibili con Single Sign-On di Azure AD?**
   
    R: Sì, gli utenti possono accedere ai dispositivi Chromebook con le credenziali di Azure AD. Per informazioni sui motivi per cui agli utenti può essere richiesto di immettere le credenziali due volte, vedere questo [articolo del supporto tecnico di Google Apps](https://support.google.com/chrome/a/answer/6060880) .
2. **D: Se si abilita il Single Sign-On, gli utenti potranno usare le credenziali di Azure AD per accedere a qualsiasi prodotto di Google, ad esempio Google Classroom, GMail, Google Drive, YouTube e così via?**
   
    R: Sì, a seconda delle [Google Apps](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) che si sceglie di abilitare o disabilitare per la propria organizzazione.
3. **D: È possibile abilitare il Single Sign-On solo per un sottoinsieme di utenti di Google Apps?**
   
    R: No, l'attivazione del Single Sign-On richiederà immediatamente a tutti gli utenti di Google Apps di autenticarsi con le proprie credenziali di Azure AD. Poiché Google Apps non supporta più provider di identità, il provider di identità per l'ambiente di Google Apps può essere AD Azure o Google, ma non entrambi contemporaneamente.
4. **D: Se un utente ha eseguito l'accesso tramite Windows, verrà autenticano automaticamente in Google Apps senza che venga richiesta una password?**
   
    R: Sono disponibili due opzioni per l'abilitazione di questo scenario. Nel primo caso gli utenti possono accedere ai dispositivi Windows 10 tramite [Aggiunta ad Azure Active Directory](active-directory-azureadjoin-overview.md). In alternativa, gli utenti possono accedere ai dispositivi Windows appartenenti a un dominio di Active Directory locale abilitato per il Single Sign-On in Azure AD tramite una distribuzione di [Active Directory Federation Services (AD FS)](active-directory-aadconnect-user-signin.md) . Naturalmente, entrambe le opzioni richiedono di seguire l'esercitazione indicata di seguito per abilitare il Single Sign-On tra Azure AD e Google Apps.

## <a name="step-1:-add-google-apps-to-your-directory"></a>Passaggio 1: Aggiungere Google Apps a una Directory
1. Nel [portale di Azure classico](https://manage.windowsazure.com)fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Selezionare Active Directory dal riquadro di spostamento a sinistra.][0]
2. Dall'elenco **Directory** selezionare la directory a cui si vuole aggiungere Google Apps.
3. Scegliere **Applicazioni** dal menu principale.
   
    ![Fare clic su applicazioni.][1]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Fare clic su Aggiungi per aggiungere una nuova applicazione.][2]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Fare clic su Aggiungi un'applicazione dalla raccolta.][3]
6. Nella **casella di ricerca** digitare **Google Apps**. Selezionare quindi **Google Apps** dai risultati, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Aggiungere Google Apps.][4]
7. Ora dovrebbe essere visualizzata la pagina Avvio rapido per Google Apps:
   
    ![Pagina di avvio rapido di Google Apps in Azure AD][5]

## <a name="step-2:-enable-single-sign-on"></a>Passaggio 2: Abilitare l'accesso Single Sign-On
1. Nella pagina di Avvio rapido per Google Apps in Azure AD fare clic sul pulsante **Configura accesso Single Sign-On** .
   
    ![Pulsante Configura accesso Single Sign-On][6]
2. Verrà aperta una finestra di dialogo, in cui verrà visualizzata una schermata con messaggio analogo a "Stabilire come si desidera che gli utenti accedano a Google Apps". Selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Selezionare Single Sign-On di Microsoft Azure AD.][7]
   
   > [!NOTE]
   > Per altre informazioni sulle diverse opzioni dell'accesso Single Sign-On, [fare clic qui](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)
   > 
   > 
3. Nella pagina **Configurare le impostazioni dell'app** immettere nel campo **URL di accesso** l'URL del tenant di Google Apps, usando uno dei formati seguenti: `https://mail.google.com/a/<yourdomain>`
   
    ![Immettere l'URL del tenant][8]
4. Nella pagina **Configura automaticamente l'accesso Single Sign-On** digitare il dominio del tenant di Google Apps. Fare clic sul pulsante **Configura** .
   
    ![Digitare il nome di dominio e scegliere Configura.](./media/active-directory-saas-google-apps-tutorial/ga-auto-config.png)
   
   > [!NOTE]
   > Se si preferisce configurare Single Sign-On manualmente, vedere [Passaggio facoltativo: Configurare Single Sign-On manualmente](#optional-step-manually-configure-single-sign-on)
   > 
   > 
5. Accedere all'account amministrativo di Google Apps. Fare quindi clic su **Allow** (Consenti) per permettere ad Azure Active Directory di apportare modifiche alla configurazione nella sottoscrizione di Google Apps.
   
    ![Digitare il nome di dominio e scegliere Configura.](./media/active-directory-saas-google-apps-tutorial/ga-consent.PNG)
6. Attendere alcuni secondi per la configurazione del tenant di Google Apps da parte di Azure Active Directory. Al termine, fare clic su **Next**.
7. Nella pagina finale della finestra di dialogo immettere un indirizzo di posta elettronica se si vogliono ricevere notifiche tramite posta elettronica relative a errori e avvisi correlati alla manutenzione di questa configurazione dell'accesso Single Sign-On.
   
   ![Immettere l'indirizzo di posta elettronica][14]
8. Fare clic su **Complete** per chiudere la finestra di dialogo. Per testare la configurazione, vedere la sezione seguente intitolata [Assegnare utenti a Google Apps](#step-4-assign-users-to-google-apps).

## <a name="optional-step:-manually-configure-single-sign-on"></a>Passaggio facoltativo: Configurare Single Sign-On manualmente
Se si preferisce impostare manualmente l'accesso Single Sign-On, completare i passaggi seguenti:

1. Nella pagina di Avvio rapido per Google Apps in Azure AD fare clic sul pulsante **Configura accesso Single Sign-On** .
   
    ![Pulsante Configura accesso Single Sign-On][6]
2. Verrà aperta una finestra di dialogo, in cui verrà visualizzata una schermata con messaggio analogo a "Stabilire come si desidera che gli utenti accedano a Google Apps". Selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Selezionare Single Sign-On di Microsoft Azure AD.][7]
   
   > [!NOTE]
   > Per altre informazioni sulle diverse opzioni dell'accesso Single Sign-On, [fare clic qui](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)
   > 
   > 
3. Nella pagina **Configurare le impostazioni dell'app** immettere nel campo **URL di accesso** l'URL del tenant di Google Apps, usando uno dei formati seguenti: `https://mail.google.com/a/<yourdomain>`
   
    ![Immettere l'URL del tenant][8]
4. Nella pagina **Configura automaticamente l'accesso Single Sign-On** selezionare la casella di controllo denominata **Configura manualmente questa applicazione per l'accesso Single Sign-On**. Quindi fare clic su **Next**.
   
    ![Scegliere la configurazione manuale.](./media/active-directory-saas-google-apps-tutorial/ga-auto-skip.PNG)
5. Nella pagina **Configura accesso Single Sign-On in Google Apps** fare clic su **Download certificato** e infine salvare il certificato localmente nel computer.
   
    ![Scaricare il certificato][9]
6. Aprire una nuova scheda nel browser e accedere a [Console di amministrazione di Google Apps](http://admin.google.com/) utilizzando l'account amministratore.
7. Fare clic su **Security**. Se non viene visualizzato il collegamento, può essere nascosto sotto il menu **More Controls** nella parte inferiore della schermata.
   
    ![Fare clic su sicurezza.][10]
8. Nella pagina **Security** (Sicurezza) fare clic su **Set up single sign-on (SSO)** (Configurazione Single Sign-On (SSO)).
   
    ![Fare clic su SSO.][11]
9. Eseguire le seguenti modifiche di configurazione:
   
    ![Configurare SSL][12]
   
   * Selezionare **Setup SSO with third party identity provider**.
   * In Azure AD copiare l'**URL di servizio di Single Sign-On** e incollarlo nel campo **Sign-in page URL** (URL pagina di accesso) in Google Apps.
   * In Azure AD copiare l'**URL di servizio di Single Sign-Out** e incollarlo nel campo **Sign-out page URL** (URL pagina di disconnessione) in Google Apps.
   * In Azure AD copiare l'**URL di Modifica password** e incollarlo nel campo **Change password URL** (Modifica URL password) in Google Apps.
   * In Google Apps per il **Certificato di verifica**, caricare il certificato che è stato scaricato nel passaggio #4.
   * Fare clic su **Save Changes**.
10. In Azure AD selezionare la casella di controllo di conferma della configurazione Single Sign-On per abilitare il certificato caricato in Google Apps. Quindi fare clic su **Avanti**.
    
     ![Selezionare la casella di controllo di conferma.][13]
11. Nella pagina finale della finestra di dialogo immettere un indirizzo di posta elettronica se si vogliono ricevere notifiche tramite posta elettronica relative a errori e avvisi correlati alla manutenzione di questa configurazione dell'accesso Single Sign-On. 
    
    ![Immettere l'indirizzo di posta elettronica][14]
12. Fare clic su **Complete** per chiudere la finestra di dialogo. Per testare la configurazione, vedere la sezione seguente intitolata [Assegnare utenti a Google Apps](#step-4-assign-users-to-google-apps).

## <a name="step-3:-enable-automated-user-provisioning"></a>Passaggio 3: Abilitare il provisioning automatizzato degli utenti
> [!NOTE]
> Un'altra opzione valida per automatizzare il provisioning dell’utente in Google Apps consiste nell'utilizzare [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en) che fornisce le identità di Active Directory locale in Google Apps. Al contrario, la soluzione in questa esercitazione esegue il provisioning per gli utenti e i gruppi abilitati alla posta elettronica in Google Apps Azure Active Directory (cloud).
> 
> 

1. Accedere alla [console di amministrazione di Google Apps](http://admin.google.com/) con l'account amministratore e fare clic su **Security**. Se non viene visualizzato il collegamento, può essere nascosto sotto il menu **More Controls** nella parte inferiore della schermata.
   
    ![Fare clic su sicurezza.][10]
2. Nella pagina **Security** (Sicurezza) fare clic su **API Reference** (Riferimento API).
   
    ![Fare clic su Informazioni di riferimento sulle API.][15]
3. Selezionare **Enable API access**.
   
    ![Fare clic su Informazioni di riferimento sulle API.][16]
   
   > [!IMPORTANT]
   > Per ogni utente per cui si intende eseguire il provisioning in Google Apps, si *deve* associare il relativo nome utente in Azure Active Directory a un dominio personalizzato. Ad esempio, nomi utente simili a bob@contoso.onmicrosoft.com non verranno accettati da Google Apps, mentre bob@contoso.com verranno accettati. È possibile modificare il dominio di un utente esistente modificandone le proprietà in Azure AD. Le istruzioni su come impostare un dominio personalizzato per Azure Active Directory e Google Apps vengono illustrate qui di seguito.
   > 
   > 
4. Se ancora non è stato aggiunto un nome di dominio personalizzato per Azure Active Directory, attenersi alla procedura seguente:
   
   * Nel [portale di Azure classico](https://manage.windowsazure.com)fare clic su **Active Directory**nel riquadro di spostamento sinistro. Nell'elenco visualizzato, selezionare la directory. 
   * Fare clic su **Domini** dal menu principale in alto e quindi fare clic su **Aggiungere un dominio personalizzato**.
     
       ![Aggiungere un dominio personalizzato][17]
   * Digitare il nome di dominio nel campo **Nome di dominio** . Questo deve essere lo stesso nome di dominio che si intende utilizzare per Google Apps. Quando si è pronti, fare clic sul pulsante **Aggiungi** .
     
       ![Digitare il nome di dominio.][18]
   * Fare clic su **Avanti** per passare alla pagina di verifica. Per verificare che si è proprietari di tale dominio, è necessario modificare i record DNS del dominio in base ai valori forniti in questa pagina. È possibile verificare tramite **record MX** o **record TXT** a seconda di ciò che viene selezionato per l'opzione **Tipo di record**. Per istruzioni più dettagliate su come verificare il nome di dominio con Microsoft Azure, vedere [Aggiungere il proprio nome di dominio ad Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
       ![Verificare il nome di dominio][19]
   * Ripetere i passaggi precedenti per tutti i domini che si desidera aggiungere alla directory.
5. Ora che tutti i domini sono stati verificati con Azure AD, è necessario verificarli nuovamente con Google Apps. Per ogni dominio che non sia già registrato con Google Apps, procedere come segue:
   
   * Nella [console di amministrazione di Google Apps](http://admin.google.com/)fare clic su **Domains**.
     
       ![Fare clic su Domini][20]
   * Fare clic su **Add a domain or a domain alias**.
     
       ![Aggiungere un nuovo dominio][21]
   * Selezionare **Add another domain**e digitare il nome del dominio che si desidera aggiungere.
     
       ![Digitare il nome di dominio][22]
   * Fare clic su **Continue and verify domain ownership**. Seguire i passaggi per verificare che si è proprietari del nome di dominio. Per istruzioni dettagliate su come verificare il dominio con Google Apps, vedere [Verificare il proprietario del sito con Google Apps](https://support.google.com/webmasters/answer/35179).
   * Ripetere i passaggi precedenti per tutti i domini aggiuntivi che si desidera aggiungere a Google Apps.
     
     > [!WARNING]
     > Se si modifica il dominio primario per il tenant di Google Apps e se è già stato configurato Single Sign-On con Azure AD, sarà necessario ripetere il passaggio 3 in [Passaggio 2: Abilitare il servizio Single Sign-On](#step-two-enable-single-sign-on).
     > 
     > 
6. Nella [console di amministrazione di Google Apps](http://admin.google.com/)fare clic su **Admin Roles**.
   
    ![Fare clic su Google Apps][26]
7. Determinare quale account di amministratore si desidera utilizzare per gestire il provisioning dell'utente. Per il **ruolo di amministratore** di tale account, modificare i **privilegi** per tale ruolo. Assicurarsi che tutti i **privilegi dell'API di amministratore** siano abilitati in modo che questo account possa essere usato per il provisioning.
   
    ![Fare clic su Google Apps][27]
   
   > [!NOTE]
   > Se si sta configurando un ambiente di produzione, la procedura consigliata consiste nel creare un nuovo account di amministratore in Google Apps specificatamente per questo passaggio. Questi account devono disporre di un ruolo amministrativo associato che abbia privilegi API necessari.
   > 
   > 
8. In Azure Active Directory fare clic su **Applicazioni** nel menu in alto e fare clic su **Google Apps**.
   
    ![Fare clic su Google Apps][23]
9. Nella pagina di Avvio rapido per Google Apps fare clic su **Configura provisioning utenti**.
   
    ![Configura provisioning utenti][24]
10. Nella finestra di dialogo visualizzata fare clic su **Abilita provisioning dell'utente** per l'autenticazione nell'account amministratore di Google Apps che si desidera usare per gestire il provisioning.
    
    ![Abilitare il provisioning][25]
11. Confermare che si desidera concedere l'autorizzazione Azure Active Directory per apportare modifiche al tenant di Google Apps.
    
    ![Verificare le autorizzazioni.][28]
12. Fare clic su **Complete** per chiudere la finestra di dialogo.

## <a name="step-4:-assign-users-to-google-apps"></a>Passaggio 4: Assegnare utenti a Google Apps
1. Per testare la configurazione, creare prima di tutto un nuovo account di test nella directory.
2. Nella pagina di Avvio rapido per Google Apps fare clic sul pulsante **Assegna utenti** .
   
    ![Fare clic su Assegna utenti][29]
3. Selezionare l'utente di test e quindi fare clic su **Assegna** nella parte inferiore della schermata:
   
   * Se il provisioning automatizzato degli utenti non è stato abilitato, verrà visualizzata la richiesta di conferma seguente:
     
        ![Confirm the assignment.][30]
   * Se il provisioning automatizzato degli utenti è stato abilitato, verrà visualizzata una richiesta per definire il tipo di ruolo da assegnare all'utente in Google Apps. Gli utenti appena sottoposti a provisioning dovrebbero essere visualizzati nell'ambiente di Google Apps dopo alcuni minuti.
4. Per testare le impostazioni dell'accesso Single Sign-On, aprire il pannello di accesso all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com/), quindi accedere all'account di test e fare clic su **Google Apps**.

## <a name="related-articles"></a>Articoli correlati
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-google-apps-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-google-apps-tutorial/add-app.png
[3]: ./media/active-directory-saas-google-apps-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-google-apps-tutorial/add-gapps.png
[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png



<!--HONumber=Oct16_HO2-->


