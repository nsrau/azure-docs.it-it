---
title: Problemi di diagnostica del desktop virtuale Windows-Azure
description: Come utilizzare la funzionalità diagnostica desktop virtuali di Windows per diagnosticare i problemi.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 0e04b075259ed1d003df4a03686d46b1adf694d3
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606855"
---
# <a name="identify-and-diagnose-issues"></a>Identificare e diagnosticare i problemi

Desktop virtuale di Windows offre una funzionalità di diagnostica che consente all'amministratore di identificare i problemi tramite un'unica interfaccia. I ruoli desktop virtuale di Windows registrano un'attività di diagnostica ogni volta che un utente interagisce con il sistema. Ogni log contiene informazioni rilevanti, ad esempio i ruoli desktop virtuale di Windows interessati dalla transazione, i messaggi di errore, le informazioni sul tenant e le informazioni sull'utente. Le attività di diagnostica vengono create sia dall'utente finale che dalle azioni amministrative e possono essere categorizzate in tre bucket principali:

* Attività di sottoscrizione di feed: l'utente finale attiva queste attività ogni volta che tentano di connettersi al feed tramite Desktop remoto Microsoft applicazioni.
* Attività di connessione: l'utente finale attiva queste attività ogni volta che tentano di connettersi a un desktop o a RemoteApp tramite Desktop remoto Microsoft applicazioni.
* Attività di gestione: l'amministratore attiva queste attività ogni volta che eseguono operazioni di gestione nel sistema, ad esempio la creazione di pool host, l'assegnazione di utenti ai gruppi di app e la creazione di assegnazioni di ruolo.
  
Le connessioni che non raggiungono il desktop virtuale di Windows non verranno visualizzate nei risultati della diagnostica perché il servizio ruolo di diagnostica fa parte del desktop virtuale di Windows. Problemi di connessione di desktop virtuali Windows possono verificarsi quando si verificano problemi di connettività di rete da parte dell'utente finale.

Per iniziare, [scaricare e importare il modulo PowerShell per desktop virtuale Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Diagnosticare i problemi con PowerShell

Diagnostica desktop virtuali Windows utilizza un solo cmdlet di PowerShell, ma contiene molti parametri facoltativi che consentono di limitare e isolare i problemi. Le sezioni seguenti elencano i cmdlet che è possibile eseguire per diagnosticare i problemi. La maggior parte dei filtri può essere applicata insieme. I valori elencati tra parentesi quadre, ad esempio `<tenantName>`, devono essere sostituiti con i valori che si applicano alla situazione.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>Recuperare le attività di diagnostica nel tenant

È possibile recuperare le attività diagnostiche immettendo il cmdlet **Get-RdsDiagnosticActivities** . Il cmdlet di esempio seguente restituirà un elenco di attività di diagnostica, ordinate dalla più recente alla meno recente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName>
```

Analogamente ad altri cmdlet di PowerShell per desktop virtuali di Windows, è necessario usare il parametro **-TenantName** per specificare il nome del tenant che si vuole usare per la query. Il nome del tenant è applicabile a quasi tutte le query di attività diagnostiche.

### <a name="retrieve-detailed-diagnostic-activities"></a>Recuperare attività diagnostiche dettagliate

Il parametro **-detailed** fornisce dettagli aggiuntivi per ogni attività diagnostica restituita. Il formato di ogni attività varia a seconda del tipo di attività. Il parametro **-detailed** può essere aggiunto a qualsiasi query **Get-RdsDiagnosticActivities** , come illustrato nell'esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Recuperare un'attività diagnostica specifica in base all'ID attività

Il parametro **-ActivityID** restituisce un'attività di diagnostica specifica, se esistente, come illustrato nel cmdlet di esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Visualizzare i messaggi di errore per un'attività non riuscita per ID attività

Per visualizzare i messaggi di errore per un'attività non riuscita, è necessario eseguire il cmdlet con il parametro **-detailed** . È possibile visualizzare l'elenco di errori eseguendo il cmdlet **Select-Object** .

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="filter-diagnostic-activities-by-user"></a>Filtrare le attività di diagnostica per utente

Il parametro **-username** restituisce un elenco di attività diagnostiche avviate dall'utente specificato, come illustrato nel cmdlet di esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Il parametro **-username** può anche essere combinato con altri parametri di filtro facoltativi.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrare le attività di diagnostica per ora

È possibile filtrare l'elenco di attività diagnostiche restituite con i parametri **-StartTime** e **-EndTime** . Il parametro **-StartTime** restituirà un elenco di attività diagnostiche a partire da una data specifica, come illustrato nell'esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

Il parametro **-EndTime** può essere aggiunto a un cmdlet con il parametro **-StartTime** per specificare un periodo di tempo specifico per cui si desidera ricevere i risultati. Il cmdlet di esempio seguente restituirà un elenco di attività di diagnostica comprese tra il 1 ° agosto e il 10 agosto.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

I parametri **-StartTime** e **-EndTime** possono anche essere combinati con altri parametri di filtro facoltativi.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrare le attività di diagnostica in base al tipo di attività

È anche possibile filtrare le attività di diagnostica in base al tipo di attività con il parametro **-ActivityType** . Il cmdlet seguente restituirà un elenco di connessioni degli utenti finali:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

Il cmdlet seguente restituirà un elenco di attività di gestione amministratore:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

Il cmdlet **Get-RdsDiagnosticActivities** attualmente non supporta la specifica del feed come ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrare le attività di diagnostica per risultato

È possibile filtrare l'elenco di attività diagnostiche restituite in base al risultato con il parametro **-outcome** . Il cmdlet di esempio seguente restituirà un elenco di attività di diagnostica riuscite.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

Il cmdlet di esempio seguente restituirà un elenco di attività di diagnostica non riuscite.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

Il parametro **-outcome** può anche essere combinato con altri parametri di filtro facoltativi.

## <a name="common-error-scenarios"></a>Scenari di errore comuni

Gli scenari di errore sono suddivisi in categorie interne al servizio ed esterni al desktop virtuale di Windows.

* Problema interno: specifica gli scenari che non possono essere mitigati dall'amministratore tenant e che devono essere risolti come un problema di supporto. Quando si forniscono commenti e suggerimenti tramite la [community Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), includere l'ID attività e l'intervallo di tempo approssimativo in cui si è verificato il problema.
* Problema esterno: si riferiscono a scenari che possono essere mitigati dall'amministratore di sistema. Sono esterni al desktop virtuale di Windows.

La tabella seguente elenca gli errori comuni che possono essere eseguiti dagli amministratori.

>[!NOTE]
>Questo elenco include gli errori più comuni e viene aggiornato a cadenza regolare. Per assicurarsi di disporre delle informazioni più aggiornate, assicurarsi di consultare nuovamente questo articolo almeno una volta al mese.

### <a name="external-management-error-codes"></a>Codici di errore di gestione esterna

|Codice numerico|Codice di errore|Soluzione suggerita|
|---|---|---|
|3|UnauthorizedAccess|L'utente che ha tentato di eseguire il cmdlet di PowerShell amministrativo non ha le autorizzazioni per eseguire questa operazione o il nome utente è digitato in modo improprio.|
|1000|TenantNotFound|Il nome del tenant immesso non corrisponde ad alcun tenant esistente. Verificare il nome del tenant per gli errori di ortografia e riprovare.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Non è possibile eliminare un tenant purché contenga oggetti. Eliminare prima i pool host sessione, quindi riprovare.|
|2000|HostPoolNotFound|Il nome del pool host immesso non corrisponde ad alcun pool host esistente. Verificare il nome del pool host per gli errori di digitazione e riprovare.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Non è possibile eliminare un pool host purché contenga oggetti. Rimuovere prima tutti i gruppi di app nel pool host.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Rimuovere tutti gli host delle sessioni prima di eliminare il pool host della sessione.|
|5001|SessionHostNotFound|L'host sessione su cui è stata eseguita la query potrebbe essere offline. Verificare lo stato del pool di host.|
|5008|SessionHostUserSessionsExist |Prima di eseguire l'attività di gestione prevista, è necessario disconnettersi tutti gli utenti nell'host sessione.|
|6000|AppGroupNotFound|Il nome del gruppo di app immesso non corrisponde ad alcun gruppo di app esistente. Verificare il nome del gruppo di app per i digitazioni e riprovare.|
|6022|RemoteAppNotFound|Il nome RemoteApp immesso non corrisponde ad alcun RemoteApp. Verificare il nome RemoteApp per i digitazioni e riprovare.|
|6010|PublishedItemsExist|Il nome della risorsa che si sta tentando di pubblicare è identico a quello di una risorsa già esistente. Modificare il nome della risorsa e riprovare.|
|7002|NameNotValidWhiteSpace|Non usare spazi vuoti nel nome.|
|8000|InvalidAuthorizationRoleScope|Il nome del ruolo immesso non corrisponde ad alcun nome di ruolo esistente. Esaminare il nome del ruolo per i digitazioni e riprovare. |
|8001|UserNotFound |Il nome utente immesso non corrisponde ad alcun nome utente esistente. Verificare il nome per gli errori di digitazione e riprovare.|
|8005|UserNotFoundInAAD |Il nome utente immesso non corrisponde ad alcun nome utente esistente. Verificare il nome per gli errori di digitazione e riprovare.|
|8008|TenantConsentRequired|Seguire le istruzioni riportate [qui](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) per fornire il consenso per il tenant.|

### <a name="external-connection-error-codes"></a>Codici di errore della connessione esterna

|Codice numerico|Codice di errore|Soluzione suggerita|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|L'host sessione non è stato aggiunto correttamente al Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Connessioni non riuscite perché l'host sessione non è disponibile. Controllare l'integrità dell'host sessione.|
|-2146233088|ConnectionFailedClientDisconnect|Se questo errore viene visualizzato di frequente, verificare che il computer dell'utente sia connesso alla rete.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|La sessione a cui l'utente host ha tentato di connettersi non è integro. Eseguire il debug della macchina virtuale.|
|-2146233088|ConnectionFailedUserNotAuthorized|L'utente non è autorizzato ad accedere all'app pubblicata o al desktop. L'errore potrebbe essere visualizzato dopo che l'amministratore ha rimosso le risorse pubblicate. Richiedere all'utente di aggiornare il feed nell'applicazione Desktop remoto.|
|2|FileNotFound|L'applicazione a cui l'utente ha tentato di accedere non è installata correttamente o è impostata su un percorso errato.|
|3|InvalidCredentials|Il nome utente o la password immessi dall'utente non corrisponde ad alcun nome utente o password esistente. Verificare le credenziali per i digitazioni e riprovare.|
|8|ConnectionBroken|La connessione tra il client e il gateway o il server è stata eliminata. Non è necessaria alcuna azione a meno che non venga eseguita in modo imprevisto.|
|14|UnexpectedNetworkDisconnect|Connessione alla rete eliminata. Richiedere all'utente di connettersi di nuovo.|
|24|ReverseConnectFailed|La macchina virtuale host non dispone di una linea di visibilità diretta per Gateway Desktop remoto. Verificare che l'indirizzo IP del gateway possa essere risolto.|

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui ruoli all'interno di desktop virtuale di Windows, vedere [ambiente desktop virtuale di Windows](environment-setup.md).

Per visualizzare un elenco dei cmdlet di PowerShell disponibili per desktop virtuale di Windows, vedere le informazioni di [riferimento su PowerShell](/powershell/windows-virtual-desktop/overview).
