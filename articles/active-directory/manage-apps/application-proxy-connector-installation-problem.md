---
title: Problemi di installazione del connettore dell'agente proxy dell'applicazione
description: Come risolvere i problemi che potrebbero verificarsi durante l'installazione del connettore dell'agente proxy di applicazione per Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 7babe23426cafe01cadc7a5557f91896aa9bbae4
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108202"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problemi di installazione del connettore dell'agente proxy dell'applicazione

Microsoft Azure Active Directory connettore del proxy di applicazione è un componente di dominio interno che usa le connessioni in uscita per stabilire la connettività dall'endpoint disponibile del cloud al dominio interno.

## <a name="general-problem-areas-with-connector-installation"></a>Aree problematiche generali con l'installazione del connettore

Quando l'installazione di un connettore non riesce, la causa principale riguarda in genere una delle seguenti aree:

1.  **Connettività**: per completare un'installazione, è necessario che il nuovo connettore registri e definisca le future proprietà di attendibilità. Questa operazione viene eseguita connettendosi al servizio Cloud Azure Active Directory Application Proxy.

2.  **Definizione dell'attendibilità**: il nuovo connettore crea un certificato autofirmato ed effettua la registrazione al servizio cloud.

3.  **Autenticazione dell'amministratore**: durante l'installazione, l'utente deve fornire le credenziali di amministratore per completare l'installazione del connettore.

> [!NOTE]
> I log di installazione del connettore sono disponibili nella cartella% TEMP% e consentono di fornire informazioni aggiuntive su ciò che causa un errore di installazione.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Verificare la connettività al servizio proxy dell'applicazione cloud e alla pagina di accesso Microsoft

**Obiettivo:** Verificare che il computer connettore sia in grado di connettersi all'endpoint di registrazione del proxy di applicazione e alla pagina di accesso di Microsoft.

1.  Sul server del connettore, eseguire un test di porta utilizzando [Telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) o un altro strumento di test della porta per verificare che le porte 443 e 80 siano aperte.

2.  Se una di queste porte ha esito negativo, verificare che il firewall o il proxy back-end abbia accesso ai domini e alle porte richiesti. vedere [preparare l'ambiente locale](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Aprire un browser (scheda separata) e passare alla pagina Web `https://login.microsoftonline.com`, quindi assicurarsi di poter accedere a tale pagina.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>Verificare il supporto dei componenti computer e backend per il certificato di attendibilità del proxy di applicazione

**Obiettivo:** Verificare che il computer connettore, il proxy back-end e il firewall siano in grado di supportare il certificato creato dal connettore per l'attendibilità futura e che il certificato sia valido.

>[!NOTE]
>Il connettore tenta di creare un certificato SHA512 supportato da TLS1.2. Se il computer o il firewall e il proxy back-end non supportano TLS 1.2, l'installazione non riesce.
>
>

**Verificare i prerequisiti necessari:**

1.  Verificare che il computer supporti TLS1.2: tutte le versioni di Windows successive alla 2012 R2 dovrebbero supportare TLS 1.2. Se la versione del computer di connessione è 2012 R2 o precedente, assicurarsi che vi siano installate le Knowledge Base seguenti: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Contattare l'amministratore di rete e chiedere di verificare che il proxy e il firewall di back-end non blocchino SHA512 per il traffico in uscita.

**Per verificare il certificato client:**

Verificare l'identificazione personale del certificato client corrente. L'archivio certificati si trova in `%ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml` .

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

I possibili valori di **IsInUserStore** sono **true** e **false**. Il valore **true** indica che il certificato rinnovato automaticamente viene archiviato nel contenitore personale nell'archivio certificati utente del servizio di rete. Il valore **false** indica che il certificato client è stato creato durante l'installazione o la registrazione avviata dal comando Register-AppProxyConnector e viene archiviato nel contenitore personale nell'archivio certificati del computer locale.

Se il valore è **true**, attenersi alla seguente procedura per verificare il certificato:
1. Scarica [PsTools.zip](https://docs.microsoft.com/sysinternals/downloads/pstools)
2. Estrarre [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) dal pacchetto ed eseguire **psexec-i-u "NT authority\network Service" cmd.exe** da un prompt dei comandi con privilegi elevati.
3. Eseguire **certmgr. msc** nel prompt dei comandi appena visualizzato
4. Nella console di gestione espandere il contenitore personale e fare clic su certificati.
5. Individuare il certificato emesso da **connectorregistrationca.msappproxy.NET**

Se il valore è **false**, attenersi alla seguente procedura per verificare il certificato:
1. Eseguire **certlm. msc**
2. Nella console di gestione espandere il contenitore personale e fare clic su certificati.
3. Individuare il certificato emesso da **connectorregistrationca.msappproxy.NET**

**Per rinnovare il certificato client:**

Se un connettore non viene connesso al servizio per molti mesi, i relativi certificati potrebbero non essere più aggiornati. L'errore del rinnovo del certificato comporta un certificato scaduto. In questo modo il servizio del connettore smette di funzionare. L'evento 1000 viene registrato nel log di amministrazione del connettore:

"La nuova registrazione del connettore non è riuscita: il certificato di attendibilità del connettore è scaduto. Eseguire il cmdlet di PowerShell Register-AppProxyConnector nel computer in cui è in esecuzione il connettore per registrare nuovamente il connettore ".

In questo caso, disinstallare e reinstallare il connettore per attivare la registrazione oppure è possibile eseguire i comandi di PowerShell seguenti:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Per altre informazioni sul comando Register-AppProxyConnector, vedere [creare uno script di installazione automatica per il connettore del proxy di applicazione Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-register-connector-powershell)

## <a name="verify-admin-is-used-to-install-the-connector"></a>Verificare che il connettore venga installato da un utente con il ruolo di amministratore

**Obiettivo:** verificare che l'utente che tenta di installare il connettore sia un amministratore con le credenziali corrette. Attualmente, l'utente deve essere almeno un amministratore dell'applicazione affinché l'installazione abbia esito positivo.

**Per verificare che le credenziali siano corrette:**

Connettersi a `https://login.microsoftonline.com` e usare le stesse credenziali. Assicurarsi che l'accesso venga completato. È possibile controllare il ruolo utente passando a **Azure Active Directory**  - &gt; **utenti e gruppi**  - &gt; **tutti gli utenti**. 

Selezionare il proprio account utente, quindi "directory Role" nel menu risultante. Verificare che il ruolo selezionato sia "amministratore applicazione". Se non si riesce ad accedere a nessuna delle pagine con questa procedura, non si ha il ruolo necessario.

## <a name="next-steps"></a>Passaggi successivi
[Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-connectors.md)
