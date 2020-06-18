---
title: Problemi di diagnostica di Desktop virtuale Windows - Azure
description: Come utilizzare la funzionalità di diagnostica di Desktop virtuale Windows per diagnosticare i problemi.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d4fb87ae5edd4919923e66336760aadf23d1888
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657240"
---
# <a name="identify-and-diagnose-issues"></a>Identificare e diagnosticare i problemi

>[!IMPORTANT]
>Questo contenuto si applica alla versione Autunno 2019 che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si sta tentando di gestire gli oggetti Azure Resource Manager di Desktop virtuale Windows introdotti nell'aggiornamento di Primavera 2020, vedere [questo articolo](../diagnostics-role-service.md).

Desktop virtuale Windows offre una funzionalità di diagnostica che consente all'amministratore di identificare i problemi usando un'unica interfaccia. I ruoli di Desktop virtuale Windows registrano un'attività di diagnostica ogni volta che un utente interagisce con il sistema. Ogni log contiene informazioni rilevanti, ad esempio i ruoli di Desktop virtuale Windows coinvolti nella transazione, i messaggi di errore, le informazioni sul tenant e le informazioni sull'utente. Le attività di diagnostica vengono create sia da azioni dell'utente finale sia da azioni amministrative e possono essere raggruppate in tre categorie principali:

* Attività di sottoscrizione a feed: l'utente finale attiva queste attività ogni volta che prova a connettersi al proprio feed tramite applicazioni Desktop remoto Microsoft.
* Attività di connessione: l'utente finale attiva queste attività ogni volta che prova a connettersi a un desktop o a RemoteApp tramite applicazioni Desktop remoto Microsoft.
* Attività di gestione: l'amministratore attiva queste attività ogni volta che esegue operazioni di gestione nel sistema, ad esempio la creazione di pool di host, l'assegnazione di utenti ai gruppi di app e la creazione di assegnazioni di ruolo.
  
Le connessioni che non raggiungono Desktop virtuale Windows non vengono visualizzate nei risultati della diagnostica perché il servizio dei ruoli di diagnostica fa parte del servizio Desktop virtuale Windows. Quando l'utente finale riscontra problemi di connettività di rete, possono verificarsi problemi di connessione di Desktop virtuale Windows.

Per iniziare, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](/powershell/windows-virtual-desktop/overview/) da usare nella sessione di PowerShell, se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Diagnosticare i problemi con PowerShell

La funzionalità di diagnostica di Desktop virtuale Windows usa un solo cmdlet di PowerShell, ma contiene molti parametri facoltativi che consentono di limitare e isolare i problemi. Le sezioni seguenti elencano i cmdlet che è possibile eseguire per diagnosticare i problemi. Quasi tutti i filtri possono essere applicati contemporaneamente. I valori riportati tra parentesi, ad esempio `<tenantName>`, devono essere sostituiti con i valori applicabili alla situazione specifica.

>[!IMPORTANT]
>La funzionalità di diagnostica consente la risoluzione dei problemi per singolo utente. Tutte le query che usano PowerShell devono includere i parametri *-UserName* o *-ActivityID*. Per le funzionalità di monitoraggio, usare Log Analytics. Per altre informazioni su come inviare i dati di diagnostica all'area di lavoro, vedere [Usare Log Analytics per la funzionalità di diagnostica](diagnostics-log-analytics-2019.md). 

### <a name="filter-diagnostic-activities-by-user"></a>Filtrare le attività di diagnostica in base all'utente

Il parametro **-UserName** restituisce un elenco di attività di diagnostica avviate dall'utente specificato, come illustrato nel cmdlet di esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Il parametro **-UserName** può anche essere combinato con altri parametri di filtro facoltativi.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrare le attività di diagnostica in base al tempo

È possibile filtrare l'elenco di attività di diagnostica restituito con i parametri **-StartTime** ed **-EndTime**. Il parametro **-StartTime** restituisce un elenco di attività di diagnostica a partire da una data specifica, come illustrato nell'esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

Il parametro **-EndTime** può essere aggiunto a un cmdlet con il parametro **-StartTime** per specificare un periodo di tempo per cui ricevere i risultati. Il cmdlet di esempio seguente restituirà un elenco di attività di diagnostica comprese tra il 1° agosto e il 10 agosto.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

I parametri **-StartTime** ed **-EndTime** possono anche essere combinati con altri parametri di filtro facoltativi.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrare le attività di diagnostica in base al tipo di attività

È anche possibile filtrare le attività di diagnostica in base al tipo di attività usando il parametro **-ActivityType**. Il cmdlet seguente restituirà un elenco di connessioni dell'utente finale:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

Il cmdlet seguente restituirà un elenco di attività di gestione dell'amministratore:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

Il cmdlet **Get-RdsDiagnosticActivities** non supporta attualmente la specifica di Feed come ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrare le attività di diagnostica in base al risultato

È possibile filtrare l'elenco di attività di diagnostica restituito in base al risultato usando il parametro **-Outcome**. Il cmdlet di esempio seguente restituirà un elenco di attività di diagnostica riuscite.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

Il cmdlet di esempio seguente restituirà un elenco di attività di diagnostica non riuscite.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

Il parametro **-Outcome** può anche essere combinato con altri parametri di filtro facoltativi.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Recuperare una specifica attività diagnostica in base all'ID attività

Il parametro **-ActivityId** restituisce una specifica attività di diagnostica, se esistente, come illustrato nel cmdlet di esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Visualizzare i messaggi di errore per un'attività non riuscita in base all'ID attività

Per visualizzare i messaggi di errore per un'attività non riuscita, è necessario eseguire il cmdlet con il parametro **-Detailed**. È possibile visualizzare l'elenco di errori eseguendo il cmdlet **Select-Object**.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Recuperare attività di diagnostica dettagliate

Il parametro **-Detailed** consente di ottenere dettagli aggiuntivi per ogni attività di diagnostica restituita. Il formato di ogni attività varia a seconda del tipo di attività. Il parametro **-Detailed** può essere aggiunto a qualsiasi query **Get-RdsDiagnosticActivities**, come illustrato nell'esempio seguente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Scenari di errore comuni

Gli scenari di errore sono suddivisi in problemi interni al servizio e problemi esterni a Desktop virtuale Windows.

* Problema interno: si riferisce agli scenari che non possono essere mitigati dall'amministratore tenant e che devono essere risolti come problema di supporto. Quando si invia feedback tramite la [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), includere l'ID attività e il periodo di tempo approssimativo in cui si è verificato il problema.
* Problema esterno: si riferisce agli scenari che possono essere mitigati dall'amministratore di sistema e che sono esterni a Desktop virtuale Windows.

La tabella seguente elenca gli errori comuni che possono essere riscontrati dagli amministratori.

>[!NOTE]
>L'elenco include gli errori più comuni e viene aggiornato con frequenza regolare. Per assicurarsi di avere le informazioni più aggiornate, consultare questo articolo almeno una volta al mese.

### <a name="external-management-error-codes"></a>Codici di errore relativi alla gestione esterna

|Codice numerico|Codice di errore|Soluzione suggerita|
|---|---|---|
|1322|ConnectionFailedNoMappingOfSIDinAD|L'utente non è membro di Azure Active Directory. Per aggiungerlo, seguire le istruzioni riportate in [Centro di amministrazione di Active Directory](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center).|
|3|UnauthorizedAccess|L'utente che ha tentato di eseguire il cmdlet di PowerShell amministrativo non dispone delle autorizzazioni per eseguire questa operazione oppure il nome utente non è stato digitato correttamente.|
|1000|TenantNotFound|Il nome del tenant immesso non corrisponde ad alcun tenant esistente. Verificare che il nome del tenant sia stato digitato correttamente e riprovare.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Non è possibile eliminare un tenant se contiene oggetti. Eliminare prima i pool di host di sessione e quindi riprovare.|
|2000|HostPoolNotFound|Il nome del pool di host immesso non corrisponde ad alcun pool di host esistente. Verificare che il nome del pool di host sia stato digitato correttamente e riprovare.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Non è possibile eliminare un pool di host se contiene oggetti. Rimuovere prima tutti i gruppi di app nel pool di host.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Rimuovere tutti gli host di sessione prima di eliminare il pool di host.|
|5001|SessionHostNotFound|L'host di sessione su cui è stata eseguita la query potrebbe essere offline. Verificare lo stato del pool di host.|
|5008|SessionHostUserSessionsExist |Prima di eseguire l'attività di gestione prevista, è necessario disconnettere tutti gli utenti sull'host di sessione.|
|6000|AppGroupNotFound|Il nome del gruppo di app immesso non corrisponde ad alcun gruppo di app esistente. Verificare che il nome del gruppo di app sia stato digitato correttamente e riprovare.|
|6022|RemoteAppNotFound|Il nome del servizio RemoteApp immesso non corrisponde ad alcun servizio RemoteApp. Verificare che il nome del servizio RemoteApp sia stato digitato correttamente e riprovare.|
|6010|PublishedItemsExist|Il nome della risorsa che si sta tentando di pubblicare è identico a quello di una risorsa già esistente. Modificare il nome della risorsa e riprovare.|
|7002|NameNotValidWhiteSpace|Il nome non può contenere spazi.|
|8000|InvalidAuthorizationRoleScope|Il nome del ruolo immesso non corrisponde ad alcun nome di ruolo esistente. Verificare che il nome del ruolo sia stato digitato correttamente e riprovare. |
|8001|UserNotFound |Il nome utente immesso non corrisponde ad alcun nome utente esistente. Verificare che il nome sia stato digitato correttamente e riprovare.|
|8005|UserNotFoundInAAD |Il nome utente immesso non corrisponde ad alcun nome utente esistente. Verificare che il nome sia stato digitato correttamente e riprovare.|
|8008|TenantConsentRequired|Seguire le istruzioni riportate [qui](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) per fornire il consenso per il tenant.|

### <a name="external-connection-error-codes"></a>Codici di errore relativi alla connessione esterna

|Codice numerico|Codice di errore|Soluzione suggerita|
|---|---|---|
|-2147467259|ConnectionFailedAdErrorNoSuchMember|L'utente non è membro di Azure Active Directory. Per aggiungerlo, seguire le istruzioni riportate in [Centro di amministrazione di Active Directory](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center).|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|L'host di sessione non è stato aggiunto correttamente ad Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Le connessioni non sono riuscite perché l'host di sessione non è disponibile. Controllare l'integrità dell'host di sessione.|
|-2146233088|ConnectionFailedClientDisconnect|Se questo errore viene visualizzato di frequente, verificare che il computer dell'utente sia connesso alla rete.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|La sessione a cui l'utente dell'host ha tentato di connettersi non è integra. Eseguire il debug della macchina virtuale.|
|-2146233088|ConnectionFailedUserNotAuthorized|L'utente non è autorizzato ad accedere all'app pubblicata o al desktop. L'errore potrebbe essere visualizzato dopo che l'amministratore ha rimosso le risorse pubblicate. Chiedere all'utente di aggiornare il feed nell'applicazione Desktop remoto.|
|2|FileNotFound|L'applicazione a cui l'utente ha tentato di accedere non è installata correttamente o è impostata su un percorso errato.|
|3|InvalidCredentials|Il nome utente o la password che l'utente ha immesso non corrisponde ad alcun nome utente o password esistente. Verificare che le credenziali siano state digitate correttamente e riprovare.|
|8|ConnectionBroken|La connessione tra il client e il gateway o il server si è interrotta. Non è necessaria alcuna azione a meno che l'interruzione sia avvenuta in modo imprevisto.|
|14|UnexpectedNetworkDisconnect|La connessione alla rete si è interrotta. Chiedere all'utente di connettersi di nuovo.|
|24|ReverseConnectFailed|La macchina virtuale host non comunica direttamente con Gateway Desktop remoto. Verificare che l'indirizzo IP del gateway possa essere risolto.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui ruoli all'interno di Desktop virtuale Windows, vedere [Ambiente di Desktop virtuale Windows](environment-setup-2019.md).

Per visualizzare l'elenco dei cmdlet di PowerShell disponibili per Desktop virtuale Windows, vedere le [informazioni di riferimento su PowerShell](/powershell/windows-virtual-desktop/overview).
