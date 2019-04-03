---
title: Risoluzione dei problemi di protezione tramite password di Azure AD - Azure Active Directory
description: Comprendere i problemi comuni di Azure AD password protection
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
ms.openlocfilehash: 108ead982529d2ac6549cceffd9d2177ab6456bf
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863178"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Risoluzione dei problemi relativi a Password di protezione di Azure AD

Dopo la distribuzione di Password di protezione di Azure AD, può essere necessario risolvere alcuni problemi. Questo articolo presenta informazioni dettagliate su alcuni passaggi comuni per la risoluzione dei problemi.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>L'agente controller di dominio non è possibile posizionare un proxy nella directory

Il sintomo principale di questo problema è 30017 eventi nel registro eventi di amministrazione di agente controller di dominio.

La causa del problema è che un proxy non è ancora stato registrato. Se è stato registrato un proxy, potrebbe esserci un ritardo a causa della latenza di replica di Active Directory fino a quando un agente controller di dominio specifico è in grado di visualizzare tale proxy.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>L'agente controller di dominio non è in grado di comunicare con un proxy

Il sintomo principale di questo problema è 30018 eventi nel registro eventi di amministrazione di agente controller di dominio. Le cause possono essere diverse:

1. L'agente controller di dominio si trova in una parte della rete che non consente la connettività di rete per la proxy(s) registrati. Questo problema potrebbe pertanto essere expected\benign purché altri agenti di DC possano comunicare con il proxy(s) per scaricare i criteri password di Azure, che viene quindi ottenuto dal controller di dominio isolato tramite la replica dei file dei criteri nella condivisione sysvol.

1. Il computer host proxy sta bloccando l'accesso all'endpoint di BizTalk mapper di endpoint RPC (porta 135)

   Il programma di installazione di Proxy di protezione delle Password di Azure AD crea automaticamente una regola in ingresso di Windows Firewall che consenta l'accesso alla porta 135. Se questa regola in un secondo momento viene eliminata o disabilitata, gli agenti di DC saranno non riesce a comunicare con il servizio Proxy. Se il comando predefinito Windows Firewall è stata disabilitata al posto di un altro prodotto di firewall, è necessario configurare il firewall per consentire l'accesso alla porta 135.

1. Il computer host proxy sta bloccando l'accesso all'endpoint RPC (dinamico o statico) in ascolto dal servizio Proxy

   Il programma di installazione di Proxy di protezione delle Password di Azure AD crea automaticamente un Firewall di Windows regola in ingresso che consenta l'accesso alle porte in ingresso in ascolto il servizio Proxy di protezione delle Password di Azure AD. Se questa regola in un secondo momento viene eliminata o disabilitata, gli agenti di DC saranno non riesce a comunicare con il servizio Proxy. Se il comando predefinito Windows Firewall è stata disabilitata al posto di un altro prodotto di firewall, che è necessario configurare firewall per consentire l'accesso alle porte in ingresso in ascolto il servizio Proxy di protezione delle Password di Azure AD. Questa configurazione possa essere resi più specifica se il servizio Proxy è stato configurato per restare in attesa su una porta RPC statica specifica (usando il `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet).

## <a name="the-proxy-service-can-receive-calls-from-dc-agents-in-the-domain-but-is-unable-to-communicate-with-azure"></a>Il servizio Proxy può ricevere chiamate dagli agenti di controller di dominio nel dominio ma non è in grado di comunicare con Azure

1. Verificare che il computer proxy ha la connettività agli endpoint elencato nella [requisiti di distribuzione](howto-password-ban-bad-on-premises-deploy.md).

1. Verificare che la foresta e tutti i proxy server sono registrati con lo stesso tenant di Azure.

   È possibile verificarlo eseguendo il `Get-AzureADPasswordProtectionProxy` e `Get-AzureADPasswordProtectionDCAgent` cmdlet di PowerShell, quindi confrontare il `AzureTenant` proprietà di ogni elemento restituito. Per un corretto funzionamento questi deve essere lo stesso all'interno di una foresta, in tutti gli agenti di controller di dominio e server proxy.

   Se esiste una condizione di mancata corrispondenza di registrazione tenant di Azure, può essere ripristinato eseguendo il `Register-AzureADPasswordProtectionProxy` e/o `Register-AzureADPasswordProtectionForest` i cmdlet di PowerShell in base alle esigenze, assicurandosi di utilizzare le credenziali dello stesso tenant di Azure per tutte le registrazioni.

## <a name="the-dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files-and-other-state"></a>L'agente controller di dominio non è in grado di crittografare o decrittografare il file dei criteri password e altro stato

Questo problema può risolversi con un'ampia gamma di sintomi ma in genere ha una causa radice comune.

Protezione di Password di AD Azure ha una dipendenza critica sulla funzionalità di crittografia e decrittografia fornito dal servizio distribuzione chiavi di Microsoft, che è disponibile nei controller di dominio che esegue Windows Server 2012 e versioni successive. Il servizio distribuzione CHIAVI deve essere abilitato e funzionali in tutti i Windows Server 2012 e versioni successive controller di dominio in un dominio.

Per impostazione predefinita il KDS modalità di avvio del servizio del servizio è configurata come manuale (avvio Trigger). Questa configurazione indica che la prima volta che un client prova a usare il servizio, viene avviato su richiesta. Questa modalità di avvio del servizio predefinito è accettabile per la protezione di Password di Azure AD a funzionare.

Se la modalità di avvio del servizio distribuzione CHIAVI del servizio è stata configurata su Disabled, questa configurazione deve essere corretta prima che la protezione con Password di Azure Active Directory funzionino correttamente.

Un semplice test per risolvere questo problema consiste nell'avviare manualmente il servizio del servizio distribuzione CHIAVI, tramite la console MMC di gestione del servizio, o usando altri strumenti di gestione del servizio (ad esempio, eseguire "net start kdssvc" da una console del prompt dei comandi). Il servizio distribuzione CHIAVI è previsto vengano avviate correttamente e restare in esecuzione.

La causa radice più comune per il servizio distribuzione CHIAVI sia in grado di avviare è che l'oggetto controller di dominio Active Directory si trova di fuori l'unità Organizzativa dei controller di dominio predefinito. Questa configurazione non è supportata dal servizio distribuzione CHIAVI e non viene imposto da Azure AD Password di protezione. La correzione per questa condizione consiste nello spostare l'oggetto controller di dominio in un percorso con il valore predefinito dell'unità Organizzativa controller di dominio.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Password vulnerabili vengono accettate, ma non deve essere

Questo problema può avere cause diverse.

1. L'agente di DC / non è possibile scaricare i criteri o non è in grado di decrittografare i criteri esistenti. Cercare le cause possibili negli argomenti precedenti.

1. La modalità di imposizione dei criteri password è ancora impostata su Controllo. Se questa configurazione è in effetti, riconfigurarlo a Imponi usando il portale di Azure AD Password di protezione. Visualizzare [abilitare la Password di protezione](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. I criteri password sono stati disabilitati. Se questa configurazione è in effetti, riconfigurarlo abilitato usando il portale di Azure AD Password di protezione. Visualizzare [abilitare la Password di protezione](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Non è installato il software dell'agente controller di dominio su tutti i controller di dominio nel dominio. In questo caso, è difficile garantire che i client remoti di Windows come destinazione un particolare controller di dominio durante un'operazione di modifica della password. Se pensi è stato assegnato correttamente un controller di dominio particolare in cui è installato il software dell'agente controller di dominio, è possibile verificare ricontrollare il log eventi di amministrazione agente controller di dominio: indipendentemente dal risultato, sarà presente almeno un evento per documentare il risultato della password convalida. Se è presente alcun evento per l'utente la cui password viene modificata, la modifica della password è stata probabilmente elaborata da un controller di dominio diverso.

   Come un test di alternativo, provare a setting\changing password mentre si è connessi direttamente a un controller di dominio in cui è installato il software dell'agente controller di dominio. Questa tecnica non è consigliabile per i domini di Active Directory di produzione.

   Mentre la distribuzione incrementale del software agente controller di dominio è supportata soggette a queste limitazioni, Microsoft consiglia vivamente che sia installato il software dell'agente controller di dominio su tutti i controller di dominio in un dominio appena possibile.

1. L'algoritmo di convalida delle password può effettivamente essere funzionino come previsto. Visualizzare [modo in cui vengono valutate le password](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Modalità di ripristino dei servizi directory

Se il controller di dominio è avviato in modalità ripristino servizi Directory, il servizio agente controller di dominio rileva questa condizione e causerà tutti la convalida della password o le attività di imposizione deve essere disabilitata, indipendentemente dalla configurazione dei criteri attualmente attivo.

## <a name="emergency-remediation"></a>Correzione di emergenza

Se si verifica una situazione in cui il servizio agente del controller di dominio provoca problemi, tale servizio può essere arrestato immediatamente. La DLL di filtro dell'agente del controller di dominio tenta ancora di chiamare il servizio non in esecuzione e registra eventi di avviso (10012, 10013), ma durante questo periodo di tempo tutte le password in ingresso vengono accettate. Il servizio agente del controller di dominio può quindi essere configurato anche tramite Gestione controllo servizi di Windows con tipo di avvio "Disabilitato" in base alle esigenze.

Un'altra misura correttiva può essere quella di impostare la modalità di abilitazione su No nel portale di Password di protezione di Azure AD. Dopo aver scaricato i criteri aggiornati, ogni servizio agente del controller di dominio passa a una modalità inattiva in cui tutte le password vengono accettate così come sono. Per altre informazioni, vedere [Modalità di imposizione](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Abbassamento di livello del controller di dominio

È possibile abbassare di livello un controller di dominio che è ancora in esecuzione nel software dell'agente del controller di dominio. Gli amministratori devono tenere tuttavia presente il fatto che durante la procedura di abbassamento di livello il software dell'agente del controller di dominio continua a imporre i criteri password correnti. La nuova password dell'account amministratore locale (specificata come parte dell'operazione di abbassamento di livello) viene convalidata come qualsiasi altra password. Microsoft consiglia di scegliere password sicure per gli account amministratore locali nell'ambito di una procedura di abbassamento di livello del controller di dominio. Tuttavia, la convalida della nuova password dell'account amministratore locale da parte del software dell'agente del controller di dominio può avere un forte impatto sulle procedure operative di abbassamento di livello preesistenti.

Al termine dell'abbassamento di livello e quando il controller di dominio è stato riavviato ed è di nuovo in esecuzione come normale server membro, il software dell'agente del controller di dominio torna a operare in modalità passiva. Può quindi essere disinstallato in qualsiasi momento.

## <a name="removal"></a>Rimozione

Se si decide di disinstallare il software di protezione di password di Azure AD e la pulizia dello stato di tutte le relative dai domini e foreste, questa attività può essere eseguita usando la procedura seguente:

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

4. Rimuovere manualmente tutti i punti di connessione dell'agente del controller di dominio in ogni contesto dei nomi di dominio. Potrebbe essere presente un questi oggetti per ogni controller di dominio nella foresta, a seconda del contesto in cui è stato distribuito il software. Il percorso dell'oggetto può essere individuato con il comando di PowerShell per Active Directory seguente:

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

[Domande frequenti per la protezione con Password di Azure AD](howto-password-ban-bad-on-premises-faq.md)

Per altre informazioni sugli elenchi personalizzati di password escluse, vedere l'articolo [Eliminare le password non appropriate nell'organizzazione](concept-password-ban-bad.md)
