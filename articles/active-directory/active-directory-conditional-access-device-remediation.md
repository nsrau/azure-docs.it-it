---
title: 'Risoluzione dei problemi: Non è possibile accedervi da qui | Microsoft Docs'
description: Questo argomento consente di identificare una procedura di correzione da seguire per ottenere l'accesso a un'applicazione.
services: active-directory
keywords: accesso condizionale basato su dispositivo, registrazione dispositivo, abilitare registrazione dispositivo, registrazione dispositivo e software MDM
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: markvi

---
# Risoluzione dei problemi: Non è possibile accedervi da qui
Quando durante l'accesso a un'applicazione come SharePoint Online viene visualizzata una pagina "Accesso negato", come si deve procedere?

Questa guida consente di identificare se è disponibile una procedura di correzione da seguire per ottenere l'accesso all'applicazione.

In base alla piattaforma di esecuzione del dispositivo in uso, scegliere la sezione di questo argomento appropriata:

* Dispositivo Windows
* Dispositivo iOS (iPhone o iPad)
* Dispositivo Android

## Accesso da un dispositivo Windows
In caso di dispositivo Windows 10, Windows 8.1, Windows 8.0, Windows 7, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2, scegliere la causa appropriata identificando la pagina visualizzata quando si è provato ad accedere all'applicazione.

### Dispositivo non registrato
Se il dispositivo non è registrato in Azure Active Directory (Azure AD) e l'applicazione è protetta con criteri basati sul dispositivo, potrebbe essere visualizzata una pagina con il contenuto seguente:

![Messaggi "Non è possibile accedervi da qui" per dispositivi non registrati](./media/active-directory-conditional-access-device-remediation/01.png "Scenario")

Se il dispositivo è aggiunto al dominio di Active Directory dell'organizzazione, provare a eseguire le operazioni seguenti:

1. Verificare di aver eseguito l'accesso a Windows con il proprio account aziendale (account Active Directory).
2. Connettersi alla rete aziendale tramite VPN o DirectAccess.
3. Dopo aver stabilito la connessione, bloccare la sessione di Windows premendo il tasto WINDOWS+L.
4. Sbloccare la sessione di Windows immettendo le credenziali dell'account aziendale.
5. Attendere un minuto e quindi provare di nuovo ad accedere all'applicazione.
6. Se viene visualizzata la stessa pagina, contattare l'amministratore, fare clic sul collegamento **Altri dettagli** e quindi specificare i dettagli.

Se il dispositivo non è aggiunto al dominio ed esegue Windows 10, sono disponibili due opzioni:

* Eseguire l'aggiunta ad Azure AD.
* Aggiungere l'account aziendale o dell'istituto di istruzione a Windows

Per informazioni sulle differenze tra le due opzioni, vedere [Uso di dispositivi Windows 10 in azienda](active-directory-azureadjoin-windows10-devices.md).

Per eseguire l'aggiunta ad Azure AD, seguire questa procedura (non disponibile per Windows Phone):

**Aggiornamento dell'anniversario di Windows 10**

1. Aprire l'app **Impostazioni**.
2. Fare clic su **Account** > **Access work or school** (Accesso azienda o istituto di istruzione).
3. Fare clic su **Connect**.
4. Fare clic su **Join this device to Azure AD** (Aggiungi il dispositivo ad Azure AD) nella parte inferiore della pagina.
5. Eseguire l'autenticazione nell'organizzazione, fornire la prova dell'autenticazione a più fattori, se necessario, e quindi seguire la procedura fino al termine.
6. Disconnettersi e quindi eseguire l'accesso usando l'account aziendale.
7. Provare ad accedere di nuovo all'applicazione.

**Aggiornamento di novembre 2015 di Windows 10**

1. Aprire l'app **Impostazioni**.
2. Fare clic su **Sistema** > **Informazioni su**.
3. Fare clic su **Aggiungi ad Azure AD**.
4. Eseguire l'autenticazione nell'organizzazione, fornire la prova dell'autenticazione a più fattori, se necessario, e quindi seguire la procedura fino al termine.
5. Disconnettersi e quindi eseguire l'accesso usando l'account aziendale (account Azure AD).
6. Provare ad accedere di nuovo all'applicazione.

Per aggiungere l'account aziendale o dell'istituto di istruzione, seguire questa procedura:

**Aggiornamento dell'anniversario di Windows 10**

1. Aprire l'app **Impostazioni**.
2. Fare clic su **Account** > **Access work or school** (Accesso azienda o istituto di istruzione).
3. Fare clic su **Connect**.
4. Eseguire l'autenticazione nell'organizzazione, fornire la prova dell'autenticazione a più fattori, se necessario, e quindi seguire la procedura fino al termine.
5. Provare ad accedere di nuovo all'applicazione.

**Aggiornamento di novembre 2015 di Windows 10**

1. Aprire l'app **Impostazioni**.
2. Fare clic su **Account** > **Your accounts** (Account personali).
3. Fare clic su **Aggiungi account aziendale o dell'istituto di istruzione**.
4. Eseguire l'autenticazione nell'organizzazione, fornire la prova dell'autenticazione a più fattori, se necessario, e quindi seguire la procedura fino al termine.
5. Provare ad accedere di nuovo all'applicazione.

Se il dispositivo non è aggiunto al dominio ed esegue Windows 8.1, è possibile eseguire l'aggiunta all'area di lavoro e la registrazione in Microsoft Intune seguendo questa procedura:

1. Aprire **Impostazioni PC**.
2. Fare clic su **Rete** > **Rete aziendale**.
3. Fare clic su **Accedi**.
4. Eseguire l'autenticazione nell'organizzazione, fornire la prova dell'autenticazione a più fattori, se necessario, e quindi seguire la procedura fino al termine.
5. Fare clic su **Attiva**.
6. Attendere il completamento.
7. Provare ad accedere di nuovo all'applicazione.

## Browser non supportato
Se si accede all'applicazione dai browser seguenti, verrà visualizzata una pagina simile a quella illustrata prima:

* Chrome, Firefox o qualsiasi altro browser diverso da Microsoft Edge o Microsoft Internet Explorer in Windows 10 o Windows Server 2016.
* Firefox in Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.

![Messaggi "Non è possibile accedervi da qui" per browser non supportati](./media/active-directory-conditional-access-device-remediation/02.png "Scenario")

L'unica correzione consiste nell'usare un browser supportato dall'applicazione per la piattaforma del dispositivo in uso.

## Accesso da un dispositivo iOS
Per le istruzioni per iPhone o iPad, visitare di nuovo questa pagina tra qualche tempo.

## Accesso da un dispositivo Android
Per le istruzioni per telefoni o tablet Android, visitare di nuovo questa pagina tra qualche tempo.

## Passaggi successivi
[Accesso condizionale di Azure Active Directory](active-directory-conditional-access.md)

<!----HONumber=AcomDC_0831_2016-->