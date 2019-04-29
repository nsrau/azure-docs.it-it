---
title: Identificare i problemi con la funzionalità di diagnostica di anteprima di Desktop virtuale Windows - Azure
description: Descrive la funzionalità di diagnostica di anteprima di Desktop virtuale Windows e come usarlo.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 6b79a26d63c02dd06b62ea6ad09941f947704dc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870490"
---
# <a name="identify-issues-with-the-diagnostics-feature"></a>Identificare i problemi con la funzionalità di diagnostica

Anteprima di Desktop virtuale Windows offre funzionalità di diagnostica che consente all'amministratore di identificare i problemi tramite una singola interfaccia. I ruoli di Desktop virtuale Windows di un'attività di diagnostica log ogni volta che un utente interagisce con il sistema. Ogni log contiene informazioni rilevanti, ad esempio i ruoli di Desktop virtuale Windows coinvolti nella transazione, i messaggi di errore, le informazioni del tenant e le informazioni utente. Le attività di diagnostica vengono create sia per l'utente finale e le azioni amministrative e possono essere suddivise in tre bucket principali:

* Feed attività di sottoscrizione: l'utente finale attiva queste attività ogni volta che tentano di connettersi a dei feed tramite le applicazioni Desktop remoto Microsoft.
* Le attività di connessione: l'utente finale attiva queste attività ogni volta che tentano di connettersi a RemoteApp o desktop tramite le applicazioni Desktop remoto Microsoft.
* Le attività di gestione: l'amministratore può attivare queste attività ogni volta che esegue operazioni di gestione nel sistema, ad esempio creazione di pool di host, assegnazione di utenti a gruppi di app e la creazione di assegnazioni di ruolo.
  
Le connessioni che non raggiungono Windows Desktop virtuale non verranno visualizzato nei risultati di diagnostica perché il servizio ruolo di diagnostica stessa fa parte di Desktop virtuale Windows. Problemi di connessione di Desktop virtuale di Windows possono verificarsi quando l'utente finale riscontra problemi di connettività di rete.

Per iniziare, [scaricare e importare il modulo Windows PowerShell di Desktop virtuale](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell se non è già presente.

## <a name="diagnose-issues-with-powershell"></a>Diagnosticare i problemi con PowerShell

Diagnostica di Desktop virtuale Windows Usa un solo cmdlet di PowerShell, ma contiene numerosi parametri facoltativi per limitare e isolare i problemi. Le sezioni seguenti elencano i cmdlet è possibile eseguire per diagnosticare i problemi. La maggior parte dei filtri possono essere applicati insieme. I valori racchiusi tra parentesi quadre, ad esempio `<tenantName>`, devono essere sostituiti con i valori che si applicano alle proprie esigenze.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>Recuperare le attività di diagnostica nel tenant

È possibile recuperare le attività di diagnostica, immettere il **Get-RdsDiagnosticActivities** cmdlet. Il cmdlet di esempio seguente restituirà un elenco delle attività di diagnostica, ordinati dal più specifico al meno recente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName>
```

Come per altri cmdlet di Windows PowerShell di Desktop virtuale, è necessario usare il **Nometenant -** parametro per specificare il nome del tenant da usare per la query. Il nome del tenant è applicabile per quasi tutte le query di attività diagnostica.

### <a name="retrieve-detailed-diagnostic-activities"></a>Recuperare le attività di diagnostica dettagliate

Il **-Detailed** parametro fornisce dettagli aggiuntivi per ogni attività diagnostica restituita. Il formato per ogni attività varia a seconda del tipo di attività. Il **-Detailed** parametro possa essere aggiunte a qualsiasi **Get-RdsDiagnosticActivities** esegue una query, come illustrato nell'esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Recuperare un ID attività dall'attività di diagnostica specifico

Il **- ActivityId** parametro restituisce una specifica attività diagnostica se esiste, come illustrato nel cmdlet di esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="filter-diagnostic-activities-by-user"></a>Filtrare le attività di diagnostica dall'utente

Il **- UserName** parametro restituisce un elenco delle attività di diagnostica avviati dall'utente specificato, come illustrato nel cmdlet di esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Il **- UserName** parametro può anche essere combinato con altri parametri di filtro facoltative.

### <a name="filter-diagnostic-activities-by-time"></a>Filtra le attività di diagnostica per ora

È possibile filtrare l'elenco di attività diagnostica restituita con il **- StartTime** e **- EndTime** parametri. Il **- StartTime** parametro restituirà un elenco di attività diagnostica a partire da una data specifica, come illustrato nell'esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

Il **- EndTime** parametro può essere aggiunto a un cmdlet con il **- StartTime** parametro per specificare un determinato periodo di tempo si vuole ricevere i risultati per. Il cmdlet di esempio seguente restituirà un elenco delle attività di diagnostica nell'intervallo tra 1 ° agosto e il 10 agosto.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Il **- StartTime** e **- EndTime** parametri possono anche essere combinati con altri parametri di filtro facoltative.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrare le attività di diagnostica dal tipo di attività

È anche possibile filtrare le attività di diagnostica dal tipo di attività con il **- ActivityType** parametro. Il cmdlet seguente restituirà un elenco delle connessioni degli utenti finali:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

Il cmdlet seguente restituirà un elenco delle attività di gestione amministratore:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

Il **Get-RdsDiagnosticActivities** cmdlet attualmente non supporta l'indicazione Feed come la proprietà ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrare le attività di diagnostica dal risultato

È possibile filtrare l'elenco di attività diagnostica restituita dal risultato con il **-risultato** parametro. Il cmdlet di esempio seguente restituirà un elenco delle attività di diagnostica ha esito positivo.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

Il cmdlet di esempio seguente restituirà un elenco di attività di diagnostica non riuscite.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

Il **-risultato** parametro può anche essere combinato con altri parametri di filtro facoltative.

## <a name="common-error-scenarios"></a>Scenari di errore comuni

Scenari di errore sono classificati in interno al servizio ed esterno a Windows Desktop virtuale.

* Errore interno: specifica gli scenari che non possono essere attenuati dall'amministratore tenant e devono essere risolti come richiesta di supporto. Quando la generazione di un ticket di fornire l'ID attività, nome del tenant e l'intervallo di tempo approssimativo il problema si è verificato.
* Problema esterno: riguardano scenari che possono essere ridotti dall'amministratore di sistema. Questi sono esterni a Windows Desktop virtuale.

Nella tabella seguente elenca gli amministratori potrebbero riscontrare alcuni errori comuni.

>[!NOTE]
>Questa versione di anteprima non includono una categorizzazione completata degli errori e verrà aggiornato a intervalli regolari. Per verificare che le informazioni più aggiornate, assicurarsi di visitare regolarmente questo articolo almeno una volta al mese.

### <a name="external-management-error-codes"></a>Codici di errore di gestione esterno

|Codice numerico|Codice di errore|Soluzione suggerita|
|---|---|---|
|3|UnauthorizedAccess|L'utente che ha tentato di eseguire il cmdlet di PowerShell amministrativo non dispone delle autorizzazioni per eseguire questa operazione o digitato correttamente il nome utente.|
|1000|TenantNotFound|Il nome del tenant che è stato immesso non corrisponde a tutti i tenant esistenti. Esaminare il nome del tenant per errori di digitazione e riprovare.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Quanto tempo contiene oggetti non è possibile eliminare un tenant. Eliminare i pool di host sessione prima di tutto, quindi riprovare.|
|2000|HostPoolNotFound|Il nome del pool di host immesso non corrisponde a tutti i pool di host esistente. Esaminare il nome del pool di host presenti errori di digitazione e riprovare.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|È possibile eliminare un pool di host, purché contenga gli oggetti. Rimuovere prima tutti i gruppi di app nel pool di host.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Rimuovere tutti gli host di sessioni prima di tutto eliminando prima il pool di host sessione.|
|5001|SessionHostNotFound|L'host di sessione che è eseguita una query potrebbe essere offline. Controllare lo stato del pool di host.|
|5008|SessionHostUserSessionsExist |Prima di eseguire l'attività di gestione desiderata, è necessario accedere out tutti gli utenti nell'host della sessione.|
|6000|AppGroupNotFound|Il nome del gruppo di app che è stato immesso non corrisponde a tutti i gruppi di app esistente. Esaminare il nome del gruppo di app per errori di digitazione e riprovare.|
|6022|RemoteAppNotFound|Il nome di RemoteApp che è stato immesso non corrisponde su eventuali macchine virtuali. Esaminare il nome di RemoteApp per errori di digitazione e riprovare.|
|6010|PublishedItemsExist|Il nome della risorsa di che cui vuoi pubblicare è quello utilizzato per una risorsa già esistente. Modificare il nome della risorsa e riprovare.|
|7002|NameNotValidWhiteSpace|Non usare spazi vuoti nel nome.|
|8000|InvalidAuthorizationRoleScope|Il nome del ruolo che è stato immesso non corrisponde a tutti i nomi dei ruoli esistenti. Esaminare il nome del ruolo presenti errori di digitazione e riprovare. |
|8001|UserNotFound |Il nome utente immesso non corrisponde ad alcun nome utente. Esaminare il nome per errori di digitazione e riprovare.|
|8005|UserNotFoundInAAD |Il nome utente immesso non corrisponde ad alcun nome utente. Esaminare il nome per errori di digitazione e riprovare.|
|8008|TenantConsentRequired|Seguire le istruzioni riportate [qui](tenant-setup-azure-active-directory.md#grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service) per fornire il consenso per il tenant.|

### <a name="external-connection-error-codes"></a>Codici di errore di connessione esterna

|Codice numerico|Codice di errore|Soluzione suggerita|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|L'host sessione non sia stato aggiunto ad Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Le connessioni non è riuscita perché l'host sessione non è disponibile. Verificare l'integrità dell'host della sessione.|
|-2146233088|ConnectionFailedClientDisconnect|Se questo errore viene visualizzato spesso, assicurarsi che il computer dell'utente è connesso alla rete.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|La sessione che utente host prova a connettersi non è integro. Eseguire il debug della macchina virtuale.|
|-2146233088|ConnectionFailedUserNotAuthorized|L'utente non è autorizzato ad accedere all'app pubblicata o desktop. Dopo che l'amministratore di rimossa le risorse pubblicate, potrebbe essere visualizzato l'errore. Chiedere all'utente di aggiornare il feed nell'applicazione Desktop remoto.|
|2|FileNotFound|L'applicazione in cui che l'utente ha provato ad accedere in modo non corretto installato o impostato su un percorso non corretto.|
|3|InvalidCredentials|Il nome utente o la password immessi dall'utente non corrisponde a tutti i nomi utente o password esistente. Esaminare le credenziali per errori di digitazione e riprovare.|
|8|ConnectionBroken|La connessione tra Client e Gateway o un Server eliminato. Nessuna azione necessaria a meno che non si verifica in modo imprevisto.|
|14|UnexpectedNetworkDisconnect|Eliminata la connessione alla rete. Chiedere all'utente di connettersi di nuovo.|
|24|ReverseConnectFailed|La macchina virtuale host non dispone di alcuna linea di visuale diretta a Gateway Desktop remoto. Verificare che l'indirizzo IP del Gateway può essere risolto.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui ruoli all'interno di Desktop virtuale Windows, vedere [ambiente di anteprima di Desktop virtuale Windows](environment-setup.md).

Per visualizzare un elenco di cmdlet di PowerShell disponibili per Windows Desktop virtuale, vedere la [riferimento a PowerShell](/powershell/windows-virtual-desktop/overview).
