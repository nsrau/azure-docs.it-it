---
title: Portale utenti per il server Azure MFA | Documentazione Microsoft
description: "Questa è la pagina di Azure Multi-Factor Authentication che descrive come iniziare a utilizzare Azure MFA e ADFS."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 06b419fa-3507-4980-96a4-d2e3960e1772
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 2a4f64524f94a782434306f89f6ad1034297c4cc
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="user-portal-for-the-azure-multi-factor-authentication-server"></a>Portale utenti per il server Azure Multi-Factor Authentication

Il portale utenti è un sito Web IIS che consente agli utenti di registrarsi in Azure Multi-Factor Authentication (MFA) e gestire i relativi account. L'utente può modificare il numero di telefono, modificare il PIN o scegliere di ignorare la verifica in due passaggi all'accesso successivo.

Gli utenti accedono al portale utenti con il nome utente e la password normali, quindi completano una chiamata di verifica in due passaggi o rispondono a domande di sicurezza per completare l'autenticazione. Se è consentita la registrazione, l'utente può configurare numero di telefono e PIN al primo accesso al portale utenti.

È possibile configurare gli amministratori del portale utenti e concedere loro l'autorizzazione ad aggiungere nuovi utenti e aggiornare quelli esistenti.

A seconda dell'ambiente, è possibile distribuire il portale utenti nello stesso server in cui è in esecuzione il server Azure Multi-Factor Authentication o in un altro server con connessione Internet.

![Portale utenti di MFA](./media/multi-factor-authentication-get-started-portal/portal.png)

> [!NOTE]
> Il portale utenti è disponibile unicamente con il server Multi-Factor Authentication. Se si usa Multi-Factor Authentication nel cloud, gli utenti possono vedere gli articoli [Configurare l'account per la verifica in due passaggi](./end-user/multi-factor-authentication-end-user-first-time.md) o [Gestire le impostazioni per la verifica in due passaggi](./end-user/multi-factor-authentication-end-user-manage-settings.md).

## <a name="install-the-web-service-sdk"></a>Installare l'SDK del servizio Web

In entrambi gli scenari, se l'SDK del servizio Web Azure Multi-Factor Authentication **non** è già installato nel server Azure Multi-Factor Authentication (MFA), seguire questa procedura:

1. Aprire la console del server Multi-Factor Authentication.
2. Passare all'**SDK del servizio Web** e selezionare **Installa SDK servizio Web**.
3. Completare l'installazione usando le impostazioni predefinite, a meno che non sia necessario modificarle per qualche motivo.
4. Associare un certificato SSL al sito in IIS.

Per eventuali dubbi sulla configurazione di un certificato SSL in un server IIS, vedere l'articolo [How to Set Up SSL on IIS](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) (Come configurare SSL in IIS).

L'SDK servizio Web deve essere protetto con un certificato SSL. Un certificato autofirmato è accettabile per questo scopo. Importare il certificato nell'archivio "Autorità di certificazione radice disponibile nell'elenco locale" dell'account del computer locale nel server Web del portale utenti, in modo che venga considerato attendibile all'avvio della connessione SSL.

![Configurazione del server MFA, SDK servizio Web](./media/multi-factor-authentication-get-started-portal/sdk.png)

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Distribuire il portale utenti nello stesso server di Azure Multi-Factor Authentication

I prerequisiti seguenti sono necessari per l'installazione del portale utenti nello **stesso server** in cui è in esecuzione il server Azure Multi-Factor Authentication:

* IIS con ASP.NET e la compatibilità della metabase di IIS 6 (per IIS 7 o versione successiva).
* Un account con diritti di amministratore per il computer e il dominio, se applicabile. L'account deve essere autorizzato a creare gruppi di sicurezza di Active Directory.
* Proteggere il portale utenti con un certificato SSL.
* Proteggere Azure Multi-Factor Authentication Web Service SDK con un certificato SSL.

Per distribuire il portale utenti, seguire questa procedura:

1. Aprire la console del server Azure Multi-Factor Authentication e fare clic sull'icona del **portale utenti** nel menu a sinistra, quindi su **Installa portale utenti**.
2. Completare l'installazione usando le impostazioni predefinite, a meno che non sia necessario modificarle per qualche motivo.
3. Associare un certificato SSL al sito in IIS.

   > [!NOTE]
   > Il certificato SSL è in genere un certificato SSL firmato pubblicamente.

4. Aprire un Web browser da qualsiasi computer e passare all'URL in cui è stato installato il portale utenti, ad esempio https://mfa.contoso.com/MultiFactorAuth. Assicurarsi che non vengano visualizzati errori o avvisi relativi al certificato.

![Installazione del portale utenti del server MFA](./media/multi-factor-authentication-get-started-portal/install.png)

Per eventuali dubbi sulla configurazione di un certificato SSL in un server IIS, vedere l'articolo [How to Set Up SSL on IIS](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) (Come configurare SSL in IIS).

## <a name="deploy-the-user-portal-on-a-separate-server"></a>Distribuire il portale utenti in un server separato

Se il server in cui è in esecuzione il server Azure Multi-Factor Authentication non è connesso a Internet, è necessario installare il portale utenti in un **server separato connesso a Internet**.

Se l'organizzazione fa uso dell'app Microsoft Authenticator come uno dei metodi di verifica e si vuole distribuire il portale utenti nel relativo server, è necessario completare i requisiti seguenti:

* Usare la versione 6.0 o versioni successive del server Azure Multi-Factor Authentication.
* Installare il portale utenti in un server Web con connessione Internet in cui è in esecuzione Microsoft Internet Information Services (IIS) 6.x o versione successiva.
* Quando si usa IIS 6.x, controllare che ASP.NET v2.0.50727 sia installato, registrato e impostato su **Consentito**.
* Quando si usa IIS 7.x o versione successiva, IIS, che include l'autenticazione di base, ASP.NET e la compatibilità della metabase di IIS 6.
* Proteggere il portale utenti con un certificato SSL.
* Proteggere Azure Multi-Factor Authentication Web Service SDK con un certificato SSL.
* Assicurarsi che il portale utenti possa connettersi ad Azure Multi-Factor Authentication Web Service SDK tramite SSL.
* Il portale utenti deve poter eseguire l'autenticazione in Azure Multi-Factor Authentication Web Service SDK usando le credenziali di un account del servizio nel gruppo di sicurezza "PhoneFactor Admins". Se il server Azure Multi-Factor Authentication è in esecuzione in un server aggiunto a un dominio, l'account e il gruppo del servizio devono essere presenti in Active Directory. Questo account del servizio e il gruppo esistono in locale sul server Azure Multi-Factor Authentication se non è aggiunto a un dominio.

Per installare il portale utenti su un server diverso dal server Azure Multi-Factor Authentication, è necessario eseguire questi passaggi:

1. **Aprire il server Multi-Factor Authentication**, passare al percorso di installazione, ad esempio C:\Program Files\Multi-Factor Authentication Server, e copiare il file **MultiFactorAuthenticationUserPortalSetup64** in una posizione accessibile per il server con connessione Internet in cui verrà installato.
2. **Nel server Web con connessione Internet** eseguire il file di installazione MultiFactorAuthenticationUserPortalSetup64 come amministratore, modificare il sito se necessario ed eventualmente sostituire il nome della directory virtuale con un nome breve.
3. Associare un certificato SSL al sito in IIS.

   > [!NOTE]
   > Il certificato SSL è in genere un certificato SSL firmato pubblicamente.

4. Passare a **C:\inetpub\wwwroot\MultiFactorAuth**.
5. Modificare il file Web.Config nel Blocco note.

    * Trovare la chiave **"USE_WEB_SERVICE_SDK"** e sostituire **value="false"** con **value="true"**.
    * Trovare la chiave **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** e sostituire **value=""** con **value="DOMAIN\User"**, dove DOMAIN\User è un account del servizio che fa parte del gruppo "PhoneFactor Admins".
    * Trovare la chiave **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** e sostituire **value=""** con **value="Password"**, dove Password è la password dell'account del servizio specificato alla riga precedente.
    * Trovare il valore **https://www.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx** e sostituire questo URL segnaposto con l'URL dell'SDK servizio Web installato nel passaggio 2.
    * Salvare il file Web.Config e chiudere il Blocco note.

6. Aprire un Web browser da qualsiasi computer e passare all'URL in cui è stato installato il portale utenti, ad esempio https://mfa.contoso.com/MultiFactorAuth. Assicurarsi che non vengano visualizzati errori o avvisi relativi al certificato.

Per eventuali dubbi sulla configurazione di un certificato SSL in un server IIS, vedere l'articolo [How to Set Up SSL on IIS](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) (Come configurare SSL in IIS).

## <a name="configure-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Configurare le impostazioni del portale utenti nel server Azure Multi-Factor Authentication

Il portale utenti è ora installato. È necessario configurare il server Azure Multi-Factor Authentication per l'utilizzo con il portale.

1. Nella console del server Azure Multi-Factor Authentication fare clic sull'icona del **portale utenti**. Nella scheda Impostazioni immettere l'URL del portale utenti nella casella di testo **URL portale utenti**. Se la funzionalità di posta elettronica è stata abilitata, questo URL viene incluso nei messaggi di posta elettronica inviati agli utenti quando vengono importati nel server Azure Multi-Factor Authentication.
2. Scegliere le impostazioni che si desidera utilizzare nel portale utenti. Ad esempio, se gli utenti sono autorizzati a scegliere i relativi metodi di autenticazione, verificare che sia selezionata l'opzione **Consenti agli utenti di selezionare il metodo**.
3. Specificare gli amministratori nella scheda **Amministratori**. È possibile creare autorizzazioni amministrative granulari usando le caselle di controllo e i menu a discesa nelle caselle Aggiungi/Modifica.

Configurazione facoltativa:
- **Domande di sicurezza**: definire domande di sicurezza approvate per l'ambiente e la lingua in cui visualizzarle.
- **Sessioni passate**: configurare l'integrazione del portale utenti con un sito Web basato su form tramite MFA.
- **Indirizzi IP attendibili**: consentire agli utenti di ignorare l'autenticazione a più fattori quando eseguono l'autenticazione da un elenco di indirizzi IP o intervalli IP attendibili.

![Configurazione del portale utenti del server MFA](./media/multi-factor-authentication-get-started-portal/config.png)

Il server Azure Multi-Factor Authentication offre diverse opzioni per il portale utenti. La tabella seguente include un elenco di queste opzioni e una spiegazione dell'uso a cui sono destinate.

| Impostazioni del portale utenti | Descrizione |
|:--- |:--- |
| URL portale utenti | Immettere l'URL in cui viene ospitato il portale. |
| Autenticazione primaria | Specificare il tipo di autenticazione da usare quando si accede al portale. Autenticazione di Windows, Radius o LDAP. |
| Consenti agli utenti di accedere | Consente agli utenti di immettere un nome utente e una password nella pagina di accesso del portale utenti. Se l'opzione non è selezionata, le caselle risultano disattivate. |
| Consenti registrazione utente | Consente all'utente di registrarsi in Multi-Factor Authentication visualizzando una schermata di configurazione che richiede informazioni aggiuntive, ad esempio il numero di telefono. Richiedi telefono di backup consente agli utenti di specificare un numero di telefono secondario. Richiedi token OATH di terze parti consente agli utenti di specificare un token OATH di terze parti. |
| Consenti agli utenti di avviare il bypass monouso | Consente agli utenti di avviare il bypass monouso. Se un utente configura questa opzione, verrà applicata all'accesso successivo. Richiedi secondi per bypass presenta all'utente una casella in cui può modificare il valore predefinito di 300 secondi. In caso contrario, il bypass monouso è valido solo per 300 secondi. |
| Consenti agli utenti di selezionare il metodo | Consente agli utenti di specificare il metodo di contatto primario. Il metodo può essere una telefonata, un SMS, un'app per dispositivi mobili o un token OATH. |
| Consenti agli utenti di selezionare la lingua | Consente agli utenti di cambiare la lingua usata per la telefonata, l'SMS, l'app per dispositivi mobili o il token OATH. |
| Consenti agli utenti di attivare l'app mobile | Consente agli utenti di generare un codice di attivazione per completare il processo di attivazione dell'app per dispositivi mobili usato con il server.  È anche possibile impostare il numero di dispositivi in cui possono attivare l'app, specificando un numero compreso tra 1 e 10. |
| Usa domande di sicurezza per il fallback | Consente le domande di sicurezza in caso di esito negativo della verifica in due passaggi. È possibile specificare il numero di domande di sicurezza a cui deve essere fornita una risposta corretta. |
| Consenti agli utenti di associare token OATH di terze parti | Consente agli utenti di specificare un token OATH di terze parti. |
| Usa token OATH per fallback | Consente l'uso di un token OATH in caso di esito negativo della verifica in due passaggi. È anche possibile specificare il timeout della sessione in minuti. |
| Abilitazione della registrazione | Abilita la registrazione nel portale utenti. I file di log si trovano in: C:\Programmi\Multi-Factor Authentication Server\Logs. |

Queste impostazioni diventano visibili all'utente nel portale dopo la relativa abilitazione e l'accesso al portale utenti.

![Impostazioni del portale utenti](./media/multi-factor-authentication-get-started-portal/portalsettings.png)

### <a name="self-service-user-enrollment"></a>Registrazione utente in modalità self-service

Se si vuole che gli utenti accedano e si registrino, è necessario selezionare le opzioni **Consenti agli utenti di accedere** e **Consenti registrazione utente** nella scheda Impostazioni. Tenere presente che le impostazioni selezionate influiscono sull'esperienza di accesso degli utenti.

Quando ad esempio un utente accede per la prima volta al portale utenti, viene visualizzata la pagina di configurazione degli utenti di Azure Multi-Factor Authentication. A seconda della configurazione di Azure Multi-Factor Authentication, l'utente potrebbe essere in grado di selezionare il metodo di autenticazione.

Se si seleziona il metodo di verifica tramite chiamata vocale o questo metodo è stato preconfigurato, la pagina richiede di immettere il numero di telefono principale e l'interno, se applicabile. Si può anche consentire all'utente di immettere un numero di telefono di backup.

![Registrare i numeri di telefono principale e di backup](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Se l'utente deve usare un PIN quando esegue l'autenticazione, la pagina richiede di creare un PIN. Dopo aver immesso il numero di telefono e il PIN (se applicabile), l'utente fa clic sul pulsante **Chiama per autenticare utente corrente**. Azure Multi-Factor Authentication esegue una verifica tramite chiamata al numero di telefono principale dell'utente. L'utente deve rispondere alla chiamata e immettere il PIN (se applicabile) e premere il tasto # per proseguire con il passaggio successivo del processo di autoregistrazione.

Se si seleziona il metodo di verifica tramite SMS o questo metodo è stato preconfigurato, la pagina richiede il numero di telefono cellulare. Se l'utente deve usare un PIN quando esegue l'autenticazione, la pagina richiede anche di immettere un PIN.  Dopo aver immesso il numero di telefono e il PIN (se applicabile), l'utente fa clic sul pulsante **Invia SMS per autenticare utente corrente**. Azure Multi-Factor Authentication esegue la verifica tramite SMS al cellulare dell'utente. L'utente riceve l'SMS con un passcode monouso, quindi risponde al messaggio con tale passcode monouso e il proprio PIN (se applicabile).

![SMS del portale utenti](./media/multi-factor-authentication-get-started-portal/text.png)

Se si seleziona il metodo di verifica tramite app per dispositivi mobili, la pagina richiede all'utente di installare l'app Microsoft Authenticator nel dispositivo e di generare un codice di attivazione. Dopo aver installato l'app, l'utente fa clic sul pulsante Genera codice di attivazione.

> [!NOTE]
> Per usare l'app Microsoft Authenticator, l'utente deve abilitare le notifiche push per il dispositivo.

La pagina visualizza quindi un codice di attivazione e un URL con l'immagine di un codice a barre. Se l'utente deve usare un PIN quando esegue l'autenticazione, la pagina richiede anche di immettere un PIN. L'utente immette il codice di attivazione e l'URL nell'app Microsoft Authenticator o usa il lettore di codice a barre per eseguire la scansione dell'immagine del codice a barre, quindi fa clic sul pulsante Attiva.

Dopo aver completato l'attivazione, l'utente fa clic sul pulsante **Autentica adesso**. Azure Multi-Factor Authentication esegue la verifica dell'app per dispositivi mobili dell'utente. L'utente deve immettere il PIN (se applicabile) e scegliere il pulsante Esegui autenticazione nell'app per dispositivi mobili per proseguire con il passaggio successivo del processo di autoregistrazione.

Se gli amministratori hanno configurato il server Azure Multi-Factor Authentication per raccogliere domande e risposte di sicurezza, viene visualizzata la pagina Domande di sicurezza. L'utente deve selezionare quattro domande di sicurezza e fornire le risposte alle domande selezionate.

![Domande di sicurezza del portale utenti](./media/multi-factor-authentication-get-started-portal/secq.png)

A questo punto la registrazione automatica è stata completata e l'utente è connesso al portale utenti. Se la modifica del metodo è consentita dagli amministratori, gli utenti possono accedere al portale utenti in qualsiasi momento per modificare numeri di telefono, PIN, metodi di autenticazione e domande di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire il servizio Web per app per dispositivi mobili del server Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-webservice.md)