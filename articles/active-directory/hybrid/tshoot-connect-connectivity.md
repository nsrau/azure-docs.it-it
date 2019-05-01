---
title: 'Azure AD Connect: Risolvere i problemi di Azure AD i problemi di connettività | Microsoft Docs'
description: Descrive come risolvere i problemi di connettività con Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7519f47037d2d7ff37564ab27c1cc58b65ff6c14
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64572790"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Risolvere i problemi di connettività di Azure Active Directory
Questo articolo illustra il funzionamento della connettività tra Azure AD Connect e Azure AD e come risolverne i problemi. Questi problemi si verificano con maggiore probabilità in un ambiente con un server proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Risolvere i problemi di connettività nell'Installazione guidata
Azure AD Connect usa l'autenticazione moderna con la libreria ADAL per l'autenticazione. L'Installazione guidata e il motore di sincronizzazione richiedono machine.config per una corretta configurazione, in quanto si tratta di due applicazioni .NET.

Questo articolo illustra in che modo Fabrikam si connette ad Azure AD tramite il proxy. Il server proxy è denominato fabrikamproxy e usa la porta 8080.

Prima di tutto è necessario verificare che [**machine.config**](how-to-connect-install-prerequisites.md#connectivity) sia configurato correttamente.  
![machineconfig](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> In alcuni blog non Microsoft è invece documentato che le modifiche devono essere apportate al file miiserver.exe.config. Questo file viene però sovrascritto a ogni aggiornamento, quindi anche se funziona durante l'installazione iniziale, il sistema smetterà di funzionare al primo aggiornamento. Per questo motivo è consigliabile aggiornare machine.config.
>
>

Per il server proxy devono essere aperti anche gli URL necessari. L'elenco ufficiale è documentato in [URL e intervalli di indirizzi IP per Office 365 ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Nella tabella seguente sono riportate le impostazioni minime relative agli URL assolutamente indispensabili per potersi connettere ad Azure AD. L'elenco non include le funzionalità facoltative, ad esempio il writeback delle password o Azure AD Connect Health. Le impostazioni documentate di seguito sono finalizzate alla risoluzione dei problemi relativi alla configurazione iniziale.

| URL | Porta | DESCRIZIONE |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Usate per scaricare gli elenchi di CRL. |
| \*.verisign.com |HTTP/80 |Usate per scaricare gli elenchi di CRL. |
| \*.entrust.net |HTTP/80 |Usato per scaricare gli elenchi di CRL per MFA. |
| \*.windows.net |HTTPS/443 |Usato per accedere ad Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Usato per MFA. |
| \*.microsoftonline.com |HTTPS/443 |Usato per configurare la directory di Azure AD e i dati di importazione/esportazione. |

## <a name="errors-in-the-wizard"></a>Errori nella procedura guidata
L'Installazione guidata usa due diversi contesti di sicurezza. Nella pagina **Connessione ad Azure AD** usa l'utente attualmente connesso. Nella pagina **Configura** passa all'[account che esegue il servizio per il motore di sincronizzazione](reference-connect-accounts-permissions.md#adsync-service-account). La presenza di un eventuale problema sarà probabilmente già evidente nella pagina **Connessione ad Azure AD** della procedura guidata, in quanto la configurazione del proxy è globale.

Di seguito sono riportati i problemi più comuni che vengono visualizzati nell'Installazione guidata.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>L'Installazione guidata non è stata configurata correttamente
Questo errore viene visualizzato quando la procedura guidata non riesce a raggiungere il proxy.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Se viene visualizzato questo errore, verificare che [machine.config](how-to-connect-install-prerequisites.md#connectivity) sia stato configurato correttamente.
* Se il file è corretto, seguire i passaggi in [Verificare la connettività del proxy](#verify-proxy-connectivity) per vedere se il problema è presente anche all'esterno della procedura guidata.

### <a name="a-microsoft-account-is-used"></a>Viene usato un account Microsoft
Se si usa un **account Microsoft** anziché un account **dell'istituto di istruzione o dell'organizzazione**, viene visualizzato un errore generico.  
![Viene usato un account Microsoft](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>L'endpoint MFA non è raggiungibile
Questo errore viene visualizzato se l'endpoint **https://secure.aadcdn.microsoftonline-p.com** non è raggiungibile e l'amministratore globale ha abilitato l'autenticazione MFA.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Se viene visualizzato questo errore, verificare che l'endpoint **secure.aadcdn.microsoftonline-p.com** sia stato aggiunto al proxy.

### <a name="the-password-cannot-be-verified"></a>La password non può essere verificata
Se l'Installazione guidata riesce a connettersi ad Azure AD, ma non è possibile verificare la password, viene visualizzato questo errore:  
![Password errata.](./media/tshoot-connect-connectivity/badpassword.png)

* È una password temporanea e deve essere modificata? È effettivamente la password corretta? Provare ad accedere a https://login.microsoftonline.com da un computer diverso dal server di Azure AD Connect e verificare che l'account sia utilizzabile.

### <a name="verify-proxy-connectivity"></a>Verificare la connettività del proxy
Per verificare se il server Azure AD Connect può effettivamente connettersi al proxy e a Internet, usare alcuni comandi di PowerShell per controllare se il proxy consente o meno le richieste Web. Al prompt di PowerShell eseguire `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. Tecnicamente la prima chiamata viene effettuata a https://login.microsoftonline.com e anche questo URI funziona, ma la risposta dell'altro URI è più veloce.

PowerShell usa la configurazione presente in machine.config per contattare il proxy. Le impostazioni in winhttp/netsh non dovranno influire su questi cmdlet.

Se il proxy è configurato correttamente, viene visualizzato lo stato corrispondente alla riuscita dell'operazione: ![proxy200](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Se viene visualizzato il messaggio **Impossibile effettuare la connessione al server remoto**, PowerShell sta provando a effettuare una chiamata diretta senza usare il proxy oppure il DNS non è configurato correttamente. Verificare che il file **machine.config** sia configurato correttamente.
![unabletoconnect](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Se il proxy non è configurato correttamente, verrà visualizzato un errore: ![proxy200](./media/tshoot-connect-connectivity/invokewebrequest403.png)
![proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Tipi di errore | Testo dell'errore | Comment |
| --- | --- | --- |
| 403 |Accesso negato |Il proxy non è stato aperto per l'URL richiesto. Rivedere la configurazione del proxy e assicurarsi che gli [URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) siano stati aperti. |
| 407 |Autenticazione proxy obbligatoria |Il server proxy ha richiesto l'accesso, che non è stato eseguito. Se il server proxy richiede l'autenticazione, assicurarsi che tale impostazione sia configurata in machine.config. Verificare anche di usare account di dominio sia per l'utente che esegue la procedura guidata sia per l'account del servizio. |

### <a name="proxy-idle-timeout-setting"></a>Impostazione del timeout di inattività del proxy
Quando Azure AD Connect invia una richiesta di esportazione ad Azure AD, potrebbero volerci fino a 5 minuti affinché Azure AD elabori la richiesta prima di generare una risposta. Questo può verificarsi soprattutto se sono presenti un numero di oggetti del gruppo con appartenenza a un gruppo di grandi dimensioni incluso nella stessa richiesta di esportazione. Verificare che il timeout di inattività del proxy sia configurato per essere superiore ai 5 minuti. In caso contrario, è possibile che si verifichino problemi di connettività intermittente con Azure Active Directory nel server di Azure AD Connect.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Modello di comunicazione tra Azure AD Connect e Azure AD
Se sono stati eseguiti tutti i passaggi precedenti e ancora non è possibile connettersi, si può iniziare a esaminare i log di rete. Questa sezione documenta un normale modello di connettività riuscita. Sono elencati anche alcuni diversivi comuni che possono essere ignorati durante la lettura dei log di rete.

* Vengono effettuate chiamate a https://dc.services.visualstudio.com. Non è necessario che questo URL sia aperto nel proxy perché l'installazione riesca e le chiamate possono essere ignorate.
* La risoluzione DNS elenca gli host effettivi che devono essere presenti nello spazio dei nomi DNS nsatc.net e altri spazi dei nomi non in microsoftonline.com. Non sono tuttavia presenti richieste di servizi Web su nomi di server effettivi e non è necessario aggiungere tali URL al proxy.
* Gli endpoint adminwebservice e provisioningapi sono endpoint di individuazione e servono per trovare l'endpoint effettivo da usare. Questi endpoint variano in base al paese.

### <a name="reference-proxy-logs"></a>Log del proxy di riferimento
Ecco il dump del log di un proxy effettivo e la pagina dell'Installazione guidata dalla quale è stato rilevato. Le voci duplicate per lo stesso endpoint sono state rimosse. Questa sezione può essere usata come riferimento per i log di rete e proxy in uso. È possibile che gli endpoint effettivi siano diversi nell'ambiente in uso, in particolare gli URL riportati in *corsivo*.

**Connessione ad Azure AD**

| Tempo | URL |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Configura**

| Tempo | URL |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Sincronizzazione iniziale**

| Tempo | URL |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-anchor*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Errori di autenticazione
Questa sezione illustra gli errori che possono essere restituiti da ADAL (la libreria di autenticazione usata da Azure AD Connect) e PowerShell. La spiegazione dell'errore può essere utile per comprendere i passaggi successivi.

### <a name="invalid-grant"></a>Concessione non valida
Nome utente o password non validi. Per altre informazioni, vedere [La password non può essere verificata](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Tipo di utente sconosciuto
Non è possibile trovare o risolvere la directory di Azure AD. Si tenta di accedere con un nome utente in un dominio non verificato?

### <a name="user-realm-discovery-failed"></a>Individuazione dell'area di autenticazione utente non riuscita
Problemi di configurazione di rete o del proxy. Non è possibile raggiungere la rete. Vedere [Risolvere i problemi di connettività nell'Installazione guidata](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Password utente scaduta
Le credenziali sono scadute. Modificare la password.

### <a name="authorization-failure"></a>Errore di autorizzazione
Non è stato possibile autorizzare l'utente a eseguire azioni in Azure AD.

### <a name="authentication-canceled"></a>Autenticazione annullata
La sfida di multi-factor authentication (MFA) è stata annullata.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Connessione a MS Online non riuscita
L'autenticazione ha avuto esito positivo, ma Azure AD PowerShell ha un problema di autenticazione.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>È necessario il ruolo di amministratore globale di Azure AD
Utente autenticato correttamente. All'utente non è stato assegnato il ruolo di amministratore globale. In [questo modo è possibile assegnare il ruolo di amministratore globale](../users-groups-roles/directory-assign-admin-roles.md) all'utente. 

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Privileged Identity Management (PIM) abilitata
L'autenticazione ha avuto esito positivo. Privileged Identity Management è abilitata e l'utente attualmente non è un amministratore globale. Per altre informazioni, vedere [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Informazioni sulla società non disponibili
L'autenticazione ha avuto esito positivo. Impossibile recuperare le informazioni aziendali da Azure AD.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Informazioni sul dominio non disponibili
L'autenticazione ha avuto esito positivo. Impossibile recuperare le informazioni sul dominio da Azure AD.

### <a name="unspecified-authentication-failure"></a>Errore di autenticazione non specificato
Visualizzata come un errore imprevisto nell'Installazione guidata, può verificarsi se si tenta di usare un **Account Microsoft** anziché un **account dell'istituto di istruzione o dell'organizzazione**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Procedure di risoluzione dei problemi per le versioni precedenti.
L'Assistente per l'accesso è stato ritirato a partire dalle versioni con numero di build 1.1.105.0, rilasciata nel mese di febbraio 2016. Questa sezione e la configurazione non dovrebbero essere più necessarie, ma vengono conservate come riferimento.

Per consentire il funzionamento dell'Assistente per l'accesso, è necessario configurare winhttp Questa configurazione può essere eseguita con [ **netsh**](how-to-connect-install-prerequisites.md#connectivity).  
![netsh](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>L'Assistente per l'accesso non è stato configurato correttamente
Questo errore viene visualizzato quando l'Assistente per l'accesso non riesce a raggiungere il proxy o il proxy non consente la richiesta.
![nonetsh](./media/tshoot-connect-connectivity/nonetsh.png)

* Se viene visualizzato questo errore, esaminare la configurazione del proxy in [netsh](how-to-connect-install-prerequisites.md#connectivity) e verificare che sia corretta.
  ![netshshow](./media/tshoot-connect-connectivity/netshshow.png)
* Se il file è corretto, seguire i passaggi in [Verificare la connettività del proxy](#verify-proxy-connectivity) per vedere se il problema è presente anche all'esterno della procedura guidata.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
