---
title: Monitoraggio e registrazione in Azure AD Password di protezione - Azure Active Directory
description: Informazioni sul monitoraggio e la registrazione in Protezione password di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae2d18541788e769e4f1b44319aa1be200921b88
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437561"
---
# <a name="azure-ad-password-protection-monitoring-and-logging"></a>Monitoraggio e registrazione in Protezione password di Azure AD

Dopo la distribuzione di Protezione password di Azure AD, il monitoraggio e la creazione di report sono attività essenziali. Questo articolo spiega nel dettaglio varie tecniche di monitoraggio, indicando i percorsi in cui ogni servizio registra le informazioni e illustrando come creare report sull'uso di Protezione password di Azure AD.

Monitoraggio e creazione di report vengono eseguiti i messaggi di log eventi oppure eseguendo i cmdlet di PowerShell. I controller di dominio agente di servizi e proxy sia messaggi del registro eventi di log. Tutti i cmdlet di PowerShell descritti di seguito sono disponibili nel server proxy solo (vedere il modulo AzureADPasswordProtection PowerShell). Il software dell'agente controller di dominio non viene installato un modulo di PowerShell.

## <a name="dc-agent-event-logging"></a>Registrazione eventi dell'agente del controller di dominio

In ogni controller di dominio il software del servizio agente del controller di dominio scrive i risultati di ogni singola operazione di convalida delle password (e altri stati) in un log eventi locale:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Il log degli eventi di amministrazione dell'agente del controller di dominio è la principale fonte di informazioni sul comportamento del software.

Il log di traccia è disattivato per impostazione predefinita.

Gli eventi registrati dai diversi componenti dell'agente del controller di dominio rientrano negli intervalli seguenti:

|Componente |Intervallo di ID evento|
| --- | --- |
|DLL di filtro delle password dell'agente del controller di dominio| 10000-19999|
|Processo di hosting del servizio agente del controller di dominio| 20000-29999|
|Logica di convalida dei criteri del servizio agente del controller di dominio| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Log eventi di amministrazione dell'agente del controller di dominio

### <a name="password-validation-outcome-events"></a>Eventi relativi ai risultati di convalida delle password

In ogni controller di dominio il software del servizio agente del controller di dominio scrive i risultati di ogni singola operazione di convalida delle password nel log eventi di amministrazione dell'agente stesso.

Per un'operazione di convalida delle password riuscita, in genere viene registrato un evento dalla DLL di filtro delle password dell'agente del controller di dominio. Per un'operazione di convalida delle password non riuscita, vengono in genere registrati due eventi, uno dal servizio agente del controller di dominio e uno dalla DLL di filtro delle password dell'agente del controller di dominio.

Vengono registrati eventi discreti per acquisire informazioni riguardo a queste situazioni, in base ai fattori seguenti:

* Se una password specifica è o meno in fase di impostazione o modifica.
* Se la convalida di una password specifica è o meno riuscita.
* Se la convalida non è riuscita a causa dei criteri globali Microsoft, dei criteri aziendali o di una combinazione di entrambi.
* Se la modalità di solo controllo è attualmente attivata o disattivata per i criteri password correnti.

Gli eventi principali relativi alla convalida delle password sono i seguenti:

|   |Modifica della password |Impostazione della password|
| --- | :---: | :---: |
|Pass |10014 |10015|
|Non riuscita (a causa dei criteri per le password del cliente)| 10016, 30002| 10017, 30003|
|Non riuscita (a causa dei criteri per le password di Microsoft)| 10016, 30004| 10017, 30005|
|Non riuscita (a causa di una combinazione dei criteri per le password di Microsoft e del cliente)| 10016, 30026| 10017, 30027|
|Passaggio di solo controllo (non soddisferebbe i criteri password del cliente)| 10024, 30008| 10025, 30007|
|Passaggio di solo controllo (non soddisferebbe i criteri password Microsoft)| 10024, 30010| 10025, 30009|
|Riuscita solo a livello di controllo (non soddisferebbe la combinazione dei criteri per le password di Microsoft e del cliente)| 10024, 30028| 10025, 30029|

I casi che si riferiscono alla combinazione di criteri, nella tabella riportata sopra, riguardano situazioni in cui è stata rilevata una password di un utente che contiene almeno un token dell'elenco di password escluse sia di Microsoft che del cliente.

Quando una coppia di eventi viene registrata insieme, entrambi vengono associati in modo esplicito in base allo stesso ID di correlazione.

### <a name="password-validation-summary-reporting-via-powershell"></a>Creazione di report di riepilogo sulla convalida delle password tramite PowerShell

È possibile usare il cmdlet `Get-AzureADPasswordProtectionSummaryReport` per produrre una visualizzazione di riepilogo dell'attività di convalida delle password. Di seguito viene mostrato un esempio dell'output di questo cmdlet:

```PowerShell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

È possibile influire sull'ambito del reporting del cmdlet usando uno dei parametri –Forest, -Domain o –DomainController. Se non si specifica un parametro, viene usato il parametro –Forest.

Il cmdlet `Get-AzureADPasswordProtectionSummaryReport` esegue query sul log eventi dell'agente del controller di dominio e quindi conta il numero totale di eventi che corrispondono a ogni categoria di risultati visualizzata. La tabella seguente contiene i mapping tra ogni risultato e l'ID evento corrispondente:

|Proprietà Get-AzureADPasswordProtectionSummaryReport |ID evento corrispondente|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Il cmdlet `Get-AzureADPasswordProtectionSummaryReport` viene fornito nel formato di script di PowerShell e, se necessario, vi si può fare riferimento direttamente al percorso seguente:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Questo cmdlet funziona aprendo una sessione di PowerShell per ogni controller di dominio. Per il corretto funzionamento, il supporto di sessioni remote di PowerShell deve essere abilitato in ogni controller di dominio e il client deve avere privilegi sufficienti. Per altre informazioni sui requisiti per le sessioni remote di PowerShell, eseguire 'Get-Help about_Remote_Troubleshooting' in una finestra di PowerShell.

> [!NOTE]
> Questo cmdlet funziona eseguendo query in modalità remota sul log eventi di amministrazione di ogni servizio agente del controller di dominio. Se i log eventi contengono un numero elevato di eventi, il completamento del cmdlet potrebbe richiedere molto tempo. Inoltre, le query di rete in blocco di grandi set di dati possono influire sulle prestazioni dei controller di dominio. Di conseguenza, questo cmdlet deve essere usato con cautela negli ambienti di produzione.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Messaggio del log eventi di esempio per l'ID evento 10014 (password cambiata correttamente)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Messaggio del log eventi di esempio per l'impostazione delle password non riuscita con ID evento 10017 e 30003

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Messaggio del log eventi di esempio per l'ID evento 30001 (password accettata perché non sono ancora disponibili criteri)

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Messaggio del log eventi di esempio per l'ID evento 30006 (nuovi criteri applicati)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Messaggio del log eventi di esempio per l'ID evento 30019 (Protezione password di Azure AD disabilitata)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Log operativo agente del controller di dominio

Il servizio agente del controller di dominio registra anche gli eventi operativi correlati al log seguente:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Log di traccia agente del controller di dominio

Il servizio agente del controller di dominio può registrare anche eventi di traccia a livello di debug dettagliati nel log seguente:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

La registrazione della traccia è disabilitata per impostazione predefinita.

> [!WARNING]
> Quando è abilitato, il log di traccia riceve un numero elevato di eventi e può influire sulle prestazioni del controller di dominio. Di conseguenza, questo log avanzato deve essere abilitato solo quando un problema richiede un'indagine più approfondita e solo per un periodo di tempo minimo.

## <a name="dc-agent-text-logging"></a>Registrazione di testo agente del controller di dominio

Il servizio agente del controller di dominio può essere configurato per scrivere in un log di testo impostando il valore del Registro di sistema seguente:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

La registrazione di testo è disabilitata per impostazione predefinita. Per rendere effettive le modifiche, è necessario riavviare il servizio agente del controller di dominio. Quando il servizio agente del controller di dominio è abilitato, scrive in un file di log che si trova in:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Il log di testo riceve le stesse voci a livello di debug che possono essere registrate nel log di traccia, ma è in genere in un formato più semplice da esaminare e analizzare.

> [!WARNING]
> Quando è abilitato, questo log riceve un numero elevato di eventi e può influire sulle prestazioni del controller di dominio. Di conseguenza, questo log avanzato deve essere abilitato solo quando un problema richiede un'indagine più approfondita e solo per un periodo di tempo minimo.

## <a name="dc-agent-performance-monitoring"></a>Monitoraggio delle prestazioni dell'agente del controller di dominio

Il software del servizio agente del controller di dominio installa un oggetto contatore delle prestazioni denominato **protezione password di Azure AD**. Sono attualmente disponibili i contatori delle prestazioni seguenti:

|Nome contatore delle prestazioni | DESCRIZIONE|
| --- | --- |
|Passwords processed (Password elaborate) |Questo contatore indica il numero totale di password elaborate (accettate o rifiutate) dall'ultimo riavvio.|
|Passwords accepted (Password accettate) |Questo contatore indica il numero totale di password accettate dall'ultimo riavvio.|
|Password rejected (Password rifiutate) |Questo contatore indica il numero totale di password rifiutate dall'ultimo riavvio.|
|Password filter requests in progress (Richieste filtro password in corso) |Questo contatore indica il numero di richieste di filtro delle password attualmente in corso.|
|Peak password filter requests (Max richieste filtro password) |Questo contatore indica il numero massimo di richieste di filtro delle password simultanee dall'ultimo riavvio.|
|Password filter request errors (Errori richieste filtro password) |Questo contatore indica il numero totale di richieste di filtro delle password non riuscite a causa di un errore dall'ultimo riavvio. Possono verificarsi errori quando il servizio agente del controller di dominio di Protezione password di Azure AD non è in esecuzione.|
|Password filter requests/sec (Richieste filtro password al secondo) |Questo contatore indica la frequenza con cui vengono elaborate le richieste.|
|Password filter request processing time (Tempo di elaborazione richieste filtro password) |Questo contatore indica il tempo medio necessario per elaborare una richiesta di filtro delle password.|
|Peak password filter request processing time (Tempo di elaborazione max richieste filtro password) |Questo contatore indica il tempo di elaborazione massimo delle richieste di filtro delle password dall'ultimo riavvio.|
|Passwords accepted due to audit mode (Password accettate a causa della modalità di controllo) |Questo contatore indica il numero totale di password che verrebbero normalmente rifiutate, ma che sono state accettate perché i criteri delle password sono stati configurati come in modalità di controllo (dall'ultimo riavvio).|

## <a name="dc-agent-discovery"></a>Individuazione dell'agente del controller di dominio

È possibile usare il cmdlet `Get-AzureADPasswordProtectionDCAgent` per visualizzare informazioni di base sui diversi agenti del controller di dominio in esecuzione in un dominio o una foresta. Queste informazioni vengono recuperate dagli oggetti serviceConnectionPoint registrati da uno o più servizi agente del controller di dominio in esecuzione.

Di seguito viene mostrato un esempio dell'output di questo cmdlet:

```PowerShell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

Le diverse proprietà vengono aggiornate da ogni servizio agente del controller di dominio all'incirca ogni ora. I dati sono comunque soggetti alla latenza di replica di Active Directory.

L'ambito della query del cmdlet può essere ulteriormente definito usando il parametro –Forest o –Domain.

Se il valore HeartbeatUTC non viene aggiornato, il problema può essere dovuto al fatto che l'agente del controller di dominio di Protezione password di Azure AD in quel controller di dominio non è in esecuzione o è stato disinstallato oppure il computer è stato abbassato di livello e non è più un controller di dominio.

Se il valore PasswordPolicyDateUTC non viene aggiornato, il problema può essere dovuto al fatto che l'agente del controller di dominio di Protezione password di Azure AD in quel computer non funziona correttamente.

## <a name="proxy-service-event-logging"></a>Registrazione eventi del servizio proxy

Il servizio proxy genera un set minimo di eventi per i log eventi seguenti:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Il log di traccia è disattivato per impostazione predefinita.

> [!WARNING]
> Quando è abilitato, il log di traccia riceve un numero elevato di eventi e può influire sulle prestazioni dell'host del proxy. Di conseguenza, questo log deve essere abilitato solo quando un problema richiede un'indagine più approfondita e solo per un periodo di tempo minimo.

Gli eventi vengono registrati dai diversi componenti proxy tramite gli intervalli seguenti:

|Componente |Intervallo di ID evento|
| --- | --- |
|Processo di hosting del servizio proxy| 10000-19999|
|Logica di business di base del servizio proxy| 20000-29999|
|Cmdlet PowerShell| 30000-39999|

## <a name="proxy-service-text-logging"></a>Registrazione di testo del servizio proxy

Il servizio proxy può essere configurato per scrivere in un log di testo impostando il valore del Registro di sistema seguente:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1 (valore REG_DWORD)

La registrazione di testo è disabilitata per impostazione predefinita. Per rendere effettive le modifiche, è necessario riavviare il servizio proxy. Quando è abilitato, il servizio proxy scrive in un file di log che si trova in:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Il log di testo riceve le stesse voci a livello di debug che possono essere registrate nel log di traccia, ma è in genere in un formato più semplice da esaminare e analizzare.

> [!WARNING]
> Quando è abilitato, questo log riceve un numero elevato di eventi e può influire sulle prestazioni del computer. Di conseguenza, questo log avanzato deve essere abilitato solo quando un problema richiede un'indagine più approfondita e solo per un periodo di tempo minimo.

## <a name="powershell-cmdlet-logging"></a>Registrazione di cmdlet di PowerShell

I cmdlet di PowerShell che generano una modifica dello stato (ad esempio Register-AzureADPasswordProtectionProxy) normalmente registrano un evento risultato nel log operativo.

Inoltre, la maggior parte dei cmdlet di protezione di Password di Azure AD PowerShell scriverà un registro di testo che si trova in:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Se si verifica un errore di cmdlet e la causa e/o soluzione non è immediatamente evidente, è possibile consultare anche questi log di testo.

## <a name="proxy-discovery"></a>Individuazione dei servizi proxy

È possibile usare il cmdlet `Get-AzureADPasswordProtectionProxy` per visualizzare informazioni di base sui vari servizi proxy di Protezione password di Azure AD in esecuzione in un dominio o una foresta. Queste informazioni vengono recuperate dagli oggetti serviceConnectionPoint registrati dai servizi proxy in esecuzione.

Di seguito viene mostrato un esempio dell'output di questo cmdlet:

```PowerShell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

Le diverse proprietà vengono aggiornate da ogni servizio proxy all'incirca ogni ora. I dati sono comunque soggetti alla latenza di replica di Active Directory.

L'ambito della query del cmdlet può essere ulteriormente definito usando il parametro –Forest o –Domain.

Se il valore HeartbeatUTC non viene aggiornato, il problema può essere dovuto al fatto che il proxy di Protezione password di Azure AD in quel computer non è in esecuzione o è stato disinstallato.

## <a name="next-steps"></a>Passaggi successivi

[Monitoraggio, creazione di report e risoluzione dei problemi nella protezione password di Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)

Per altre informazioni sugli elenchi personalizzati di password escluse, vedere l'articolo [Eliminare le password non appropriate nell'organizzazione](concept-password-ban-bad.md)
