---
title: Risoluzione dei problemi in Azure AD password protection-Azure Active Directory
description: Informazioni sulla risoluzione dei problemi comuni di Azure AD Protection password
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
ms.openlocfilehash: 690d49a94ff4f516e24494622ca378eb0794fee9
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71314922"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Risoluzione dei problemi relativi a Password di protezione di Azure AD

Dopo la distribuzione di Password di protezione di Azure AD, può essere necessario risolvere alcuni problemi. Questo articolo presenta informazioni dettagliate su alcuni passaggi comuni per la risoluzione dei problemi.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>L'agente del controller di dominio non è in grado di individuare un proxy nella directory

Il sintomo principale di questo problema è 30017 eventi nel registro eventi di amministrazione dell'agente del controller di dominio.

La causa usuale di questo problema è che un proxy non è ancora stato registrato. Se è stato registrato un proxy, è possibile che si verifichi un ritardo dovuto alla latenza di replica di Active Directory fino a quando un particolare agente del controller di dominio non è in grado di visualizzare il proxy

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>L'agente del controller di dominio non è in grado di comunicare con un proxy

Il sintomo principale di questo problema è 30018 eventi nel registro eventi di amministrazione dell'agente del controller di dominio. Questo problema può avere diverse cause possibili:

1. L'agente del controller di dominio si trova in una parte isolata della rete che non consente la connettività di rete ai proxy registrati. Questo problema può essere benigno purché altri agenti del controller di dominio possano comunicare con i proxy per scaricare i criteri password da Azure. Una volta scaricati, i criteri verranno ottenuti dal controller di dominio isolato tramite la replica dei file di criteri nella condivisione SYSVOL.

1. Il computer host proxy blocca l'accesso all'endpoint di mapping degli endpoint RPC (porta 135)

   Il programma di installazione del proxy di protezione Azure AD password crea automaticamente una regola in ingresso Windows Firewall che consente l'accesso alla porta 135. Se questa regola viene successivamente eliminata o disattivata, gli agenti del controller di dominio non saranno in grado di comunicare con il servizio proxy. Se il Windows Firewall incorporato è stato disabilitato al posto di un altro prodotto firewall, è necessario configurare il firewall per consentire l'accesso alla porta 135.

1. Il computer host proxy blocca l'accesso all'endpoint RPC (dinamico o statico) in ascolto da parte del servizio proxy

   Il programma di installazione del proxy di protezione Azure AD password crea automaticamente una regola in ingresso Windows Firewall che consente l'accesso a tutte le porte in ingresso ascoltate dal servizio proxy Azure AD password protection. Se questa regola viene successivamente eliminata o disattivata, gli agenti del controller di dominio non saranno in grado di comunicare con il servizio proxy. Se il Windows Firewall incorporato è stato disabilitato al posto di un altro prodotto firewall, è necessario configurarlo per consentire l'accesso a tutte le porte in ingresso ascoltate dal servizio Azure AD proxy di protezione delle password. Questa configurazione può essere resa più specifica se il servizio proxy è stato configurato per l'ascolto su una porta RPC statica specifica (tramite `Set-AzureADPasswordProtectionProxyConfiguration` il cmdlet).

1. Il computer host proxy non è configurato per consentire ai controller di dominio di accedere al computer. Questo comportamento viene controllato tramite l'assegnazione dei privilegi utente "accedi al computer dalla rete". È necessario concedere questo privilegio a tutti i controller di dominio in tutti i domini della foresta. Questa impostazione è spesso vincolata come parte di una maggiore attività di protezione avanzata della rete.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Il servizio proxy non è in grado di comunicare con Azure

1. Verificare che il computer proxy disponga di connettività agli endpoint elencati nei [requisiti di distribuzione](howto-password-ban-bad-on-premises-deploy.md).

1. Verificare che la foresta e tutti i server proxy siano registrati nello stesso tenant di Azure.

   È possibile verificare questo requisito eseguendo i `Get-AzureADPasswordProtectionProxy` cmdlet e `Get-AzureADPasswordProtectionDCAgent` di PowerShell, quindi confrontare la `AzureTenant` proprietà di ogni elemento restituito. Per il corretto funzionamento, il nome del tenant restituito deve essere lo stesso in tutti gli agenti controller di dominio e i server proxy.

   Se esiste una condizione di mancata corrispondenza della registrazione del tenant di Azure, questo problema può essere `Register-AzureADPasswordProtectionProxy` risolto eseguendo i `Register-AzureADPasswordProtectionForest` cmdlet di e/o PowerShell in base alle esigenze, assicurandosi di usare le credenziali dello stesso tenant di Azure per tutte le registrazioni.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>Impossibile crittografare o decrittografare i file di criteri password

Azure AD la protezione con password ha una dipendenza critica dalla funzionalità di crittografia e decrittografia fornita dal servizio di distribuzione delle chiavi Microsoft. Gli errori di crittografia o decrittografia possono manifestarsi con diversi sintomi e hanno diverse cause potenziali.

1. Verificare che il servizio KDS sia abilitato e funzionante in tutti i controller di dominio Windows Server 2012 e versioni successive in un dominio.

   Per impostazione predefinita, la modalità di avvio del servizio KDS è configurata come manuale (avvio del trigger). Questa configurazione significa che la prima volta che un client tenta di usare il servizio, viene avviato su richiesta. Questa modalità di avvio del servizio predefinita è accettabile per il funzionamento Azure AD la protezione delle password.

   Se la modalità di avvio del servizio KDS è stata configurata come disabilitata, è necessario correggere questa configurazione prima di Azure AD la protezione delle password funzionerà correttamente.

   Un semplice test di questo problema consiste nell'avviare manualmente il servizio KDS tramite la console MMC di gestione dei servizi o con altri strumenti di gestione (ad esempio, eseguire "net start kdssvc" da una console del prompt dei comandi). Il servizio KDS dovrebbe avviarsi correttamente e rimanere in esecuzione.

   La causa principale più comune per l'avvio del servizio KDS è che l'oggetto controller di dominio Active Directory si trova al di fuori dell'unità organizzativa dei controller di dominio predefiniti. Questa configurazione non è supportata dal servizio KDS e non è un limite imposto da Azure AD la protezione delle password. La correzione per questa condizione consiste nello spostare l'oggetto controller di dominio in un percorso all'interno dell'unità organizzativa dei controller di dominio predefiniti.

1. Modifica del formato del buffer crittografato KDS incompatibile da Windows Server 2012 R2 a Windows Server 2016

   Una correzione di sicurezza KDS è stata introdotta in Windows Server 2016 che modifica il formato dei buffer crittografati KDS; questi buffer talvolta non riescono a decrittografare in Windows Server 2012 e Windows Server 2012 R2. La direzione inversa è OK: i buffer con KDS crittografati in Windows Server 2012 e Windows Server 2012 R2 verranno sempre decrittografati in Windows Server 2016 e versioni successive. Se i controller di dominio nei domini Active Directory eseguono una combinazione di questi sistemi operativi, è possibile che vengano segnalati errori occasionali di decrittografia della protezione Azure AD password. Non è possibile prevedere accuratamente l'intervallo o i sintomi di questi errori in base alla natura della correzione della sicurezza e, dato che non è deterministica, che Azure AD agente di controller di dominio per la protezione delle password in cui il controller di dominio eseguirà la crittografia dei dati in un determinato momento.

   Microsoft sta esaminando una correzione per questo problema, ma non è ancora disponibile l'ETA. Nel frattempo, non esiste alcuna soluzione per questo problema, ad eccezione di non eseguire una combinazione di questi sistemi operativi incompatibili nei domini Active Directory. In altre parole, è consigliabile eseguire solo i controller di dominio Windows Server 2012 e Windows Server 2012 R2 oppure eseguire solo i controller di dominio Windows Server 2016 e versioni successive.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Le password vulnerabili vengono accettate, ma non devono essere

Questo problema può avere diverse cause.

1. Gli agenti del controller di dominio eseguono una versione di anteprima pubblica del software scaduta. Vedere [l'anteprima pubblica. il software dell'agente DC è scaduto](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. Gli agenti del controller di dominio non possono scaricare un criterio oppure non è in grado di decrittografare i criteri esistenti. Verificare la presenza di possibili cause negli argomenti precedenti.

1. La modalità di imposizione dei criteri password è ancora impostata su Controllo. Se questa configurazione è attiva, riconfigurarla per applicare usando il portale di Azure AD password protection. Vedere [Enable Password Protection](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. I criteri password sono stati disabilitati. Se questa configurazione è attiva, riconfigurarla per abilitarla tramite il portale di Azure AD password protection. Vedere [Enable Password Protection](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Il software dell'agente DC non è stato installato in tutti i controller di dominio del dominio. In questa situazione è difficile garantire che i client Windows remoti rivoltino a un particolare controller di dominio durante un'operazione di modifica della password. Se si ritiene di avere come destinazione un particolare controller di dominio in cui è installato il software dell'agente di controller di dominio, è possibile verificare eseguendo il doppio controllo del registro eventi di amministrazione dell'agente del controller di dominio: indipendentemente dal risultato, sarà presente almeno un evento per documentare il risultato della password convalida. Se non è presente alcun evento per l'utente la cui password è stata modificata, la modifica della password è stata probabilmente elaborata da un controller di dominio diverso.

   In alternativa, provare a usare le password setting\changing mentre si è connessi direttamente a un controller di dominio in cui è installato il software dell'agente DC. Questa tecnica non è consigliata per i domini di produzione Active Directory.

   Sebbene la distribuzione incrementale del software dell'agente di controller di dominio sia supportata in conformità a queste limitazioni, Microsoft consiglia vivamente di installare il software dell'agente DC su tutti i controller di dominio di un dominio non appena possibile.

1. È possibile che l'algoritmo di convalida della password funzioni come previsto. Vedere [come vengono valutate le password](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil. exe non è in grado di impostare una password di ripristino servizi directory debole

Active Directory convaliderà sempre una nuova password della modalità di ripristino dei servizi directory per verificare che soddisfi i requisiti di complessità delle password del dominio; Questa convalida chiama anche le dll del filtro password come Azure AD la protezione delle password. Se la nuova password della modalità ripristino servizi directory viene rifiutata, viene restituito il messaggio di errore seguente:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Quando Azure AD Password Protection registra gli eventi del registro eventi di convalida della password per una password Active Directory ripristino servizi directory, si prevede che i messaggi del registro eventi non includano un nome utente. Questo comportamento si verifica perché l'account della modalità ripristino servizi directory è un account locale che non fa parte del dominio Active Directory effettivo.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>La promozione della replica del controller di dominio non riesce a causa di una password ripristino servizi directory

Durante il processo di promozione del controller di dominio, la nuova password della modalità di ripristino dei servizi directory verrà inviata a un controller di dominio esistente nel dominio per la convalida. Se la nuova password della modalità ripristino servizi directory viene rifiutata, viene restituito il messaggio di errore seguente:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Analogamente al problema precedente, qualsiasi evento del risultato di convalida della password di Azure AD password protection avrà nomi utente vuoti per questo scenario.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>L'abbassamento di livello del controller di dominio non riesce a causa di una password amministratore locale

È possibile abbassare di livello un controller di dominio che è ancora in esecuzione nel software dell'agente del controller di dominio. Gli amministratori devono tenere tuttavia presente il fatto che durante la procedura di abbassamento di livello il software dell'agente del controller di dominio continua a imporre i criteri password correnti. La nuova password dell'account amministratore locale (specificata come parte dell'operazione di abbassamento di livello) viene convalidata come qualsiasi altra password. Microsoft consiglia di scegliere password sicure per gli account amministratore locale come parte di una procedura di abbassamento di livello del controller di dominio.

Al termine dell'abbassamento di livello e quando il controller di dominio è stato riavviato ed è di nuovo in esecuzione come normale server membro, il software dell'agente del controller di dominio torna a operare in modalità passiva. Può quindi essere disinstallato in qualsiasi momento.

## <a name="booting-into-directory-services-repair-mode"></a>Avvio in modalità di ripristino dei servizi directory

Se il controller di dominio viene avviato in modalità ripristino servizi directory, la dll del filtro password agente controller di dominio rileva questa condizione e causerà la disabilitazione di tutte le attività di convalida o di applicazione della password, indipendentemente dal criterio attualmente attivo configurazione. La dll del filtro della password dell'agente controller di dominio registrerà un evento di avviso 10023 nel registro eventi di amministrazione, ad esempio:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>Il software dell'agente DC di anteprima pubblica è scaduto

Durante il periodo di anteprima pubblica di Azure AD Password Protection, il software dell'agente DC è stato hardcoded per arrestare l'elaborazione delle richieste di convalida delle password nelle date seguenti:

* La versione 1.2.65.0 smetterà di elaborare le richieste di convalida delle password il 1 2019 settembre.
* Versione 1.2.25.0 e precedente arrestata elaborazione delle richieste di convalida delle password il 1 2019 luglio.

Come si avvicina la scadenza, tutte le versioni dell'agente del controller di dominio con limitazioni temporali generano un evento 10021 nel registro eventi di amministrazione dell'agente del controller di dominio al momento dell'avvio, simile al seguente:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Una volta superata la scadenza, tutte le versioni dell'agente del controller di dominio con limitazioni temporali generano un evento 10022 nel registro eventi di amministrazione dell'agente del controller di dominio al momento dell'avvio, simile al seguente:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Poiché la scadenza viene controllata solo all'avvio iniziale, è possibile che questi eventi non vengano visualizzati fino a quando la scadenza del calendario non è stata superata. Una volta riconosciuta la scadenza, nessun effetto negativo sul controller di dominio o sull'ambiente più grande si verificherà solo se tutte le password verranno approvate automaticamente.

> [!IMPORTANT]
> Microsoft consiglia di aggiornare immediatamente gli agenti del controller di dominio di anteprima pubblica scaduti alla versione più recente.

Un modo semplice per individuare gli agenti DC nell'ambiente che devono essere aggiornati è eseguire il `Get-AzureADPasswordProtectionDCAgent` cmdlet, ad esempio:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

Per questo argomento, il campo SoftwareVersion è ovviamente la proprietà chiave da esaminare. È anche possibile usare il filtro di PowerShell per filtrare gli agenti del controller di dominio che sono già alla versione Baseline obbligatoria, ad esempio:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Il software proxy per la protezione Azure AD password non è limitato da tempo in alcuna versione. Microsoft consiglia comunque di aggiornare gli agenti controller di dominio e proxy alle versioni più recenti non appena vengono rilasciati. Il `Get-AzureADPasswordProtectionProxy` cmdlet può essere utilizzato per trovare agenti proxy che richiedono aggiornamenti, in modo analogo all'esempio precedente per gli agenti DC.

Per ulteriori informazioni sulle procedure di aggiornamento specifiche, vedere [aggiornamento dell'agente del controller](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) di dominio e [aggiornamento dell'agente proxy](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-agent) .

## <a name="emergency-remediation"></a>Correzione di emergenza

Se si verifica una situazione in cui il servizio agente del controller di dominio provoca problemi, tale servizio può essere arrestato immediatamente. La DLL di filtro dell'agente del controller di dominio tenta ancora di chiamare il servizio non in esecuzione e registra eventi di avviso (10012, 10013), ma durante questo periodo di tempo tutte le password in ingresso vengono accettate. Il servizio agente del controller di dominio può quindi essere configurato anche tramite Gestione controllo servizi di Windows con tipo di avvio "Disabilitato" in base alle esigenze.

Un'altra misura correttiva può essere quella di impostare la modalità di abilitazione su No nel portale di Password di protezione di Azure AD. Dopo aver scaricato i criteri aggiornati, ogni servizio agente del controller di dominio passa a una modalità inattiva in cui tutte le password vengono accettate così come sono. Per altre informazioni, vedere [Modalità di imposizione](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="removal"></a>Rimozione

Se si decide di disinstallare il software di protezione Azure AD password e di pulire tutti gli stati correlati dal dominio o dalla foresta, questa attività può essere eseguita attenendosi alla procedura seguente:

> [!IMPORTANT]
> È importante eseguire i passaggi nell'ordine specificato. Se un'istanza del servizio proxy viene lasciata in esecuzione, ne viene periodicamente ricreare il relativo oggetto serviceConnectionPoint. Se un'istanza del servizio agente del controller di dominio viene lasciata in esecuzione, ne vengono ricreati periodicamente l'oggetto serviceConnectionPoint e lo stato sysvol.

1. Disinstallare il software del proxy da tutti i computer. Questo passaggio **non** richiede un riavvio.
2. Disinstallare il software dell'agente del controller di dominio da tutti i controller di dominio. Questo passaggio **richiede** un riavvio.
3. Rimuovere manualmente tutti i punti di connessione del servizio proxy in ogni contesto dei nomi di dominio. Il percorso di questi oggetti può essere individuato con il comando di PowerShell per Active Directory seguente:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Non omettere l'asterisco ("*") alla fine del valore della variabile $keywords.

   L'oggetto risultante individuabile tramite il comando `Get-ADObject` può quindi essere inoltrato tramite pipe a `Remove-ADObject` oppure può essere eliminato manualmente.

4. Rimuovere manualmente tutti i punti di connessione dell'agente del controller di dominio in ogni contesto dei nomi di dominio. Potrebbero essere presenti uno di questi oggetti per controller di dominio nell'insieme di strutture, a seconda della frequenza con cui è stato distribuito il software. Il percorso dell'oggetto può essere individuato con il comando di PowerShell per Active Directory seguente:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   L'oggetto risultante individuabile tramite il comando `Get-ADObject` può quindi essere inoltrato tramite pipe a `Remove-ADObject` oppure può essere eliminato manualmente.

   Non omettere l'asterisco ("*") alla fine del valore della variabile $keywords.

5. Rimuovere manualmente lo stato di configurazione a livello di foresta. Lo stato di configurazione a livello di foresta viene mantenuto in un contenitore nel contesto dei nomi della configurazione di Active Directory. Può essere individuato ed eliminato in questo modo:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Rimuovere manualmente tutto lo stato correlato a sysvol eliminando manualmente la cartella seguente e tutto il suo contenuto:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Se necessario, è possibile accedere a questo percorso anche in locale in un controller di dominio specificato. il percorso predefinito può essere simile al seguente:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Il percorso è diverso se la condivisione sysvol è stata configurata in un percorso non predefinito.

## <a name="next-steps"></a>Passaggi successivi

[Domande frequenti per Password di protezione di Azure AD](howto-password-ban-bad-on-premises-faq.md)

Per altre informazioni sugli elenchi personalizzati di password escluse, vedere l'articolo [Eliminare le password non appropriate nell'organizzazione](concept-password-ban-bad.md)
