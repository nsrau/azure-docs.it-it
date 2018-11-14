---
title: Risoluzione dei problemi e registrazione nell'anteprima della protezione password di Azure AD
description: Informazioni sulla registrazione e sulla risoluzione dei problemi nell'anteprima della protezione password di Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 1e5782ce3421cc5f0d2e0e51484d4bbe6b9eb6ab
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978639"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Anteprima: monitoraggio, creazione di report e risoluzione dei problemi nella protezione password di Azure AD

|     |
| --- |
| La protezione password di Azure AD è una funzionalità in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Dopo la distribuzione della protezione delle password di Azure AD, il monitoraggio e la creazione di report sono attività essenziali. Questo articolo esplora dettagliatamente questi argomenti per aiutare a identificare i percorsi in cui ogni servizio registra le informazioni e come creare report sull'uso della protezione password di Azure AD.

## <a name="on-premises-logs-and-events"></a>Log ed eventi locali

### <a name="dc-agent-admin-log"></a>Log di amministrazione agente controller di dominio

In ogni controller di dominio il software del servizio agente del controller di dominio scrive i risultati delle proprie convalide delle password (e altri stati) in un log eventi locale:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

Gli eventi vengono registrati dai diversi componenti dell'agente del controller di dominio tramite gli intervalli seguenti:

|Componente |Intervallo di ID evento|
| --- | --- |
|DLL di filtro delle password dell'agente del controller di dominio| 10000-19999|
|Processo di hosting del servizio agente del controller di dominio| 20000-29999|
|Logica di convalida dei criteri del servizio agente del controller di dominio| 30000-39999|

Per un'operazione di convalida delle password riuscita, in genere viene registrato un evento dalla DLL di filtro delle password dell'agente del controller di dominio. Per un'operazione di convalida delle password non riuscita, vengono in genere registrati due eventi, uno dal servizio agente del controller di dominio e uno dalla DLL di filtro delle password dell'agente del controller di dominio.

Vengono registrati eventi discreti per acquisire informazioni riguardo a queste situazioni, in base ai fattori seguenti:

* Se una password specifica è o meno in fase di impostazione o modifica.
* Se la convalida di una password specifica è o meno riuscita.
* Se la convalida non è riuscita a causa dei criteri globali Microsoft o dei criteri aziendali.
* Se la modalità di solo controllo è attualmente attivata o disattivata per i criteri password correnti.

Gli eventi principali relativi alla convalida delle password sono i seguenti:

|   |Modifica della password |Impostazione della password|
| --- | :---: | :---: |
|Pass |10014 |10015|
|Non riuscita (non ha soddisfatto i criteri password del cliente)| 10016, 30002| 10017, 30003|
|Non riuscita (non ha soddisfatto i criteri password Microsoft)| 10016, 30004| 10017, 30005|
|Passaggio di solo controllo (non soddisferebbe i criteri password del cliente)| 10024, 30008| 10025, 30007|
|Passaggio di solo controllo (non soddisferebbe i criteri password Microsoft)| 10024, 30010| 10025, 30009|

> [!TIP]
> Le password in ingresso vengono convalidate prima di tutto rispetto all'elenco globale di password Microsoft. Se la convalida non riesce, non viene eseguita ulteriore elaborazione. Si tratta dello stesso comportamento eseguito sulle modifiche delle password in Azure.

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Messaggio del log eventi di esempio per l'impostazione corretta delle password con ID evento 10014

```
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Messaggio del log eventi di esempio per l'impostazione delle password non riuscita con ID evento 10017 e 30003

10017:

```
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Messaggio del log eventi di esempio per l'ID evento 30001 (password accettata perché non sono ancora disponibili criteri)

```
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

#### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Messaggio del log eventi di esempio per l'ID evento 30006 (nuovi criteri applicati)

```
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

#### <a name="dc-agent-operational-log"></a>Log operativo agente del controller di dominio

Il servizio agente del controller di dominio registra anche gli eventi operativi correlati al log seguente:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

#### <a name="dc-agent-trace-log"></a>Log di traccia agente del controller di dominio

Il servizio agente del controller di dominio può registrare anche eventi di traccia a livello di debug dettagliati nel log seguente:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

La registrazione della traccia è disabilitata per impostazione predefinita.

> [!WARNING]
>  Quando è abilitato, il log di traccia riceve un numero elevato di eventi e può influire sulle prestazioni del controller di dominio. Di conseguenza, questo log avanzato deve essere abilitato solo quando un problema richiede un'indagine più approfondita e solo per un periodo di tempo minimo.

#### <a name="dc-agent-text-logging"></a>Registrazione di testo agente del controller di dominio

Il servizio agente del controller di dominio può essere configurato per scrivere in un log di testo impostando il valore del Registro di sistema seguente:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (valore REG_DWORD)

La registrazione di testo è disabilitata per impostazione predefinita. Per rendere effettive le modifiche, è necessario riavviare il servizio agente del controller di dominio. Quando il servizio agente del controller di dominio è abilitato, scrive in un file di log che si trova in:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Il log di testo riceve le stesse voci a livello di debug che possono essere registrate nel log di traccia, ma è in genere in un formato più semplice da esaminare e analizzare.

> [!WARNING]
> Quando è abilitato, questo log riceve un numero elevato di eventi e può influire sulle prestazioni del controller di dominio. Di conseguenza, questo log avanzato deve essere abilitato solo quando un problema richiede un'indagine più approfondita e solo per un periodo di tempo minimo.

### <a name="azure-ad-password-protection-proxy-service"></a>Servizio proxy di protezione password di Azure AD

#### <a name="proxy-service-event-logs"></a>Log eventi di servizi proxy

Il servizio proxy genera un set minimo di eventi per i log eventi seguenti:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

Il servizio proxy può registrare anche eventi di traccia a livello di debug dettagliati nel log seguente:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

La registrazione della traccia è disabilitata per impostazione predefinita.

> [!WARNING]
> Quando è abilitato, il log di traccia riceve un numero elevato di eventi e può influire sulle prestazioni dell'host del proxy. Di conseguenza, questo log deve essere abilitato solo quando un problema richiede un'indagine più approfondita e solo per un periodo di tempo minimo.

#### <a name="proxy-service-text-logging"></a>Registrazione di testo del servizio proxy

Il servizio proxy può essere configurato per scrivere in un log di testo impostando il valore del Registro di sistema seguente:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1 (valore REG_DWORD)

La registrazione di testo è disabilitata per impostazione predefinita. Per rendere effettive le modifiche, è necessario riavviare il servizio proxy. Quando è abilitato, il servizio proxy scrive in un file di log che si trova in:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Il log di testo riceve le stesse voci a livello di debug che possono essere registrate nel log di traccia, ma è in genere in un formato più semplice da esaminare e analizzare.

> [!WARNING]
> Quando è abilitato, questo log riceve un numero elevato di eventi e può influire sulle prestazioni del controller di dominio. Di conseguenza, questo log avanzato deve essere abilitato solo quando un problema richiede un'indagine più approfondita e solo per un periodo di tempo minimo.

#### <a name="powershell-cmdlet-logging"></a>Registrazione di cmdlet di PowerShell

La maggior parte dei cmdlets di protezione delle password di Azure AD scrivono in un log di testo che si trova in:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Se si verifica un errore di cmdlet e la causa e/o soluzione non è immediatamente evidente, è possibile consultare anche questi log di testo.

### <a name="emergency-remediation"></a>Correzione di emergenza

Se si verifica una situazione in cui il servizio agente del controller di dominio provoca problemi, tale servizio può essere arrestato immediatamente. La DLL di filtro dell'agente del controller di dominio tenta ancora di chiamare il servizio non in esecuzione e registra eventi di avviso (10012, 10013), ma durante questo periodo di tempo tutte le password in ingresso vengono accettate. Il servizio agente del controller di dominio può quindi essere configurato anche tramite Gestione controllo servizi di Windows con tipo di avvio "Disabilitato" in base alle esigenze.

### <a name="performance-monitoring"></a>Monitoraggio delle prestazioni

Il software del servizio agente del controller di dominio installa un oggetto contatore delle prestazioni denominato **protezione password di Azure AD**. Sono attualmente disponibili i contatori delle prestazioni seguenti:

|Nome contatore delle prestazioni | DESCRIZIONE|
| --- | --- |
|Passwords processed (Password elaborate) |Questo contatore indica il numero totale di password elaborate (accettate o rifiutate) dall'ultimo riavvio.|
|Passwords accepted (Password accettate) |Questo contatore indica il numero totale di password accettate dall'ultimo riavvio.|
|Password rejected (Password rifiutate) |Questo contatore indica il numero totale di password rifiutate dall'ultimo riavvio.|
|Password filter requests in progress (Richieste filtro password in corso) |Questo contatore indica il numero di richieste di filtro delle password attualmente in corso.|
|Peak password filter requests (Max richieste filtro password) |Questo contatore indica il numero massimo di richieste di filtro delle password simultanee dall'ultimo riavvio.|
|Password filter request errors (Errori richieste filtro password) |Questo contatore indica il numero totale di richieste di filtro delle password non riuscite a causa di un errore dall'ultimo riavvio. Possono verificarsi errori quando il servizio agente del controller di dominio di protezione password di Azure AD non è in esecuzione.|
|Password filter requests/sec (Richieste filtro password al secondo) |Questo contatore indica la frequenza con cui vengono elaborate le richieste.|
|Password filter request processing time (Tempo di elaborazione richieste filtro password) |Questo contatore indica il tempo medio necessario per elaborare una richiesta di filtro delle password.|
|Peak password filter request processing time (Tempo di elaborazione max richieste filtro password) |Questo contatore indica il tempo di elaborazione massimo delle richieste di filtro delle password dall'ultimo riavvio.|
|Passwords accepted due to audit mode (Password accettate a causa della modalità di controllo) |Questo contatore indica il numero totale di password che verrebbero normalmente rifiutate, ma che sono state accettate perché i criteri delle password sono stati configurati come in modalità di controllo (dall'ultimo riavvio).|

## <a name="directory-services-repair-mode"></a>Modalità di ripristino dei servizi directory

Se il controller di dominio viene avviato in modalità di ripristino dei servizi directory, il servizio agente del controller di dominio rileva questo comportamento provocando la disabilitazione dell'intera convalida delle password o di tutte le attività di applicazione, indipendentemente dalla configurazione dei criteri attualmente attiva.

## <a name="domain-controller-demotion"></a>Abbassamento di livello del controller di dominio

È possibile abbassare di livello un controller di dominio che è ancora in esecuzione nel software dell'agente del controller di dominio. Gli amministratori devono tenere tuttavia presente il fatto che durante la procedura di abbassamento di livello il software dell'agente del controller di dominio continua a essere eseguito e ad applicare i criteri password correnti. La nuova password dell'account amministratore locale (specificata come parte dell'operazione di abbassamento di livello) viene convalidata come qualsiasi altra password. Microsoft consiglia di scegliere password sicure per gli account amministratore locali nell'ambito di una procedura di abbassamento di livello del controller di dominio. Tuttavia, la convalida della nuova password dell'account amministratore locale da parte del software dell'agente del controller di dominio può avere un forte impatto sulle procedure operative di abbassamento di livello preesistenti.

Al termine dell'abbassamento di livello e quando il controller di dominio è stato riavviato ed è di nuovo in esecuzione come normale server membro, il software dell'agente del controller di dominio torna a operare in modalità passiva. Può quindi essere disinstallato in qualsiasi momento.

## <a name="removal"></a>Rimozione

Se si decide di disinstallare il software in versione di anteprima pubblica e di pulire tutti gli stati correlati dai domini e dalla foresta, è possibile completare i passaggi seguenti a questo scopo:

> [!IMPORTANT]
> È importante eseguire i passaggi nell'ordine specificato. Se un'istanza del servizio proxy viene lasciata in esecuzione, ne viene periodicamente ricreare il relativo oggetto serviceConnectionPoint. Se un'istanza del servizio agente del controller di dominio viene lasciata in esecuzione, ne vengono ricreati periodicamente l'oggetto serviceConnectionPoint e lo stato sysvol.

1. Disinstallare il software del proxy di protezione password da tutti i computer. Questo passaggio **non** richiede un riavvio.
2. Disinstallare il software dell'agente del controller di dominio da tutti i controller di dominio. Questo passaggio **richiede** un riavvio.
3. Rimuovere manualmente tutti i punti di connessione del servizio proxy in ogni contesto dei nomi di dominio. Il percorso di questi oggetti può essere individuato con il comando di PowerShell per Active Directory seguente:

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Non omettere l'asterisco ("*") alla fine del valore della variabile $keywords.

   L'oggetto risultante individuabile tramite il comando `Get-ADObject` può quindi essere inoltrato tramite pipe a `Remove-ADObject` oppure può essere eliminato manualmente. 

4. Rimuovere manualmente tutti i punti di connessione dell'agente del controller di dominio in ogni contesto dei nomi di dominio. Potrebbe essere presente uno di questi oggetti per ogni controller di dominio nella foresta, a seconda dell'ampiezza della distribuzione del software in versione di anteprima pubblica. Il percorso dell'oggetto può essere individuato con il comando di PowerShell per Active Directory seguente:

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   L'oggetto risultante individuabile tramite il comando `Get-ADObject` può quindi essere inoltrato tramite pipe a `Remove-ADObject` oppure può essere eliminato manualmente.

5. Rimuovere manualmente lo stato di configurazione a livello di foresta. Lo stato di configurazione a livello di foresta viene mantenuto in un contenitore nel contesto dei nomi della configurazione di Active Directory. Può essere individuato ed eliminato in questo modo:

   ```Powershell
   $passwordProtectonConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Rimuovere manualmente tutto lo stato correlato a sysvol eliminando manualmente la cartella seguente e tutto il suo contenuto:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Se necessario, è possibile accedere a questo percorso anche in locale in un controller di dominio specificato. il percorso predefinito può essere simile al seguente:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Il percorso è diverso se la condivisione sysvol è stata configurata in un percorso non predefinito.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli elenchi personalizzati di password escluse, vedere l'articolo [Eliminare le password non appropriate nell'organizzazione](concept-password-ban-bad.md)
