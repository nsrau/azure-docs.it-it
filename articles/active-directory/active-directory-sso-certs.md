<properties
	pageTitle="Come gestire i certificati di federazione in Azure AD | Microsoft Azure"
	description="Informazioni su come personalizzare la data di scadenza per i certificati di federazione e su come rinnovare i certificati con scadenza imminente."
	services="active-directory"
	documentationCenter=""
	authors="liviodlc"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="liviodlc"/>

#Gestione di certificati per Single Sign-On federato in Azure Active Directory

Questo articolo affronta domande comuni relative ai certificati creati da Azure Active Directory per stabilire l'accesso Single Sign-On federato (SSO) alle applicazioni SaaS.

Questo articolo è applicabile solo alle app configurate per usare **Single Sign-On di Microsoft Azure AD**, come illustrato nell'esempio seguente:

![Accesso Single Sign-On di Azure AD](./media/active-directory-sso-certs/fed-sso.PNG)

##Come personalizzare la data di scadenza per il certificato di federazione

Per impostazione predefinita, i certificati sono impostati per la scadenza dopo due anni. È possibile scegliere una data di scadenza diversa per il certificato eseguendo i passaggi seguenti. Le schermate incluse usano Salesforce per le finalità dell'esempio, ma questi passaggi possono essere applicati a qualsiasi app SaaS federata.

1. Nella pagina di Avvio rapido per l'applicazione in Azure Active Directory fare clic su **Configura accesso Single Sign-On**.

	![Aprire la configurazione guidata di SSO.](./media/active-directory-sso-certs/config-sso.png)

2. Selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

3. Immettere l'**URL accesso** dell'applicazione, quindi selezionare la casella di controllo **Configura il certificato usato per l'autenticazione Single Sign-On federata**. Quindi fare clic su **Avanti**.

	![Accesso Single Sign-On di Azure AD](./media/active-directory-sso-certs/new-app-config-sso.PNG)

4. Nella pagina successiva selezionare **Genera un nuovo certificato**, quindi specificare la durata desiderata per la validità del certificato. Quindi fare clic su **Avanti**.

	![Generare un nuovo certificato](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Fare quindi clic su **Download certificato**. Per informazioni su come caricare il certificato nell'app SaaS specifica, fare clic su **Visualizzare le istruzioni di configurazione di**.

	![Scaricare e quindi caricare il certificato](./media/active-directory-sso-certs/new-app-config-app.PNG)

6. Il certificato sarà abilitato solo dopo la selezione della casella di controllo di conferma nella parte inferiore della finestra di dialogo e dopo la selezione di Invia.

##Come rinnovare un certificato con scadenza imminente

I passaggi per il rinnovo illustrati di seguito non dovrebbero idealmente comportare tempi di inattività significativi per gli utenti. Le schermate usate in questa sezione includono Salesforce come esempio, ma i passaggi possono essere applicati a qualsiasi app SaaS federata.

1. Nella pagina di Avvio rapido per l'applicazione in Azure Active Directory fare clic su **Configura accesso Single Sign-On**.

	![Aprire la configurazione guidata di SSO](./media/active-directory-sso-certs/renew-sso-button.PNG)

2. Nella prima pagina della finestra di dialogo dovrebbe essere già selezionata l'opzione **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.

3. Nella seconda pagina selezionare la casella di controllo **Configura il certificato usato per l'autenticazione Single Sign-On federata**. Quindi fare clic su **Avanti**.

	![Accesso Single Sign-On di Azure AD](./media/active-directory-sso-certs/renew-config-sso.PNG)

4. Nella pagina successiva selezionare **Genera un nuovo certificato**, quindi specificare la durata desiderata per la validità del nuovo certificato. Quindi fare clic su **Avanti**.

	![Generare un nuovo certificato](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Fare clic su **Download certificato**. Per rinnovare correttamente il certificato, è necessario eseguire i due passaggi seguenti:

	- Caricare il nuovo certificato nella schermata di configurazione dell'accesso Single Sign-On dell'app SaaS. Per informazioni su come eseguire questa operazione per l'app SaaS specifica, fare clic su **Visualizzare le istruzioni di configurazione di**.

	- In Azure AD selezionare la casella di conferma nella parte inferiore della finestra di dialogo per abilitare il nuovo certificato, quindi fare clic su **Avanti** per l'invio.

	> [AZURE.IMPORTANT] L'accesso Single Sign-On all'app verrà disabilitato dopo il completamento di uno di questi due passaggi, ma verrà abilitato di nuovo dopo il completamento del secondo passaggio. Per ridurre al minimo il tempo di inattività, è quindi consigliabile prepararsi per completare entrambi i passaggi a breve distanza l'uno dall'altro.

	![Scaricare e quindi caricare il certificato](./media/active-directory-sso-certs/renew-config-app.PNG)

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!---HONumber=AcomDC_0204_2016-->