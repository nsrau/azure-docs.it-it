---
title: Operazioni di provider con Azure Resource Manager | Documentazione Microsoft
description: Fornisce informazioni dettagliate sulle operazioni disponibili nei provider di risorse di Microsoft Azure Resource Manager
services: active-directory
documentationcenter: 
author: jboeshart
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: jaboes
ms.openlocfilehash: 27880402d377701448d095a1295ece875729cd67
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operazioni di provider di risorse con Azure Resource Manager

Questo documento elenca le operazioni disponibili per ciascun provider di risorse Microsoft Azure Resource Manager. Tali operazioni possono essere utilizzate nei ruoli personalizzati per fornire autorizzazioni RBAC (controllo degli accessi in base al ruolo) granulari alle risorse di Azure. L’elenco qui fornito non è da ritenersi completo e le operazioni potrebbero essere aggiunte o rimosse man mano che il provider viene aggiornato. Le stringhe di operazione seguono il formato di `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Per un elenco completo e aggiornato delle operazioni dei provider di risorse di Azure usare `Get-AzureRmProviderOperation` (in PowerShell) o `az provider operation list` (nell'interfaccia della riga di comando di Azure versione 2).

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Operazione | DESCRIZIONE |
|---|---|
|/configuration/action|Aggiorna la configurazione tenant.|
|/services/action|Aggiorna un'istanza del servizio nel tenant.|
|/configuration/write|Crea una configurazione tenant.|
|/configuration/read|Legge la configurazione tenant.|
|/services/write|Aggiorna un'istanza del servizio nel tenant.|
|/services/read|Legge le istanze del servizio nel tenant.|
|/services/delete|Elimina un'istanza del servizio nel tenant.|
|/services/servicemembers/action|Crea un'istanza del membro del servizio nel servizio.|
|/services/servicemembers/read|Legge l'istanza del membro del servizio nel servizio.|
|/services/servicemembers/delete|Elimina un'istanza del membro del servizio nel servizio.|
|/services/servicemembers/alerts/read|Legge gli avvisi per un membro del servizio.|
|/services/alerts/read|Legge gli avvisi per un servizio.|
|/services/alerts/read|Legge gli avvisi per un servizio.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Operazione | DESCRIZIONE |
|---|---|
|/generateRecommendations/action|Genera suggerimenti.|
|/suppressions/action|Crea/aggiorna eliminazioni.|
|/register/action|Registra la sottoscrizione per Microsoft Advisor.|
|/generateRecommendations/read|Ottiene lo stato dei suggerimenti generati.|
|/recommendations/read|Legge i suggerimenti.|
|/suppressions/read|Ottiene le eliminazioni.|
|/suppressions/delete|Cancella le eliminazioni.|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Operazione | DESCRIZIONE |
|---|---|
|/servers/read|Recupera le informazioni dell’Analysis Server specificato.|
|/servers/write|Crea o aggiorna l’Analysis Server specificato.|
|/servers/delete|Elimina l’Analysis Server.|
|/servers/suspend/action|Sospende l’Analysis Server.|
|/servers/resume/action|Riprende l’Analysis Server.|
|/servers/checkNameAvailability<br>/action|Verifica che il nome dell’Analysis Server specificato sia valido e non in uso.|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Operazione | DESCRIZIONE |
|---|---|
|/checkNameAvailability/action|Verifica che il nome del servizio sia disponibile.|
|/register/action|Registra la sottoscrizione per il provider di risorse Microsoft.ApiManagement.|
|/unregister/action|Annulla la registrazione della sottoscrizione per il provider di risorse Microsoft.ApiManagement.|
|/service/write|Creare una nuova istanza del servizio Gestione API|
|/service/read|Leggere i metadati per un'istanza del servizio Gestione API|
|/service/delete|Elimina l’istanza del servizio Gestione API.|
|/service/updatehostname/action|Configura, aggiorna o rimuove i nomi di dominio personalizzati per un servizio Gestione API.|
|/service/uploadcertificate/action|Carica il certificato SSL per un servizio Gestione API.|
|/service/backup/action|Esegue il backup del servizio Gestione API nel contenitore specificato in un account di archiviazione fornito dall’utente.|
|/service/restore/action|Ripristinare il servizio Gestione API dal contenitore specificato in un account di archiviazione fornito dall'utente|
|/service/managedeployments/action|Modifica SKU/unità, aggiunge/rimuove distribuzioni regionali del servizio Gestione API.|
|/service/getssotoken/action|Ottiene il token SSO che può essere utilizzato per l'accesso al portale legacy del servizio Gestione API come amministratore.|
|/service/applynetworkconfigurationupdates/action|Aggiorna le risorse Microsoft.ApiManagement in esecuzione nella rete virtuale per selezionare le impostazioni di rete aggiornate.|
|/service/operationresults/read|Ottiene lo stato corrente dell'operazione in esecuzione prolungata|
|/service/networkStatus/read|Ottiene lo stato di accesso di rete delle risorse.|
|/service/loggers/read|Ottiene l'elenco di logger o i dettagli del logger|
|/service/loggers/write|Aggiunge un nuovo logger o aggiorna i dettagli di un logger esistente|
|/service/loggers/delete|Rimuove un logger esistente|
|/service/users/read|Ottiene un elenco di utenti registrati o i dettagli dell’account di un utente|
|/service/users/write|Registra un nuovo utente o aggiorna i dettagli dell’account di un utente esistente|
|/service/users/delete|Rimuove l’account utente|
|/service/users/generateSsoUrl/action|Genera un URL SSO. L'URL può essere utilizzato per accedere al portale di amministrazione|
|/service/users/subscriptions/read|Ottiene l'elenco di sottoscrizioni utente|
|/service/users/keys/read|Ottiene l'elenco di chiavi utente|
|/service/users/groups/read|Ottiene l'elenco di gruppi utente|
|/service/tenant/operationResults/read|Ottiene l'elenco dei risultati dell'operazione o il risultato di un'operazione specifica|
|/service/tenant/policy/read|Ottiene la configurazione dei criteri per il tenant|
|/service/tenant/policy/write|Imposta la configurazione dei criteri per il tenant|
|/service/tenant/policy/delete|Rimuove la configurazione dei criteri per il tenant|
|/service/tenant/configuration/save/action|Crea commit con la snapshot di configurazione sul ramo specificato in archivio|
|/service/tenant/configuration/deploy/action|Esegue un'attività di distribuzione per applicare le modifiche dal ramo git specificato alla configurazione sul database.|
|/service/tenant/configuration/validate/action|Convalida le modifiche dal ramo git specificato|
|/service/tenant/configuration/operationResults/read|Ottiene l'elenco dei risultati dell'operazione o il risultato di un'operazione specifica|
|/service/tenant/configuration/syncState/read|Ottiene lo stato dell’ultima sincronizzazione git|
|/service/tenant/access/read|Ottiene i dettagli sulle informazioni di accesso del tenant|
|/service/tenant/access/write|Aggiorna i dettagli sulle informazioni di accesso del tenant|
|/service/tenant/access/regeneratePrimaryKey/action|Rigenera la chiave di accesso primaria|
|/service/tenant/access/regenerateSecondaryKey/action|Rigenera la chiave di accesso secondaria|
|/service/identityProviders/read|Ottiene l’elenco di provider di identità o i dettagli del provider di identità|
|/service/identityProviders/write|Crea un nuovo provider di identità o aggiorna i dettagli di un provider di identità esistente|
|/service/identityProviders/delete|Rimuove un provider di identità esistente|
|/service/subscriptions/read|Ottiene un elenco di sottoscrizioni al prodotto o i dettagli di sottoscrizione a un prodotto|
|/service/subscriptions/write|Effettua la sottoscrizione di un utente esistente a un prodotto esistente oppure aggiorna i dettagli di sottoscrizione esistenti. Questa operazione può essere utilizzata per rinnovare la sottoscrizione|
|/service/subscriptions/delete|Elimina una sottoscrizione. Questa operazione può essere utilizzata per eliminare la sottoscrizione|
|/service/subscriptions/regeneratePrimaryKey/action|Rigenera la chiave primaria di sottoscrizione|
|/service/subscriptions/regenerateSecondaryKey/action|Rigenera la chiave secondaria di sottoscrizione|
|/service/backends/read|Ottiene l’elenco di back-end o i dettagli del back-end|
|/service/backends/write|Aggiunge un nuovo back-end o aggiorna i dettagli di un back-end esistente|
|/service/backends/delete|Rimuove il back-end esistente|
|/service/apis/read|Ottiene l’elenco di tutte le API registrate o i dettagli delle API|
|/service/apis/write|Crea una nuova API o aggiorna i dettagli di una API esistente|
|/service/apis/delete|Rimuove una API esistente|
|/service/apis/policy/read|Ottiene i dettagli di configurazione dei criteri per l’API|
|/service/apis/policy/write|Imposta i dettagli di configurazione dei criteri per l’API|
|/service/apis/policy/delete|Rimuove la configurazione dei criteri dalla API|
|/service/apis/operations/read|Ottiene l’elenco di operazioni API esistenti o i dettagli dell’operazione API|
|/service/apis/operations/write|Crea una nuova operazione API o ne aggiorna una esistente|
|/service/apis/operations/delete|Rimuove un’operazione API esistente|
|/service/apis/operations/policy/read|Ottiene i dettagli di configurazione dei criteri per l’operazione|
|/service/apis/operations/policy/write|Imposta i dettagli di configurazione dei criteri per l’operazione|
|/service/apis/operations/policy/delete|Rimuove la configurazione dei criteri dall’operazione|
|/service/products/read|Ottiene l’elenco dei prodotti o i dettagli di un prodotto|
|/service/products/write|Crea nuovo prodotto o aggiorna i dettagli del prodotto esistente.|
|/service/products/delete|Rimuove il prodotto esistente.|
|/service/products/subscriptions/read|Ottiene l'elenco delle sottoscrizioni di prodotto.|
|/service/products/apis/read|Ottiene l'elenco delle API aggiunte al prodotto esistente.|
|/service/products/apis/write|Aggiunge le API esistenti al prodotto esistente.|
|/service/products/apis/delete|Rimuove le API esistenti dal prodotto esistente.|
|/service/products/policy/read|Ottiene la configurazione dei criteri del prodotto esistente.|
|/service/products/policy/write|Imposta la configurazione dei criteri del prodotto esistente.|
|/service/products/policy/delete|Rimuove la configurazione dei criteri del prodotto esistente.|
|/service/products/groups/read|Ottiene l'elenco di gruppi di sviluppatori associato al prodotto.|
|/service/products/groups/write|Associa il gruppo di sviluppatori esistenti al prodotto esistente.|
|/service/products/groups/delete|Elimina l’associazione del gruppo di sviluppatori esistenti al prodotto esistente.|
|/service/openidConnectProviders/read|Ottiene l'elenco di provider di OpenID Connect o le informazioni dettagliate del Provider di OpenID Connect.|
|/service/openidConnectProviders/write|Crea un nuovo provider OpenID Connect o aggiorna i dettagli di un provider di OpenID Connect esistente.|
|/service/openidConnectProviders/delete|Rimuove il provider di OpenID Connect esistente.|
|/service/certificates/read|Ottiene l'elenco di certificati o i dettagli del certificato.|
|/service/certificates/write|Aggiunge un nuovo certificato.|
|/service/certificates/delete|Elimina un certificato esistente.|
|/service/properties/read|Ottiene l'elenco di tutte le proprietà o i dettagli della proprietà specificata.|
|/service/properties/write|Crea una nuova proprietà o aggiorna il valore per la proprietà specificata.|
|/service/properties/delete|Rimuove la proprietà esistente.|
|/service/groups/read|Ottiene l'elenco dei gruppi o i dettagli di un gruppo.|
|/service/groups/write|Crea un nuovo gruppo o aggiorna i dettagli di un gruppo esistente.|
|/service/groups/delete|Rimuove il gruppo esistente.|
|/service/groups/users/read|Ottiene l'elenco degli utenti del gruppo.|
|/service/groups/users/write|Aggiunge l’utente esistente al gruppo esistente.|
|/service/groups/users/delete|Rimuove l’utente esistente dal gruppo esistente.|
|/service/authorizationServers/read|Ottiene l'elenco dei server di autorizzazione o i dettagli del server di autorizzazione.|
|/service/authorizationServers/write|Crea un nuovo server di autorizzazione o aggiorna i dettagli di un server di autorizzazione esistente.|
|/service/authorizationServers/delete|Rimuove il server di autorizzazione esistente.|
|/service/reports/bySubscription/read|Ottiene un report aggregato in base alla sottoscrizione.|
|/service/reports/byRequest/read|Ottiene i dati dei report sulle richieste|
|/service/reports/byOperation/read|Ottiene un report aggregato per operazioni.|
|/service/reports/byGeo/read|Ottenere il report aggregato per area geografica.|
|/service/reports/byUser/read|Ottiene un report aggregato per sviluppatori.|
|/service/reports/byTime/read|Ottiene un report aggregato per periodi di tempo.|
|/service/reports/byApi/read|Ottiene un report aggregato per API.|
|/service/reports/byProduct/read|Ottiene un report aggregato per prodotti.|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Operazione | DESCRIZIONE |
|---|---|
|/elevateAccess/action|Concede al chiamante l'accesso di tipo Amministratore Accesso utenti a livello dell'ambito del tenant|
|/classicAdministrators/read|Esegue la lettura degli amministratori per la sottoscrizione.|
|/classicAdministrators/write|Aggiunge o modifica l'amministratore in una sottoscrizione.|
|/classicAdministrators/delete|Rimuove l'amministratore dalla sottoscrizione.|
|/locks/read|Ottiene i blocchi per l'ambito specificato.|
|/locks/write|Aggiunge i blocchi per l'ambito specificato.|
|/locks/delete|Elimina i blocchi per l'ambito specificato.|
|/policyAssignments/read|Ottiene informazioni su un'assegnazione di criteri.|
|/policyAssignments/write|Crea un'assegnazione di criteri per l'ambito specificato.|
|/policyAssignments/delete|Elimina un'assegnazione di criteri per l'ambito specificato.|
|/permissions/read|Elenca tutte le autorizzazioni del chiamante per un ambito specifico.|
|/roleDefinitions/read|Ottiene informazioni su una definizione di ruolo.|
|/roleDefinitions/write|Crea o aggiorna una definizione del ruolo personalizzata con le autorizzazioni specificate e gli ambiti assegnabili.|
|/roleDefinitions/delete|Elimina la definizione del ruolo personalizzata specificata.|
|/providerOperations/read|Ottiene operazioni per tutti i provider di risorse che possono essere usati nelle definizioni del ruolo.|
|/policyDefinitions/read|Ottiene informazioni su una definizione di criteri.|
|/policyDefinitions/write|Crea una definizione di criteri personalizzata.|
|/policyDefinitions/delete|Elimina una definizione criteri.|
|/roleAssignments/read|Ottiene informazioni su un'assegnazione di ruolo.|
|/roleAssignments/write|Crea un'assegnazione di ruolo per l'ambito specificato.|
|/roleAssignments/delete|È possibile eliminare un'assegnazione di ruolo nell'ambito specificato.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Operazione | DESCRIZIONE |
|---|---|
|/automationAccounts/read|Ottiene un account di automazione di Azure|
|/automationAccounts/write|Crea o aggiorna un account di automazione di Azure|
|/automationAccounts/delete|Elimina un account di automazione di Azure|
|/automationAccounts/configurations/readContent/action|Ottiene un contenuto di Automation DSC per Azure|
|/automationAccounts/hybridRunbookWorkerGroups/read|Legge le risorse del ruolo di lavoro ibrido per runbook|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Elimina le risorse del ruolo di lavoro ibrido per runbook|
|/automationAccounts/jobSchedules/read|Ottiene una pianificazione del processo di automazione di Azure|
|/automationAccounts/jobSchedules/write|Crea una pianificazione del processo di automazione di Azure|
|/automationAccounts/jobSchedules/delete|Elimina una pianificazione del processo di automazione di Azure|
|/automationAccounts/connectionTypes/read|Ottiene un asset del tipo di connessione di automazione di Azure|
|/automationAccounts/connectionTypes/write|Crea un asset del tipo di connessione di automazione di Azure|
|/automationAccounts/connectionTypes/delete|Elimina un asset del tipo di connessione di automazione di Azure|
|/automationAccounts/modules/read|Ottiene un modulo di automazione di Azure|
|/automationAccounts/modules/write|Crea o aggiorna un modulo di automazione di Azure|
|/automationAccounts/modules/delete|Elimina un modulo di automazione di Azure|
|/automationAccounts/credentials/read|Ottiene un asset delle credenziali di automazione di Azure|
|/automationAccounts/credentials/write|Crea o aggiorna un asset delle credenziali di automazione di Azure|
|/automationAccounts/credentials/delete|Elimina un asset delle credenziali di automazione di Azure|
|/automationAccounts/certificates/read|Ottiene un asset del certificato di automazione di Azure|
|/automationAccounts/certificates/write|Crea o aggiorna un asset del certificato di automazione di Azure|
|/automationAccounts/certificates/delete|Elimina un asset del certificato di automazione di Azure|
|/automationAccounts/schedules/read|Ottiene un asset della pianificazione di automazione di Azure|
|/automationAccounts/schedules/write|Crea o aggiorna un asset della pianificazione di automazione di Azure|
|/automationAccounts/schedules/delete|Elimina un asset della pianificazione di automazione di Azure|
|/automationAccounts/jobs/read|Ottiene un processo di automazione di Azure|
|/automationAccounts/jobs/write|Crea un processo di automazione di Azure|
|/automationAccounts/jobs/stop/action|Arresta un processo di automazione di Azure|
|/automationAccounts/jobs/suspend/action|Sospende un processo di automazione di Azure|
|/automationAccounts/jobs/resume/action|Riprende un processo di automazione di Azure|
|/automationAccounts/jobs/runbookContent/action|Ottiene il contenuto del runbook di automazione di Azure durante l'esecuzione del processo|
|/automationAccounts/jobs/output/action|Ottiene l'output di un processo|
|/automationAccounts/jobs/read|Ottiene un processo di automazione di Azure|
|/automationAccounts/jobs/write|Crea un processo di automazione di Azure|
|/automationAccounts/jobs/stop/action|Arresta un processo di automazione di Azure|
|/automationAccounts/jobs/suspend/action|Sospende un processo di automazione di Azure|
|/automationAccounts/jobs/resume/action|Riprende un processo di automazione di Azure|
|/automationAccounts/jobs/streams/read|Ottiene un flusso del processo di automazione di Azure|
|/automationAccounts/connections/read|Ottiene un asset della connessione di automazione di Azure|
|/automationAccounts/connections/write|Crea o aggiorna un asset della connessione di automazione di Azure|
|/automationAccounts/connections/delete|Elimina un asset della connessione di automazione di Azure|
|/automationAccounts/variables/read|Esegue la lettura di un asset della variabile di automazione di Azure|
|/automationAccounts/variables/write|Crea o aggiorna un asset della variabile di automazione di Azure|
|/automationAccounts/variables/delete|Elimina un asset della variabile di automazione di Azure|
|/automationAccounts/runbooks/readContent/action|Ottiene il contenuto di un runbook di automazione di Azure|
|/automationAccounts/runbooks/read|Ottiene un runbook di automazione di Azure|
|/automationAccounts/runbooks/write|Crea o aggiorna un runbook di automazione di Azure|
|/automationAccounts/runbooks/delete|Elimina un runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/readContent/action|Ottiene il contenuto di una bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/writeContent/action|Crea il contenuto di una bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/read|Ottiene una bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/publish/action|Pubblica una bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/undoEdit/action|Annulla le modifiche di una bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/testJob/read|Ottiene un processo di test per la bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/testJob/write|Crea un processo di test per la bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/testJob/stop/action|Interrompe un processo di test per la bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Sospende un processo di test per la bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/testJob/resume/action|Riprende un processo di test per la bozza del runbook di automazione di Azure|
|/automationAccounts/webhooks/read|Esegue la lettura un webhook di automazione di Azure|
|/automationAccounts/webhooks/write|Crea o aggiorna un webhook di automazione di Azure|
|/automationAccounts/webhooks/delete|Elimina un webhook di automazione di Azure |
|/automationAccounts/webhooks/generateUri/action|Genera un URI per un webhook di automazione di Azure|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

Questo provider non è un provider ARM completo e non fornisce operazioni ARM.

## <a name="microsoftbatch"></a>Microsoft.Batch

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Registra la sottoscrizione per il provider di risorse Batch e consente la creazione di account Batch|
|/batchAccounts/write|Crea un nuovo account Batch o aggiorna un account Batch esistente|
|/batchAccounts/read|Elenca account Batch oppure ottiene le proprietà di un account Batch|
|/batchAccounts/delete|Elimina un account Batch|
|/batchAccounts/listkeys/action|Elenca le chiavi di accesso per un account Batch|
|/batchAccounts/regeneratekeys/action|Rigenera le chiavi di accesso per un account Batch|
|/batchAccounts/syncAutoStorageKeys/action|Consente di sincronizzare le chiavi di accesso per l'account di archiviazione automatica configurato per un account Batch|
|/batchAccounts/applications/read|Elenca le applicazioni o ottiene le proprietà di un'applicazione|
|/batchAccounts/applications/write|Crea una nuova applicazione o ne aggiorna una esistente|
|/batchAccounts/applications/delete|Elimina un'applicazione|
|/batchAccounts/applications/versions/read|Ottiene le proprietà di un pacchetto dell'applicazione|
|/batchAccounts/applications/versions/write|Crea un nuovo pacchetto dell'applicazione o ne aggiorna uno esistente|
|/batchAccounts/applications/versions/activate/action|Attiva un pacchetto dell'applicazione|
|/batchAccounts/applications/versions/delete|Elimina un pacchetto dell'applicazione|
|/locations/quotas/read|Ottiene quote Batch della sottoscrizione specificata nell'area di Azure specificata|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Operazione | DESCRIZIONE |
|---|---|
|/invoices/read|Elenca le fatture disponibili|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Operazione | DESCRIZIONE |
|---|---|
|/mapApis/Read|Operazione di lettura|
|/mapApis/Write|Operazione di scrittura|
|/mapApis/Delete|Operazione di eliminazione|
|/mapApis/regenerateKey/action|Rigenera la chiave|
|/mapApis/listSecrets/action|Elenca i segreti|
|/mapApis/listSingleSignOnToken/action|Esegue la lettura del token di autorizzazione Single Sign-On per la risorsa|
|/Operations/read|Descrizione dell'operazione.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Operazione | DESCRIZIONE |
|---|---|
|/checknameavailability/action|Verifica se un nome è disponibile per essere assegnato a nuova cache Redis|
|/register/action|Registra il provider di risorse 'Microsoft.Cache' con una sottoscrizione|
|/unregister/action|Annulla la registrazione del provider di risorse 'Microsoft.Cache' con una sottoscrizione|
|/redis/write|Modifica la configurazione e le impostazioni della Cache Redis nel portale di gestione|
|/redis/read|Visualizza la configurazione e le impostazioni della Cache Redis nel portale di gestione|
|/redis/delete|Elimina l'intera Cache Redis|
|/redis/listKeys/action|Visualizza il valore delle chiavi di accesso della Cache Redis nel portale di gestione|
|/redis/regenerateKey/action|Modifica il valore delle chiavi di accesso della Cache Redis nel portale di gestione|
|/redis/import/action|Importa i dati di un formato specificato da più BLOB in Redis|
|/redis/export/action|Esporta i dati Redis nei BLOB di archiviazione predefiniti nel formato specificato|
|/redis/forceReboot/action|Forza il riavvio di un'istanza della cache, con possibile perdita di dati.|
|/redis/stop/action|Arresta un'istanza della cache.|
|/redis/start/action|Avvia un'istanza della cache.|
|/redis/metricDefinitions/read|Ottiene la metrica disponibile per una cache Redis|
|/redis/firewallRules/read|Ottiene le regole del firewall per gli indirizzi IP di una cache Redis|
|/redis/firewallRules/write|Modifica le regole del firewall per gli indirizzi IP di una cache Redis|
|/redis/firewallRules/delete|Elimina le regole del firewall per gli indirizzi IP di una cache Redis|
|/redis/listUpgradeNotifications/read|Elenca le notifiche di aggiornamento più recenti per il tenant della cache.|
|/redis/linkedservers/read|Ottiene i server collegati associati a una cache Redis.|
|/redis/linkedservers/write|Aggiunge un server collegato a una cache Redis|
|/redis/linkedservers/delete|Elimina un server collegato da una cache Redis|
|/redis/patchSchedules/read|Ottiene la pianificazione dell'applicazione di patch di una cache Redis|
|/redis/patchSchedules/write|Modifica la pianificazione dell'applicazione di patch di una cache Redis|
|/redis/patchSchedules/delete|Elimina la pianificazione dell'applicazione di patch di una cache Redis|

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Operazione | DESCRIZIONE |
|---|---|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Esegue il provisioning dell'entità servizio per l'entità app del servizio|
|/validateCertificateRegistrationInformation/Action|Convalida l'oggetto acquisto di certificato senza inviarlo|
|/register/action|Registra il provider di risorse certificati Microsoft per la sottoscrizione|
|/certificateOrders/Write|Aggiunge un nuovo ordine di certificato o ne aggiorna uno esistente|
|/certificateOrders/Delete|Elimina un certificato del servizio app esistente|
|/certificateOrders/Read|Ottiene l'elenco degli ordini di certificato|
|/certificateOrders/reissue/Action|Riemette un ordine di certificato esistente|
|/certificateOrders/renew/Action|Rinnova un ordine di certificato esistente|
|/certificateOrders/retrieveCertificateActions/Action|Recupera l'elenco di azioni di certificato|
|/certificateOrders/retrieveEmailHistory/Action|Recupera la cronologia della posta elettronica di certificato|
|/certificateOrders/resendEmail/Action|Invia di nuovo la posta elettronica di certificato|
|/certificateOrders/verifyDomainOwnership/Action|Verificare la proprietà del dominio|
|/certificateOrders/resendRequestEmails/Action|Invia di nuovo messaggi di richiesta a un altro indirizzo di posta elettronica|
|/certificateOrders/resendRequestEmails/Action|Recupera il sealing di sito per un certificato del servizio app emesso|
|/certificateOrders/certificates/Write|Aggiunge un nuovo certificato o ne aggiorna uno esistente|
|/certificateOrders/certificates/Delete|Elimina un certificato esistente|
|/certificateOrders/certificates/Read|Ottiene l'elenco dei certificati|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Consente di eseguire la registrazione per Calcolo classico|
|/checkDomainNameAvailability/action|Controlla la disponibilità di un nome di dominio specifico.|
|/moveSubscriptionResources/action|Sposta tutte le risorse classiche in un'altra sottoscrizione.|
|/validateSubscriptionMoveAvailability/action|Convalida la disponibilità della sottoscrizione per l'operazione di spostamento classica.|
|/operatingSystemFamilies/read|Elenca le famiglie di sistemi operativi guest disponibili in Microsoft Azure ed elenca anche le versioni dei sistemi operativi disponibili per ogni f
|/capabilities/read|Mostra le funzionalità|
|/operatingSystems/read|Elenca le versioni dei sistemi operativi guest attualmente disponibili in Microsoft Azure.|
|/resourceTypes/skus/read|Ottiene l'elenco degli SKU per i tipi di risorse supportati.|
|/domainNames/read|Restituisce i nomi di dominio per le risorse.|
|/domainNames/write|Aggiunge o modifica i nomi di dominio per le risorse.|
|/domainNames/delete|Rimuove i nomi di dominio per le risorse.|
|/domainNames/swap/action|Passa dallo slot di staging allo slot di produzione.|
|/domainNames/serviceCertificates/read|Restituisce i certificati di servizio usati.|
|/domainNames/serviceCertificates/write|Aggiunge o modifica i certificati di servizio usati.|
|/domainNames/serviceCertificates/delete|Elimina i certificati di servizio usati.|
|/domainNames/serviceCertificates/operationStatuses/read|Esegue la lettura dello stato dell'operazione per i certificati di servizio dei nomi di dominio.|
|/domainNames/capabilities/read|Mostra le funzionalità del nome di dominio|
|/domainNames/extensions/read|Restituisce le estensioni del nome di dominio.|
|/domainNames/extensions/write|Aggiunge le estensioni del nome di dominio.|
|/domainNames/extensions/delete|Rimuove le estensioni del nome di dominio.|
|/domainNames/extensions/operationStatuses/read|Esegue la lettura dello stato dell'operazione per le estensioni dei nomi di dominio.|
|/domainNames/active/write|Imposta il nome del dominio attivo.|
|/domainNames/slots/read|Mostra gli slot di distribuzione.|
|/domainNames/slots/write|Crea o aggiorna la distribuzione.|
|/domainNames/slots/delete|Elimina uno slot di distribuzione specifico.|
|/domainNames/slots/start/action|Avvia uno slot di distribuzione.|
|/domainNames/slots/stop/action|Sospende lo slot di distribuzione.|
|/domainNames/slots/operationStatuses/read|Esegue la lettura dello stato dell'operazione per gli slot dei nomi di dominio.|
|/domainNames/slots/roles/read|Ottiene il ruolo per lo slot di distribuzione.|
|/domainNames/slots/roles/extensionReferences/read|Restituisce il riferimento all'estensione del ruolo dello slot di distribuzione.|
|/domainNames/slots/roles/extensionReferences/write|Aggiunge o modifica il riferimento all'estensione del ruolo dello slot di distribuzione.|
|/domainNames/slots/roles/extensionReferences/delete|Rimuove il riferimento all'estensione del ruolo dello slot di distribuzione.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|Legge lo stato operazione dei riferimenti all'estensione per i ruoli slot dei nomi di dominio.|
|/domainNames/slots/roles/roleInstances/read|Ottiene l'istanza del ruolo.|
|/domainNames/slots/roles/roleInstances/restart/action|Riavvia le istanze del ruolo.|
|/domainNames/slots/roles/roleInstances/reimage/action|Ricrea l'immagine dell'istanza del ruolo.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|Legge lo stato dell'operazione per le istanze ruolo dei ruoli slot dei nomi di dominio.|
|/domainNames/slots/state/start/write|Imposta lo stato dello slot di distribuzione su arrestato.|
|/domainNames/slots/state/stop/write|Imposta lo stato dello slot di distribuzione su avviato.|
|/domainNames/slots/upgradeDomain/write|Esegue l'aggiornamento del dominio.|
|/domainNames/internalLoadBalancers/read|Ottiene i servizi di bilanciamento del carico interno.|
|/domainNames/internalLoadBalancers/write|Crea un nuovo bilanciamento del carico interno.|
|/domainNames/internalLoadBalancers/delete|Rimuove un nuovo bilanciamento del carico interno.|
|/domainNames/internalLoadBalancers/operationStatuses/read|Legge lo stato dell'operazione per i servizi di bilanciamento del carico dei nomi di dominio.|
|/domainNames/loadBalancedEndpointSets/read|Visualizza gli endpoint con bilanciamento del carico|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|Legge lo stato dell'operazione per gli endpoint con bilanciamento del carico per i nomi di dominio.|
|/domainNames/availabilitySets/read|Visualizza il set di disponibilità per la risorsa.|
|/quotas/read|Ottiene la quota per la sottoscrizione.|
|/virtualMachines/read|Recupera l'elenco delle macchine virtuali.|
|/virtualMachines/write|Aggiunge o modifica le macchine virtuali.|
|/virtualMachines/delete|Rimuove le macchine virtuali.|
|/virtualMachines/start/action|Avviare la macchina virtuale.|
|/virtualMachines/redeploy/action|Ridistribuisce la macchina virtuale.|
|/virtualMachines/restart/action|Riavvia le macchine virtuali.|
|/virtualMachines/stop/action|Arresta la macchina virtuale.|
|/virtualMachines/shutdown/action|Chiude la macchina virtuale.|
|/virtualMachines/attachDisk/action|Collega un disco dati a una macchina virtuale.|
|/virtualMachines/detachDisk/action|Scollega un disco dati da una macchina virtuale.|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|Scarica il file RDP per la macchina virtuale.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/read|Ottiene il gruppo di sicurezza di rete associato all'interfaccia di rete.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/write|Aggiunge un gruppo di sicurezza di rete associato all'interfaccia di rete.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/delete|Elimina il gruppo di sicurezza di rete associato all'interfaccia di rete.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/operationStatuses/read|Legge lo stato operazione dei gruppi di sicurezza di rete associati alle macchine virtuali.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Ottiene le definizioni di metrica.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene le impostazioni di diagnostica.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Aggiunge o modifica le impostazioni di diagnostica.|
|/virtualMachines/metrics/read|Ottiene la metrica.|
|/virtualMachines/operationStatuses/read|Legge lo stato operazione delle macchine virtuali.|
|/virtualMachines/extensions/read|Ottiene l'estensione macchina virtuale.|
|/virtualMachines/extensions/write|Visualizza l'estensione macchina virtuale.|
|/virtualMachines/extensions/operationStatuses/read|Legge lo stato operazione delle estensioni macchina virtuale.|
|/virtualMachines/asyncOperations/read|Ottiene le operazioni asincrone possibili|
|/virtualMachines/disks/read|Recupera l'elenco dei dischi dati|
|/virtualMachines/associatedNetworkSecurityGroups/read|Ottiene il gruppo di sicurezza di rete associato alla macchina virtuale.|
|/virtualMachines/associatedNetworkSecurityGroups/write|Aggiunge un gruppo di sicurezza di rete associato alla macchina virtuale.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|Elimina il gruppo di sicurezza di rete associato alla macchina virtuale.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|Legge lo stato operazione dei gruppi di sicurezza di rete associati alle macchine virtuali.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Esegue la registrazione per Rete classica|
|/gatewaySupportedDevices/read|Recupera l'elenco dei dispositivi supportati.|
|/reservedIps/read|Ottiene gli IP riservati|
|/reservedIps/write|Aggiunge un nuovo IP riservato|
|/reservedIps/delete|Elimina un IP riservato.|
|/reservedIps/link/action|Collega un IP riservato|
|/reservedIps/join/action|Unisce un IP riservato|
|/reservedIps/operationStatuses/read|Legge lo stato dell'operazione per gli IP riservati.|
|/virtualNetworks/read|Ottiene la rete virtuale.|
|/virtualNetworks/write|Aggiunge una nuova rete virtuale.|
|/virtualNetworks/delete|Elimina la rete virtuale.|
|/virtualNetworks/peer/action|Associa una rete virtuale a un'altra rete virtuale.|
|/virtualNetworks/join/action|Unisce la rete virtuale.|
|/virtualNetworks/checkIPAddressAvailability/action|Verifica la disponibilità di un determinato indirizzo IP in una rete virtuale.|
|/virtualNetworks/capabilities/read|Visualizza le funzionalità|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/read|Ottiene il gruppo di sicurezza di rete associato alla subnet.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/write|Aggiunge un gruppo di sicurezza di rete associato alla subnet.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/delete|Elimina il gruppo di sicurezza di rete associato alla subnet.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/operationStatuses/read|Legge lo stato operazione del gruppo di sicurezza di rete associato alla subnet della rete virtuale.|
|/virtualNetworks/operationStatuses/read|Legge lo stato operazione delle reti virtuali.|
|/virtualNetworks/gateways/read|Ottiene i gateway di rete virtuale.|
|/virtualNetworks/gateways/write|Aggiunge un gateway di rete virtuale.|
|/virtualNetworks/gateways/delete|Elimina il gateway di rete virtuale.|
|/virtualNetworks/gateways/startDiagnostics/action|Avvia la diagnostica per il gateway di rete virtuale.|
|/virtualNetworks/gateways/stopDiagnostics/action|Arresta la diagnostica del gateway di rete virtuale.|
|/virtualNetworks/gateways/downloadDiagnostics/action|Scarica la diagnostica del gateway.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Recupera la chiave del servizio del circuito.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|Scarica lo script di configurazione del dispositivo.|
|/virtualNetworks/gateways/listPackage/action|Elenca i pacchetti del gateway di rete virtuale.|
|/virtualNetworks/gateways/operationStatuses/read|Legge lo stato operazione dei gateway di rete virtuale.|
|/virtualNetworks/gateways/packages/read|Ottiene il pacchetto del gateway di rete virtuale.|
|/virtualNetworks/gateways/connections/read|Recupera l'elenco di connessioni.|
|/virtualNetworks/gateways/connections/connect/action|Connette una connessione gateway da sito a sito.|
|/virtualNetworks/gateways/connections/disconnect/action|Disconnette una connessione gateway da sito a sito.|
|/virtualNetworks/gateways/connections/test/action|Esegue il test di una connessione gateway da sito a sito.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|Esegue la lettura dei certificati client revocati.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|Revoca un certificato client.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|Annulla la revoca di un certificato client.|
|/virtualNetworks/gateways/clientRootCertificates/read|Trova i certificati radice client.|
|/virtualNetworks/gateways/clientRootCertificates/write|Carica un nuovo certificato radice client.|
|/virtualNetworks/gateways/clientRootCertificates/delete|Elimina il certificato client del gateway di rete virtuale.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|Scarica il certificato in base all'identificazione personale.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|Elenca il pacchetto del certificato del gateway di rete virtuale.|
|/networkSecurityGroups/read|Ottiene il gruppo di sicurezza di rete.|
|/networkSecurityGroups/write|Aggiunge un nuovo gruppo di sicurezza di rete.|
|/networkSecurityGroups/delete|Elimina il gruppo di sicurezza di rete.|
|/networkSecurityGroups/operationStatuses/read|Legge lo stato operazione del gruppo di sicurezza di rete.|
|/networkSecurityGroups/securityRules/read|Ottiene la regola di sicurezza.|
|/networkSecurityGroups/securityRules/write|Aggiunge o aggiorna una regola di sicurezza.|
|/networkSecurityGroups/securityRules/delete|Elimina la regola di sicurezza.|
|/networkSecurityGroups/securityRules/operationStatuses/read|Legge lo stato dell'operazione per le regole di sicurezza del gruppo di sicurezza di rete.|
|/quotas/read|Ottiene la quota per la sottoscrizione.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Consente di eseguire la registrazione per Archiviazione (versione classica)|
|/checkStorageAccountAvailability/action|Verifica la disponibilità di un account di archiviazione.|
|/capabilities/read|Visualizza le funzionalità|
|/publicImages/read|Ottiene l'immagine di macchina virtuale pubblica.|
|/images/read|Restituisce l'immagine.|
|/storageAccounts/read|Restituisce l'account di archiviazione con l'account specificato.|
|/storageAccounts/write|Aggiunge un nuovo account di archiviazione.|
|/storageAccounts/delete|Elimina l'account di archiviazione.|
|/storageAccounts/listKeys/action|Elenca le chiavi di accesso per gli account di archiviazione.|
|/storageAccounts/regenerateKey/action|Rigenera le chiavi di accesso esistenti per l'account di archiviazione.|
|/storageAccounts/operationStatuses/read|Legge lo stato dell'operazione per la risorsa.|
|/storageAccounts/images/read|Restituisce l'immagine dell'account di archiviazione.|
|/storageAccounts/images/delete|Elimina una data immagine dell'account di archiviazione.|
|/storageAccounts/disks/read|Restituisce il disco dell'account di archiviazione.|
|/storageAccounts/disks/write|Aggiunge un disco dell'account di archiviazione.|
|/storageAccounts/disks/delete|Elimina un disco specifico dell'account di archiviazione.|
|/storageAccounts/disks/operationStatuses/read|Legge lo stato dell'operazione per la risorsa.|
|/storageAccounts/osImages/read|Restituisce l'immagine del sistema operativo dell'account di archiviazione.|
|/storageAccounts/osImages/delete|Elimina un'immagine specifica del sistema operativo dell'account di archiviazione.|
|/storageAccounts/services/read|Ottiene i servizi disponibili.|
|/storageAccounts/services/metricDefinitions/read|Ottiene le definizioni di metrica.|
|/storageAccounts/services/metrics/read|Ottiene la metrica.|
|/storageAccounts/services/diagnosticSettings/read|Ottiene le impostazioni di diagnostica.|
|/storageAccounts/services/diagnosticSettings/write|Consente di aggiungere o modificare le impostazioni di diagnostica.|
|/disks/read|Restituisce il disco dell'account di archiviazione.|
|/osImages/read|Restituisce l'immagine del sistema operativo.|
|/quotas/read|Ottiene la quota per la sottoscrizione.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Operazione | DESCRIZIONE |
|---|---|
|/accounts/read|Legge gli account delle API.|
|/accounts/write|Scrive gli account delle API.|
|/accounts/delete|Elimina gli account delle API|
|/accounts/listKeys/action|Elenco delle chiavi|
|/accounts/regenerateKey/action|Rigenerazione della chiave|
|/accounts/skus/read|Legge gli SKU disponibili per una risorsa esistente.|
|/accounts/usages/read|Ottiene l'uso della quota per una risorsa esistente.|
|/Operations/read|Descrizione dell'operazione.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Operazione | DESCRIZIONE |
|---|---|
|/RateCard/read|Restituisce dati dell'offerta, metadati delle risorse o del contatore e tariffe per una determinata sottoscrizione.|
|/UsageAggregates/read|Recupera il consumo di Microsoft Azure da una sottoscrizione. Il risultato contiene dati di utilizzo delle aggregazioni, informazioni correlate a sottoscrizioni e risorse in un intervallo di tempo specifico.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Registra la sottoscrizione con il provider di risorse Microsoft.Compute|
|/restorePointCollections/read|Ottiene le proprietà di un insieme di punti di ripristino|
|/restorePointCollections/write|Crea un nuovo insieme di punti di ripristino o aggiorna un insieme esistente|
|/restorePointCollections/delete|Elimina la raccolta di punti di ripristino e i punti di ripristino contenuti|
|/restorePointCollections/restorePoints/read|Ottiene le proprietà di un punto di ripristino|
|/restorePointCollections/restorePoints/write|Crea un nuovo punto di ripristino|
|/restorePointCollections/restorePoints/delete|Elimina il punto di ripristino|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Ottiene le proprietà di un punto di ripristino insieme agli URI SAS del BLOB|
|/virtualMachineScaleSets/read|Ottiene le proprietà di un set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/write|Crea un nuovo set di scalabilità di macchine virtuali o ne aggiorna uno esistente|
|/virtualMachineScaleSets/delete|Elimina il set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/start/action|Avvia le istanze del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/powerOff/action|Disabilita le istanze del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/restart/action|Riavvia le istanze del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/deallocate/action|Disabilita e rilascia le risorse di calcolo per le istanze del set di scalabilità di macchine virtuali |
|/virtualMachineScaleSets/manualUpgrade/action|Aggiorna manualmente le istanze all’ultimo modello del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/scale/action|Scala verticalmente/orizzontalmente il numero di istanze di un set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/instanceView/read|Ottiene la vista delle istanze del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/skus/read|Elenca le SKU valide per un set di scalabilità di macchine virtuali esistente|
|/virtualMachineScaleSets/virtualMachines/read|Recupera le proprietà di una macchina virtuale in un set di scalabilità VM|
|/virtualMachineScaleSets/virtualMachines/delete|Elimina una specifica macchina virtuale in un set di scalabilità VM.|
|/virtualMachineScaleSets/virtualMachines/start/action|Avvia un'istanza di macchina virtuale in un set di scalabilità VM.|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|Disabilita un'istanza di macchina virtuale in un set di scalabilità VM.|
|/virtualMachineScaleSets/virtualMachines/restart/action|Riavvia un'istanza di macchina virtuale in un set di scalabilità VM.|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|Disabilita e rilascia le risorse di calcolo per una macchina virtuale in un set di scalabilità VM.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|Recupera la vista istanza di una macchina virtuale in un set di scalabilità di una VM.|
|/images/read|Ottiene le proprietà dell’immagine|
|/images/write|Crea una nuova immagine o ne aggiorna una esistente|
|/images/delete|Elimina l'immagine|
|/operations/read|Elenca le operazioni disponibili sul provider di risorse Microsoft.Compute|
|/disks/read|Ottiene le proprietà di un disco|
|/disks/write|Crea un nuovo disco o ne aggiorna uno esistente|
|/disks/delete|Elimina il disco|
|/disks/beginGetAccess/action|Ottiene gli URI SAS del disco per l’accesso al BLOB|
|/disks/endGetAccess/action|Revoca l’URI SAS del disco|
|/snapshots/read|Ottiene le proprietà di una snapshot|
|/snapshots/write|Crea una nuova snapshot o ne aggiorna una esistente|
|/snapshots/delete|Elimina una snapshot|
|/availabilitySets/read|Ottiene le proprietà di un set di disponibilità|
|/availabilitySets/write|Crea un nuovo set di disponibilità o ne aggiorna uno esistente|
|/availabilitySets/delete|Elimina il set di disponibilità|
|/availabilitySets/vmSizes/read|Elenca le dimensioni disponibili per creare o aggiornare una macchina virtuale nel set di disponibilità|
|/virtualMachines/read|Ottiene le proprietà di una macchina virtuale|
|/virtualMachines/write|Crea una nuova macchina virtuale o ne aggiorna una esistente|
|/virtualMachines/delete|Elimina la macchina virtuale|
|/virtualMachines/start/action|Avvia la macchina virtuale|
|/virtualMachines/powerOff/action|Disabilita la macchina virtuale. Notare che la macchina virtuale continuerà a essere fatturata.|
|/virtualMachines/redeploy/action|Ridistribuisce la macchina virtuale|
|/virtualMachines/restart/action|Riavvia la macchina virtuale|
|/virtualMachines/deallocate/action|Disabilita la macchina virtuale e rilascia le risorse di calcolo|
|/virtualMachines/generalize/action|Imposta lo stato della macchina virtuale a Generalizzato e prepara la macchina virtuale per l'acquisizione|
|/virtualMachines/capture/action|Acquisisce la macchina virtuale copiando i dischi rigidi virtuali e genera un modello utilizzabile per creare macchine virtuali simili|
|/virtualMachines/convertToManagedDisks/action|Converte i dischi basati su BLOB della macchina virtuale in dischi gestiti|
|/virtualMachines/vmSizes/read|Elenca le dimensioni disponibili a cui è possibile aggiornare la macchina virtuale|
|/virtualMachines/instanceView/read|Ottiene lo stato di runtime dettagliato della macchina virtuale e delle relative risorse|
|/virtualMachines/extensions/read|Ottiene le proprietà di un'estensione macchina virtuale|
|/virtualMachines/extensions/write|Crea una nuova estensione macchina virtuale o ne aggiorna una esistente|
|/virtualMachines/extensions/delete|Elimina l'estensione macchina virtuale|
|/locations/vmSizes/read|Elenca le dimensioni delle macchine virtuali disponibili in una posizione|
|/locations/usages/read|Ottiene i limiti del servizio e le quantità di utilizzo corrente delle risorse di calcolo della sottoscrizione in una posizione|
|/locations/operations/read|Ottiene lo stato di un'operazione asincrona|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Registra la sottoscrizione per il provider di risorse registro contenitori e consente la creazione di registri contenitori.|
|/checknameavailability/read|Controlla che il nome del registro sia valido e che non sia in uso.|
|/registries/read|Restituisce l'elenco dei registri contenitori o ottiene le proprietà del registro contenitori specificato.|
|/registries/write|Crea un registro contenitori con i parametri specificati o aggiorna le proprietà o i tag per il registro contenitori specificato.|
|/registries/delete|Elimina un registro contenitori esistente.|
|/registries/listCredentials/action|Elenca le credenziali di accesso per il registro contenitori specificato.|
|/registries/regenerateCredential/action|Rigenera le credenziali di accesso per il registro contenitori specificato.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Operazione | DESCRIZIONE |
|---|---|
|/containerServices/subscriptions/read|Ottiene i servizi contenitore specificati in base alla sottoscrizione|
|/containerServices/resourceGroups/read|Ottiene i servizi contenitore specificati in base al gruppo di risorse|
|/containerServices/resourceGroups/ContainerServiceName/read|Ottiene il servizio contenitore specificato|
|/containerServices/resourceGroups/ContainerServiceName/write|Inserisce o aggiorna il servizio contenitore specificato|
|/containerServices/resourceGroups/ContainerServiceName/delete|Elimina il servizio contenitore specificato|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Operazione | DESCRIZIONE |
|---|---|
|/updateCommunicationPreference/action|Aggiorna le preferenze di comunicazione|
|/listCommunicationPreference/action|Elenca le preferenze di comunicazione|
|/applications/read|Operazione di lettura|
|/applications/write|Operazione di scrittura|
|/applications/write|Operazione di scrittura|
|/applications/delete|Operazione di eliminazione|
|/applications/listSecrets/action|Elenca i segreti|
|/applications/listSingleSignOnToken/action|Esegue la lettura di token Single Sign-On|
|/operations/read|operazioni di lettura|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Operazione | DESCRIZIONE |
|---|---|
|/hubs/read|Esegue la lettura di qualsiasi hub di Azure Customer Insights|
|/hubs/write|Crea o aggiorna qualsiasi hub di Azure Customer Insights|
|/hubs/delete|Elimina qualsiasi hub di Azure Customer Insights|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per la risorsa|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|Ottiene i log disponibili per la risorsa|
|/hubs/authorizationPolicies/read|Esegue la lettura di qualsiasi criterio di firma di accesso condiviso di Azure Customer Insights|
|/hubs/authorizationPolicies/write|Crea o aggiorna qualsiasi criterio di firma di accesso condiviso di Azure Customer Insights|
|/hubs/authorizationPolicies/delete|Elimina qualsiasi criterio di firma di accesso condiviso di Azure Customer Insights|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Rigenera la chiave primaria dei criteri di firma di accesso condiviso di Azure Customer Insights|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Rigenera la chiave secondaria dei criteri di firma di accesso condiviso di Azure Customer Insights|
|/hubs/profiles/read|Esegue la lettura di qualsiasi profilo di Azure Customer Insights|
|/hubs/profiles/write|Esegue la scrittura di qualsiasi profilo di Azure Customer Insights|
|/hubs/kpi/read|Esegue la lettura di qualsiasi indicatore di prestazioni chiave di Azure Customer Insights|
|/hubs/kpi/write|Crea o aggiorna qualsiasi indicatore di prestazioni chiave Azure Customer Insights|
|/hubs/kpi/delete|Elimina qualsiasi indicatore di prestazioni chiave Azure Customer Insights|
|/hubs/views/read|Esegue la lettura di qualsiasi visualizzazione app di Azure Customer Insights|
|/hubs/views/write|Crea o aggiorna qualsiasi visualizzazione app di Azure Customer Insights|
|/hubs/views/delete|Elimina qualsiasi visualizzazione app di Azure Customer Insights|
|/hubs/interactions/read|Esegue la lettura di qualsiasi interazione di Azure Customer Insights|
|/hubs/interactions/write|Crea o aggiorna qualsiasi interazione di Azure Customer Insights|
|/hubs/roleAssignments/read|Esegue la lettura di qualsiasi assegnazione di controllo degli accessi in base al ruolo di Azure Customer Insights|
|/hubs/roleAssignments/write|Crea o aggiorna qualsiasi assegnazione di controllo degli accessi in base al ruolo di Azure Customer Insights|
|/hubs/roleAssignments/delete|Elimina qualsiasi assegnazione di controllo degli accessi in base al ruolo di Azure Customer Insights|
|/hubs/connectors/read|Esegue la lettura di qualsiasi connettore Azure Customer Insights|
|/hubs/connectors/write|Crea o aggiorna qualsiasi connettore Azure Customer Insights|
|/hubs/connectors/delete|Elimina qualsiasi connettore Azure Customer Insights|
|/hubs/connectors/mappings/read|Esegue la lettura di qualsiasi mapping di connettori Azure Customer Insights|
|/hubs/connectors/mappings/write|Crea o aggiorna qualsiasi mapping di connettori Azure Customer Insights|
|/hubs/connectors/mappings/delete|Elimina qualsiasi mapping di connettori Azure Customer Insights|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Operazione | DESCRIZIONE |
|---|---|
|/checkNameAvailability/action|Controlla la disponibilità del nome di catalogo per il tenant.|
|/catalogs/read|Ottiene le proprietà del catalogo o dei cataloghi nella sottoscrizione o nel gruppo di risorse.|
|/catalogs/write|Crea il catalogo o ne aggiorna i tag e le proprietà.|
|/catalogs/delete|Elimina il catalogo.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Operazione | DESCRIZIONE |
|---|---|
|/datafactories/read|Esegue la lettura del data factory.|
|/datafactories/write|Crea o aggiorna il data factory|
|/datafactories/delete|Elimina il data factory.|
|/datafactories/datapipelines/read|Esegue la lettura della pipeline.|
|/datafactories/datapipelines/delete|Elimina la pipeline.|
|/datafactories/datapipelines/pause/action|Sospende la pipeline.|
|/datafactories/datapipelines/resume/action|Riavvia la pipeline.|
|/datafactories/datapipelines/update/action|Aggiorna la pipeline.|
|/datafactories/datapipelines/write|Crea o aggiorna la pipeline|
|/datafactories/linkedServices/read|Esegue la lettura del servizio collegato.|
|/datafactories/linkedServices/delete|Elimina il servizio collegato.|
|/datafactories/linkedServices/write|Crea o aggiorna il servizio collegato|
|/datafactories/tables/read|Esegue la lettura di una tabella.|
|/datafactories/tables/delete|Elimina una tabella.|
|/datafactories/tables/write|Crea o aggiorna una tabella|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Operazione | DESCRIZIONE |
|---|---|
|/accounts/read|Ottiene informazioni sull'account DataLakeAnalytics.|
|/accounts/write|Crea o aggiorna l'account DataLakeAnalytics.|
|/accounts/delete|Elimina l'account DataLakeAnalytics.|
|/accounts/firewallRules/read|Ottiene informazioni su una regola del firewall.|
|/accounts/firewallRules/write|Crea o aggiorna una regola del firewall.|
|/accounts/firewallRules/delete|Elimina una regola del firewall.|
|/accounts/storageAccounts/read|Ottiene l'account di archiviazione collegato per l'account DataLakeAnalytics.|
|/accounts/storageAccounts/write|Collega un account di archiviazione all'account DataLakeAnalytics.|
|/accounts/storageAccounts/delete|Scollega un account di archiviazione dall'account DataLakeAnalytics.|
|/accounts/storageAccounts/Containers/read|Ottiene i contenitori nell'account di archiviazione|
|/accounts/storageAccounts/Containers/listSasTokens/action|Elenca token di firma di accesso condiviso per il contenitore di archiviazione|
|/accounts/dataLakeStoreAccounts/read|Ottiene l'account DataLakeStore collegato per l'account DataLakeAnalytics.|
|/accounts/dataLakeStoreAccounts/write|Collega un account DataLakeStore all'account DataLakeAnalytics.|
|/accounts/dataLakeStoreAccounts/delete|Scollega un account DataLakeStore dall'account DataLakeAnalytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Operazione | DESCRIZIONE |
|---|---|
|/accounts/read|Ottiene informazioni su un account DataLakeStore esistente.|
|/accounts/write|Crea un nuovo account DataLakeStore o aggiorna un account DataLakeStore esistente.|
|/accounts/delete|Elimina un account DataLakeStore esistente.|
|/accounts/firewallRules/read|Ottiene informazioni su una regola del firewall.|
|/accounts/firewallRules/write|Crea o aggiorna una regola del firewall.|
|/accounts/firewallRules/delete|Elimina una regola del firewall.|
|/accounts/trustedIdProviders/read|Ottiene informazioni su un provider di identità attendibile.|
|/accounts/trustedIdProviders/write|Crea o aggiorna un provider di identità attendibile.|
|/accounts/trustedIdProviders/delete|Elimina un provider di identità attendibile.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Registra la sottoscrizione per il provider di risorse del servizio IotHub e consente la creazione di risorse IotHub|
|/checkNameAvailability/Action|Controlla se il nome IotHub è disponibile|
|/usages/Read|Ottiene dettagli sull'utilizzo della sottoscrizione per questo provider.|
|/operations/Read|Ottiene tutte le operazioni ResourceProvider|
|/iotHubs/Read|Ottiene la risorsa o le risorse IotHub|
|/iotHubs/Write|Crea o aggiorna una risorsa IotHub|
|/iotHubs/Delete|Elimina una risorsa IotHub|
|/iotHubs/listkeys/Action|Ottiene tutte le chiavi IotHub|
|/iotHubs/exportDevices/Action|Esporta dispositivi|
|/iotHubs/importDevices/Action|Importa dispositivi|
|/IotHubs/metricDefinitions/read|Ottiene la metrica disponibile per il servizio IotHub|
|/iotHubs/iotHubKeys/listkeys/Action|Ottiene la chiave IotHub per il nome specificato|
|/iotHubs/iotHubStats/Read|Ottiene le statistiche IotHub|
|/iotHubs/quotaMetrics/Read|Ottiene la metrica di quota|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Crea un gruppo di consumer EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Ottiene il gruppo o i gruppi di consumer EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|Elimina un gruppo di consumer EventHub|
|/iotHubs/routing/routes/$testall/Action|Effettua il test di un messaggio con tutte le route esistenti|
|/iotHubs/routing/routes/$testnew/Action|Effettua il test di un messaggio con la route di test fornita|
|/IotHubs/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/IotHubs/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/iotHubs/skus/Read|Ottiene gli SKU IotHub validi|
|/iotHubs/jobs/Read|Ottiene i dettagli del processo o dei processi inviati per un servizio IotHub specifico|
|/iotHubs/routingEndpointsHealth/Read|Ottiene lo stato di tutti gli endpoint di routing per un servizio IotHub|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Operazione | DESCRIZIONE |
|---|---|
|/Subscription/register/action|Registra la sottoscrizione|
|/labs/delete|Elimina lab.|
|/labs/read|Esegue la lettura di lab.|
|/labs/write|Aggiunge o modifica lab.|
|/labs/ListVhds/action|Elenca le immagini di un disco disponibili per la creazione di immagini personalizzate.|
|/labs/GenerateUploadUri/action|Genera un URI per il caricamento di immagini disco personalizzate in un lab.|
|/labs/CreateEnvironment/action|Crea macchine virtuali in un lab.|
|/labs/ClaimAnyVm/action|Attesta una macchina virtuale attestabile casualmente nel lab.|
|/labs/ExportResourceUsage/action|Esporta l'utilizzo delle risorse del lab in un account di archiviazione|
|/labs/users/delete|Elimina profili utente.|
|/labs/users/read|Esegue la lettura di profili utente.|
|/labs/users/write|Aggiunge o modifica profili utente.|
|/labs/users/secrets/delete|Elimina segreti.|
|/labs/users/secrets/read|Esegue la lettura di segreti.|
|/labs/users/secrets/write|Aggiunge o modifica segreti.|
|/labs/users/environments/delete|Elimina ambienti.|
|/labs/users/environments/read|Esegue la lettura di ambienti.|
|/labs/users/environments/write|Aggiunge o modifica ambienti.|
|/labs/users/disks/delete|Elimina dischi.|
|/labs/users/disks/read|Esegue la lettura di dischi.|
|/labs/users/disks/write|Aggiunge o modifica dischi.|
|/labs/users/disks/Attach/action|Collegare e creare il lease del disco alla macchina virtuale.|
|/labs/users/disks/Detach/action|Rimuove il disco collegato alla macchina virtuale e interrompe il lease.|
|/labs/customImages/delete|Elimina immagini personalizzate.|
|/labs/customImages/read|Esegue la lettura di immagini personalizzate.|
|/labs/customImages/write|Aggiunge o modifica immagini personalizzate.|
|/labs/serviceRunners/delete|Elimina strumenti di esecuzione servizio.|
|/labs/serviceRunners/read|Esegue la lettura di strumenti di esecuzione servizio.|
|/labs/serviceRunners/write|Aggiunge o modifica strumenti di esecuzione servizio.|
|/labs/artifactSources/delete|Elimina origini elemento.|
|/labs/artifactSources/read|Esegue la lettura di origini elemento.|
|/labs/artifactSources/write|Aggiunge o modifica origini elemento.|
|/labs/artifactSources/artifacts/read|Esegue la lettura di elementi.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Genera un modello ARM per l'elemento specificato, carica i file necessari in un account di archiviazione e convalida l'elemento generato.|
|/labs/artifactSources/armTemplates/read|Esegue la lettura di modelli di Azure Resource Manager.|
|/labs/costs/read|Esegue la lettura di costi.|
|/labs/costs/write|Aggiunge o modifica costi.|
|/labs/virtualNetworks/delete|Elimina reti virtuali.|
|/labs/virtualNetworks/read|Esegue la lettura di reti virtuali.|
|/labs/virtualNetworks/write|Aggiunge o modifica reti virtuali.|
|/labs/formulas/delete|Elimina le formule.|
|/labs/formulas/read|Esegue la lettura di formule.|
|/labs/formulas/write|Aggiunge o modifica le formule.|
|/labs/schedules/delete|Elimina le pianificazioni.|
|/labs/schedules/read|Esegue la lettura delle pianificazioni.|
|/labs/schedules/write|Aggiunge o modifica pianificazioni.|
|/labs/schedules/Execute/action|Esegue una pianificazione.|
|/labs/schedules/ListApplicable/action|Elenca tutte le pianificazioni applicabili|
|/labs/galleryImages/read|Esegue la lettura delle immagini della raccolta.|
|/labs/policySets/EvaluatePolicies/action|Valuta i criteri del lab.|
|/labs/policySets/policies/delete|Elimina i criteri.|
|/labs/policySets/policies/read|Esegue la lettura di criteri.|
|/labs/policySets/policies/write|Aggiunge o modifica i criteri.|
|/labs/virtualMachines/delete|Elimina macchine virtuali.|
|/labs/virtualMachines/read|Esegue la lettura di macchine virtuali.|
|/labs/virtualMachines/write|Aggiunge o modifica macchine virtuali.|
|/labs/virtualMachines/Start/action|Avvia una macchina virtuale.|
|/labs/virtualMachines/Stop/action|Arrestare una macchina virtuale|
|/labs/virtualMachines/ApplyArtifacts/action|Applica elementi a una macchina virtuale.|
|/labs/virtualMachines/AddDataDisk/action|Collega un disco dati nuovo o esistente alla macchina virtuale.|
|/labs/virtualMachines/DetachDataDisk/action|Rimuove il disco specificato dalla macchina virtuale.|
|/labs/virtualMachines/Claim/action|Consente di assumere la proprietà di una macchina virtuale esistente|
|/labs/virtualMachines/ListApplicableSchedules/action|Elenca tutte le pianificazioni applicabili|
|/labs/virtualMachines/schedules/delete|Elimina le pianificazioni.|
|/labs/virtualMachines/schedules/read|Esegue la lettura delle pianificazioni.|
|/labs/virtualMachines/schedules/write|Aggiunge o modifica pianificazioni.|
|/labs/virtualMachines/schedules/Execute/action|Esegue una pianificazione.|
|/labs/notificationChannels/delete|Elimina canali di notifica.|
|/labs/notificationChannels/read|Esegue la lettura di canali di notifica.|
|/labs/notificationChannels/write|Aggiunge o modifica canali di notifica.|
|/labs/notificationChannels/Notify/action|Invia la notifica al canale specificato.|
|/schedules/delete|Elimina le pianificazioni.|
|/schedules/read|Esegue la lettura delle pianificazioni.|
|/schedules/write|Aggiunge o modifica pianificazioni.|
|/schedules/Execute/action|Esegue una pianificazione.|
|/schedules/Retarget/action|Aggiorna l'ID risorsa di destinazione di una pianificazione.|
|/locations/operations/read|Esegue la lettura delle operazioni.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Operazione | DESCRIZIONE |
|---|---|
|/databaseAccountNames/read|Controlla la disponibilità del nome.|
|/databaseAccounts/read|Esegue la lettura di un account di database.|
|/databaseAccounts/write|Aggiorna un account di database.|
|/databaseAccounts/listKeys/action|Elenca le chiavi di un account di database|
|/databaseAccounts/regenerateKey/action|Ruota le chiavi di un account di database|
|/databaseAccounts/listConnectionStrings/action|Ottiene le stringhe di connessione per un account di database|
|/databaseAccounts/changeResourceGroup/action|Modifica il gruppo di risorse di un account di database|
|/databaseAccounts/failoverPriorityChange/action|Modifica le priorità di failover di aree di un account di database. Usata per eseguire l'operazione di failover manuale|
|/databaseAccounts/delete|Elimina gli account di database.|
|/databaseAccounts/metricDefinitions/read|Esegue la lettura delle definizioni delle metriche degli account di database.|
|/databaseAccounts/metrics/read|Esegue la lettura della metrica degli account di database.|
|/databaseAccounts/usages/read|Esegue la lettura degli utilizzi degli account di database.|
|/databaseAccounts/databases/collections/metricDefinitions/read|Esegue la lettura delle definizioni delle metriche delle raccolte.|
|/databaseAccounts/databases/collections/metrics/read|Esegue la lettura della metrica delle raccolte.|
|/databaseAccounts/databases/collections/usages/read|Esegue la lettura degli utilizzi delle raccolte.|
|/databaseAccounts/databases/metricDefinitions/read|Esegue la lettura delle definizioni delle metriche dei database|
|/databaseAccounts/databases/metrics/read|Esegue la lettura della metrica dei database.|
|/databaseAccounts/databases/usages/read|Esegue la lettura degli utilizzi dei database.|
|/databaseAccounts/readonlykeys/read|Legge le chiavi di sola lettura degli account di database.|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Operazione | DESCRIZIONE |
|---|---|
|/generateSsoRequest/Action|Genera una richiesta per l'accesso al centro di controllo del dominio.|
|/validateDomainRegistrationInformation/Action|Convalida l'oggetto acquisto di dominio senza inviarlo|
|/checkDomainAvailability/Action|Controlla se un dominio è disponibile per l'acquisto|
|/listDomainRecommendations/Action|Recupera l'elenco di consigli sui domini in base alle parole chiave|
|/register/action|Registra il provider di risorse domini Microsoft per la sottoscrizione|
|/domains/Read|Ottiene l'elenco dei domini|
|/domains/Write|Aggiunge un nuovo dominio o ne aggiorna uno esistente|
|/domains/Delete|Elimina un dominio esistente.|
|/domains/operationresults/Read|Ottiene un'operazione di dominio|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Operazione | DESCRIZIONE |
|---|---|
|/lcsprojects/read|Visualizza i progetti dei servizi del ciclo di vita Microsoft Dynamics che appartengono a un utente|
|/lcsprojects/write|Crea e aggiorna i progetti dei servizi del ciclo di vita Microsoft Dynamics che appartengono all'utente. Solo le proprietà nome e descrizione possono essere aggiornate. La sottoscrizione e il percorso associati al progetto non possono essere aggiornati dopo la creazione|
|/lcsprojects/delete|Elimina i progetti dei servizi del ciclo di vita Microsoft Dynamics che appartengono all'utente|
|/lcsprojects/clouddeployments/read|Visualizza le distribuzioni di valutazione di Microsoft Dynamics AX 2012 R3 in un progetto dei servizi del ciclo di vita di Microsoft Dynamics che appartiene a un utente|
|/lcsprojects/clouddeployments/write|Crea una distribuzione di valutazione di Microsoft Dynamics AX 2012 R3 in un progetto dei servizi del ciclo di vita di Microsoft Dynamics che appartiene a un utente. Le distribuzioni possono essere gestite dal portale di gestione di Azure|
|/lcsprojects/connectors/read|Legge i connettori che appartengono al progetto dei servizi del ciclo di vita Microsoft Dynamics|
|/lcsprojects/connectors/write|Crea e aggiorna i connettori che appartengono al progetto dei servizi del ciclo di vita Microsoft Dynamics|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Operazione | DESCRIZIONE |
|---|---|
|/checkNameAvailability/action|Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata.|
|/register/action|Registra la sottoscrizione per il provider di risorse hub eventi e abilita la creazione di risorse hub eventi|
|/namespaces/write|Crea una risorsa spazio dei nomi e ne aggiorna le proprietà. Le proprietà che si possono aggiornare sono quelle relative ai tag e allo stato dello spazio dei nomi.|
|/namespaces/read|Ottiene l'elenco delle descrizioni delle risorse spazio dei nomi|
|/namespaces/Delete|Elimina una risorsa spazio dei nomi|
|/namespaces/metricDefinitions/read|Ottiene l'elenco di descrizioni delle risorse metrica dello spazio dei nomi|
|/namespaces/authorizationRules/read|Ottiene l'elenco di descrizioni delle regole di autorizzazione degli spazi dei nomi.|
|/namespaces/authorizationRules/write|Crea regole di autorizzazione a livello dello spazio dei nomi e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/namespaces/authorizationRules/delete|Elimina regola di autorizzazione dello spazio dei nomi. Non è possibile eliminare la regola di autorizzazione dello spazio dei nomi predefinita. |
|/namespaces/authorizationRules/listkeys/action|Ottiene la stringa di connessione per lo spazio dei nomi|
|/namespaces/authorizationRules/regenerateKeys/action|Rigenera la chiave primaria o secondaria per la risorsa|
|/namespaces/eventhubs/write|Crea o aggiorna le proprietà dell'hub eventi.|
|/namespaces/eventhubs/read|Ottiene l'elenco delle descrizioni delle risorse dell'hub eventi|
|/namespaces/eventhubs/Delete|Operazione per l'eliminazione di risorse hub eventi|
|/namespaces/eventHubs/consumergroups/write|Crea o aggiorna le proprietà del gruppo consumer.|
|/namespaces/eventHubs/consumergroups/read|Ottiene l'elenco delle descrizioni delle risorse gruppo consumer|
|/namespaces/eventHubs/consumergroups/Delete|Operazione per l'eliminazione di risorse gruppo consumer|
|/namespaces/eventhubs/authorizationRules/read| Ottiene l'elenco delle regole di autorizzazione dell'hub eventi|
|/namespaces/eventhubs/authorizationRules/write|Crea regole di autorizzazione dell'hub eventi e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/namespaces/eventhubs/authorizationRules/delete|Operazione per l'eliminazione di regole di autorizzazione dell'hub eventi|
|/namespaces/eventhubs/authorizationRules/listkeys/action|Ottiene la stringa di connessione per l'hub eventi|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|Rigenera la chiave primaria o secondaria per la risorsa|
|/namespaces/diagnosticSettings/read|Ottiene l'elenco di descrizioni delle risorse impostazioni di diagnostica dello spazio dei nomi|
|/namespaces/diagnosticSettings/write|Ottiene l'elenco di descrizioni delle risorse impostazioni di diagnostica dello spazio dei nomi|
|/namespaces/logDefinitions/read|Ottiene l'elenco di descrizioni delle risorse log dello spazio dei nomi|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Operazione | DESCRIZIONE |
|---|---|
|/providers/features/read|Ottiene la funzionalità di una sottoscrizione in un provider di risorse specificato.|
|/providers/features/register/action|Registra la funzionalità per una sottoscrizione in un provider di risorse specificato.|
|/features/read|Ottiene le funzionalità di una sottoscrizione.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Operazione | DESCRIZIONE |
|---|---|
|/clusters/write|Crea o aggiorna un cluster HDInsight|
|/clusters/read|Ottiene informazioni sul cluster HDInsight|
|/clusters/delete|Elimina un cluster HDInsight|
|/clusters/changerdpsetting/action|Modifica l'impostazione RDP per il cluster HDInsight|
|/clusters/configurations/action|Aggiorna la configurazione del cluster HDInsight|
|/clusters/configurations/read|Ottiene le configurazioni del cluster HDInsight|
|/clusters/roles/resize/action|Ridimensiona un cluster HDInsight|
|/locations/capabilities/read|Ottiene le funzionalità di sottoscrizione|
|/locations/checkNameAvailability/read|Controlla la disponibilità del nome|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Registra la sottoscrizione per il provider di risorse importazione/esportazione e consente la creazione di processi di importazione/esportazione.|
|/jobs/write|Crea un processo con i parametri specificati o aggiorna le proprietà o i tag per il processo specificato.|
|/jobs/read|Ottiene le proprietà per il processo specificato o restituisce l'elenco dei processi.|
|/jobs/listBitLockerKeys/action|Ottiene le chiavi BitLocker per il processo specificato.|
|/jobs/delete|Elimina il processo esistente.|
|/locations/read|Ottiene le proprietà per il percorso specificato o restituisce l'elenco dei percorsi.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Operazione | DESCRIZIONE |
|---|---|
|/Register/Action|Registra il provider Microsoft Insights|
|/AlertRules/Write|Scrittura in una configurazione di regola di avviso|
|/AlertRules/Delete|Eliminazione di una configurazione di regola di avviso|
|/AlertRules/Read|Lettura di una configurazione di regola di avviso|
|/AlertRules/Activated/Action|Regola di avviso attivata|
|/AlertRules/Resolved/Action|Regola di avviso risolta|
|/AlertRules/Throttled/Action|La regola di avviso è limitata|
|/AlertRules/Incidents/Read|Lettura di una configurazione di eventi imprevisti associati alla regola di avviso|
|/MetricDefinitions/Read|Consente di leggere le definizioni della metrica|
|/eventtypes/values/Read|Consente di leggere i valori dei tipi di evento di gestione|
|/eventtypes/digestevents/Read|Consente di leggere il digest dei tipi di evento di gestione|
|/Metrics/Read|Esegue la lettura delle metriche|
|/LogProfiles/Write|Scrittura in una configurazione di profilo di log|
|/LogProfiles/Delete|Elimina configurazione dei profili di log|
|/LogProfiles/Read|Lettura profili di log|
|/AutoscaleSettings/Write|Scrittura in una configurazione di impostazione di scalabilità automatica|
|/AutoscaleSettings/Delete|Eliminazione di una configurazione di impostazione di scalabilità automatica|
|/AutoscaleSettings/Read|Lettura di una configurazione di impostazione di scalabilità automatica|
|/AutoscaleSettings/Scaleup/Action|Scalabilità automatica: operazione di aumento|
|/AutoscaleSettings/Scaledown/Action|Scalabilità automatica: operazione di riduzione|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|Consente di leggere le definizioni della metrica|
|/ActivityLogAlerts/Activated/Action|L'avviso del log attività è stato attivato|
|/DiagnosticSettings/Write|È in corso la scrittura in una configurazione di impostazioni di diagnostica|
|/DiagnosticSettings/Delete|È in corso l'eliminazione di una configurazione di impostazioni di diagnostica|
|/DiagnosticSettings/Read|È in corso la lettura di una configurazione di impostazioni di diagnostica|
|/LogDefinitions/Read|Consente di leggere le definizioni del log|
|/ExtendedDiagnosticSettings/Write|È in corso la scrittura in una configurazione di impostazioni di diagnostica estese|
|/ExtendedDiagnosticSettings/Delete|È in corso l'eliminazione di una configurazione di impostazioni di diagnostica estese|
|/ExtendedDiagnosticSettings/Read|È in corso la lettura di una configurazione di impostazioni di diagnostica estese|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Registra una sottoscrizione|
|/checkNameAvailability/read|Controlla che il nome dell'insieme di credenziali delle chiavi sia valido e che non sia in uso|
|/vaults/read|Visualizza le proprietà di un insieme di credenziali delle chiavi|
|/vaults/write|Crea un nuovo insieme di credenziali delle chiavi o aggiorna le proprietà di un insieme di credenziali delle chiavi esistente|
|/vaults/delete|Elimina un insieme di credenziali delle chiavi|
|/vaults/deploy/action|Consente l'accesso ai segreti in un insieme di credenziali delle chiavi durante la distribuzione di risorse di Azure|
|/vaults/secrets/read|Visualizza le proprietà di un segreto ma non il relativo valore|
|/vaults/secrets/write|Crea un nuovo segreto o aggiorna il valore di un segreto esistente|
|/vaults/accessPolicies/write|Aggiorna un criterio di accesso esistente unendo o sostituendo oppure aggiungendo un nuovo criterio di accesso a un insieme di credenziali.|
|/deletedVaults/read|Visualizza le proprietà di Key Vault eliminati temporaneamente|
|/locations/operationResults/read|Controlla il risultato di un'operazione a esecuzione prolungata|
|/locations/deletedVaults/read|Visualizza le proprietà di un Key Vault eliminato temporaneamente|
|/locations/deletedVaults/purge/action|Ripulisce un Key Vault eliminato temporaneamente|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Operazione | DESCRIZIONE |
|---|---|
|/workflows/read|Esegue la lettura del flusso di lavoro.|
|/workflows/write|Crea un flusso di lavoro o ne aggiorna uno esistente.|
|/workflows/delete|Elimina il flusso di lavoro.|
|/workflows/run/action|Avvia l'esecuzione di un flusso di lavoro.|
|/workflows/disable/action|Disabilita il flusso di lavoro.|
|/workflows/enable/action|Abilita il flusso di lavoro.|
|/workflows/validate/action|Convalida il flusso di lavoro.|
|/workflows/move/action|Sposta il flusso di lavoro dall'ID sottoscrizione, dal gruppo di risorse e/o dal nome esistente a un ID sottoscrizione, a un gruppo di risorse e/o a un nome diverso.|
|/workflows/listSwagger/action|Ottiene le definizioni di Swagger del flusso di lavoro.|
|/workflows/regenerateAccessKey/action|Rigenera i segreti della chiave di accesso.|
|/workflows/listCallbackUrl/action|Ottiene l'URL di callback per il flusso di lavoro.|
|/workflows/versions/read|Esegue la lettura della versione del flusso di lavoro.|
|/workflows/versions/triggers/listCallbackUrl/action|Ottiene l'URL di callback per il trigger.|
|/workflows/runs/read|Esegue la lettura dell'esecuzione del flusso di lavoro.|
|/workflows/runs/cancel/action|Annulla l'esecuzione di un flusso di lavoro.|
|/workflows/runs/actions/read|Esegue la lettura dell'azione di esecuzione del flusso di lavoro.|
|/workflows/runs/operations/read|Esegue la lettura dello stato dell'operazione di esecuzione del flusso di lavoro.|
|/workflows/triggers/read|Esegue la lettura del trigger.|
|/workflows/triggers/run/action|Esegue il trigger.|
|/workflows/triggers/listCallbackUrl/action|Ottiene l'URL di callback per il trigger.|
|/workflows/triggers/histories/read|Esegue la lettura delle cronologie del trigger.|
|/workflows/triggers/histories/resubmit/action|Invia di nuovo il trigger del flusso di lavoro.|
|/workflows/accessKeys/read|Esegue la lettura della chiave di accesso.|
|/workflows/accessKeys/write|Crea una chiave di accesso o ne aggiorna una esistente.|
|/workflows/accessKeys/delete|Elimina la chiave di accesso.|
|/workflows/accessKeys/list/action|Elenca i segreti della chiave di accesso.|
|/workflows/accessKeys/regenerate/action|Rigenera i segreti della chiave di accesso.|
|/locations/workflows/validate/action|Convalida il flusso di lavoro.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Registra la sottoscrizione per il provider di risorse del servizio Web di Machine Learning e consente la creazione di servizi Web.|
|/webServices/action|Creare proprietà del servizio Web regionali per le aree supportate|
|/commitmentPlans/read|Esegue la lettura di qualsiasi piano di impegno di Machine Learning|
|/commitmentPlans/write|Crea o aggiorna un qualsiasi piano di impegno di Machine Learning|
|/commitmentPlans/delete|Elimina un qualsiasi piano di impegno di Machine Learning|
|/commitmentPlans/join/action|Consente di partecipare a qualsiasi piano di impegno di Machine Learning|
|/commitmentPlans/commitmentAssociations/read|Esegue la lettura di qualsiasi associazione a un piano di impegno di Machine Learning|
|/commitmentPlans/commitmentAssociations/move/action|Sposta qualsiasi associazione a un piano di impegno di Machine Learning|
|/Workspaces/read|Esegue la lettura di una qualsiasi area di lavoro di Machine Learning|
|/Workspaces/write|Crea o aggiorna una qualsiasi area di lavoro di Machine Learning|
|/Workspaces/delete|Elimina una qualsiasi area di lavoro di Machine Learning|
|/Workspaces/listworkspacekeys/action|Elenca le chiavi per un'area di lavoro di Machine Learning|
|/Workspaces/resyncstoragekeys/action|Risincronizza le chiavi dell'account di archiviazione configurato per un'area di lavoro di Machine Learning|
|/webServices/read|Legge qualsiasi servizio Web di Machine Learning.|
|/webServices/write|Crea o aggiorna qualsiasi servizio Web di Machine Learning.|
|/webServices/delete|Elimina qualsiasi servizio Web di Machine Learning.|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Operazione | DESCRIZIONE |
|---|---|
|/agreements/offers/plans/read|Restituisce un contratto per uno specifico elemento del marketplace.|
|/agreements/offers/plans/sign/action|Firma un contratto per uno specifico elemento del marketplace.|
|/agreements/offers/plans/cancel/action|Elimina un contratto per uno specifico elemento del marketplace.|

## <a name="microsoftmedia"></a>Microsoft.Media

| Operazione | DESCRIZIONE |
|---|---|
|/mediaservices/read||
|/mediaservices/write||
|/mediaservices/delete||
|/mediaservices/regenerateKey/action||
|/mediaservices/listKeys/action||
|/mediaservices/syncStorageKeys/action||

## <a name="microsoftnetwork"></a>Microsoft.Network

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Registra la sottoscrizione.|
|/unregister/action|Annulla la registrazione della sottoscrizione.|
|/checkTrafficManagerNameAvailability/action|Controlla la disponibilità di un nome DNS relativo di gestione traffico.|
|/dnszones/read|Ottiene la zona DNS in formato Json. Le proprietà di zona includono tag, etag, numberOfRecordSets e maxNumberOfRecordSets. Si tenga presente che questo comando non recupera il set di record contenuti all'interno della zona.|
|/dnszones/write|Crea o aggiorna una zona DNS in un gruppo di risorse.  Consente di aggiornare i tag in una risorsa di zona DNS. Si tenga presente che questo comando non può essere utilizzato per creare o aggiornare i set di record all'interno della zona.|
|/dnszones/delete|Elimina la zona DNS in formato Json. Le proprietà di zona includono tag, etag, numberOfRecordSets e maxNumberOfRecordSets.|
|/dnszones/MX/read|Ottiene il set di record di tipo "MX" in formato JSON. Il set di record contiene un elenco di record, oltre a TTL, tag ed etag.|
|/dnszones/MX/write|Crea o aggiorna un set di record di tipo "MX" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record.|
|/dnszones/MX/delete|Rimuove il set di record di un determinato nome e tipo "MX" da una zona DNS.|
|/dnszones/NS/read|Ottiene i set di record DNS di tipo NS.|
|/dnszones/NS/write|Crea o aggiorna i set di record DNS di tipo NS.|
|/dnszones/NS/delete|Elimina i set di record DNS di tipo NS.|
|/dnszones/AAAA/read|Ottiene il set di record di tipo "AAAA" in formato JSON. Il set di record contiene un elenco di record, nonché la durata (TTL), tag ed etag.|
|/dnszones/AAAA/write|Crea o aggiorna un set di record di tipo "AAAA" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record.|
|/dnszones/AAAA/delete|Rimuove il set di record di un determinato nome e tipo "AAAA" da una zona DNS.|
|/dnszones/CNAME/read|Ottiene il set di record di tipo "CNAME" in formato JSON. Il set di record contiene durata (TTL), tag ed etag.|
|/dnszones/CNAME/write|Crea o aggiorna un set di record di tipo "CNAME" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record.|
|/dnszones/CNAME/delete|Rimuove il set di record di un determinato nome e tipo "CNAME" da una zona DNS.|
|/dnszones/SOA/read|Ottiene i set di record DNS di tipo SOA.|
|/dnszones/SOA/write|Crea o aggiorna i set di record DNS di tipo SOA.|
|/dnszones/SRV/read|Ottiene il set di record di tipo "SRV" in formato JSON. Il set di record contiene un elenco di record, oltre a TTL, tag ed etag.|
|/dnszones/SRV/write|Crea o aggiorna il set di record di tipo SRV.|
|/dnszones/SRV/delete|Rimuove il set di record di un determinato nome e tipo "SRV" da una zona DNS.|
|/dnszones/PTR/read|Ottiene il set di record di tipo "PTR" in formato JSON. Il set di record contiene un elenco di record, oltre a TTL, tag ed etag.|
|/dnszones/PTR/write|Crea o aggiorna un set di record di tipo "PTR" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record.|
|/dnszones/PTR/delete|Rimuove il set di record di un determinato nome e tipo "PTR" da una zona DNS.|
|/dnszones/A/read|Ottiene il set di record di tipo "A" in formato JSON. Il set di record contiene un elenco di record, oltre a TTL, tag ed etag.|
|/dnszones/A/write|Crea o aggiorna un set di record di tipo "A" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record.|
|/dnszones/A/delete|Rimuove il set di record di un determinato nome e tipo "A" da una zona DNS.|
|/dnszones/TXT/read|Ottiene il set di record di tipo ‘TXT’, in formato JSON. Il set di record contiene un elenco di record, oltre a TTL, tag ed etag.|
|/dnszones/TXT/write|Crea o aggiorna un set di record di tipo ‘TXT’ all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record.|
|/dnszones/TXT/delete|Rimuove il set di record di un determinato nome e tipo ‘TXT’ da una zona DNS.|
|/dnszones/recordsets/read|Ottiene i set di record DNS tra i tipi|
|/networkInterfaces/read|Ottiene una definizione dell’interfaccia di rete. |
|/networkInterfaces/write|Crea un'interfaccia di rete o ne aggiorna una esistente. |
|/networkInterfaces/join/action|Aggiunge una macchina virtuale a un'interfaccia di rete|
|/networkInterfaces/delete|Elimina un'interfaccia di rete|
|/networkInterfaces/effectiveRouteTable/action|Ottiene la tabella di instradamento configurata sull’interfaccia di rete della macchina virtuale|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|Ottiene i gruppi di sicurezza di rete configurati sull’interfaccia di rete della macchina virtuale|
|/networkInterfaces/loadBalancers/read|Ottiene tutti i servizi di bilanciamento del carico ai quali appartiene l’interfaccia di rete|
|/networkInterfaces/ipconfigurations/read|Ottiene la definizione della configurazione IP dell’interfaccia di rete. |
|/publicIPAddresses/read|Ottiene una definizione dell’indirizzo IP pubblico.|
|/publicIPAddresses/write|Crea un indirizzo IP pubblico o ne aggiorna uno esistente. |
|/publicIPAddresses/delete|Elimina un indirizzo IP pubblico.|
|/publicIPAddresses/join/action|Aggiunge un indirizzo IP pubblico|
|/routeFilters/read|Ottiene una definizione del filtro di instradamento|
|/routeFilters/join/action|Aggiunge un filtro di instradamento|
|/routeFilters/delete|Elimina una definizione del filtro di instradamento|
|/routeFilters/write|Crea un filtro di instradamento o ne aggiorna uno esistente|
|/routeFilters/rules/read|Ottiene una definizione della regola del filtro di instradamento|
|/routeFilters/rules/write|Crea una regola del filtro di instradamento o ne aggiorna una esistente|
|/routeFilters/rules/delete|Elimina una definizione della regola del filtro di instradamento|
|/networkWatchers/read|Ottiene la definizione del Network Watcher|
|/networkWatchers/write|Crea un Network Watcher o ne aggiorna uno esistente|
|/networkWatchers/delete|Elimina un Network Watcher|
|/networkWatchers/configureFlowLog/action|Configura la registrazione del flusso per una risorsa di destinazione.|
|/networkWatchers/ipFlowVerify/action|Indica se il pacchetto è autorizzato o meno su o da una specifica destinazione.|
|/networkWatchers/nextHop/action|Per un indirizzo IP destinazione specificato, restituisce il tipo di hop successivo e l’indirizzo IP dell’hop successivo.|
|/networkWatchers/queryFlowLogStatus/action|Ottiene lo stato della registrazione del flusso su una risorsa.|
|/networkWatchers/queryTroubleshootResult/action|Ottiene il risultato della risoluzione dei problemi dall’esecuzione precedente o dall’operazione di risoluzione dei problemi attualmente in corso.|
|/networkWatchers/securityGroupView/action|Visualizza le regole del gruppo di sicurezza di rete configurate e in vigore applicate a una macchina virtuale.|
|/networkWatchers/topology/action|Ottiene una vista a livello di rete delle risorse e le relative relazioni in un gruppo di risorse.|
|/networkWatchers/troubleshoot/action|Avvia la risoluzione dei problemi su una risorsa di rete in Azure.|
|/networkWatchers/packetCaptures/queryStatus/action|Ottiene informazioni sulle proprietà e sullo stato di una risorsa di acquisizione dei pacchetti.|
|/networkWatchers/packetCaptures/stop/action|Arresta la sessione di acquisizione dei pacchetti in corso.|
|/networkWatchers/packetCaptures/read|Ottiene la definizione di acquisizione dei pacchetti|
|/networkWatchers/packetCaptures/write|Crea un'acquisizione di pacchetti|
|/networkWatchers/packetCaptures/delete|Elimina un'acquisizione di pacchetti|
|/loadBalancers/read|Ottiene una definizione del servizio di bilanciamento del carico|
|/loadBalancers/write|Crea un servizio di bilanciamento del carico o ne aggiorna uno esistente|
|/loadBalancers/delete|Elimina un servizio di bilanciamento del carico|
|/loadBalancers/networkInterfaces/read|Ottiene i riferimenti a tutte le interfacce di rete sotto a un servizio di bilanciamento del carico|
|/loadBalancers/loadBalancingRules/read|Ottiene una definizione di regola di bilanciamento del carico per il servizio di bilanciamento del carico|
|/loadBalancers/backendAddressPools/read|Ottiene una definizione del pool di indirizzi di back-end del servizio di bilanciamento del carico|
|/loadBalancers/backendAddressPools/join/action|Aggiunge un pool di indirizzi di back-end del servizio di bilanciamento del carico|
|/loadBalancers/inboundNatPools/read|Ottiene una definizione del pool NAT in entrata del servizio di bilanciamento del carico|
|/loadBalancers/inboundNatPools/join/action|Aggiunge un pool NAT in entrata del servizio di bilanciamento del carico|
|/loadBalancers/inboundNatRules/read|Ottiene una definizione di regola NAT in entrata del servizio di bilanciamento del carico|
|/loadBalancers/inboundNatRules/write|Crea una regola NAT in entrata del servizio di bilanciamento del carico o aggiorna una regola NAT in entrata di bilanciamento del carico esistente|
|/loadBalancers/inboundNatRules/delete|Elimina una regola NAT in entrata del servizio di bilanciamento del carico|
|/loadBalancers/inboundNatRules/join/action|Aggiunge una regola NAT in entrata del servizio di bilanciamento del carico|
|/loadBalancers/outboundNatRules/read|Ottiene una definizione della regola NAT in uscita del servizio di bilanciamento del carico|
|/loadBalancers/probes/read|Ottiene un probe del servizio di bilanciamento del carico|
|/loadBalancers/virtualMachines/read|Ottiene i riferimenti a tutte le macchine virtuali sotto a un servizio di bilanciamento del carico|
|/loadBalancers/frontendIPConfigurations/read|Ottiene una definizione della configurazione IP del front-end del servizio di bilanciamento del carico|
|/trafficManagerGeographicHierarchies/read|Ottiene la gerarchia geografica di Gestione traffico contenente le aree che possono essere utilizzate con il metodo di routing del traffico geografico|
|/bgpServiceCommunities/read|Ottiene le community del servizio BGP|
|/applicationGatewayAvailableWafRuleSets/read|Ottiene i set di regole WAF disponibili sul gateway applicazione|
|/virtualNetworks/read|Ottiene la definizione della rete virtuale|
|/virtualNetworks/write|Crea una rete virtuale o ne aggiorna una esistente|
|/virtualNetworks/delete|Elimina una rete virtuale|
|/virtualNetworks/peer/action|Associa una rete virtuale a un'altra rete virtuale|
|/virtualNetworks/virtualNetworkPeerings/read|Ottiene una definizione di peering della rete virtuale|
|/virtualNetworks/virtualNetworkPeerings/write|Crea un peering della rete virtuale o ne aggiorna uno esistente|
|/virtualNetworks/virtualNetworkPeerings/delete|Elimina un peering della rete virtuale|
|/virtualNetworks/subnets/read|Ottiene una definizione di subnet della rete virtuale|
|/virtualNetworks/subnets/write|Crea una subnet della rete virtuale o ne aggiorna una esistente|
|/virtualNetworks/subnets/delete|Elimina una subnet della rete virtuale|
|/virtualNetworks/subnets/join/action|Aggiunge una rete virtuale|
|/virtualNetworks/subnets/joinViaServiceTunnel/action|Aggiunge una risorsa come account di archiviazione o database SQL a una subnet abilitata al tunneling del servizio.|
|/virtualNetworks/subnets/virtualMachines/read|Ottiene i riferimenti a tutte le macchine virtuali nella subnet della rete virtuale|
|/virtualNetworks/checkIpAddressAvailability/read|Verifica se l’indirizzo IP è disponibile presso la rete virtuale specificata|
|/virtualNetworks/virtualMachines/read|Ottiene i riferimenti a tutte le macchine virtuali in una rete virtuale|
|/expressRouteServiceProviders/read|Ottiene i provider del servizio Express Route|
|/dnsoperationresults/read|Ottiene i risultati di un'operazione DNS|
|/localnetworkgateways/read|Ottiene il LocalNetworkGateway|
|/localnetworkgateways/write|Crea un LocalNetworkGateway o ne aggiorna uno esistente|
|/localnetworkgateways/delete|Elimina il LocalNetworkGateway|
|/trafficManagerProfiles/read|Ottiene la configurazione del profilo di Gestione traffico. Include le impostazioni DNS, di routing del traffico, di monitoraggio dell'endpoint e un elenco degli endpoint instradati da questo profilo di Gestione traffico.|
|/trafficManagerProfiles/write|Crea un profilo di Gestione traffico o modifica la configurazione di un profilo di Gestione traffico esistente. Questo include l’abilitazione o la disabilitazione di un profilo e la modifica delle impostazioni di DNS, di instradamento del traffico o di monitoraggio dell’endpoint. Gli endpoint instradati dal profilo di Gestione traffico possono essere aggiunti, rimossi, abilitati o disabilitati.|
|/trafficManagerProfiles/delete|Elimina il profilo di Gestione traffico. Tutte le impostazioni associate al profilo di Gestione traffico andranno perse e il profilo non può più essere utilizzato per instradare il traffico.|
|/dnsoperationstatuses/read|Ottiene lo stato di un’operazione DNS |
|/operations/read|Ottiene le operazioni disponibili|
|/expressRouteCircuits/read|Ottiene un ExpressRouteCircuit|
|/expressRouteCircuits/write|Crea un ExpressRouteCircuit o ne aggiorna uno esistente|
|/expressRouteCircuits/delete|Elimina un ExpressRouteCircuit|
|/expressRouteCircuits/stats/read|Ottiene uno Stat di ExpressRouteCircuit|
|/expressRouteCircuits/peerings/read|Ottiene il peering di ExpressRouteCircuit|
|/expressRouteCircuits/peerings/write|Crea il peering di ExpressRouteCircuit o ne aggiorna uno esistente|
|/expressRouteCircuits/peerings/delete|Elimina il peering di ExpressRouteCircuit|
|/expressRouteCircuits/peerings/arpTables/action|Ottiene una ArpTable di peering di ExpressRouteCircuit|
|/expressRouteCircuits/peerings/routeTables/action|Ottiene una RouteTable di peering di ExpressRouteCircuit|
|/expressRouteCircuits/peerings/<br>routeTablesSummary/action|Ottiene il riepilogo di una RouteTable di peering di ExpressRouteCircuit|
|/expressRouteCircuits/peerings/stats/read|Ottiene lo Stat di peering di ExpressRouteCircuit|
|/expressRouteCircuits/authorizations/read|Ottiene un’autorizzazione di ExpressRouteCircuit|
|/expressRouteCircuits/authorizations/write|Crea un'autorizzazione di circuito Express Route o ne aggiorna una esistente|
|/expressRouteCircuits/authorizations/delete|Elimina un’autorizzazione di circuito Express Route|
|/connections/read|Ottiene una connessione di gateway di rete virtuale|
|/connections/write|Crea una connessione del gateway di rete virtuale o ne aggiorna una esistente|
|/connections/delete|Elimina una connessione di gateway di rete virtuale|
|/connections/sharedKey/read|Ottiene una connessione di gateway di rete virtuale SharedKey|
|/connections/sharedKey/write|Crea una connessione del gateway di rete virtuale SharedKey o ne aggiorna una esistente|
|/networkSecurityGroups/read|Ottiene una definizione del gruppo di sicurezza di rete|
|/networkSecurityGroups/write|Crea un gruppo di sicurezza di rete o ne aggiorna uno esistente|
|/networkSecurityGroups/delete|Elimina un gruppo di sicurezza di rete|
|/networkSecurityGroups/join/action|Aggiunge un gruppo di sicurezza di rete|
|/networkSecurityGroups/defaultSecurityRules/read|Ottiene una definizione delle regole di sicurezza predefinita|
|/networkSecurityGroups/securityRules/read|Ottiene una definizione delle regole di sicurezza|
|/networkSecurityGroups/securityRules/write|Crea una regola di sicurezza o ne aggiorna una esistente|
|/networkSecurityGroups/securityRules/delete|Elimina una regola di sicurezza|
|/applicationGateways/read|Ottiene un gateway applicazione|
|/applicationGateways/write|Crea un gateway applicazione o ne aggiorna uno esistente|
|/applicationGateways/delete|Elimina un gateway applicazione|
|/applicationGateways/backendhealth/action|Ottiene l'integrità back-end del gateway applicazione|
|/applicationGateways/start/action|Avvia un gateway applicazione|
|/applicationGateways/stop/action|Arresta un gateway applicazione|
|/applicationGateways/backendAddressPools/join/action|Aggiunge un pool di indirizzi back-end del gateway applicazione|
|/routeTables/read|Ottiene una definizione della tabella di route|
|/routeTables/write|Crea una tabella di route o ne aggiorna una esistente|
|/routeTables/delete|Elimina una definizione della tabella di route|
|/routeTables/join/action|Aggiunge una tabella di route|
|/routeTables/routes/read|Ottiene una definizione di route|
|/routeTables/routes/write|Crea una route o ne aggiorna una esistente|
|/routeTables/routes/delete|Elimina una definizione di route|
|/locations/operationResults/read|Ottiene il risultato di un'operazione POST o DELETE asincrona|
|/locations/checkDnsNameAvailability/read|Controlla se l'etichetta DNS è disponibile nel percorso specificato|
|/locations/usages/read|Ottiene la metrica di utilizzo delle risorse|
|/locations/operations/read|Ottiene una risorsa per l'operazione che rappresenta lo stato di un'operazione asincrona|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Registra la sottoscrizione per il provider di risorse hub di notifica e abilita la creazione di spazi dei nomi e hub di notifica|
|/CheckNamespaceAvailability/action|Verifica se il nome di risorsa di uno spazio dei nomi specificato è disponibile o meno all'interno del servizio Hub di notifica.|
|/Namespaces/write|Crea una risorsa spazio dei nomi e ne aggiorna le proprietà. Le proprietà che si possono aggiornare sono quelle relative ai tag e allo stato dello spazio dei nomi.|
|/Namespaces/read|Ottiene l'elenco delle descrizioni delle risorse spazio dei nomi|
|/Namespaces/Delete|Elimina la risorsa spazio dei nomi|
|/Namespaces/authorizationRules/action|Ottiene l'elenco di descrizioni delle regole di autorizzazione degli spazi dei nomi.|
|/Namespaces/CheckNotificationHubAvailability/action|Controlla se un determinato nome di hub di notifica è disponibile all'interno di uno spazio dei nomi.|
|/Namespaces/authorizationRules/write|Crea regole di autorizzazione a livello dello spazio dei nomi e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/Namespaces/authorizationRules/read|Ottiene l'elenco di descrizioni delle regole di autorizzazione degli spazi dei nomi.|
|/Namespaces/authorizationRules/delete|Elimina regola di autorizzazione dello spazio dei nomi. Non è possibile eliminare la regola di autorizzazione dello spazio dei nomi predefinita. |
|/Namespaces/authorizationRules/listkeys/action|Ottiene la stringa di connessione per lo spazio dei nomi|
|/Namespaces/authorizationRules/regenerateKeys/action|Regola di autorizzazione dello spazio dei nomi - Rigenera chiave primaria/secondaria. Specificare la chiave da rigenerare|
|/Namespaces/NotificationHubs/write|Crea un hub di notifica e ne aggiorna le proprietà. Le proprietà includono principalmente credenziali PNS, regole di autorizzazione e TTL|
|/Namespaces/NotificationHubs/read|Ottiene l’elenco di descrizioni dell’hub di notifica|
|/Namespaces/NotificationHubs/Delete|Elimina la risorsa nell’hub di notifica|
|/Namespaces/NotificationHubs/authorizationRules/action|Ottiene l'elenco di regole di autorizzazione dell’hub di notifica|
|/Namespaces/NotificationHubs/pnsCredentials/action|Ottiene tutte le credenziali PNS dell’hub di notifica. Queste includono le credenziali WNS, MPNS, APNS, GCM e Baidu|
|/Namespaces/NotificationHubs/debugSend/action|Invia una notifica push di prova.|
|/Namespaces/NotificationHubs/metricDefinitions/read|Ottiene l'elenco di descrizioni delle risorse di metrica dello spazio dei nomi|
|/Namespaces/NotificationHubs/<br>authorizationRules/write|Crea regole di autorizzazione dell'inoltro dell’hub di notifica e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/Namespaces/NotificationHubs/<br>authorizationRules/read|Ottiene l'elenco di regole di autorizzazione dell’hub di notifica|
|/Namespaces/NotificationHubs/<br>authorizationRules/delete|Elimina le regole di autorizzazione dell’hub di notifica|
|/Namespaces/NotificationHubs/<br>authorizationRules/listkeys/action|Ottiene la stringa di connessione all'hub di notifica|
|/Namespaces/NotificationHubs/<br>authorizationRules/regenerateKeys/action|La regola di autorizzazione dell’hub di notifica rigenera la chiave primaria/secondaria. Specificare la chiave da rigenerare|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Registra una sottoscrizione a un provider di risorse.|
|/linkTargets/read|Elenca gli account esistenti non associati a una sottoscrizione di Azure. Per collegare questa sottoscrizione di Azure a un’area di lavoro, utilizzare un ID cliente restituito da questa operazione nella proprietà ID cliente dell’operazione Crea area di lavoro.|
|/workspaces/write|Crea una nuova area di lavoro o collega a un'area di lavoro esistente fornendo l'ID cliente dall’area di lavoro esistente.|
|/workspaces/read|Ottiene un'area di lavoro esistente|
|/workspaces/delete|Elimina un'area di lavoro. Se l'area di lavoro era collegata a un’area di lavoro esistente al momento della creazione, l’area di lavoro alla quale era collegata non viene eliminata.|
|/workspaces/generateregistrationcertificate/action|Genera il certificato di registrazione per l’area di lavoro. Il certificato viene utilizzato per la connessione di Microsoft System Center Operations Manager all'area di lavoro.|
|/workspaces/sharedKeys/action|Recupera le chiavi condivise per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro.|
|/workspaces/search/action|Esegue una query di ricerca|
|/workspaces/datasources/read|Ottiene le origini dati in un'area di lavoro.|
|/workspaces/datasources/write|Crea/Aggiorna le origini dati in un'area di lavoro.|
|/workspaces/datasources/delete|Elimina le origini dati in un'area di lavoro.|
|/workspaces/managementGroups/read|Ottiene nomi e metadati per i gruppi di gestione di System Center Operations Manager connessi a questa area di lavoro.|
|/workspaces/schema/read|Ottiene lo schema di ricerca per l'area di lavoro.  Lo schema di ricerca include i campi esposti e i relativi tipi.|
|/workspaces/usages/read|Ottiene i dati di utilizzo di un'area di lavoro tra cui la quantità di dati letti dall'area di lavoro.|
|/workspaces/intelligencepacks/read|Elenca tutti gli Intelligence Pack visibili per una specifica area di lavoro, oltre a elencare se il pacchetto è abilitato o disabilitato per quell’area di lavoro.|
|/workspaces/intelligencepacks/enable/action|Abilita un Intelligence Pack per una specifica area di lavoro.|
|/workspaces/intelligencepacks/disable/action|Disabilita un Intelligence Pack per una specifica area di lavoro.|
|/workspaces/sharedKeys/read|Recupera le chiavi condivise per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro.|
|/workspaces/savedSearches/read|Ottiene una query di ricerca salvata|
|/workspaces/savedSearches/write|Crea una query di ricerca salvata|
|/workspaces/savedSearches/delete|Elimina una query di ricerca salvata|
|/workspaces/storageinsightconfigs/write|Crea una nuova configurazione di archiviazione. Tali configurazioni servono per estrarre i dati da una posizione in un account di archiviazione esistente.|
|/workspaces/storageinsightconfigs/read|Ottiene una configurazione di archiviazione.|
|/workspaces/storageinsightconfigs/delete|Elimina una configurazione di archiviazione. Questa operazione interrompe la lettura dei dati dall’account di archiviazione da parte di Microsoft Operational Insights.|
|/workspaces/configurationScopes/read|Ottiene l'ambito di configurazione|
|/workspaces/configurationScopes/write|Imposta l'ambito di configurazione|
|/workspaces/configurationScopes/delete|Elimina l'ambito di configurazione|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Registra una sottoscrizione a un provider di risorse.|
|/solutions/write|Crea una nuova soluzione OMS|
|/solutions/read|Ottiene soluzione OMS esistente|
|/solutions/delete|Elimina soluzione OMS esistente|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Operazione | DESCRIZIONE |
|---|---|
|/Vaults/backupJobsExport/action|Esporta processi|
|/Vaults/write|L'operazione Crea insieme di credenziali crea una risorsa di Azure di tipo 'vault'|
|/Vaults/read|L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault'|
|/Vaults/delete|L'operazione Elimina insieme di credenziali elimina la risorsa di Azure specificata di tipo 'vault'|
|/Vaults/refreshContainers/read|Aggiorna l'elenco di contenitori|
|/Vaults/backupJobsExport/operationResults/read|Restituisce il risultato dell'operazione di esportazione del processo.|
|/Vaults/backupOperationResults/read|Restituisce il risultato dell'operazione di backup di un insieme di credenziali di Servizi di ripristino.|
|/Vaults/monitoringAlerts/read|Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino.|
|/Vaults/monitoringAlerts/{uniqueAlertId}/read|Ottiene i dettagli dell'avviso.|
|/Vaults/backupSecurityPIN/read|Restituisce le informazioni sul PIN di sicurezza dell'insieme di credenziali di Servizi di ripristino.|
|/vaults/replicationEvents/read|Legge tutti gli eventi|
|/Vaults/backupProtectableItems/read|Restituisce l'elenco di tutti gli elementi da proteggere.|
|/vaults/replicationFabrics/read|Legge tutte le infrastrutture|
|/vaults/replicationFabrics/write|Crea o aggiorna tutte le infrastrutture|
|/vaults/replicationFabrics/remove/action|Rimuove l'infrastruttura|
|/vaults/replicationFabrics/checkConsistency/action|Verifica la coerenza dell'infrastruttura|
|/vaults/replicationFabrics/delete|Elimina tutte le infrastrutture|
|/vaults/replicationFabrics/renewcertificate/action||
|/vaults/replicationFabrics/deployProcessServerImage/action|Distribuisce immagine del server di elaborazione|
|/vaults/replicationFabrics/reassociateGateway/action|Riassocia gateway|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>read|Legge tutti i provider dei servizi di ripristino|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>remove/action|Rimuove tutti i provider dei servizi di ripristino|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>delete|Elimina tutti i provider dei servizi di ripristino|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>refreshProvider/action|Aggiorna i provider|
|/vaults/replicationFabrics/replicationStorageClassifications/read|Legge tutte le classificazioni di archiviazione|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/read|Legge tutti i mapping di classificazioni di archiviazione|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/write|Crea o aggiorna tutti i mapping di classificazioni di archiviazione|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/delete|Elimina tutti i mapping di classificazioni di archiviazione|
|/vaults/replicationFabrics/replicationvCenters/read|Legge tutti i processi|
|/vaults/replicationFabrics/replicationvCenters/write|Crea o aggiorna tutti i processi|
|/vaults/replicationFabrics/replicationvCenters/delete|Elimina tutti i processi|
|/vaults/replicationFabrics/replicationNetworks/read|Legge tutte le reti|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/read|Legge tutti i mapping di rete|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/write|Crea o aggiorna tutti i mapping di rete|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/delete|Elimina tutti i mapping di rete|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>read|Legge tutti i contenitori di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>discoverProtectableItem/action|Individua elemento da proteggere|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>write|Crea o aggiorna i contenitori di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>remove/action|Rimuove il contenitore di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>switchprotection/action|Cambia il contenitore di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectableItems/read|Legge gli elementi da proteggere|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/read|Legge i mapping dei contenitori di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/write|Crea o aggiorna i mapping dei contenitori di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/remove/action|Rimuove il mapping di contenitore di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/delete|Elimina i mapping dei contenitori di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/read|Legge tutti gli elementi protetti|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/write|Crea o aggiorna gli elementi protetti|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/delete|Elimina tutti gli elementi protetti|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/remove/action|Rimuove l'elemento protetto|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/plannedFailover/action|Failover pianificato|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/unplannedFailover/action|Failover|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailover/action|Failover di test|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailoverCleanup/action|Pulizia del failover di test|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/failoverCommit/action|Commit del failover|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/reProtect/action|Riprotegge l'elemento protetto|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/updateMobilityService/action|Aggiorna servizio Mobility|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/repairReplication/action|Ripristina replica|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/applyRecoveryPoint/action|Applica punto di ripristino|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/recoveryPoints/read|Legge i punti di ripristino di replica|
|/vaults/replicationPolicies/read|Legge tutti i criteri|
|/vaults/replicationPolicies/write|Crea o aggiorna i criteri|
|/vaults/replicationPolicies/delete|Elimina i criteri|
|/vaults/replicationRecoveryPlans/read|Legge i piani di ripristino|
|/vaults/replicationRecoveryPlans/write|Crea o aggiorna i piani di ripristino|
|/vaults/replicationRecoveryPlans/delete|Elimina i piani di ripristino|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Piano di ripristino del failover pianificato|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Piano di ripristino del failover|
|/vaults/replicationRecoveryPlans/testFailover/action|Piano di ripristino del failover di test|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Piano di ripristino della pulizia del failover di test|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Piano di ripristino del commit del failover|
|/vaults/replicationRecoveryPlans/reProtect/action|Piano di ripristino di riprotezione|
|/Vaults/extendedInformation/read|L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault?|
|/Vaults/extendedInformation/write|L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault?|
|/Vaults/extendedInformation/delete|L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault?|
|/Vaults/backupManagementMetaData/read|Restituisce i metadati di gestione di backup di un insieme di credenziali di Servizi di ripristino.|
|/Vaults/backupProtectionContainers/read|Restituisce tutti i contenitori che appartengono alla sottoscrizione|
|/Vaults/backupFabrics/operationResults/read|Restituisce lo stato dell'operazione|
|/Vaults/backupFabrics/protectionContainers/read|Restituisce tutti i contenitori registrati|
|/Vaults/backupFabrics/protectionContainers/<br>operationResults/read|Ottiene il risultato dell'operazione eseguita sul contenitore di protezione.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/read|Restituisce i dettagli dell'oggetto dell'elemento protetto|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/write|Crea un elemento protetto di backup|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/delete|Elimina l'elemento protetto|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/backup/action|Esegue il backup dell'elemento protetto.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationResults/read|Ottiene il risultato dell'operazione eseguita sugli elementi protetti.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationStatus/read|Restituisce lo stato dell'operazione eseguita sugli elementi protetti.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/read|Ottiene i punti di ripristino degli elementi protetti.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>restore/action|Ripristina i punti di ripristino degli elementi protetti.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>provisionInstantItemRecovery/action|Effettua il provisioning del ripristino elementi immediato per l'elemento protetto|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>revokeInstantItemRecovery/action|Revoca il ripristino elementi immediato per l'elemento protetto|
|/Vaults/usages/read|Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino.|
|/vaults/usages/read|Legge tutti gli utilizzi dell'insieme di credenziali|
|/Vaults/certificates/write|L'operazione Aggiorna certificato risorsa aggiorna il certificato delle credenziali per la risorsa e/o l'insieme di credenziali.|
|/Vaults/tokenInfo/read|Restituisce le informazioni sul token dell'insieme di credenziali di Servizi di ripristino.|
|/vaults/replicationAlertSettings/read|Legge tutte le impostazioni degli avvisi|
|/vaults/replicationAlertSettings/write|Crea o aggiorna le impostazioni degli avvisi|
|/Vaults/backupOperations/read|Restituisce lo stato dell'operazione di backup dell'insieme di credenziali di Servizi di ripristino.|
|/Vaults/storageConfig/read|Restituisce la configurazione di archiviazione dell'insieme di credenziali di Servizi di ripristino.|
|/Vaults/storageConfig/write|Aggiorna la configurazione di archiviazione dell'insieme di credenziali di Servizi di ripristino.|
|/Vaults/backupUsageSummaries/read|Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino.|
|/Vaults/backupProtectedItems/read|Restituisce l'elenco di tutti gli elementi protetti.|
|/Vaults/backupconfig/vaultconfig/read|Restituisce la configurazione dell'insieme di credenziali di Servizi di ripristino.|
|/Vaults/backupconfig/vaultconfig/write|Aggiorna la configurazione dell'insieme di credenziali di Servizi di ripristino.|
|/Vaults/registeredIdentities/write|L'operazione Registra contenitore di servizi può essere usata per registrare un contenitore con il servizio di ripristino.|
|/Vaults/registeredIdentities/read|L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa.|
|/Vaults/registeredIdentities/delete|L'operazione Annulla registrazione contenitore di servizi può essere usata per annullare la registrazione di un contenitore.|
|/Vaults/registeredIdentities/operationResults/read|L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono|
|/vaults/replicationJobs/read|Legge tutti i processi|
|/vaults/replicationJobs/cancel/action|Annulla processo|
|/vaults/replicationJobs/restart/action|Riavvia processo|
|/vaults/replicationJobs/resume/action|Riprende il processo|
|/Vaults/backupPolicies/read|Restituisce tutti i criteri di protezione|
|/Vaults/backupPolicies/write|Crea i criteri di protezione|
|/Vaults/backupPolicies/delete|Elimina i criteri di protezione|
|/Vaults/backupPolicies/operationResults/read|Ottiene i risultati dell'operazione sui criteri.|
|/Vaults/backupPolicies/operationStatus/read|Ottiene lo stato dell'operazione sui criteri.|
|/Vaults/vaultTokens/read|L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali.|
|/Vaults/monitoringConfigurations/notificationConfiguration/read|Ottiene la configurazione delle notifiche dell'insieme di credenziali dei servizi di ripristino.|
|/Vaults/backupJobs/read|Restituisce tutti gli oggetti processo|
|/Vaults/backupJobs/cancel/action|Annulla il processo|
|/Vaults/backupJobs/operationResults/read|Restituisce il risultato dell'operazione di processo.|
|/locations/allocateStamp/action|AllocateStamp è un'operazione interna usata dal servizio|
|/locations/allocatedStamp/read|GetAllocatedStamp è un'operazione interna usata dal servizio|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Operazione | DESCRIZIONE |
|---|---|
|/checkNamespaceAvailability/action|Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata.|
|/register/action|Registra la sottoscrizione per il provider di risorse inoltro e abilita la creazione di risorse inoltro|
|/namespaces/write|Crea una risorsa spazio dei nomi e ne aggiorna le proprietà. Le proprietà che si possono aggiornare sono quelle relative ai tag e allo stato dello spazio dei nomi.|
|/namespaces/read|Ottiene l'elenco delle descrizioni delle risorse spazio dei nomi|
|/namespaces/Delete|Elimina una risorsa spazio dei nomi|
|/namespaces/authorizationRules/write|Crea regole di autorizzazione a livello dello spazio dei nomi e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/namespaces/authorizationRules/delete|Elimina regola di autorizzazione dello spazio dei nomi. Non è possibile eliminare la regola di autorizzazione dello spazio dei nomi predefinita. |
|/namespaces/authorizationRules/listkeys/action|Ottiene la stringa di connessione per lo spazio dei nomi|
|/namespaces/HybridConnections/write|Crea o aggiorna proprietà della connessione ibrida.|
|/namespaces/HybridConnections/read|Ottiene l'elenco delle descrizioni delle risorse della connessione ibrida|
|/namespaces/HybridConnections/Delete|Operazione per l'eliminazione di risorse di connessione ibrida|
|/namespaces/HybridConnections/authorizationRules/write|Crea regole di autorizzazione della connessione ibrida e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/namespaces/HybridConnections/authorizationRules/delete|Operazione per l'eliminazione delle regole di autorizzazione della connessione ibrida|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|Ottiene la stringa di connessione per la connessione ibrida|
|/namespaces/WcfRelays/write|Crea o aggiorna proprietà inoltro WCF.|
|/namespaces/WcfRelays/read|Ottiene l'elenco delle descrizioni delle risorse dell'inoltro WCF|
|/namespaces/WcfRelays/Delete|Operazione per l'eliminazione delle risorse dell'inoltro WCF|
|/namespaces/WcfRelays/authorizationRules/write|Crea regole di autorizzazione dell'inoltro WCF e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/namespaces/WcfRelays/authorizationRules/delete|Operazione per l'eliminazione di regole di autorizzazione dell'inoltro WCF|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|Ottiene la stringa di connessione per l'inoltro WCF|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Operazione | DESCRIZIONE |
|---|---|
|/AvailabilityStatuses/read|Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato|
|/AvailabilityStatuses/current/read|Ottiene lo stato di disponibilità per la risorsa specificata|

## <a name="microsoftresources"></a>Microsoft.Resources

| Operazione | DESCRIZIONE |
|---|---|
|/checkResourceName/action|Verifica la validità del nome della risorsa.|
|/providers/read|Ottiene l'elenco dei provider.|
|/subscriptions/read|Ottiene l'elenco delle sottoscrizioni.|
|/subscriptions/operationresults/read|Ottiene i risultati dell'operazione di sottoscrizione.|
|/subscriptions/providers/read|Ottiene o elenca i provider di risorse.|
|/subscriptions/tagNames/read|Ottiene o elenca le categorie della sottoscrizione.|
|/subscriptions/tagNames/write|Aggiunge una categoria della sottoscrizione.|
|/subscriptions/tagNames/delete|Elimina una categoria della sottoscrizione.|
|/subscriptions/tagNames/tagValues/read|Ottiene o elenca i valori delle categorie della sottoscrizione.|
|/subscriptions/tagNames/tagValues/write|Aggiunge un valore di categoria della sottoscrizione.|
|/subscriptions/tagNames/tagValues/delete|Elimina un valore di categoria della sottoscrizione.|
|/subscriptions/resources/read|Ottiene le risorse di una sottoscrizione.|
|/subscriptions/resourceGroups/read|Ottiene o elenca i gruppi di risorse.|
|/subscriptions/resourceGroups/write|Crea o aggiorna un gruppo di risorse.|
|/subscriptions/resourceGroups/delete|Elimina un gruppo di risorse e tutte le risorse in esso contenute.|
|/subscriptions/resourceGroups/moveResources/action|Sposta le risorse da un gruppo di risorse a un altro.|
|/subscriptions/resourceGroups/validateMoveResources/action|Convalida lo spostamento di risorse da un gruppo di risorse a un altro.|
|/subscriptions/resourcegroups/resources/read|Ottiene le risorse del gruppo di risorse.|
|/subscriptions/resourcegroups/deployments/read|Ottiene o elenca le distribuzioni.|
|/subscriptions/resourcegroups/deployments/write|Crea o aggiorna una distribuzione.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|Ottiene o elenca gli stati dell'operazione di distribuzione.|
|/subscriptions/resourcegroups/deployments/operations/read|Ottiene o elenca le operazioni di distribuzione.|
|/subscriptions/locations/read|Ottiene l'elenco delle posizioni supportate.|
|/links/read|Ottiene o elenca i collegamenti a una risorsa.|
|/links/write|Crea o aggiorna un collegamento a una risorsa.|
|/links/delete|Elimina un collegamento a una risorsa.|
|/tenants/read|Ottiene l'elenco dei tenant.|
|/resources/read|Ottiene l'elenco delle risorse in base a filtri.|
|/deployments/read|Ottiene o elenca le distribuzioni.|
|/deployments/write|Crea o aggiorna una distribuzione.|
|/deployments/delete|Elimina una distribuzione.|
|/deployments/cancel/action|Annulla una distribuzione.|
|/deployments/validate/action|Convalida una distribuzione.|
|/deployments/operations/read|Ottiene o elenca le operazioni di distribuzione.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Operazione | DESCRIZIONE |
|---|---|
|/jobcollections/read|Ottiene la raccolta processi|
|/jobcollections/write|Crea o aggiorna la raccolta processi.|
|/jobcollections/delete|Elimina la raccolta processi.|
|/jobcollections/enable/action|Abilita la raccolta processi.|
|/jobcollections/disable/action|Disabilita la raccolta processi.|
|/jobcollections/jobs/read|Ottiene il processo.|
|/jobcollections/jobs/write|Crea o aggiorna il processo.|
|/jobcollections/jobs/delete|Elimina il processo.|
|/jobcollections/jobs/run/action|Esegue il processo.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Genera la definizione dell'app per la logica in base a un processo dell'Utilità di pianificazione.|
|/jobcollections/jobs/jobhistories/read|Ottiene la cronologia processi.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Registra la sottoscrizione per il provider di risorse di ricerca e consente la creazione di servizi di ricerca.|
|/checkNameAvailability/action|Controlla la disponibilità del nome del servizio.|
|/searchServices/write|Crea o aggiorna il servizio di ricerca.|
|/searchServices/read|Legge il servizio di ricerca.|
|/searchServices/delete|Elimina il servizio di ricerca.|
|/searchServices/start/action|Avvia il servizio di ricerca.|
|/searchServices/stop/action|Arresta il servizio di ricerca.|
|/searchServices/listAdminKeys/action|Legge le chiavi di amministrazione.|
|/searchServices/regenerateAdminKey/action|Rigenera la chiave di amministrazione.|
|/searchServices/createQueryKey/action|Crea la chiave di query.|
|/searchServices/queryKey/read|Legge le chiavi di query.|
|/searchServices/queryKey/delete|Elimina la chiave di query.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Operazione | DESCRIZIONE |
|---|---|
|/jitNetworkAccessPolicies/read|Ottiene i criteri di accesso alla rete JIT|
|/jitNetworkAccessPolicies/write|Crea un nuovo criterio di accesso alla rete JIT o ne aggiorna uno esistente|
|/jitNetworkAccessPolicies/initiate/action|Avvia un criterio di accesso alla rete JIT|
|/securitySolutionsReferenceData/read|Ottiene i dati di riferimento delle soluzioni di sicurezza|
|/securityStatuses/read|Ottiene gli stati di integrità della sicurezza per le soluzioni Azure|
|/webApplicationFirewalls/read|Ottiene i firewall dell'applicazione Web|
|/webApplicationFirewalls/write|Crea un nuovo firewall dell'applicazione Web o ne aggiorna uno esistente|
|/webApplicationFirewalls/delete|Elimina un firewall dell'applicazione Web|
|/securitySolutions/read|Ottiene le soluzioni di sicurezza|
|/securitySolutions/write|Crea una nuova soluzione di sicurezza o ne aggiorna una esistente|
|/securitySolutions/delete|Elimina una soluzione di sicurezza|
|/tasks/read|Ottiene tutti i consigli per la sicurezza disponibili|
|/tasks/dismiss/action|Ignora un consiglio per la sicurezza|
|/tasks/activate/action|Attiva un consiglio per la sicurezza|
|/policies/read|Ottiene i criteri di sicurezza|
|/policies/write|Aggiorna i criteri di sicurezza|
|/applicationWhitelistings/read|Ottiene gli elenchi elementi consentiti delle applicazioni|
|/applicationWhitelistings/write|Crea un nuovo elenco elementi consentiti per l'applicazione o ne aggiorna uno esistente|

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

| Operazione | DESCRIZIONE |
|---|---|
|/subscriptions/write|Crea o aggiorna una sottoscrizione|
|/gateways/write|Crea o aggiorna un gateway|
|/gateways/delete|Elimina un gateway|
|/gateways/read|Ottiene un gateway|
|/gateways/regenerateprofile/action|Rigenera il profilo del gateway|
|/gateways/upgradetolatest/action|Aggiorna il gateway alla versione più recente|
|/nodes/write|Crea o aggiorna un nodo|
|/nodes/delete|Elimina un nodo|
|/nodes/read|Ottiene un nodo|
|/sessions/write|Crea o aggiorna una sessione|
|/sessions/read|Ottiene una sessione|
|/sessions/delete|Elimina una sessione|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Operazione | DESCRIZIONE |
|---|---|
|/checkNameAvailability/action|Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata.|
|/register/action|Registra la sottoscrizione per il provider di risorse bus di servizio e consente la creazione di risorse bus di servizio|
|/namespaces/write|Crea una risorsa spazio dei nomi e ne aggiorna le proprietà. Le proprietà che si possono aggiornare sono quelle relative ai tag e allo stato dello spazio dei nomi.|
|/namespaces/read|Ottiene l'elenco delle descrizioni delle risorse spazio dei nomi|
|/namespaces/Delete|Elimina una risorsa spazio dei nomi|
|/namespaces/metricDefinitions/read|Ottiene l'elenco di descrizioni delle risorse di metrica dello spazio dei nomi|
|/namespaces/authorizationRules/write|Crea regole di autorizzazione a livello dello spazio dei nomi e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/namespaces/authorizationRules/read|Ottiene l'elenco di descrizioni delle regole di autorizzazione degli spazi dei nomi.|
|/namespaces/authorizationRules/delete|Elimina regola di autorizzazione dello spazio dei nomi. Non è possibile eliminare la regola di autorizzazione dello spazio dei nomi predefinita. |
|/namespaces/authorizationRules/listkeys/action|Ottiene la stringa di connessione per lo spazio dei nomi|
|/namespaces/authorizationRules/regenerateKeys/action|Rigenera la chiave primaria o secondaria per la risorsa|
|/namespaces/diagnosticSettings/read|Ottiene l'elenco di descrizioni delle risorse impostazioni di diagnostica dello spazio dei nomi|
|/namespaces/diagnosticSettings/write|Ottiene l'elenco di descrizioni delle risorse impostazioni di diagnostica dello spazio dei nomi|
|/namespaces/queues/write|Crea o aggiorna proprietà di coda.|
|/namespaces/queues/read|Ottiene l'elenco delle descrizioni delle risorse coda|
|/namespaces/queues/Delete|Operazione per l'eliminazione delle risorse coda|
|/namespaces/queues/authorizationRules/write|Crea regole di autorizzazione delle code e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/namespaces/queues/authorizationRules/read| Ottiene l'elenco delle regole di autorizzazione delle code|
|/namespaces/queues/authorizationRules/delete|Operazione per l'eliminazione di regole di autorizzazione delle code|
|/namespaces/queues/authorizationRules/listkeys/action|Ottiene la stringa di connessione alla coda|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Rigenera la chiave primaria o secondaria per la risorsa|
|/namespaces/logDefinitions/read|Ottiene l'elenco di descrizioni delle risorse log dello spazio dei nomi|
|/namespaces/topics/write|Crea o aggiorna le proprietà degli argomenti.|
|/namespaces/topics/read|Ottiene l'elenco delle descrizioni delle risorse argomento|
|/namespaces/topics/Delete|Operazione per l'eliminazione di risorse argomento|
|/namespaces/topics/authorizationRules/write|Crea regole di autorizzazione dell’argomento e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/namespaces/topics/authorizationRules/read| Ottiene l'elenco delle regole di autorizzazione dell’argomento|
|/namespaces/topics/authorizationRules/delete|Operazione per l'eliminazione di regole di autorizzazione dell’argomento|
|/namespaces/topics/authorizationRules/listkeys/action|Ottiene la stringa di connessione all’argomento|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Rigenera la chiave primaria o secondaria per la risorsa|
|/namespaces/topics/subscriptions/write|Crea o aggiorna le proprietà TopicSubscription.|
|/namespaces/topics/subscriptions/read|Ottiene l'elenco delle descrizioni delle risorse TopicSubscription|
|/namespaces/topics/subscriptions/Delete|Operazione per l’eliminazione di una risorsa TopicSubscription|
|/namespaces/topics/subscriptions/rules/write|Crea o aggiorna le proprietà di una regola.|
|/namespaces/topics/subscriptions/rules/read|Ottiene l'elenco delle descrizioni delle risorse di una regola|
|/namespaces/topics/subscriptions/rules/Delete|Operazione per l'eliminazione delle risorse di una regola|

## <a name="microsoftsql"></a>Microsoft.Sql

| Operazione | DESCRIZIONE |
|---|---|
|/servers/read|Restituisce un elenco di server in un gruppo di risorse per una sottoscrizione|
|/servers/write|Crea un nuovo server o modifica le proprietà di un server esistente in un gruppo di risorse per una sottoscrizione|
|/servers/delete|Elimina un server e tutti i database e i pool elastici contenuti|
|/servers/import/action|Crea un nuovo database sul server e distribuisce schema e dati da un pacchetto DacPac|
|/servers/upgrade/action|Abilita la nuova funzionalità disponibile sull’ultima versione del server e specifica la mappa di conversione della versione di database|
|/servers/VulnerabilityAssessmentScans/action|Esegue la scansione del server per la valutazione della vulnerabilità|
|/servers/operationResults/read|L’operazione viene utilizzata per tenere traccia dei progressi di aggiornamento del server dalla prima versione all’ultima|
|/servers/operationResults/delete|Interrompe l’aggiornamento di versione del server in corso|
|/servers/securityAlertPolicies/read|Recupera i dettagli dei criteri di rilevamento minacce del server configurati in uno specifico server|
|/servers/securityAlertPolicies/write|Modifica il rilevamento minacce del server per uno specifico server|
|/servers/securityAlertPolicies/operationResults/read|Recupera i risultati dell’operazione di impostazione criteri per rilevamento minacce del server|
|/servers/administrators/read|Recupera i dettagli dell’amministratore del server|
|/servers/administrators/write|Crea o aggiorna l’amministratore del server|
|/servers/administrators/delete|Elimina l’amministratore del server dal server|
|/servers/recoverableDatabases/read|Questa operazione viene utilizzata per il ripristino di emergenza di database attivi, per ripristinare il database a un punto di backup noto come valido. Restituisce informazioni sull'ultimo backup valido, ma in realtà non ripristina il database.|
|/servers/serviceObjectives/read|Recupera l'elenco degli obiettivi del livello di servizio (anche noti come livelli di prestazioni) disponibili in un determinato server|
|/servers/firewallRules/read|Recupera i dettagli della regola del firewall del server|
|/servers/firewallRules/write|Crea o aggiorna la regola del firewall che controlla l’intervallo di indirizzi IP autorizzati a connettersi al server|
|/servers/firewallRules/delete|Elimina la regola del firewall dal server|
|/servers/administratorOperationResults/read|Recupera i risultati dell’operazione dell’amministratore del server|
|/servers/recommendedElasticPools/read|Recupera il consiglio per i pool di database elastici per ridurre i costi o migliorare le prestazioni in base all’utilizzo storico delle risorse|
|/servers/recommendedElasticPools/metrics/read|Recupera le metriche per i pool di database elastici consigliati per un determinato server|
|/servers/recommendedElasticPools/databases/read|Recupera i database da aggiungere nei pool di database elastici consigliati per un determinato server|
|/servers/elasticPools/read|Recupera i dettagli per il pool di database elastico per un determinato server|
|/servers/elasticPools/write|Crea nuove proprietà o elimina quelle esistenti di un pool di database elastico|
|/servers/elasticPools/delete|Elimina un pool di database elastico esistente|
|/servers/elasticPools/operationResults/read|Recupera i dettagli su un’operazione di un pool di database elastico specifico|
|/servers/elasticPools/providers/Microsoft.Insights/<br>metricDefinitions/read|Restituisce i tipi di metriche disponibili per i pool di database elastici|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/servers/elasticPools/metrics/read|Restituisce la metrica di utilizzo delle risorse di un pool di database elastico|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Recupera attività e dettagli su uno specifico database che fa parte del pool di database elastico|
|/servers/elasticPools/advisors/read|Restituisce l'elenco degli advisor disponibili per il pool elastico|
|/servers/elasticPools/advisors/write|Aggiorna lo stato di esecuzione automatica di un advisor a livello di pool elastico.|
|/servers/elasticPools/advisors/recommendedActions/read|Restituisce l'elenco di azioni consigliate per l’advisor specificato per il pool elastico|
|/servers/elasticPools/advisors/recommendedActions/write|Applica l'azione consigliata al pool elastico|
|/servers/elasticPools/elasticPoolActivity/read|Recupera attività e dettagli su un pool di database elastico|
|/servers/elasticPools/databases/read|Recupera elenco e dettagli dei database che fanno parte di un pool di database elastico su un server specifico|
|/servers/auditingPolicies/read|Recupera i dettagli dei criteri di controllo della tabella del server predefinito configurati in uno specifico database|
|/servers/auditingPolicies/write|Modifica il controllo della tabella del server predefinito per uno specifico server|
|/servers/disasterRecoveryConfiguration/operationResults/read|Ottiene i risultati dell’operazione di configurazione di ripristino di emergenza|
|/servers/advisors/read|Restituisce l'elenco degli advisor disponibili per il server|
|/servers/advisors/write|Aggiorna lo stato di esecuzione automatica di un advisor a livello di server.|
|/servers/advisors/recommendedActions/read|Restituisce l'elenco di azioni consigliate per l’advisor specificato per il server|
|/servers/advisors/recommendedActions/write|Applica l'azione consigliata al server|
|/servers/usages/read|Restituisce la quota DTU del server e il consumo di DTU corrente del server da parte di tutti i database nel server|
|/servers/elasticPoolEstimates/read|Restituisce l’elenco di stime del pool elastico già create per questo server|
|/servers/elasticPoolEstimates/write|Crea una nuova stima del pool elastico per l'elenco dei database fornito|
|/servers/auditingSettings/read|Recupera i dettagli dei criteri di controllo del BLOB del server configurati in uno specifico server|
|/servers/auditingSettings/write|Modifica il controllo del BLOB del server per uno specifico server|
|/servers/auditingSettings/operationResults/read|Recupera il risultato dell’operazione di impostazione criteri di controllo BLOB del server|
|/servers/backupLongTermRetentionVaults/read|Questa operazione serve per acquisire un insieme di credenziali di conservazione a lungo termine del backup. Restituisce informazioni sull’insieme di credenziali registrato su questo server.|
|/servers/backupLongTermRetentionVaults/write|Registra un insieme di credenziali di conservazione a lungo termine del backup|
|/servers/restorableDroppedDatabases/read|Recupera un elenco di database eliminati su uno specifico server che rientrano ancora nei criteri di conservazione. Questa operazione restituisce un elenco di database e metadati associati, come ad esempio la data di eliminazione.|
|/servers/databases/read|Restituisce un elenco di server in un gruppo di risorse per una sottoscrizione|
|/servers/databases/write|Crea un nuovo server o modifica le proprietà di un server esistente in un gruppo di risorse per una sottoscrizione|
|/servers/databases/delete|Elimina un server e tutti i database e i pool elastici contenuti|
|/servers/databases/export/action|Crea un nuovo database sul server e distribuisce schema e dati da un pacchetto DacPac|
|/servers/databases/VulnerabilityAssessmentScans/action|Esegue la scansione del database per la valutazione della vulnerabilità.|
|/servers/databases/pause/action|Sospende un database versione DataWarehouse|
|/servers/databases/resume/action|Riprende un database versione DataWarehouse|
|/servers/databases/operationResults/read|L’operazione viene utilizzata per tenere traccia dei progressi di un’operazione di database prolungata, come ad esempio la scalabilità.|
|/servers/databases/replicationLinks/read|Restituisce i dettagli sui collegamenti di replica stabiliti per uno specifico database|
|/servers/databases/replicationLinks/delete|Termina forzatamente la relazione di replica e con potenziale perdita di dati|
|/servers/databases/replicationLinks/unlink/action|Termina forzatamente la relazione di replica o dopo la sincronizzazione con il partner|
|/servers/databases/replicationLinks/failover/action|Failover dopo la sincronizzazione di tutte modifiche dal database primario, rendendo questo database primario nella relazione di replica e rendendo il database primario remoto il database secondario|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Esegue immediatamente il failover con potenziale perdita di dati, rendendo questo database primario nella relazione di replica e rendendo il database primario remoto il database secondario|
|/servers/databases/replicationLinks/updateReplicationMode/action|Aggiorna la modalità di replica per il collegamento in modalità sincrona o asincrona|
|/servers/databases/replicationLinks/operationResults/read|Ottiene lo stato di operazioni prolungate sui collegamenti di replica del database|
|/servers/databases/dataMaskingPolicies/read|Recupera i dettagli dei criteri di mascheramento dati configurati in uno specifico database|
|/servers/databases/dataMaskingPolicies/write|Modifica i criteri di mascheramento dati per uno specifico database|
|/servers/databases/dataMaskingPolicies/rules/read|Recupera i dettagli della regola per i criteri di mascheramento dati configurati in uno specifico database|
|/servers/databases/dataMaskingPolicies/rules/write|Modifica la regola per i criteri di mascheramento dati per uno specifico database|
|/servers/databases/securityAlertPolicies/read|Recupera i dettagli dei criteri di rilevamento minacce configurati in uno specifico database|
|/servers/databases/securityAlertPolicies/write|Modifica i criteri di rilevamento minacce per uno specifico database|
|/servers/databases/providers/Microsoft.Insights/<br>metricDefinitions/read|Restituisce i tipi di metriche disponibili per i database|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/servers/databases/providers/Microsoft.Insights/<br>logDefinitions/read|Ottiene i registri disponibili per i database|
|/servers/databases/topQueries/read|Restituisce le statistiche aggregate a runtime per la query selezionata nel periodo di tempo selezionato|
|/servers/databases/topQueries/queryText/read|Restituisce il testo Transact-SQL per l'ID query selezionato|
|/servers/databases/topQueries/statistics/read|Restituisce le statistiche aggregate a runtime per la query selezionata nel periodo di tempo selezionato|
|/servers/databases/connectionPolicies/read|Recupera i dettagli dei criteri di connessione configurati su uno specifico database|
|/servers/databases/connectionPolicies/write|Modifica i criteri di connessione per uno specifico database|
|/servers/databases/metrics/read|Restituisce le metriche di utilizzo delle risorse del database|
|/servers/databases/auditRecords/read|Recupera i record di controllo BLOB del database|
|/servers/databases/transparentDataEncryption/read|Recupera lo stato e i dettagli della funzionalità di sicurezza di Transparent Data Encryption per uno specifico database|
|/servers/databases/transparentDataEncryption/write|Abilita o disabilita la Transparent Data Encryption per uno specifico database|
|/servers/databases/transparentDataEncryption/operationResults/read|Recupera lo stato e i dettagli della funzionalità di sicurezza di Transparent Data Encryption per uno specifico database|
|/servers/databases/auditingPolicies/read|Recupera i dettagli dei criteri di controllo della tabella configurati in uno specifico database|
|/servers/databases/auditingPolicies/write|Modifica i criteri di controllo della tabella per uno specifico database|
|/servers/databases/dataWarehouseQueries/read|Restituisce le informazioni sulla query di distribuzione del data warehouse per l’ID query selezionato|
|/servers/databases/dataWarehouseQueries/<br>dataWarehouseQuerySteps/read|Restituisce le informazioni sul passaggio della query distribuita della query del data warehouse per l’ID passaggio selezionato|
|/servers/databases/serviceTierAdvisors/read|Restituisce un suggerimento sulla scalabilità verticale o orizzontale del database in base alle statistiche di esecuzione query per migliorare le prestazioni o ridurre i costi|
|/servers/databases/advisors/read|Restituisce l'elenco degli advisor disponibili per il database|
|/servers/databases/advisors/write|Aggiorna lo stato di esecuzione automatica di un advisor a livello di database.|
|/servers/databases/advisors/recommendedActions/read|Restituisce l'elenco di azioni consigliate per l’advisor specificato per il database|
|/servers/databases/advisors/recommendedActions/write|Applica l'azione consigliata al database|
|/servers/databases/usages/read|Restituisce la dimensione massima raggiungibile dal database e la dimensione attualmente occupata dai dati|
|/servers/databases/queryStore/read|Restituisce i valori correnti delle impostazioni di Archivio query per il database|
|/servers/databases/queryStore/write|Aggiorna le impostazioni di Archivio query per il database|
|/servers/databases/auditingSettings/read|Recupera i dettagli dei criteri di controllo del BLOB configurati in uno specifico database|
|/servers/databases/auditingSettings/write|Modifica i criteri di controllo del BLOB per uno specifico database|
|/servers/databases/schemas/tables/recommendedIndexes/read|Recupera l’elenco di raccomandazioni sull’indice su un database|
|/servers/databases/schemas/tables/recommendedIndexes/write|Applica la raccomandazione sull’indice|
|/servers/databases/schemas/tables/columns/read|Recupera l'elenco delle colonne di una tabella|
|/servers/databases/missingindexes/read|Restituisce le raccomandazioni sugli indici di database da creare, modificare o eliminare per migliorare le prestazioni delle query|
|/servers/databases/missingindexes/write|Usa la raccomandazione sull’indice di database in uno specifico database|
|/servers/databases/importExportOperationResults/read|Restituisce i dettagli sulle operazioni di importazione o esportazione del database dal DacPac situato sull’account di archiviazione|
|/servers/importExportOperationResults/read|Restituisce l'elenco con i dettagli per le operazioni di importazione del database dall’account di archiviazione su uno specifico server|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Registra la sottoscrizione per il provider di risorse di archiviazione e consente la creazione di account di archiviazione.|
|/checknameavailability/read|Controlla che il nome dell’account sia valido e non in uso.|
|/storageAccounts/write|Crea un account di archiviazione con i parametri specificati o aggiorna le proprietà o i tag o aggiunge un dominio personalizzato per l’account di archiviazione specificato.|
|/storageAccounts/delete|Elimina un account di archiviazione esistente.|
|/storageAccounts/listkeys/action|Restituisce le chiavi di accesso per l'account di archiviazione specificato.|
|/storageAccounts/regeneratekey/action|Rigenera le chiavi di accesso per l'account di archiviazione specificato.|
|/storageAccounts/read|Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato.|
|/storageAccounts/listAccountSas/action|Restituisce il token SAS dell’account per l’account di archiviazione specificato.|
|/storageAccounts/listServiceSas/action|Token SAS del servizio di archiviazione|
|/storageAccounts/services/diagnosticSettings/write|Crea/Aggiorna le impostazioni di diagnostica dell’account di archiviazione.|
|/skus/read|Elenca gli SKU supportati da Microsoft.Storage.|
|/usages/read|Restituisce il limite e il conteggio corrente degli utilizzi per le risorse nella sottoscrizione specificata|
|/operations/read|Interroga sullo stato di un’operazione asincrona.|
|/locations/deleteVirtualNetworkOrSubnets/action|Avvisa Microsoft.Storage che la rete virtuale o la subnet è in fase di eliminazione|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Operazione | DESCRIZIONE |
|---|---|
|/managers/clearAlerts/action|Cancella tutti gli avvisi associati a Gestione dispositivi.|
|/managers/getActivationKey/action|Ottiene la chiave di attivazione per Gestione dispositivi.|
|/managers/regenerateActivationKey/action|Rigenera la chiave di attivazione per Gestione dispositivi.|
|/managers/regenarateRegistationCertificate/action|Rigenera il certificato di registrazione per Gestione dispositivi.|
|/managers/getEncryptionKey/action|Ottiene la chiave di crittografia per Gestione dispositivi.|
|/managers/read|Elenca o ottiene gli strumenti di gestione dei dispositivi|
|/managers/delete|Elimina gli strumenti di gestione dei dispositivi|
|/managers/write|Crea o aggiorna gli strumenti di gestione dei dispositivi|
|/managers/configureDevice/action|Configura un dispositivo|
|/managers/listActivationKey/action|Ottiene la chiave di attivazione di Gestione dispositivi StorSimple.|
|/managers/listPublicEncryptionKey/action|Elenca le chiavi di crittografia pubbliche di uno strumento Gestione dispositivi StorSimple.|
|/managers/listPrivateEncryptionKey/action|Ottiene la chiave di crittografia privata per uno strumento Gestione dispositivi StorSimple.|
|/managers/provisionCloudAppliance/action|Crea una nuova appliance cloud.|
|/Managers/write|L'operazione Crea insieme di credenziali crea una risorsa di Azure di tipo 'vault'|
|/Managers/read|L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault'|
|/Managers/delete|L'operazione Elimina insieme di credenziali elimina la risorsa di Azure specificata di tipo 'vault'|
|/managers/storageAccountCredentials/write|Crea o aggiorna le credenziali dell'account di archiviazione|
|/managers/storageAccountCredentials/read|Elenca o ottiene le credenziali dell'account di archiviazione|
|/managers/storageAccountCredentials/delete|Elimina le credenziali dell'account di archiviazione|
|/managers/storageAccountCredentials/listAccessKey/action|Elenca le chiavi di accesso delle credenziali dell'account di archiviazione|
|/managers/accessControlRecords/read|Elenca o ottiene i record di controllo di accesso|
|/managers/accessControlRecords/write|Crea o aggiorna i record di controllo di accesso|
|/managers/accessControlRecords/delete|Elimina i record di controllo di accesso|
|/managers/metrics/read|Elenca o ottiene la metrica|
|/managers/bandwidthSettings/read|Elenca le impostazioni di larghezza di banda (solo serie 8000)|
|/managers/bandwidthSettings/write|Crea una nuova impostazione o aggiorna le impostazioni di larghezza di banda esistenti (solo serie 8000)|
|/managers/bandwidthSettings/delete|Elimina impostazioni di larghezza di banda esistenti (solo serie 8000)|
|/Managers/extendedInformation/read|L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault?|
|/Managers/extendedInformation/write|L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault?|
|/Managers/extendedInformation/delete|L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault?|
|/managers/alerts/read|Elenca o ottiene gli avvisi|
|/managers/storageDomains/read|Elenca o ottiene i domini di archiviazione|
|/managers/storageDomains/write|Crea o aggiorna i domini di archiviazione|
|/managers/storageDomains/delete|Elimina i domini di archiviazione|
|/managers/devices/scanForUpdates/action|Verifica la disponibilità di aggiornamenti per un dispositivo.|
|/managers/devices/download/action|Scarica aggiornamenti per un dispositivo.|
|/managers/devices/install/action|Installa aggiornamenti in un dispositivo.|
|/managers/devices/read|Elenca o ottiene i dispositivi|
|/managers/devices/write|Crea o aggiorna i dispositivi|
|/managers/devices/delete|Elimina i dispositivi|
|/managers/devices/deactivate/action|Disattiva un dispositivo.|
|/managers/devices/publishSupportPackage/action|Pubblica il pacchetto per il supporto di un dispositivo per la risoluzione dei problemi da parte del supporto tecnico Microsoft.|
|/managers/devices/failover/action|Failover del dispositivo.|
|/managers/devices/sendTestAlertEmail/action|Invia messaggi di posta elettronica di avviso di test ai destinatari di posta elettronica configurati.|
|/managers/devices/installUpdates/action|Installa aggiornamenti nei dispositivi|
|/managers/devices/listFailoverSets/action|Elenca i set di failover per un dispositivo esistente.|
|/managers/devices/listFailoverTargets/action|Elenca le destinazioni di failover dei dispositivi|
|/managers/devices/publicEncryptionKey/action|Elenca le chiavi di crittografia pubbliche di Gestione dispositivi|
|/managers/devices/hardwareComponentGroups/<br>read|Elenca i gruppi di componenti hardware|
|/managers/devices/hardwareComponentGroups/<br>changeControllerPowerState/action|Modifica lo stato di alimentazione del controller dei gruppi di componenti hardware|
|/managers/devices/metrics/read|Elenca o ottiene le metriche|
|/managers/devices/chapSettings/write|Crea o aggiorna le impostazioni CHAP|
|/managers/devices/chapSettings/read|Elenca o ottiene le impostazioni CHAP|
|/managers/devices/chapSettings/delete|Elimina le impostazioni CHAP|
|/managers/devices/backupScheduleGroups/read|Elenca o ottiene i gruppi di pianificazione del backup|
|/managers/devices/backupScheduleGroups/write|Crea o aggiorna i gruppi di pianificazione del backup|
|/managers/devices/backupScheduleGroups/delete|Elimina i gruppi di pianificazione del backup|
|/managers/devices/updateSummary/read|Elenca o ottiene il riepilogo aggiornamenti|
|/managers/devices/migrationSourceConfigurations/<br>import/action|Importa le configurazioni di origine per la migrazione|
|/managers/devices/migrationSourceConfigurations/<br>startMigrationEstimate/action|Avvia un processo per stimare la durata del processo di migrazione.|
|/managers/devices/migrationSourceConfigurations/<br>startMigration/action|Avvia la migrazione usando le configurazioni di origine|
|/managers/devices/migrationSourceConfigurations/<br>confirmMigration/action|Conferma una migrazione completata correttamente e ne esegue il commit.|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationEstimate/action|Recupera lo stato del processo di stima della migrazione.|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationStatus/action|Recupera lo stato della migrazione.|
|/managers/devices/migrationSourceConfigurations/<br>fetchConfirmMigrationStatus/action|Recupera lo stato di conferma della migrazione.|
|/managers/devices/alertSettings/read|Elenca o ottiene le impostazioni degli avvisi|
|/managers/devices/alertSettings/write|Crea o aggiorna le impostazioni degli avvisi|
|/managers/devices/networkSettings/read|Elenca o ottiene le impostazioni di rete|
|/managers/devices/networkSettings/write|Crea nuove impostazioni di rete o aggiorna quelle esistenti|
|/managers/devices/jobs/read|Elenca o ottiene i processi|
|/managers/devices/jobs/cancel/action|Annulla un processo in esecuzione|
|/managers/devices/metricsDefinitions/read|Elenca o ottiene le definizioni di metrica|
|/managers/devices/volumeContainers/write|Crea nuovi contenitori del volume o aggiorna quelli esistenti (solo serie 8000)|
|/managers/devices/volumeContainers/read|Elenca i contenitori del volume (solo serie 8000)|
|/managers/devices/volumeContainers/delete|Elenca un contenitore del volume esistente (solo serie 8000)|
|/managers/devices/volumeContainers/listEncryptionKeys/action|Elenca le chiavi di crittografia dei contenitori del volume|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|Sostituisce le chiavi di crittografia dei contenitori del volume|
|/managers/devices/volumeContainers/metrics/read|Elenca le metriche|
|/managers/devices/volumeContainers/volumes/read|Elenca i volumi|
|/managers/devices/volumeContainers/volumes/write|Crea nuovi volumi o aggiorna quelli esistenti|
|/managers/devices/volumeContainers/volumes/delete|Elimina i volumi esistenti|
|/managers/devices/volumeContainers/volumes/metrics/read|Elenca le metriche|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|Elenca le definizioni di metrica|
|/managers/devices/volumeContainers/metricsDefinitions/read|Elenca le definizioni di metrica|
|/managers/devices/iscsiservers/read|Elenca o ottiene i server iSCSI|
|/managers/devices/iscsiservers/write|Crea o aggiorna i server iSCSI|
|/managers/devices/iscsiservers/delete|Elimina i server iSCSI|
|/managers/devices/iscsiservers/backup/action|Esegue il backup di un server iSCSI.|
|/managers/devices/iscsiservers/metrics/read|Elenca o ottiene le metriche|
|/managers/devices/iscsiservers/disks/read|Elenca o ottiene i dischi|
|/managers/devices/iscsiservers/disks/write|Crea o aggiorna i dischi|
|/managers/devices/iscsiservers/disks/delete|Elimina i dischi|
|/managers/devices/iscsiservers/disks/metrics/read|Elenca o ottiene le metriche|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|Elenca o ottiene le definizioni di metrica|
|/managers/devices/iscsiservers/metricsDefinitions/read|Elenca o ottiene le definizioni di metrica|
|/managers/devices/backups/read|Elenca o ottiene i set di backup|
|/managers/devices/backups/delete|Elimina il set di backup|
|/managers/devices/backups/restore/action|Ripristina tutti i volumi da un set di backup.|
|/managers/devices/backups/elements/clone/action|Clona una condivisione o volume usando un elemento di backup.|
|/managers/devices/backupPolicies/write|Crea nuovi criteri di backup o aggiorna quelli esistenti (solo serie 8000)|
|/managers/devices/backupPolicies/read|Elenca i criteri di backup (solo serie 8000)|
|/managers/devices/backupPolicies/delete|Elimina criteri di backup esistenti (solo serie 8000)|
|/managers/devices/backupPolicies/backup/action|A partire da un backup manuale crea un backup su richiesta di tutti i volumi protetti dai criteri.|
|/managers/devices/backupPolicies/schedules/write|Crea pianificazioni o aggiorna pianificazioni esistenti|
|/managers/devices/backupPolicies/schedules/read|Elenca le pianificazioni|
|/managers/devices/backupPolicies/schedules/delete|Elimina le pianificazioni esistenti|
|/managers/devices/securitySettings/update/action|Aggiorna le impostazioni di protezione.|
|/managers/devices/securitySettings/read|Elenca le impostazioni di protezione|
|/managers/devices/securitySettings/<br>syncRemoteManagementCertificate/action|Sincronizza il certificato di gestione remota per un dispositivo.|
|/managers/devices/securitySettings/write|Crea nuove impostazioni di protezione o aggiorna quelle esistenti|
|/managers/devices/fileservers/read|Elenca o ottiene i file server|
|/managers/devices/fileservers/write|Crea o aggiorna i file server|
|/managers/devices/fileservers/delete|Elimina i file server|
|/managers/devices/fileservers/backup/action|Esegue il backup di un file server.|
|/managers/devices/fileservers/metrics/read|Elenca o ottiene le metriche|
|/managers/devices/fileservers/shares/write|Crea o aggiorna le condivisioni|
|/managers/devices/fileservers/shares/read|Elenca o ottiene le condivisioni|
|/managers/devices/fileservers/shares/delete|Elimina le condivisioni|
|/managers/devices/fileservers/shares/metrics/read|Elenca o ottiene le metriche|
|/managers/devices/fileservers/shares/metricsDefinitions/read|Elenca o ottiene le definizioni di metrica|
|/managers/devices/fileservers/metricsDefinitions/read|Elenca o ottiene le definizioni di metrica|
|/managers/devices/timeSettings/read|Elenca o ottiene le impostazioni ora|
|/managers/devices/timeSettings/write|Crea nuove impostazioni ora o aggiorna quelle esistenti|
|/Managers/certificates/write|L'operazione Aggiorna certificato risorsa aggiorna il certificato delle credenziali della risorsa o dell'insieme di credenziali.|
|/managers/cloudApplianceConfigurations/read|Elenca le configurazioni supportate dall'appliance cloud|
|/managers/metricsDefinitions/read|Elenca o ottiene le definizioni di metrica|
|/managers/encryptionSettings/read|Elenca o ottiene le impostazioni di crittografia|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Operazione | DESCRIZIONE |
|---|---|
|/streamingjobs/Start/action|Avvia un processo di Analisi di flusso|
|/streamingjobs/Stop/action|Arresta un processo di Analisi di flusso|
|/streamingjobs/Read|Legge un processo di Analisi di flusso|
|/streamingjobs/Write|Scrive un processo di Analisi di flusso|
|/streamingjobs/Delete|Elimina un processo di Analisi di flusso|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per i processi di streaming|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Legge un'impostazione di diagnostica.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Scrive un'impostazione di diagnostica.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|Ottiene i registri disponibili per i processi di streaming|
|/streamingjobs/transformations/Read|Crea una trasformazione processo di Analisi di flusso|
|/streamingjobs/transformations/Write|Scrive una trasformazione processo di Analisi di flusso|
|/streamingjobs/transformations/Delete|Elimina una trasformazione processo di Analisi di flusso|
|/streamingjobs/inputs/Read|Legge un input processo di Analisi di flusso|
|/streamingjobs/inputs/Write|Scrive un input processo di Analisi di flusso|
|/streamingjobs/inputs/Delete|Elimina un input processo di Analisi di flusso|
|/streamingjobs/outputs/Read|Legge un output processo di Analisi di flusso|
|/streamingjobs/outputs/Write|Scrive un output processo di Analisi di flusso|
|/streamingjobs/outputs/Delete|Elimina un output processo di Analisi di flusso|

## <a name="microsoftsupport"></a>Microsoft.Support

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Esegue la registrazione al provider di risorse di supporto|
|/supportTickets/read|Ottiene i dettagli del ticket di supporto (inclusi stato, gravità, dettagli di contatto e comunicazioni) oppure ottiene l'elenco dei ticket di supporto dalle diverse sottoscrizioni.|
|/supportTickets/write|Crea o aggiorna un ticket di supporto. È possibile creare un ticket di supporto per problemi associati ai settori Tecnico, Fatturazione, Quote o Gestione della sottoscrizione. È possibile aggiornare la gravità, i dettagli di contatto e le comunicazioni dei ticket di supporto esistenti.|

## <a name="microsoftweb"></a>Microsoft.Web

| Operazione | DESCRIZIONE |
|---|---|
|/unregister/action|Annulla la registrazione del provider di risorse Microsoft.Web per la sottoscrizione.|
|/validate/action|Convalida il provider di risorse Microsoft.Web per la sottoscrizione.|
|/register/action|Registra il provider di risorse Microsoft.Web per la sottoscrizione.|
|/hostingEnvironments/Read|Ottiene le proprietà di un ambiente del servizio app|
|/hostingEnvironments/Write|Crea un nuovo ambiente del servizio app o ne aggiorna uno esistente|
|/hostingEnvironments/Delete|Elimina un ambiente del servizio app|
|/hostingEnvironments/reboot/Action|Riavvia tutti i computer in un ambiente del servizio app|
|/hostingenvironments/resume/action|Ripristina l'esecuzione degli ambienti di hosting.|
|/hostingenvironments/suspend/action|Sospende l'esecuzione degli ambienti di hosting.|
|/hostingenvironments/metricdefinitions/read|Ottiene le definizioni metrica degli ambienti di hosting.|
|/hostingEnvironments/workerPools/Read|Ottiene le proprietà di un pool di lavoro in un ambiente del servizio app|
|/hostingEnvironments/workerPools/Write|Crea un nuovo pool di lavoro in un ambiente del servizio app o ne aggiorna uno esistente|
|/hostingenvironments/workerpools/metricdefinitions/read|Ottiene le definizioni metrica dei pool di lavoro degli ambienti di hosting.|
|/hostingenvironments/workerpools/metrics/read|Ottiene la metrica dei pool di lavoro degli ambienti di hosting.|
|/hostingenvironments/workerpools/skus/read|Ottiene gli SKU dei pool di lavoro degli ambienti di hosting.|
|/hostingenvironments/workerpools/usages/read|Ottiene gli utilizzi dei pool di lavoro degli ambienti di hosting.|
|/hostingenvironments/sites/read|Ottiene le app Web degli ambienti di hosting.|
|/hostingenvironments/serverfarms/read|Ottiene i piani di servizio app degli ambienti di hosting.|
|/hostingenvironments/usages/read|Ottiene gli utilizzi degli ambienti di hosting.|
|/hostingenvironments/capacities/read|Ottiene le capacità degli ambienti di hosting.|
|/hostingenvironments/operations/read|Ottiene le operazioni degli ambienti di hosting.|
|/hostingEnvironments/multiRolePools/Read|Ottiene le proprietà di un pool front-end in un ambiente del servizio app|
|/hostingEnvironments/multiRolePools/Write|Crea un nuovo pool front-end o ne aggiorna uno esistente in un ambiente del servizio app|
|/hostingenvironments/multirolepools/metricdefinitions/read|Ottiene le definizioni metrica dei pool multiruolo degli ambienti di hosting.|
|/hostingenvironments/multirolepools/metrics/read|Ottiene la metrica dei pool multiruolo degli ambienti di hosting.|
|/hostingenvironments/multirolepools/skus/read|Ottiene gli SKU dei pool multiruolo degli ambienti di hosting.|
|/hostingenvironments/multirolepools/usages/read|Ottiene gli utilizzi dei pool multiruolo degli ambienti di hosting.|
|/hostingenvironments/diagnostics/read|Ottiene la diagnostica degli ambienti di hosting.|
|/publishingusers/read|Ottiene gli utenti di pubblicazione.|
|/publishingusers/write|Aggiorna gli utenti di pubblicazione.|
|/checknameavailability/read|Verifica se il nome della risorsa è disponibile.|
|/geoRegions/Read|Ottiene l'elenco delle aree geografiche.|
|/sites/Read|Ottiene le proprietà di un'app Web|
|/sites/Write|Crea una nuova app Web o ne aggiorna una esistente|
|/sites/Delete|Eliminare un'app Web esistente|
|/sites/backup/Action|Crea il backup di una nuova app Web|
|/sites/publishxml/Action|Ottiene l'XML del profilo di pubblicazione di un'app Web|
|/sites/publish/Action|Pubblica un'app Web|
|/sites/restart/Action|Riavvia un'app Web|
|/sites/start/Action|Avvia un'app Web|
|/sites/stop/Action|Arresta un'app Web|
|/sites/slotsswap/Action|Scambia gli slot di distribuzione di un'app Web|
|/sites/slotsdiffs/Action|Ottiene le differenze di configurazione tra l'app Web e gli slot|
|/sites/applySlotConfig/Action|Applica la configurazione slot dell'app Web dallo slot di destinazione all'app Web corrente|
|/sites/resetSlotConfig/Action|Ripristina la configurazione dell'app Web|
|/sites/functions/action|Funzioni delle app Web.|
|/sites/listsyncfunctiontriggerstatus/action|Elenca le app Web di stato dei trigger delle funzioni.|
|/sites/networktrace/action|App Web di traccia della rete.|
|/sites/newpassword/action|Consente di creare una nuova password per app Web.|
|/sites/sync/action|App Web di sincronizzazione.|
|/sites/operationresults/read|Ottiene i risultati delle operazioni delle app Web.|
|/sites/webjobs/read|Ottiene i processi Web delle app Web.|
|/sites/backup/read|Ottiene il backup delle app Web.|
|/sites/backup/write|Aggiorna il backup delle app Web.|
|/sites/metricdefinitions/read|Ottiene le definizioni di metrica per le app Web.|
|/sites/metrics/read|Ottiene la metrica di app Web.|
|/sites/continuouswebjobs/delete|Elimina i processi Web continui delle app Web.|
|/sites/continuouswebjobs/read|Ottiene i processi Web continui delle app Web.|
|/sites/continuouswebjobs/start/action|Avvia i processi Web continui delle app Web.|
|/sites/continuouswebjobs/stop/action|Interrompe i processi Web continui delle app Web.|
|/sites/domainownershipidentifiers/read|Ottiene gli identificatori di proprietà del dominio delle app Web.|
|/sites/domainownershipidentifiers/write|Aggiorna gli identificatori di proprietà del dominio delle app Web.|
|/sites/premieraddons/delete|Elimina i componenti aggiuntivi Premier delle app Web.|
|/sites/premieraddons/read|Ottiene i componenti aggiuntivi Premier delle app Web.|
|/sites/premieraddons/write|Aggiorna i componenti aggiuntivi Premier delle app Web.|
|/sites/triggeredwebjobs/delete|Elimina i processi Web attivati delle app Web.|
|/sites/triggeredwebjobs/read|Ottiene i processi Web attivati delle app Web.|
|/sites/triggeredwebjobs/run/action|Esegue i processi Web attivati delle app Web.|
|/sites/hostnamebindings/delete|Elimina i binding nome host delle app Web.|
|/sites/hostnamebindings/read|Ottiene i binding nome host delle app Web.|
|/sites/hostnamebindings/write|Aggiorna i binding nome host delle app Web.|
|/sites/virtualnetworkconnections/delete|Elimina le connessioni di rete virtuale delle app Web.|
|/sites/virtualnetworkconnections/read|Ottiene le connessioni di rete virtuale delle app Web.|
|/sites/virtualnetworkconnections/write|Aggiorna le connessioni di rete virtuale delle app Web.|
|/sites/virtualnetworkconnections/gateways/read|Ottiene i gateway delle connessioni di rete virtuale delle app Web.|
|/sites/virtualnetworkconnections/gateways/write|Aggiorna i gateway delle connessioni di rete virtuale delle app Web.|
|/sites/publishxml/read|Ottiene l'XML di pubblicazione delle app Web.|
|/sites/hybridconnectionrelays/read|Ottiene i relay di connessione ibrida delle app Web.|
|/sites/perfcounters/read|Ottiene i contatori delle prestazioni delle app Web.|
|/sites/usages/read|Ottiene gli utilizzi delle app Web.|
|/sites/slots/Write|Crea un nuovo slot di app Web o ne aggiorna uno esistente|
|/sites/slots/Delete|Elimina uno slot di app Web esistente|
|/sites/slots/backup/Action|Crea un nuovo backup di slot di app Web.|
|/sites/slots/publishxml/Action|Ottiene l'XML del profilo di pubblicazione per uno slot di app Web|
|/sites/slots/publish/Action|Pubblica uno slot di app Web|
|/sites/slots/restart/Action|Riavvia uno slot di app Web|
|/sites/slots/start/Action|Avvia uno slot di app Web|
|/sites/slots/stop/Action|Arresta uno slot di app Web|
|/sites/slots/slotsswap/Action|Scambia gli slot di distribuzione di un'app Web|
|/sites/slots/slotsdiffs/Action|Ottiene le differenze di configurazione tra l'app Web e gli slot|
|/sites/slots/applySlotConfig/Action|Applica la configurazione slot dell'app Web dallo slot di destinazione allo slot corrente.|
|/sites/slots/resetSlotConfig/Action|Ripristina la configurazione dello slot di app Web|
|/sites/slots/Read|Ottiene le proprietà di uno slot di distribuzione di app Web|
|/sites/slots/newpassword/action|Consente di creare una nuova password per slot di app Web.|
|/sites/slots/sync/action|Sincronizza gli slot di app Web.|
|/sites/slots/operationresults/read|Ottiene i risultati delle operazioni degli slot di app Web.|
|/sites/slots/webjobs/read|Ottiene i processi Web degli slot per le app Web.|
|/sites/slots/backup/write|Aggiorna il backup degli slot per le app Web.|
|/sites/slots/metricdefinitions/read|Ottiene le definizioni metrica degli slot per le app Web.|
|/sites/slots/metrics/read|Ottiene la metrica degli slot per le app Web.|
|/sites/slots/continuouswebjobs/delete|Elimina i processi Web continui degli slot per le app Web.|
|/sites/slots/continuouswebjobs/read|Ottiene i processi Web continui degli slot per le app Web.|
|/sites/slots/continuouswebjobs/start/action|Avvia i processi Web continui degli slot per le app Web.|
|/sites/slots/continuouswebjobs/stop/action|Arresta i processi Web continui degli slot per le app Web.|
|/sites/slots/premieraddons/delete|Elimina i componenti aggiuntivi Premier degli slot per le app Web.|
|/sites/slots/premieraddons/read|Ottiene i componenti aggiuntivi Premier degli slot per le app Web.|
|/sites/slots/premieraddons/write|Aggiorna i componenti aggiuntivi Premier degli slot per le app Web.|
|/sites/slots/triggeredwebjobs/delete|Elimina i processi Web attivati degli slot per le app Web.|
|/sites/slots/triggeredwebjobs/read|Ottiene i processi Web attivati degli slot per le app Web.|
|/sites/slots/triggeredwebjobs/run/action|Esegue i processi Web attivati degli slot per le app Web.|
|/sites/slots/hostnamebindings/delete|Elimina le associazioni nome host degli slot per le app Web.|
|/sites/slots/hostnamebindings/read|Ottiene le associazioni nome host degli slot per le app Web.|
|/sites/slots/hostnamebindings/write|Aggiorna le associazioni nome host degli slot per le app Web.|
|/sites/slots/phplogging/read|Ottiene la registrazione PHP degli slot per le app Web.|
|/sites/slots/virtualnetworkconnections/delete|Elimina le connessioni rete virtuale degli slot per le app Web.|
|/sites/slots/virtualnetworkconnections/read|Ottiene le connessioni rete virtuale degli slot per le app Web.|
|/sites/slots/virtualnetworkconnections/write|Aggiorna le connessioni rete virtuale degli slot per le app Web.|
|/sites/slots/virtualnetworkconnections/gateways/write|Aggiorna i gateway delle connessioni rete virtuale degli slot per le app Web.|
|/sites/slots/usages/read|Ottiene gli utilizzi degli slot per le app Web.|
|/sites/slots/hybridconnection/delete|Elimina la connessione ibrida degli slot per le app Web.|
|/sites/slots/hybridconnection/read|Ottiene la connessione ibrida degli slot per le app Web.|
|/sites/slots/hybridconnection/write|Aggiorna la connessione ibrida degli slot per le app Web.|
|/sites/slots/config/Read|Ottiene le impostazioni di configurazione degli slot per le app Web|
|/sites/slots/config/list/Action|Elenca le impostazioni importanti per la sicurezza degli slot per le app Web, quali le credenziali di pubblicazione, le impostazioni delle app e le stringhe di connessione|
|/sites/slots/config/Write|Aggiorna le impostazioni di configurazione degli slot per le app Web|
|/sites/slots/config/delete|Elimina la configurazione degli slot per le app Web.|
|/sites/slots/instances/read|Ottiene le istanze degli slot per le app Web.|
|/sites/slots/instances/processes/read|Ottiene i processi delle istanze degli slot per le app Web.|
|/sites/slots/instances/deployments/read|Ottiene le distribuzioni delle istanze degli slot per le app Web.|
|/sites/slots/sourcecontrols/Read|Ottiene le impostazioni di configurazione del controllo codice sorgente degli slot per le app Web|
|/sites/slots/sourcecontrols/Write|Aggiorna le impostazioni di configurazione del controllo codice sorgente degli slot per le app Web|
|/sites/slots/sourcecontrols/Delete|Elimina le impostazioni di configurazione del controllo codice sorgente degli slot per le app Web|
|/sites/slots/restore/read|Ottiene il ripristino degli slot per le app Web.|
|/sites/slots/analyzecustomhostname/read|Ottiene l'analisi nome host personalizzato degli slot per le app Web.|
|/sites/slots/backups/Read|Ottiene le proprietà del backup di uno slot delle app Web|
|/sites/slots/backups/list/action|Elenca i backup degli slot per le app Web.|
|/sites/slots/backups/restore/action|Ripristina i backup degli slot per le app Web.|
|/sites/slots/deployments/delete|Elimina le distribuzioni degli slot per le app Web.|
|/sites/slots/deployments/read|Ottiene le distribuzioni degli slot per le app Web.|
|/sites/slots/deployments/write|Aggiorna le distribuzioni degli slot per le app Web.|
|/sites/slots/deployments/log/read|Ottiene il registro delle distribuzioni degli slot per le app Web.|
|/sites/hybridconnection/delete|Elimina la connessione ibrida delle app Web.|
|/sites/hybridconnection/read|Ottiene la connessione ibrida delle app Web.|
|/sites/hybridconnection/write|Aggiorna la connessione ibrida delle app Web.|
|/sites/recommendationhistory/read|Ottiene la cronologia consigli delle app Web.|
|/sites/recommendations/Read|Ottiene l'elenco dei consigli per l'app Web.|
|/sites/recommendations/disable/action|Disattiva i consigli delle app Web.|
|/sites/config/Read|Ottiene le impostazioni di configurazione delle app Web|
|/sites/config/list/Action|Elenca le impostazioni dell'app Web sensibili alla sicurezza, ad esempio le credenziali di pubblicazione, le impostazioni dell'app e le stringhe di connessione|
|/sites/config/Write|Aggiorna le impostazioni di configurazione dell'app Web|
|/sites/config/delete|Elimina la configurazione delle app Web.|
|/sites/instances/read|Ottiene le istanze delle app Web.|
|/sites/instances/processes/delete|Elimina i processi delle istanze delle app Web.|
|/sites/instances/processes/read|Ottiene i processi delle istanze delle app Web.|
|/sites/instances/deployments/read|Ottiene le distribuzioni delle istanze delle app Web.|
|/sites/sourcecontrols/Read|Ottiene le impostazioni di configurazione del controllo codice sorgente dell'app Web|
|/sites/sourcecontrols/Write|Aggiorna le impostazioni di configurazione del controllo codice sorgente dell'app Web|
|/sites/sourcecontrols/Delete|Elimina le impostazioni di configurazione del controllo codice sorgente dell'app Web|
|/sites/restore/read|Ottiene il ripristino delle app Web.|
|/sites/analyzecustomhostname/read|Analizza il nome host personalizzato.|
|/sites/backups/Read|Ottiene le proprietà del backup di un'app Web|
|/sites/backups/list/action|Elenca i backup delle app Web.|
|/sites/backups/restore/action|Ripristina i backup delle app Web.|
|/sites/snapshots/read|Ottiene snapshot delle app Web.|
|/sites/functions/delete|Elimina funzioni delle app Web.|
|/sites/functions/listsecrets/action|Elenca i segreti delle funzioni delle app Web.|
|/sites/functions/read|Ottiene le funzioni delle app Web.|
|/sites/functions/write|Aggiorna le funzioni delle app Web.|
|/sites/deployments/delete|Elimina le distribuzioni delle app Web.|
|/sites/deployments/read|Ottiene le distribuzioni delle app Web.|
|/sites/deployments/write|Aggiorna le distribuzioni delle app Web.|
|/sites/deployments/log/read|Ottiene il registro delle distribuzioni delle app Web.|
|/sites/diagnostics/read|Ottiene la diagnostica delle app Web.|
|/sites/diagnostics/workerprocessrecycle/read|Ottiene il riciclo del processo di lavoro di diagnostica delle app Web.|
|/sites/diagnostics/workeravailability/read|Ottiene la disponibilità del processo di lavoro di diagnostica delle app Web.|
|/sites/diagnostics/runtimeavailability/read|Ottiene la disponibilità del runtime di diagnostica delle app Web.|
|/sites/diagnostics/cpuanalysis/read|Ottiene l'analisi CPU della diagnostica delle app Web.|
|/sites/diagnostics/servicehealth/read|Ottiene l'integrità di servizio della diagnostica delle app Web.|
|/sites/diagnostics/frebanalysis/read|Ottiene l'analisi FREB della diagnostica delle app Web.|
|/availablestacks/read|Ottiene gli stack disponibili.|
|/isusernameavailable/read|Verifica se il nome utente è disponibile.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Read|Ottiene l'elenco delle API.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Write|Aggiunge una nuova API o ne aggiorna una esistente.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Delete|Elimina un'API esistente.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Read|Ottiene l'elenco delle connessioni.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Write|Salva una nuova connessione o ne aggiorna una esistente.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Delete|Elimina una connessione esistente.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Read|Legge gli ACL della connessione|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Write|Aggiunge o aggiorna un ACL della connessione|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Delete|Elimina un ACL della connessione|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connectionAcls/Read|Legge gli ACL della connessione per l'API|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Read|Legge gli ACL della connessione|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Write|Crea o aggiorna gli ACL dell'API|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Delete|Elimina gli ACL dell'API|
|/serverfarms/Read|Ottiene le proprietà per un piano di servizio app|
|/serverfarms/Write|Crea un nuovo piano di servizio app o ne aggiorna uno esistente|
|/serverfarms/Delete|Eliminare un piano di servizio app esistente|
|/serverfarms/restartSites/Action|Riavvia tutte le app Web in un piano di servizio app|
|/serverfarms/operationresults/read|Ottiene i risultati dell'operazione per i piani di servizio app.|
|/serverfarms/capabilities/read|Ottiene le capacità dei piani di servizio app.|
|/serverfarms/metricdefinitions/read|Ottiene le definizioni metrica dei piani di servizio app.|
|/serverfarms/metrics/read|Ottiene la metrica dei piani di servizio app.|
|/serverfarms/hybridconnectionplanlimits/read|Ottiene i limiti del piano di connessione ibrida per i piani di servizio app.|
|/serverfarms/virtualnetworkconnections/read|Ottiene le connessioni rete virtuale dei piani di servizio app.|
|/serverfarms/virtualnetworkconnections/routes/delete|Elimina i percorsi delle connessioni rete virtuale dei piani di servizio app.|
|/serverfarms/virtualnetworkconnections/routes/read|Ottiene i percorsi delle connessioni rete virtuale dei piani di servizio app.|
|/serverfarms/virtualnetworkconnections/routes/write|Aggiorna i percorsi delle connessioni rete virtuale dei piani di servizio app.|
|/serverfarms/virtualnetworkconnections/gateways/write|Aggiorna i gateway delle connessioni rete virtuale dei piani di servizio app.|
|/serverfarms/firstpartyapps/settings/delete|Elimina le impostazioni delle app proprietarie dei piani di servizio app.|
|/serverfarms/firstpartyapps/settings/read|Ottiene le impostazioni delle app proprietarie dei piani di servizio app.|
|/serverfarms/firstpartyapps/settings/write|Aggiorna le impostazioni delle app proprietarie dei piani di servizio app.|
|/serverfarms/sites/read|Ottiene le app Web dei piani di servizio app.|
|/serverfarms/workers/reboot/action|Riavvia i ruoli di lavoro dei piani di servizio app.|
|/serverfarms/hybridconnectionrelays/read|Ottiene gli inoltri connessione ibrida dei piani di servizio app.|
|/serverfarms/skus/read|Ottiene gli SKU dei piani di servizio app.|
|/serverfarms/usages/read|Ottiene gli utilizzi dei piani di servizio app.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|Ottiene le app Web degli inoltri spazio dei nomi connessione ibrida dei piani di servizio app.|
|/ishostnameavailable/read|Verifica se il nome host è disponibile.|
|/connectionGateways/Read|Ottiene l'elenco dei gateway di connessione.|
|/connectionGateways/Write|Crea o aggiorna un gateway di connessione.|
|/connectionGateways/Delete|Elimina un gateway di connessione.|
|/connectionGateways/Join/Action|Unisce un gateway di connessione.|
|/classicmobileservices/read|Ottiene servizi mobili classici.|
|/skus/read|Ottiene SKU.|
|/certificates/Read|Ottiene l'elenco dei certificati.|
|/certificates/Write|Aggiunge un nuovo certificato o ne aggiorna uno esistente.|
|/certificates/Delete|Elimina un certificato esistente.|
|/operations/read|Ottiene operazioni.|
|/recommendations/Read|Ottiene l'elenco di consigli per le sottoscrizioni.|
|/ishostingenvironmentnameavailable/read|Definisce se il nome dell'ambiente di hosting è disponibile.|
|/apiManagementAccounts/Read|Ottiene l'elenco di account di Gestione API.|
|/apiManagementAccounts/Write|Aggiunge un nuovo account di Gestione API o ne aggiorna uno esistente|
|/apiManagementAccounts/Delete|Elimina un account di Gestione API esistente|
|/apiManagementAccounts/connectionAcls/Read|Ottiene l'elenco degli ACL della connessione.|
|/apiManagementAccounts/apiAcls/Read|Legge gli ACL della connessione|
|/connections/Read|Ottiene l'elenco delle connessioni.|
|/connections/Write|Crea o aggiorna una connessione.|
|/connections/Delete|Elimina una connessione.|
|/connections/Join/Action|Unisce una connessione.|
|/connections/confirmconsentcode/action|Conferma il codice di consenso delle connessioni.|
|/connections/listconsentlinks/action|Elenca i collegamenti di consenso per le connessioni.|
|/deploymentlocations/read|Ottiene i percorsi di distribuzione.|
|/sourcecontrols/read|Ottiene i controlli del codice sorgente.|
|/sourcecontrols/write|Aggiorna i controlli del codice sorgente.|
|/managedhostingenvironments/read|Ottiene gli ambienti di hosting gestiti.|
|/managedhostingenvironments/sites/read|Ottiene le app Web degli ambienti di hosting gestiti.|
|/managedhostingenvironments/serverfarms/read|Ottiene i piani di servizio app degli ambienti di hosting gestiti.|
|/locations/managedapis/read|Ottiene le API gestite dei percorsi.|
|/locations/apioperations/read|Ottiene le operazioni delle API dei percorsi.|
|/locations/connectiongatewayinstallations/read|Ottiene le installazioni dei gateway di connessione dei percorsi.|
|/listSitesAssignedToHostName/Read|Ottiene i nomi dei siti assegnati al nome host.|

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un ruolo personalizzato](role-based-access-control-custom-roles.md).
- [Ruoli RBAC incorporati](role-based-access-built-in-roles.md).
- Informazioni sulla gestione delle assegnazioni di accesso [per utente](role-based-access-control-manage-assignments.md) o [per risorsa](role-based-access-control-configure.md) 
- Informazioni su come [visualizzare i log attività per controllare le azioni sulle risorse](~/articles/azure-resource-manager/resource-group-audit.md)
