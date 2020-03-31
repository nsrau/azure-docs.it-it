---
title: Analisi del log di diagnostica di Windows Virtual Desktop - Azure
description: Come utilizzare l'analisi dei log con la funzionalità di diagnostica di Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 355acb081afef8c78cdf971c7a82acdb91ab5593
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127953"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Usare Log Analytics per la funzionalità di diagnosticaUse Log Analytics for the diagnostics feature

Windows Virtual Desktop offre una funzionalità di diagnostica che consente all'amministratore di identificare i problemi tramite un'unica interfaccia. Questa funzionalità registra le informazioni di diagnostica ogni volta che un utente assegnato al ruolo Desktop virtuale di Windows utilizza il servizio. Ogni registro contiene informazioni sul ruolo di Desktop virtuale di Windows coinvolto nell'attività, sugli eventuali messaggi di errore visualizzati durante la sessione, le informazioni sul tenant e le informazioni utente. La funzionalità di diagnostica crea log attività per le azioni utente e amministrative. Ogni registro attività rientra in tre categorie principali: 

- Attività di sottoscrizione del feed: quando un utente tenta di connettersi al feed tramite le applicazioni Desktop remoto Microsoft.Feed subscription activities: when a user tries to connect to their feed through Microsoft Remote Desktop applications.
- Attività di connessione: quando un utente tenta di connettersi a un desktop o remoteApp tramite le applicazioni Microsoft Remote Desktop.
- Attività di gestione: quando un amministratore esegue operazioni di gestione nel sistema, ad esempio la creazione di pool host, l'assegnazione di utenti ai gruppi di app e la creazione di assegnazioni di ruolo.

Le connessioni che non raggiungono Windows Virtual Desktop non verranno visualizzate nei risultati di diagnostica perché il servizio ruolo di diagnostica stesso fa parte di Windows Virtual Desktop. Problemi di connessione desktop virtuale di Windows possono verificarsi quando l'utente si verificano problemi di connettività di rete.

## <a name="why-you-should-use-log-analytics"></a>Perché usare Log AnalyticsWhy you should use Log Analytics

È consigliabile usare Log Analytics per analizzare i dati di diagnostica nel client Azure che va oltre la risoluzione dei problemi con un singolo utente. Come è possibile estrarre in contatori delle prestazioni della macchina virtuale in Log Analytics è disporre di uno strumento per raccogliere informazioni per la distribuzione.

## <a name="before-you-get-started"></a>Prima di iniziare

Prima di poter utilizzare Log Analytics con la funzionalità di diagnostica, è necessario [creare un'area di lavoro.](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace)

Dopo aver creato l'area di lavoro, seguire le istruzioni in [Connettere](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) computer Windows ad Monitoraggio di Azure per ottenere le informazioni seguenti:After you've created your workspace, follow the instructions in Connect Windows computers to Azure Monitor to get the following information: 

- ID dell'area di lavoro
- La chiave primaria dell'area di lavoro

Queste informazioni saranno necessarie più avanti nel processo di installazione.

## <a name="push-diagnostics-data-to-your-workspace"></a>Eseguire il push dei dati di diagnostica nell'area di lavoro 

È possibile eseguire il push dei dati di diagnostica dal tenant di Windows Virtual Desktop a Log Analytics per l'area di lavoro. È possibile configurare questa funzionalità immediatamente quando si crea il tenant per la prima volta collegando l'area di lavoro al tenant oppure è possibile configurarla in un secondo momento con un tenant esistente.

Per collegare il tenant all'area di lavoro di Log Analytics durante la configurazione del nuovo tenant, eseguire il cmdlet seguente per accedere a Desktop virtuale windows con l'account utente TenantCreator: 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

Se si intende collegare un tenant esistente anziché un nuovo tenant, eseguire invece questo cmdlet: 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Sarà necessario eseguire questi cmdlet per ogni tenant che si desidera collegare a Log Analytics. 

>[!NOTE]
>Se non si desidera collegare l'area di lavoro di `New-RdsTenant` Log Analytics quando si crea un tenant, eseguire invece il cmdlet. 

## <a name="cadence-for-sending-diagnostic-events"></a>Cadenza per l'invio di eventi diagnostici

Gli eventi di diagnostica vengono inviati a Log Analytics al termine dell'operazione.  

## <a name="example-queries"></a>Query di esempio

Nelle query di esempio seguenti viene illustrato come la funzionalità di diagnostica genera un report per le attività più frequenti nel sistema:The following example queries show how the diagnostics feature generates a report for the most frequent activities in your system:

Questo primo esempio mostra le attività di connessione avviate dagli utenti con client desktop remoto supportati:

```powershell
WVDActivityV1_CL 

| where Type_s == "Connection" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```

La query di esempio seguente mostra le attività di gestione degli amministratori nei tenant:This next example query shows management activities by admins on tenants:

```powershell
WVDActivityV1_CL 

| where Type_s == "Management" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```
 
## <a name="stop-sending-data-to-log-analytics"></a>Interrompere l'invio di dati a Log Analytics 

Per interrompere l'invio di dati da un tenant esistente a Log Analytics, eseguire il cmdlet seguente e impostare stringhe vuote:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Sarà necessario eseguire questo cmdlet per ogni tenant da cui si desidera interrompere l'invio di dati. 

## <a name="next-steps"></a>Passaggi successivi 

Per esaminare scenari di errore comuni che la funzionalità di diagnostica è in grado di identificare automaticamente, vedere [Identificare e diagnosticare i problemi](diagnostics-role-service.md#common-error-scenarios).
