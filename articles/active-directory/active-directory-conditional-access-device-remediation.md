<properties
	pageTitle="Correzione a disposizione dell'utente per accedere ad applicazioni protette con accesso condizionale basato su dispositivo di Azure AD | Microsoft Azure"
	description="Questo argomento consente di identificare se è disponibile una procedura di correzione da seguire per ottenere l'accesso all'applicazione desiderata."
	services="active-directory"
	keywords="accesso condizionale basato su dispositivo, registrazione dispositivo, abilitare registrazione dispositivo, registrazione dispositivo e software MDM"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/15/2016"
	ms.author="markvi"/>


# Correzione a disposizione dell'utente per accedere ad applicazioni protette con accesso condizionale basato su dispositivo di Azure AD

Quando durante l'accesso a un'applicazione come Office 365 SharePoint Online viene visualizzata una pagina "Accesso negato", come si deve procedere?

Questa guida consente di identificare se è disponibile una procedura di correzione da seguire per ottenere l'accesso all'applicazione desiderata.



In base alla piattaforma di esecuzione del dispositivo in uso, scegliere la sezione di questo argomento appropriata:
 

-	Dispositivo Windows

-	Dispositivo iOS (iPhone o iPad)

-	Dispositivo Android

## Accesso da un dispositivo Windows

In caso di dispositivo Windows 10, Windows 8.1, Windows 8.0, Windows 7, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2, scegliere la causa appropriata identificando la pagina visualizzata quando si prova ad accedere all'applicazione.

### Dispositivo non registrato

Se il dispositivo non è registrato in Azure Active Directory e l'applicazione è protetta con criteri basati sul dispositivo, potrebbe essere visualizzata una pagina con il contenuto seguente:

![Scenario](./media/active-directory-conditional-access-device-remediation/01.png "Scenario")

 

Se il dispositivo è aggiunto al dominio di Active Directory dell'organizzazione, provare a eseguire le operazioni seguenti:

1.	Verificare di aver eseguito l'accesso a Windows con il proprio account aziendale (account Active Directory).

2.	Connettersi alla rete aziendale tramite VPN o DirectAccess.

3.	Dopo aver stabilito la connessione, bloccare la sessione di Windows premendo il tasto WINDOWS + L.

4.	Sbloccare la sessione di Windows immettendo le credenziali dell'account aziendale.

5.	Attendere un minuto e provare di nuovo ad accedere all'applicazione.

6.	Se viene visualizzata la stessa pagina, contattare l'amministratore e specificare i dettagli visualizzati facendo clic sul collegamento "Altri dettagli".

Se il dispositivo non è aggiunto al dominio ed esegue Windows 10, sono disponibili due opzioni:

1. Eseguire l'aggiunta ad Azure AD
2. Aggiungere l'account aziendale o dell'istituto di istruzione a Windows

Per informazioni sulle differenze tra le due opzioni, vedere [Uso di dispositivi Windows 10 in azienda](active-directory-azureadjoin-windows10-devices.md).

Per eseguire l'aggiunta ad Azure AD, seguire questa procedura (non disponibile in Windows Phone):

**Aggiornamento dell'anniversario di Windows 10**

1.	Avviare l'app Impostazioni.

2.	Passare ad "Account" e quindi ad "Access work or school" (Accesso azienda o istituto di istruzione).

3.	Fare clic su "Connetti".

4.	Scegliere "Aggiungi il dispositivo ad Azure Active Directory" nella parte inferiore della pagina.

5.	Eseguire l'autenticazione nell'organizzazione, fornire la prova MFA, se necessario, e seguire la procedura fino al termine.

6.	Disconnettersi ed eseguire l'accesso con l'account aziendale.

7.	Provare ad accedere di nuovo all'applicazione.




**Aggiornamento di novembre 2015 di Windows 10**


1.	Avviare l'app Impostazioni.

2.	Passare a "Sistema" e quindi a "Informazioni su".
	
3.	Fare clic su "Aggiungi ad Azure AD".

4.	Eseguire l'autenticazione nell'organizzazione, fornire la prova MFA, se necessario, e seguire la procedura fino al termine.

5.	Disconnettersi ed eseguire l'accesso con l'account aziendale (account Azure AD).

6.	Provare ad accedere di nuovo all'applicazione.


Per aggiungere l'account aziendale o dell'istituto di istruzione, seguire questa procedura:

**Aggiornamento dell'anniversario di Windows 10**

1.	Avviare l'app Impostazioni.

2.	Passare ad "Account" e quindi ad "Access work or school" (Accesso azienda o istituto di istruzione).

3.	Fare clic su "Connetti".

4.	Eseguire l'autenticazione nell'organizzazione, fornire la prova MFA, se necessario, e seguire la procedura fino al termine.

5.	Provare ad accedere di nuovo all'applicazione.


**Aggiornamento di novembre 2015 di Windows 10**
	
1.	Avviare l'app Impostazioni.
2.	Passare ad "Account" e quindi a "Your accounts" (Account personali).
3.	Fare clic su "Aggiungi account aziendale o dell'istituto di istruzione".
4.	Eseguire l'autenticazione nell'organizzazione, fornire la prova MFA, se necessario, e seguire la procedura fino al termine.
5.	Provare ad accedere di nuovo all'applicazione.

Se il dispositivo non è aggiunto al dominio ed esegue Windows 8.1, è possibile eseguire l'aggiunta alla rete aziendale e la registrazione in Microsoft Intune seguendo questa procedura:

1.	Avviare Impostazioni PC.

2.	Passare a "Rete" e quindi a "Rete aziendale".

3.	Fare clic su "Aggiungi".

4.	Eseguire l'autenticazione nell'organizzazione, fornire la prova MFA, se necessario, e seguire la procedura fino al termine.

5.	Fare clic su "Attiva".

6.	Attendere il completamento.

7.	Provare ad accedere di nuovo all'applicazione.


## Browser non supportato

Se si accede all'applicazione dai browser seguenti, verrà visualizzata una pagina simile a quella illustrata di seguito.

1.	Chrome, Firefox o qualsiasi altro browser diverso da Microsoft Edge o Microsoft Internet Explorer in Windows 10 o Windows Server 2016.

2.	Firefox in Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.
 

![Scenario](./media/active-directory-conditional-access-device-remediation/02.png "Scenario")


L'unica correzione consiste nell'usare un browser supportato per la piattaforma del dispositivo in uso.

## Accesso da un dispositivo iOS

Per le istruzioni per iPhone o iPad, visitare di nuovo questa pagina tra qualche tempo.

## Accesso da un dispositivo Android

Per le istruzioni per telefoni o tablet Android, visitare di nuovo questa pagina tra qualche tempo.


## Passaggi successivi

[Accesso condizionale di Azure Active Directory](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0817_2016-->