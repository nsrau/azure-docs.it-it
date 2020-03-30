---
title: Risolvere i problemi relativi alla protezione con password di Azure AD localeTroubleshoot on-premises Azure AD Password Protection
description: Informazioni su come risolvere i problemi relativi a Protezione password di Azure AD per un ambiente Servizi di dominio Active Directory locale
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79ebf543a3880a4f2c8ee8c0d706c268ef3f08d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263647"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Risoluzione dei problemi: Protezione password di Azure AD localeTroubleshoot: On-premises Azure AD Password Protection

Dopo la distribuzione di Password di protezione di Azure AD, può essere necessario risolvere alcuni problemi. Questo articolo presenta informazioni dettagliate su alcuni passaggi comuni per la risoluzione dei problemi.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>L'agente controller di dominio non è in grado di individuare un proxy nella directory

Il sintomo principale di questo problema è 30017 eventi nel registro eventi di amministrazione dell'agente di controller di dominio.

La causa abituale di questo problema è che un proxy non è ancora stato registrato. Se è stato registrato un proxy, potrebbe verificarsi un ritardo dovuto alla latenza di replica di Active Directory fino a quando un determinato agente controller di dominio non è in grado di visualizzare tale proxy.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>L'agente controller di dominio non è in grado di comunicare con un proxy

Il sintomo principale di questo problema è 30018 eventi nel registro eventi di amministrazione dell'agente di controller di dominio. Questo problema può avere diverse possibili cause:

1. L'agente controller di dominio si trova in una parte isolata della rete che non consente la connettività di rete ai proxy registrati. Questo problema può essere benigno fino a quando altri agenti controller di dominio possono comunicare con i proxy per scaricare i criteri password da Azure.This problem may be benign as long as other DC agents can communicate with the proxy(s) per scaricare i criteri password da Azure. Una volta scaricati, tali criteri verranno ottenuti dal controller di dominio isolato tramite la replica dei file di criteri nella condivisione sysvol.

1. Il computer host proxy sta bloccando l'accesso all'endpoint del mapping degli endpoint RPC (porta 135)

   Il programma di installazione del proxy di protezione password di Azure AD crea automaticamente una regola in ingresso di Windows Firewall che consente l'accesso alla porta 135.The Azure AD Password Protection Proxy installer automatically creates a Windows Firewall inbound rule that allows access to port 135. Se questa regola viene eliminata o disabilitata in un secondo momento, gli agenti controller di dominio non saranno in grado di comunicare con il servizio proxy. Se Windows Firewall incorporato è stato disabilitato al posto di un altro prodotto firewall, è necessario configurare tale firewall per consentire l'accesso alla porta 135.

1. Il computer host proxy sta bloccando l'accesso all'endpoint RPC (dinamico o statico) ascoltato dal servizio proxy

   Il programma di installazione del proxy di protezione password di Azure AD crea automaticamente una regola in ingresso di Windows Firewall che consente l'accesso a tutte le porte in ingresso ascoltate dal servizio proxy di protezione password di Azure AD. Se questa regola viene eliminata o disabilitata in un secondo momento, gli agenti controller di dominio non saranno in grado di comunicare con il servizio proxy. Se Windows Firewall incorporato è stato disabilitato al posto di un altro prodotto firewall, è necessario configurare tale firewall per consentire l'accesso a tutte le porte in ingresso ascoltate dal servizio Proxy di protezione password di Azure AD. Questa configurazione può essere resa più specifica se il servizio proxy è stato `Set-AzureADPasswordProtectionProxyConfiguration` configurato per l'ascolto su una porta RPC statica specifica (utilizzando il cmdlet).

1. Il computer host proxy non è configurato per consentire ai controller di dominio di accedere al computer. Questo comportamento viene controllato tramite l'assegnazione dei privilegi utente "Accedi al computer dalla rete". A tutti i controller di dominio in tutti i domini dell'insieme di strutture deve essere concesso questo privilegio. Questa impostazione è spesso vincolata come parte di un maggiore sforzo di protezione avanzata della rete.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Il servizio proxy non è in grado di comunicare con Azure

1. Verificare che la macchina proxy disponga di connettività agli endpoint elencati nei requisiti di [distribuzione.](howto-password-ban-bad-on-premises-deploy.md)

1. Verificare che la foresta e tutti i server proxy siano registrati nello stesso tenant di Azure.Ensure that the forest and all proxy servers are registered against the same Azure tenant.

   È possibile controllare questo `Get-AzureADPasswordProtectionProxy` requisito eseguendo i cmdlet `Get-AzureADPasswordProtectionDCAgent` `AzureTenant` e PowerShell, quindi confrontare la proprietà di ogni elemento restituito. Per un'operazione corretta, il nome del tenant segnalato deve essere lo stesso in tutti gli agenti controller di dominio e i server proxy.

   Se esiste una condizione di mancata corrispondenza della registrazione `Register-AzureADPasswordProtectionProxy` tenant di `Register-AzureADPasswordProtectionForest` Azure, questo problema può essere risolto eseguendo i cmdlet e/o PowerShell in base alle esigenze, assicurandosi di usare le credenziali dello stesso tenant di Azure per tutte le registrazioni.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>L'agente controller di dominio non è in grado di crittografare o decrittografare i file dei criteri password

Azure AD Password Protection ha una dipendenza critica dalla funzionalità di crittografia e decrittografia fornita dal servizio di distribuzione delle chiavi Microsoft.Azure AD Password Protection has a critical dependency on the encryption and decryption functionality fornito by the Microsoft Key Distribution Service. Errori di crittografia o decrittografia possono manifestarsi con una varietà di sintomi e hanno diverse cause potenziali.

1. Verificare che il servizio KDS sia abilitato e funzionante in tutti i controller di dominio Windows Server 2012 e versioni successive in un dominio.

   Per impostazione predefinita, la modalità di avvio del servizio KDS è configurata come Manuale (Avvio trigger). Questa configurazione significa che la prima volta che un client tenta di utilizzare il servizio, viene avviato su richiesta. Questa modalità di avvio del servizio predefinita è accettabile per il funzionamento di Azure AD Password Protection.This default service start mode is acceptable for Azure AD Password Protection to work.

   Se la modalità di avvio del servizio KDS è stata configurata su Disabilitato, questa configurazione deve essere corretta prima che Azure AD Password Protection funzioni correttamente.

   Un semplice test per questo problema consiste nell'avviare manualmente il servizio KDS, tramite la console MMC Gestione servizi, o utilizzando altri strumenti di gestione (ad esempio, eseguire "net start kdssvc" da una console del prompt dei comandi). Il servizio KDS dovrebbe avviarsi correttamente e rimanere in esecuzione.

   La causa principale più comune per il servizio KDS che non è in grado di avviare è che l'oggetto controller di dominio Active Directory si trova all'esterno dell'unità organizzativa Controller di dominio predefinito. Questa configurazione non è supportata dal servizio KDS e non è una limitazione imposta da Azure AD Password Protection. La correzione di questa condizione consiste nello spostare l'oggetto controller di dominio in un percorso nell'unità organizzativa Controller di dominio predefinita.

1. Modifica del formato del buffer crittografato KDS incompatibile da Windows Server 2012 R2 a Windows Server 2016

   Una correzione di sicurezza KDS è stata introdotta in Windows Server 2016 che modifica il formato dei buffer crittografati KDS; questi buffer a volte non riescono a decrittografare su Windows Server 2012 e Windows Server 2012 R2. La direzione inversa è corretta - i buffer che sono kDS-crittografato su Windows Server 2012 e Windows Server 2012 R2 sarà sempre decrittografare correttamente su Windows Server 2016 e versioni successive. Se i controller di dominio nei domini di Active Directory eseguono una combinazione di questi sistemi operativi, potrebbero essere segnalati errori occasionali di decrittografia di Azure AD Password Protection. Non è possibile prevedere con precisione i tempi o i sintomi di questi errori data la natura della correzione di sicurezza e dato che non è deterministico quale agente del controller di dominio di Azure AD Password Protection in cui il controller di dominio crittograferà i dati in un determinato momento.

   Microsoft sta esaminando una correzione per questo problema, ma non è ancora disponibile alcun ETA. Nel frattempo, non esiste una soluzione alternativa per questo problema se non per non eseguire una combinazione di questi sistemi operativi incompatibili nei domini di Active Directory. In altre parole, è necessario eseguire solo controller di dominio Windows Server 2012 e Windows Server 2012 R2, OPPURE è necessario eseguire solo Windows Server 2016 e versioni precedenti.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Le password deboli vengono accettate, ma non

Questo problema può avere diverse cause.

1. Gli agenti del controller di dominio eseguono una versione software di anteprima pubblica scaduta. Vedere [Il software dell'agente di anteprima pubblica del controller di dominio è scaduto.](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired)

1. Gli agenti controller di dominio non possono scaricare un criterio o non sono in grado di decrittografare i criteri esistenti. Verificare la presenza di possibili cause negli argomenti precedenti.

1. La modalità di imposizione dei criteri password è ancora impostata su Controllo. Se questa configurazione è attiva, riconfigurarla su Applica tramite il portale di Protezione password di Azure AD. Per ulteriori informazioni, vedere [Modalità di funzionamento](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. I criteri password sono stati disabilitati. Se questa configurazione è attiva, riconfigurarla per abilitarla tramite il portale di protezione delle password di Azure AD. Per ulteriori informazioni, vedere [Modalità di funzionamento](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Non è stato installato il software dell'agente di controller di dominio in tutti i controller di dominio nel dominio. In questo caso, è difficile garantire che i client Windows remoti destinati a un particolare controller di dominio durante un'operazione di modifica della password. Se si ritiene di aver scelto come target un determinato controller di dominio in cui è installato il software dell'agente controller di dominio, è possibile verificare controllando il registro eventi dell'agente controller di dominio: indipendentemente dal risultato, sarà presente almeno un evento per documentare il risultato della password Convalida. Se non è presente alcun evento per l'utente la cui password viene modificata, è probabile che la modifica della password sia stata elaborata da un controller di dominio diverso.

   Come test alternativo, provare a impostare, modificare le password mentre si è connessi direttamente a un controller di dominio in cui è installato il software dell'agente controller di dominio. Questa tecnica non è consigliata per i domini di Active Directory di produzione.

   Mentre la distribuzione incrementale del software agente controller di dominio è supportata in base a queste limitazioni, Microsoft consiglia vivamente che il software dell'agente controller di dominio sia installato in tutti i controller di dominio in un dominio il prima possibile.

1. L'algoritmo di convalida della password potrebbe effettivamente funzionare come previsto. Vedere [Come vengono valutate le password](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe non riesce a impostare una password DSRM debole

Active Directory convaliderà sempre una nuova password della modalità di ripristino dei servizi directory per assicurarsi che soddisfi i requisiti di complessità delle password del dominio. questa convalida chiama anche le dll di filtro delle password come Azure AD Password Protection. Se la nuova password DSRM viene rifiutata, viene restituito il seguente messaggio di errore:

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

Quando Azure AD Password Protection registra gli eventi del registro eventi di convalida della password per una password DSRM di Active Directory, è previsto che i messaggi del log eventi non includano un nome utente. Questo comportamento si verifica perché l'account DSRM è un account locale che non fa parte del dominio di Active Directory effettivo.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>L'innalzamento di livello della replica del controller di dominio non riesce a causa di una password DSRM debole

Durante il processo di innalzamento di livello del controller di dominio, la nuova password della modalità ripristino servizi directory verrà inviata a un controller di dominio esistente nel dominio per la convalida. Se la nuova password DSRM viene rifiutata, viene restituito il seguente messaggio di errore:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Come nel problema precedente, qualsiasi evento di convalida password di Azure AD Password Protection avrà nomi utente vuoti per questo scenario.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>Abbassamento di livello del controller di dominio non riesce a causa di una password di amministratore locale deboleDomain controller demotion fails due to a weak local Administrator password

È possibile abbassare di livello un controller di dominio che è ancora in esecuzione nel software dell'agente del controller di dominio. Gli amministratori devono tenere tuttavia presente il fatto che durante la procedura di abbassamento di livello il software dell'agente del controller di dominio continua a imporre i criteri password correnti. La nuova password dell'account amministratore locale (specificata come parte dell'operazione di abbassamento di livello) viene convalidata come qualsiasi altra password. Microsoft consiglia di scegliere password sicure per gli account amministratore locale come parte di una procedura di abbassamento dei controller di dominio.

Al termine dell'abbassamento di livello e quando il controller di dominio è stato riavviato ed è di nuovo in esecuzione come normale server membro, il software dell'agente del controller di dominio torna a operare in modalità passiva. Può quindi essere disinstallato in qualsiasi momento.

## <a name="booting-into-directory-services-repair-mode"></a>Avvio in modalità di ripristino dei servizi directory

Se il controller di dominio viene avviato in modalità ripristino servizi directory, la dll del filtro password dell'agente controller di dominio rileva questa condizione e causerà la disabilitazione di tutte le attività di convalida o imposizione della password, indipendentemente dal criterio attualmente attivo Conﬁgurazione. La dll del filtro password dell'agente controller di dominio registrerà un evento di avviso 10023 nel registro eventi di amministrazione, ad esempio:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>Il software dell'agente del controller di dominio di anteprima pubblico è scaduto

Durante il periodo di anteprima pubblica di Azure AD Password Protection, il software dell'agente controller di dominio è stato hardcoded per interrompere l'elaborazione delle richieste di convalida delle password nelle date seguenti:During the Azure AD Password Protection public preview period, the DC agent software was hard-coded to stop processing password validation requests on the following dates:

* La versione 1.2.65.0 interromperà l'elaborazione delle richieste di convalida delle password il 1 settembre 2019.
* La versione 1.2.25.0 e precedenti hanno interrotto l'elaborazione delle richieste di convalida delle password il 1 luglio 2019.

Con l'avvicinarsi della scadenza, tutte le versioni dell'agente di CAEM limitate nel tempo genereranno un evento 10021 nel registro eventi di amministrazione dell'agente controller di dominio all'avvio simile al seguente:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Una volta superata la scadenza, tutte le versioni dell'agente controller di dominio limitate nel tempo genereranno un evento 10022 nel registro eventi di amministrazione dell'agente controller di dominio all'avvio simile al seguente:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Poiché la scadenza viene controllata solo all'avvio iniziale, questi eventi potrebbero non essere visualizzati fino a molto tempo dopo il termine del calendario. Una volta riconosciuta la scadenza, non si verificheranno effetti negativi sul controller di dominio o sull'ambiente più grande, ad esempio tutte le password, verrà approvato automaticamente.

> [!IMPORTANT]
> Microsoft consiglia che gli agenti di controller di dominio di anteprima pubblica scaduti vengano immediatamente aggiornati alla versione più recente.

Un modo semplice per individuare gli agenti controller di dominio `Get-AzureADPasswordProtectionDCAgent` nell'ambiente che devono essere aggiornati consiste nell'eseguire il cmdlet, ad esempio:

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

Per questo argomento, il campo SoftwareVersion è ovviamente la proprietà chiave da esaminare. È anche possibile usare il filtro di PowerShell per filtrare gli agenti controller di dominio già presenti o meno nella versione di base richiesta, ad esempio:You can also use PowerShell filtering to filter out DC agents that are already at or above the required baseline version, for example:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Il software proxy di protezione password di Azure AD non è limitato nel tempo in qualsiasi versione. Microsoft consiglia comunque di aggiornare sia gli agenti di controller di dominio che gli agenti proxy alle versioni più recenti man mano che vengono rilasciati. Il `Get-AzureADPasswordProtectionProxy` cmdlet può essere utilizzato per trovare gli agenti proxy che richiedono aggiornamenti, simile all'esempio precedente per gli agenti di controller di dominio.

Per ulteriori informazioni sulle procedure di aggiornamento specifiche, vedere [Aggiornamento dell'agente controller di dominio](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) e Aggiornamento del servizio [proxy.](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service)

## <a name="emergency-remediation"></a>Correzione di emergenza

Se si verifica una situazione in cui il servizio agente del controller di dominio provoca problemi, tale servizio può essere arrestato immediatamente. La DLL di filtro dell'agente del controller di dominio tenta ancora di chiamare il servizio non in esecuzione e registra eventi di avviso (10012, 10013), ma durante questo periodo di tempo tutte le password in ingresso vengono accettate. Il servizio agente del controller di dominio può quindi essere configurato anche tramite Gestione controllo servizi di Windows con tipo di avvio "Disabilitato" in base alle esigenze.

Un'altra misura correttiva può essere quella di impostare la modalità di abilitazione su No nel portale di Password di protezione di Azure AD. Dopo aver scaricato i criteri aggiornati, ogni servizio agente del controller di dominio passa a una modalità inattiva in cui tutte le password vengono accettate così come sono. Per ulteriori informazioni, vedere [Modalità di funzionamento](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

## <a name="removal"></a>Rimozione

Se si decide di disinstallare il software di protezione password di Azure AD e di pulire tutto lo stato correlato dai domini e dalla foresta, questa attività può essere eseguita attenendosi alla procedura seguente:If it is decided to uninstall the Azure AD password protection software and cleanup all related state from the domain(s) and forest, this task can be accomplished using the following steps:

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

4. Rimuovere manualmente tutti i punti di connessione dell'agente del controller di dominio in ogni contesto dei nomi di dominio. Nell'insieme di strutture può essere presente uno di questi oggetti per controller di dominio, a seconda dell'ampia distribuzione del software. Il percorso dell'oggetto può essere individuato con il comando di PowerShell per Active Directory seguente:

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
