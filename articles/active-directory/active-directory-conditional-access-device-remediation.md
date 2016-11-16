---
title: Risoluzione dei problemi di accesso di Azure Active Directory | Documentazione Microsoft
description: "Informazioni sulle procedure che è possibile eseguire per risolvere i problemi di accesso relativi alle risorse online dell&quot;organizzazione."
services: active-directory
keywords: accesso condizionale basato su dispositivo, registrazione dispositivo, abilitare registrazione dispositivo, registrazione dispositivo e software MDM
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/11/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd2076f22c6048fda83d6da3b069e2805afb453f


---
# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Risoluzione dei problemi di accesso di Azure Active Directory
Quando si prova ad accedere alla Intranet SharePoint Online dell'organizzazione, viene visualizzato un messaggio di errore di "accesso negato". Come si deve procedere?


Questo articolo illustra la procedura di correzione che consente di risolvere i problemi di accesso relativi alle risorse online dell'organizzazione.

Per risolvere i problemi di accesso di Azure Active Directory (Azure AD), andare alla sezione dell'articolo relativa alla piattaforma del dispositivo:

* Dispositivo Windows
* Dispositivo iOS (l'assistenza per iPhone e iPad sarà disponibile a breve)
* Dispositivo Android (l'assistenza per telefoni e tablet Android sarà disponibile a breve)

## <a name="access-from-a-windows-device"></a>Accesso da un dispositivo Windows
Se il dispositivo esegue una delle piattaforme seguenti, cercare nelle sezioni successive il messaggio di errore visualizzato quando si prova ad accedere a un'applicazione o a un servizio:

* Windows 10
* Windows 8.1
* Windows 8
* Windows 7
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2

### <a name="device-is-not-registered"></a>Dispositivo non registrato
Se il dispositivo non è registrato in Azure AD e l'applicazione è protetta con criteri basati sul dispositivo, potrebbe essere visualizzata una pagina con uno di questi messaggi di errore:

![Messaggi "Non è possibile accedervi da qui" per dispositivi non registrati](./media/active-directory-conditional-access-device-remediation/01.png "Scenario")

Se il dispositivo è aggiunto al dominio di Active Directory dell'organizzazione, provare a eseguire le operazioni seguenti:

1. Assicurarsi di accedere a Windows con il proprio account aziendale (account Active Directory).
2. Connettersi alla rete aziendale tramite una rete privata virtuale (VPN) o DirectAccess.
3. Dopo aver stabilito la connessione, premere il tasto WINDOWS+L per bloccare la sessione di Windows.
4. Immettere le credenziali dell'account aziendale per sbloccare la sessione di Windows.
5. Attendere un minuto e quindi provare di nuovo ad accedere all'applicazione o al servizio.
6. Se viene visualizzata la stessa pagina, fare clic sul collegamento **Altri dettagli** e quindi riferire i dettagli all'amministratore.

Se il dispositivo non è aggiunto al dominio ed esegue Windows 10, sono disponibili due opzioni:

* Eseguire l'aggiunta ad Azure AD
* Aggiungere l'account aziendale o dell'istituto di istruzione a Windows

Per informazioni sulle differenze tra queste opzioni, vedere [Uso di dispositivi Windows 10 in azienda](active-directory-azureadjoin-windows10-devices.md).

Per effettuare l'aggiunta ad Azure AD, eseguire questa procedura per la piattaforma su cui è eseguito il dispositivo. L'aggiunta ad Azure AD non è disponibile nei telefoni Windows.

**Aggiornamento dell'anniversario di Windows 10**

1. Aprire l'app **Impostazioni** .
2. Fare clic su **Account** > **Accedi all'azienda o all'istituto di istruzione**.
3. Fare clic su **Connetti**.
4. Fare clic su **Aggiungi il dispositivo ad Azure Active Directory**.
5. Eseguire l'autenticazione nell'organizzazione, fornire l'autenticazione a più fattori, se richiesta, e quindi seguire la procedura visualizzata.
6. Disconnettersi e quindi eseguire l'accesso con l'account aziendale.
7. Provare ad accedere di nuovo all'applicazione.

**Aggiornamento di novembre 2015 di Windows 10**

1. Aprire l'app **Impostazioni** .
2. Fare clic su **Sistema** > **Informazioni**.
3. Fare clic su **Aggiungi ad Azure AD**.
4. Eseguire l'autenticazione nell'organizzazione, fornire l'autenticazione a più fattori, se richiesta, e quindi seguire la procedura visualizzata.
5. Disconnettersi e quindi eseguire l'accesso con l'account aziendale (l'account Azure AD).
6. Provare ad accedere di nuovo all'applicazione.

Per aggiungere l'account aziendale o dell'istituto di istruzione, seguire questa procedura:

**Aggiornamento dell'anniversario di Windows 10**

1. Aprire l'app **Impostazioni** .
2. Fare clic su **Account** > **Accedi all'azienda o all'istituto di istruzione**.
3. Fare clic su **Connetti**.
4. Eseguire l'autenticazione nell'organizzazione, fornire l'autenticazione a più fattori, se richiesta, e quindi seguire la procedura visualizzata.
5. Provare ad accedere di nuovo all'applicazione.

**Aggiornamento di novembre 2015 di Windows 10**

1. Aprire l'app **Impostazioni** .
2. Fare clic su **Account** > **Your accounts** (Account personali).
3. Fare clic su **Aggiungi account aziendale o dell'istituto di istruzione**.
4. Eseguire l'autenticazione nell'organizzazione, fornire l'autenticazione a più fattori, se richiesta, e quindi seguire la procedura visualizzata.
5. Provare ad accedere di nuovo all'applicazione.

Se il dispositivo non è aggiunto al dominio ed esegue Windows 8.1, per eseguire l'aggiunta all'area di lavoro e la registrazione in Microsoft Intune, seguire questa procedura:

1. Aprire **Impostazioni PC**.
2. Fare clic su **Rete** > **Rete aziendale**.
3. Fare clic su **Accedi**.
4. Eseguire l'autenticazione nell'organizzazione, fornire l'autenticazione a più fattori, se richiesta, e quindi seguire la procedura visualizzata.
5. Fare clic su **Attiva**.
6. Provare ad accedere di nuovo all'applicazione.

### <a name="browser-is-not-supported"></a>Browser non supportato
L'accesso potrebbe essere negato se si prova ad accedere a un'applicazione o a un servizio usando uno dei browser seguenti:

* Chrome, Firefox o qualsiasi altro browser diverso da Microsoft Edge o Microsoft Internet Explorer in Windows 10 o Windows Server 2016
* Firefox in Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2

Verrà visualizzata la pagina di errore seguente:

![Messaggi "Non è possibile accedervi da qui" per browser non supportati](./media/active-directory-conditional-access-device-remediation/02.png "Scenario")

L'unica correzione consiste nell'usare un browser supportato dall'applicazione per la piattaforma del dispositivo in uso.

## <a name="next-steps"></a>Passaggi successivi
[Accesso condizionale di Azure Active Directory](active-directory-conditional-access.md)




<!--HONumber=Nov16_HO2-->


