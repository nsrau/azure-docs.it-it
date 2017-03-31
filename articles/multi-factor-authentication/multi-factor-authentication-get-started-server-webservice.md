---
title: Servizio Web app per dispositivi mobili del server Azure MFA | Microsoft Docs
description: L&quot;app Microsoft Authenticator offre un&quot;opzione di autenticazione fuori banda aggiuntiva.  Consente al server MFA di inviare notifiche push agli utenti.
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/25/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 20afeb3ba290ddf728d2b52c076c7a57fadc77c6
ms.openlocfilehash: 4014bf0217e25ea9bc8473ef2383279e5eb79b87
ms.lasthandoff: 02/28/2017

---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Abilitare l'autenticazione con il server Azure Multi-Factor Authentication

L'app Microsoft Authenticator offre un'opzione di verifica fuori banda aggiuntiva. Invece di effettuare una chiamata automatizzata o di inviare un SMS all'utente durante l'accesso, Azure Multi-Factor Authentication inoltra una notifica push all'app Microsoft Authenticator sul tablet o sullo smartphone dell'utente. L'utente tocca semplicemente **Verifica** (o immette un PIN e tocca "Esegui autenticazione") nell'app per eseguire l'accesso. 

Quando la ricezione del telefono non è affidabile, è preferibile usare un'app per dispositivi mobili per la verifica in due passaggi. Se si usa l'app come un generatore di token OATH, non è necessaria una connessione di rete o Internet. 

Per installare il portale utenti su un server diverso dal server Azure Multi-Factor Authentication, è necessario eseguire questi passaggi:

1. Installare l'SDK del servizio Web
2. Installare il servizio Web app per dispositivi mobili
3. Configurare le impostazioni dell'app per dispositivi mobili nel server Azure Multi-Factor Authentication
4. Attivare l'app Microsoft Authenticator per gli utenti finali

## <a name="requirements"></a>Requisiti

Per usare l'app Microsoft Authenticator, è necessario quanto segue in modo che l'app possa comunicare correttamente con il servizio Web app per dispositivi mobili:

* Server Azure Multi-Factor Authentication v6.0 o versioni successive
* Installare il servizio Web app per dispositivi mobili in un server Web con connessione Internet che esegue Microsoft® [Internet Information Services (IIS) 7.x o versione successiva](http://www.iis.net/)
* ASP.NET v4.0.30319 deve essere installato, registrato e impostato su Consentito
* I servizi ruolo obbligatori includono ASP.NET e Compatibilità metabase IIS 6
* Il servizio Web app per dispositivi mobili deve essere accessibile tramite un URL pubblico
* Il servizio Web app per dispositivi mobili deve essere protetto con un certificato SSL.
* Installare l'SDK del servizio Web Azure Multi-Factor Authentication in IIS 7.x o versione successiva sullo stesso server di Azure Multi-Factor Authentication
* L'SDK del servizio Web Azure Multi-Factor Authentication deve essere protetto con un certificato SSL.
* Il servizio Web app per dispositivi mobili deve essere in grado di connettersi all'SDK servizio Web Azure Multi-Factor Authentication tramite SSL
* Il servizio Web app per dispositivi mobili deve essere in grado di eseguire l'autenticazione all'SDK servizio Web Azure MFA usando le credenziali di un account di servizio membro del gruppo di sicurezza "PhoneFactor Admins". Questo account e gruppo del servizio esistono in Active Directory se il server Azure Multi-Factor Authentication si trova su un server appartenente a un dominio. Questo account del servizio e il gruppo esistono in locale sul server Azure Multi-Factor Authentication se non è aggiunto a un dominio.


## <a name="install-the-web-service-sdk"></a>Installare l'SDK del servizio Web
Se l'SDK del servizio Web Azure Multi-Factor Authentication non è già installato nel server Azure Multi-Factor Authentication (MFA), accedere al server e aprire il server Azure MFA. 

1. Fare clic sull'icona dell'SDK del servizio Web.
2. Fare clic su **Installa SDK servizio Web** e seguire le istruzioni visualizzate. 

L'SDK servizio Web deve essere protetto con un certificato SSL. Un certificato autofirmato è accettabile per questo scopo. Importare il certificato nell'archivio "Autorità di certificazione radice attendibili" dell'account del computer locale sul server Web del portale utenti per poter essere considerato attendibile all'avvio della connessione SSL.

![Configurazione](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

## <a name="install-the-mobile-app-web-service"></a>Installare il servizio Web app per dispositivi mobili
Prima di installare il servizio Web app per dispositivi mobili, tenere presente i dettagli seguenti:

* Se il portale utenti di Azure MFA è già installato nel server con connessione Internet, il nome utente, la password e l'URL per SDK servizio Web possono essere copiati dal file web.config del portale utenti.
* È utile aprire un browser Web nel server Web connesso a Internet e andare all'URL dell'SDK servizio Web riportato nel file web.config. Se il browser è in grado di accedere al servizio Web, chiederà le credenziali. Immettere il nome utente e la password immessi nel file web.config esattamente come visualizzati nel file. Assicurarsi che non vengano visualizzati errori o avvisi relativi al certificato.
* Se un proxy inverso o un firewall si trova prima del server Web del servizio Web app per dispositivi mobili ed esegue l'offload SSL, è possibile modificare il file web.config del servizio Web app per dispositivi mobili in modo che il servizio Web app per dispositivi mobili possa usare il protocollo http anziché https. SSL è comunque richiesto dall'app per dispositivi mobili per il proxy inverso o per il firewall. Aggiungere la chiave seguente alla sezione \<appSettings\>: 

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-service"></a>Installare il servizio

1. Aprire Esplora risorse sul server Azure Multi-Factor Authentication e andare alla cartella in cui è installato il server Azure MFA (generalmente C:\Program Files\Azure Multi-Factor Authentication). Scegliere la versione a 32 bit o 64 bit del file di installazione Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup. Copiare il file di installazione nel server connesso a Internet.

2. Sul server Web connesso a Internet, eseguire il file di installazione con diritti di amministratore. Aprire un prompt dei comandi come amministratore e andare al percorso in cui è stato copiato il file di installazione.

3. Eseguire il file di installazione Multi-Factor AuthenticationMobileAppWebServiceSetup, se lo si desidera modificare il sito e assegnare alla directory virtuale un nome breve, ad esempio "PA".

  Un nome della directory virtuale breve è consigliato poiché gli utenti devono immettere l'URL del servizio Web app per dispositivi mobili nel dispositivo mobile durante l'attivazione.

4. Dopo aver completato l'installazione di Azure Multi-Factor AuthenticationMobileAppWebServiceSetup, andare a C:\inetpub\wwwroot\PA (o alla directory appropriata in base al nome della directory virtuale) e modificare il file web.config. 

5. Individuare le chiavi WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD. Impostare i valori per il nome utente e la password di un account di servizio membro del gruppo di sicurezza PhoneFactor Admins. Può trattarsi dello stesso account utilizzato come identità del portale utenti di Azure Multi-Factor Authentication se è stato precedentemente installato. Assicurarsi di immettere il nome utente e la password tra virgolette alla fine della riga (value=””/>). Usare un nome utente completo, ad esempio dominio\nome utente o computer\nome utente.  

6. Individuare l'impostazione pfMobile App Web Service_pfwssdk_PfWsSdk setting. Sostituire il valore di *http://localhost:4898/PfWsSdk.asmx* con l'URL dell'SDK servizio Web in esecuzione nel server Azure Multi-Factor Authentication (ad esempio https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). 

  Poiché viene utilizzato SSL per la connessione, è necessario fare riferimento all'SDK servizio Web per il nome del server e non per l'indirizzo IP. Il certificato SSL viene rilasciato per il nome del server e l'URL usato deve corrispondere al nome sul certificato. Il nome del server potrebbe non essere risolto in un indirizzo IP dal server con connessione Internet. In questo caso aggiungere una voce al file host sul server per il mapping del nome del server Azure Multi-Factor Authentication con il relativo indirizzo IP. Salvare il file web.config dopo che sono state apportate le modifiche.

7. Se il sito Web in cui è installato il servizio Web app per dispositivi mobili non è già stato associato a un certificato firmato pubblicamente, installare il certificato sul server, aprire Gestione IIS e associare il certificato al sito Web.

8. Aprire un Web browser da qualsiasi computer e andare all'URL in cui è stato installato il servizio Web app per dispositivi mobili (ad esempio, https://www.publicwebsite.com/PA). Assicurarsi che non vengano visualizzati errori o avvisi relativi al certificato.

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configurare le impostazioni dell'app per dispositivi mobili nel server Azure Multi-Factor Authentication
Il servizio Web app per dispositivi mobili è ora installato. È necessario configurare il server Azure Multi-Factor Authentication per l'utilizzo con il portale.

1. Nel server di Azure MFA, fare clic sull'icona del portale per gli utenti. Se gli utenti sono autorizzati a controllare i metodi di autenticazione, nella scheda Impostazioni, in **Consenti agli utenti di selezionare il metodo**, selezionare **App per dispositivi mobili**. Se questa funzionalità non è attivata, agli utenti finali verrà chiesto di contattare l'Help Desk per completare l'attivazione dell'app per dispositivi mobili.
2. Selezionare la casella **Consenti agli utenti di attivare l'app mobile**.
3. Selezionare la casella **Consenti registrazione utente**.
4. Fare clic sull'icona dell'app per dispositivi mobili.
5. Immettere l'URL usato con la directory virtuale creata durante l'installazione di Azure Multi-Factor AuthenticationMobileAppWebServiceSetup. È necessario inserire un nome dell'account nello spazio fornito. Il nome della società verrà visualizzato nell'applicazione mobile. Se lasciato vuoto, verrà visualizzato il nome del provider di Multi-Factor Authentication creato nel portale di Azure classico.

<center>![Configurazione](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

