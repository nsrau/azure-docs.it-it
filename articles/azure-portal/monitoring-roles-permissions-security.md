---
title: Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure | Microsoft Docs
description: Informazioni su come usare le autorizzazioni e i ruoli predefiniti di monitoraggio di Azure per limitare l'accesso alle risorse di monitoraggio.
author: johnkemnetz
manager: rboucher
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: johnkem

---
# Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure
Molti team hanno bisogno di regolare rigorosamente l'accesso ai dati e alle impostazioni di monitoraggio. Ad esempio, se si dispone di membri del team che lavorano esclusivamente sul monitoraggio (tecnici del supporto, tecnici DevOps) o si usa un provider di servizi gestiti, si consiglia di concedere loro l'accesso ai dati di monitoraggio solo limitandone la possibilità di creare, modificare o eliminare le risorse. In questo articolo viene illustrato come applicare rapidamente un ruolo di monitoraggio predefinito nel Controllo degli accessi in base al ruolo a un utente in Azure o creare il proprio ruolo personalizzato per un utente che ha bisogno di autorizzazioni di monitoraggio limitate. Vengono poi esposte alcune considerazioni sulla sicurezza per le risorse legate al monitoraggio di Azure e viene illustrato come è possibile limitare l'accesso ai dati che contengono.

## Ruoli di monitoraggio predefiniti
I ruoli predefiniti del monitoraggio di Azure consentono di limitare l'accesso alle risorse in una sottoscrizione e allo stesso tempo consentire ai responsabili del monitoraggio dell'infrastruttura di ottenere e configurare i dati necessari. Il monitoraggio di Azure fornisce due ruoli predefiniti: un lettore di monitoraggio e un collaboratore al monitoraggio.

### Lettore di monitoraggio
Le persone a cui è assegnato il ruolo di lettore di monitoraggio possono visualizzare tutti i dati di monitoraggio in una sottoscrizione ma non possono modificare alcuna risorsa o impostazione relativa alle risorse di monitoraggio. Questo ruolo è appropriato per gli utenti di un'organizzazione, ad esempio tecnici del supporto o delle operazioni, che devono essere in grado di:

* Visualizzare i dashboard di monitoraggio nel portale e creare dashboard di monitoraggio privati.
* Eseguire query per le metriche usando l'[API REST di monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931930.aspx), i [cmdlet di PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md) o l'[interfaccia della riga di comando multipiattaforma](../monitoring-and-diagnostics/insights-cli-samples.md).
* Eseguire query per il registro attività usando il portale, l'API REST di monitoraggio di Azure, i cmdlet di PowerShell o l'interfaccia della riga di comando multipiattaforma.
* Visualizzare le [impostazioni di diagnostica](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) per una risorsa.
* Visualizzare il [profilo di registro](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) per una sottoscrizione.
* Visualizzare le impostazioni di scalabilità automatica.
* Visualizzare impostazioni e attività di avviso.
* Accedere ai dati di Application Insights e visualizzarli in AI Analytics.
* Cercare i dati dell'area di lavoro Log Analytics (OMS), inclusi i dati sull'uso dell'area di lavoro.
* Visualizzare i gruppi di gestione di Log Analytics (OMS).
* Recuperare lo schema di ricerca di Log Analytics (OMS).
* Elencare gli Intelligence Pack di Log Analytics (OMS).
* Recuperare ed eseguire le ricerche salvate di Log Analytics (OMS).
* Recuperare la configurazione di archiviazione di Log Analytics (OMS).

> [!NOTE]
> Questo ruolo non concede l'accesso in lettura ai dati del registro che sono stati trasmessi a un hub eventi o archiviati in un account di archiviazione. Per informazioni sulla configurazione dell'accesso a queste risorse, [vedere di seguito](#security-considerations-for-monitoring-data).
> 
> 

### Collaboratore al monitoraggio
Le persone a cui è assegnato il ruolo di collaboratore al monitoraggio possono visualizzare tutti i dati di monitoraggio in una sottoscrizione e creare o modificare le impostazioni, ma non possono modificare altre risorse. Questo ruolo è un soprainsieme del ruolo di lettore di monitoraggio ed è adatto ai membri del team di monitoraggio di un'organizzazione o ai fornitori di servizi gestiti che, oltre alle autorizzazioni di cui sopra, devono essere in grado di:

* Pubblicare dashboard di monitoraggio come dashboard condivisi.
* Configurare le [impostazioni di diagnostica](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) per una risorsa.*
* Impostare il [profilo di registro](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) per una sottoscrizione.*
* Configurare impostazioni e attività di avviso.
* Creare componenti e test Web di Application Insights.
* Elencare le chiavi condivise dell'area di lavoro di Log Analytics (OMS).
* Abilitare o disabilitare gli Intelligence Pack di Log Analytics (OMS).
* Creare ed eliminare poi eseguire le ricerche salvate di Log Analytics (OMS).
* Creare ed eliminare la configurazione di archiviazione di Log Analytics (OMS).

*per configurare un profilo di registro o un'impostazione di diagnostica, è necessario che all'utente sia concessa separatamente anche l'autorizzazione ListKeys nella risorsa di destinazione (account di archiviazione o spazio dei nomi dell'hub eventi).

> [!NOTE]
> Questo ruolo non concede l'accesso in lettura ai dati del registro che sono stati trasmessi a un hub eventi o archiviati in un account di archiviazione. Per informazioni sulla configurazione dell'accesso a queste risorse, [vedere di seguito](#security-considerations-for-monitoring-data).
> 
> 

## Autorizzazioni per il monitoraggio e ruoli personalizzati nel Controllo degli accessi in base al ruolo
Se i precedenti ruoli predefiniti non soddisfano le esigenze esatte del team, è possibile [creare un ruolo personalizzato nel Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-custom-roles.md) con autorizzazioni più granulari. Di seguito sono riportate le più comuni operazioni nel Controllo degli accessi in base al ruolo di monitoraggio di Azure con le relative descrizioni.

| Operazione | Descrizione |
| --- | --- |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Regole di avviso di lettura, scrittura ed eliminazione. |
| Microsoft.Insights/AlertRules/Incidents/Read |Elenco degli eventi imprevisti (cronologia della regola di avviso attivata) per le regole di avviso. Si applica solo al portale. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Impostazioni di scalabilità automatica di lettura, scrittura ed eliminazione. |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |Impostazioni di diagnostica di lettura, scrittura ed eliminazione. |
| Microsoft.Insights/eventtypes/digestevents/Read |Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai registri attività tramite il portale. |
| Microsoft.Insights/eventtypes/values/Read |Elenco degli eventi dei registri attività (eventi di gestione) in una sottoscrizione. Questa autorizzazione è applicabile sia all'accesso programmatico che all'accesso al portale per il registro attività. |
| Microsoft.Insights/LogDefinitions/Read |Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai registri attività tramite il portale. |
| Microsoft.Insights/MetricDefinitions/Read |Definizioni delle metriche (elenco dei tipi di metriche disponibili per una risorsa). |
| Microsoft.Insights/Metrics/Read |Metriche per una risorsa. |

> [!NOTE]
> L'accesso ad avvisi, impostazioni di diagnostica e metriche per una risorsa richiede che l'utente disponga dell'accesso in lettura per il tipo di risorsa e l'ambito di tale risorsa. La creazione ("scrittura") di un'impostazione di diagnostica o di un profilo di registro che archivia in un account di archiviazione o trasmette a un hub eventi richiede che l'utente disponga anche dell'autorizzazione ListKeys nella risorsa di destinazione.
> 
> 

Ad esempio, usando la tabella sopra è possibile creare un ruolo personalizzato nel Controllo degli accessi in base al ruolo per un "lettore di registro attività" come il seguente:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## Considerazioni sulla sicurezza per i dati sul monitoraggio
I dati sul monitoraggio dei dati, in particolare i file di registro, possono contenere informazioni sensibili, come indirizzi IP o nomi utente. I dati sul monitoraggio di Azure sono forniti in tre forme base:

1. Registro attività, che descrive tutte le azioni del piano di controllo nella sottoscrizione di Azure.
2. Registri di diagnostica, cioè registri generati da una risorsa.
3. Metriche, generate dalle risorse.

Tutti e tre questi tipi di dati possono essere archiviati in un account di archiviazione o trasmessi a un hub eventi, che sono entrambi risorse di Azure di scopo generico. Poiché si tratta di risorse di scopo generico, la creazione, l'eliminazione e l'accesso sono operazioni privilegiate e generalmente riservate agli amministratori. Si consiglia di usare le procedure seguenti con le risorse relative al monitoraggio per evitare un uso improprio:

* Usare un account di archiviazione singolo e dedicato per il monitoraggio dei dati. Se è necessario separare i dati di monitoraggio in più account di archiviazione, non condividere mai l'uso di un account di archiviazione tra dati relativi al monitoraggio e non relativi al monitoraggio, perché in questo modo si potrebbe inavvertitamente fornire l'accesso ai dati non relativi al monitoraggio a chi ha bisogno solo dei dati di monitoraggio (es. SIEM di terze parti).
* Usare un singolo bus di servizio o spazio dei nomi dell'hub eventi dedicato in tutte le impostazioni di diagnostica per lo stesso motivo specificato sopra.
* Limitare l'accesso agli hub eventi o agli account di archiviazione relativi al monitoraggio tenendoli in un gruppo di risorse separato e [usare l'ambito](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) nei ruoli di monitoraggio per limitare l'accesso solo a tale gruppo di risorse.
* Non concedere mai l'autorizzazione ListKeys ad account di archiviazione o hub eventi nell'ambito della sottoscrizione quando un utente ha bisogno solo dell'accesso ai dati di monitoraggio. Piuttosto, assegnare queste autorizzazioni all'utente nell'ambito di una risorsa o di un gruppo di risorse (se si dispone di un gruppo di risorse di monitoraggio dedicato).

### Limitare l'accesso agli account di archiviazione relativi al monitoraggio
Quando un utente o un'applicazione richiede l'accesso ai dati di monitoraggio in un account di archiviazione, è necessario [generare una firma di accesso condiviso per l'account](https://msdn.microsoft.com/library/azure/mt584140.aspx) nell'account di archiviazione che contiene i dati di monitoraggio con accesso in sola lettura a livello di servizio all'archivio BLOB. In PowerShell, potrebbe avere un aspetto simile al seguente:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

A questo punto è possibile fornire il token all'entità che ha bisogno di leggere da quell'account di archiviazione; tale token può elencare e leggere da tutti i BLOB nell'account di archiviazione.

In alternativa, se è necessario verificare l'autorizzazione con il Controllo degli accessi in base al ruolo, è possibile concedere a tale entità l'autorizzazione Microsoft.Storage/storageAccounts/listkeys/action su quel particolare account di archiviazione. Questo è necessario per gli utenti che devono essere in grado di configurare un'impostazione di diagnostica o un profilo di registro per l'archiviazione in un account di archiviazione. Ad esempio, è possibile creare il seguente ruolo personalizzato nel Controllo degli accessi in base al ruolo per un utente o un'applicazione che deve solo leggere da un account di archiviazione:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [!WARNING]
> L'autorizzazione ListKeys consente all'utente di elencare le chiavi dell'account di archiviazione primario e secondario. Queste chiavi concedono all'utente tutte le autorizzazioni accettate (lettura, scrittura, creazione di BLOB, eliminazione di BLOB e così via) in tutti i servizi accettati (BLOB, coda, tabella, file) di tale account di archiviazione. Se possibile, si consiglia di usare una firma di accesso condiviso dell'account come descritto sopra.
> 
> 

### Limitare l'accesso agli hub eventi relativi al monitoraggio
Un modello simile può essere seguito con gli hub eventi, tuttavia è innanzitutto necessario creare una regola di autorizzazione di ascolto dedicata. Se si desidera concedere l'accesso a un'applicazione che ha bisogno solo di ascoltare gli hub eventi relativi al monitoraggio, procedere come segue:

1. Creare un criterio di accesso condiviso negli hub eventi creati per trasmettere i dati di monitoraggio solo con attestazioni di ascolto. Questa operazione può essere eseguita nel portale. Ad esempio, è possibile chiamarlo "monitoringReadOnly". Se possibile, si dovrà fornire la chiave direttamente al consumatore e ignorare il passaggio successivo.
2. Se il consumatore deve essere in grado di ottenere la chiave ad hoc, concedere all'utente l'azione ListKeys per l'hub eventi. Questo è necessario anche per gli utenti che devono essere in grado di configurare un'impostazione di diagnostica o un profilo di registro per trasmettere agli hub eventi. Ad esempio, è possibile creare una regola nel Controllo degli accessi in base al ruolo:
   
   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```

## Passaggi successivi
* [Controllo degli accessi in base al ruolo e autorizzazioni in Resource Manager](../active-directory/role-based-access-control-what-is.md)
* [Panoramica sul monitoraggio in Azure](../monitoring-and-diagnostics/monitoring-overview.md)

<!---HONumber=AcomDC_0928_2016-->