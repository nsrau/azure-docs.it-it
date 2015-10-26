<properties
   pageTitle="Esercitazione: Integrazione di NetSuite con Azure Active Directory | Microsoft Azure"
   description="Informazioni su come usare NetSuite con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora."
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="TerryLanfear"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/15/2015"
   ms.author="liviodlc"/>

#Esercitazione: Come eseguire l'integrazione di NetSuite con Azure Active Directory

Questa esercitazione illustrerà come connettere l'ambiente di NetSuite ad Azure Active Directory (Azure AD). Verrà illustrato come configurare l'accesso Single Sign-On per NetSuite, come abilitare il provisioning automatizzato degli utenti e come assegnare gli utenti in modo che possano accedere a NetSuite.

##Prerequisiti

1. Per accedere ad Azure Active Directory tramite il [portale di gestione di Azure](https://manage.windowsazure.com), è prima di tutto necessario avere una sottoscrizione di Azure valida.

2. È necessario l'accesso amministrativo alla sottoscrizione di [NetSuite](http://www.netsuite.com/portal/home.shtml). È possibile usare un account della versione di valutazione gratuita per entrambi i servizi.

##Passaggio 1: Aggiungere NetSuite ad Active Directory

1. Nel [portale di gestione di Azure](https://manage.windowsazure.com) fare clic su **Active Directory** nel pannello di navigazione sinistro.

	![Selezionare Active Directory dal pannello di navigazione di sinistra.][0]

2. Dall'elenco **Directory** selezionare la directory in cui si vuole aggiungere NetSuite.

3. Scegliere **Applicazioni** dal menu principale.

	![Fare clic su applicazioni.][1]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

	![Fare clic su Aggiungi per aggiungere una nuova applicazione.][2]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungi un'applicazione dalla raccolta**.

	![Fare clic su Aggiungi un'applicazione dalla raccolta.][3]

6. Nella **casella di ricerca** digitare **NetSuite**. Selezionare quindi **NetSuite** dai risultati, quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Aggiungere NetSuite.][4]

7. Dovrebbe essere visualizzata la pagina Avvio rapido per NetSuite:

	![Pagina di Avvio rapido di NetSuite in Azure AD][5]

##Passaggio 2: Abilitare l'accesso Single Sign-On

1. Nella pagina di Avvio rapido per NetSuite in Azure AD fare clic sul pulsante **Configura accesso Single Sign-On**.

	![Pulsante Configura accesso Single Sign-On][6]

2. Verrà aperta una finestra di dialogo, in cui verrà visualizzata una schermata con messaggio analogo a "Stabilire come si desidera che gli utenti accedano a NetSuite". Selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

	![Selezionare Single Sign-On di Microsoft Azure AD][7]

	> [AZURE.NOTE]Per altre informazioni sulle diverse opzioni dell'accesso Single Sign-On, [fare clic qui](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work).

3. Nella pagina **Configurare le impostazioni dell'app** immettere nel campo **URL di accesso** l'URL del tenant di NetSuite, usando uno dei formati seguenti:
	- `https://<tenant-name>.netsuite.com`
	- `https://<tenant-name>.na1.netsuite.com`
	- `https://<tenant-name>.na2.netsuite.com`
	- `https://<tenant-name>.sandbox.netsuite.com`
	- `https://<tenant-name>.na1.sandbox.netsuite.com`
	- `https://<tenant-name>.na2.sandbox.netsuite.com`

	![Immettere l'URL del tenant][8]

4. Nella pagina **Configura accesso Single Sign-On in NetSuite** fare clic su **Scarica metadati** e infine salvare il file di certificato localmente nel computer.

	![Scaricare i metadati.][9]

5. Aprire una nuova scheda nel browser e accedere al sito della società Netsuite come amministratore.

6. Sulla barra degli strumenti nella parte superiore della pagina fare clic su **Setup** e quindi su **Setup Manager**.

	![Passare a Setup Manager.][10]

7. Dall'elenco **Setup Tasks** selezionare **Integration**.

	![Passare a Integration.][11]

8. Nella sezione **Manage Authentication** fare clic su **SAML Single Sign-on**.

	![Passare a SAML Single Sign-on.][12]

9. Nella pagina **SAML Setup** eseguire i passaggi seguenti:

	- In Azure Active Directory copiare il valore di **URL accesso remoto**, quindi incollarlo nel campo **Identity Provider Login Page** in NetSuite.

		![Pagina SAML Setup.][13]

	- In NetSuite selezionare **Primary Authentication Method**.

	- Per il campo con etichetta **SAMLV2 Identity Provider Metadata** selezionare **Upload IDP Metadata File**. Fare quindi clic su **Browse** per caricare il file di metadati scaricato nel passaggio 4.

		![Caricare i metadati.][16]

	- Fare clic su **Submit**.

9. In Azure AD selezionare la casella di controllo di conferma della configurazione Single Sign-On per abilitare il certificato caricato in NetSuite. Quindi fare clic su **Avanti**.

	![Selezionare la casella di controllo di conferma.][14]

10. Nella pagina finale della finestra di dialogo immettere un indirizzo di posta elettronica se si vogliono ricevere notifiche tramite posta elettronica relative a errori e avvisi correlati alla manutenzione di questa configurazione dell'accesso Single Sign-On.

	![Immettere l'indirizzo di posta elettronica.][15]

11. Fare clic su **Completa** per chiudere la finestra di dialogo. Fare quindi clic su **Attributi** nella parte superiore della pagina.

	![Fare clic su Attributi.][17]

12. Fare clic su **Aggiungi attributo utente**.

	![Fare clic su Aggiungi attributo utente.][18]

13. Nel campo **Nome attributo** immettere `account`. Nel campo **Valore attributo** immettere l'ID dell'account NetSuite. Per istruzioni su come individuare l'ID dell'account, vedere di seguito:

	![Aggiungere l'ID dell'account NetSuite.][19]

	- In NetSuite scegliere **Setup** dal menu di navigazione principale.
	- Fare quindi clic sulla sezione **Setup Tasks** del menu di navigazione sinistro, selezionare la sezione **Integration**, quindi fare clic su **Web Services Preferences**.
	- Copiare l'ID dell'account NetSuite e incollarlo nel campo **Attribute Value** in Azure AD.

		![Ottenere l'ID dell'account.][20]

14. In Azure AD fare clic su **Completa** per completare l'aggiunta dell'attributo SAML. Scegliere quindi **Applica modifiche** dal menu inferiore.

	![Salvare le modifiche.][21]

15. Per consentire l'accesso Single Sign-On a NetSuite, è prima di tutto necessario che le autorizzazioni appropriate vengano assegnate agli utenti in NetSuite. Per assegnare le autorizzazioni, seguire le istruzioni seguenti.

	- Scegliere **Setup** dal menu di navigazione principale, quindi fare clic su **Setup Manager**.

	![Passare a Setup Manager.][10]

	- Scegliere **Users/Roles** dal menu di navigazione a sinistra, quindi fare clic su **Manage Roles**.

	![Passare a Manage Roles.][22]

	- Fare clic su **New Role**.

	- Immettere un valore per **Name** per il nuovo ruolo, quindi selezionare la casella di controllo **Single Sign-On Only**.

	![Assegnare un nome al nuovo ruolo.][23]

	- Fare clic su **Save**.

	- Scegliere **Permissions** dal menu disponibile nella parte superiore. Fare quindi clic su **Setup**.

	![Passare a Permissions.][24]

	- Selezionare **Set Up SAM Single Sign-on**, quindi fare clic su **Add**.

	- Fare clic su **Save**.

	- Scegliere **Setup** dal menu di navigazione principale, quindi fare clic su **Setup Manager**.

	![Passare a Setup Manager.][10]

	- Scegliere **Users/Roles** dal menu di navigazione a sinistra, quindi fare clic su **Manage Users**.

	![Passare a Manage Users.][25]

	- Selezionare un utente di test. Fare quindi clic su **Edit**.

	![Passare a Manage Users.][26]

	- Nella finestra di dialogo Roles selezionare il ruolo creato e quindi fare clic su **Add**.

	![Passare a Manage Users.][27]

	- Fare clic su **Save**.

19. Per testare la configurazione, vedere la sezione seguente intitolata [Assegnare utenti a NetSuite](#step-4-assign-users-to-netsuite).

##Passaggio 3: Abilitare il provisioning automatizzato degli utenti

1. Nella pagina di Avvio rapido per NetSuite in Azure Active Directory fare clic su **Configura provisioning utenti**.

	![Configurare il provisioning utente.][28]

2. Nella finestra di dialogo visualizzata immettere le credenziali di amministratore per NetSuite, quindi fare clic su **Avanti**.

	![Immettere le credenziali dell'amministratore di NetSuite.][29]

3. Nella pagina di conferma, immettere l'indirizzo di posta elettronica per ricevere notifiche relative agli errori di provisioning.

	![Confermare.][30]

4. Fare clic su **Completa** per chiudere la finestra di dialogo.

##Passaggio 4: Assegnare utenti a NetSuite

1. Per testare la configurazione, creare prima di tutto un nuovo account di test nella directory.

2. Nella pagina di Avvio rapido di NetSuite fare clic sul pulsante **Assegna utenti**.

	![Fare clic su Assegna utenti.][31]

3. Selezionare l'utente di test, quindi fare clic su **Assegna** nella parte inferiore della schermata:

 - Se il provisioning automatizzato degli utenti non è stato abilitato, verrà visualizzata la richiesta di conferma seguente:

		![Confirm the assignment.][32]

 - Se il provisioning automatizzato degli utenti è stato abilitato, verrà visualizzata una richiesta per definire il tipo di ruolo da assegnare all'utente in NetSuite. Gli utenti appena sottoposti a provisioning dovrebbero essere visualizzati nell'ambiente di NetSuite dopo alcuni minuti.

4. Per testare le impostazioni dell'accesso Single Sign-On, aprire il pannello di accesso all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com/), quindi accedere all'account di test e fare clic su **NetSuite**.

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png

<!---HONumber=Oct15_HO3-->