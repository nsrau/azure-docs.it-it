<properties 
	pageTitle="Stati utente in Microsoft Azure Multi-Factor Authentication"
	description="Informazioni sugli stati utente in Azure MFA."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>  

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# Stati utente in Azure Multi-Factor Authentication

Gli account utente in modalità Multi-Factor Authentication di Azure presentano i seguenti tre stati distinti:

Stato | Descrizione |App interessate non basate su browser| Note
:-------------: | :-------------: |:-------------: |:-------------: |
Disabled | Lo stato predefinito per un nuovo utente non registrato alla modalità Multi-Factor Authentication.|No|L'utente attualmente non usa la modalità Multi-Factor Authentication.
Enabled |L'utente è stato registrato alla modalità Multi-Factor Authentication.|No. Continuano a funzionare fino al completamento della registrazione.|L'utente è abilitato ma non ha completato la procedura di registrazione. All'utente verrà chiesto di completare la procedura all'accesso successivo.
Enforced|L'utente è stato registrato e ha completato la procedura di registrazione per l'utilizzo della modalità Multi-Factor Authentication.|Sì. Le app richiedono password per le app. | È possibile che l'utente abbia o non abbia completato la registrazione. Se la procedura di registrazione è stata completata, l'utente userà la modalità Multi-Factor Authentication. In caso contrario, all'utente verrà chiesto di completare la procedura all'accesso successivo.

## Modifica dello stato utente
Lo stato utente cambia a seconda di due fattori: la configurazione per la MFA e il completamento del processo da parte dell'utente. Quando si attiva la MFA per un utente, lo stato degli utenti cambia da disabilitato ad abilitato. Dopo che l'utente abilitato esegue l'accesso e completa il processo, lo stato si imposterà su Applicato.

### Per visualizzare lo stato di un utente
--------------------------------------------------------------------------------
1.  Accedere al **portale di Azure classico** come amministratore.
2.  A sinistra fare clic su **Active Directory**.
3.  In **Directory** fare clic sulla directory relativa all'utente che si desidera attivare. ![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Nella parte superiore fare clic su **Utenti**.
5.  Nella parte inferiore della pagina fare clic su **Gestisci Multi-Factor Auth**. ![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Verrà aperta una nuova scheda del browser. Sarà possibile visualizzare lo stato degli utenti. ![Fare clic su Directory](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

###Per modificare lo stato da disabilitato ad abilitato
1.  Accedere al **portale di Azure classico** come amministratore.
2.  A sinistra fare clic su **Active Directory**.
3.  In **Directory** fare clic sulla directory relativa all'utente che si desidera attivare. ![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Nella parte superiore fare clic su **Utenti**.
5.  Nella parte inferiore della pagina fare clic su **Gestisci Multi-Factor Auth**. ![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Verrà aperta una nuova scheda del browser. Trovare l'utente che si desidera abilitare alla modalità Multi-Factor Authentication Potrebbe essere necessario modificare la visualizzazione nella parte superiore. Assicurarsi che lo stato sia **Disattivato.** ![Attivare l'utente](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  **Selezionare** la casella accanto al nome.
7.  A destra fare clic su **Abilita**. ![Attivare l'utente](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Fare clic su **Abilita Multi-Factor Auth**. ![Attivare l'utente](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Si noterà che lo stato dell'utente è cambiato da **Disattivato** in **Attivato**. ![Attivare gli utenti](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Dopo avere abilitato gli utenti, è consigliabile inviare una notifica tramite posta elettronica. È anche opportuno informarli su come possono usare le app non basate su browser per evitare di essere bloccati.

### Per modificare lo stato da abilitato/applicato a disabilitato
1.  Accedere al **portale di Azure classico** come amministratore.
2.  A sinistra fare clic su **Active Directory**.
3.  In **Directory** fare clic sulla directory relativa all'utente che si desidera attivare. ![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Nella parte superiore fare clic su **Utenti**.
5.  Nella parte inferiore della pagina fare clic su **Gestisci Multi-Factor Auth**. ![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Verrà aperta una nuova scheda del browser. Cercare l'utente che si desidera disabilitare. Potrebbe essere necessario modificare la visualizzazione nella parte superiore. Assicurarsi che lo stato sia **Abilitato** o **Applicato**.
7.  **Selezionare** la casella accanto al nome.
7.  A destra fare clic su **Disattiva**. ![Disabilitare l'utente](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
8.  Verrà richiesta conferma. Fare clic su **Sì**. ![Disabilitare l'utente](./media/multi-factor-authentication-get-started-user-states/userstate3.png)
9.  A questo punto l'operazione dovrebbe essere completa. Fare clic su **Chiudi.** ![Disabilitare l'utente](./media/multi-factor-authentication-get-started-user-states/userstate4.png)

<!---HONumber=AcomDC_0921_2016-->