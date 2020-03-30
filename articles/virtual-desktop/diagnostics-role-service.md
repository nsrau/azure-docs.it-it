---
title: Problemi di diagnosi di Windows Virtual Desktop - Azure
description: Come utilizzare la funzionalità di diagnostica di Windows Virtual Desktop per diagnosticare i problemi.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce85fb70e1480ad285eee78fe20faa8d77b9a147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254261"
---
# <a name="identify-and-diagnose-issues"></a>Identificare e diagnosticare i problemi

Windows Virtual Desktop offre una funzionalità di diagnostica che consente all'amministratore di identificare i problemi tramite un'unica interfaccia. I ruoli di Desktop virtuale di Windows registrano un'attività di diagnostica ogni volta che un utente interagisce con il sistema. Ogni log contiene informazioni rilevanti, ad esempio i ruoli di Desktop virtuale di Windows coinvolti nella transazione, i messaggi di errore, le informazioni sul tenant e le informazioni utente. Le attività diagnostiche vengono create da azioni sia dell'utente finale che di azioni amministrative e possono essere suddivise in tre bucket principali:

* Attività di sottoscrizione del feed: l'utente finale attiva queste attività ogni volta che tenta di connettersi al feed tramite le applicazioni Desktop remoto Microsoft.
* Attività di connessione: l'utente finale attiva queste attività ogni volta che tenta di connettersi a un desktop o a RemoteApp tramite le applicazioni Desktop remoto Microsoft.
* Attività di gestione: l'amministratore attiva queste attività ogni volta che esegue operazioni di gestione nel sistema, ad esempio la creazione di pool host, l'assegnazione di utenti ai gruppi di app e la creazione di assegnazioni di ruolo.
  
Le connessioni che non raggiungono Windows Virtual Desktop non verranno visualizzate nei risultati di diagnostica perché il servizio ruolo di diagnostica stesso fa parte di Windows Virtual Desktop. Problemi di connessione desktop virtuale di Windows possono verificarsi quando l'utente finale si verificano problemi di connettività di rete.

Per iniziare, [scaricare e importare il modulo PowerShell](/powershell/windows-virtual-desktop/overview/) di Windows Virtual Desktop da usare nella sessione di PowerShell, se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Diagnosticare i problemi con PowerShellDiagnose issues with PowerShell

Diagnostica desktop virtuale di Windows utilizza un solo cmdlet di PowerShell, ma contiene molti parametri facoltativi per limitare e isolare i problemi. Nelle sezioni seguenti sono elencati i cmdlet che è possibile eseguire per diagnosticare i problemi. La maggior parte dei filtri può essere applicata insieme. I valori elencati tra `<tenantName>`parentesi quadre, ad esempio , devono essere sostituiti con i valori che si applicano alla situazione.

>[!IMPORTANT]
>La funzionalità di diagnostica è per la risoluzione dei problemi per utente singolo. Tutte le query che utilizzano PowerShell devono includere i parametri *-UserName* o *-ActivityID.* Per le funzionalità di monitoraggio, usare Log Analytics.For monitoring capabilities, use Log Analytics. Per altre informazioni su come inviare dati di diagnostica all'area di lavoro, vedere [Usare Log Analytics.See Use Log Analytics](diagnostics-log-analytics.md) for the diagnostics feature for more information about how to send diagnostics data to your workspace. 

### <a name="filter-diagnostic-activities-by-user"></a>Filtrare le attività di diagnostica per utenteFilter diagnostic activities by user

Il parametro **-UserName** restituisce un elenco di attività di diagnostica avviate dall'utente specificato, come illustrato nel cmdlet di esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Il parametro **-UserName** può anche essere combinato con altri parametri di filtro facoltativi.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrare le attività diagnostiche in base al tempoFilter diagnostic activities by time

È possibile filtrare l'elenco di attività di diagnostica restituito con i parametri **-StartTime** e **-EndTime.** Il parametro **-StartTime** restituirà un elenco di attività di diagnostica a partire da una data specifica, come illustrato nell'esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

Il parametro **-EndTime** può essere aggiunto a un cmdlet con il parametro **-StartTime** per specificare un periodo di tempo specifico per il quale si desidera ricevere i risultati. Il cmdlet di esempio seguente restituirà un elenco di attività diagnostiche tra il 1 agosto e il 10 agosto.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

I parametri **-StartTime** e **-EndTime** possono anche essere combinati con altri parametri di filtro facoltativi.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrare le attività diagnostiche per tipo di attivitàFilter diagnostic activities by activity type

È inoltre possibile filtrare le attività di diagnostica in base al tipo di attività con il parametro **-ActivityType.You** can also filter diagnostic activities by activity type with the -ActivityType parameter. Il cmdlet seguente restituirà un elenco di connessioni degli utenti finali:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

Il cmdlet seguente restituirà un elenco di attività di gestione dell'amministratore:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

Il cmdlet **Get-RdsDiagnosticActivities** non supporta attualmente la specifica di Feed come ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrare le attività diagnostiche in base al risultatoFilter diagnostic activities by outcome

È possibile filtrare l'elenco di attività di diagnostica restituito in base al risultato con il parametro **-Outcome.** Il cmdlet di esempio seguente restituirà un elenco delle attività di diagnostica riuscite.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

Il cmdlet di esempio seguente restituirà un elenco di attività diagnostiche non riuscite.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

Il parametro **-Outcome** può anche essere combinato con altri parametri di filtro facoltativi.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Recuperare un'attività diagnostica specifica in base all'ID attivitàRetrieve a specific diagnostic activity by activity ID

Il parametro **-ActivityId** restituisce un'attività di diagnostica specifica, se esistente, come illustrato nel cmdlet di esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Visualizzare i messaggi di errore per un'attività non riuscita in base all'ID attivitàView error messages for a failed activity by activity ID

Per visualizzare i messaggi di errore per un'attività non riuscita, è necessario eseguire il cmdlet con il parametro **-Detailed.** È possibile visualizzare l'elenco degli errori eseguendo il cmdlet **Select-Object.**

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Recuperare le attività di diagnostica dettagliateRetrieve detailed diagnostic activities

Il parametro **-Detailed** fornisce dettagli aggiuntivi per ogni attività di diagnostica restituita. Il formato di ogni attività varia a seconda del tipo di attività. Il parametro **-Detailed** può essere aggiunto a qualsiasi query **Get-RdsDiagnosticActivities,** come illustrato nell'esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Scenari di errore comuni

Gli scenari di errore sono classificati internamente al servizio ed esterni a Desktop virtuale di Windows.

* Problema interno: specifica scenari che non possono essere attenuati dall'amministratore tenant e devono essere risolti come problemi di supporto. Quando si forniscono commenti e suggerimenti tramite la community tecnica di [Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), includere l'ID attività e l'intervallo di tempo approssimativo in cui si è verificato il problema.
* Problema esterno: si riferiscono a scenari che possono essere attenuati dall'amministratore di sistema. Questi sono esterni a Windows Virtual Desktop.

Nella tabella seguente sono elencati gli errori comuni che gli amministratori potrebbero riscontrare.

>[!NOTE]
>Questo elenco include gli errori più comuni e viene aggiornato a cadenza regolare. Per assicurarti di avere le informazioni più aggiornate, assicurati di controllare questo articolo almeno una volta al mese.

### <a name="external-management-error-codes"></a>Codici di errore di gestione esterna

|Codice numerico|Codice di errore|Soluzione suggerita|
|---|---|---|
|3|Accesso non autorizzato|L'utente che ha tentato di eseguire il cmdlet PowerShell amministrativo non dispone delle autorizzazioni per eseguire questa operazione o ha digitato erroneamente il proprio nome utente.|
|1000|TenantNotAtrovato|Il nome del tenant immesso non corrisponde ad alcun tenant esistente. Esaminare il nome del tenant per gli anni di battitura e riprovare.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Non è possibile eliminare un tenant finché contiene oggetti. Eliminare prima i pool host di sessione, quindi riprovare.|
|2000|HostPoolNotFound|Il nome del pool host immesso non corrisponde ad alcun pool host esistente. Esaminare il nome del pool host per gli anni di battitura e riprovare.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Non è possibile eliminare un pool host purché contenga oggetti. Rimuovere prima tutti i gruppi di app nel pool host.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Rimuovere tutti gli host di sessioni prima di eliminare il pool host della sessione.|
|5001|SessionHostNotFound|L'host della sessione su cui è stata eseguita la query potrebbe essere offline. Controllare lo stato del pool host.|
|5008|SessioneHostUserSessionsExist |È necessario disconnettere tutti gli utenti nell'host della sessione prima di eseguire l'attività di gestione prevista.|
|6000|AppGroupNotFound (AppGroupNotFound)|Il nome del gruppo di app immesso non corrisponde ad alcun gruppo di app esistente. Esaminare il nome del gruppo di app per gli eventi di battitura e riprovare.|
|6022|RemoteAppNotFound (Informazioni in base alle remote|Il nome RemoteApp immesso non corrisponde ad alcuna RemoteApp. Esaminare il nome RemoteApp per gli errore di battitura e riprovare.|
|6010|PublishedItemsExist|Il nome della risorsa che si sta tentando di pubblicare è lo stesso di una risorsa già esistente. Modificare il nome della risorsa e riprovare.|
|7002|NameNotValidWhiteSpace|Non usare spazi vuoti nel nome.|
|8000|Autorizzazione autorizzazioneAuthorizationRoleScope|Il nome del ruolo immesso non corrisponde ad alcun nome di ruolo esistente. Esaminare il nome del ruolo per gli anni di battitura e riprovare. |
|8001|UserNotFound |Il nome utente immesso non corrisponde ad alcun nome utente esistente. Esaminare il nome per gli anni di battitura e riprovare.|
|8005|UserNotFoundInAAD (Utente NotFoundInAAAD) |Il nome utente immesso non corrisponde ad alcun nome utente esistente. Esaminare il nome per gli anni di battitura e riprovare.|
|8008|TenantConsentRequired|Seguire le istruzioni [qui](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) per fornire il consenso per il tenant.|

### <a name="external-connection-error-codes"></a>Codici di errore di connessione esterna

|Codice numerico|Codice di errore|Soluzione suggerita|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|L'host della sessione non viene aggiunto correttamente ad Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Le connessioni non sono riuscite perché l'host della sessione non è disponibile. Controllare l'integrità dell'host della sessione.|
|-2146233088|ConnectionFailedClientDisconnect (Connessione failedClientDisconnect)|Se questo errore viene visualizzato di frequente, assicurarsi che il computer dell'utente sia connesso alla rete.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|La sessione a cui l'utente host ha tentato di connettersi non è integra. Eseguire il debug della macchina virtuale.|
|-2146233088|ConnectionFailedUserNotAuthorized|L'utente non dispone dell'autorizzazione per accedere all'app o al desktop pubblicato. L'errore potrebbe essere visualizzato dopo che l'amministratore ha rimosso le risorse pubblicate. Chiedere all'utente di aggiornare il feed nell'applicazione Desktop remoto.|
|2|FileNotFound|L'applicazione a cui l'utente ha tentato di accedere non è installata correttamente o è impostata su un percorso non corretto.|
|3|InvalidCredentials|Il nome utente o la password immessi dall'utente non corrisponde ad alcun nome utente o password esistente. Esaminare le credenziali per gli errore di battitura e riprovare.|
|8|ConnessioneInterrotta|La connessione tra Client e Gateway o Server è stata interrotta. Nessuna azione necessaria a meno che non accada inaspettatamente.|
|14|UnexpectedNetworkDisconnect (Rete di connessione imprevista)|La connessione alla rete è diminuita. Chiedere all'utente di connettersi di nuovo.|
|24|ReverseConnectFailed|La macchina virtuale host non ha una linea di vista diretta su Gateway Desktop remoto. Verificare che l'indirizzo IP del gateway possa essere risolto.|

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui ruoli all'interno di Windows Virtual Desktop, vedere [Ambiente Windows Virtual Desktop](environment-setup.md).

Per visualizzare un elenco dei cmdlet di PowerShell disponibili per Windows Virtual Desktop, vedere il riferimento di [PowerShell](/powershell/windows-virtual-desktop/overview).
