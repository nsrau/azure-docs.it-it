---
title: Risoluzione dei problemi e registrazione nell'anteprima della protezione password di Azure AD
description: Informazioni sulla registrazione e sulla risoluzione dei problemi nell'anteprima della protezione password di Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 1eea6380d4276644db0c7681f23a4b0c5e79ff09
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187350"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Anteprima: monitoraggio, creazione di report e risoluzione dei problemi nella protezione password di Azure AD

|     |
| --- |
| La protezione password e l'elenco personalizzato di password escluse di Azure AD sono funzionalità in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzionalità in anteprima, vedere [Condizioni supplementari per l'Utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Dopo la distribuzione della protezione password di Azure AD, il monitoraggio e la creazione di report sono attività essenziali. Questo articolo esplora dettagliatamente questi argomenti per aiutare a identificare i percorsi in cui ogni servizio registra le informazioni e come creare report sull'uso della protezione password di Azure AD.

## <a name="on-premises-logs-and-events"></a>Log ed eventi locali

### <a name="dc-agent-service"></a>Servizio agente del controller di dominio

In ogni controller di dominio il software del servizio agente del controller di dominio scrive i risultati delle relative convalide delle password (e altri stati) in un log eventi locale: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin

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

La password modificata per l'utente specificato è stata convalidata come conforme ai criteri password di Azure correnti.

 Nome utente: BPL_02885102771 FullName:

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Messaggio del log eventi di esempio per l'impostazione delle password non riuscita con ID evento 10017 e 30003

10017:

La password reimpostata per l'utente specificato è stata rifiutata perché non è conforme ai criteri password di Azure correnti. Per altre informazioni, vedere il messaggio del log eventi correlato.

 Nome utente: BPL_03283841185 FullName:

30003:

La password reimpostata per l'utente specificato è stata rifiutata perché corrisponde ad almeno uno dei token presenti nell'elenco di password per tenant dei criteri password di Azure correnti.

 Nome utente: BPL_03283841185 FullName:

Altri messaggi importanti del log eventi di cui tenere conto:

#### <a name="sample-event-log-message-for-event-id-30001"></a>Messaggio del log eventi di esempio per l'ID evento 30001

La password per l'utente specificato è stata accettata perché non sono ancora disponibili criteri password di Azure

Nome utente: <user> FullName: <user>

Questa condizione può essere dovuta a uno o più dei motivi seguenti:%n

1. La foresta non è stata ancora registrata con Azure.

   Passaggi per la risoluzione: un amministratore deve registrare la foresta usando il cmdlet Register-AzureADPasswordProtectionForest.

2. Un proxy di protezione password di Azure AD non è ancora disponibile in almeno un computer nella foresta corrente.

   Passaggi per la risoluzione: un amministratore deve installare e registrare un proxy usando il cmdlet Register-AzureADPasswordProtectionProxy.

3. Il controller di dominio non ha connettività di rete ad alcuna delle istanze del proxy di protezione password di Azure AD.

   Passaggi per la risoluzione: verificare la presenza di connettività di rete ad almeno un'istanza del proxy di protezione password di Azure AD.

4. Il controller di dominio non ha connettività ad altri controller di dominio nel dominio.

   Passaggi per la risoluzione: verificare la presenza di connettività di rete al dominio.

#### <a name="sample-event-log-message-for-event-id-30006"></a>Messaggio del log eventi di esempio per l'ID evento 30006

Il servizio sta ora applicando il criterio password di Azure seguente.

 AuditOnly: 1

 Data criteri globali: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z

 Data criteri del tenant: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z

 Applicazione dei criteri del tenant: 1

#### <a name="dc-agent-log-locations"></a>Percorsi dei log eventi del controller di dominio

Il servizio agente del controller di dominio registra anche eventi correlati alle operazioni in questo log: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational

Il servizio agente del controller di dominio può registrare anche eventi di traccia a livello di debug dettagliati in questo log: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace

> [!WARNING]
> Il log di traccia è disabilitato per impostazione predefinita. Quando è abilitato, questo log riceve un numero elevato di eventi e può influire sulle prestazioni del controller di dominio. Di conseguenza, questo log avanzato deve essere abilitato solo quando un problema richiede un'indagine più approfondita e solo per un periodo di tempo minimo.

### <a name="azure-ad-password-protection-proxy-service"></a>Servizio proxy di protezione password di Azure AD

Il servizio proxy di protezione password genera un set minimo di eventi in questo log eventi: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational

Il servizio proxy di protezione password può anche registrare eventi di traccia a livello di debug dettagliati in questo log: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace

> [!WARNING]
> Il log di traccia è disabilitato per impostazione predefinita. Quando è abilitato, questo log riceve un numero elevato di eventi e può influire sulle prestazioni dell'host del proxy. Di conseguenza, questo log deve essere abilitato solo quando un problema richiede un'indagine più approfondita e solo per un periodo di tempo minimo.

### <a name="dc-agent-discovery"></a>Individuazione dell'agente del controller di dominio

È possibile usare il cmdlet `Get-AzureADPasswordProtectionDCAgent` per visualizzare informazioni di base sui diversi agenti del controller di dominio in esecuzione in un dominio o una foresta. Queste informazioni vengono recuperate dagli oggetti serviceConnectionPoint registrati da uno o più servizi agente del controller di dominio in esecuzione. Di seguito viene mostrato un esempio dell'output di questo cmdlet:

```
PS C:\> Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

Le diverse proprietà vengono aggiornate da ogni servizio agente del controller di dominio all'incirca ogni ora. I dati sono comunque soggetti alla latenza di replica di Active Directory.

L'ambito della query del cmdlet può essere ulteriormente definito usando il parametro –Forest o –Domain.

### <a name="emergency-remediation"></a>Correzione di emergenza

Se si verifica una situazione indesiderabile in cui il servizio agente del controller di dominio provoca problemi, il servizio agente del controller di dominio può essere arrestato immediatamente. La DLL di filtro dell'agente del controller di dominio tenta di chiamare il servizio non in esecuzione e registra eventi di avviso (10012, 10013), ma durante questo periodo di tempo tutte le password in ingresso vengono accettate. Il servizio agente del controller di dominio può quindi essere configurato anche tramite Gestione controllo servizi di Windows con tipo di avvio "Disabilitato" in base alle esigenze.

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
> È importante eseguire i passaggi nell'ordine specificato. Se qualsiasi istanza del servizio proxy di protezione password viene lasciata in esecuzione, ricreerà periodicamente l'oggetto serviceConnectionPoint, nonché lo stato di sysvol.

1. Disinstallare il software del proxy di protezione password da tutti i computer. Questo passaggio **non** richiede un riavvio.
2. Disinstallare il software dell'agente del controller di dominio da tutti i controller di dominio. Questo passaggio **richiede** un riavvio.
3. Rimuovere manualmente tutti i punti di connessione del servizio proxy in ogni contesto dei nomi di dominio. Il percorso di questi oggetti può essere individuato con il comando di PowerShell per Active Directory seguente:
   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{EBEFB703-6113-413D-9167-9F8DD4D24468}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Non omettere l'asterisco ("*") alla fine del valore della variabile $keywords.

   L'oggetto risultante individuabile tramite il comando `Get-ADObject` può quindi essere inoltrato tramite pipe a `Remove-ADObject` oppure può essere eliminato manualmente. 

4. Rimuovere manualmente tutti i punti di connessione dell'agente del controller di dominio in ogni contesto dei nomi di dominio. Potrebbe essere presente uno di questi oggetti per ogni controller di dominio nella foresta, a seconda dell'ampiezza della distribuzione del software in versione di anteprima pubblica. Il percorso dell'oggetto può essere individuato con il comando di PowerShell per Active Directory seguente:

   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{B11BB10A-3E7D-4D37-A4C3-51DE9D0F77C9}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   L'oggetto risultante individuabile tramite il comando `Get-ADObject` può quindi essere inoltrato tramite pipe a `Remove-ADObject` oppure può essere eliminato manualmente.

5. Rimuovere manualmente lo stato di configurazione a livello di foresta. Lo stato di configurazione a livello di foresta viene mantenuto in un contenitore nel contesto dei nomi della configurazione di Active Directory. Può essere individuato ed eliminato in questo modo:

   ```
   $passwordProtectonConfigContainer = "CN=Azure AD password protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Rimuovere manualmente tutto lo stato correlato a sysvol eliminando manualmente la cartella seguente e tutto il suo contenuto:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Se necessario, è possibile accedere a questo percorso anche in locale in un controller di dominio specificato. il percorso predefinito può essere simile al seguente:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Il percorso è diverso se la condivisione sysvol è stata configurata in un percorso non predefinito.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli elenchi personalizzati di password escluse, vedere l'articolo [Eliminare le password non appropriate nell'organizzazione](concept-password-ban-bad.md)
