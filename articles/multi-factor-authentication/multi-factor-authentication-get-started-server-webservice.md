---
title: Servizio Web app per dispositivi mobili del server Azure MFA | Microsoft Docs
description: L'app Microsoft Authenticator offre un'opzione di autenticazione fuori banda aggiuntiva.  Consente al server MFA di inviare notifiche push agli utenti.
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: alexwe
ms.custom: it-pro
ms.openlocfilehash: bf758d1241f2a56eba4d5c92ace713d6e563df65
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Abilitare l'autenticazione con il server Azure Multi-Factor Authentication

L'app Microsoft Authenticator offre un'opzione di verifica fuori banda aggiuntiva. Invece di effettuare una chiamata automatizzata o di inviare un SMS all'utente durante l'accesso, Azure Multi-Factor Authentication inoltra una notifica push all'app Microsoft Authenticator sul tablet o sullo smartphone dell'utente. L'utente tocca semplicemente **Verifica** (o immette un PIN e tocca "Esegui autenticazione") nell'app per eseguire l'accesso.

Quando la ricezione del telefono non è affidabile, è preferibile usare un'app per dispositivi mobili per la verifica in due passaggi. Se si usa l'app come un generatore di token OATH, non è necessaria una connessione di rete o Internet.

A seconda dell'ambiente, è possibile distribuire il servizio Web per app per dispositivi mobili nello stesso server in cui è in esecuzione il server Azure Multi-Factor Authentication o in un altro server con connessione Internet.

## <a name="requirements"></a>Requisiti

Per usare l'app Microsoft Authenticator, è necessario quanto segue in modo che l'app possa comunicare correttamente con il servizio Web app per dispositivi mobili:

* Server Azure Multi-Factor Authentication v6.0 o versioni successive
* Installare il servizio Web app per dispositivi mobili in un server Web con connessione Internet che esegue Microsoft® [Internet Information Services (IIS) 7.x o versione successiva](http://www.iis.net/)
* ASP.NET v4.0.30319 deve essere installato, registrato e impostato su Consentito
* I servizi ruolo obbligatori includono ASP.NET e Compatibilità metabase IIS 6
* Il servizio Web app per dispositivi mobili deve essere accessibile tramite un URL pubblico
* Il servizio Web app per dispositivi mobili deve essere protetto con un certificato SSL.
* Installare l'SDK del servizio Web Azure Multi-Factor Authentication in IIS 7.x o versione successiva **nello stesso server in cui è in esecuzione il server Azure Multi-Factor Authentication**
* L'SDK del servizio Web Azure Multi-Factor Authentication deve essere protetto con un certificato SSL.
* Il servizio Web app per dispositivi mobili deve essere in grado di connettersi all'SDK servizio Web Azure Multi-Factor Authentication tramite SSL
* Il servizio Web app per dispositivi mobili deve essere in grado di eseguire l'autenticazione all'SDK servizio Web Azure MFA usando le credenziali di un account di servizio membro del gruppo di sicurezza "PhoneFactor Admins". Questo account e gruppo del servizio esistono in Active Directory se il server Azure Multi-Factor Authentication si trova su un server appartenente a un dominio. Questo account del servizio e il gruppo esistono in locale sul server Azure Multi-Factor Authentication se non è aggiunto a un dominio.

## <a name="install-the-mobile-app-web-service"></a>Installare il servizio Web app per dispositivi mobili

Prima di installare il servizio Web app per dispositivi mobili, tenere presente i dettagli seguenti:

* È necessario un account del servizio che fa parte del gruppo "PhoneFactor Admins". L'account può essere lo stesso usato per l'installazione del portale utenti.
* È utile aprire un browser Web nel server Web connesso a Internet e andare all'URL dell'SDK servizio Web riportato nel file web.config. Se il browser è in grado di accedere al servizio Web, chiederà le credenziali. Immettere il nome utente e la password immessi nel file web.config esattamente come visualizzati nel file. Assicurarsi che non vengano visualizzati errori o avvisi relativi al certificato.
* Se un proxy inverso o un firewall si trova prima del server Web del servizio Web app per dispositivi mobili ed esegue l'offload SSL, è possibile modificare il file web.config del servizio Web app per dispositivi mobili in modo che il servizio Web app per dispositivi mobili possa usare il protocollo http anziché https. SSL è comunque richiesto dall'app per dispositivi mobili per il proxy inverso o per il firewall. Aggiungere la chiave seguente alla sezione \<appSettings\>:

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-web-service-sdk"></a>Installare l'SDK del servizio Web

In entrambi gli scenari, se l'SDK del servizio Web Azure Multi-Factor Authentication **non** è già installato nel server Azure Multi-Factor Authentication (MFA), seguire questa procedura:

1. Aprire la console del server Multi-Factor Authentication.
2. Passare all'**SDK del servizio Web** e selezionare **Installa SDK servizio Web**.
3. Completare l'installazione usando le impostazioni predefinite, a meno che non sia necessario modificarle per qualche motivo.
4. Associare un certificato SSL al sito in IIS.

Per eventuali dubbi sulla configurazione di un certificato SSL in un server IIS, vedere l'articolo [How to Set Up SSL on IIS](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) (Come configurare SSL in IIS).

L'SDK servizio Web deve essere protetto con un certificato SSL. Un certificato autofirmato è accettabile per questo scopo. Importare il certificato nell'archivio "Autorità di certificazione radice disponibile nell'elenco locale" dell'account del computer locale nel server Web del portale utenti, in modo che venga considerato attendibile all'avvio della connessione SSL.

![Configurazione del server MFA, SDK servizio Web](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

### <a name="install-the-service"></a>Installare il servizio

1. **Nel server MFA** passare al percorso di installazione.
2. Passare alla cartella in cui è installato il server Azure MFA, l'impostazione predefinita è **C:\Program Files\Azure Multi-Factor Authentication**.
3. Trovare il file di installazione **MultiFactorAuthenticationMobileAppWebServiceSetup64**. Se il server **non** ha una connessione Internet, copiare il file di installazione nel server connesso a Internet.
4. Se il server MFA **non** ha una connessione Internet, passare al **server con connessione Internet**.
5. Eseguire il file di installazione **MultiFactorAuthenticationMobileAppWebServiceSetup64** come amministratore, modificare il sito se necessario ed eventualmente sostituire il nome della directory virtuale con un nome breve.
6. Dopo aver completato l'installazione, passare a **C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService** o alla directory appropriata in base al nome della directory virtuale e modificare il file Web.Config.

   * Trovare la chiave **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** e sostituire **value=""** con **value="DOMAIN\User"**, dove DOMAIN\User è un account del servizio che fa parte del gruppo "PhoneFactor Admins".
   * Trovare la chiave **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** e sostituire **value=""** con **value="Password"**, dove Password è la password dell'account del servizio specificato alla riga precedente.
   * Trovare l'impostazione **pfMobile App Web Service_pfwssdk_PfWsSdk** e sostituire il valore **http://localhost:4898/PfWsSdk.asmx** con l'URL dell'SDK servizio Web, ad esempio https://mfa.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx.
   * Salvare il file Web.Config e chiudere il Blocco note.

   > [!NOTE]
   > Dato che per la connessione viene usato SSL, è necessario fare riferimento all'SDK servizio Web come **nome di dominio completo** e **non come indirizzo IP**. Il certificato SSL viene rilasciato per il nome di dominio completo e l'URL usato deve corrispondere al nome sul certificato.

7. Se il sito Web in cui è installato il servizio Web per app per dispositivi mobili non è già stato associato a un certificato firmato pubblicamente, installare il certificato nel server, aprire Gestione IIS e associare il certificato al sito Web.
8. Aprire un Web browser da qualsiasi computer e passare all'URL in cui è stato installato il servizio Web per app per dispositivi mobili, ad esempio https://mfa.contoso.com/MultiFactorAuthMobileAppWebService. Assicurarsi che non vengano visualizzati errori o avvisi relativi al certificato.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configurare le impostazioni dell'app per dispositivi mobili nel server Azure Multi-Factor Authentication

Il servizio Web app per dispositivi mobili è ora installato. È necessario configurare il server Azure Multi-Factor Authentication per l'utilizzo con il portale.

1. Nella console del server Multi-Factor Authentication fare clic sull'icona del portale utenti. Se gli utenti sono autorizzati a controllare i metodi di autenticazione, nella scheda Impostazioni, in **Consenti agli utenti di selezionare il metodo**, selezionare **App per dispositivi mobili**. Se questa funzionalità non è abilitata, agli utenti finali viene richiesto di contattare l'help desk per completare l'attivazione dell'app per dispositivi mobili.
2. Selezionare la casella **Consenti agli utenti di attivare l'app mobile**.
3. Selezionare la casella **Consenti registrazione utente**.
4. Fare clic sull'icona dell'**app per dispositivi mobili**.
5. Immettere l'URL usato con la directory virtuale creata durante l'installazione di MultiFactorAuthenticationMobileAppWebServiceSetup64, ad esempio https://mfa.contoso.com/MultiFactorAuthMobileAppWebService/ nel campo **URL servizio Web app mobile**.
6. Popolare il campo **Nome account** con il nome della società o dell'organizzazione da visualizzare nell'app per dispositivi mobili per questo account.
   ![Impostazioni dell'app per dispositivi mobili per la configurazione del server MFA](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)

## <a name="next-steps"></a>Passaggi successivi

- [Scenari avanzati con Azure Multi-Factor Authentication e soluzioni VPN di terze parti](multi-factor-authentication-advanced-vpn-configurations.md).