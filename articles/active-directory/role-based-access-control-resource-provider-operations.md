---
title: Operazioni di provider con Azure Resource Manager | Documentazione Microsoft
description: Fornisce informazioni dettagliate sulle operazioni disponibili nei provider di risorse di Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.openlocfilehash: 0b8c8823c6d21df96dcfd926db1855169f1570e4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operazioni di provider di risorse con Azure Resource Manager

Questo documento elenca le operazioni disponibili per ciascun provider di risorse Microsoft Azure Resource Manager. Tali operazioni possono essere utilizzate nei ruoli personalizzati per fornire autorizzazioni RBAC (controllo degli accessi in base al ruolo) granulari alle risorse di Azure. L’elenco qui fornito non è da ritenersi completo e le operazioni potrebbero essere aggiunte o rimosse man mano che il provider viene aggiornato. Le stringhe di operazione seguono il formato di `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Per un elenco completo e aggiornato delle operazioni dei provider di risorse di Azure usare `Get-AzureRmProviderOperation` (in PowerShell) o `az provider operation list` (nell'interfaccia della riga di comando di Azure versione 2).

## <a name="microsoftaad"></a>Microsoft.AAD

| Operazione | DESCRIZIONE |
|---|---|
|/domainServices/delete|Elimina i servizi di dominio.|
|/domainServices/read|Legge i servizi di dominio.|
|/domainServices/write|Scrive i servizi di dominio|
|/locations/operationresults/read|Legge lo stato di un'operazione asincrona.|
|/Operations/read|La descrizione discorsiva localizzata per l'operazione che deve essere visualizzata all'utente.|

## <a name="microsoftaadiam"></a>microsoft.aadiam

| Operazione | DESCRIZIONE |
|---|---|
|/diagnosticsettings/delete|Eliminazione di un'impostazione di diagnostica|
|/diagnosticsettings/read|Lettura di un'impostazione di diagnostica|
|/diagnosticsettings/write|Scrittura di un'impostazione di diagnostica|
|/diagnosticsettingscategories/read|Lettura delle categorie di un'impostazione di diagnostica|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/tenants/providers/Microsoft.Insights/logDefinitions/read|Ottiene i log disponibili per il tenant|

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Operazione | DESCRIZIONE |
|---|---|
|/configuration/action|Aggiorna la configurazione tenant.|
|/configuration/read|Legge la configurazione tenant.|
|/configuration/write|Crea una configurazione tenant.|
|/services/action|Aggiorna un'istanza del servizio nel tenant.|
|/services/alerts/read|Legge gli avvisi per un servizio.|
|/services/alerts/read|Legge gli avvisi per un servizio.|
|/services/delete|Elimina un'istanza del servizio nel tenant.|
|/services/read|Legge le istanze del servizio nel tenant.|
|/services/servicemembers/action|Crea un'istanza del membro del servizio nel servizio.|
|/services/servicemembers/alerts/read|Legge gli avvisi per un membro del servizio.|
|/services/servicemembers/delete|Elimina un'istanza del membro del servizio nel servizio.|
|/services/servicemembers/read|Legge l'istanza del membro del servizio nel servizio.|
|/services/write|Aggiorna un'istanza del servizio nel tenant.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Operazione | DESCRIZIONE |
|---|---|
|/configurations/read|Get configurations|
|/configurations/write|Crea/aggiorna la configurazione|
|/generateRecommendations/action|Genera suggerimenti.|
|/generateRecommendations/read|Ottiene lo stato dei suggerimenti generati.|
|/operations/read|Ottiene le operazioni per Microsoft Advisor|
|/recommendations/read|Legge i suggerimenti.|
|/recommendations/suppressions/delete|Cancella le eliminazioni.|
|/recommendations/suppressions/read|Ottiene le eliminazioni.|
|/recommendations/suppressions/write|Crea/aggiorna eliminazioni.|
|/register/action|Registra la sottoscrizione per Microsoft Advisor.|
|/suppressions/delete|Cancella le eliminazioni.|
|/suppressions/read|Ottiene le eliminazioni.|
|/suppressions/write|Crea/aggiorna eliminazioni.|
|/unregister/action|Annulla la registrazione della sottoscrizione per Microsoft Advisor|

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

| Operazione | DESCRIZIONE |
|---|---|
|/alerts/read|Ottiene tutti gli avvisi per i filtri di input.|
|/alerts/resolve/action|Modifica lo stato dell'avviso in "Risoluzione"|
|/alertsSummary/read|Ottiene il riepilogo degli avvisi|
|/Operations/read|Legge le operazioni fornite|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Operazione | DESCRIZIONE |
|---|---|
|/locations/checkNameAvailability/action|Verifica che il nome dell’Analysis Server specificato sia valido e non in uso.|
|/locations/operationresults/read|Recupera le informazioni del risultato dell'operazione specificata.|
|/locations/operationstatuses/read|Recupera le informazioni dello stato dell'operazione specificata.|
|/operations/read|Recupera le informazioni delle operazioni|
|/register/action|Registra il provider di risorse di Analysis Services.|
|/servers/delete|Elimina l’Analysis Server.|
|/servers/listGatewayStatus/action|Elenca lo stato del gateway associato al server.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per Analysis Server|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per Analysis Server|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Ottiene i log disponibili per i server|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per Analysis Server|
|/servers/read|Recupera le informazioni dell’Analysis Server specificato.|
|/servers/resume/action|Riprende l’Analysis Server.|
|/servers/skus/read|Recupera informazioni sugli SKU disponibili per il server|
|/servers/suspend/action|Sospende l’Analysis Server.|
|/servers/write|Crea o aggiorna l’Analysis Server specificato.|
|/skus/read|Recupera le informazioni degli SKU|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Operazione | DESCRIZIONE |
|---|---|
|/checkNameAvailability/read|Verifica che il nome del servizio sia disponibile.|
|/operations/read|Legge tutte le operazioni API disponibili per la risorsa Microsoft.ApiManagement|
|/register/action|Registra la sottoscrizione per il provider di risorse Microsoft.ApiManagement.|
|/reports/read|Ottiene report aggregati per periodi di tempo, area geografica, sviluppatori, prodotti, API, operazioni, sottoscrizione e richiesta.|
|/service/apis/delete|Rimuove una API esistente|
|/service/apis/diagnostics/delete|Rimuove le informazioni di diagnostica esistenti|
|/service/apis/diagnostics/loggers/delete|Rimuove il mapping di un logger con un'impostazione di diagnostica|
|/service/apis/diagnostics/loggers/read|Ottiene l'elenco dei logger di diagnostica esistenti|
|/service/apis/diagnostics/loggers/write|Esegue il mapping del logger a un'impostazione di diagnostica|
|/service/apis/diagnostics/read|Ottiene l'elenco delle informazioni di diagnostica o ottiene informazioni di diagnostica dettagliate|
|/service/apis/diagnostics/write|Aggiunge nuove informazioni di diagnostica o aggiorna informazioni di diagnostica dettagliate esistenti|
|/service/apis/operations/delete|Rimuove un’operazione API esistente|
|/service/apis/operations/policies/delete|Rimuove la configurazione dei criteri dai criteri delle operazioni API|
|/service/apis/operations/policies/read|Ottiene i criteri per l'operazione API o ottiene i dettagli di configurazione dei criteri per l'operazione API|
|/service/apis/operations/policies/write|Imposta i dettagli di configurazione dei criteri per l'operazione API|
|/service/apis/operations/policy/delete|Rimuove la configurazione dei criteri dall’operazione|
|/service/apis/operations/policy/read|Ottiene i dettagli di configurazione dei criteri per l’operazione|
|/service/apis/operations/policy/write|Imposta i dettagli di configurazione dei criteri per l’operazione|
|/service/apis/operations/read|Ottiene l’elenco di operazioni API esistenti o i dettagli dell’operazione API|
|/service/apis/operations/tags/delete|Elimina l'associazione di un tag esistente con un'operazione esistente|
|/service/apis/operations/tags/read|Ottiene i tag associati con l'operazione o ottiene i dettagli dei tag|
|/service/apis/operations/tags/write|Associa un tag esistente con un'operazione esistente|
|/service/apis/operations/write|Crea una nuova operazione API o ne aggiorna una esistente|
|/service/apis/operationsByTags/read|Ottiene l'elenco delle associazioni operazione/tag|
|/service/apis/policies/delete|Rimuove la configurazione dei criteri dai criteri API|
|/service/apis/policies/read|Ottiene i criteri per l'API o ottiene i dettagli di configurazione dei criteri per l'API|
|/service/apis/policies/write|Imposta i dettagli di configurazione dei criteri per l’API|
|/service/apis/policy/delete|Rimuove la configurazione dei criteri dalla API|
|/service/apis/policy/read|Ottiene i dettagli di configurazione dei criteri per l’API|
|/service/apis/policy/write|Imposta i dettagli di configurazione dei criteri per l’API|
|/service/apis/products/read|Ottiene tutti i prodotti di cui l'API fa parte|
|/service/apis/read|Ottiene l’elenco di tutte le API registrate o i dettagli delle API|
|/service/apis/releases/delete|Rimuove tutte le versioni dell'API o rimuove la versione dell'API|
|/service/apis/releases/read|Ottiene le versioni per un'API o ottiene i dettagli della versione dell'API|
|/service/apis/releases/write|Crea una nuova versione dell'API o aggiorna una versione dell'API esistente|
|/service/apis/revisions/delete|Rimuove tutte le revisioni di un'API|
|/service/apis/revisions/read|Ottiene le revisioni appartenenti a un'API|
|/service/apis/schemas/delete|Rimuove lo schema esistente|
|/service/apis/schemas/document/read|Ottiene il documento che descrive lo schema|
|/service/apis/schemas/document/write|Aggiorna il documento che descrive lo schema|
|/service/apis/schemas/read|Ottiene tutti gli schemi per un'API specificata o ottiene gli schemi utilizzati dall'API|
|/service/apis/schemas/write|Imposta gli schemi utilizzati dall'API|
|/service/apis/tagDescriptions/delete|Rimuove la descrizione del tag dall'API|
|/service/apis/tagDescriptions/read|Ottiene le descrizioni dei tag nell'ambito dell'API o ottiene la descrizione del tag nell'ambito dell'API|
|/service/apis/tagDescriptions/write|Crea/modifica la descrizione del tag nell'ambito dell'API|
|/service/apis/tags/delete|Rimuove l'associazione API/tag esistente|
|/service/apis/tags/read|Ottiene tutte le associazioni di API/tag per l'API o ottiene i dettagli dell'associazione API/tag|
|/service/apis/tags/write|Aggiunge una nuova associazione API/tag|
|/service/apis/write|Crea una nuova API o aggiorna i dettagli di una API esistente|
|/service/apisByTags/read|Ottiene l'elenco delle associazioni API/tag|
|/service/api-version-sets/delete|Rimuove un VersionSet esistente|
|/service/api-version-sets/read|Ottiene l'elenco delle entità gruppo versione o ottiene i dettagli di un VersionSet|
|/service/api-version-sets/versions/read|Ottiene l'elenco delle entità versione|
|/service/api-version-sets/write|Crea un nuovo VersionSet o aggiorna i dettagli di un VersionSet esistente|
|/service/applynetworkconfigurationupdates/action|Aggiorna le risorse Microsoft.ApiManagement in esecuzione nella rete virtuale per selezionare le impostazioni di rete aggiornate.|
|/service/authorizationServers/delete|Rimuove il server di autorizzazione esistente.|
|/service/authorizationServers/read|Ottiene l'elenco dei server di autorizzazione o i dettagli del server di autorizzazione.|
|/service/authorizationServers/write|Crea un nuovo server di autorizzazione o aggiorna i dettagli di un server di autorizzazione esistente.|
|/service/backends/delete|Rimuove il back-end esistente|
|/service/backends/read|Ottiene l’elenco di back-end o i dettagli del back-end|
|/service/backends/reconnect/action|Crea una richiesta di riconnessione|
|/service/backends/write|Aggiunge un nuovo back-end o aggiorna i dettagli di un back-end esistente|
|/service/backup/action|Esegue il backup del servizio Gestione API nel contenitore specificato in un account di archiviazione fornito dall’utente.|
|/service/certificates/delete|Elimina un certificato esistente.|
|/service/certificates/read|Ottiene l'elenco di certificati o i dettagli del certificato.|
|/service/certificates/write|Aggiunge un nuovo certificato.|
|/service/delete|Elimina l’istanza del servizio Gestione API.|
|/service/diagnostics/delete|Rimuove le informazioni di diagnostica esistenti|
|/service/diagnostics/loggers/delete|Rimuove il mapping di un logger con un'impostazione di diagnostica|
|/service/diagnostics/loggers/read|Ottiene l'elenco dei logger di diagnostica esistenti|
|/service/diagnostics/loggers/write|Esegue il mapping del logger a un'impostazione di diagnostica|
|/service/diagnostics/read|Ottiene l'elenco delle informazioni di diagnostica o ottiene informazioni di diagnostica dettagliate|
|/service/diagnostics/write|Aggiunge nuove informazioni di diagnostica o aggiorna informazioni di diagnostica dettagliate esistenti|
|/service/getssotoken/action|Ottiene il token SSO che può essere utilizzato per l'accesso al portale legacy del servizio Gestione API come amministratore.|
|/service/groups/delete|Rimuove il gruppo esistente.|
|/service/groups/read|Ottiene l'elenco dei gruppi o i dettagli di un gruppo.|
|/service/groups/users/delete|Rimuove l’utente esistente dal gruppo esistente.|
|/service/groups/users/read|Ottiene l'elenco degli utenti del gruppo.|
|/service/groups/users/write|Aggiunge l’utente esistente al gruppo esistente.|
|/service/groups/write|Crea un nuovo gruppo o aggiorna i dettagli di un gruppo esistente.|
|/service/identityProviders/delete|Rimuove un provider di identità esistente|
|/service/identityProviders/read|Ottiene l’elenco di provider di identità o i dettagli del provider di identità|
|/service/identityProviders/write|Crea un nuovo provider di identità o aggiorna i dettagli di un provider di identità esistente|
|/service/locations/networkstatus/read|Ottiene lo stato di accesso di rete delle risorse da cui il servizio dipende nella posizione.|
|/service/loggers/delete|Rimuove un logger esistente|
|/service/loggers/read|Ottiene l'elenco di logger o i dettagli del logger|
|/service/loggers/write|Aggiunge un nuovo logger o aggiorna i dettagli di un logger esistente|
|/service/managedeployments/action|Modifica SKU/unità, aggiunge/rimuove distribuzioni regionali del servizio Gestione API.|
|/service/networkstatus/read|Ottiene lo stato di accesso di rete delle risorse da cui il servizio dipende.|
|/service/notifications/action|Invia notifiche a un utente specifico|
|/service/notifications/read|Ottiene tutte le notifiche del server di pubblicazione di Gestione API o ottiene i dettagli delle notifiche del server di pubblicazione di Gestione API|
|/service/notifications/recipientEmails/delete|Rimuove l'indirizzo di posta elettronica associato alle notifiche|
|/service/notifications/recipientEmails/read|Ottiene i destinatari di posta elettronica associati alle notifiche del server di pubblicazione di Gestione API|
|/service/notifications/recipientEmails/write|Crea un nuovo destinatario di posta elettronica delle notifiche|
|/service/notifications/recipientUsers/delete|Rimuove l'utente associato ai destinatari delle notifiche|
|/service/notifications/recipientUsers/read|Ottiene gli utenti destinatari associati alle notifiche|
|/service/notifications/recipientUsers/write|Aggiunge un utente ai destinatari delle notifiche|
|/service/notifications/write|Crea o aggiorna le notifiche del server di pubblicazione di Gestione API|
|/service/openidConnectProviders/delete|Rimuove il provider di OpenID Connect esistente.|
|/service/openidConnectProviders/read|Ottiene l'elenco di provider di OpenID Connect o le informazioni dettagliate del Provider di OpenID Connect.|
|/service/openidConnectProviders/write|Crea un nuovo provider OpenID Connect o aggiorna i dettagli di un provider di OpenID Connect esistente.|
|/service/operationresults/read|Ottiene lo stato corrente dell'operazione in esecuzione prolungata|
|/service/policies/delete|Rimuove la configurazione dei criteri dai criteri del tenant|
|/service/policies/read|Ottiene i criteri per il tenant o ottiene i dettagli di configurazione dei criteri per il tenant|
|/service/policies/write|Imposta i dettagli di configurazione dei criteri per il tenant|
|/service/policySnippets/read|Ottiene tutti i frammenti di codice dei criteri|
|/service/portalsettings/read|Ottiene le impostazioni di registrazione per il portale o ottiene le impostazioni di accesso per il portale o ottiene le impostazioni di delega per il portale|
|/service/portalsettings/write|Aggiorna le impostazioni di registrazione per il portale o aggiorna le impostazioni di accesso per il portale o aggiorna le impostazioni di delega per il portale|
|/service/products/apis/delete|Rimuove le API esistenti dal prodotto esistente.|
|/service/products/apis/read|Ottiene l'elenco delle API aggiunte al prodotto esistente.|
|/service/products/apis/write|Aggiunge le API esistenti al prodotto esistente.|
|/service/products/delete|Rimuove il prodotto esistente.|
|/service/products/groups/delete|Elimina l’associazione del gruppo di sviluppatori esistenti al prodotto esistente.|
|/service/products/groups/read|Ottiene l'elenco di gruppi di sviluppatori associato al prodotto.|
|/service/products/groups/write|Associa il gruppo di sviluppatori esistenti al prodotto esistente.|
|/service/products/policies/delete|Rimuove la configurazione dei criteri dai criteri del prodotto|
|/service/products/policies/read|Ottiene i criteri per il prodotto o ottiene i dettagli di configurazione dei criteri per il prodotto|
|/service/products/policies/write|Imposta i dettagli di configurazione dei criteri per il prodotto|
|/service/products/policy/delete|Rimuove la configurazione dei criteri del prodotto esistente.|
|/service/products/policy/read|Ottiene la configurazione dei criteri del prodotto esistente.|
|/service/products/policy/write|Imposta la configurazione dei criteri del prodotto esistente.|
|/service/products/read|Ottiene l’elenco dei prodotti o i dettagli di un prodotto|
|/service/products/subscriptions/read|Ottiene l'elenco delle sottoscrizioni di prodotto.|
|/service/products/tags/delete|Elimina l'associazione di un tag esistente con un prodotto esistente|
|/service/products/tags/read|Ottiene i tag associati con il prodotto o ottiene i dettagli dei tag|
|/service/products/tags/write|Associa un tag esistente con un prodotto esistente|
|/service/products/write|Crea nuovo prodotto o aggiorna i dettagli del prodotto esistente.|
|/service/properties/delete|Rimuove la proprietà esistente.|
|/service/properties/read|Ottiene l'elenco di tutte le proprietà o i dettagli della proprietà specificata.|
|/service/properties/write|Crea una nuova proprietà o aggiorna il valore per la proprietà specificata.|
|/service/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per il servizio Gestione API|
|/service/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per il servizio Gestione API|
|/service/providers/Microsoft.Insights/logDefinitions/read|Ottiene i log disponibili per il servizio Gestione API|
|/service/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per il servizio Gestione API|
|/service/quotas/periods/read|Ottiene il valore del contatore di quote per il periodo|
|/service/quotas/periods/write|Imposta il valore corrente del contatore di quote|
|/service/quotas/read|Ottiene i valori per la quota|
|/service/quotas/write|Imposta il valore corrente del contatore di quote|
|/service/read|Leggere i metadati per un'istanza del servizio Gestione API|
|/service/reports/read|Ottiene report aggregati per periodi di tempo o ottiene un report aggregato per area geografica o ottiene un report aggregato per sviluppatori. o ottiene un report aggregato per prodotti. o ottiene un report aggregato per API o ottiene un report aggregato per operazioni o ottiene un report aggregato per sottoscrizione. o ottiene i dati dei report sulle richieste|
|/service/restore/action|Ripristinare il servizio Gestione API dal contenitore specificato in un account di archiviazione fornito dall'utente|
|/service/subscriptions/delete|Elimina una sottoscrizione. Questa operazione può essere utilizzata per eliminare la sottoscrizione|
|/service/subscriptions/read|Ottiene un elenco di sottoscrizioni al prodotto o i dettagli di sottoscrizione a un prodotto|
|/service/subscriptions/regeneratePrimaryKey/action|Rigenera la chiave primaria di sottoscrizione|
|/service/subscriptions/regenerateSecondaryKey/action|Rigenera la chiave secondaria di sottoscrizione|
|/service/subscriptions/write|Effettua la sottoscrizione di un utente esistente a un prodotto esistente oppure aggiorna i dettagli di sottoscrizione esistenti. Questa operazione può essere utilizzata per rinnovare la sottoscrizione|
|/service/tagResources/read|Ottiene l'elenco dei tag con risorse associate|
|/service/tags/delete|Rimuove un tag esistente|
|/service/tags/read|Ottiene l'elenco dei tag o ottiene i dettagli del tag|
|/service/tags/write|Aggiunge un nuovo tag o aggiorna i dettagli di un tag esistente|
|/service/templates/delete|Reimposta il modello di posta elettronica predefinito di Gestione API|
|/service/templates/read|Ottiene tutti i modelli di posta elettronica o ottiene i dettagli del modello di posta elettronica di Gestione API|
|/service/templates/write|Crea o aggiorna il modello di posta elettronica di Gestione API o aggiorna il modello di posta elettronica di Gestione API|
|/service/tenant/delete|Rimuove la configurazione dei criteri per il tenant|
|/service/tenant/deploy/action|Esegue un'attività di distribuzione per applicare le modifiche dal ramo git specificato alla configurazione sul database.|
|/service/tenant/operationResults/read|Ottiene l'elenco dei risultati dell'operazione o il risultato di un'operazione specifica|
|/service/tenant/read|Ottiene la configurazione dei criteri per il tenant o ottiene i dettagli di accesso per il tenant|
|/service/tenant/regeneratePrimaryKey/action|Rigenera la chiave di accesso primaria|
|/service/tenant/regenerateSecondaryKey/action|Rigenera la chiave di accesso secondaria|
|/service/tenant/save/action|Crea commit con la snapshot di configurazione sul ramo specificato in archivio|
|/service/tenant/syncState/read|Ottiene lo stato dell’ultima sincronizzazione git|
|/service/tenant/validate/action|Convalida le modifiche dal ramo git specificato|
|/service/tenant/write|Imposta la configurazione dei criteri per il tenant o aggiorna i dettagli di accesso per il tenant|
|/service/updatecertificate/action|Carica il certificato SSL per un servizio Gestione API.|
|/service/updatehostname/action|Configura, aggiorna o rimuove i nomi di dominio personalizzati per un servizio Gestione API.|
|/service/users/action|Registra un nuovo utente|
|/service/users/applications/attachments/delete|Rimuove un allegato|
|/service/users/applications/attachments/read|Ottiene gli allegati dell'applicazione o ottiene un allegato|
|/service/users/applications/attachments/write|Aggiunge l'allegato all'applicazione|
|/service/users/applications/delete|Rimuove un'applicazione esistente|
|/service/users/applications/read|Ottiene l'elenco di tutte le applicazioni utente o ottiene i dettagli dell'applicazione di Gestione API|
|/service/users/applications/write|Registra un'applicazione in Gestione API o aggiorna i dettagli dell'applicazione|
|/service/users/delete|Rimuove l’account utente|
|/service/users/generateSsoUrl/action|Genera un URL SSO. L'URL può essere utilizzato per accedere al portale di amministrazione|
|/service/users/groups/read|Ottiene l'elenco di gruppi utente|
|/service/users/keys/read|Ottiene l'elenco di chiavi utente|
|/service/users/read|Ottiene un elenco di utenti registrati o i dettagli dell’account di un utente|
|/service/users/subscriptions/read|Ottiene l'elenco di sottoscrizioni utente|
|/service/users/token/action|Ottiene il token di accesso per un utente|
|/service/users/write|Registra un nuovo utente o aggiorna i dettagli dell’account di un utente esistente|
|/service/write|Creare una nuova istanza del servizio Gestione API|
|/unregister/action|Annulla la registrazione della sottoscrizione per il provider di risorse Microsoft.ApiManagement.|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Operazione | DESCRIZIONE |
|---|---|
|/checkAccess/action|Controlla se il chiamante è autorizzato a eseguire un'azione specifica|
|/classicAdministrators/delete|Rimuove l'amministratore dalla sottoscrizione.|
|/classicAdministrators/read|Esegue la lettura degli amministratori per la sottoscrizione.|
|/classicAdministrators/write|Aggiunge o modifica l'amministratore in una sottoscrizione.|
|/elevateAccess/action|Concede al chiamante l'accesso di tipo Amministratore Accesso utenti a livello dell'ambito del tenant|
|/locks/delete|Elimina i blocchi per l'ambito specificato.|
|/locks/read|Ottiene i blocchi per l'ambito specificato.|
|/locks/write|Aggiunge i blocchi per l'ambito specificato.|
|/permissions/read|Elenca tutte le autorizzazioni del chiamante per un ambito specifico.|
|/policyAssignments/delete|Elimina un'assegnazione di criteri per l'ambito specificato.|
|/policyAssignments/read|Ottiene informazioni su un'assegnazione di criteri.|
|/policyAssignments/write|Crea un'assegnazione di criteri per l'ambito specificato.|
|/policyDefinitions/delete|Elimina una definizione criteri.|
|/policyDefinitions/read|Ottiene informazioni su una definizione di criteri.|
|/policyDefinitions/write|Crea una definizione di criteri personalizzata.|
|/policySetDefinitions/delete|Elimina una definizione di set di criteri.|
|/policySetDefinitions/read|Ottiene informazioni su una definizione di set di criteri.|
|/policySetDefinitions/write|Crea una definizione di set di criteri personalizzata.|
|/providerOperations/read|Ottiene operazioni per tutti i provider di risorse che possono essere usati nelle definizioni del ruolo.|
|/roleAssignments/delete|È possibile eliminare un'assegnazione di ruolo nell'ambito specificato.|
|/roleAssignments/read|Ottiene informazioni su un'assegnazione di ruolo.|
|/roleAssignments/write|Crea un'assegnazione di ruolo per l'ambito specificato.|
|/roleDefinitions/delete|Elimina la definizione del ruolo personalizzata specificata.|
|/roleDefinitions/read|Ottiene informazioni su una definizione di ruolo.|
|/roleDefinitions/write|Crea o aggiorna una definizione del ruolo personalizzata con le autorizzazioni specificate e gli ambiti assegnabili.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Operazione | DESCRIZIONE |
|---|---|
|/automationAccounts/agentRegistrationInformation/read|Legge le informazioni di registrazione di un DSC di Automazione di Azure|
|/automationAccounts/agentRegistrationInformation/regenerateKey/action|Scrive una richiesta di rigenerazione delle chiavi di Automation DSC di Azure|
|/automationAccounts/certificates/delete|Elimina un asset del certificato di automazione di Azure|
|/automationAccounts/certificates/read|Ottiene un asset del certificato di automazione di Azure|
|/automationAccounts/certificates/write|Crea o aggiorna un asset del certificato di automazione di Azure|
|/automationAccounts/compilationjobs/read|Legge una compilazione Automation DSC di Azure|
|/automationAccounts/compilationjobs/write|Scrive una compilazione Automation DSC di Azure|
|/automationAccounts/configurations/delete|Elimina un contenuto di Automation DSC di Azure|
|/automationAccounts/configurations/getCount/action|Legge il conteggio di un contenuto di Automation DSC di Azure|
|/automationAccounts/configurations/read|Ottiene un contenuto di Automation DSC per Azure|
|/automationAccounts/configurations/write|Scrive un contenuto di Automation DSC di Azure|
|/automationAccounts/connections/delete|Elimina un asset della connessione di automazione di Azure|
|/automationAccounts/connections/read|Ottiene un asset della connessione di automazione di Azure|
|/automationAccounts/connections/write|Crea o aggiorna un asset della connessione di automazione di Azure|
|/automationAccounts/connectionTypes/delete|Elimina un asset del tipo di connessione di automazione di Azure|
|/automationAccounts/connectionTypes/read|Ottiene un asset del tipo di connessione di automazione di Azure|
|/automationAccounts/connectionTypes/write|Crea un asset del tipo di connessione di automazione di Azure|
|/automationAccounts/credentials/delete|Elimina un asset delle credenziali di automazione di Azure|
|/automationAccounts/credentials/read|Ottiene un asset delle credenziali di automazione di Azure|
|/automationAccounts/credentials/write|Crea o aggiorna un asset delle credenziali di automazione di Azure|
|/automationAccounts/delete|Elimina un account di automazione di Azure|
|/automationAccounts/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/automationAccounts/diagnosticSettings/write|Attiva l'impostazione di diagnostica per la risorsa|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Elimina le risorse del ruolo di lavoro ibrido per runbook|
|/automationAccounts/hybridRunbookWorkerGroups/read|Legge le risorse del ruolo di lavoro ibrido per runbook|
|/automationAccounts/jobs/output/action|Ottiene l'output di un processo|
|/automationAccounts/jobs/output/action|Ottiene l'output di un processo|
|/automationAccounts/jobs/read|Ottiene un processo di automazione di Azure|
|/automationAccounts/jobs/read|Ottiene un processo di automazione di Azure|
|/automationAccounts/jobs/resume/action|Riprende un processo di automazione di Azure|
|/automationAccounts/jobs/resume/action|Riprende un processo di automazione di Azure|
|/automationAccounts/jobs/runbookContent/action|Ottiene il contenuto del runbook di automazione di Azure durante l'esecuzione del processo|
|/automationAccounts/jobs/runbookContent/action|Ottiene il contenuto del runbook di automazione di Azure durante l'esecuzione del processo|
|/automationAccounts/jobs/stop/action|Arresta un processo di automazione di Azure|
|/automationAccounts/jobs/stop/action|Arresta un processo di automazione di Azure|
|/automationAccounts/jobs/streams/read|Ottiene un flusso del processo di automazione di Azure|
|/automationAccounts/jobs/streams/read|Ottiene un flusso del processo di automazione di Azure|
|/automationAccounts/jobs/suspend/action|Sospende un processo di automazione di Azure|
|/automationAccounts/jobs/suspend/action|Sospende un processo di automazione di Azure|
|/automationAccounts/jobs/write|Crea un processo di automazione di Azure|
|/automationAccounts/jobs/write|Crea un processo di automazione di Azure|
|/automationAccounts/jobSchedules/delete|Elimina una pianificazione del processo di automazione di Azure|
|/automationAccounts/jobSchedules/read|Ottiene una pianificazione del processo di automazione di Azure|
|/automationAccounts/jobSchedules/write|Crea una pianificazione del processo di automazione di Azure|
|/automationAccounts/linkedWorkspace/read|Ottiene l'area di lavoro collegata all'account di automazione|
|/automationAccounts/logDefinitions/read|Ottiene i log disponibili per l'account di automazione|
|/automationAccounts/modules/activities/read|Ottiene le attività di Automazione di Azure|
|/automationAccounts/modules/delete|Elimina un modulo di automazione di Azure|
|/automationAccounts/modules/read|Ottiene un modulo di automazione di Azure|
|/automationAccounts/modules/write|Crea o aggiorna un modulo di automazione di Azure|
|/automationAccounts/nodeConfigurations/delete|Elimina una configurazione nodo di Automation DSC di Azure|
|/automationAccounts/nodeConfigurations/read|Legge una configurazione nodo di Automation DSC di Azure|
|/automationAccounts/nodeConfigurations/readContent/action|Legge il contenuto di una configurazione nodo di Automation DSC di Azure|
|/automationAccounts/nodeConfigurations/write|Scrive una configurazione nodo di Automation DSC di Azure|
|/automationAccounts/nodes/delete|Elimina i nodi di Automation DSC di Azure|
|/automationAccounts/nodes/read|Legge i nodi di Automation DSC di Azure|
|/automationAccounts/nodes/reports/read|Legge i contenuti dei report di Automation DSC di Azure|
|/automationAccounts/nodes/reports/read|Legge i report di Automation DSC di Azure|
|/automationAccounts/objectDataTypes/fields/read|Ottiene i TypeField di Automazione di Azure|
|/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read|Ottiene le definizioni della metrica di automazione|
|/automationAccounts/read|Ottiene un account di automazione di Azure|
|/automationAccounts/runbooks/delete|Elimina un runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/publish/action|Pubblica una bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/read|Ottiene una bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/readContent/action|Ottiene il contenuto di una bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/testJob/read|Ottiene un processo di test per la bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/testJob/resume/action|Riprende un processo di test per la bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/testJob/stop/action|Interrompe un processo di test per la bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Sospende un processo di test per la bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/testJob/write|Crea un processo di test per la bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/undoEdit/action|Annulla le modifiche di una bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/draft/writeContent/action|Crea il contenuto di una bozza del runbook di automazione di Azure|
|/automationAccounts/runbooks/read|Ottiene un runbook di automazione di Azure|
|/automationAccounts/runbooks/readContent/action|Ottiene il contenuto di un runbook di automazione di Azure|
|/automationAccounts/runbooks/write|Crea o aggiorna un runbook di automazione di Azure|
|/automationAccounts/schedules/delete|Elimina un asset della pianificazione di automazione di Azure|
|/automationAccounts/schedules/read|Ottiene un asset della pianificazione di automazione di Azure|
|/automationAccounts/schedules/write|Crea o aggiorna un asset della pianificazione di automazione di Azure|
|/automationAccounts/statistics/read|Ottiene statistiche di Automazione di Azure|
|/automationAccounts/usages/read|Ottiene l'utilizzo di Automazione di Azure|
|/automationAccounts/variables/delete|Elimina un asset della variabile di automazione di Azure|
|/automationAccounts/variables/read|Esegue la lettura di un asset della variabile di automazione di Azure|
|/automationAccounts/variables/write|Crea o aggiorna un asset della variabile di automazione di Azure|
|/automationAccounts/watchers/streams/read|Ottiene il flusso del processo di un watcher di Automazione di Azure|
|/automationAccounts/webhooks/delete|Elimina un webhook di automazione di Azure |
|/automationAccounts/webhooks/generateUri/action|Genera un URI per un webhook di automazione di Azure|
|/automationAccounts/webhooks/read|Esegue la lettura un webhook di automazione di Azure|
|/automationAccounts/webhooks/write|Crea o aggiorna un webhook di automazione di Azure|
|/automationAccounts/write|Crea o aggiorna un account di automazione di Azure|
|/automationAccounts/write|Crea o aggiorna un account di automazione di Azure|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

| Operazione | DESCRIZIONE |
|---|---|
|/b2cDirectories/delete|Elimina la risorsa directory B2C|
|/b2cDirectories/read|Visualizza la risorsa directory B2C|
|/b2cDirectories/write|Crea o aggiorna la risorsa directory B2C|
|/operations/read|Legge tutte le operazioni API disponibili per il provider di risorse Microsoft.AzureActiveDirectory|
|/register/action|Registra la sottoscrizione per il provider di risorse Microsoft.AzureActiveDirectory|

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

| Operazione | DESCRIZIONE |
|---|---|
|/Operations/read|Ottiene le proprietà di un'operazione del provider di risorse|
|/register/action|Registra la sottoscrizione con il provider di risorse Microsoft.AzureStack|
|/registrations/customerSubscriptions/delete|Elimina una sottoscrizione cliente di Azure Stack|
|/registrations/customerSubscriptions/read|Ottiene le proprietà di una sottoscrizione cliente di Azure Stack|
|/registrations/customerSubscriptions/write|Crea o aggiorna una sottoscrizione cliente di Azure Stack|
|/registrations/delete|Elimina una registrazione di Azure Stack|
|/registrations/getActivationKey/action|Ottiene la chiave di attivazione di Azure Stack più recente|
|/registrations/products/listDetails/action|Recupera i dettagli completi per un prodotto del marketplace Azure Stack|
|/registrations/products/read|Ottiene le proprietà di un prodotto del marketplace Azure Stack|
|/registrations/read|Ottiene le proprietà di una registrazione di Azure Stack|
|/registrations/write|Crea o aggiorna una registrazione di Azure Stack|

## <a name="microsoftbatch"></a>Microsoft.Batch

| Operazione | DESCRIZIONE |
|---|---|
|/batchAccounts/applications/delete|Elimina un'applicazione|
|/batchAccounts/applications/read|Elenca le applicazioni o ottiene le proprietà di un'applicazione|
|/batchAccounts/applications/versions/activate/action|Attiva un pacchetto dell'applicazione|
|/batchAccounts/applications/versions/delete|Elimina un pacchetto dell'applicazione|
|/batchAccounts/applications/versions/read|Ottiene le proprietà di un pacchetto dell'applicazione|
|/batchAccounts/applications/versions/write|Crea un nuovo pacchetto dell'applicazione o ne aggiorna uno esistente|
|/batchAccounts/applications/write|Crea una nuova applicazione o ne aggiorna una esistente|
|/batchAccounts/certificateOperationResults/read|Ottiene i risultati di un'operazione di certificato a esecuzione prolungata in un account Batch|
|/batchAccounts/certificates/cancelDelete/action|Annulla l'eliminazione non riuscita di un certificato in un account Batch|
|/batchAccounts/certificates/delete|Elimina un certificato da un account Batch|
|/batchAccounts/certificates/read|Elenca i certificati di un account Batch o ottiene le proprietà di un certificato|
|/batchAccounts/certificates/write|Crea un nuovo certificato in un account Batch o aggiorna un certificato esistente|
|/batchAccounts/delete|Elimina un account Batch|
|/batchAccounts/listkeys/action|Elenca le chiavi di accesso per un account Batch|
|/batchAccounts/operationResults/read|Ottiene i risultati di un'operazione a esecuzione prolungata in un account Batch|
|/batchAccounts/poolOperationResults/read|Ottiene i risultati di un'operazione di pool a esecuzione prolungata in un account Batch|
|/batchAccounts/pools/delete|Elimina un pool da un account Batch|
|/batchAccounts/pools/disableAutoscale/action|Disabilita il ridimensionamento automatico per un pool di account Batch|
|/batchAccounts/pools/read|Elenca i pool in un account Batch o ottiene le proprietà di un pool|
|/batchAccounts/pools/stopResize/action|Arresta un'operazione di ridimensionamento in corso in un pool di account Batch|
|/batchAccounts/pools/upgradeOs/action|Aggiorna il sistema operativo del pool di account Batch|
|/batchAccounts/pools/write|Crea un nuovo pool in un account Batch o aggiorna un pool esistente|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/batchAccounts/providers/Microsoft.Insights/logDefinitions/read|Ottiene i log disponibili per il servizio Batch|
|/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per il servizio Batch|
|/batchAccounts/read|Elenca account Batch oppure ottiene le proprietà di un account Batch|
|/batchAccounts/regeneratekeys/action|Rigenera le chiavi di accesso per un account Batch|
|/batchAccounts/syncAutoStorageKeys/action|Consente di sincronizzare le chiavi di accesso per l'account di archiviazione automatica configurato per un account Batch|
|/batchAccounts/write|Crea un nuovo account Batch o aggiorna un account Batch esistente|
|/locations/checkNameAvailability/action|Controlla che il nome di account sia valido e non in uso.|
|/locations/quotas/read|Ottiene quote Batch della sottoscrizione specificata nell'area di Azure specificata|
|/register/action|Registra la sottoscrizione per il provider di risorse Batch e consente la creazione di account Batch|
|/unregister/action|Annulla la registrazione della sottoscrizione per il provider di risorse Batch e impedisce la creazione di account Batch|

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

| Operazione | DESCRIZIONE |
|---|---|
|/clusters/delete|Elimina un cluster Batch per intelligenza artificiale|
|/clusters/read|Elenca i cluster Batch per intelligenza artificiale o ottiene le proprietà di un cluster Batch per intelligenza artificiale|
|/clusters/remoteLoginInformation/action|Elenca le informazioni di accesso remoto per un cluster Batch per intelligenza artificiale|
|/clusters/write|Crea un nuovo cluster Batch per intelligenza artificiale o aggiorna un cluster Batch per intelligenza artificiale esistente|
|/fileservers/delete|Elimina un file server Batch per intelligenza artificiale|
|/fileservers/read|Elenca i file server Batch per intelligenza artificiale o ottiene le proprietà di un file server Batch per intelligenza artificiale|
|/fileservers/resume/action|Riprende l'esecuzione di un file server Batch per intelligenza artificiale|
|/fileservers/suspend/action|Sospende un file server Batch per intelligenza artificiale|
|/fileservers/write|Crea un nuovo file server Batch per intelligenza artificiale o aggiorna un file server Batch per intelligenza artificiale esistente|
|/jobs/delete|Elimina un processo Batch per intelligenza artificiale|
|/jobs/read|Elenca i processi Batch per intelligenza artificiale o ottiene le proprietà di un progetto Batch per intelligenza artificiale|
|/jobs/remoteLoginInformation/action|Elenca le informazioni di accesso remoto per un processo Batch per intelligenza artificiale|
|/jobs/terminate/action|Termina un processo Batch per intelligenza artificiale|
|/jobs/write|Crea un nuovo processo Batch per intelligenza artificiale o aggiorna un processo Batch per intelligenza artificiale esistente|
|/register/action|Registra la sottoscrizione per il provider di risorse Batch per intelligenza artificiale e consente la creazione di risorse Batch per intelligenza artificiale|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Operazione | DESCRIZIONE |
|---|---|
|/billingPeriods/read|Elenca i periodi di fatturazione disponibili|
|/invoices/read|Elenca le fatture disponibili|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Operazione | DESCRIZIONE |
|---|---|
|/mapApis/Delete|Operazione di eliminazione|
|/mapApis/listSecrets/action|Elenca i segreti|
|/mapApis/listSingleSignOnToken/action|Esegue la lettura del token di autorizzazione Single Sign-On per la risorsa|
|/mapApis/Read|Operazione di lettura|
|/mapApis/regenerateKey/action|Rigenera la chiave|
|/mapApis/Write|Operazione di scrittura|
|/Operations/read|Descrizione dell'operazione.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Operazione | DESCRIZIONE |
|---|---|
|/checknameavailability/action|Verifica se un nome è disponibile per essere assegnato a nuova cache Redis|
|/operations/read|Elenca le operazioni supportate dal provider "Microsoft.Cache".|
|/redis/delete|Elimina l'intera Cache Redis|
|/redis/export/action|Esporta i dati Redis nei BLOB di archiviazione predefiniti nel formato specificato|
|/redis/firewallRules/delete|Elimina le regole del firewall per gli indirizzi IP di una cache Redis|
|/redis/firewallRules/read|Ottiene le regole del firewall per gli indirizzi IP di una cache Redis|
|/redis/firewallRules/write|Modifica le regole del firewall per gli indirizzi IP di una cache Redis|
|/redis/forceReboot/action|Forza il riavvio di un'istanza della cache, con possibile perdita di dati.|
|/redis/import/action|Importa i dati di un formato specificato da più BLOB in Redis|
|/redis/linkedservers/delete|Elimina un server collegato da una cache Redis|
|/redis/linkedservers/read|Ottiene i server collegati associati a una cache Redis.|
|/redis/linkedservers/write|Aggiunge un server collegato a una cache Redis|
|/redis/listKeys/action|Visualizza il valore delle chiavi di accesso della Cache Redis nel portale di gestione|
|/redis/listUpgradeNotifications/read|Elenca le notifiche di aggiornamento più recenti per il tenant della cache.|
|/redis/locations/operationresults/read|Ottiene il risultato di un'operazione a esecuzione prolungata per la quale l'intestazione "Location" è stata restituita precedentemente al client|
|/redis/metricDefinitions/read|Ottiene la metrica disponibile per una cache Redis|
|/redis/patchSchedules/delete|Elimina la pianificazione dell'applicazione di patch di una cache Redis|
|/redis/patchSchedules/read|Ottiene la pianificazione dell'applicazione di patch di una cache Redis|
|/redis/patchSchedules/write|Modifica la pianificazione dell'applicazione di patch di una cache Redis|
|/redis/read|Visualizza la configurazione e le impostazioni della Cache Redis nel portale di gestione|
|/redis/regenerateKey/action|Modifica il valore delle chiavi di accesso della Cache Redis nel portale di gestione|
|/redis/start/action|Avvia un'istanza della cache.|
|/redis/stop/action|Arresta un'istanza della cache.|
|/redis/write|Modifica la configurazione e le impostazioni della Cache Redis nel portale di gestione|
|/register/action|Registra il provider di risorse 'Microsoft.Cache' con una sottoscrizione|
|/unregister/action|Annulla la registrazione del provider di risorse 'Microsoft.Cache' con una sottoscrizione|

## <a name="microsoftcapacity"></a>Microsoft.Capacity

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Registra il provider di risorse Capacity e consente la creazione di risorse Capacity.|
|/reservationorders/action|Aggiorna tutte le prenotazioni|
|/reservationorders/delete|Elimina tutte le prenotazioni|
|/reservationorders/read|Legge tutte le prenotazioni|
|/reservationorders/reservations/action|Aggiorna tutte le prenotazioni|
|/reservationorders/reservations/delete|Elimina tutte le prenotazioni|
|/reservationorders/reservations/read|Legge tutte le prenotazioni|
|/reservationorders/reservations/revisions/read|Legge tutte le prenotazioni|
|/reservationorders/reservations/write|Crea tutte le prenotazioni|
|/reservationorders/write|Crea tutte le prenotazioni|

## <a name="microsoftcdn"></a>Microsoft.Cdn

| Operazione | DESCRIZIONE |
|---|---|
|/CheckNameAvailability/action||
|/CheckResourceUsage/action||
|/edgenodes/delete||
|/edgenodes/read||
|/edgenodes/write||
|/operationresults/delete||
|/operationresults/profileresults/CheckResourceUsage/action||
|/operationresults/profileresults/delete||
|/operationresults/profileresults/endpointresults/CheckResourceUsage/action||
|/operationresults/profileresults/endpointresults/customdomainresults/delete||
|/operationresults/profileresults/endpointresults/customdomainresults/ DisableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/ EnableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/read||
|/operationresults/profileresults/endpointresults/customdomainresults/write||
|/operationresults/profileresults/endpointresults/delete||
|/operationresults/profileresults/endpointresults/Load/action||
|/operationresults/profileresults/endpointresults/originresults/delete||
|/operationresults/profileresults/endpointresults/originresults/read||
|/operationresults/profileresults/endpointresults/originresults/write||
|/operationresults/profileresults/endpointresults/Purge/action||
|/operationresults/profileresults/endpointresults/read||
|/operationresults/profileresults/endpointresults/Start/action||
|/operationresults/profileresults/endpointresults/Stop/action||
|/operationresults/profileresults/endpointresults/ValidateCustomDomain/action||
|/operationresults/profileresults/endpointresults/write||
|/operationresults/profileresults/GenerateSsoUri/action||
|/operationresults/profileresults/GetSupportedOptimizationTypes/action||
|/operationresults/profileresults/read||
|/operationresults/profileresults/write||
|/operationresults/read||
|/operationresults/write||
|/operations/read||
|/profiles/CheckResourceUsage/action||
|/profiles/delete||
|/profiles/endpoints/CheckResourceUsage/action||
|/profiles/endpoints/customdomains/delete||
|/profiles/endpoints/customdomains/DisableCustomHttps/action||
|/profiles/endpoints/customdomains/EnableCustomHttps/action||
|/profiles/endpoints/customdomains/read||
|/profiles/endpoints/customdomains/write||
|/profiles/endpoints/delete||
|/profiles/endpoints/Load/action||
|/profiles/endpoints/origins/delete||
|/profiles/endpoints/origins/read||
|/profiles/endpoints/origins/write||
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene le impostazioni di diagnostica per la risorsa|
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna le impostazioni di diagnostica per la risorsa|
|/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read|Ottiene i log disponibili per Microsoft.Cdn|
|/profiles/endpoints/Purge/action||
|/profiles/endpoints/read||
|/profiles/endpoints/Start/action||
|/profiles/endpoints/Stop/action||
|/profiles/endpoints/ValidateCustomDomain/action||
|/profiles/endpoints/write||
|/profiles/GenerateSsoUri/action||
|/profiles/GetSupportedOptimizationTypes/action||
|/profiles/read||
|/profiles/write||
|/register/action|Registra la sottoscrizione per il provider di risorse della rete CDN e consente la creazione di profili CDN.|
|/ValidateProbe/action||

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Operazione | DESCRIZIONE |
|---|---|
|/certificateOrders/certificates/Delete|Elimina un certificato esistente|
|/certificateOrders/certificates/Read|Ottiene l'elenco dei certificati|
|/certificateOrders/certificates/Write|Aggiunge un nuovo certificato o ne aggiorna uno esistente|
|/certificateOrders/Delete|Elimina un certificato del servizio app esistente|
|/certificateOrders/operations/Read|Elenca tutte le operazioni dalla registrazione del certificato di servizio dell'app|
|/certificateOrders/Read|Ottiene l'elenco degli ordini di certificato|
|/certificateOrders/reissue/Action|Riemette un ordine di certificato esistente|
|/certificateOrders/renew/Action|Rinnova un ordine di certificato esistente|
|/certificateOrders/resendEmail/Action|Invia di nuovo la posta elettronica di certificato|
|/certificateOrders/resendRequestEmails/Action|Invia di nuovo messaggi di richiesta a un altro indirizzo di posta elettronica|
|/certificateOrders/resendRequestEmails/Action|Recupera il sealing di sito per un certificato del servizio app emesso|
|/certificateOrders/retrieveCertificateActions/Action|Recupera l'elenco di azioni di certificato|
|/certificateOrders/retrieveEmailHistory/Action|Recupera la cronologia della posta elettronica di certificato|
|/certificateOrders/verifyDomainOwnership/Action|Verificare la proprietà del dominio|
|/certificateOrders/Write|Aggiunge un nuovo ordine di certificato o ne aggiorna uno esistente|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Esegue il provisioning dell'entità servizio per l'entità app del servizio|
|/register/action|Registra il provider di risorse certificati Microsoft per la sottoscrizione|
|/validateCertificateRegistrationInformation/Action|Convalida l'oggetto acquisto di certificato senza inviarlo|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Operazione | DESCRIZIONE |
|---|---|
|/capabilities/read|Visualizza le funzionalità|
|/checkDomainNameAvailability/action|Controlla la disponibilità di un nome di dominio specifico.|
|/domainNames/active/write|Imposta il nome del dominio attivo.|
|/domainNames/availabilitySets/read|Visualizza il set di disponibilità per la risorsa.|
|/domainNames/capabilities/read|Mostra le funzionalità del nome di dominio|
|/domainNames/delete|Rimuove i nomi di dominio per le risorse.|
|/domainNames/extensions/delete|Rimuove le estensioni del nome di dominio.|
|/domainNames/extensions/operationStatuses/read|Esegue la lettura dello stato dell'operazione per le estensioni dei nomi di dominio.|
|/domainNames/extensions/read|Restituisce le estensioni del nome di dominio.|
|/domainNames/extensions/write|Aggiunge le estensioni del nome di dominio.|
|/domainNames/internalLoadBalancers/delete|Rimuove un nuovo bilanciamento del carico interno.|
|/domainNames/internalLoadBalancers/operationStatuses/read|Legge lo stato dell'operazione per i servizi di bilanciamento del carico dei nomi di dominio.|
|/domainNames/internalLoadBalancers/read|Ottiene i servizi di bilanciamento del carico interno.|
|/domainNames/internalLoadBalancers/write|Crea un nuovo bilanciamento del carico interno.|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|Legge lo stato dell'operazione per gli endpoint con bilanciamento del carico per i nomi di dominio.|
|/domainNames/loadBalancedEndpointSets/read|Visualizza gli endpoint con bilanciamento del carico|
|/domainNames/read|Restituisce i nomi di dominio per le risorse.|
|/domainNames/serviceCertificates/delete|Elimina i certificati di servizio usati.|
|/domainNames/serviceCertificates/operationStatuses/read|Esegue la lettura dello stato dell'operazione per i certificati di servizio dei nomi di dominio.|
|/domainNames/serviceCertificates/read|Restituisce i certificati di servizio usati.|
|/domainNames/serviceCertificates/write|Aggiunge o modifica i certificati di servizio usati.|
|/domainNames/slots/delete|Elimina uno slot di distribuzione specifico.|
|/domainNames/slots/operationStatuses/read|Esegue la lettura dello stato dell'operazione per gli slot dei nomi di dominio.|
|/domainNames/slots/read|Mostra gli slot di distribuzione.|
|/domainNames/slots/roles/extensionReferences/delete|Rimuove il riferimento all'estensione del ruolo dello slot di distribuzione.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|Legge lo stato operazione dei riferimenti all'estensione per i ruoli slot dei nomi di dominio.|
|/domainNames/slots/roles/extensionReferences/read|Restituisce il riferimento all'estensione del ruolo dello slot di distribuzione.|
|/domainNames/slots/roles/extensionReferences/write|Aggiunge o modifica il riferimento all'estensione del ruolo dello slot di distribuzione.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene le impostazioni di diagnostica.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write|Consente di aggiungere o modificare le impostazioni di diagnostica.|
|/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read|Ottiene le definizioni di metrica.|
|/domainNames/slots/roles/read|Ottiene il ruolo per lo slot di distribuzione.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|Legge lo stato dell'operazione per le istanze ruolo dei ruoli slot dei nomi di dominio.|
|/domainNames/slots/roles/roleInstances/read|Ottiene l'istanza del ruolo.|
|/domainNames/slots/roles/roleInstances/rebuild/action|Ricostruisce l'istanza del ruolo.|
|/domainNames/slots/roles/roleInstances/reimage/action|Ricrea l'immagine dell'istanza del ruolo.|
|/domainNames/slots/roles/roleInstances/restart/action|Riavvia le istanze del ruolo.|
|/domainNames/slots/start/action|Avvia uno slot di distribuzione.|
|/domainNames/slots/state/start/write|Imposta lo stato dello slot di distribuzione su arrestato.|
|/domainNames/slots/state/stop/write|Imposta lo stato dello slot di distribuzione su avviato.|
|/domainNames/slots/stop/action|Sospende lo slot di distribuzione.|
|/domainNames/slots/upgradeDomain/write|Esegue l'aggiornamento del dominio.|
|/domainNames/slots/write|Crea o aggiorna la distribuzione.|
|/domainNames/swap/action|Passa dallo slot di staging allo slot di produzione.|
|/domainNames/write|Aggiunge o modifica i nomi di dominio per le risorse.|
|/moveSubscriptionResources/action|Sposta tutte le risorse classiche in un'altra sottoscrizione.|
|/operatingSystemFamilies/read|Elenca le famiglie di sistemi operativi guest disponibili in Microsoft Azure ed elenca anche le versioni dei sistemi operativi disponibili per ogni famiglia.|
|/operatingSystems/read|Elenca le versioni dei sistemi operativi guest attualmente disponibili in Microsoft Azure.|
|/quotas/read|Ottiene la quota per la sottoscrizione.|
|/register/action|Consente di eseguire la registrazione per Calcolo classico|
|/resourceTypes/skus/read|Ottiene l'elenco degli SKU per i tipi di risorse supportati.|
|/validateSubscriptionMoveAvailability/action|Convalida la disponibilità della sottoscrizione per l'operazione di spostamento classica.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|Elimina il gruppo di sicurezza di rete associato alla macchina virtuale.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|Legge lo stato operazione dei gruppi di sicurezza di rete associati alle macchine virtuali.|
|/virtualMachines/associatedNetworkSecurityGroups/read|Ottiene il gruppo di sicurezza di rete associato alla macchina virtuale.|
|/virtualMachines/associatedNetworkSecurityGroups/write|Aggiunge un gruppo di sicurezza di rete associato alla macchina virtuale.|
|/virtualMachines/asyncOperations/read|Ottiene le operazioni asincrone possibili|
|/virtualMachines/attachDisk/action|Collega un disco dati a una macchina virtuale.|
|/virtualMachines/delete|Rimuove le macchine virtuali.|
|/virtualMachines/detachDisk/action|Scollega un disco dati da una macchina virtuale.|
|/virtualMachines/disks/read|Recupera l'elenco dei dischi dati|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|Scarica il file RDP per la macchina virtuale.|
|/virtualMachines/extensions/operationStatuses/read|Legge lo stato operazione delle estensioni macchina virtuale.|
|/virtualMachines/extensions/read|Ottiene l'estensione macchina virtuale.|
|/virtualMachines/extensions/write|Visualizza l'estensione macchina virtuale.|
|/virtualMachines/metrics/read|Ottiene la metrica.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete|Elimina il gruppo di sicurezza di rete associato all'interfaccia di rete.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/ operationStatuses/read|Legge lo stato operazione dei gruppi di sicurezza di rete associati alle macchine virtuali.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read|Ottiene il gruppo di sicurezza di rete associato all'interfaccia di rete.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write|Aggiunge un gruppo di sicurezza di rete associato all'interfaccia di rete.|
|/virtualMachines/operationStatuses/read|Legge lo stato operazione delle macchine virtuali.|
|/virtualMachines/performMaintenance/action|Esegue operazioni di manutenzione sulla macchina virtuale.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene le impostazioni di diagnostica.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Consente di aggiungere o modificare le impostazioni di diagnostica.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Ottiene le definizioni di metrica.|
|/virtualMachines/read|Recupera l'elenco delle macchine virtuali.|
|/virtualMachines/redeploy/action|Ridistribuisce la macchina virtuale.|
|/virtualMachines/restart/action|Riavvia le macchine virtuali.|
|/virtualMachines/shutdown/action|Chiude la macchina virtuale.|
|/virtualMachines/start/action|Avviare la macchina virtuale.|
|/virtualMachines/stop/action|Arresta la macchina virtuale.|
|/virtualMachines/write|Aggiunge o modifica macchine virtuali.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Operazione | DESCRIZIONE |
|---|---|
|/gatewaySupportedDevices/read|Recupera l'elenco dei dispositivi supportati.|
|/networkSecurityGroups/delete|Elimina il gruppo di sicurezza di rete.|
|/networkSecurityGroups/operationStatuses/read|Legge lo stato operazione del gruppo di sicurezza di rete.|
|/networkSecurityGroups/read|Ottiene il gruppo di sicurezza di rete.|
|/networkSecurityGroups/securityRules/delete|Elimina la regola di sicurezza.|
|/networkSecurityGroups/securityRules/operationStatuses/read|Legge lo stato dell'operazione per le regole di sicurezza del gruppo di sicurezza di rete.|
|/networkSecurityGroups/securityRules/read|Ottiene la regola di sicurezza.|
|/networkSecurityGroups/securityRules/write|Aggiunge o aggiorna una regola di sicurezza.|
|/networkSecurityGroups/write|Aggiunge un nuovo gruppo di sicurezza di rete.|
|/quotas/read|Ottiene la quota per la sottoscrizione.|
|/register/action|Esegue la registrazione per Rete classica|
|/reservedIps/delete|Elimina un IP riservato.|
|/reservedIps/join/action|Unisce un IP riservato|
|/reservedIps/link/action|Collega un IP riservato|
|/reservedIps/operationStatuses/read|Legge lo stato dell'operazione per gli IP riservati.|
|/reservedIps/read|Ottiene gli IP riservati|
|/reservedIps/write|Aggiunge un nuovo IP riservato|
|/virtualNetworks/capabilities/read|Visualizza le funzionalità|
|/virtualNetworks/checkIPAddressAvailability/action|Verifica la disponibilità di un determinato indirizzo IP in una rete virtuale.|
|/virtualNetworks/delete|Elimina la rete virtuale.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|Annulla la revoca di un certificato client.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|Esegue la lettura dei certificati client revocati.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|Revoca un certificato client.|
|/virtualNetworks/gateways/clientRootCertificates/delete|Elimina il certificato client del gateway di rete virtuale.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|Scarica il certificato in base all'identificazione personale.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|Elenca il pacchetto del certificato del gateway di rete virtuale.|
|/virtualNetworks/gateways/clientRootCertificates/read|Trova i certificati radice client.|
|/virtualNetworks/gateways/clientRootCertificates/write|Carica un nuovo certificato radice client.|
|/virtualNetworks/gateways/connections/connect/action|Connette una connessione gateway da sito a sito.|
|/virtualNetworks/gateways/connections/disconnect/action|Disconnette una connessione gateway da sito a sito.|
|/virtualNetworks/gateways/connections/read|Recupera l'elenco di connessioni.|
|/virtualNetworks/gateways/connections/test/action|Esegue il test di una connessione gateway da sito a sito.|
|/virtualNetworks/gateways/delete|Elimina il gateway di rete virtuale.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|Scarica lo script di configurazione del dispositivo.|
|/virtualNetworks/gateways/downloadDiagnostics/action|Scarica la diagnostica del gateway.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Recupera la chiave del servizio del circuito.|
|/virtualNetworks/gateways/listPackage/action|Elenca i pacchetti del gateway di rete virtuale.|
|/virtualNetworks/gateways/operationStatuses/read|Legge lo stato operazione dei gateway di rete virtuale.|
|/virtualNetworks/gateways/packages/read|Ottiene il pacchetto del gateway di rete virtuale.|
|/virtualNetworks/gateways/read|Ottiene i gateway di rete virtuale.|
|/virtualNetworks/gateways/startDiagnostics/action|Avvia la diagnostica per il gateway di rete virtuale.|
|/virtualNetworks/gateways/stopDiagnostics/action|Arresta la diagnostica del gateway di rete virtuale.|
|/virtualNetworks/gateways/write|Aggiunge un gateway di rete virtuale.|
|/virtualNetworks/join/action|Unisce la rete virtuale.|
|/virtualNetworks/operationStatuses/read|Legge lo stato operazione delle reti virtuali.|
|/virtualNetworks/peer/action|Associa una rete virtuale a un'altra rete virtuale.|
|/virtualNetworks/read|Ottiene la rete virtuale.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete|Elimina il gruppo di sicurezza di rete associato alla subnet.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read|Legge lo stato operazione del gruppo di sicurezza di rete associato alla subnet della rete virtuale.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/read|Ottiene il gruppo di sicurezza di rete associato alla subnet.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/write|Aggiunge un gruppo di sicurezza di rete associato alla subnet.|
|/virtualNetworks/write|Aggiunge una nuova rete virtuale.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Operazione | DESCRIZIONE |
|---|---|
|/capabilities/read|Visualizza le funzionalità|
|/checkStorageAccountAvailability/action|Verifica la disponibilità di un account di archiviazione.|
|/disks/read|Restituisce il disco dell'account di archiviazione.|
|/images/read|Restituisce l'immagine.|
|/osImages/read|Restituisce l'immagine del sistema operativo.|
|/publicImages/read|Ottiene l'immagine di macchina virtuale pubblica.|
|/quotas/read|Ottiene la quota per la sottoscrizione.|
|/register/action|Consente di eseguire la registrazione per Archiviazione (versione classica)|
|/storageAccounts/delete|Elimina l'account di archiviazione.|
|/storageAccounts/disks/delete|Elimina un disco specifico dell'account di archiviazione.|
|/storageAccounts/disks/operationStatuses/read|Legge lo stato dell'operazione per la risorsa.|
|/storageAccounts/disks/read|Restituisce il disco dell'account di archiviazione.|
|/storageAccounts/disks/write|Aggiunge un disco dell'account di archiviazione.|
|/storageAccounts/images/delete|Elimina una data immagine dell'account di archiviazione.|
|/storageAccounts/images/read|Restituisce l'immagine dell'account di archiviazione.|
|/storageAccounts/listKeys/action|Elenca le chiavi di accesso per gli account di archiviazione.|
|/storageAccounts/operationStatuses/read|Legge lo stato dell'operazione per la risorsa.|
|/storageAccounts/osImages/delete|Elimina un'immagine specifica del sistema operativo dell'account di archiviazione.|
|/storageAccounts/osImages/read|Restituisce l'immagine del sistema operativo dell'account di archiviazione.|
|/storageAccounts/read|Restituisce l'account di archiviazione con l'account specificato.|
|/storageAccounts/regenerateKey/action|Rigenera le chiavi di accesso esistenti per l'account di archiviazione.|
|/storageAccounts/services/diagnosticSettings/read|Ottiene le impostazioni di diagnostica.|
|/storageAccounts/services/diagnosticSettings/write|Consente di aggiungere o modificare le impostazioni di diagnostica.|
|/storageAccounts/services/metricDefinitions/read|Ottiene le definizioni di metrica.|
|/storageAccounts/services/metrics/read|Ottiene la metrica.|
|/storageAccounts/services/read|Ottiene i servizi disponibili.|
|/storageAccounts/write|Aggiunge un nuovo account di archiviazione.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Operazione | DESCRIZIONE |
|---|---|
|/accounts/delete|Elimina gli account delle API|
|/accounts/listKeys/action|Elenco delle chiavi|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per Servizi cognitivi.|
|/accounts/read|Legge gli account delle API.|
|/accounts/regenerateKey/action|Rigenerazione della chiave|
|/accounts/skus/read|Legge gli SKU disponibili per una risorsa esistente.|
|/accounts/usages/read|Ottiene l'uso della quota per una risorsa esistente.|
|/accounts/write|Scrive gli account delle API.|
|/locations/checkSkuAvailability/action|Legge gli SKU disponibili per una sottoscrizione.|
|/Operations/read|Elenca tutte le operazioni disponibili|
|/register/action|Registra la sottoscrizione per Servizi cognitivi|
|/skus/read|Legge gli SKU disponibili per Servizi cognitivi.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Operazione | DESCRIZIONE |
|---|---|
|/RateCard/read|Restituisce dati dell'offerta, metadati delle risorse o del contatore e tariffe per una determinata sottoscrizione.|
|/UsageAggregates/read|Recupera il consumo di Microsoft Azure da una sottoscrizione. Il risultato contiene dati di utilizzo delle aggregazioni, informazioni correlate a sottoscrizioni e risorse in un intervallo di tempo specifico.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Operazione | DESCRIZIONE |
|---|---|
|/availabilitySets/delete|Elimina il set di disponibilità|
|/availabilitySets/read|Ottiene le proprietà di un set di disponibilità|
|/availabilitySets/vmSizes/read|Elenca le dimensioni disponibili per creare o aggiornare una macchina virtuale nel set di disponibilità|
|/availabilitySets/write|Crea un nuovo set di disponibilità o ne aggiorna uno esistente|
|/disks/beginGetAccess/action|Ottiene gli URI SAS del disco per l’accesso al BLOB|
|/disks/delete|Elimina il disco|
|/disks/endGetAccess/action|Revoca l’URI SAS del disco|
|/disks/read|Ottiene le proprietà di un disco|
|/disks/write|Crea un nuovo disco o ne aggiorna uno esistente|
|/images/delete|Elimina l'immagine|
|/images/read|Ottiene le proprietà dell’immagine|
|/images/write|Crea una nuova immagine o ne aggiorna una esistente|
|/locations/capsOperations/read|Ottiene lo stato di un'operazione Caps asincrona|
|/locations/diskOperations/read|Ottiene lo stato di un'operazione Disk asincrona|
|/locations/operations/read|Ottiene lo stato di un'operazione asincrona|
|/locations/publishers/artifacttypes/offers/read|Ottiene le proprietà di un'offerta dell'immagine della piattaforma|
|/locations/publishers/artifacttypes/offers/skus/read|Ottiene le proprietà di uno SKU dell'immagine della piattaforma|
|/locations/publishers/artifacttypes/offers/skus/versions/read|Ottiene le proprietà di una versione dell'immagine della piattaforma|
|/locations/publishers/artifacttypes/types/read|Ottiene le proprietà di un tipo VMExtension|
|/locations/publishers/artifacttypes/types/versions/read|Ottiene le proprietà di una versione VMExtension|
|/locations/publishers/read|Ottiene le proprietà di un server di pubblicazione|
|/locations/runCommands/read|Elenca i comandi di esecuzione disponibili nella posizione|
|/locations/usages/read|Ottiene i limiti del servizio e le quantità di utilizzo corrente delle risorse di calcolo della sottoscrizione in una posizione|
|/locations/vmSizes/read|Elenca le dimensioni delle macchine virtuali disponibili in una posizione|
|/operations/read|Elenca le operazioni disponibili sul provider di risorse Microsoft.Compute|
|/register/action|Registra la sottoscrizione con il provider di risorse Microsoft.Compute|
|/restorePointCollections/delete|Elimina la raccolta di punti di ripristino e i punti di ripristino contenuti|
|/restorePointCollections/read|Ottiene le proprietà di un insieme di punti di ripristino|
|/restorePointCollections/restorePoints/delete|Elimina il punto di ripristino|
|/restorePointCollections/restorePoints/read|Ottiene le proprietà di un punto di ripristino|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Ottiene le proprietà di un punto di ripristino insieme agli URI SAS del BLOB|
|/restorePointCollections/restorePoints/write|Crea un nuovo punto di ripristino|
|/restorePointCollections/write|Crea un nuovo insieme di punti di ripristino o aggiorna un insieme esistente|
|/sharedVMImages/delete|Elimina la SharedVMImage|
|/sharedVMImages/read|Ottiene le proprietà di una SharedVMImage|
|/sharedVMImages/versions/delete|Elimina una versione di immagine della macchina virtuale condivisa|
|/sharedVMImages/versions/read|Ottiene le proprietà di una versione di immagine della macchina virtuale condivisa|
|/sharedVMImages/versions/replicate/action|Replica una versione di immagine della macchina virtuale condivisa per le aree di destinazione|
|/sharedVMImages/versions/write|Crea una nuova versione di immagine della macchina virtuale condivisa o ne aggiorna una esistente|
|/sharedVMImages/write|Crea una nuova SharedVMImage o aggiornarne una esistente|
|/skus/read|Ottiene l'elenco degli SKU Microsoft.Compute disponibili per la sottoscrizione|
|/snapshots/beginGetAccess/action|Ottiene l'URI SAS dello snapshot per l'accesso al BLOB|
|/snapshots/delete|Elimina una snapshot|
|/snapshots/endGetAccess/action|Revoca l'URI SAS dello snapshot|
|/snapshots/read|Ottiene le proprietà di una snapshot|
|/snapshots/write|Crea una nuova snapshot o ne aggiorna una esistente|
|/virtualMachines/capture/action|Acquisisce la macchina virtuale copiando i dischi rigidi virtuali e genera un modello utilizzabile per creare macchine virtuali simili|
|/virtualMachines/convertToManagedDisks/action|Converte i dischi basati su BLOB della macchina virtuale in dischi gestiti|
|/virtualMachines/deallocate/action|Disabilita la macchina virtuale e rilascia le risorse di calcolo|
|/virtualMachines/delete|Elimina la macchina virtuale|
|/virtualMachines/extensions/delete|Elimina l'estensione macchina virtuale|
|/virtualMachines/extensions/read|Ottiene le proprietà di un'estensione macchina virtuale|
|/virtualMachines/extensions/write|Crea una nuova estensione macchina virtuale o ne aggiorna una esistente|
|/virtualMachines/generalize/action|Imposta lo stato della macchina virtuale a Generalizzato e prepara la macchina virtuale per l'acquisizione|
|/virtualMachines/instanceView/read|Ottiene lo stato di runtime dettagliato della macchina virtuale e delle relative risorse|
|/virtualMachines/performMaintenance/action|Esegue operazioni di manutenzione sulla VM.|
|/virtualMachines/powerOff/action|Disabilita la macchina virtuale. Notare che la macchina virtuale continuerà a essere fatturata.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Legge le definizioni della metrica della macchina virtuale|
|/virtualMachines/read|Ottiene le proprietà di una macchina virtuale|
|/virtualMachines/redeploy/action|Ridistribuisce la macchina virtuale|
|/virtualMachines/reimage/action|Ricrea l'immagine della macchina virtuale che usa un disco differenze.|
|/virtualMachines/restart/action|Riavvia la macchina virtuale|
|/virtualMachines/runCommand/action|Esegue uno script predefinito nella macchina virtuale|
|/virtualMachines/start/action|Avvia la macchina virtuale|
|/virtualMachines/vmSizes/read|Elenca le dimensioni disponibili a cui è possibile aggiornare la macchina virtuale|
|/virtualMachines/write|Crea una nuova macchina virtuale o ne aggiorna una esistente|
|/virtualMachineScaleSets/deallocate/action|Disabilita e rilascia le risorse di calcolo per le istanze del set di scalabilità di macchine virtuali |
|/virtualMachineScaleSets/delete|Elimina il set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/delete/action|Elimina le istanze del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/extensions/delete|Elimina l'estensione set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/extensions/read|Ottiene le proprietà di un'estensione set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/extensions/write|Crea una nuova estensione set di scalabilità di macchine virtuali o ne aggiorna una esistente|
|/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action|Esamina manualmente i domini di aggiornamento della piattaforma di un set di scalabilità di macchine virtuali dell'infrastruttura di servizi|
|/virtualMachineScaleSets/instanceView/read|Ottiene la vista delle istanze del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/manualUpgrade/action|Aggiorna manualmente le istanze all'ultimo modello del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/networkInterfaces/read|Ottiene le proprietà di tutte le interfacce di rete di un set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/osUpgradeHistory/read|Recupera la cronologia degli aggiornamenti del sistema operativo per un set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/performMaintenance/action|Esegue la manutenzione pianificata sulle istanze del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/powerOff/action|Disabilita le istanze del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read|Legge le definizioni della metrica del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/publicIPAddresses/read|Ottiene le proprietà di tutti gli indirizzi IP pubblici di un set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/read|Ottiene le proprietà di un set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/redeploy/action|Ridistribuisce le istanze del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/reimage/action|Ricrea l'immagine delle istanze del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/restart/action|Riavvia le istanze del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/rollingUpgrades/cancel/action|Annulla l'aggiornamento in sequenza di un set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/rollingUpgrades/read|Recupera lo stato più recente dell'aggiornamento in sequenza per un set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/scale/action|Verifica se un set di scalabilità di macchine virtuali esistente può ridurre/aumentare il numero di istanze fino al numero di istanze specificato|
|/virtualMachineScaleSets/skus/read|Elenca gli SKU validi per un set di scalabilità di macchine virtuali esistente|
|/virtualMachineScaleSets/start/action|Avvia le istanze del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|Disabilita e rilascia le risorse di calcolo per una macchina virtuale in un set di scalabilità VM.|
|/virtualMachineScaleSets/virtualMachines/delete|Elimina una specifica macchina virtuale in un set di scalabilità VM.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|Recupera la vista istanza di una macchina virtuale in un set di scalabilità di una VM.|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ ipConfigurations/publicIPAddresses/read|Ottiene le proprietà degli indirizzi IP pubblici creati usando un set di scalabilità di macchine virtuali. Il set di scalabilità di macchine virtuali può creare al massimo un indirizzo IP pubblico per ogni configurazione IP, ovvero IP privati|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read|Ottiene le proprietà di una o tutte le configurazioni IP di un'interfaccia di rete creata usando un ser di scalabilità di macchine virtuali. Le configurazioni IP rappresentano indirizzi IP privati|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/read|Ottiene le proprietà di una o tutte le interfacce di rete di una macchina virtuale creata usando un ser di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/virtualMachines/performMaintenance/action|Esegue la manutenzione pianificata su un'istanza di macchina virtuale in un set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|Disabilita un'istanza di macchina virtuale in un set di scalabilità VM.|
|/virtualMachineScaleSets/virtualMachines/providers/ Microsoft.Insights/metricDefinitions/read|Legge le definizioni della metrica del set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/virtualMachines/read|Recupera le proprietà di una macchina virtuale in un set di scalabilità VM|
|/virtualMachineScaleSets/virtualMachines/redeploy/action|Ridistribuisce l'immagine dell'istanza di una macchina virtuale in un set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/virtualMachines/reimage/action|Ricrea l'immagine dell'istanza di una macchina virtuale in un set di scalabilità di macchine virtuali.|
|/virtualMachineScaleSets/virtualMachines/restart/action|Riavvia un'istanza di macchina virtuale in un set di scalabilità VM.|
|/virtualMachineScaleSets/virtualMachines/start/action|Avvia un'istanza di macchina virtuale in un set di scalabilità VM.|
|/virtualMachineScaleSets/virtualMachines/write|Aggiorna le proprietà di una macchina virtuale in un set di scalabilità di macchine virtuali|
|/virtualMachineScaleSets/write|Crea un nuovo set di scalabilità di macchine virtuali o ne aggiorna uno esistente|

## <a name="microsoftconsumption"></a>Microsoft.Consumption

| Operazione | DESCRIZIONE |
|---|---|
|/balances/read|Elenca il riepilogo di utilizzo per un periodo di fatturazione per un gruppo di gestione.|
|/budgets/read|Elenca i budget per una sottoscrizione o un gruppo di gestione.|
|/budgets/write|Crea, aggiorna ed elimina i budget per una sottoscrizione o un gruppo di gestione.|
|/marketplaces/read|Elenca i dettagli di utilizzo della risorsa del marketplace per un ambito per sottoscrizioni EA e WebDirect.|
|/operations/read|Elenca tutte le operazioni supportate per provider di risorse Microsoft.Consumption.|
|/pricesheets/read|Elenca i dati degli elenchi prezzi per una sottoscrizione o un gruppo di gestione.|
|/reservationDetails/read|Elenca i dettagli di utilizzo per le istanze riservate per ordine di prenotazione o gruppi di gestione. I dati dei dettagli sono a livello di istanza e giorno.|
|/reservationSummaries/read|Elenca il riepilogo di utilizzo per le istanze riservate per ordine di prenotazione o gruppi di gestione. I dati di riepilogo sono a livello mensile o giornaliero.|
|/reservationTransactions/read|Elenca la cronologia delle transazioni per le istanze riservate per gruppi di gestione.|
|/terms/read|Elenca i termini per una sottoscrizione o un gruppo di gestione.|
|/usageDetails/read|Elenca i dettagli di utilizzo per un ambito per sottoscrizioni EA e WebDirect.|

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

| Operazione | DESCRIZIONE |
|---|---|
|/containerGroups/containers/logs/read|Recupera i log per un contenitore specifico.|
|/containerGroups/delete|Elimina il gruppo di contenitori specifico.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per il gruppo di contenitori.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write|Crea e aggiorna l'impostazione di diagnostica per il gruppo di contenitori.|
|/containerGroups/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per il gruppo di contenitori.|
|/containerGroups/read|Ottiene tutti i gruppi di contenitori.|
|/containerGroups/write|Crea o aggiorna un gruppo di contenitori specifico.|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Operazione | DESCRIZIONE |
|---|---|
|/checkNameAvailability/read|Controlla se il nome del registro contenitori è disponibile per l'uso.|
|/locations/operationResults/read|Ottiene il risultato di un'operazione asincrona|
|/operations/read|Elenca tutte le operazioni API REST del Registro contenitori di Azure disponibili|
|/register/action|Registra la sottoscrizione per il provider di risorse registro contenitori e consente la creazione di registri contenitori.|
|/registries/delete|Elimina un registro contenitori.|
|/registries/eventGridFilters/delete|Elimina un filtro di Griglia di eventi da un registro contenitori.|
|/registries/eventGridFilters/read|Ottiene le proprietà del filtro di Griglia di eventi specificato o elenca tutti i filtri di Griglia di eventi per il registro contenitori specificato.|
|/registries/eventGridFilters/write|Crea o aggiorna un filtro di Griglia di eventi per un registro contenitori con i parametri specificati.|
|/registries/listCredentials/action|Elenca le credenziali di accesso per il registro contenitori specificato.|
|/registries/listUsages/read|Elenca gli utilizzi di quota per il registro contenitori specificato.|
|/registries/operationStatuses/read|Ottiene uno stato operazione registry async|
|/registries/read|Ottiene le proprietà del registro contenitori specificato o elenca tutti i registri contenitori sotto il gruppo di risorse o la sottoscrizione specificata.|
|/registries/regenerateCredential/action|Rigenera una delle credenziali di accesso per il registro contenitori specificato.|
|/registries/replications/delete|Elimina una replica da un registro contenitori.|
|/registries/replications/operationStatuses/read|Ottiene lo stato di un'operazione asincrona di replica|
|/registries/replications/read|Ottiene le proprietà della replica specificato o elenca tutte le repliche per il registro contenitori specificato.|
|/registries/replications/write|Crea o aggiorna una replica per un registro contenitori con i parametri specificati.|
|/registries/webhooks/delete|Elimina un webhook da un registro contenitori.|
|/registries/webhooks/getCallbackConfig/action|Ottiene la configurazione dell'URI del servizio e le intestazioni personalizzate per il webhook.|
|/registries/webhooks/listEvents/action|Elenca gli eventi di recenti per il webhook specificato.|
|/registries/webhooks/operationStatuses/read|Ottiene lo stato di un'operazione asincrona di webhook|
|/registries/webhooks/ping/action|Attiva un evento ping da inviare al webhook.|
|/registries/webhooks/read|Ottiene le proprietà del webhook specificato o elenca tutti i webhook per il registro contenitori specificato.|
|/registries/webhooks/write|Crea o aggiorna un webhook per un registro contenitori con i parametri specificati.|
|/registries/write|Crea o aggiorna un registro contenitori con i parametri specificati.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Operazione | DESCRIZIONE |
|---|---|
|/containerServices/delete|Elimina il servizio contenitore specificato|
|/containerServices/read|Ottiene il servizio contenitore specificato|
|/containerServices/write|Inserisce o aggiorna il servizio contenitore specificato|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Operazione | DESCRIZIONE |
|---|---|
|/applications/delete|Operazione di eliminazione|
|/applications/listSecrets/action|Elenca i segreti|
|/applications/listSingleSignOnToken/action|Esegue la lettura di token Single Sign-On|
|/applications/read|Operazione di lettura|
|/applications/write|Operazione di scrittura|
|/applications/write|Operazione di scrittura|
|/listCommunicationPreference/action|Elenca le preferenze di comunicazione|
|/operations/read|operazioni di lettura|
|/updateCommunicationPreference/action|Aggiorna le preferenze di comunicazione|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Operazione | DESCRIZIONE |
|---|---|
|/hubs/adobemetadata/action|Crea o aggiorna metadati Adobe di Customer Insights per Azure|
|/hubs/adobemetadata/read|Legge metadati Adobe di Customer Insights per Azure|
|/hubs/authorizationPolicies/delete|Elimina qualsiasi criterio di firma di accesso condiviso di Azure Customer Insights|
|/hubs/authorizationPolicies/read|Esegue la lettura di qualsiasi criterio di firma di accesso condiviso di Azure Customer Insights|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Rigenera la chiave primaria dei criteri di firma di accesso condiviso di Azure Customer Insights|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Rigenera la chiave secondaria dei criteri di firma di accesso condiviso di Azure Customer Insights|
|/hubs/authorizationPolicies/write|Crea o aggiorna qualsiasi criterio di firma di accesso condiviso di Azure Customer Insights|
|/hubs/connectors/activate/action|Attiva tutti i connettori di Customer Insights per Azure|
|/hubs/connectors/activate/action|Attiva tutti i connettori di Customer Insights per Azure|
|/hubs/connectors/delete|Elimina qualsiasi connettore Azure Customer Insights|
|/hubs/connectors/getruntimestatus/action|Ottiene lo stato di runtime di tutti i connettori di Customer Insights per Azure|
|/hubs/connectors/mappings/activate/action|Attiva tutti i mapping di connettori di Customer Insights per Azure|
|/hubs/connectors/mappings/delete|Elimina qualsiasi mapping di connettori Azure Customer Insights|
|/hubs/connectors/mappings/operations/read|Legge il risultato di tutte le operazioni di mapping di connettori di Customer Insights per Azure|
|/hubs/connectors/mappings/read|Esegue la lettura di qualsiasi mapping di connettori Azure Customer Insights|
|/hubs/connectors/mappings/write|Crea o aggiorna qualsiasi mapping di connettori Azure Customer Insights|
|/hubs/connectors/operations/read|Legge il risultato di tutte le operazioni dei connettori di Customer Insights per Azure|
|/hubs/connectors/read|Esegue la lettura di qualsiasi connettore Azure Customer Insights|
|/hubs/connectors/saveauthinfo/action|Crea o aggiorna le informazione di autenticazione dei connettori di Customer Insights per Azure|
|/hubs/connectors/update/action|Aggiorna tutti i connettori di Customer Insights per Azure|
|/hubs/connectors/write|Crea o aggiorna qualsiasi connettore Azure Customer Insights|
|/hubs/crmmetadata/action|Crea o aggiorna metadati CRM di Customer Insights per Azure|
|/hubs/crmmetadata/read|Legge metadati CRM di Customer Insights per Azure|
|/hubs/delete|Elimina qualsiasi hub di Azure Customer Insights|
|/hubs/gdpr/delete|Elimina il Regolamento generale sulla protezione dei dati di Customer Insights per Azure|
|/hubs/gdpr/read|Legge il Regolamento generale sulla protezione dei dati di Customer Insights per Azure|
|/hubs/gdpr/write|Crea o aggiorna il Regolamento generale sulla protezione dei dati di Customer Insights per Azure|
|/hubs/getbillingcredits/read|Ottiene i crediti di fatturazione di Customer Insights per Azure|
|/hubs/getbillinghistory/read|Ottiene la cronologia di fatturazione di Customer Insights per Azure|
|/hubs/images/delete|Elimina tutte le immagini di Customer Insights per Azure|
|/hubs/images/read|Legge tutte le immagini di Customer Insights per Azure|
|/hubs/images/write|Crea o aggiorna le immagini di Customer Insights per Azure|
|/hubs/interactions/delete|Elimina tutte le interazioni di Customer Insights per Azure|
|/hubs/interactions/operations/read|Legge il risultato di tutte le operazioni di interazione di Customer Insights per Azure|
|/hubs/interactions/read|Esegue la lettura di qualsiasi interazione di Azure Customer Insights|
|/hubs/interactions/suggestrelationshiplinks/action|Suggerisce collegamenti di relazioni per tutte le interazioni di Customer Insights per Azure|
|/hubs/interactions/write|Crea o aggiorna qualsiasi interazione di Azure Customer Insights|
|/hubs/kpi/delete|Elimina qualsiasi indicatore di prestazioni chiave Azure Customer Insights|
|/hubs/kpi/operations/read|Legge il risultato di tutte le operazioni degli indicatori di prestazioni chiave di Customer Insights per Azure|
|/hubs/kpi/read|Esegue la lettura di qualsiasi indicatore di prestazioni chiave di Azure Customer Insights|
|/hubs/kpi/reprocess/action|Rielabora gli indicatori di prestazioni chiave di Customer Insights per Azure|
|/hubs/kpi/write|Crea o aggiorna qualsiasi indicatore di prestazioni chiave Azure Customer Insights|
|/hubs/links/delete|Elimina tutti i collegamenti di Customer Insights per Azure|
|/hubs/links/operations/read|Legge il risultato di tutte le operazioni di collegamento di Customer Insights per Azure|
|/hubs/links/read|Legge i collegamenti di Customer Insights per Azure|
|/hubs/links/write|Crea o aggiorna i collegamenti di Customer Insights per Azure|
|/hubs/msemetadata/action|Crea o aggiorna metadati MSE di Customer Insights per Azure|
|/hubs/msemetadata/read|Legge metadati MSE di Customer Insights per Azure|
|/hubs/operationresults/read|Ottiene i risultati delle operazioni hub di Customer Insights per Azure|
|/hubs/predictions/delete|Elimina tutte le stime di Customer Insights per Azure|
|/hubs/predictions/operations/read|Legge il risultato di tutte le operazioni di stima di Customer Insights per Azure|
|/hubs/predictions/read|Legge le stime di Customer Insights per Azure|
|/hubs/predictions/write|Crea o aggiorna le stime di Customer Insights per Azure|
|/hubs/predictivematchpolicies/delete|Elimina tutti i criteri di corrispondenza predittiva di Customer Insights per Azure|
|/hubs/predictivematchpolicies/operations/read|Legge il risultato di tutte le operazioni di criteri di corrispondenza predittiva di Customer Insights per Azure|
|/hubs/predictivematchpolicies/read|Legge i criteri di corrispondenza predittiva di Customer Insights per Azure|
|/hubs/predictivematchpolicies/write|Crea o aggiorna i criterio di corrispondenza predittiva di Customer Insights per Azure|
|/hubs/profiles/delete|Elimina tutti i profili di Customer Insights per Azure|
|/hubs/profiles/operations/read|Legge il risultato di tutte le operazioni di profilo di Customer Insights per Azure|
|/hubs/profiles/read|Esegue la lettura di qualsiasi profilo di Azure Customer Insights|
|/hubs/profiles/write|Esegue la scrittura di qualsiasi profilo di Azure Customer Insights|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|Ottiene i log disponibili per la risorsa|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per la risorsa|
|/hubs/read|Esegue la lettura di qualsiasi hub di Azure Customer Insights|
|/hubs/relationshiplinks/delete|Elimina tutti i collegamenti di relazione di Customer Insights per Azure|
|/hubs/relationshiplinks/operations/read|Legge il risultato di tutte le operazioni di collegamento di relazione di Customer Insights per Azure|
|/hubs/relationshiplinks/read|Legge tutti i collegamenti di relazione di Customer Insights per Azure|
|/hubs/relationshiplinks/write|Crea o aggiorna i collegamenti di relazione di Customer Insights per Azure|
|/hubs/relationships/delete|Elimina tutte le relazioni di Customer Insights per Azure|
|/hubs/relationships/operations/read|Legge il risultato di tutte le operazioni di relazione di Customer Insights per Azure|
|/hubs/relationships/read|Legge tutte le relazioni di Customer Insights per Azure|
|/hubs/relationships/write|Crea o aggiorna le relazioni di Customer Insights per Azure|
|/hubs/roleAssignments/delete|Elimina qualsiasi assegnazione di controllo degli accessi in base al ruolo di Azure Customer Insights|
|/hubs/roleAssignments/operations/read|Legge il risultato di tutte le operazioni di assegnazione di controllo degli accessi in base al ruolo di Customer Insights per Azure|
|/hubs/roleAssignments/read|Esegue la lettura di qualsiasi assegnazione di controllo degli accessi in base al ruolo di Azure Customer Insights|
|/hubs/roleAssignments/write|Crea o aggiorna qualsiasi assegnazione di controllo degli accessi in base al ruolo di Azure Customer Insights|
|/hubs/roles/read|Legge tutti i ruoli del controllo degli accessi in base al ruolo di Customer Insights per Azure|
|/hubs/salesforcemetadata/action|Crea o aggiorna metadati SalesForce di Customer Insights per Azure|
|/hubs/salesforcemetadata/read|Legge metadati SalesForce di Customer Insights per Azure|
|/hubs/segments/delete|Elimina tutti i segmenti di Customer Insights per Azure|
|/hubs/segments/dynamic/action|Gestisce i segmenti dinamici di Customer Insights per Azure|
|/hubs/segments/read|Legge tutti i segmenti di Customer Insights per Azure|
|/hubs/segments/static/action|Gestisce i segmenti statici di Customer Insights per Azure|
|/hubs/segments/write|Crea o aggiorna i segmenti di Customer Insights per Azure|
|/hubs/sqlconnectionstrings/delete|Elimina tutte le stringhe di connessione SQL di Customer Insights per Azure|
|/hubs/sqlconnectionstrings/read|Legge tutte le stringhe di connessione SQL di Customer Insights per Azure|
|/hubs/sqlconnectionstrings/write|Crea o aggiorna le stringhe di connessione SQL di Customer Insights per Azure|
|/hubs/suggesttypeschema/action|Genera lo schema di tipo suggerito da dati di esempio|
|/hubs/tenantmanagement/read|Gestisce le impostazioni di hub di Customer Insights per Azure|
|/hubs/views/delete|Elimina qualsiasi visualizzazione app di Azure Customer Insights|
|/hubs/views/read|Esegue la lettura di qualsiasi visualizzazione app di Azure Customer Insights|
|/hubs/views/write|Crea o aggiorna qualsiasi visualizzazione app di Azure Customer Insights|
|/hubs/widgettypes/read|Legge tutti i tipi widget di app di Customer Insights per Azure|
|/hubs/write|Crea o aggiorna qualsiasi hub di Azure Customer Insights|
|/operations/read|Legge metadati API di Customer Insights per Azure|
|/register/action|Registra la sottoscrizione per il provider di risorse Customer Insights e consente la creazione di risorse Customer Insights|
|/unregister/action|Annulla la registrazione della sottoscrizione per il provider di risorse Customer Insights|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Operazione | DESCRIZIONE |
|---|---|
|/catalogs/delete|Elimina il catalogo.|
|/catalogs/read|Ottiene le proprietà del catalogo o dei cataloghi nella sottoscrizione o nel gruppo di risorse.|
|/catalogs/write|Crea il catalogo o ne aggiorna i tag e le proprietà.|
|/checkNameAvailability/action|Controlla la disponibilità del nome di catalogo per il tenant.|
|/operations/read|Elenca le operazioni disponibili nel provider di risorse Microsoft.DataCatalog.|
|/register/action|Registra la sottoscrizione con il provider di risorse Microsoft.DataCatalog.|
|/unregister/action|Annulla la registrazione della sottoscrizione dal provider di risorse Microsoft.DataCatalog.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Operazione | DESCRIZIONE |
|---|---|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/datafactories/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per le data factory|
|/factories/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/factories/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/factories/providers/Microsoft.Insights/logDefinitions/read|Ottiene i log disponibili per le factory|
|/factories/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per le factory|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Operazione | DESCRIZIONE |
|---|---|
|/accounts/computePolicies/delete|Elimina criteri di calcolo.|
|/accounts/computePolicies/read|Ottiene informazioni su criteri di calcolo.|
|/accounts/computePolicies/write|Crea o aggiorna i criteri di calcolo.|
|/accounts/dataLakeStoreAccounts/delete|Scollega un account Archivio Data Lake da un account Analisi Data Lake.|
|/accounts/dataLakeStoreAccounts/read|Ottiene informazioni su un account Archivio Data Lake collegato a un account Analisi Data Lake.|
|/accounts/dataLakeStoreAccounts/write|Crea o aggiorna un account Archivio Data Lake collegato a un account Analisi Data Lake.|
|/accounts/delete|Elimina un account Analisi Data Lake.|
|/accounts/firewallRules/delete|Elimina una regola del firewall.|
|/accounts/firewallRules/read|Ottiene informazioni su una regola del firewall.|
|/accounts/firewallRules/write|Crea o aggiorna una regola del firewall.|
|/accounts/operationResults/read|Ottiene i risultati di un'operazione di account Analisi Data Lake.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene le impostazioni di diagnostica per l'account Analisi Data Lake.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna le impostazioni di diagnostica per l'account Analisi Data Lake.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Ottiene i log disponibili per l'account Analisi Data Lake.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per l'account Analisi Data Lake.|
|/accounts/read|Ottiene informazioni su un account Analisi Data Lake esistente.|
|/accounts/storageAccounts/Containers/listSasTokens/action|Elenca i token SAS per i contenitori di archiviazione di un account di archiviazione collegato a un account Analisi Data Lake.|
|/accounts/storageAccounts/Containers/read|Ottiene i contenitori di un account di archiviazione collegato a un account Analisi Data Lake.|
|/accounts/storageAccounts/delete|Scollega un account di archiviazione da un account Analisi Data Lake.|
|/accounts/storageAccounts/read|Ottiene informazioni su un account di archiviazione collegato a un account Analisi Data Lake.|
|/accounts/storageAccounts/write|Crea o aggiorna un account di archiviazione collegato a un account Analisi Data Lake.|
|/accounts/TakeOwnership/action|Concede le autorizzazioni per annullare i processi inviati da altri utenti.|
|/accounts/write|Crea o aggiorna un account Analisi Data Lake.|
|/locations/capability/read|Ottiene informazioni sulla capacità di una sottoscrizione sull'utilizzo di Data Lake Analytics.|
|/locations/checkNameAvailability/action|Controlla la disponibilità di un nome di account Analisi Data Lake.|
|/locations/operationResults/read|Ottiene i risultati di un'operazione di account Analisi Data Lake.|
|/operations/read|Ottiene le operazioni disponibili di Data Lake Analytics.|
|/register/action|Registra la sottoscrizione a Data Lake Analytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Operazione | DESCRIZIONE |
|---|---|
|/accounts/delete|Elimina un account Archivio Data Lake.|
|/accounts/enableKeyVault/action|Abilita KeyVault per un account Archivio Data Lake.|
|/accounts/firewallRules/delete|Elimina una regola del firewall.|
|/accounts/firewallRules/read|Ottiene informazioni su una regola del firewall.|
|/accounts/firewallRules/write|Crea o aggiorna una regola del firewall.|
|/accounts/operationResults/read|Ottiene i risultati di un'operazione di account Archivio Data Lake.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene le impostazioni di diagnostica per l'account Archivio Data Lake.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna le impostazioni di diagnostica per l'account Archivio Data Lake.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Ottiene i log disponibili per l'account Archivio Data Lake.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per l'account Archivio Data Lake.|
|/accounts/read|Ottiene informazioni su un account Archivio Data Lake esistente.|
|/accounts/Superuser/action|Concede privilegi avanzati a un utente quando si usa Microsoft.Authorization/roleAssignments/write.|
|/accounts/trustedIdProviders/delete|Elimina un provider di identità attendibile.|
|/accounts/trustedIdProviders/read|Ottiene informazioni su un provider di identità attendibile.|
|/accounts/trustedIdProviders/write|Crea o aggiorna un provider di identità attendibile.|
|/accounts/write|Crea o aggiorna un account Archivio Data Lake.|
|/locations/capability/read|Ottiene informazioni sulla capacità di una sottoscrizione sull'utilizzo di Data Lake Store.|
|/locations/checkNameAvailability/action|Controlla la disponibilità di un nome di account Archivio Data Lake.|
|/locations/operationResults/read|Ottiene i risultati di un'operazione di account Archivio Data Lake.|
|/operations/read|Ottiene le operazioni disponibili di Data Lake Store.|
|/register/action|Registra la sottoscrizione a Data Lake Store.|

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

| Operazione | DESCRIZIONE |
|---|---|
|/locations/performanceTiers/read|Restituisce l'elenco dei livelli di prestazioni disponibili.|
|/performanceTiers/read|Restituisce l'elenco dei livelli di prestazioni disponibili.|
|/servers/delete|Elimina un server esistente.|
|/servers/firewallRules/delete|Elimina una regola firewall esistente.|
|/servers/firewallRules/read|Restituisce l'elenco delle regole del firewall per un server o ottiene le proprietà per la regola del firewall specificata.|
|/servers/firewallRules/write|Crea una regola del firewall con i parametri specificati o aggiorna una regola esistente.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Restituisce i tipi di metriche disponibili per i database|
|/servers/read|Restituisce l'elenco di server o ottiene le proprietà per il server specificato.|
|/servers/recoverableServers/read|Restituisce le informazioni recuperabili del server MySQL|
|/servers/virtualNetworkRules/delete|Elimina una regola di rete virtuale esistente|
|/servers/virtualNetworkRules/read|Restituisce l'elenco di regole di rete virtuale o ottiene le proprietà per la regola di rete virtuale specificata.|
|/servers/virtualNetworkRules/write|Crea una regola di rete virtuale con i parametri specificati o aggiorna le proprietà o i tag per la regola di rete virtuale specificata.|
|/servers/write|Crea un server con i parametri specificati o aggiorna le proprietà o i tag per il server specificato.|

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

| Operazione | DESCRIZIONE |
|---|---|
|/locations/performanceTiers/read|Restituisce l'elenco dei livelli di prestazioni disponibili.|
|/performanceTiers/read|Restituisce l'elenco dei livelli di prestazioni disponibili.|
|/servers/delete|Elimina un server esistente.|
|/servers/firewallRules/delete|Elimina una regola firewall esistente.|
|/servers/firewallRules/read|Restituisce l'elenco delle regole del firewall per un server o ottiene le proprietà per la regola del firewall specificata.|
|/servers/firewallRules/write|Crea una regola del firewall con i parametri specificati o aggiorna una regola esistente.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Restituisce i tipi di log disponibili per i database|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Restituisce i tipi di metriche disponibili per i database|
|/servers/read|Restituisce l'elenco di server o ottiene le proprietà per il server specificato.|
|/servers/recoverableServers/read|Restituisce le informazioni recuperabili del server PostgreSQL|
|/servers/virtualNetworkRules/delete|Elimina una regola di rete virtuale esistente|
|/servers/virtualNetworkRules/read|Restituisce l'elenco di regole di rete virtuale o ottiene le proprietà per la regola di rete virtuale specificata.|
|/servers/virtualNetworkRules/write|Crea una regola di rete virtuale con i parametri specificati o aggiorna le proprietà o i tag per la regola di rete virtuale specificata.|
|/servers/write|Crea un server con i parametri specificati o aggiorna le proprietà o i tag per il server specificato.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Operazione | DESCRIZIONE |
|---|---|
|/checkNameAvailability/Action|Controlla se il nome IotHub è disponibile|
|/checkProvisioningServiceNameAvailability/Action|Controlla se il nome IotHub è disponibile|
|/ElasticPools/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/ElasticPools/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/elasticPools/iotHubTenants/Delete|Elimina la risorsa tenant IotHub|
|/ElasticPools/IotHubTenants/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/ElasticPools/IotHubTenants/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete|Elimina un gruppo di consumer EventHub|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read|Ottiene il gruppo o i gruppi di consumer EventHub|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write|Crea un gruppo di consumer EventHub|
|/elasticPools/iotHubTenants/exportDevices/Action|Esporta dispositivi|
|/elasticPools/iotHubTenants/getStats/Read|Ottiene la risorsa di statistiche del tenant IotHub|
|/elasticPools/iotHubTenants/importDevices/Action|Importa dispositivi|
|/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action|Ottiene la chiave tenant IotHub|
|/elasticPools/iotHubTenants/jobs/Read|Ottiene i dettagli del processo o dei processi inviati per un servizio IotHub specifico|
|/elasticPools/iotHubTenants/listKeys/Action|Ottiene le chiavi tenant IotHub|
|/ElasticPools/IotHubTenants/logDefinitions/read|Ottiene le definizioni di log disponibili per il servizio IotHub|
|/ElasticPools/IotHubTenants/metricDefinitions/read|Ottiene la metrica disponibile per il servizio IotHub|
|/elasticPools/iotHubTenants/quotaMetrics/Read|Ottiene la metrica di quota|
|/elasticPools/iotHubTenants/Read|Ottiene la risorsa tenant IotHub|
|/elasticPools/iotHubTenants/routing/routes/$testall/Action|Effettua il test di un messaggio con tutte le route esistenti|
|/elasticPools/iotHubTenants/routing/routes/$testnew/Action|Effettua il test di un messaggio con la route di test fornita|
|/elasticPools/iotHubTenants/routingEndpointsHealth/Read|Ottiene lo stato di tutti gli endpoint di routing per un servizio IotHub|
|/elasticPools/iotHubTenants/Write|Crea o aggiorna la risorsa tenant IotHub|
|/ElasticPools/metricDefinitions/read|Ottiene la metrica disponibile per il servizio IotHub|
|/iotHubs/certificates/generateVerificationCode/Action|Genera il codice di verifica|
|/iotHubs/certificates/verify/Action|Verifica la risorsa certificato|
|/iotHubs/Delete|Elimina una risorsa IotHub|
|/IotHubs/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/IotHubs/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/iotHubs/eventGridFilters/Delete|Elimina il filtro di Griglia di eventi|
|/iotHubs/eventGridFilters/Read|Ottiene il filtro di Griglia di eventi|
|/iotHubs/eventGridFilters/Write|Crea un nuovo filtro di Griglia di eventi o ne aggiorna uno esistente|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|Elimina un gruppo di consumer EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Ottiene il gruppo o i gruppi di consumer EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Crea un gruppo di consumer EventHub|
|/iotHubs/exportDevices/Action|Esporta dispositivi|
|/iotHubs/importDevices/Action|Importa dispositivi|
|/iotHubs/iotHubKeys/listkeys/Action|Ottiene la chiave IotHub per il nome specificato|
|/iotHubs/iotHubStats/Read|Ottiene le statistiche IotHub|
|/iotHubs/jobs/Read|Ottiene i dettagli del processo o dei processi inviati per un servizio IotHub specifico|
|/iotHubs/listkeys/Action|Ottiene tutte le chiavi IotHub|
|/IotHubs/logDefinitions/read|Ottiene le definizioni di log disponibili per il servizio IotHub|
|/IotHubs/metricDefinitions/read|Ottiene la metrica disponibile per il servizio IotHub|
|/iotHubs/quotaMetrics/Read|Ottiene la metrica di quota|
|/iotHubs/Read|Ottiene la risorsa o le risorse IotHub|
|/iotHubs/routing/$testall/Action|Effettua il test di un messaggio con tutte le route esistenti|
|/iotHubs/routing/$testnew/Action|Effettua il test di un messaggio con la route di test fornita|
|/iotHubs/routingEndpointsHealth/Read|Ottiene lo stato di tutti gli endpoint di routing per un servizio IotHub|
|/iotHubs/skus/Read|Ottiene gli SKU IotHub validi|
|/iotHubs/Write|Crea o aggiorna una risorsa IotHub|
|/operations/Read|Ottiene tutte le operazioni ResourceProvider|
|/provisioningServices/certificates/generateVerificationCode/Action|Genera il codice di verifica|
|/provisioningServices/certificates/verify/Action|Verifica la risorsa certificato|
|/provisioningServices/Delete|Elimina la risorsa IotDps|
|/provisioningServices/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/provisioningServices/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/provisioningServices/listkeys/Action|Ottiene tutte le chiavi IotDps|
|/provisioningServices/logDefinitions/read|Ottiene le definizioni di log disponibili per il servizio di provisioning|
|/provisioningServices/metricDefinitions/read|Ottiene la metrica disponibile per il servizio di provisioning|
|/provisioningServices/ProvisioningServiceKeys/listkeys/Action|Ottiene le chiavi IotDps per il nome della chiave|
|/provisioningServices/Read|Ottiene la risorsa IotDps|
|/provisioningServices/skus/Read|Ottiene SKU IotDps validi|
|/provisioningServices/Write|Crea la risorsa IotDps|
|/register/action|Registra la sottoscrizione per il provider di risorse del servizio IotHub e consente la creazione di risorse IotHub|
|/register/action|Registra la sottoscrizione per il provider di risorse del servizio IotHub e consente la creazione di risorse IotHub|
|/usages/Read|Ottiene dettagli sull'utilizzo della sottoscrizione per questo provider.|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Operazione | DESCRIZIONE |
|---|---|
|/labCenters/delete|Elimina i centri del lab.|
|/labCenters/read|Legge i centri del lab.|
|/labCenters/write|Aggiunge o modifica i centri del lab.|
|/labs/artifactSources/armTemplates/read|Esegue la lettura di modelli di Azure Resource Manager.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Genera un modello ARM per l'elemento specificato, carica i file necessari in un account di archiviazione e convalida l'elemento generato.|
|/labs/artifactSources/artifacts/read|Esegue la lettura di elementi.|
|/labs/artifactSources/delete|Elimina origini elemento.|
|/labs/artifactSources/read|Esegue la lettura di origini elemento.|
|/labs/artifactSources/write|Aggiunge o modifica origini elemento.|
|/labs/ClaimAnyVm/action|Attesta una macchina virtuale attestabile casualmente nel lab.|
|/labs/costs/read|Esegue la lettura di costi.|
|/labs/costs/write|Aggiunge o modifica costi.|
|/labs/CreateEnvironment/action|Crea macchine virtuali in un lab.|
|/labs/customImages/delete|Elimina immagini personalizzate.|
|/labs/customImages/read|Esegue la lettura di immagini personalizzate.|
|/labs/customImages/write|Aggiunge o modifica immagini personalizzate.|
|/labs/delete|Elimina lab.|
|/labs/ExportResourceUsage/action|Esporta l'utilizzo delle risorse del lab in un account di archiviazione|
|/labs/formulas/delete|Elimina le formule.|
|/labs/formulas/read|Esegue la lettura di formule.|
|/labs/formulas/write|Aggiunge o modifica le formule.|
|/labs/galleryImages/read|Esegue la lettura delle immagini della raccolta.|
|/labs/GenerateUploadUri/action|Genera un URI per il caricamento di immagini disco personalizzate in un lab.|
|/labs/ImportVirtualMachine/action|Importa una macchina virtuale in un lab diverso.|
|/labs/ListVhds/action|Elenca le immagini di un disco disponibili per la creazione di immagini personalizzate.|
|/labs/notificationChannels/delete|Elimina canali di notifica.|
|/labs/notificationChannels/Notify/action|Invia la notifica al canale specificato.|
|/labs/notificationChannels/read|Esegue la lettura di canali di notifica.|
|/labs/notificationChannels/write|Aggiunge o modifica canali di notifica.|
|/labs/policySets/EvaluatePolicies/action|Valuta i criteri del lab.|
|/labs/policySets/policies/delete|Elimina i criteri.|
|/labs/policySets/policies/read|Esegue la lettura di criteri.|
|/labs/policySets/policies/write|Aggiunge o modifica i criteri.|
|/labs/read|Esegue la lettura di lab.|
|/labs/schedules/delete|Elimina le pianificazioni.|
|/labs/schedules/Execute/action|Esegue una pianificazione.|
|/labs/schedules/ListApplicable/action|Elenca tutte le pianificazioni applicabili|
|/labs/schedules/read|Esegue la lettura delle pianificazioni.|
|/labs/schedules/write|Aggiunge o modifica pianificazioni.|
|/labs/serviceRunners/delete|Elimina strumenti di esecuzione servizio.|
|/labs/serviceRunners/read|Esegue la lettura di strumenti di esecuzione servizio.|
|/labs/serviceRunners/write|Aggiunge o modifica strumenti di esecuzione servizio.|
|/labs/users/delete|Elimina profili utente.|
|/labs/users/disks/Attach/action|Collegare e creare il lease del disco alla macchina virtuale.|
|/labs/users/disks/delete|Elimina dischi.|
|/labs/users/disks/Detach/action|Rimuove il disco collegato alla macchina virtuale e interrompe il lease.|
|/labs/users/disks/read|Esegue la lettura di dischi.|
|/labs/users/disks/write|Aggiunge o modifica dischi.|
|/labs/users/environments/delete|Elimina ambienti.|
|/labs/users/environments/read|Esegue la lettura di ambienti.|
|/labs/users/environments/write|Aggiunge o modifica ambienti.|
|/labs/users/read|Esegue la lettura di profili utente.|
|/labs/users/secrets/delete|Elimina segreti.|
|/labs/users/secrets/read|Esegue la lettura di segreti.|
|/labs/users/secrets/write|Aggiunge o modifica segreti.|
|/labs/users/serviceFabrics/delete|Elimina le infrastrutture di servizi.|
|/labs/users/serviceFabrics/ListApplicableSchedules/action|Elenca tutte le pianificazioni applicabili|
|/labs/users/serviceFabrics/read|Legge le infrastrutture di servizi.|
|/labs/users/serviceFabrics/schedules/delete|Elimina le pianificazioni.|
|/labs/users/serviceFabrics/schedules/Execute/action|Esegue una pianificazione.|
|/labs/users/serviceFabrics/schedules/read|Esegue la lettura delle pianificazioni.|
|/labs/users/serviceFabrics/schedules/write|Aggiunge o modifica pianificazioni.|
|/labs/users/serviceFabrics/Start/action|Avvia un'infrastruttura di servizi.|
|/labs/users/serviceFabrics/Stop/action|Arresta un'infrastruttura di servizi|
|/labs/users/serviceFabrics/write|Aggiunge o modifica le infrastrutture di servizi.|
|/labs/users/write|Aggiunge o modifica profili utente.|
|/labs/virtualMachines/AddDataDisk/action|Collega un disco dati nuovo o esistente alla macchina virtuale.|
|/labs/virtualMachines/ApplyArtifacts/action|Applica elementi a una macchina virtuale.|
|/labs/virtualMachines/Claim/action|Consente di assumere la proprietà di una macchina virtuale esistente|
|/labs/virtualMachines/delete|Elimina macchine virtuali.|
|/labs/virtualMachines/DetachDataDisk/action|Rimuove il disco specificato dalla macchina virtuale.|
|/labs/virtualMachines/ListApplicableSchedules/action|Elenca tutte le pianificazioni applicabili|
|/labs/virtualMachines/read|Esegue la lettura di macchine virtuali.|
|/labs/virtualMachines/Restart/action|Riavvia una macchina virtuale.|
|/labs/virtualMachines/schedules/delete|Elimina le pianificazioni.|
|/labs/virtualMachines/schedules/Execute/action|Esegue una pianificazione.|
|/labs/virtualMachines/schedules/read|Esegue la lettura delle pianificazioni.|
|/labs/virtualMachines/schedules/write|Aggiunge o modifica pianificazioni.|
|/labs/virtualMachines/Start/action|Avvia una macchina virtuale.|
|/labs/virtualMachines/Stop/action|Arrestare una macchina virtuale|
|/labs/virtualMachines/TransferDisks/action|Trasferisce la proprietà dei dischi della macchina virtuale all'utente|
|/labs/virtualMachines/UnClaim/action|Rilascia la proprietà di una macchina virtuale esistente|
|/labs/virtualMachines/write|Aggiunge o modifica macchine virtuali.|
|/labs/virtualNetworks/delete|Elimina reti virtuali.|
|/labs/virtualNetworks/read|Esegue la lettura di reti virtuali.|
|/labs/virtualNetworks/write|Aggiunge o modifica reti virtuali.|
|/labs/write|Aggiunge o modifica lab.|
|/locations/operations/read|Esegue la lettura delle operazioni.|
|/register/action|Registra la sottoscrizione.|
|/schedules/delete|Elimina le pianificazioni.|
|/schedules/Execute/action|Esegue una pianificazione.|
|/schedules/read|Esegue la lettura delle pianificazioni.|
|/schedules/Retarget/action|Aggiorna l'ID risorsa di destinazione di una pianificazione.|
|/schedules/write|Aggiunge o modifica pianificazioni.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Operazione | DESCRIZIONE |
|---|---|
|/databaseAccountNames/read|Controlla la disponibilità del nome.|
|/databaseAccounts/changeResourceGroup/action|Modifica il gruppo di risorse di un account di database|
|/databaseAccounts/databases/collections/metricDefinitions/read|Esegue la lettura delle definizioni delle metriche delle raccolte.|
|/databaseAccounts/databases/collections/metrics/read|Esegue la lettura della metrica delle raccolte.|
|/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read|Legge la metrica a livello di chiave della partizione dell'account di database|
|/databaseAccounts/databases/collections/partitions/metrics/read|Legge la metrica a livello della partizione dell'account di database|
|/databaseAccounts/databases/collections/partitions/usages/read|Legge gli utilizzi a livello della partizione dell'account di database|
|/databaseAccounts/databases/collections/usages/read|Esegue la lettura degli utilizzi delle raccolte.|
|/databaseAccounts/databases/metricDefinitions/read|Esegue la lettura delle definizioni delle metriche dei database|
|/databaseAccounts/databases/metrics/read|Esegue la lettura della metrica dei database.|
|/databaseAccounts/databases/usages/read|Esegue la lettura degli utilizzi dei database.|
|/databaseAccounts/delete|Elimina gli account di database.|
|/databaseAccounts/failoverPriorityChange/action|Modifica le priorità di failover di aree di un account di database. Usata per eseguire l'operazione di failover manuale|
|/databaseAccounts/listConnectionStrings/action|Ottiene le stringhe di connessione per un account di database|
|/databaseAccounts/listKeys/action|Elenca le chiavi di un account di database|
|/databaseAccounts/metricDefinitions/read|Esegue la lettura delle definizioni delle metriche degli account di database.|
|/databaseAccounts/metrics/read|Esegue la lettura della metrica degli account di database.|
|/databaseAccounts/operationResults/read|Legge lo stato dell'operazione asincrona|
|/databaseAccounts/percentile/metrics/read|Metrica di latenza di lettura|
|/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read|Legge la metrica di latenza per una specifica area di origine e di destinazione|
|/databaseAccounts/percentile/targetRegion/metrics/read|Legge la metrica di latenza per una specifica area di destinazione|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read|Ottiene le categorie di log disponibili per l'account del database|
|/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per l'account del database|
|/databaseAccounts/read|Esegue la lettura di un account di database.|
|/databaseAccounts/readonlykeys/action|Legge le chiavi di sola lettura degli account di database.|
|/databaseAccounts/readonlykeys/read|Legge le chiavi di sola lettura degli account di database.|
|/databaseAccounts/regenerateKey/action|Ruota le chiavi di un account di database|
|/databaseAccounts/region/databases/collections/metrics/read|Legge la metrica delle raccolte dell'area.|
|/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read|Legge la metrica a livello di chiave della partizione dell'account di database dell'area|
|/databaseAccounts/region/databases/collections/partitions/metrics/read|Legge la metrica a livello della partizione dell'account di database dell'area|
|/databaseAccounts/region/databases/collections/partitions/read|Legge le partizioni degli account del database in una raccolta|
|/databaseAccounts/region/metrics/read|Legge la metrica degli account per il database e l'area.|
|/databaseAccounts/usages/read|Esegue la lettura degli utilizzi degli account di database.|
|/databaseAccounts/write|Aggiorna un account di database.|
|/locations/deleteVirtualNetworkOrSubnets/action|Avvisa Microsoft.DocumentDB che la rete virtuale o la subnet è in fase di eliminazione|
|/operationResults/read|Legge lo stato dell'operazione asincrona|
|/operations/read|Legge le operazioni disponibili per Microsoft DocumentDB |
|/register/action| Registra il provider di risorse Microsoft DocumentDB per la sottoscrizione|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Operazione | DESCRIZIONE |
|---|---|
|/checkDomainAvailability/Action|Controlla se un dominio è disponibile per l'acquisto|
|/domains/Delete|Elimina un dominio esistente.|
|/domains/domainownershipidentifiers/Delete|Elimina l'identificatore di proprietà|
|/domains/domainownershipidentifiers/Read|Elenca gli identificatori di proprietà|
|/domains/domainownershipidentifiers/Read|Ottiene l'identificatore di proprietà|
|/domains/domainownershipidentifiers/Write|Crea o aggiorna l'identificatore|
|/domains/operationresults/Read|Ottiene un'operazione di dominio|
|/domains/operations/Read|Elenca tutte le operazioni dalla registrazione del dominio del servizio app|
|/domains/Read|Ottiene l'elenco dei domini|
|/domains/Read|Ottiene il dominio|
|/domains/renew/Action|Rinnova un dominio esistente.|
|/domains/Write|Aggiunge un nuovo dominio o ne aggiorna uno esistente|
|/generateSsoRequest/Action|Genera una richiesta per l'accesso al centro di controllo del dominio.|
|/listDomainRecommendations/Action|Recupera l'elenco di consigli sui domini in base alle parole chiave|
|/register/action|Registra il provider di risorse domini Microsoft per la sottoscrizione|
|/topLevelDomains/listAgreements/Action|Azione elenco contratti|
|/topLevelDomains/Read|Ottiene i domini di massimo livello|
|/topLevelDomains/Read|Ottiene il dominio di massimo livello|
|/validateDomainRegistrationInformation/Action|Convalida l'oggetto acquisto di dominio senza inviarlo|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Operazione | DESCRIZIONE |
|---|---|
|/lcsprojects/clouddeployments/read|Visualizza le distribuzioni di valutazione di Microsoft Dynamics AX 2012 R3 in un progetto dei servizi del ciclo di vita di Microsoft Dynamics che appartiene a un utente|
|/lcsprojects/clouddeployments/write|Crea una distribuzione di valutazione di Microsoft Dynamics AX 2012 R3 in un progetto dei servizi del ciclo di vita di Microsoft Dynamics che appartiene a un utente. Le distribuzioni possono essere gestite dal portale di gestione di Azure|
|/lcsprojects/connectors/read|Legge i connettori che appartengono al progetto dei servizi del ciclo di vita Microsoft Dynamics|
|/lcsprojects/connectors/write|Crea e aggiorna i connettori che appartengono al progetto dei servizi del ciclo di vita Microsoft Dynamics|
|/lcsprojects/delete|Elimina i progetti dei servizi del ciclo di vita Microsoft Dynamics che appartengono all'utente|
|/lcsprojects/read|Visualizza i progetti dei servizi del ciclo di vita Microsoft Dynamics che appartengono a un utente|
|/lcsprojects/write|Crea e aggiorna i progetti dei servizi del ciclo di vita Microsoft Dynamics che appartengono all'utente. Solo le proprietà nome e descrizione possono essere aggiornate. La sottoscrizione e il percorso associati al progetto non possono essere aggiornati dopo la creazione|

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

| Operazione | DESCRIZIONE |
|---|---|
|/eventSubscriptions/delete|Elimina una sottoscrizione evento|
|/eventSubscriptions/getFullUrl/action|Ottiene l'URL completo per la sottoscrizione evento|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per le sottoscrizioni evento|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per le sottoscrizioni evento|
|/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per le sottoscrizioni evento|
|/eventSubscriptions/read|Legge una sottoscrizione evento|
|/eventSubscriptions/write|Crea o aggiorna una sottoscrizione evento|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per gli argomenti|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per gli argomenti|
|/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per gli argomenti|
|/register/action|Registra la sottoscrizione evento per il provider di risorse EventGrid e abilita la creazione di sottoscrizioni Griglia di eventi.|
|/topics/delete|Eliminare un argomento|
|/topics/listKeys/action|Elenca le chiavi per l'argomento|
|/topics/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per gli argomenti|
|/topics/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per gli argomenti|
|/topics/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per gli argomenti|
|/topics/read|Legge un argomento|
|/topics/regenerateKey/action|Rigenera la chiave per l'argomento|
|/topics/write|Crea o aggiorna un argomento|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Operazione | DESCRIZIONE |
|---|---|
|/checkNameAvailability/action|Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata.|
|/checkNamespaceAvailability/action|Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata. Questa API è deprecata, usare invece CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/action|Aggiorna la regola di autorizzazione dello spazio dei nomi. Questa API è deprecata. Usare invece una chiamata PUT per aggiornare la regola di autorizzazione dello spazio dei nomi... Questa operazione non supportata per l'API versione 2017-04-01.|
|/namespaces/authorizationRules/delete|Elimina regola di autorizzazione dello spazio dei nomi. Non è possibile eliminare la regola di autorizzazione dello spazio dei nomi predefinita. |
|/namespaces/authorizationRules/listkeys/action|Ottiene la stringa di connessione per lo spazio dei nomi|
|/namespaces/authorizationRules/read|Ottiene l'elenco di descrizioni delle regole di autorizzazione degli spazi dei nomi.|
|/namespaces/authorizationRules/regenerateKeys/action|Rigenera la chiave primaria o secondaria per la risorsa|
|/namespaces/authorizationRules/write|Crea regole di autorizzazione a livello dello spazio dei nomi e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/namespaces/Delete|Elimina una risorsa spazio dei nomi|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Ottiene le chiavi delle regole di autorizzazione per lo spazio dei nomi primario di ripristino di emergenza|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Ottiene le regole di autorizzazione dello spazio dei nomi primario di ripristino di emergenza|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Disabilita il ripristino di emergenza e arresta la replica delle modifiche dagli spazi dei nomi primari a quelli secondari.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Verifica la disponibilità di alias dello spazio dei nomi nella sottoscrizione specificata.|
|/namespaces/disasterRecoveryConfigs/delete|Elimina la configurazione di ripristino di emergenza associata allo spazio dei nomi. Questa operazione può essere chiamata solo tramite lo spazio dei nomi primario.|
|/namespaces/disasterRecoveryConfigs/failover/action|Chiama il failover di ripristino di emergenza con ridondanza geografica e riconfigura l'alias dello spazio dei nomi affinché punti allo spazio dei nomi secondario.|
|/namespaces/disasterRecoveryConfigs/read|Recupera la configurazione di ripristino di emergenza associata allo spazio dei nomi.|
|/namespaces/disasterRecoveryConfigs/write|Crea o aggiorna la configurazione di ripristino di emergenza associata allo spazio dei nomi.|
|/namespaces/eventhubs/authorizationRules/action|Operazione per aggiornare EventHub. Questa operazione non supportata per l'API versione 2017-04-01. Regole di autorizzazione. Usare una chiamata PUT per aggiornare la regola di autorizzazione.|
|/namespaces/eventhubs/authorizationRules/delete|Operazione per l'eliminazione di regole di autorizzazione dell'hub eventi|
|/namespaces/eventhubs/authorizationRules/listkeys/action|Ottiene la stringa di connessione per l'hub eventi|
|/namespaces/eventhubs/authorizationRules/read| Ottiene l'elenco delle regole di autorizzazione dell'hub eventi|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|Rigenera la chiave primaria o secondaria per la risorsa|
|/namespaces/eventhubs/authorizationRules/write|Crea regole di autorizzazione dell'hub eventi e ne aggiorna le proprietà. È possibile aggiornare i diritti di accesso delle regole di autorizzazione.|
|/namespaces/eventHubs/consumergroups/Delete|Operazione per l'eliminazione di risorse gruppo consumer|
|/namespaces/eventHubs/consumergroups/read|Ottiene l'elenco delle descrizioni delle risorse gruppo consumer|
|/namespaces/eventHubs/consumergroups/write|Crea o aggiorna le proprietà del gruppo consumer.|
|/namespaces/eventhubs/Delete|Operazione per l'eliminazione di risorse hub eventi|
|/namespaces/eventhubs/read|Ottiene l'elenco delle descrizioni delle risorse dell'hub eventi|
|/namespaces/eventhubs/write|Crea o aggiorna le proprietà dell'hub eventi.|
|/namespaces/messagingPlan/read|Ottiene il piano di messaggistica per uno spazio dei nomi. Questa API è deprecata. Le proprietà esposte tramite la risorsa MessagingPlan sono state spostate nella risorsa spazio dei nomi, padre, nelle versioni API successive. Questa operazione non supportata per l'API versione 2017-04-01.|
|/namespaces/messagingPlan/write|Aggiorna il piano di messaggistica per uno spazio dei nomi. Questa API è deprecata. Le proprietà esposte tramite la risorsa MessagingPlan sono state spostate nella risorsa spazio dei nomi, padre, nelle versioni API successive. Questa operazione non supportata per l'API versione 2017-04-01.|
|/namespaces/operationresults/read|Operazione di recupero dello stato dello spazio dei nomi|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'elenco di descrizioni delle risorse impostazioni di diagnostica dello spazio dei nomi|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Ottiene l'elenco di descrizioni delle risorse impostazioni di diagnostica dello spazio dei nomi|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Ottiene l'elenco di descrizioni delle risorse log dello spazio dei nomi|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Ottiene l'elenco di descrizioni delle risorse di metrica dello spazio dei nomi|
|/namespaces/read|Ottiene l'elenco delle descrizioni delle risorse spazio dei nomi|
|/namespaces/write|Crea una risorsa spazio dei nomi e ne aggiorna le proprietà. Le proprietà che si possono aggiornare sono quelle relative ai tag e alla capacità dello spazio dei nomi.|
|/operations/read|Ottiene operazioni|
|/register/action|Registra la sottoscrizione per il provider di risorse hub eventi e abilita la creazione di risorse hub eventi|
|/sku/read|Ottiene l'elenco delle descrizioni di una risorsa SKU|
|/sku/regions/read|Ottiene l'elenco delle descrizioni di una risorsa SkuRegions|
|/unregister/action|Registra il provider di risorse hub eventi|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Operazione | DESCRIZIONE |
|---|---|
|/features/read|Ottiene le funzionalità di una sottoscrizione.|
|/providers/features/read|Ottiene la funzionalità di una sottoscrizione in un provider di risorse specificato.|
|/providers/features/register/action|Registra la funzionalità per una sottoscrizione in un provider di risorse specificato.|
|/providers/features/unregister/action|Annulla la registrazione della funzionalità per una sottoscrizione in un provider di risorse specificato.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Operazione | DESCRIZIONE |
|---|---|
|/clusters/changerdpsetting/action|Modifica l'impostazione RDP per il cluster HDInsight|
|/clusters/configurations/action|Aggiorna la configurazione del cluster HDInsight|
|/clusters/configurations/read|Ottiene le configurazioni del cluster HDInsight|
|/clusters/delete|Elimina un cluster HDInsight|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per il cluster HDInsight della risorsa|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per il cluster HDInsight della risorsa|
|/clusters/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per il cluster HDInsight|
|/clusters/read|Ottiene informazioni sul cluster HDInsight|
|/clusters/roles/resize/action|Ridimensiona un cluster HDInsight|
|/clusters/write|Crea o aggiorna un cluster HDInsight|
|/locations/capabilities/read|Ottiene le funzionalità di sottoscrizione|
|/locations/checkNameAvailability/read|Controlla la disponibilità del nome|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Operazione | DESCRIZIONE |
|---|---|
|/jobs/delete|Elimina il processo esistente.|
|/jobs/listBitLockerKeys/action|Ottiene le chiavi BitLocker per il processo specificato.|
|/jobs/read|Ottiene le proprietà per il processo specificato o restituisce l'elenco dei processi.|
|/jobs/write|Crea un processo con i parametri specificati o aggiorna le proprietà o i tag per il processo specificato.|
|/locations/read|Ottiene le proprietà per il percorso specificato o restituisce l'elenco dei percorsi.|
|/register/action|Registra la sottoscrizione per il provider di risorse importazione/esportazione e consente la creazione di processi di importazione/esportazione.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Operazione | DESCRIZIONE |
|---|---|
|/ActionGroups/Delete|Eliminazione di un gruppo di azioni|
|/ActionGroups/Read|Lettura di un gruppo di azioni|
|/ActionGroups/Write|Scrittura di un gruppo di azioni|
|/ActivityLogAlerts/Activated/Action|L'avviso del log attività è stato attivato|
|/ActivityLogAlerts/Delete|Eliminazione di un avviso del log attività|
|/ActivityLogAlerts/Read|Lettura di un avviso del log attività|
|/ActivityLogAlerts/Write|Lettura di un avviso del log attività|
|/AlertRules/Activated/Action|Regola di avviso attivata|
|/AlertRules/Delete|Eliminazione di una configurazione di regola di avviso|
|/AlertRules/Incidents/Read|Lettura di una configurazione di eventi imprevisti associati alla regola di avviso|
|/AlertRules/Read|Lettura di una configurazione di regola di avviso|
|/AlertRules/Resolved/Action|Regola di avviso risolta|
|/AlertRules/Throttled/Action|La regola di avviso è limitata|
|/AlertRules/Write|Scrittura in una configurazione di regola di avviso|
|/AutoscaleSettings/Delete|Eliminazione di una configurazione di impostazione di scalabilità automatica|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|Consente di leggere le definizioni della metrica|
|/AutoscaleSettings/Read|Lettura di una configurazione di impostazione di scalabilità automatica|
|/AutoscaleSettings/Scaledown/Action|Scalabilità automatica: operazione di riduzione|
|/AutoscaleSettings/Scaleup/Action|Scalabilità automatica: operazione di aumento|
|/AutoscaleSettings/Write|Scrittura in una configurazione di impostazione di scalabilità automatica|
|/Components/AnalyticsItems/Delete|Eliminazione di un elemento di analisi di Application Insights|
|/Components/AnalyticsItems/Read|Lettura di un elemento di analisi di Application Insights|
|/Components/AnalyticsItems/Write|Scrittura di un elemento di analisi di Application Insights|
|/Components/AnalyticsTables/Action|Azione di tabella di analisi di Application Insights|
|/Components/AnalyticsTables/Delete|Eliminazione di uno schema tabella di analisi di Application Insights|
|/Components/AnalyticsTables/Read|Lettura di uno schema tabella di analisi di Application Insights|
|/Components/AnalyticsTables/Write|Scrittura di uno schema tabella di analisi di Application Insights|
|/Components/Annotations/Delete|Eliminazione di un'annotazione di Application Insights|
|/Components/Annotations/Read|Lettura di un'annotazione di Application Insights|
|/Components/Annotations/Write|Scrittura di un'annotazione di Application Insights|
|/Components/Api/Read|Lettura dell'API dei dati dei componenti di Application Insights|
|/Components/ApiKeys/Action|Generazione di una chiave API di Application Insights|
|/Components/ApiKeys/Delete|Eliminazione di una chiave API di Application Insights|
|/Components/ApiKeys/Read|Lettura di una chiave API di Application Insights|
|/Components/BillingPlanForComponent/Read|Lettura di un piano di fatturazione per un componente di Application Insights|
|/Components/CurrentBillingFeatures/Read|Lettura delle funzioni di fatturazione correnti per un componente di Application Insights|
|/Components/CurrentBillingFeatures/Write|Scrittura delle funzioni di fatturazione correnti per un componente di Application Insights|
|/Components/DefaultWorkItemConfig/Read|Lettura di una configurazione di integrazione di ALM predefinita di Application Insights|
|/Components/Delete|Eliminazione della configurazione di un componente di Application Insights|
|/Components/ExportConfiguration/Action|Azione di impostazioni di esportazione di Application Insights|
|/Components/ExportConfiguration/Delete|Eliminazione delle impostazioni di esportazione di Application Insights|
|/Components/ExportConfiguration/Read|Lettura delle impostazioni di esportazione di Application Insights|
|/Components/ExportConfiguration/Write|Scrittura delle impostazioni di esportazione di Application Insights|
|/Components/ExtendQueries/Read|Lettura dei risultati di query estesi del componente di Application Insights|
|/Components/Favorites/Delete|Eliminazione di un preferito di Application Insights|
|/Components/Favorites/Read|Lettura di un preferito di Application Insights|
|/Components/Favorites/Write|Scrittura di un preferito di Application Insights|
|/Components/FeatureCapabilities/Read|Lettura delle capacità delle funzioni di un componente di Application Insights|
|/Components/GetAvailableBillingFeatures/Read|Lettura delle funzioni di fatturazione disponibili per un componente di Application Insights|
|/Components/GetToken/Read|Lettura del token di un componente di Application Insights|
|/Components/ListMigrationDate/Action|Riottiene la data di migrazione della sottoscrizione|
|/Components/ListMigrationDate/Read|Riottiene la data di migrazione della sottoscrizione|
|/Components/MetricDefinitions/Read|Lettura delle definizioni della metrica di un componente di Application Insights|
|/Components/Metrics/Read|Lettura della metrica di un componente di Application Insights|
|/Components/MigrateToNewpricingModel/Action|Migra la sottoscrizione a un nuovo modello di determinazione dei prezzi|
|/Components/Move/Action|Sposta un componente di Application Insights in un altro gruppo di risorse o sottoscrizione|
|/Components/MyAnalyticsItems/Delete|Eliminazione di un elemento di analisi personale di Application Insights|
|/Components/MyAnalyticsItems/Read|Lettura di un elemento di analisi personale di Application Insights|
|/Components/MyAnalyticsItems/Write|Scrittura di un elemento di analisi personale di Application Insights|
|/Components/MyFavorites/Read|Lettura di un preferito personale di Application Insights|
|/Components/PricingPlans/Read|Lettura del piano di determinazione dei prezzi di un componente di Application Insights|
|/Components/PricingPlans/Write|Scrittura del piano di determinazione dei prezzi di un componente di Application Insights|
|/Components/ProactiveDetectionConfigs/Read|Lettura della configurazione di rilevamento proattivo di Application Insights|
|/Components/ProactiveDetectionConfigs/Write|Scrittura della configurazione di rilevamento proattivo di Application Insights|
|/Components/providers/Microsoft.Insights/MetricDefinitions/Read|Consente di leggere le definizioni della metrica|
|/Components/QuotaStatus/Read|Lettura dello stato delle quote di un componente di Application Insights|
|/Components/Read|Lettura della configurazione di un componente di Application Insights|
|/Components/RollbackToLegacyPricingModel/Action|Ripristino del modello di determinazione dei prezzi precedente di una sottoscrizione|
|/Components/SyntheticMonitorLocations/Read|Lettura delle posizioni dei test Web di Application Insights|
|/Components/WorkItemConfigs/Delete|Eliminazione di una configurazione di integrazione di ALM di Application Insights|
|/Components/WorkItemConfigs/Read|Lettura di una configurazione di integrazione di ALM di Application Insights|
|/Components/WorkItemConfigs/Write|Scrittura di una configurazione di integrazione di ALM di Application Insights|
|/Components/Write|Scrittura nella configurazione di un componente di Application Insights|
|/DiagnosticSettings/Delete|È in corso l'eliminazione di una configurazione di impostazioni di diagnostica|
|/DiagnosticSettings/Read|È in corso la lettura di una configurazione di impostazioni di diagnostica|
|/DiagnosticSettings/Write|È in corso la scrittura in una configurazione di impostazioni di diagnostica|
|/EventCategories/Read|Lettura di una categoria di eventi|
|/eventtypes/digestevents/Read|Consente di leggere il digest dei tipi di evento di gestione|
|/eventtypes/values/Read|Consente di leggere i valori dei tipi di evento di gestione|
|/ExtendedDiagnosticSettings/Delete|È in corso l'eliminazione di una configurazione di impostazioni di diagnostica estese|
|/ExtendedDiagnosticSettings/Read|È in corso la lettura di una configurazione di impostazioni di diagnostica estese|
|/ExtendedDiagnosticSettings/Write|È in corso la scrittura in una configurazione di impostazioni di diagnostica estese|
|/LogDefinitions/Read|Consente di leggere le definizioni del log|
|/LogProfiles/Delete|Elimina configurazione dei profili di log|
|/LogProfiles/Read|Lettura profili di log|
|/LogProfiles/Write|Scrittura in una configurazione di profilo di log|
|/MetricAlerts/Delete|Eliminazione di un avviso di metrica|
|/MetricAlerts/Read|Lettura di un avviso di metrica|
|/MetricAlerts/Write|Scrittura di un avviso di metrica|
|/MetricDefinitions/Microsoft.Insights/Read|Consente di leggere le definizioni della metrica|
|/MetricDefinitions/providers/Microsoft.Insights/Read|Consente di leggere le definizioni della metrica|
|/MetricDefinitions/Read|Consente di leggere le definizioni della metrica|
|/Metrics/providers/Metrics/Read|Esegue la lettura delle metriche|
|/Metrics/Read|Esegue la lettura delle metriche|
|/Metrics/Write|Scrive metrica|
|/Operations/Read|Lettura di operazioni|
|/Register/Action|Registra il provider Microsoft Insights|
|/Tenants/Register/Action|Inizializza il provider Microsoft Insights|
|/Unregister/Action|Registra il provider Microsoft Insights|
|/Webtests/Delete|Eliminazione della configurazione di un test Web|
|/Webtests/GetToken/Read|Lettura di un token di test Web|
|/Webtests/MetricDefinitions/Read|Lettura delle definizioni della metrica un test Web|
|/Webtests/Metrics/Read|Lettura della metrica di un test Web|
|/Webtests/Read|Lettura della configurazione di un test Web|
|/Webtests/Write|Scrittura della configurazione di un test Web|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Operazione | DESCRIZIONE |
|---|---|
|/checkNameAvailability/read|Controlla che il nome dell'insieme di credenziali delle chiavi sia valido e che non sia in uso|
|/deletedVaults/read|Visualizza le proprietà di Key Vault eliminati temporaneamente|
|/hsmPools/delete|Elimina un pool di moduli di protezione hardware|
|/hsmPools/joinVault/action|Aggiunge un insieme di credenziali delle chiavi a un pool di moduli di protezione hardware|
|/hsmPools/read|Visualizza le proprietà di un pool di moduli di protezione hardware|
|/hsmPools/write|Crea un nuovo pool di moduli di protezione hardware o aggiorna le proprietà di un pool di moduli di protezione hardware esistente|
|/locations/deletedVaults/purge/action|Ripulisce un Key Vault eliminato temporaneamente|
|/locations/deletedVaults/read|Visualizza le proprietà di un Key Vault eliminato temporaneamente|
|/locations/deleteVirtualNetworkOrSubnets/action|Notifica a Microsoft.KeyVault che è in corso l'eliminazione di una rete virtuale o di una subnet|
|/locations/operationResults/read|Controlla il risultato di un'operazione a esecuzione prolungata|
|/operations/read|Elenca le operazioni disponibili nel provider di risorse Microsoft.KeyVault|
|/register/action|Registra una sottoscrizione|
|/unregister/action|Annulla la registrazione di una sottoscrizione|
|/vaults/accessPolicies/write|Aggiorna un criterio di accesso esistente unendo o sostituendo oppure aggiungendo un nuovo criterio di accesso a un insieme di credenziali.|
|/vaults/delete|Elimina un insieme di credenziali delle chiavi|
|/vaults/deploy/action|Consente l'accesso ai segreti in un insieme di credenziali delle chiavi durante la distribuzione di risorse di Azure|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Read|Ottiene l'impostazione di diagnostica per la risorsa|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/vaults/providers/Microsoft.Insights/logDefinitions/read|Ottiene i log disponibili per un insieme di credenziali delle chiavi|
|/vaults/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per un insieme di credenziali delle chiavi|
|/vaults/read|Visualizza le proprietà di un insieme di credenziali delle chiavi|
|/vaults/secrets/read|Visualizza le proprietà di un segreto ma non il relativo valore|
|/vaults/secrets/write|Crea un nuovo segreto o aggiorna il valore di un segreto esistente|
|/vaults/write|Crea un nuovo insieme di credenziali delle chiavi o aggiorna le proprietà di un insieme di credenziali delle chiavi esistente|

## <a name="microsoftlabservices"></a>Microsoft.LabServices

| Operazione | DESCRIZIONE |
|---|---|
|/labAccounts/CreateLab/action|Crea un lab in un account del lab.|
|/labAccounts/delete|Elimina gli account del lab.|
|/labAccounts/labs/delete|Elimina lab.|
|/labAccounts/labs/environmentSettings/delete|Elimina un'impostazione di ambiente.|
|/labAccounts/labs/environmentSettings/environments/delete|Elimina ambienti.|
|/labAccounts/labs/environmentSettings/environments/read|Esegue la lettura di ambienti.|
|/labAccounts/labs/environmentSettings/environments/write|Aggiunge o modifica ambienti.|
|/labAccounts/labs/environmentSettings/Publish/action|Effettua il provisioning/deprovisioning delle risorse necessarie per un'impostazione di ambiente in base allo stato corrente dell'impostazione di lab/ambiente.|
|/labAccounts/labs/environmentSettings/read|Legge un'impostazione di ambiente.|
|/labAccounts/labs/environmentSettings/write|Aggiunge o modifica un'impostazione di ambiente.|
|/labAccounts/labs/read|Esegue la lettura di lab.|
|/labAccounts/labs/users/delete|Elimina gli utenti.|
|/labAccounts/labs/users/read|Legge utenti.|
|/labAccounts/labs/users/write|Aggiunge o modifica gli utenti.|
|/labAccounts/labs/write|Aggiunge o modifica lab.|
|/labAccounts/read|Legge gli account del lab.|
|/labAccounts/write|Aggiunge o modifica gli account del lab.|
|/locations/operations/read|Esegue la lettura delle operazioni.|
|/register/action|Registra la sottoscrizione.|

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

| Operazione | DESCRIZIONE |
|---|---|
|/accounts/delete|Elimina un account di Servizi Location Based.|
|/accounts/listKeys/action|Elenca le chiavi degli account di Servizi Location Based|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per gli account di Servizi Location Based|
|/accounts/read|Ottiene un account di Servizi Location Based.|
|/accounts/regenerateKey/action|Genera la chiave primaria o secondaria di un nuovo account di Servizi Location Based|
|/accounts/write|Crea o aggiorna un account di Servizi Location Based.|
|/register/action|Registra il provider|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Operazione | DESCRIZIONE |
|---|---|
|/integrationAccounts/agreements/delete|Elimina il contratto nell'account di integrazione.|
|/integrationAccounts/agreements/listContentCallbackUrl/action|Ottiene l'URL di callback per il contenuto del contratto nell'account di integrazione.|
|/integrationAccounts/agreements/read|Legge il contratto nell'account di integrazione.|
|/integrationAccounts/agreements/write|Crea o aggiorna il contratto nell'account di integrazione.|
|/integrationAccounts/assemblies/delete|Elimina l'assembly nell'account di integrazione.|
|/integrationAccounts/assemblies/listContentCallbackUrl/action|Ottiene l'URL di callback per il contenuto dell'assembly nell'account di integrazione.|
|/integrationAccounts/assemblies/read|Legge l'assembly nell'account di integrazione.|
|/integrationAccounts/assemblies/write|Crea o aggiorna l'assembly nell'account di integrazione.|
|/integrationAccounts/batchConfigurations/delete|Elimina la configurazione di batch nell'account di integrazione.|
|/integrationAccounts/batchConfigurations/read|Legge la configurazione di batch nell'account di integrazione.|
|/integrationAccounts/batchConfigurations/write|Crea o aggiorna la configurazione di batch nell'account di integrazione.|
|/integrationAccounts/certificates/delete|Elimina il certificato nell'account di integrazione.|
|/integrationAccounts/certificates/read|Legge il certificato nell'account di integrazione.|
|/integrationAccounts/certificates/write|Crea o aggiorna il certificato nell'account di integrazione.|
|/integrationAccounts/delete|Elimina l'account di integrazione.|
|/integrationAccounts/listCallbackUrl/action|Ottiene l'URL di callback per l'account di integrazione.|
|/integrationAccounts/listKeyVaultKeys/action|Ottiene le chiavi nell'insieme di credenziali delle chiavi.|
|/integrationAccounts/logTrackingEvents/action|Registra gli eventi di verifica nell'account di integrazione.|
|/integrationAccounts/maps/delete|Elimina la mappa nell'account di integrazione.|
|/integrationAccounts/maps/listContentCallbackUrl/action|Ottiene l'URL di callback per il contenuto della mappa nell'account di integrazione.|
|/integrationAccounts/maps/read|Legge la mappa nell'account di integrazione.|
|/integrationAccounts/maps/write|Crea o aggiorna la mappa nell'account di integrazione.|
|/integrationAccounts/partners/delete|Elimina il partner nell'account di integrazione.|
|/integrationAccounts/partners/listContentCallbackUrl/action|Ottiene l'URL di callback per il contenuto del partner nell'account di integrazione.|
|/integrationAccounts/partners/read|Legge il partner nell'account di integrazione.|
|/integrationAccounts/partners/write|Crea o aggiorna il partner nell'account di integrazione.|
|/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read|Legge le definizioni dei log dell'account di integrazione.|
|/integrationAccounts/read|Legge l'account di integrazione.|
|/integrationAccounts/regenerateAccessKey/action|Rigenera i segreti della chiave di accesso.|
|/integrationAccounts/schemas/delete|Elimina lo schema nell'account di integrazione.|
|/integrationAccounts/schemas/listContentCallbackUrl/action|Ottiene l'URL di callback per il contenuto dello schema nell'account di integrazione.|
|/integrationAccounts/schemas/read|Legge lo schema nell'account di integrazione.|
|/integrationAccounts/schemas/write|Crea o aggiorna lo schema nell'account di integrazione.|
|/integrationAccounts/sessions/delete|Elimina la sessione nell'account di integrazione.|
|/integrationAccounts/sessions/read|Legge la configurazione di batch nell'account di integrazione.|
|/integrationAccounts/sessions/write|Crea o aggiorna la sessione nell'account di integrazione.|
|/integrationAccounts/write|Crea o aggiorna l'account di integrazione.|
|/locations/workflows/validate/action|Convalida il flusso di lavoro.|
|/operations/read|Ottiene l'operazione.|
|/register/action|Registra il provider di risorse Microsoft.Logic per una sottoscrizione specifica.|
|/workflows/accessKeys/delete|Elimina la chiave di accesso.|
|/workflows/accessKeys/list/action|Elenca i segreti della chiave di accesso.|
|/workflows/accessKeys/read|Esegue la lettura della chiave di accesso.|
|/workflows/accessKeys/regenerate/action|Rigenera i segreti della chiave di accesso.|
|/workflows/accessKeys/write|Crea una chiave di accesso o ne aggiorna una esistente.|
|/workflows/delete|Elimina il flusso di lavoro.|
|/workflows/disable/action|Disabilita il flusso di lavoro.|
|/workflows/enable/action|Abilita il flusso di lavoro.|
|/workflows/listCallbackUrl/action|Ottiene l'URL di callback per il flusso di lavoro.|
|/workflows/listSwagger/action|Ottiene le definizioni di Swagger del flusso di lavoro.|
|/workflows/move/action|Sposta il flusso di lavoro dall'ID sottoscrizione, dal gruppo di risorse e/o dal nome esistente a un ID sottoscrizione, a un gruppo di risorse e/o a un nome diverso.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/read|Legge le impostazioni di diagnostica del flusso di lavoro.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica del flusso di lavoro.|
|/workflows/providers/Microsoft.Insights/logDefinitions/read|Legge le definizioni dei log del flusso di lavoro.|
|/workflows/providers/Microsoft.Insights/metricDefinitions/read|Legge definizioni della metrica del flusso di lavoro.|
|/workflows/read|Esegue la lettura del flusso di lavoro.|
|/workflows/regenerateAccessKey/action|Rigenera i segreti della chiave di accesso.|
|/workflows/run/action|Avvia l'esecuzione di un flusso di lavoro.|
|/workflows/runs/actions/listExpressionTraces/action|Ottiene le tracce dell'espressione dell'azione di esecuzione del flusso di lavoro.|
|/workflows/runs/actions/read|Esegue la lettura dell'azione di esecuzione del flusso di lavoro.|
|/workflows/runs/actions/repetitions/listExpressionTraces/action|Ottiene le tracce dell'espressione della ripetizione dell'azione di esecuzione del flusso di lavoro.|
|/workflows/runs/actions/repetitions/read|Legge la ripetizione dell'azione di esecuzione del flusso di lavoro.|
|/workflows/runs/actions/scoperepetitions/read|Legge la ripetizione dell'ambito dell'azione di esecuzione del flusso di lavoro.|
|/workflows/runs/cancel/action|Annulla l'esecuzione di un flusso di lavoro.|
|/workflows/runs/operations/read|Esegue la lettura dello stato dell'operazione di esecuzione del flusso di lavoro.|
|/workflows/runs/read|Esegue la lettura dell'esecuzione del flusso di lavoro.|
|/workflows/suspend/action|Sospende il flusso di lavoro.|
|/workflows/triggers/histories/read|Esegue la lettura delle cronologie del trigger.|
|/workflows/triggers/histories/resubmit/action|Invia di nuovo il trigger del flusso di lavoro.|
|/workflows/triggers/listCallbackUrl/action|Ottiene l'URL di callback per il trigger.|
|/workflows/triggers/read|Esegue la lettura del trigger.|
|/workflows/triggers/reset/action|Reimposta il trigger.|
|/workflows/triggers/run/action|Esegue il trigger.|
|/workflows/triggers/setState/action|Imposta lo stato del trigger.|
|/workflows/validate/action|Convalida il flusso di lavoro.|
|/workflows/versions/read|Esegue la lettura della versione del flusso di lavoro.|
|/workflows/versions/triggers/listCallbackUrl/action|Ottiene l'URL di callback per il trigger.|
|/workflows/write|Crea un flusso di lavoro o ne aggiorna uno esistente.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Operazione | DESCRIZIONE |
|---|---|
|/commitmentPlans/commitmentAssociations/move/action|Sposta qualsiasi associazione a un piano di impegno di Machine Learning|
|/commitmentPlans/commitmentAssociations/read|Esegue la lettura di qualsiasi associazione a un piano di impegno di Machine Learning|
|/commitmentPlans/delete|Elimina un qualsiasi piano di impegno di Machine Learning|
|/commitmentPlans/join/action|Consente di partecipare a qualsiasi piano di impegno di Machine Learning|
|/commitmentPlans/read|Esegue la lettura di qualsiasi piano di impegno di Machine Learning|
|/commitmentPlans/write|Crea o aggiorna un qualsiasi piano di impegno di Machine Learning|
|/locations/operationresults/read|Ottiene il risultato di un'operazione di Machine Learning|
|/locations/operationsstatus/read|Ottiene lo stato di un'operazione di Machine Learning in corso|
|/operations/read|Ottiene le operazioni di Machine Learning|
|/register/action|Registra la sottoscrizione per il provider di risorse del servizio Web di Machine Learning e consente la creazione di servizi Web.|
|/skus/read|Ottiene gli SKU dei piani di impegno di Machine Learning|
|/webServices/action|Creare proprietà del servizio Web regionali per le aree supportate|
|/webServices/delete|Elimina qualsiasi servizio Web di Machine Learning.|
|/webServices/read|Legge qualsiasi servizio Web di Machine Learning.|
|/webServices/write|Crea o aggiorna qualsiasi servizio Web di Machine Learning.|
|/Workspaces/delete|Elimina una qualsiasi area di lavoro di Machine Learning|
|/Workspaces/listworkspacekeys/action|Elenca le chiavi per un'area di lavoro di Machine Learning|
|/Workspaces/read|Esegue la lettura di una qualsiasi area di lavoro di Machine Learning|
|/Workspaces/resyncstoragekeys/action|Risincronizza le chiavi dell'account di archiviazione configurato per un'area di lavoro di Machine Learning|
|/Workspaces/write|Crea o aggiorna una qualsiasi area di lavoro di Machine Learning|

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

| Operazione | DESCRIZIONE |
|---|---|
|/operationalizationClusters/checkUpdate/action|Controlla se sono disponibili aggiornamenti per i servizi di sistema per il cluster di operazionalizzazione|
|/operationalizationClusters/delete|Elimina tutti gli account di hosting|
|/operationalizationClusters/listKeys/action|Elenca le chiavi associate con il cluster di operazionalizzazione|
|/operationalizationClusters/read|Legge tutti gli account di hosting|
|/operationalizationClusters/updateSystem/action|Aggiorna i servizi di sistema in un cluster di operazionalizzazione|
|/operationalizationClusters/write|Crea o aggiorna gli account di hosting|
|/register/action|Registra l'ID sottoscrizione per il provider di risorse e consente la creazione di risorse di calcolo di apprendimento automatico|

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

| Operazione | DESCRIZIONE |
|---|---|
|/accounts/delete|Elimina tutti gli account di hosting|
|/accounts/read|Legge tutti gli account di hosting|
|/accounts/write|Crea o aggiorna gli account di hosting|
|/register/action|Registra l'ID sottoscrizione per il provider di risorse e consente la creazione di un account di hosting|

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

| Operazione | DESCRIZIONE |
|---|---|
|/userAssignedIdentities/assign/action|Azione di controllo degli accessi in base al ruolo per l'assegnazione dell'identità assegnata di un utente esistente a una risorsa|
|/userAssignedIdentities/delete|Elimina l'identità assegnata a un utente esistente|
|/userAssignedIdentities/read|Ottiene l'identità assegnata a un utente esistente|
|/userAssignedIdentities/write|Crea una nuova identità assegnata a un utente esistente o aggiorna i tag associati a un'identità assegnata a un utente esistente|

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

| Operazione | DESCRIZIONE |
|---|---|
|/labAccounts/CreateLab/action|Crea un lab in un account del lab.|
|/labAccounts/delete|Elimina gli account del lab.|
|/labAccounts/labs/delete|Elimina lab.|
|/labAccounts/labs/environmentSettings/delete|Elimina un'impostazione di ambiente.|
|/labAccounts/labs/environmentSettings/environments/delete|Elimina ambienti.|
|/labAccounts/labs/environmentSettings/environments/read|Esegue la lettura di ambienti.|
|/labAccounts/labs/environmentSettings/environments/write|Aggiunge o modifica ambienti.|
|/labAccounts/labs/environmentSettings/read|Legge un'impostazione di ambiente.|
|/labAccounts/labs/environmentSettings/write|Aggiunge o modifica un'impostazione di ambiente.|
|/labAccounts/labs/labVms/delete|Elimina le macchine virtuali del lab.|
|/labAccounts/labs/labVms/read|Elimina macchine virtuali del lab.|
|/labAccounts/labs/labVms/write|Aggiunge o modifica le macchine virtuali del lab.|
|/labAccounts/labs/read|Esegue la lettura di lab.|
|/labAccounts/labs/write|Aggiunge o modifica lab.|
|/labAccounts/read|Legge gli account del lab.|
|/labAccounts/write|Aggiunge o modifica gli account del lab.|
|/locations/operations/read|Esegue la lettura delle operazioni.|
|/register/action|Registra la sottoscrizione.|

## <a name="microsoftmanagement"></a>Microsoft.Management

| Operazione | DESCRIZIONE |
|---|---|
|/checkNameAvailability/action|Controlla se il nome del gruppo di gestione specificato è valido e univoco.|
|/getEntities/action|Elenca tutte le entità, ovvero gruppi di gestione, sottoscrizioni e così via, per l'utente autenticato.|
|/managementGroups/delete|Elimina un gruppo di gestione.|
|/managementGroups/read|Elenca i gruppi di gestione per l'utente autenticato.|
|/managementGroups/subscriptions/delete|Annulla l'associazione della sottoscrizione dal gruppo di gestione.|
|/managementGroups/subscriptions/write|Associa una sottoscrizione esistente con il gruppo di gestione.|
|/managementGroups/write|Crea o aggiorna un gruppo di gestione.|

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

| Operazione | DESCRIZIONE |
|---|---|
|/ClassicDevServices/delete|Esegue un'operazione DELETE su una risorsa ClassicDevService.|
|/ClassicDevServices/listSecrets/action|Ottiene le chiavi di gestione di una risorsa ClassicDevService.|
|/ClassicDevServices/listSingleSignOnToken/action|Ottiene l'URL di Single Sign-On per un ClassicDevService.|
|/ClassicDevServices/read|Esegue un'operazione GET su un ClassicDevService.|
|/ClassicDevServices/regenerateKey/action|Genera le chiavi di gestione per una risorsa ClassicDevService.|
|/Operations/read|Legge le operazioni per tutti i tipi di risorsa.|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Operazione | DESCRIZIONE |
|---|---|
|/agreements/offers/plans/cancel/action|Elimina un contratto per uno specifico elemento del marketplace.|
|/agreements/offers/plans/read|Restituisce un contratto per uno specifico elemento del marketplace.|
|/agreements/offers/plans/sign/action|Firma un contratto per uno specifico elemento del marketplace.|
|/agreements/read|Restituisce tutti i contratti della sottoscrizione specificata|
|/offertypes/publishers/offers/plans/agreements/read|Restituisce un contratto per uno specifico elemento di macchina virtuale del marketplace|
|/offertypes/publishers/offers/plans/agreements/write|Firma o annulla un contratto per uno specifico elemento di macchina virtuale del marketplace|

## <a name="microsoftmedia"></a>Microsoft.Media

| Operazione | DESCRIZIONE |
|---|---|
|/checknameavailability/action|Controlla se un nome di account di Servizi multimediali è disponibile|
|/mediaservices/delete|Elimina tutti gli account di Servizi multimediali|
|/mediaservices/listKeys/action|Elenca le chiavi ACS per l'account di Servizi multimediali|
|/mediaservices/read|Legge tutti gli account di Servizi multimediali|
|/mediaservices/regenerateKey/action|Rigenera una chiave ACS di Servizi multimediali|
|/mediaservices/syncStorageKeys/action|Sincronizza le chiavi di archiviazione per un account di Archiviazione di Azure collegato|
|/mediaservices/write|Crea o aggiorna gli account di Servizi multimediali|
|/operations/read|Legge tutti gli account di Servizi multimediali|
|/register/action|Registra la sottoscrizione per il provider di risorse di Servizi multimediali e consente la creazione di account di Servizi multimediali|

## <a name="microsoftmigrate"></a>Microsoft.Migrate

| Operazione | DESCRIZIONE |
|---|---|
|/Operations/read|Legge le operazioni esposte|

## <a name="microsoftnetwork"></a>Microsoft.Network

| Operazione | DESCRIZIONE |
|---|---|
|/applicationGatewayAvailableSslOptions/predefinedPolicies/read|Criteri SSL predefiniti del gateway applicazione|
|/applicationGatewayAvailableSslOptions/read|Opzioni SSL disponibili per il gateway applicazione|
|/applicationGatewayAvailableWafRuleSets/read|Ottiene i set di regole WAF disponibili sul gateway applicazione|
|/applicationGateways/backendAddressPools/join/action|Aggiunge un pool di indirizzi back-end del gateway applicazione|
|/applicationGateways/backendhealth/action|Ottiene l'integrità back-end del gateway applicazione|
|/applicationGateways/delete|Elimina un gateway applicazione|
|/applicationGateways/effectiveNetworkSecurityGroups/action|Ottiene la tabella di route configurata nel gateway applicazione|
|/applicationGateways/effectiveRouteTable/action|Ottiene la tabella di route configurata nel gateway applicazione|
|/applicationGateways/providers/Microsoft.Insights/logDefinitions/read|Ottiene gli eventi per il gateway applicazione|
|/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per il gateway applicazione|
|/applicationGateways/read|Ottiene un gateway applicazione|
|/applicationGateways/setSecurityCenterConfiguration/action|Imposta la configurazione del Centro sicurezza del gateway applicazione|
|/applicationGateways/start/action|Avvia un gateway applicazione|
|/applicationGateways/stop/action|Arresta un gateway applicazione|
|/applicationGateways/write|Crea un gateway applicazione o ne aggiorna uno esistente|
|/applicationSecurityGroups/delete|Elimina un gruppo di sicurezza dell'applicazione|
|/applicationSecurityGroups/joinIpConfiguration/action|Aggiunge una configurazione IP a gruppi di sicurezza dell'applicazione.|
|/applicationSecurityGroups/joinNetworkSecurityRule/action|Aggiunge una regola di sicurezza a gruppi di sicurezza dell'applicazione.|
|/applicationSecurityGroups/read|Ottiene un ID gruppo di sicurezza dell'applicazione.|
|/applicationSecurityGroups/write|Crea un gruppo di sicurezza dell'applicazione o aggiorna un gruppo di sicurezza dell'applicazione esistente.|
|/bgpServiceCommunities/read|Ottiene le community del servizio BGP|
|/checkTrafficManagerNameAvailability/action|Controlla la disponibilità di un nome DNS relativo di gestione traffico.|
|/connections/delete|Elimina una connessione di gateway di rete virtuale|
|/connections/read|Ottiene una connessione di gateway di rete virtuale|
|/connections/sharedkey/action|Ottiene la chiave condivisa della connessione gateway di rete virtuale|
|/connections/sharedKey/read|Ottiene una connessione di gateway di rete virtuale SharedKey|
|/connections/sharedKey/write|Crea una connessione del gateway di rete virtuale SharedKey o ne aggiorna una esistente|
|/connections/vpndeviceconfigurationscript/read|Ottiene la configurazione del dispositivo VPN della connessione gateway di rete virtuale|
|/connections/write|Crea una connessione del gateway di rete virtuale o ne aggiorna una esistente|
|/ddosProtectionPlans/ddosProtectionPlanProxies/delete|Elimina un proxy di piano di Protezione DDoS|
|/ddosProtectionPlans/ddosProtectionPlanProxies/read|Ottiene una definizione di proxy di piano di Protezione DDoS|
|/ddosProtectionPlans/ddosProtectionPlanProxies/write|Crea un proxy di piano di Protezione DDoS o aggiorna un proxy di piano di Protezione DDoS esistente|
|/ddosProtectionPlans/delete|Elimina un piano di Protezione DDoS|
|/ddosProtectionPlans/join/action|Aggiunge un piano di Protezione DDoS|
|/ddosProtectionPlans/read|Ottiene un piano di Protezione DDoS|
|/ddosProtectionPlans/write|Crea un piano di Protezione DDoS o aggiorna un piano di Protezione DDoS esistente |
|/dnsoperationresults/read|Ottiene i risultati di un'operazione DNS|
|/dnsoperationstatuses/read|Ottiene lo stato di un’operazione DNS |
|/dnszones/A/delete|Rimuove il set di record di un determinato nome e tipo "A" da una zona DNS.|
|/dnszones/A/read|Ottiene il set di record di tipo "A" in formato JSON. Il set di record contiene un elenco di record, oltre a TTL, tag ed etag.|
|/dnszones/A/write|Crea o aggiorna un set di record di tipo "A" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record.|
|/dnszones/AAAA/delete|Rimuove il set di record di un determinato nome e tipo "AAAA" da una zona DNS.|
|/dnszones/AAAA/read|Ottiene il set di record di tipo "AAAA" in formato JSON. Il set di record contiene un elenco di record, nonché la durata (TTL), tag ed etag.|
|/dnszones/AAAA/write|Crea o aggiorna un set di record di tipo "AAAA" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record.|
|/dnszones/all/read|Ottiene i set di record DNS tra i tipi|
|/dnszones/CAA/delete|Rimuove il set di record di un determinato nome e tipo "CAA" da una zona DNS.|
|/dnszones/CAA/read|Ottiene il set di record di tipo "CAA" in formato JSON. Il set di record contiene durata (TTL), tag ed etag.|
|/dnszones/CAA/write|Crea o aggiorna un set di record di tipo "CAA" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record.|
|/dnszones/CNAME/delete|Rimuove il set di record di un determinato nome e tipo "CNAME" da una zona DNS.|
|/dnszones/CNAME/read|Ottiene il set di record di tipo "CNAME" in formato JSON. Il set di record contiene durata (TTL), tag ed etag.|
|/dnszones/CNAME/write|Crea o aggiorna un set di record di tipo "CNAME" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record.|
|/dnszones/delete|Elimina la zona DNS in formato Json. Le proprietà di zona includono tag, etag, numberOfRecordSets e maxNumberOfRecordSets.|
|/dnszones/MX/delete|Rimuove il set di record di un determinato nome e tipo "MX" da una zona DNS.|
|/dnszones/MX/read|Ottiene il set di record di tipo "MX" in formato JSON. Il set di record contiene un elenco di record, oltre a TTL, tag ed etag.|
|/dnszones/MX/write|Crea o aggiorna un set di record di tipo "MX" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record.|
|/dnszones/NS/delete|Elimina i set di record DNS di tipo NS.|
|/dnszones/NS/read|Ottiene i set di record DNS di tipo NS.|
|/dnszones/NS/write|Crea o aggiorna i set di record DNS di tipo NS.|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene le impostazioni di diagnostica della zona DNS|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna le impostazioni di diagnostica della zona DNS|
|/dnszones/providers/Microsoft.Insights/metricDefinitions/read|Ottiene le definizioni della metrica della zona DNS|
|/dnszones/PTR/delete|Rimuove il set di record di un determinato nome e tipo "PTR" da una zona DNS.|
|/dnszones/PTR/read|Ottiene il set di record di tipo "PTR" in formato JSON. Il set di record contiene un elenco di record, oltre a TTL, tag ed etag.|
|/dnszones/PTR/write|Crea o aggiorna un set di record di tipo "PTR" all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record.|
|/dnszones/read|Ottiene la zona DNS in formato Json. Le proprietà di zona includono tag, etag, numberOfRecordSets e maxNumberOfRecordSets. Si tenga presente che questo comando non recupera il set di record contenuti all'interno della zona.|
|/dnszones/recordsets/read|Ottiene i set di record DNS tra i tipi|
|/dnszones/SOA/read|Ottiene i set di record DNS di tipo SOA.|
|/dnszones/SOA/write|Crea o aggiorna i set di record DNS di tipo SOA.|
|/dnszones/SRV/delete|Rimuove il set di record di un determinato nome e tipo "SRV" da una zona DNS.|
|/dnszones/SRV/read|Ottiene il set di record di tipo "SRV" in formato JSON. Il set di record contiene un elenco di record, oltre a TTL, tag ed etag.|
|/dnszones/SRV/write|Crea o aggiorna il set di record di tipo SRV.|
|/dnszones/TXT/delete|Rimuove il set di record di un determinato nome e tipo ‘TXT’ da una zona DNS.|
|/dnszones/TXT/read|Ottiene il set di record di tipo ‘TXT’, in formato JSON. Il set di record contiene un elenco di record, oltre a TTL, tag ed etag.|
|/dnszones/TXT/write|Crea o aggiorna un set di record di tipo ‘TXT’ all'interno di una zona DNS. I record specificati andranno a sostituire quelli attualmente esistenti nel set di record.|
|/dnszones/write|Crea o aggiorna una zona DNS in un gruppo di risorse.  Consente di aggiornare i tag in una risorsa di zona DNS. Si tenga presente che questo comando non può essere utilizzato per creare o aggiornare i set di record all'interno della zona.|
|/expressRouteCircuits/authorizations/delete|Elimina un’autorizzazione di circuito Express Route|
|/expressRouteCircuits/authorizations/read|Ottiene un’autorizzazione di ExpressRouteCircuit|
|/expressRouteCircuits/authorizations/write|Crea un'autorizzazione di circuito Express Route o ne aggiorna una esistente|
|/expressRouteCircuits/delete|Elimina un ExpressRouteCircuit|
|/expressRouteCircuits/peerings/arpTables/action|Ottiene una ArpTable di peering di ExpressRouteCircuit|
|/expressRouteCircuits/peerings/connections/delete|Elimina una connessione circuito ExpressRoute|
|/expressRouteCircuits/peerings/connections/read|Ottiene una connessione circuito ExpressRoute|
|/expressRouteCircuits/peerings/connections/write|Crea o aggiorna una risorsa connessione circuito ExpressRoute esistente|
|/expressRouteCircuits/peerings/delete|Elimina il peering di ExpressRouteCircuit|
|/expressRouteCircuits/peerings/read|Ottiene il peering di ExpressRouteCircuit|
|/expressRouteCircuits/peerings/routeTables/action|Ottiene una RouteTable di peering di ExpressRouteCircuit|
|/expressRouteCircuits/peerings/routeTablesSummary/action|Ottiene il riepilogo di una RouteTable di peering di ExpressRouteCircuit|
|/expressRouteCircuits/peerings/stats/read|Ottiene lo Stat di peering di ExpressRouteCircuit|
|/expressRouteCircuits/peerings/write|Crea il peering di ExpressRouteCircuit o ne aggiorna uno esistente|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene le impostazioni di diagnostica per i circuiti ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna le impostazioni di diagnostica per i circuiti ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read|Ottiene gli eventi per i circuiti ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read|Ottiene le definizioni della metrica per i circuiti ExpressRoute|
|/expressRouteCircuits/read|Ottiene un ExpressRouteCircuit|
|/expressRouteCircuits/stats/read|Ottiene uno Stat di ExpressRouteCircuit|
|/expressRouteCircuits/write|Crea un ExpressRouteCircuit o ne aggiorna uno esistente|
|/expressRouteCrossConnections/delete|Elimina una Cross Connection ExpressRoute|
|/expressRouteCrossConnections/join/action|Aggiunge una Cross Connection ExpressRoute|
|/expressRouteCrossConnections/peerings/arpTables/action|Ottiene una tabella ARP di peering delle Cross Connection ExpressRoute|
|/expressRouteCrossConnections/peerings/delete|Elimina un peering di Cross Connection ExpressRoute|
|/expressRouteCrossConnections/peerings/read|Ottiene un peering di Cross Connection ExpressRoute|
|/expressRouteCrossConnections/peerings/routeTables/action|Ottiene una tabella di route delle Cross Connection ExpressRoute|
|/expressRouteCrossConnections/peerings/routeTableSummary/action|Ottiene un riepilogo della tabella di routing delle Cross Connection ExpressRoute|
|/expressRouteCrossConnections/peerings/stats/read|Ottiene statistiche di peering delle Cross Connection ExpressRoute|
|/expressRouteCrossConnections/peerings/write|Crea un peering di Cross Connection ExpressRoute o aggiorna un peering di Cross Connection ExpressRoute esistente|
|/expressRouteCrossConnections/read|Ottiene una Cross Connection ExpressRoute|
|/expressRouteCrossConnections/write|Crea o aggiorna una Cross Connection ExpressRoute|
|/expressRouteServiceProviders/read|Ottiene i provider del servizio Express Route|
|/loadBalancers/backendAddressPools/join/action|Aggiunge un pool di indirizzi di back-end del servizio di bilanciamento del carico|
|/loadBalancers/backendAddressPools/read|Ottiene una definizione del pool di indirizzi di back-end del servizio di bilanciamento del carico|
|/loadBalancers/delete|Elimina un servizio di bilanciamento del carico|
|/loadBalancers/frontendIPConfigurations/read|Ottiene una definizione della configurazione IP del front-end del servizio di bilanciamento del carico|
|/loadBalancers/inboundNatPools/join/action|Aggiunge un pool NAT in entrata del servizio di bilanciamento del carico|
|/loadBalancers/inboundNatPools/read|Ottiene una definizione del pool NAT in entrata del servizio di bilanciamento del carico|
|/loadBalancers/inboundNatRules/delete|Elimina una regola NAT in entrata del servizio di bilanciamento del carico|
|/loadBalancers/inboundNatRules/join/action|Aggiunge una regola NAT in entrata del servizio di bilanciamento del carico|
|/loadBalancers/inboundNatRules/read|Ottiene una definizione di regola NAT in entrata del servizio di bilanciamento del carico|
|/loadBalancers/inboundNatRules/write|Crea una regola NAT in entrata del servizio di bilanciamento del carico o aggiorna una regola NAT in entrata di bilanciamento del carico esistente|
|/loadBalancers/loadBalancingRules/read|Ottiene una definizione di regola di bilanciamento del carico per il servizio di bilanciamento del carico|
|/loadBalancers/networkInterfaces/read|Ottiene i riferimenti a tutte le interfacce di rete sotto a un servizio di bilanciamento del carico|
|/loadBalancers/outboundNatRules/read|Ottiene una definizione della regola NAT in uscita del servizio di bilanciamento del carico|
|/loadBalancers/probes/join/action|Consente l'uso di probe di un servizio di bilanciamento del carico. Con questa autorizzazione, ad esempio, la proprietà healthProbe di un set di scalabilità di macchine virtuali può fare riferimento al probe.|
|/loadBalancers/probes/read|Ottiene un probe del servizio di bilanciamento del carico|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene le impostazioni di diagnostica del servizio di bilanciamento del carico|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna le impostazioni di diagnostica del servizio di bilanciamento del carico|
|/loadBalancers/providers/Microsoft.Insights/logDefinitions/read|Ottiene gli eventi per il servizio di bilanciamento del carico|
|/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per il servizio di bilanciamento del carico|
|/loadBalancers/read|Ottiene una definizione del servizio di bilanciamento del carico|
|/loadBalancers/virtualMachines/read|Ottiene i riferimenti a tutte le macchine virtuali sotto a un servizio di bilanciamento del carico|
|/loadBalancers/write|Crea un servizio di bilanciamento del carico o ne aggiorna uno esistente|
|/localnetworkgateways/delete|Elimina il LocalNetworkGateway|
|/localnetworkgateways/read|Ottiene il LocalNetworkGateway|
|/localnetworkgateways/write|Crea un LocalNetworkGateway o ne aggiorna uno esistente|
|/locations/checkDnsNameAvailability/read|Controlla se l'etichetta DNS è disponibile nel percorso specificato|
|/locations/operationResults/read|Ottiene il risultato di un'operazione POST o DELETE asincrona|
|/locations/operations/read|Ottiene una risorsa per l'operazione che rappresenta lo stato di un'operazione asincrona|
|/locations/usages/read|Ottiene la metrica di utilizzo delle risorse|
|/locations/virtualNetworkAvailableEndpointServices/read|Ottiene un elenco dei servizi di endpoint di rete virtuale disponibili|
|/networkInterfaces/delete|Elimina un'interfaccia di rete|
|/networkInterfaces/diagnosticIdentity/read|Ottiene l'identità di diagnostica della risorsa|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|Ottiene i gruppi di sicurezza di rete configurati sull’interfaccia di rete della macchina virtuale|
|/networkInterfaces/effectiveRouteTable/action|Ottiene la tabella di instradamento configurata sull’interfaccia di rete della macchina virtuale|
|/networkInterfaces/ipconfigurations/read|Ottiene la definizione della configurazione IP dell’interfaccia di rete. |
|/networkInterfaces/join/action|Aggiunge una macchina virtuale a un'interfaccia di rete|
|/networkInterfaces/loadBalancers/read|Ottiene tutti i servizi di bilanciamento del carico ai quali appartiene l’interfaccia di rete|
|/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per l'interfaccia di rete|
|/networkInterfaces/read|Ottiene una definizione dell’interfaccia di rete. |
|/networkInterfaces/write|Crea un'interfaccia di rete o ne aggiorna una esistente. |
|/networkSecurityGroups/defaultSecurityRules/read|Ottiene una definizione delle regole di sicurezza predefinita|
|/networkSecurityGroups/delete|Elimina un gruppo di sicurezza di rete|
|/networkSecurityGroups/join/action|Aggiunge un gruppo di sicurezza di rete|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene le impostazioni di diagnostica dei gruppi di sicurezza di rete|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna le impostazioni di diagnostica dei gruppi di sicurezza di rete, questa operazione viene eseguita dal provider di risorse Insights.|
|/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read|Ottiene gli eventi per un gruppo di sicurezza di rete|
|/networkSecurityGroups/read|Ottiene una definizione del gruppo di sicurezza di rete|
|/networkSecurityGroups/securityRules/delete|Elimina una regola di sicurezza|
|/networkSecurityGroups/securityRules/read|Ottiene una definizione delle regole di sicurezza|
|/networkSecurityGroups/securityRules/write|Crea una regola di sicurezza o ne aggiorna una esistente|
|/networkSecurityGroups/write|Crea un gruppo di sicurezza di rete o ne aggiorna uno esistente|
|/networkWatchers/availableProvidersList/action|Restituisce tutti i provider di servizi Internet disponibili per un'area di Azure specificata.|
|/networkWatchers/azureReachabilityReport/action|Restituisce il punteggio di latenza relativa per i provider di servizi Internet da una località specificata alle aree di Azure.|
|/networkWatchers/configureFlowLog/action|Configura la registrazione del flusso per una risorsa di destinazione.|
|/networkWatchers/connectionMonitors/delete|Elimina un monitoraggio connessione|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/read|Ottiene le impostazioni di diagnostica del monitoraggio connessione|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/write|Crea o aggiorna le impostazioni di diagnostica del monitoraggio connessione|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ metricDefinitions/read|Ottiene la metrica disponibile per il monitoraggio connessione|
|/networkWatchers/connectionMonitors/query/action|Esegue query sul monitoraggio della connettività tra gli endpoint specificati|
|/networkWatchers/connectionMonitors/read|Ottiene dettagli di monitoraggio connessione|
|/networkWatchers/connectionMonitors/start/action|Avvia il monitoraggio della connettività tra gli endpoint specificati|
|/networkWatchers/connectionMonitors/stop/action|Arresta/mette in pausa il monitoraggio della connettività tra gli endpoint specificati|
|/networkWatchers/connectionMonitors/write|Crea un monitoraggio connessione|
|/networkWatchers/connectivityCheck/action|Verifica se è possibile stabilire una connessione TCP diretta da una macchina virtuale a un determinato endpoint inclusa un'altra VM o un server remoto arbitrario.|
|/networkWatchers/delete|Elimina un Network Watcher|
|/networkWatchers/ipFlowVerify/action|Indica se il pacchetto è autorizzato o meno su o da una specifica destinazione.|
|/networkWatchers/lenses/delete|Elimina una sezione|
|/networkWatchers/lenses/query/action|Esegue query sul monitoraggio del traffico di rete in un endpoint specificato|
|/networkWatchers/lenses/read|Ottiene i dettagli della sezione|
|/networkWatchers/lenses/start/action|Avvia il monitoraggio del traffico di rete in un endpoint specificato|
|/networkWatchers/lenses/stop/action|Arresta/mette in pausa il monitoraggio del traffico di rete in un endpoint specificato|
|/networkWatchers/lenses/write|Crea una sezione|
|/networkWatchers/nextHop/action|Per un indirizzo IP destinazione specificato, restituisce il tipo di hop successivo e l’indirizzo IP dell’hop successivo.|
|/networkWatchers/packetCaptures/delete|Elimina un'acquisizione di pacchetti|
|/networkWatchers/packetCaptures/queryStatus/action|Ottiene informazioni sulle proprietà e sullo stato di una risorsa di acquisizione dei pacchetti.|
|/networkWatchers/packetCaptures/read|Ottiene la definizione di acquisizione dei pacchetti|
|/networkWatchers/packetCaptures/stop/action|Arresta la sessione di acquisizione dei pacchetti in corso.|
|/networkWatchers/packetCaptures/write|Crea un'acquisizione di pacchetti|
|/networkWatchers/queryFlowLogStatus/action|Ottiene lo stato della registrazione del flusso su una risorsa.|
|/networkWatchers/queryTroubleshootResult/action|Ottiene il risultato della risoluzione dei problemi dall’esecuzione precedente o dall’operazione di risoluzione dei problemi attualmente in corso.|
|/networkWatchers/read|Ottiene la definizione del Network Watcher|
|/networkWatchers/securityGroupView/action|Visualizza le regole del gruppo di sicurezza di rete configurate e in vigore applicate a una macchina virtuale.|
|/networkWatchers/topology/action|Ottiene una vista a livello di rete delle risorse e le relative relazioni in un gruppo di risorse.|
|/networkWatchers/troubleshoot/action|Avvia la risoluzione dei problemi su una risorsa di rete in Azure.|
|/networkWatchers/write|Crea un Network Watcher o ne aggiorna uno esistente|
|/operations/read|Ottiene le operazioni disponibili|
|/publicIPAddresses/delete|Elimina un indirizzo IP pubblico.|
|/publicIPAddresses/join/action|Aggiunge un indirizzo IP pubblico|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene le impostazioni di diagnostica dell'indirizzo IP pubblico|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna le impostazioni di diagnostica dell'indirizzo IP pubblico|
|/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read|Ottiene le definizioni di log di un indirizzo IP pubblico|
|/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read|Ottiene le definizioni di metrica di un indirizzo IP pubblico|
|/publicIPAddresses/read|Ottiene una definizione dell’indirizzo IP pubblico.|
|/publicIPAddresses/write|Crea un indirizzo IP pubblico o ne aggiorna uno esistente. |
|/register/action|Registra la sottoscrizione.|
|/routeFilters/delete|Elimina una definizione del filtro di instradamento|
|/routeFilters/join/action|Aggiunge un filtro di instradamento|
|/routeFilters/read|Ottiene una definizione del filtro di instradamento|
|/routeFilters/routeFilterRules/delete|Elimina una definizione della regola del filtro di instradamento|
|/routeFilters/routeFilterRules/read|Ottiene una definizione della regola del filtro di instradamento|
|/routeFilters/routeFilterRules/write|Crea una regola del filtro di instradamento o ne aggiorna una esistente|
|/routeFilters/write|Crea un filtro di instradamento o ne aggiorna uno esistente|
|/routeTables/delete|Elimina una definizione della tabella di route|
|/routeTables/join/action|Aggiunge una tabella di route|
|/routeTables/read|Ottiene una definizione della tabella di route|
|/routeTables/routes/delete|Elimina una definizione di route|
|/routeTables/routes/read|Ottiene una definizione di route|
|/routeTables/routes/write|Crea una route o ne aggiorna una esistente|
|/routeTables/write|Crea una tabella di route o ne aggiorna una esistente|
|/securegateways/applicationRuleCollections/delete|Elimina una raccolta di regole di applicazione per un gateway sicuro|
|/securegateways/applicationRuleCollections/read|Recupera una raccolta di regole di applicazione per un determianto gateway sicuro|
|/securegateways/applicationRuleCollections/write|Crea o aggiorna una raccolta di regole di applicazione per un gateway sicuro|
|/securegateways/delete|Elimina un gateway sicuro|
|/securegateways/networkRuleCollections/delete|Elimina una raccolta di regole di rete per un gateway sicuro|
|/securegateways/networkRuleCollections/read|Recupera una raccolta di regole di rete per un determinato gateway sicuro|
|/securegateways/networkRuleCollections/write|Crea o aggiorna una raccolta di regole di rete per un gateway sicuro|
|/securegateways/read|Ottiene un gateway sicuro|
|/securegateways/write|Crea o aggiorna un gateway sicuro|
|/serviceEndpointPolicies/delete|Elimina un criterio di endpoint di servizio|
|/serviceEndpointPolicies/join/action|Aggiunge un criterio di endpoint di servizio|
|/serviceEndpointPolicies/joinSubnet/action|Aggiunge una subnet a criteri di endpoint di servizio|
|/serviceEndpointPolicies/read|Ottiene una descrizione dei criteri di endpoint di servizio|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete|Elimina una definizione di criteri di endpoint di servizio|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read|Ottiene una descrizione della definizione dei criteri di endpoint di servizio|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write|Crea una definizione di criteri di endpoint di servizio o aggiorna una definizione di criteri di endpoint di servizio esistente|
|/serviceEndpointPolicies/write|Crea criteri di endpoint di servizio o aggiorna criteri di endpoint di servizio esistenti|
|/trafficManagerGeographicHierarchies/read|Ottiene la gerarchia geografica di Gestione traffico contenente le aree che possono essere utilizzate con il metodo di routing del traffico geografico|
|/trafficManagerProfiles/azureEndpoints/delete|Elimina un Endpoint di Azure da un profilo di Gestione traffico esistente. Gestione traffico smetterà di eseguire il routing del traffico all'Endpoint di Azure eliminato.|
|/trafficManagerProfiles/azureEndpoints/read|Ottiene un Endpoint di Azure che appartiene a un profilo di Gestione traffico, incluse tutte le proprietà di tale Endpoint di Azure.|
|/trafficManagerProfiles/azureEndpoints/write|Aggiunge un nuovo Endpoint di Azure in un profilo di Gestione traffico esistente o aggiorna le proprietà di un Endpoint di Azure esistente in quel profilo di Gestione traffico.|
|/trafficManagerProfiles/delete|Elimina il profilo di Gestione traffico. Tutte le impostazioni associate al profilo di Gestione traffico andranno perse e il profilo non può più essere utilizzato per instradare il traffico.|
|/trafficManagerProfiles/externalEndpoints/delete|Elimina un endpoint esterno da un profilo di Gestione traffico esistente. Gestione traffico smetterà di eseguire il routing del traffico all'endpoint esterno eliminato.|
|/trafficManagerProfiles/externalEndpoints/read|Ottiene un endpoint esterno che appartiene a un profilo di Gestione traffico, incluse tutte le proprietà di tale endpoint esterno.|
|/trafficManagerProfiles/externalEndpoints/write|Aggiunge un nuovo endpoint esterno in un profilo di Gestione traffico esistente o aggiorna le proprietà di un endpoint esterno esistente in quel profilo di Gestione traffico.|
|/trafficManagerProfiles/heatMaps/read|Ottiene la mappa termica di Gestione traffico per il profilo di Gestione traffico specificato che contiene conteggi di query e dati di latenza per percorso e IP di origine.|
|/trafficManagerProfiles/nestedEndpoints/delete|Elimina un endpoint annidato da un profilo di Gestione traffico esistente. Gestione traffico smetterà di eseguire il routing del traffico all'endpoint annidato eliminato.|
|/trafficManagerProfiles/nestedEndpoints/read|Ottiene un endpoint annidato che appartiene a un profilo di Gestione traffico, incluse tutte le proprietà di tale endpoint annidato.|
|/trafficManagerProfiles/nestedEndpoints/write|Aggiunge un nuovo endpoint annidato in un profilo di Gestione traffico esistente o aggiorna le proprietà di un endpoint annidato esistente in quel profilo di Gestione traffico.|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene le impostazioni di diagnostica di Gestione traffico|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna le impostazioni di diagnostica di Gestione traffico, questa operazione viene eseguita dal provider di risorse Insights.|
|/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read|Ottiene gli eventi per Gestione traffico|
|/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per Gestione traffico.|
|/trafficManagerProfiles/read|Ottiene la configurazione del profilo di Gestione traffico. Include le impostazioni DNS, di routing del traffico, di monitoraggio dell'endpoint e un elenco degli endpoint instradati da questo profilo di Gestione traffico.|
|/trafficManagerProfiles/write|Crea un profilo di Gestione traffico o modifica la configurazione di un profilo di Gestione traffico esistente. Questo include l’abilitazione o la disabilitazione di un profilo e la modifica delle impostazioni di DNS, di instradamento del traffico o di monitoraggio dell’endpoint. Gli endpoint instradati dal profilo di Gestione traffico possono essere aggiunti, rimossi, abilitati o disabilitati.|
|/trafficManagerUserMetricsKeys/delete|Elimina la chiave a livello di sottoscrizione utilizzata per la raccolta della metrica utente in tempo reale.|
|/trafficManagerUserMetricsKeys/read|Ottiene la chiave a livello di sottoscrizione utilizzata per la raccolta della metrica utente in tempo reale.|
|/trafficManagerUserMetricsKeys/write|Crea una nuova chiave a livello di sottoscrizione da usare per la raccolta della metrica utente in tempo reale.|
|/unregister/action|Annulla la registrazione della sottoscrizione.|
|/virtualHubs/delete|Elimina un hub virtuale|
|/virtualHubs/hubVirtualNetworkConnections/delete|Elimina una connessione di rete virtuale dell'hub|
|/virtualHubs/hubVirtualNetworkConnections/read|Ottiene una connessione di rete virtuale dell'hub|
|/virtualHubs/hubVirtualNetworkConnections/write|Crea o aggiorna una connessione di rete virtuale dell'hub|
|/virtualHubs/read|Ottiene un hub virtuale|
|/virtualHubs/write|Crea o aggiorna un hub virtuale|
|/virtualnetworkgateways/connections/read|Ottiene una connessione di gateway di rete virtuale|
|/virtualNetworkGateways/delete|Elimina un gateway di rete virtuale|
|/virtualnetworkgateways/generatevpnclientpackage/action|Genera il pacchetto VpnClient per il gateway di rete virtuale|
|/virtualnetworkgateways/generatevpnprofile/action|Genera il pacchetto VpnProfile per il gateway di rete virtuale|
|/virtualnetworkgateways/getadvertisedroutes/action|Ottiene le route pubblicizzate per il gateway di rete virtuale|
|/virtualnetworkgateways/getbgppeerstatus/action|Ottiene lo stato peer BGP pubblicizzato per il gateway di rete virtuale|
|/virtualnetworkgateways/getlearnedroutes/action|Ottiene le route apprese dal gateway di rete virtuale|
|/virtualnetworkgateways/getvpnclientipsecparameters/action|Ottiene i parametri IPsec client VPN per il client P2S del gateway di rete virtuale.|
|/virtualnetworkgateways/getvpnprofilepackageurl/action|Ottiene l'URL di un pacchetto di profilo client VPN generato in precedenza|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene le impostazioni di diagnostica del gateway di rete virtuale|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna le impostazioni di diagnostica del gateway di rete virtuale, questa operazione viene eseguita dal provider di risorse Insights.|
|/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read|Ottiene gli eventi per il gateway di rete virtuale|
|/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per il gateway di rete virtuale|
|/virtualNetworkGateways/read|Ottiene un gateway di rete virtuale|
|/virtualnetworkgateways/reset/action|Reimposta un gateway di rete virtuale|
|/virtualnetworkgateways/setvpnclientipsecparameters/action|Imposta i parametri IPsec client VPN per il client P2S del gateway di rete virtuale.|
|/virtualnetworkgateways/supportedvpndevices/action|Elenca i dispositivi VPN supportati|
|/virtualNetworkGateways/write|Crea o aggiorna un gateway di rete virtuale|
|/virtualNetworks/checkIpAddressAvailability/read|Verifica se l’indirizzo IP è disponibile presso la rete virtuale specificata|
|/virtualNetworks/customViews/get/action|Ottiene il contenuto di una visualizzazione personalizzata della rete virtuale|
|/virtualNetworks/customViews/read|Ottiene la definizione di una visualizzazione personalizzata della rete virtuale|
|/virtualNetworks/delete|Elimina una rete virtuale|
|/virtualNetworks/peer/action|Associa una rete virtuale a un'altra rete virtuale|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene le impostazioni di diagnostica della rete virtuale|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna le impostazioni di diagnostica della rete virtuale|
|/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read|Ottiene le definizioni di log della rete virtuale|
|/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read|Ottiene le definizioni di metrica della rete virtuale|
|/virtualNetworks/read|Ottiene la definizione della rete virtuale|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete|Elimina un proxy di peering della rete virtuale|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/read|Ottiene una definizione di un proxy di peering della rete virtuale|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/write|Crea un proxy di peering della rete virtuale o ne aggiorna uno esistente|
|/virtualNetworks/subnets/delete|Elimina una subnet della rete virtuale|
|/virtualNetworks/subnets/join/action|Aggiunge una rete virtuale|
|/virtualNetworks/subnets/joinViaServiceEndpoint/action|Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet.|
|/virtualNetworks/subnets/read|Ottiene una definizione di subnet della rete virtuale|
|/virtualNetworks/subnets/resourceNavigationLinks/delete|Elimina un collegamento di navigazione a una risorsa|
|/virtualNetworks/subnets/resourceNavigationLinks/read|Ottiene la definizione del collegamento di navigazione a una risorse|
|/virtualNetworks/subnets/resourceNavigationLinks/write|Crea un collegamento di navigazione a una risorsa o aggiorna un collegamento di navigazione a una risorsa esistente|
|/virtualNetworks/subnets/virtualMachines/read|Ottiene i riferimenti a tutte le macchine virtuali nella subnet della rete virtuale|
|/virtualNetworks/subnets/write|Crea una subnet della rete virtuale o ne aggiorna una esistente|
|/virtualNetworks/taggedTrafficConsumers/delete|Elimina un consumer di traffico contrassegnato|
|/virtualNetworks/taggedTrafficConsumers/read|Ottiene la definizione di un consumer di traffico contrassegnato|
|/virtualNetworks/taggedTrafficConsumers/validate/action|Convalida un consumer di traffico contrassegnato|
|/virtualNetworks/taggedTrafficConsumers/write|Crea un consumer di traffico contrassegnato o aggiorna un consumer di traffico contrassegnato esistente|
|/virtualNetworks/usages/read|Ottiene gli utilizzi IP per ogni subnet della rete virtuale|
|/virtualNetworks/virtualMachines/read|Ottiene i riferimenti a tutte le macchine virtuali in una rete virtuale|
|/virtualNetworks/virtualNetworkPeerings/delete|Elimina un peering della rete virtuale|
|/virtualNetworks/virtualNetworkPeerings/read|Ottiene una definizione di peering della rete virtuale|
|/virtualNetworks/virtualNetworkPeerings/write|Crea un peering della rete virtuale o ne aggiorna uno esistente|
|/virtualNetworks/write|Crea una rete virtuale o ne aggiorna una esistente|
|/virtualNetworkTaps/delete|Elimina un TAP di rete virtuale|
|/virtualNetworkTaps/join/action|Aggiunge un TAP di rete virtuale|
|/virtualNetworkTaps/read|Ottiene un TAP di rete virtuale|
|/virtualNetworkTaps/write|Crea o aggiorna un TAP di rete virtuale|
|/virtualwans/delete|Elimina una rete WAN virtuale|
|/virtualwans/read|Ottiene una rete WAN virtuale|
|/virtualWans/virtualHubProxies/delete|Elimina un proxy di hub virtuale|
|/virtualWans/virtualHubProxies/read|Ottiene una definizione di proxy di hub virtuale|
|/virtualWans/virtualHubProxies/write|Crea un proxy di hub virtuale o aggiorna un proxy di hub virtuale esistente|
|/virtualwans/virtualHubs/read|Ottiene tutti gli hub virtuali associati a una rete WAN virtuale.|
|/virtualwans/vpnconfiguration/read|Ottiene una configurazione VPN|
|/virtualWans/vpnSiteProxies/delete|Elimina un proxy di sito VPN|
|/virtualWans/vpnSiteProxies/read|Ottiene la definizione di un proxy di sito VPN|
|/virtualWans/vpnSiteProxies/write|Crea un proxy di sito VPN o aggiorna un proxy di sito VPN esistente|
|/virtualwans/vpnSites/read|Ottiene tutti i siti VPN associati a una rete WAN virtuale.|
|/virtualwans/write|Crea o aggiorna una rete WAN virtuale|
|/vpnGateways/read|Ottiene un gateway VPN.|
|/vpnGateways/vpnConnections/read|Ottiene una connessione VPN.|
|/vpnGateways/vpnConnections/write|Inserisce una connessione VPN.|
|/vpnGateways/write|Inserisce un gateway VPN.|
|/vpnsites/delete|Elimina una risorsa di sito VPN.|
|/vpnsites/read|Ottiene una risorsa di sito VPN.|
|/vpnsites/write|Crea o aggiorna una risorsa di sito VPN.|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Operazione | DESCRIZIONE |
|---|---|
|/CheckNamespaceAvailability/action|Verifica se il nome di risorsa di uno spazio dei nomi specificato è disponibile o meno all'interno del servizio Hub di notifica.|
|/Namespaces/authorizationRules/action|Ottiene l'elenco di descrizioni delle regole di autorizzazione degli spazi dei nomi.|
|/Namespaces/authorizationRules/delete|Elimina regola di autorizzazione dello spazio dei nomi. Non è possibile eliminare la regola di autorizzazione dello spazio dei nomi predefinita. |
|/Namespaces/authorizationRules/listkeys/action|Ottiene la stringa di connessione per lo spazio dei nomi|
|/Namespaces/authorizationRules/read|Ottiene l'elenco di descrizioni delle regole di autorizzazione degli spazi dei nomi.|
|/Namespaces/authorizationRules/regenerateKeys/action|Regola di autorizzazione dello spazio dei nomi - Rigenera chiave primaria/secondaria. Specificare la chiave da rigenerare|
|/Namespaces/authorizationRules/write|Crea regole di autorizzazione a livello dello spazio dei nomi e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/Namespaces/CheckNotificationHubAvailability/action|Controlla se un determinato nome di hub di notifica è disponibile all'interno di uno spazio dei nomi.|
|/Namespaces/Delete|Elimina una risorsa spazio dei nomi|
|/Namespaces/NotificationHubs/authorizationRules/action|Ottiene l'elenco di regole di autorizzazione dell’hub di notifica|
|/Namespaces/NotificationHubs/authorizationRules/delete|Elimina le regole di autorizzazione dell’hub di notifica|
|/Namespaces/NotificationHubs/authorizationRules/listkeys/action|Ottiene la stringa di connessione all'hub di notifica|
|/Namespaces/NotificationHubs/authorizationRules/read|Ottiene l'elenco di regole di autorizzazione dell’hub di notifica|
|/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action|La regola di autorizzazione dell’hub di notifica rigenera la chiave primaria/secondaria. Specificare la chiave da rigenerare|
|/Namespaces/NotificationHubs/authorizationRules/write|Crea regole di autorizzazione dell'inoltro dell’hub di notifica e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/Namespaces/NotificationHubs/debugSend/action|Invia una notifica push di prova.|
|/Namespaces/NotificationHubs/Delete|Elimina la risorsa nell’hub di notifica|
|/Namespaces/NotificationHubs/metricDefinitions/read|Ottiene l'elenco di descrizioni delle risorse di metrica dello spazio dei nomi|
|/Namespaces/NotificationHubs/pnsCredentials/action|Ottiene tutte le credenziali PNS dell’hub di notifica. Queste includono le credenziali WNS, MPNS, APNS, GCM e Baidu|
|/Namespaces/NotificationHubs/read|Ottiene l’elenco di descrizioni dell’hub di notifica|
|/Namespaces/NotificationHubs/write|Crea un hub di notifica e ne aggiorna le proprietà. Le proprietà includono principalmente credenziali PNS, regole di autorizzazione e TTL|
|/Namespaces/read|Ottiene l'elenco delle descrizioni delle risorse spazio dei nomi|
|/Namespaces/write|Crea una risorsa spazio dei nomi e ne aggiorna le proprietà. Le proprietà che si possono aggiornare sono quelle relative ai tag e alla capacità dello spazio dei nomi.|
|/register/action|Registra la sottoscrizione per il provider di risorse hub di notifica e abilita la creazione di spazi dei nomi e hub di notifica|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Operazione | DESCRIZIONE |
|---|---|
|/linkTargets/read|Elenca gli account esistenti non associati a una sottoscrizione di Azure. Per collegare questa sottoscrizione di Azure a un’area di lavoro, utilizzare un ID cliente restituito da questa operazione nella proprietà ID cliente dell’operazione Crea area di lavoro.|
|/register/action|Registra una sottoscrizione a un provider di risorse.|
|/workspaces/analytics/query/action|Esegue la ricerca usando il nuovo motore.|
|/workspaces/analytics/query/schema/read|Ottiene lo schema di ricerca V2.|
|/workspaces/api/query/action|Esegue la ricerca usando il nuovo motore.|
|/workspaces/api/query/schema/read|Ottiene lo schema di ricerca V2.|
|/workspaces/configurationScopes/delete|Elimina l'ambito di configurazione|
|/workspaces/configurationScopes/read|Ottiene l'ambito di configurazione|
|/workspaces/configurationScopes/write|Imposta l'ambito di configurazione|
|/workspaces/datasources/delete|Elimina le origini dati in un'area di lavoro.|
|/workspaces/datasources/read|Ottiene le origini dati in un'area di lavoro.|
|/workspaces/datasources/write|Crea/Aggiorna le origini dati in un'area di lavoro.|
|/workspaces/delete|Elimina un'area di lavoro. Se l'area di lavoro era collegata a un’area di lavoro esistente al momento della creazione, l’area di lavoro alla quale era collegata non viene eliminata.|
|/workspaces/generateregistrationcertificate/action|Genera il certificato di registrazione per l’area di lavoro. Il certificato viene utilizzato per la connessione di Microsoft System Center Operations Manager all'area di lavoro.|
|/workspaces/intelligencepacks/disable/action|Disabilita un Intelligence Pack per una specifica area di lavoro.|
|/workspaces/intelligencepacks/enable/action|Abilita un Intelligence Pack per una specifica area di lavoro.|
|/workspaces/intelligencepacks/read|Elenca tutti gli Intelligence Pack visibili per una specifica area di lavoro, oltre a elencare se il pacchetto è abilitato o disabilitato per quell’area di lavoro.|
|/workspaces/linkedServices/delete|Elimina i servizi collegati nell'area di lavoro specificata.|
|/workspaces/linkedServices/read|Ottiene i servizi collegati nell'area di lavoro specificata.|
|/workspaces/linkedServices/write|Crea o aggiorna i servizi collegati nell'area di lavoro specificata.|
|/workspaces/listKeys/action|Recupera le chiavi di elenco per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro.|
|/workspaces/listKeys/read|Recupera le chiavi di elenco per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro.|
|/workspaces/managementGroups/read|Ottiene nomi e metadati per i gruppi di gestione di System Center Operations Manager connessi a questa area di lavoro.|
|/workspaces/metricDefinitions/read|Ottiene le definizioni di metrica nell'area di lavoro|
|/workspaces/notificationSettings/delete|Elimina le impostazioni di notifica dell'utente per l'area di lavoro.|
|/workspaces/notificationSettings/read|Ottiene le impostazioni di notifica dell'utente per l'area di lavoro.|
|/workspaces/notificationSettings/write|Imposta le impostazioni di notifica dell'utente per l'area di lavoro.|
|/workspaces/purge/action|Elimina i dati specificati dall'area di lavoro|
|/workspaces/read|Ottiene un'area di lavoro esistente|
|/workspaces/savedSearches/delete|Elimina una query di ricerca salvata|
|/workspaces/savedSearches/read|Ottiene una query di ricerca salvata|
|/workspaces/savedSearches/write|Crea una query di ricerca salvata|
|/workspaces/schema/read|Ottiene lo schema di ricerca per l'area di lavoro.  Lo schema di ricerca include i campi esposti e i relativi tipi.|
|/workspaces/search/action|Esegue una query di ricerca|
|/workspaces/sharedKeys/action|Recupera le chiavi condivise per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro.|
|/workspaces/sharedKeys/read|Recupera le chiavi condivise per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro.|
|/workspaces/storageinsightconfigs/delete|Elimina una configurazione di archiviazione. Questa operazione interrompe la lettura dei dati dall’account di archiviazione da parte di Microsoft Operational Insights.|
|/workspaces/storageinsightconfigs/read|Ottiene una configurazione di archiviazione.|
|/workspaces/storageinsightconfigs/write|Crea una nuova configurazione di archiviazione. Tali configurazioni servono per estrarre i dati da una posizione in un account di archiviazione esistente.|
|/workspaces/usages/read|Ottiene i dati di utilizzo di un'area di lavoro tra cui la quantità di dati letti dall'area di lavoro.|
|/workspaces/write|Crea una nuova area di lavoro o collega a un'area di lavoro esistente fornendo l'ID cliente dall’area di lavoro esistente.|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Operazione | DESCRIZIONE |
|---|---|
|/managementAssociations/delete|Elimina un'associazione di gestione esistente|
|/managementAssociations/read|Ottiene un'associazione di gestione esistente|
|/managementAssociations/write|Crea una nuova associazione di gestione|
|/managementConfigurations/delete|Elimina una configurazione di gestione esistente|
|/managementConfigurations/read|Ottiene una configurazione di gestione esistente|
|/managementConfigurations/write|Crea una nuova configurazione di gestione|
|/register/action|Registra una sottoscrizione a un provider di risorse.|
|/solutions/delete|Elimina soluzione OMS esistente|
|/solutions/read|Ottiene soluzione OMS esistente|
|/solutions/write|Crea una nuova soluzione OMS|

## <a name="microsoftportal"></a>Microsoft.Portal

| Operazione | DESCRIZIONE |
|---|---|
|/dashboards/delete|Rimuove il dashboard dalla sottoscrizione.|
|/dashboards/read|Legge i dashboard nella sottoscrizione.|
|/dashboards/write|Aggiunge o modifica un dashboard in una sottoscrizione.|

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

| Operazione | DESCRIZIONE |
|---|---|
|/capacities/checkNameAvailability/action|Controlla che il nome di capacità dedicata di Power BI sia valido e non in uso.|
|/capacities/delete|Elimina la capacità dedicata Power BI.|
|/capacities/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per la capacità dedicata di Power BI.|
|/capacities/read|Recupera le informazioni della capacità dedicata di Power BI specificata.|
|/capacities/write|Crea o aggiorna la capacità dedicata di Power BI specificata.|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Operazione | DESCRIZIONE |
|---|---|
|/locations/allocatedStamp/read|GetAllocatedStamp è un'operazione interna usata dal servizio|
|/locations/allocateStamp/action|AllocateStamp è un'operazione interna usata dal servizio|
|/locations/backupPreValidateProtection/action||
|/locations/backupStatus/action|Controlla lo stato del backup dell'insieme di credenziali di Servizi di ripristino|
|/locations/backupValidateFeatures/action|Convalida le funzioni|
|/operations/read|L'operazione restituisce l'elenco delle operazioni per un provider di risorse|
|/register/action|Registra la sottoscrizione per il provider di risorse specificato|
|/Vaults/backupconfig/read|Restituisce la configurazione dell'insieme di credenziali di Servizi di ripristino.|
|/Vaults/backupconfig/write|Aggiorna la configurazione dell'insieme di credenziali di Servizi di ripristino.|
|/Vaults/backupEngines/read|Restituisce tutti i server di gestione di backup registrati nell'insieme di credenziali.|
|/Vaults/backupFabrics/{fabricName}/protectionContainers/{containerName}/items/read|Ottiene tutti gli elementi in un contenitore|
|/Vaults/backupFabrics/backupProtectionIntent/write|Crea un programma di protezione del backup|
|/Vaults/backupFabrics/operationResults/read|Restituisce lo stato dell'operazione|
|/Vaults/backupFabrics/protectableContainers/read|Ottiene tutti i contenitori che si possono proteggere|
|/Vaults/backupFabrics/protectionContainers/inquire/action|Esegue operazioni di richiesta di informazioni per i carichi di lavoro all'interno di un contenitore|
|/Vaults/backupFabrics/protectionContainers/operationResults/read|Ottiene il risultato dell'operazione eseguita sul contenitore di protezione.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action|Esegue il backup dell'elemento protetto.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/delete|Elimina l'elemento protetto|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read|Ottiene il risultato dell'operazione eseguita sugli elementi protetti.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read|Restituisce lo stato dell'operazione eseguita sugli elementi protetti.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/read|Restituisce i dettagli dell'oggetto dell'elemento protetto|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/provisionInstantItemRecovery/action|Effettua il provisioning del ripristino elementi immediato per l'elemento protetto|
|/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read|Ottiene i punti di ripristino degli elementi protetti.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/restore/action|Ripristina i punti di ripristino degli elementi protetti.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/revokeInstantItemRecovery/action|Revoca il ripristino elementi immediato per l'elemento protetto|
|/Vaults/backupFabrics/protectionContainers/protectedItems/write|Crea un elemento protetto di backup|
|/Vaults/backupFabrics/protectionContainers/read|Restituisce tutti i contenitori registrati|
|/Vaults/backupFabrics/protectionContainers/write|Crea un contenitore registrato|
|/Vaults/backupFabrics/refreshContainers/action|Aggiorna l'elenco di contenitori|
|/Vaults/backupJobs/cancel/action|Annulla il processo|
|/Vaults/backupJobs/operationResults/read|Restituisce il risultato dell'operazione di processo.|
|/Vaults/backupJobs/read|Restituisce tutti gli oggetti processo|
|/Vaults/backupJobsExport/action|Esporta processi|
|/Vaults/backupJobsExport/operationResults/read|Restituisce il risultato dell'operazione di esportazione del processo.|
|/Vaults/backupManagementMetaData/read|Restituisce i metadati di gestione di backup di un insieme di credenziali di Servizi di ripristino.|
|/Vaults/backupOperationResults/read|Restituisce il risultato dell'operazione di backup di un insieme di credenziali di Servizi di ripristino.|
|/Vaults/backupOperations/read|Restituisce lo stato dell'operazione di backup dell'insieme di credenziali di Servizi di ripristino.|
|/Vaults/backupPolicies/delete|Elimina i criteri di protezione|
|/Vaults/backupPolicies/operationResults/read|Ottiene i risultati dell'operazione sui criteri.|
|/Vaults/backupPolicies/operations/read|Ottiene lo stato dell'operazione sui criteri.|
|/Vaults/backupPolicies/read|Restituisce tutti i criteri di protezione|
|/Vaults/backupPolicies/write|Crea i criteri di protezione|
|/Vaults/backupProtectableItems/read|Restituisce l'elenco di tutti gli elementi da proteggere.|
|/Vaults/backupProtectedItems/read|Restituisce l'elenco di tutti gli elementi protetti.|
|/Vaults/backupProtectionContainers/read|Restituisce tutti i contenitori che appartengono alla sottoscrizione|
|/Vaults/backupSecurityPIN/action|Restituisce le informazioni sul PIN di sicurezza dell'insieme di credenziali di Servizi di ripristino.|
|/Vaults/backupstorageconfig/read|Restituisce la configurazione di archiviazione dell'insieme di credenziali di Servizi di ripristino.|
|/Vaults/backupstorageconfig/write|Aggiorna la configurazione di archiviazione dell'insieme di credenziali di Servizi di ripristino.|
|/Vaults/backupUsageSummaries/read|Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino.|
|/Vaults/certificates/write|L'operazione Aggiorna certificato risorsa aggiorna il certificato delle credenziali della risorsa o dell'insieme di credenziali.|
|/Vaults/delete|L'operazione Elimina insieme di credenziali elimina la risorsa di Azure specificata di tipo 'vault'|
|/Vaults/extendedInformation/delete|L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault?|
|/Vaults/extendedInformation/read|L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault?|
|/Vaults/extendedInformation/write|L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault?|
|/Vaults/monitoringAlerts/read|Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino.|
|/Vaults/monitoringAlerts/write|Risolve l'avviso.|
|/Vaults/monitoringConfigurations/read|Ottiene la configurazione delle notifiche dell'insieme di credenziali dei servizi di ripristino.|
|/Vaults/monitoringConfigurations/write|Configura le notifiche di posta elettronica all'insieme di credenziali di Servizi di ripristino.|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/read|Diagnostica di Backup di Azure|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/write|Diagnostica di Backup di Azure|
|/Vaults/providers/Microsoft.Insights/logDefinitions/read|Log di Backup di Azure|
|/Vaults/providers/Microsoft.Insights/metricDefinitions/read|Metrica di Backup di Azure|
|/Vaults/read|L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault'|
|/Vaults/registeredIdentities/delete|L'operazione Annulla registrazione contenitore di servizi può essere usata per annullare la registrazione di un contenitore.|
|/Vaults/registeredIdentities/operationResults/read|L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono|
|/Vaults/registeredIdentities/read|L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa.|
|/Vaults/registeredIdentities/write|L'operazione Registra contenitore di servizi può essere usata per registrare un contenitore con il servizio di ripristino.|
|/vaults/replicationAlertSettings/read|Legge tutte le impostazioni degli avvisi|
|/vaults/replicationAlertSettings/write|Crea o aggiorna le impostazioni degli avvisi|
|/vaults/replicationEvents/read|Legge tutti gli eventi|
|/vaults/replicationFabrics/checkConsistency/action|Verifica la coerenza dell'infrastruttura|
|/vaults/replicationFabrics/delete|Elimina tutte le infrastrutture|
|/vaults/replicationFabrics/deployProcessServerImage/action|Distribuisce immagine del server di elaborazione|
|/vaults/replicationFabrics/read|Legge tutte le infrastrutture|
|/vaults/replicationFabrics/reassociateGateway/action|Riassocia gateway|
|/vaults/replicationFabrics/remove/action|Rimuove l'infrastruttura|
|/vaults/replicationFabrics/renewcertificate/action|Rinnova il certificato per l'infrastruttura|
|/vaults/replicationFabrics/replicationNetworks/read|Legge tutte le reti|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete|Elimina tutti i mapping di rete|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read|Legge tutti i mapping di rete|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write|Crea o aggiorna tutti i mapping di rete|
|/vaults/replicationFabrics/replicationProtectionContainers/ discoverProtectableItem/action|Individua elemento da proteggere|
|/vaults/replicationFabrics/replicationProtectionContainers/read|Legge tutti i contenitori di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/remove/action|Rimuove il contenitore di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectableItems/read|Legge gli elementi da proteggere|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/applyRecoveryPoint/action|Applica punto di ripristino|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/delete|Elimina tutti gli elementi protetti|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/failoverCommit/action|Commit del failover|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/plannedFailover/action|Failover pianificato|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/read|Legge tutti gli elementi protetti|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/recoveryPoints/read|Legge i punti di ripristino di replica|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/remove/action|Rimuove l'elemento protetto|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/repairReplication/action|Ripristina replica|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/reProtect/action|Riprotegge l'elemento protetto|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailover/action|Failover di test|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailoverCleanup/action|Pulizia del failover di test|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/unplannedFailover/action|Failover|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/updateMobilityService/action|Aggiorna servizio Mobility|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/write|Crea o aggiorna gli elementi protetti|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/delete|Elimina i mapping dei contenitori di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/read|Legge i mapping dei contenitori di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/remove/action|Rimuove il mapping di contenitore di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/write|Crea o aggiorna i mapping dei contenitori di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action|Cambia il contenitore di protezione|
|/vaults/replicationFabrics/replicationProtectionContainers/write|Crea o aggiorna i contenitori di protezione|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete|Elimina tutti i provider dei servizi di ripristino|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/read|Legge tutti i provider dei servizi di ripristino|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/ refreshProvider/action|Aggiorna i provider|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action|Rimuove tutti i provider dei servizi di ripristino|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/write|Crea o aggiorna tutti i provider di Servizi di ripristino|
|/vaults/replicationFabrics/replicationStorageClassifications/read|Legge tutte le classificazioni di archiviazione|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/delete|Elimina tutti i mapping di classificazioni di archiviazione|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/read|Legge tutti i mapping di classificazioni di archiviazione|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/write|Crea o aggiorna tutti i mapping di classificazioni di archiviazione|
|/vaults/replicationFabrics/replicationvCenters/delete|Elimina tutti i processi|
|/vaults/replicationFabrics/replicationvCenters/read|Legge tutti i processi|
|/vaults/replicationFabrics/replicationvCenters/write|Crea o aggiorna tutti i processi|
|/vaults/replicationFabrics/write|Crea o aggiorna tutte le infrastrutture|
|/vaults/replicationJobs/cancel/action|Annulla processo|
|/vaults/replicationJobs/read|Legge tutti i processi|
|/vaults/replicationJobs/restart/action|Riavvia processo|
|/vaults/replicationJobs/resume/action|Riprende il processo|
|/vaults/replicationPolicies/delete|Elimina i criteri|
|/vaults/replicationPolicies/read|Legge tutti i criteri|
|/vaults/replicationPolicies/write|Crea o aggiorna i criteri|
|/vaults/replicationRecoveryPlans/delete|Elimina i piani di ripristino|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Piano di ripristino del commit del failover|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Piano di ripristino del failover pianificato|
|/vaults/replicationRecoveryPlans/read|Legge i piani di ripristino|
|/vaults/replicationRecoveryPlans/reProtect/action|Piano di ripristino di riprotezione|
|/vaults/replicationRecoveryPlans/testFailover/action|Piano di ripristino del failover di test|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Piano di ripristino della pulizia del failover di test|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Piano di ripristino del failover|
|/vaults/replicationRecoveryPlans/write|Crea o aggiorna i piani di ripristino|
|/Vaults/tokenInfo/read|Restituisce le informazioni sul token dell'insieme di credenziali di Servizi di ripristino.|
|/vaults/usages/read|Legge tutti gli utilizzi dell'insieme di credenziali|
|/Vaults/usages/read|Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino.|
|/Vaults/vaultTokens/read|L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali.|
|/Vaults/write|L'operazione Crea insieme di credenziali crea una risorsa di Azure di tipo 'vault'|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Operazione | DESCRIZIONE |
|---|---|
|/checkNameAvailability/action|Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata.|
|/checkNamespaceAvailability/action|Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata. Questa API è deprecata, usare invece CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/action|Aggiorna la regola di autorizzazione dello spazio dei nomi. Questa API è deprecata. Usare invece una chiamata PUT per aggiornare la regola di autorizzazione dello spazio dei nomi... Questa operazione non supportata per l'API versione 2017-04-01.|
|/namespaces/authorizationRules/delete|Elimina regola di autorizzazione dello spazio dei nomi. Non è possibile eliminare la regola di autorizzazione dello spazio dei nomi predefinita. |
|/namespaces/authorizationRules/listkeys/action|Ottiene la stringa di connessione per lo spazio dei nomi|
|/namespaces/authorizationRules/read|Ottiene l'elenco di descrizioni delle regole di autorizzazione degli spazi dei nomi.|
|/namespaces/authorizationRules/regenerateKeys/action|Rigenera la chiave primaria o secondaria per la risorsa|
|/namespaces/authorizationRules/write|Crea regole di autorizzazione a livello dello spazio dei nomi e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/namespaces/Delete|Elimina una risorsa spazio dei nomi|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Ottiene le chiavi delle regole di autorizzazione per lo spazio dei nomi primario di ripristino di emergenza|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Ottiene le regole di autorizzazione dello spazio dei nomi primario di ripristino di emergenza|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Disabilita il ripristino di emergenza e arresta la replica delle modifiche dagli spazi dei nomi primari a quelli secondari.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Verifica la disponibilità di alias dello spazio dei nomi nella sottoscrizione specificata.|
|/namespaces/disasterRecoveryConfigs/delete|Elimina la configurazione di ripristino di emergenza associata allo spazio dei nomi. Questa operazione può essere chiamata solo tramite lo spazio dei nomi primario.|
|/namespaces/disasterRecoveryConfigs/failover/action|Chiama il failover di ripristino di emergenza con ridondanza geografica e riconfigura l'alias dello spazio dei nomi affinché punti allo spazio dei nomi secondario.|
|/namespaces/disasterRecoveryConfigs/read|Recupera la configurazione di ripristino di emergenza associata allo spazio dei nomi.|
|/namespaces/disasterRecoveryConfigs/write|Crea o aggiorna la configurazione di ripristino di emergenza associata allo spazio dei nomi.|
|/namespaces/HybridConnections/authorizationRules/action|Operazione per aggiornare una connessione ibrida. Questa operazione non supportata per l'API versione 2017-04-01. Regole di autorizzazione. Usare una chiamata PUT per aggiornare la regola di autorizzazione.|
|/namespaces/HybridConnections/authorizationRules/delete|Operazione per l'eliminazione delle regole di autorizzazione della connessione ibrida|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|Ottiene la stringa di connessione per la connessione ibrida|
|/namespaces/HybridConnections/authorizationRules/read| Ottiene l'elenco delle regole di autorizzazione della connessione ibrida|
|/namespaces/HybridConnections/authorizationRules/regeneratekeys/action|Rigenera la chiave primaria o secondaria per la risorsa|
|/namespaces/HybridConnections/authorizationRules/write|Crea regole di autorizzazione della connessione ibrida e ne aggiorna le proprietà. È possibile aggiornare i diritti di accesso delle regole di autorizzazione.|
|/namespaces/HybridConnections/Delete|Operazione per l'eliminazione di risorse di connessione ibrida|
|/namespaces/HybridConnections/read|Ottiene l'elenco delle descrizioni delle risorse della connessione ibrida|
|/namespaces/HybridConnections/write|Crea o aggiorna proprietà della connessione ibrida.|
|/namespaces/messagingPlan/read|Ottiene il piano di messaggistica per uno spazio dei nomi. Questa API è deprecata. Le proprietà esposte tramite la risorsa MessagingPlan sono state spostate nella risorsa spazio dei nomi, padre, nelle versioni API successive. Questa operazione non supportata per l'API versione 2017-04-01.|
|/namespaces/messagingPlan/write|Aggiorna il piano di messaggistica per uno spazio dei nomi. Questa API è deprecata. Le proprietà esposte tramite la risorsa MessagingPlan sono state spostate nella risorsa spazio dei nomi, padre, nelle versioni API successive. Questa operazione non supportata per l'API versione 2017-04-01.|
|/namespaces/operationresults/read|Operazione di recupero dello stato dello spazio dei nomi|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Ottiene l'elenco di descrizioni delle risorse di metrica dello spazio dei nomi|
|/namespaces/read|Ottiene l'elenco delle descrizioni delle risorse spazio dei nomi|
|/namespaces/WcfRelays/authorizationRules/action|Operazione per aggiornare un inoltro WCF. Questa operazione non supportata per l'API versione 2017-04-01. Regole di autorizzazione. Usare una chiamata PUT per aggiornare la regola di autorizzazione.|
|/namespaces/WcfRelays/authorizationRules/delete|Operazione per l'eliminazione di regole di autorizzazione dell'inoltro WCF|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|Ottiene la stringa di connessione per l'inoltro WCF|
|/namespaces/WcfRelays/authorizationRules/read| Ottiene l'elenco delle regole di autorizzazione dell'inoltro WCF|
|/namespaces/WcfRelays/authorizationRules/regeneratekeys/action|Rigenera la chiave primaria o secondaria per la risorsa|
|/namespaces/WcfRelays/authorizationRules/write|Crea regole di autorizzazione dell'inoltro WCF e ne aggiorna le proprietà. È possibile aggiornare i diritti di accesso delle regole di autorizzazione.|
|/namespaces/WcfRelays/Delete|Operazione per l'eliminazione delle risorse dell'inoltro WCF|
|/namespaces/WcfRelays/read|Ottiene l'elenco delle descrizioni delle risorse dell'inoltro WCF|
|/namespaces/WcfRelays/write|Crea o aggiorna proprietà inoltro WCF.|
|/namespaces/write|Crea una risorsa spazio dei nomi e ne aggiorna le proprietà. Le proprietà che si possono aggiornare sono quelle relative ai tag e alla capacità dello spazio dei nomi.|
|/operations/read|Ottiene operazioni|
|/register/action|Registra la sottoscrizione per il provider di risorse inoltro e abilita la creazione di risorse inoltro|
|/unregister/action|Registra la sottoscrizione per il provider di risorse inoltro e abilita la creazione di risorse inoltro|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Operazione | DESCRIZIONE |
|---|---|
|/AvailabilityStatuses/current/read|Ottiene lo stato di disponibilità per la risorsa specificata|
|/AvailabilityStatuses/read|Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato|
|/healthevent/action|Indica la variazione dello stato di integrità per la risorsa specificata|
|/healthevent/Activated/action|Indica la variazione dello stato di integrità per la risorsa specificata|
|/healthevent/InProgress/action|Indica la variazione dello stato di integrità per la risorsa specificata|
|/healthevent/Pending/action|Indica la variazione dello stato di integrità per la risorsa specificata|
|/healthevent/Resolved/action|Indica la variazione dello stato di integrità per la risorsa specificata|
|/healthevent/Updated/action|Indica la variazione dello stato di integrità per la risorsa specificata|
|/register/action|Registra la sottoscrizione per Microsoft ResourceHealth|

## <a name="microsoftresources"></a>Microsoft.Resources

| Operazione | DESCRIZIONE |
|---|---|
|/checkResourceName/action|Verifica la validità del nome della risorsa.|
|/deployments/cancel/action|Annulla una distribuzione.|
|/deployments/delete|Elimina una distribuzione.|
|/deployments/operations/read|Ottiene o elenca le operazioni di distribuzione.|
|/deployments/read|Ottiene o elenca le distribuzioni.|
|/deployments/validate/action|Convalida una distribuzione.|
|/deployments/write|Crea o aggiorna una distribuzione.|
|/links/delete|Elimina un collegamento a una risorsa.|
|/links/read|Ottiene o elenca i collegamenti a una risorsa.|
|/links/write|Crea o aggiorna un collegamento a una risorsa.|
|/marketplace/purchase/action|Permette di acquistare una risorsa dal marketplace.|
|/providers/read|Ottiene l'elenco dei provider.|
|/resources/read|Ottiene l'elenco delle risorse in base a filtri.|
|/subscriptions/locations/read|Ottiene l'elenco delle posizioni supportate.|
|/subscriptions/operationresults/read|Ottiene i risultati dell'operazione di sottoscrizione.|
|/subscriptions/providers/read|Ottiene o elenca i provider di risorse.|
|/subscriptions/read|Ottiene l'elenco delle sottoscrizioni.|
|/subscriptions/resourceGroups/delete|Elimina un gruppo di risorse e tutte le risorse in esso contenute.|
|/subscriptions/resourcegroups/deployments/operations/read|Ottiene o elenca le operazioni di distribuzione.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|Ottiene o elenca gli stati dell'operazione di distribuzione.|
|/subscriptions/resourcegroups/deployments/read|Ottiene o elenca le distribuzioni.|
|/subscriptions/resourcegroups/deployments/write|Crea o aggiorna una distribuzione.|
|/subscriptions/resourceGroups/moveResources/action|Sposta le risorse da un gruppo di risorse a un altro.|
|/subscriptions/resourceGroups/read|Ottiene o elenca i gruppi di risorse.|
|/subscriptions/resourcegroups/resources/read|Ottiene le risorse del gruppo di risorse.|
|/subscriptions/resourceGroups/validateMoveResources/action|Convalida lo spostamento di risorse da un gruppo di risorse a un altro.|
|/subscriptions/resourceGroups/write|Crea o aggiorna un gruppo di risorse.|
|/subscriptions/resources/read|Ottiene le risorse di una sottoscrizione.|
|/subscriptions/tagNames/delete|Elimina una categoria della sottoscrizione.|
|/subscriptions/tagNames/read|Ottiene o elenca le categorie della sottoscrizione.|
|/subscriptions/tagNames/tagValues/delete|Elimina un valore di categoria della sottoscrizione.|
|/subscriptions/tagNames/tagValues/read|Ottiene o elenca i valori delle categorie della sottoscrizione.|
|/subscriptions/tagNames/tagValues/write|Aggiunge un valore di categoria della sottoscrizione.|
|/subscriptions/tagNames/write|Aggiunge una categoria della sottoscrizione.|
|/tenants/read|Ottiene l'elenco dei tenant.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Operazione | DESCRIZIONE |
|---|---|
|/jobcollections/delete|Elimina la raccolta processi.|
|/jobcollections/disable/action|Disabilita la raccolta processi.|
|/jobcollections/enable/action|Abilita la raccolta processi.|
|/jobcollections/jobs/delete|Elimina il processo.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Genera la definizione dell'app per la logica in base a un processo dell'Utilità di pianificazione.|
|/jobcollections/jobs/jobhistories/read|Ottiene la cronologia processi.|
|/jobcollections/jobs/read|Ottiene il processo.|
|/jobcollections/jobs/run/action|Esegue il processo.|
|/jobcollections/jobs/write|Crea o aggiorna il processo.|
|/jobcollections/read|Ottiene la raccolta processi|
|/jobcollections/write|Crea o aggiorna la raccolta processi.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Operazione | DESCRIZIONE |
|---|---|
|/checkNameAvailability/action|Controlla la disponibilità del nome del servizio.|
|/register/action|Registra la sottoscrizione per il provider di risorse di ricerca e consente la creazione di servizi di ricerca.|
|/searchServices/createQueryKey/action|Crea la chiave di query.|
|/searchServices/delete|Elimina il servizio di ricerca.|
|/searchServices/diagnosticSettings/read|Ottiene l'impostazione di diagnostica letta per la risorsa|
|/searchServices/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/searchServices/listAdminKeys/action|Legge le chiavi di amministrazione.|
|/searchServices/logDefinitions/read|Ottiene i log disponibili per il servizio di ricerca|
|/searchServices/metricDefinitions/read|Ottiene la metrica disponibile per il servizio di ricerca|
|/searchServices/queryKey/delete|Elimina la chiave di query.|
|/searchServices/queryKey/read|Legge le chiavi di query.|
|/searchServices/read|Legge il servizio di ricerca.|
|/searchServices/regenerateAdminKey/action|Rigenera la chiave di amministrazione.|
|/searchServices/start/action|Avvia il servizio di ricerca.|
|/searchServices/stop/action|Arresta il servizio di ricerca.|
|/searchServices/write|Crea o aggiorna il servizio di ricerca.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Operazione | DESCRIZIONE |
|---|---|
|/alerts/read|Ottiene tutti gli avvisi di sicurezza disponibili|
|/applicationWhitelistings/read|Ottiene gli elenchi elementi consentiti delle applicazioni|
|/applicationWhitelistings/write|Crea un nuovo elenco elementi consentiti per l'applicazione o ne aggiorna uno esistente|
|/complianceResults/read|Ottiene i risultati di conformità per la risorsa|
|/locations/alerts/activate/action|Attiva un avviso di sicurezza|
|/locations/alerts/dismiss/action|Ignora un avviso di sicurezza|
|/locations/alerts/read|Ottiene tutti gli avvisi di sicurezza disponibili|
|/locations/jitNetworkAccessPolicies/initiate/action|Avvia un criterio di accesso alla rete JIT|
|/locations/jitNetworkAccessPolicies/read|Ottiene i criteri di accesso alla rete JIT|
|/locations/jitNetworkAccessPolicies/write|Crea un nuovo criterio di accesso alla rete JIT o ne aggiorna uno esistente|
|/locations/read|Ottiene la posizione dei dati di sicurezza|
|/locations/tasks/activate/action|Attiva un consiglio per la sicurezza|
|/locations/tasks/dismiss/action|Ignora un consiglio per la sicurezza|
|/locations/tasks/read|Ottiene tutti i consigli per la sicurezza disponibili|
|/locations/tasks/resolve/action|Risolve un consiglio di sicurezza|
|/locations/tasks/start/action|Avvia una raccomandazione di sicurezza|
|/policies/read|Ottiene i criteri di sicurezza|
|/policies/write|Aggiorna i criteri di sicurezza|
|/pricings/delete|Elimina le informazioni sui prezzi per l'ambito|
|/pricings/read|Ottiene le informazioni sui prezzi per l'ambito|
|/pricings/write|Aggiorna le informazioni sui prezzi per l'ambito|
|/register/action|Registra la sottoscrizione per Centro sicurezza di Azure|
|/securityContacts/delete|Elimina il contatto di sicurezza|
|/securityContacts/read|Ottiene il contatto di sicurezza|
|/securityContacts/write|Aggiorna il contatto di sicurezza|
|/securitySolutions/delete|Elimina una soluzione di sicurezza|
|/securitySolutions/read|Ottiene le soluzioni di sicurezza|
|/securitySolutions/write|Crea una nuova soluzione di sicurezza o ne aggiorna una esistente|
|/securitySolutionsReferenceData/read|Ottiene i dati di riferimento delle soluzioni di sicurezza|
|/securityStatuses/read|Ottiene gli stati di integrità della sicurezza per le soluzioni Azure|
|/securityStatusesSummaries/read|Ottiene i riepiloghi degli stati di sicurezza per l'ambito|
|/tasks/read|Ottiene tutti i consigli per la sicurezza disponibili|
|/webApplicationFirewalls/delete|Elimina un firewall dell'applicazione Web|
|/webApplicationFirewalls/read|Ottiene i firewall dell'applicazione Web|
|/webApplicationFirewalls/write|Crea un nuovo firewall dell'applicazione Web o ne aggiorna uno esistente|
|/workspaceSettings/connect/action|Modifica le impostazioni di riconnessione delle impostazioni dell'area di lavoro|
|/workspaceSettings/delete|Elimina le impostazioni dell'area di lavoro|
|/workspaceSettings/read|Ottiene le impostazioni dell'area di lavoro|
|/workspaceSettings/write|Aggiorna le impostazioni dell'area di lavoro|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Operazione | DESCRIZIONE |
|---|---|
|/checkNameAvailability/action|Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata.|
|/checkNamespaceAvailability/action|Verifica la disponibilità dello spazio dei nomi nella sottoscrizione specificata. Questa API è deprecata, usare invece CheckNameAvailabiltiy.|
|/namespaces/authorizationRules/action|Aggiorna la regola di autorizzazione dello spazio dei nomi. Questa API è deprecata. Usare invece una chiamata PUT per aggiornare la regola di autorizzazione dello spazio dei nomi... Questa operazione non supportata per l'API versione 2017-04-01.|
|/namespaces/authorizationRules/delete|Elimina regola di autorizzazione dello spazio dei nomi. Non è possibile eliminare la regola di autorizzazione dello spazio dei nomi predefinita. |
|/namespaces/authorizationRules/listkeys/action|Ottiene la stringa di connessione per lo spazio dei nomi|
|/namespaces/authorizationRules/read|Ottiene l'elenco di descrizioni delle regole di autorizzazione degli spazi dei nomi.|
|/namespaces/authorizationRules/regenerateKeys/action|Rigenera la chiave primaria o secondaria per la risorsa|
|/namespaces/authorizationRules/write|Crea regole di autorizzazione a livello dello spazio dei nomi e ne aggiorna le proprietà. È possibile aggiornare la chiave primaria, la chiave secondaria e i diritti di accesso delle regole di autorizzazione.|
|/namespaces/Delete|Elimina una risorsa spazio dei nomi|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Ottiene le chiavi delle regole di autorizzazione per lo spazio dei nomi primario di ripristino di emergenza|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Ottiene le regole di autorizzazione dello spazio dei nomi primario di ripristino di emergenza|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Disabilita il ripristino di emergenza e arresta la replica delle modifiche dagli spazi dei nomi primari a quelli secondari.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Verifica la disponibilità di alias dello spazio dei nomi nella sottoscrizione specificata.|
|/namespaces/disasterRecoveryConfigs/delete|Elimina la configurazione di ripristino di emergenza associata allo spazio dei nomi. Questa operazione può essere chiamata solo tramite lo spazio dei nomi primario.|
|/namespaces/disasterRecoveryConfigs/failover/action|Chiama il failover di ripristino di emergenza con ridondanza geografica e riconfigura l'alias dello spazio dei nomi affinché punti allo spazio dei nomi secondario.|
|/namespaces/disasterRecoveryConfigs/read|Recupera la configurazione di ripristino di emergenza associata allo spazio dei nomi.|
|/namespaces/disasterRecoveryConfigs/write|Crea o aggiorna la configurazione di ripristino di emergenza associata allo spazio dei nomi.|
|/namespaces/eventGridFilters/delete|Elimina il filtro di Griglia di eventi associato allo spazio dei nomi.|
|/namespaces/eventGridFilters/read|Ottiene il filtro di Griglia di eventi associato allo spazio dei nomi.|
|/namespaces/eventGridFilters/write|Crea o aggiorna il filtro di Griglia di eventi associato allo spazio dei nomi.|
|/namespaces/eventhubs/read|Ottiene l'elenco delle descrizioni delle risorse dell'hub eventi|
|/namespaces/messagingPlan/read|Ottiene il piano di messaggistica per uno spazio dei nomi. Questa API è deprecata. Le proprietà esposte tramite la risorsa MessagingPlan sono state spostate nella risorsa spazio dei nomi, padre, nelle versioni API successive. Questa operazione non supportata per l'API versione 2017-04-01.|
|/namespaces/messagingPlan/write|Aggiorna il piano di messaggistica per uno spazio dei nomi. Questa API è deprecata. Le proprietà esposte tramite la risorsa MessagingPlan sono state spostate nella risorsa spazio dei nomi, padre, nelle versioni API successive. Questa operazione non supportata per l'API versione 2017-04-01.|
|/namespaces/migrate/action|Operazione di migrazione dello spazio dei nomi|
|/namespaces/operationresults/read|Operazione di recupero dello stato dello spazio dei nomi|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'elenco di descrizioni delle risorse impostazioni di diagnostica dello spazio dei nomi|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Ottiene l'elenco di descrizioni delle risorse impostazioni di diagnostica dello spazio dei nomi|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Ottiene l'elenco di descrizioni delle risorse log dello spazio dei nomi|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Ottiene l'elenco di descrizioni delle risorse di metrica dello spazio dei nomi|
|/namespaces/queues/authorizationRules/action|Operazione per aggiornare la coda. Questa operazione non supportata per l'API versione 2017-04-01. Regole di autorizzazione. Usare una chiamata PUT per aggiornare la regola di autorizzazione.|
|/namespaces/queues/authorizationRules/delete|Operazione per l'eliminazione di regole di autorizzazione delle code|
|/namespaces/queues/authorizationRules/listkeys/action|Ottiene la stringa di connessione alla coda|
|/namespaces/queues/authorizationRules/read| Ottiene l'elenco delle regole di autorizzazione delle code|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Rigenera la chiave primaria o secondaria per la risorsa|
|/namespaces/queues/authorizationRules/write|Crea regole di autorizzazione delle code e ne aggiorna le proprietà. È possibile aggiornare i diritti di accesso delle regole di autorizzazione.|
|/namespaces/queues/Delete|Operazione per l'eliminazione delle risorse coda|
|/namespaces/queues/read|Ottiene l'elenco delle descrizioni delle risorse coda|
|/namespaces/queues/write|Crea o aggiorna proprietà di coda.|
|/namespaces/read|Ottiene l'elenco delle descrizioni delle risorse spazio dei nomi|
|/namespaces/topics/authorizationRules/action|Operazione per aggiornare l'argomento. Questa operazione non supportata per l'API versione 2017-04-01. Regole di autorizzazione. Usare una chiamata PUT per aggiornare la regola di autorizzazione.|
|/namespaces/topics/authorizationRules/delete|Operazione per l'eliminazione di regole di autorizzazione dell’argomento|
|/namespaces/topics/authorizationRules/listkeys/action|Ottiene la stringa di connessione all’argomento|
|/namespaces/topics/authorizationRules/read| Ottiene l'elenco delle regole di autorizzazione dell’argomento|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Rigenera la chiave primaria o secondaria per la risorsa|
|/namespaces/topics/authorizationRules/write|Crea regole di autorizzazione dell’argomento e ne aggiorna le proprietà. È possibile aggiornare i diritti di accesso delle regole di autorizzazione.|
|/namespaces/topics/Delete|Operazione per l'eliminazione di risorse argomento|
|/namespaces/topics/read|Ottiene l'elenco delle descrizioni delle risorse argomento|
|/namespaces/topics/subscriptions/Delete|Operazione per l’eliminazione di una risorsa TopicSubscription|
|/namespaces/topics/subscriptions/read|Ottiene l'elenco delle descrizioni delle risorse TopicSubscription|
|/namespaces/topics/subscriptions/rules/Delete|Operazione per l'eliminazione delle risorse di una regola|
|/namespaces/topics/subscriptions/rules/read|Ottiene l'elenco delle descrizioni delle risorse di una regola|
|/namespaces/topics/subscriptions/rules/write|Crea o aggiorna le proprietà di una regola.|
|/namespaces/topics/subscriptions/write|Crea o aggiorna le proprietà TopicSubscription.|
|/namespaces/topics/write|Crea o aggiorna le proprietà degli argomenti.|
|/namespaces/write|Crea una risorsa spazio dei nomi e ne aggiorna le proprietà. Le proprietà che si possono aggiornare sono quelle relative ai tag e alla capacità dello spazio dei nomi.|
|/operations/read|Ottiene operazioni|
|/register/action|Registra la sottoscrizione per il provider di risorse bus di servizio e consente la creazione di risorse bus di servizio|
|/sku/read|Ottiene l'elenco delle descrizioni di una risorsa SKU|
|/sku/regions/read|Ottiene l'elenco delle descrizioni di una risorsa SkuRegions|
|/unregister/action|Registra la sottoscrizione per il provider di risorse bus di servizio e consente la creazione di risorse bus di servizio|

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

| Operazione | DESCRIZIONE |
|---|---|
|/clusters/applications/delete|Elimina tutte le applicazioni|
|/clusters/applications/read|Legge tutte le applicazioni|
|/clusters/applications/services/delete|Elimina tutti i servizi|
|/clusters/applications/services/partitions/read|Legge tutte le partizioni|
|/clusters/applications/services/partitions/replicas/read|Legge tutte le repliche|
|/clusters/applications/services/read|Legge tutti i servizi|
|/clusters/applications/services/statuses/read|Legge tutti gli stati di servizio|
|/clusters/applications/services/write|Crea o aggiorna tutti i servizi|
|/clusters/applications/write|Crea o aggiorna tutte le applicazioni|
|/clusters/applicationTypes/delete|Elimina tutti i tipi di applicazioni|
|/clusters/applicationTypes/read|Legge tutti i tipi di applicazioni|
|/clusters/applicationTypes/versions/delete|Elimina tutte le versioni del tipo di applicazione|
|/clusters/applicationTypes/versions/read|Legge tutte le versioni del tipo di applicazione|
|/clusters/applicationTypes/versions/write|Crea o aggiorna tutte le versioni del tipo di applicazione|
|/clusters/applicationTypes/write|Crea o aggiorna tutti i tipi di applicazioni|
|/clusters/delete|Elimina tutti i cluster|
|/clusters/nodes/read|Legge tutti i nodi|
|/clusters/read|Legge tutti i cluster|
|/clusters/statuses/read|Legge tutti gli stati del cluster|
|/clusters/write|Crea o aggiorna tutti i cluster|
|/locations/clusterVersions/read|Legge tutte le versioni del cluster|
|/locations/environments/clusterVersions/read|Legge tutte le versioni cluster per un ambiente specifico|
|/locations/operationresults/read|Legge tutti i risultati delle operazioni|
|/locations/operations/read|Legge tutte le operazioni per località|
|/operations/read|Legge tutte le operazioni disponibili|
|/register/action|Registra tutte le azioni|

## <a name="microsoftsolutions"></a>Microsoft.Solutions

| Operazione | DESCRIZIONE |
|---|---|
|/applicationDefinitions/delete|Rimuove una definizione di applicazione.|
|/applicationDefinitions/read|Recupera un elenco di definizioni di applicazione.|
|/applicationDefinitions/write|Aggiunge o modifica una definizione di applicazione.|
|/applications/delete|Rimuove un'applicazione.|
|/applications/read|Recupera un elenco di applicazioni.|
|/applications/write|Crea un'applicazione.|
|/locations/operationStatuses/read|Legge lo stato dell'operazione per la risorsa.|
|/register/action|Esegue la registrazione in Microsoft Solutions.|

## <a name="microsoftsql"></a>Microsoft.Sql

| Operazione | DESCRIZIONE |
|---|---|
|/checkNameAvailability/action|Verifica se un determinato nome server è disponibile per il provisioning in tutto il mondo per una determinata sottoscrizione.|
|/locations/auditingSettingsAzureAsyncOperation/read|Recupera il risultato dell'operazione di impostazione criteri di controllo BLOB del server esteso|
|/locations/auditingSettingsOperationResults/read|Recupera il risultato dell’operazione di impostazione criteri di controllo BLOB del server|
|/locations/capabilities/read|Ottiene le capacitò per questa sottoscrizione in una determinata posizione|
|/locations/databaseAzureAsyncOperation/read|Ottiene lo stato di un'operazione del database.|
|/locations/databaseOperationResults/read|Ottiene lo stato di un'operazione del database.|
|/locations/deletedServerAsyncOperation/read|Ottiene le operazioni in corso sul server eliminato|
|/locations/deletedServerOperationResults/read|Ottiene le operazioni in corso sul server eliminato|
|/locations/deletedServers/read|Restituisce l'elenco dei server eliminati o ottiene le proprietà per il server eliminato specificato.|
|/locations/deletedServers/recover/action|Recupera un server eliminato|
|/locations/deleteVirtualNetworkOrSubnets/action|Elimina le regole di rete virtuale associate a una rete virtuale o una subnet|
|/locations/elasticPoolAzureAsyncOperation/read|Ottiene l'operazione asincrona di Azure per un'operazione asincrona di pool di database elastico|
|/locations/elasticPoolOperationResults/read|Ottiene i risultati di un'operazione di pool di database elastico.|
|/locations/extendedAuditingSettingsAzureAsyncOperation/read|Recupera il risultato dell'operazione di impostazione criteri di controllo BLOB del server esteso|
|/locations/extendedAuditingSettingsOperationResults/read|Recupera il risultato dell'operazione di impostazione criteri di controllo BLOB del server esteso|
|/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action|Completa l'operazione di ripristino del database gestito|
|/locations/managedTransparentDataEncryptionAzureAsyncOperation/read|Ottiene le operazioni in corso sulla crittografia trasparente dei dati del database gestito|
|/locations/managedTransparentDataEncryptionOperationResults/read|Ottiene le operazioni in corso sulla crittografia trasparente dei dati del database gestito|
|/locations/read|Ottiene le posizioni disponibili per una determinata sottoscrizione|
|/locations/syncAgentOperationResults/read|Recupera i risultati di un'operazione sulla risorsa dell'agente di sincronizzazione|
|/locations/syncDatabaseIds/read|Recupera gli ID del database di sincronizzazione per una particolare area e sottoscrizione|
|/locations/syncGroupOperationResults/read|Recupera i risultati di un'operazione sulla risorsa del gruppo di sincronizzazione|
|/locations/syncMemberOperationResults/read|Recupera i risultati di un'operazione sulla risorsa del membro di sincronizzazione|
|/locations/usages/read|Ottiene una raccolta di metrica di utilizzo per questa sottoscrizione in una posizione|
|/locations/virtualNetworkRulesAzureAsyncOperation/read|Restituisce i dettagli della specifica operazione asincrona di Azure per le regole di rete virtuale |
|/locations/virtualNetworkRulesOperationResults/read|Restituisce i dettagli della specifica operazione con le regole di rete virtuale |
|/managedInstances/administrators/delete|Elimina un amministratore esistente dell'istanza gestita.|
|/managedInstances/administrators/read|Ottiene un elenco degli amministratori di istanza gestita.|
|/managedInstances/administrators/write|Crea o aggiorna un amministratore di istanza gestita con i parametri specificati.|
|/managedInstances/databases/delete|Elimina un database gestito esistente|
|/managedInstances/databases/read|Ottiene un database gestito esistente|
|/managedInstances/databases/securityAlertPolicies/read|Recupera i dettagli dei criteri di rilevamento minacce configurati in uno specifico database gestito|
|/managedInstances/databases/securityAlertPolicies/write|Modifica i criteri di rilevamento minacce per uno specifico database gestito|
|/managedInstances/databases/securityEvents/read|Recupera gli eventi di sicurezza del database gestito|
|/managedInstances/databases/transparentDataEncryption/read|Recupera i dettagli di Transparent Data Encryption per uno specifico database gestito|
|/managedInstances/databases/transparentDataEncryption/write|Modifica Transparent Data Encryption per uno specifico database gestito|
|/managedInstances/databases/write|Crea un nuovo database o ne aggiorna uno esistente.|
|/managedInstances/delete|Elimina un'istanza gestita esistente.|
|/managedInstances/metricDefinitions/read|Ottiene le definizioni della metrica dell'istanza gestita|
|/managedInstances/metrics/read|Ottiene la metrica dell'istanza gestita|
|/managedInstances/read|Restituisce l'elenco delle istanze gestite o ottiene le proprietà per l'istanza gestita specificata.|
|/managedInstances/securityAlertPolicies/read|Recupera i dettagli dei criteri di rilevamento minacce configurati per uno specifico server gestito|
|/managedInstances/securityAlertPolicies/write|Modifica i criteri di rilevamento minacce per uno specifico server gestito|
|/managedInstances/write|Crea un'istanza gestita con i parametri specificati o aggiorna le proprietà o i tag per l'istanza gestita specificata.|
|/operations/read|Ottiene le operazioni REST disponibili|
|/register/action|Registra la sottoscrizione per il provider di risorse del database Microsoft SQL e consente la creazione di database Microsoft SQL.|
|/servers/administratorOperationResults/read|Ottiene le operazioni in corso per gli amministratori del server|
|/servers/administrators/delete|Elimina un amministratore del server|
|/servers/administrators/read|Recupera i dettagli dell’amministratore del server|
|/servers/administrators/write|Crea o aggiorna l’amministratore del server|
|/servers/advisors/read|Restituisce l'elenco degli advisor disponibili per il server|
|/servers/advisors/recommendedActions/read|Restituisce l'elenco di azioni consigliate per l’advisor specificato per il server|
|/servers/advisors/recommendedActions/write|Applica l'azione consigliata al server|
|/servers/advisors/write|Aggiorna lo stato di esecuzione automatica di un advisor a livello di server.|
|/servers/auditingPolicies/read|Recupera i dettagli dei criteri di controllo della tabella del server predefinito configurati in uno specifico database|
|/servers/auditingPolicies/write|Modifica il controllo della tabella del server predefinito per uno specifico server|
|/servers/auditingSettings/operationResults/read|Recupera il risultato dell’operazione di impostazione criteri di controllo BLOB del server|
|/servers/auditingSettings/read|Recupera i dettagli dei criteri di controllo del BLOB del server configurati in uno specifico server|
|/servers/auditingSettings/write|Modifica il controllo del BLOB del server per uno specifico server|
|/servers/automaticTuning/read|Restituisce le impostazioni di ottimizzazione automatica per il server|
|/servers/automaticTuning/write|Aggiorna le impostazioni di ottimizzazione automatica per il server e restituisce le impostazioni aggiornate|
|/servers/backupLongTermRetentionVaults/delete|Elimina un insieme di credenziali di archiviazione per un backup esistente.|
|/servers/backupLongTermRetentionVaults/read|Questa operazione serve per acquisire un insieme di credenziali di conservazione a lungo termine del backup. Restituisce informazioni sull'insieme di credenziali registrato su questo server|
|/servers/backupLongTermRetentionVaults/write|Questa operazione serve per registrare un insieme di credenziali di backup con conservazione a lungo termine in un server|
|/servers/communicationLinks/delete|Elimina un collegamento di comunicazione server esistente.|
|/servers/communicationLinks/read|Restituisce l'elenco dei collegamenti di comunicazione di un server specificato.|
|/servers/communicationLinks/write|Crea o aggiorna un collegamento di comunicazione del server.|
|/servers/connectionPolicies/read|Restituisce l'elenco dei criteri di connessione del server per un server specificato.|
|/servers/connectionPolicies/write|Crea o aggiorna i criteri di connessione del server.|
|/servers/databases/advisors/read|Restituisce l'elenco degli advisor disponibili per il database|
|/servers/databases/advisors/recommendedActions/read|Restituisce l'elenco di azioni consigliate per l’advisor specificato per il database|
|/servers/databases/advisors/recommendedActions/write|Applica l'azione consigliata al database|
|/servers/databases/advisors/write|Aggiorna lo stato di esecuzione automatica di un advisor a livello di database.|
|/servers/databases/auditingPolicies/read|Recupera i dettagli dei criteri di controllo della tabella configurati in uno specifico database|
|/servers/databases/auditingPolicies/write|Modifica i criteri di controllo della tabella per uno specifico database|
|/servers/databases/auditingSettings/read|Recupera i dettagli dei criteri di controllo del BLOB configurati in uno specifico database|
|/servers/databases/auditingSettings/write|Modifica i criteri di controllo del BLOB per uno specifico database|
|/servers/databases/auditRecords/read|Recupera i record di controllo BLOB del database|
|/servers/databases/automaticTuning/read|Restituisce le impostazioni di ottimizzazione automatica per un database|
|/servers/databases/automaticTuning/write|Aggiorna le impostazioni di ottimizzazione automatica per un database e restituisce le impostazioni aggiornate|
|/servers/databases/azureAsyncOperation/read|Ottiene lo stato di un'operazione del database.|
|/servers/databases/backupLongTermRetentionPolicies/read|Restituisce l'elenco dei criteri di archiviazione di backup di un database specifico.|
|/servers/databases/backupLongTermRetentionPolicies/write|Crea o aggiorna i criteri di archiviazione di backup di un database.|
|/servers/databases/connectionPolicies/read|Recupera i dettagli dei criteri di connessione configurati su uno specifico database|
|/servers/databases/connectionPolicies/write|Modifica i criteri di connessione per uno specifico database|
|/servers/databases/dataMaskingPolicies/read|Restituisce l'elenco dei criteri di maschera dati del database.|
|/servers/databases/dataMaskingPolicies/rules/delete|Elimina la regola per i criteri di mascheramento dati per uno specifico database|
|/servers/databases/dataMaskingPolicies/rules/read|Recupera i dettagli della regola per i criteri di mascheramento dati configurati in uno specifico database|
|/servers/databases/dataMaskingPolicies/rules/write|Modifica la regola per i criteri di mascheramento dati per uno specifico database|
|/servers/databases/dataMaskingPolicies/write|Modifica i criteri di mascheramento dati per uno specifico database|
|/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read|Restituisce le informazioni sul passaggio della query distribuita della query del data warehouse per l’ID passaggio selezionato|
|/servers/databases/dataWarehouseQueries/read|Restituisce le informazioni sulla query di distribuzione del data warehouse per l’ID query selezionato|
|/servers/databases/dataWarehouseUserActivities/read|Recupera le attività dell'utente per un'istanza di SQL Data Warehouse incluse le query in esecuzione e sospese|
|/servers/databases/delete|Elimina un database esistente.|
|/servers/databases/export/action|Esporta un database SQL di Azure|
|/servers/databases/extendedAuditingSettings/read|Recupera i dettagli dei criteri di controllo BLOB esteso configurati in uno specifico database|
|/servers/databases/extendedAuditingSettings/write|Modifica i criteri di controllo BLOB esteso per uno specifico database|
|/servers/databases/extensions/read|Ottiene una raccolta di estensioni per il database.|
|/servers/databases/extensions/write|Modifica l'estensione per un determinato database|
|/servers/databases/geoBackupPolicies/read|Recupera i criteri di backup di replica geografica per un determinato database|
|/servers/databases/geoBackupPolicies/write|Crea o aggiorna i criteri di backup di replica geografica di un database|
|/servers/databases/importExportOperationResults/read|Ottiene le operazioni di importazione/esportazione in corso|
|/servers/databases/metricDefinitions/read|Restituisce i tipi di metriche disponibili per i database|
|/servers/databases/metrics/read|Restituisce la metrica per i database|
|/servers/databases/move/action|Rinomina il database SQL di Azure|
|/servers/databases/operationResults/read|Ottiene lo stato di un'operazione del database.|
|/servers/databases/operations/cancel/action|Annulla l'operazione asincrona in sospeso del database SQL di Azure che non è ancora stata completata.|
|/servers/databases/operations/read|Restituisce l'elenco delle operazioni eseguite nel database|
|/servers/databases/pause/action|Mette in pausa il database Azure SQL Datawarehouse|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/servers/databases/providers/Microsoft.Insights/logDefinitions/read|Ottiene i registri disponibili per i database|
|/servers/databases/providers/Microsoft.Insights/metricDefinitions/read|Restituisce i tipi di metriche disponibili per i database|
|/servers/databases/queryStore/queryTexts/read|Restituisce la raccolta dei testi delle query che corrispondono ai parametri specificati.|
|/servers/databases/queryStore/read|Restituisce i valori correnti delle impostazioni Query Store per il database.|
|/servers/databases/queryStore/write|Aggiorna le impostazioni di Archivio query per il database|
|/servers/databases/read|Restituisce l'elenco dei database o ottiene le proprietà per il database specificato.|
|/servers/databases/replicationLinks/delete|Termina forzatamente la relazione di replica e con potenziale perdita di dati|
|/servers/databases/replicationLinks/failover/action|Failover dopo la sincronizzazione di tutte modifiche dal database primario, rendendo questo database primario nella relazione di replica e rendendo il database primario remoto il database secondario|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Esegue immediatamente il failover con potenziale perdita di dati, rendendo questo database primario nella relazione di replica e rendendo il database primario remoto il database secondario|
|/servers/databases/replicationLinks/read|Restituisce i dettagli sui collegamenti di replica stabiliti per uno specifico database|
|/servers/databases/replicationLinks/unlink/action|Termina forzatamente la relazione di replica o dopo la sincronizzazione con il partner|
|/servers/databases/replicationLinks/updateReplicationMode/action|Aggiorna la modalità di replica per il collegamento in modalità sincrona o asincrona|
|/servers/databases/restorePoints/action|Crea un nuovo punto di ripristino|
|/servers/databases/restorePoints/read|Restituisce i punti di ripristino per il database.|
|/servers/databases/resume/action|Riprende l'esecuzione del database Azure SQL Datawarehouse|
|/servers/databases/schemas/read|Recupera l'elenco degli schemi di un database|
|/servers/databases/schemas/tables/columns/read|Recupera l'elenco delle colonne di una tabella|
|/servers/databases/schemas/tables/columns/sensitivityLabels/delete|Elimina l'etichetta di riservatezza di una determinata colonna|
|/servers/databases/schemas/tables/columns/sensitivityLabels/read|Ottiene l'etichetta di riservatezza di una determinata colonna|
|/servers/databases/schemas/tables/columns/sensitivityLabels/write|Crea o aggiorna l'etichetta di riservatezza di una determinata colonna|
|/servers/databases/schemas/tables/read|Recupera l'elenco delle tabelle di un database|
|/servers/databases/schemas/tables/recommendedIndexes/read|Recupera l’elenco di raccomandazioni sull’indice su un database|
|/servers/databases/schemas/tables/recommendedIndexes/write|Applica la raccomandazione sull’indice|
|/servers/databases/securityAlertPolicies/read|Recupera i dettagli dei criteri di rilevamento minacce configurati in uno specifico database|
|/servers/databases/securityAlertPolicies/write|Modifica i criteri di rilevamento minacce per uno specifico database|
|/servers/databases/securityMetrics/read|Ottiene una raccolta della metrica di sicurezza del database|
|/servers/databases/sensitivityLabels/read|Elenca le etichette di riservatezza di un determinato database|
|/servers/databases/serviceTierAdvisors/read|Restituisce un suggerimento sulla scalabilità verticale o orizzontale del database in base alle statistiche di esecuzione query per migliorare le prestazioni o ridurre i costi|
|/servers/databases/syncGroups/cancelSync/action|Annulla la sincronizzazione del gruppo di sincronizzazione|
|/servers/databases/syncGroups/delete|Elimina un gruppo di sincronizzazione esistente.|
|/servers/databases/syncGroups/hubSchemas/read|Restituisce l'elenco degli schemi di database dell'hub di sincronizzazione|
|/servers/databases/syncGroups/logs/read|Restituisce l'elenco dei log del gruppo di sincronizzazione|
|/servers/databases/syncGroups/read|Restituisce l'elenco dei gruppi di sincronizzazione o ottiene le proprietà per il gruppo di sincronizzazione specificato.|
|/servers/databases/syncGroups/refreshHubSchema/action|Aggiorna lo schema di database dell'hub di sincronizzazione|
|/servers/databases/syncGroups/refreshHubSchemaOperationResults/read|Recupera i risultati dell'operazione di aggiornamento dello schema dell'hub di sincronizzazione|
|/servers/databases/syncGroups/syncMembers/delete|Elimina un membro di sincronizzazione esistente.|
|/servers/databases/syncGroups/syncMembers/read|Restituisce l'elenco dei membri di sincronizzazione o ottiene le proprietà per il membro di sincronizzazione specificato.|
|/servers/databases/syncGroups/syncMembers/refreshSchema/action|Aggiorna lo schema del membro di sincronizzazione|
|/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read|Recupera i risultati dell'operazione di aggiornamento dello schema del membro di sincronizzazione|
|/servers/databases/syncGroups/syncMembers/schemas/read|Restituisce l'elenco degli schemi di database del membro di sincronizzazione|
|/servers/databases/syncGroups/syncMembers/write|Crea un membro di sincronizzazione con i parametri specificati o aggiorna le proprietà per il membro di sincronizzazione specificato.|
|/servers/databases/syncGroups/triggerSync/action|Attiva la sincronizzazione del gruppo di sincronizzazione|
|/servers/databases/syncGroups/write|Crea un gruppo di sincronizzazione con i parametri specificati o aggiorna le proprietà per il gruppo di sincronizzazione specificato.|
|/servers/databases/topQueries/queryText/action|Restituisce il testo Transact-SQL per l'ID query selezionato|
|/servers/databases/topQueries/read|Restituisce le statistiche aggregate a runtime per la query selezionata nel periodo di tempo selezionato|
|/servers/databases/topQueries/statistics/read|Restituisce le statistiche aggregate a runtime per la query selezionata nel periodo di tempo selezionato|
|/servers/databases/transparentDataEncryption/operationResults/read|Ottiene le operazioni in corso sulla crittografia trasparente dei dati|
|/servers/databases/transparentDataEncryption/read|Recupera lo stato e i dettagli della funzionalità di sicurezza di Transparent Data Encryption per uno specifico database|
|/servers/databases/transparentDataEncryption/write|Cambia lo stato della crittografia trasparente dei dati|
|/servers/databases/upgradeDataWarehouse/action|Aggiorna il database Azure SQL Datawarehouse|
|/servers/databases/usages/read|Ottiene le informazioni di utilizzo del database SQL di Azure|
|/servers/databases/vulnerabilityAssessments/delete|Rimuove la valutazione delle vulnerabilità per un determinato database|
|/servers/databases/vulnerabilityAssessments/read|Recupera i dettagli della valutazione di vulnerabilità configurata in uno specifico database|
|/servers/databases/vulnerabilityAssessments/rules/baselines/delete|Rimuove la baseline della regola di valutazione delle vulnerabilità per un determinato database|
|/servers/databases/vulnerabilityAssessments/rules/baselines/read|Ottiene la baseline della regola di valutazione delle vulnerabilità per un determinato database|
|/servers/databases/vulnerabilityAssessments/rules/baselines/write|Modifica la baseline della regola di valutazione delle vulnerabilità per un determinato database|
|/servers/databases/vulnerabilityAssessments/scans/action|Esegue la scansione del database per la valutazione della vulnerabilità.|
|/servers/databases/vulnerabilityAssessments/scans/export/action|Converte un risultato di analisi esistente in un formato leggibile. Se esiste già non accade nulla|
|/servers/databases/vulnerabilityAssessments/scans/read|Restituisce l'elenco dei record dell'analisi di valutazione delle vulnerabilità del database o ottiene il record di analisi per l'ID analisi specificato.|
|/servers/databases/vulnerabilityAssessments/write|Modifica la valutazione delle vulnerabilità per un determinato database|
|/servers/databases/vulnerabilityAssessmentScans/action|Esegue la scansione del database per la valutazione della vulnerabilità.|
|/servers/databases/vulnerabilityAssessmentScans/operationResults/read|Recupera il risultato dell'operazione Execute di analisi per la valutazione della vulnerabilità del database|
|/servers/databases/vulnerabilityAssessmentSettings/read|Recupera i dettagli della valutazione di vulnerabilità configurata in uno specifico database|
|/servers/databases/vulnerabilityAssessmentSettings/write|Modifica la valutazione delle vulnerabilità per un determinato database|
|/servers/databases/write|Crea un database con i parametri specificati o aggiorna le proprietà o i tag per il database specificato.|
|/servers/delete|Elimina un server esistente.|
|/servers/disasterRecoveryConfiguration/delete|Elimina una configurazione di ripristino di emergenza esistente per un determinato server|
|/servers/disasterRecoveryConfiguration/failover/action|Esegue il failover di una configurazione di ripristino di emergenza|
|/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action|Forza il failover di una configurazione di ripristino di emergenza|
|/servers/disasterRecoveryConfiguration/read|Ottiene una raccolta configurazioni di ripristino di emergenza che includono questo server|
|/servers/disasterRecoveryConfiguration/write|Modifica la configurazione del ripristino di emergenza del server|
|/servers/elasticPoolEstimates/read|Restituisce l’elenco di stime del pool elastico già create per questo server|
|/servers/elasticPoolEstimates/write|Crea una nuova stima del pool elastico per l'elenco dei database fornito|
|/servers/elasticPools/advisors/read|Restituisce l'elenco degli advisor disponibili per il pool elastico|
|/servers/elasticPools/advisors/recommendedActions/read|Restituisce l'elenco di azioni consigliate per l’advisor specificato per il pool elastico|
|/servers/elasticPools/advisors/recommendedActions/write|Applica l'azione consigliata al pool elastico|
|/servers/elasticPools/advisors/write|Aggiorna lo stato di esecuzione automatica di un advisor a livello di pool elastico.|
|/servers/elasticPools/databases/read|Ottiene un elenco di database per un pool di database elastico|
|/servers/elasticPools/delete|Elimina un pool di database elastico esistente|
|/servers/elasticPools/elasticPoolActivity/read|Recupera attività e dettagli su un pool di database elastico|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Recupera attività e dettagli su uno specifico database che fa parte del pool di database elastico|
|/servers/elasticPools/metricDefinitions/read|Restituisce i tipi di metriche disponibili per i pool di database elastici|
|/servers/elasticPools/metrics/read|Restituisce la metrica per i pool di database elastici|
|/servers/elasticPools/operations/cancel/action|Annulla l'operazione asincrona in sospeso del pool elastico di database SQL di Azure che non è ancora stata completata.|
|/servers/elasticPools/operations/read|Restituisce l'elenco delle operazioni eseguite nel pool di database elastico|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read|Restituisce i tipi di metriche disponibili per i pool di database elastici|
|/servers/elasticPools/read|Recupera i dettagli del pool di database elastico per un determinato server|
|/servers/elasticPools/skus/read|Ottiene una raccolta di SKU disponibili per questo pool di database elastico|
|/servers/elasticPools/write|Crea nuove proprietà o modifica quelle esistenti di un pool di database elastico|
|/servers/encryptionProtector/read|Ottiene un elenco delle protezioni di crittografia dei server o ottiene le proprietà per la protezione di crittografia del server specificato.|
|/servers/encryptionProtector/write|Aggiorna le proprietà per la protezione di crittografia del server specificato.|
|/servers/extendedAuditingSettings/read|Recupera i dettagli dei criteri di controllo BLOB del server esteso configurati in uno specifico server|
|/servers/extendedAuditingSettings/write|Modifica il controllo BLOB del server esteso per uno specifico server|
|/servers/failoverGroups/delete|Elimina un gruppo di failover esistente.|
|/servers/failoverGroups/failover/action|Esegue il failover pianificato in un gruppo di failover esistente.|
|/servers/failoverGroups/forceFailoverAllowDataLoss/action|Esegue il failover forzato in un gruppo di failover esistente.|
|/servers/failoverGroups/read|Restituisce l'elenco dei gruppi di failover o ottiene le proprietà per il gruppo di failover specificato.|
|/servers/failoverGroups/write|Crea un gruppo di failover con i parametri specificati o aggiorna le proprietà o i tag per il gruppo di failover specificato.|
|/servers/firewallRules/delete|Elimina una regola firewall del server esistente.|
|/servers/firewallRules/read|Restituisce l'elenco delle regole firewall del server o ottiene le proprietà per la regola del firewall del server specificata.|
|/servers/firewallRules/write|Crea una regola firewall del server con i parametri specificati, aggiorna le proprietà per la regola specificata o sovrascrive tutte le regole esistenti con nuove regole firewall del server.|
|/servers/import/action|Crea un nuovo database sul server e distribuisce schema e dati da un pacchetto DacPac|
|/servers/importExportOperationResults/read|Ottiene le operazioni di importazione/esportazione in corso|
|/servers/keys/delete|Elimina una chiave server esistente.|
|/servers/keys/read|Restituisce l'elenco delle chiavi server o ottiene le proprietà per la chiave server specificata.|
|/servers/keys/write|Crea una chiave con i parametri specificati o aggiorna le proprietà o i tag per la chiave server specificata.|
|/servers/operationResults/read|Ottiene le operazioni del server in corso|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Restituisce i tipi di metrica disponibili per i server|
|/servers/read|Restituisce l'elenco di server o ottiene le proprietà per il server specificato.|
|/servers/recommendedElasticPools/databases/read|Recupera le metriche per i pool di database elastici consigliati per un determinato server|
|/servers/recommendedElasticPools/read|Recupera il consiglio per i pool di database elastici per ridurre i costi o migliorare le prestazioni in base all’utilizzo storico delle risorse|
|/servers/recoverableDatabases/read|Questa operazione viene utilizzata per il ripristino di emergenza di database attivi, per ripristinare il database a un punto di backup noto come valido. Restituisce informazioni sull'ultimo backup valido, ma in realtà non ripristina il database.|
|/servers/restorableDroppedDatabases/read|Ottiene un elenco di database eliminati su uno specifico server che rientrano ancora nei criteri di conservazione.|
|/servers/securityAlertPolicies/operationResults/read|Recupera i risultati dell'operazione di scrittura dei criteri di rilevamento minacce del server|
|/servers/securityAlertPolicies/read|Recupera i dettagli dei criteri di rilevamento minacce del server configurati in uno specifico server|
|/servers/securityAlertPolicies/write|Modifica i criteri di rilevamento minacce del server per uno specifico server|
|/servers/serviceObjectives/read|Recupera l'elenco degli obiettivi del livello di servizio (anche noti come livelli di prestazioni) disponibili in un determinato server|
|/servers/syncAgents/delete|Elimina un agente di sincronizzazione esistente.|
|/servers/syncAgents/generateKey/action|Genera la chiave di registrazione dell'agente di sincronizzazione|
|/servers/syncAgents/linkedDatabases/read|Restituisce l'elenco dei database collegati dell'agente di sincronizzazione|
|/servers/syncAgents/read|Restituisce l'elenco degli agenti di sincronizzazione o ottiene le proprietà per l'agente di sincronizzazione specificato.|
|/servers/syncAgents/write|Crea un agente di sincronizzazione con i parametri specificati o aggiorna le proprietà per l'agente di sincronizzazione specificato.|
|/servers/usages/read|Restituisce la quota DTU del server e il consumo di DTU corrente del server da parte di tutti i database nel server|
|/servers/virtualNetworkRules/delete|Elimina una regola di rete virtuale esistente|
|/servers/virtualNetworkRules/read|Restituisce l'elenco di regole di rete virtuale o ottiene le proprietà per la regola di rete virtuale specificata.|
|/servers/virtualNetworkRules/write|Crea una regola di rete virtuale con i parametri specificati o aggiorna le proprietà o i tag per la regola di rete virtuale specificata.|
|/servers/write|Crea un server con i parametri specificati o aggiorna le proprietà o i tag per il server specificato.|
|/unregister/action|Annulla la registrazione della sottoscrizione per il provider di risorse del database Microsoft SQL e consente la creazione di database Microsoft SQL.|
|/virtualClusters/read|Restituisce l'elenco di cluster virtuali o ottiene le proprietà per il cluster virtuale specificato.|
|/virtualClusters/write|Aggiorna i tag di cluster virtuale.|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Operazione | DESCRIZIONE |
|---|---|
|/checknameavailability/read|Controlla che il nome dell’account sia valido e non in uso.|
|/locations/deleteVirtualNetworkOrSubnets/action|Avvisa Microsoft.Storage che la rete virtuale o la subnet è in fase di eliminazione|
|/operations/read|Interroga sullo stato di un’operazione asincrona.|
|/register/action|Registra la sottoscrizione per il provider di risorse di archiviazione e consente la creazione di account di archiviazione.|
|/skus/read|Elenca gli SKU supportati da Microsoft.Storage.|
|/storageAccounts/blobServices/containers/clearLegalHold/action|Cancella il blocco a fini giudiziari del contenitore BLOB|
|/storageAccounts/blobServices/containers/delete|Restituisce il risultato dell'eliminazione di un contenitore|
|/storageAccounts/blobServices/containers/immutabilityPolicies/delete|Elimina i criteri di immutabilità del contenitore BLOB|
|/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action|Estendi i criteri di immutabilità del contenitore BLOB|
|/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action|Blocca i criteri di immutabilità del contenitore BLOB|
|/storageAccounts/blobServices/containers/immutabilityPolicies/read|Recupera i criteri di immutabilità del contenitore BLOB|
|/storageAccounts/blobServices/containers/immutabilityPolicies/write|Crea/Aggiorna i criteri di immutabilità del contenitore BLOB|
|/storageAccounts/blobServices/containers/read|Restituisce un contenitore o un elenco di contenitori|
|/storageAccounts/blobServices/containers/setLegalHold/action|Imposta il blocco a fini giudiziari del contenitore BLOB|
|/storageAccounts/blobServices/containers/write|Restituisce il risultato dell'operazione di creazione/aggiornamento (PUT) o di leasing del contenitore BLOB|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read|Recupera l'elenco delle definizioni della metrica di archiviazione Microsoft.|
|/storageAccounts/blobServices/read|Restituisce statistiche o proprietà del servizio BLOB|
|/storageAccounts/blobServices/write|Restituisce il risultato della creazione/aggiornamento delle proprietà del servizio BLOB|
|/storageAccounts/delete|Elimina un account di archiviazione esistente.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read|Recupera l'elenco delle definizioni della metrica di archiviazione Microsoft.|
|/storageAccounts/listAccountSas/action|Restituisce il token SAS dell’account per l’account di archiviazione specificato.|
|/storageAccounts/listkeys/action|Restituisce le chiavi di accesso per l'account di archiviazione specificato.|
|/storageAccounts/listServiceSas/action|Restituisce il token di firma di accesso condiviso del servizio per l'account di archiviazione specificato.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa.|
|/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read|Recupera l'elenco delle definizioni della metrica di archiviazione Microsoft.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read|Recupera l'elenco delle definizioni della metrica di archiviazione Microsoft.|
|/storageAccounts/queueServices/queues/delete|Restituisce il risultato dell'eliminazione di una coda|
|/storageAccounts/queueServices/queues/read|Restituisce una coda o un elenco di code.|
|/storageAccounts/queueServices/queues/write|Restituisce il risultato della scrittura di una coda|
|/storageAccounts/queueServices/read|Restituisce statistiche o proprietà del servizio di accodamento.|
|/storageAccounts/queueServices/write|Restituisce il risultato dell'impostazione delle proprietà del servizio di accodamento|
|/storageAccounts/read|Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato.|
|/storageAccounts/regeneratekey/action|Rigenera le chiavi di accesso per l'account di archiviazione specificato.|
|/storageAccounts/services/diagnosticSettings/write|Crea/Aggiorna le impostazioni di diagnostica dell’account di archiviazione.|
|/storageAccounts/storageAccounts/queueServices/providers/ Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read|Recupera l'elenco delle definizioni della metrica di archiviazione Microsoft.|
|/storageAccounts/write|Crea un account di archiviazione con i parametri specificati o aggiorna le proprietà o i tag o aggiunge un dominio personalizzato per l’account di archiviazione specificato.|
|/usages/read|Restituisce il limite e il conteggio corrente degli utilizzi per le risorse nella sottoscrizione specificata|

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

| Operazione | DESCRIZIONE |
|---|---|
|/storageSyncServices/delete|Elimina tutti i servizi di sincronizzazione archiviazione|
|/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per i servizi di sincronizzazione archiviazione|
|/storageSyncServices/read|Legge tutti i servizi di sincronizzazione archiviazione|
|/storageSyncServices/registeredServers/delete|Elimina tutti i server registrati|
|/storageSyncServices/registeredServers/read|Legge tutti i server registrati|
|/storageSyncServices/registeredServers/write|Crea o aggiorna tutti i server registrati|
|/storageSyncServices/syncGroups/cloudEndpoints/delete|Elimina tutti gli endpoint cloud|
|/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read|API di posizione per chiamate di backup asincrone|
|/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action|Chiama questa operazione al termine del backup|
|/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action|Chiama questa operazione al termine del ripristino|
|/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action|Chiama questa operazione prima del backup|
|/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action|Chiama questa operazione prima del ripristino|
|/storageSyncServices/syncGroups/cloudEndpoints/read|Legge tutti gli endpoint cloud|
|/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action|Ripristina l'heartbeat|
|/storageSyncServices/syncGroups/cloudEndpoints/write|Crea o aggiorna tutti gli endpoint cluster|
|/storageSyncServices/syncGroups/delete|Elimina tutti i gruppi di sincronizzazione|
|/storageSyncServices/syncGroups/read|Legge tutti i gruppi di sincronizzazione|
|/storageSyncServices/syncGroups/serverEndpoints/delete|Elimina tutti gli endpoint server|
|/storageSyncServices/syncGroups/serverEndpoints/read|Legge tutti gli endpoint server|
|/storageSyncServices/syncGroups/serverEndpoints/recallAction/action|Chiama questa azione per richiamare i file in un server|
|/storageSyncServices/syncGroups/serverEndpoints/write|Crea o aggiorna tutti gli endpoint server|
|/storageSyncServices/syncGroups/write|Crea o aggiorna tutti i gruppi di sincronizzazione|
|/storageSyncServices/write|Crea o aggiorna tutti i servizi di sincronizzare archiviazione|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Operazione | DESCRIZIONE |
|---|---|
|/managers/accessControlRecords/delete|Elimina i record di controllo di accesso|
|/managers/accessControlRecords/read|Elenca o ottiene i record di controllo di accesso|
|/managers/accessControlRecords/write|Crea o aggiorna i record di controllo di accesso|
|/managers/alerts/read|Elenca o ottiene gli avvisi|
|/managers/bandwidthSettings/delete|Elimina impostazioni di larghezza di banda esistenti (solo serie 8000)|
|/managers/bandwidthSettings/read|Elenca le impostazioni di larghezza di banda (solo serie 8000)|
|/managers/bandwidthSettings/write|Crea una nuova impostazione o aggiorna le impostazioni di larghezza di banda esistenti (solo serie 8000)|
|/Managers/certificates/write|L'operazione Aggiorna certificato risorsa aggiorna il certificato delle credenziali della risorsa o dell'insieme di credenziali.|
|/managers/clearAlerts/action|Cancella tutti gli avvisi associati a Gestione dispositivi.|
|/managers/cloudApplianceConfigurations/read|Elenca le configurazioni supportate dall'appliance cloud|
|/managers/configureDevice/action|Configura un dispositivo|
|/managers/delete|Elimina gli strumenti di gestione dei dispositivi|
|/Managers/delete|L'operazione Elimina insieme di credenziali elimina la risorsa di Azure specificata di tipo 'vault'|
|/managers/devices/alertSettings/read|Elenca o ottiene le impostazioni degli avvisi|
|/managers/devices/alertSettings/write|Crea o aggiorna le impostazioni degli avvisi|
|/managers/devices/backupPolicies/backup/action|A partire da un backup manuale crea un backup su richiesta di tutti i volumi protetti dai criteri.|
|/managers/devices/backupPolicies/delete|Elimina criteri di backup esistenti (solo serie 8000)|
|/managers/devices/backupPolicies/read|Elenca i criteri di backup (solo serie 8000)|
|/managers/devices/backupPolicies/schedules/delete|Elimina le pianificazioni esistenti|
|/managers/devices/backupPolicies/schedules/read|Elenca le pianificazioni|
|/managers/devices/backupPolicies/schedules/write|Crea pianificazioni o aggiorna pianificazioni esistenti|
|/managers/devices/backupPolicies/write|Crea nuovi criteri di backup o aggiorna quelli esistenti (solo serie 8000)|
|/managers/devices/backups/delete|Elimina il set di backup|
|/managers/devices/backups/elements/clone/action|Clona una condivisione o volume usando un elemento di backup.|
|/managers/devices/backups/read|Elenca o ottiene i set di backup|
|/managers/devices/backups/restore/action|Ripristina tutti i volumi da un set di backup.|
|/managers/devices/backupScheduleGroups/delete|Elimina i gruppi di pianificazione del backup|
|/managers/devices/backupScheduleGroups/read|Elenca o ottiene i gruppi di pianificazione del backup|
|/managers/devices/backupScheduleGroups/write|Crea o aggiorna i gruppi di pianificazione del backup|
|/managers/devices/chapSettings/delete|Elimina le impostazioni CHAP|
|/managers/devices/chapSettings/read|Elenca o ottiene le impostazioni CHAP|
|/managers/devices/chapSettings/write|Crea o aggiorna le impostazioni CHAP|
|/managers/devices/deactivate/action|Disattiva un dispositivo.|
|/managers/devices/delete|Elimina i dispositivi|
|/managers/devices/download/action|Scarica aggiornamenti per un dispositivo.|
|/managers/devices/failover/action|Failover del dispositivo.|
|/managers/devices/fileservers/backup/action|Esegue il backup di un file server.|
|/managers/devices/fileservers/delete|Elimina i file server|
|/managers/devices/fileservers/metrics/read|Elenca o ottiene le metriche|
|/managers/devices/fileservers/metricsDefinitions/read|Elenca o ottiene le definizioni di metrica|
|/managers/devices/fileservers/read|Elenca o ottiene i file server|
|/managers/devices/fileservers/shares/delete|Elimina le condivisioni|
|/managers/devices/fileservers/shares/metrics/read|Elenca o ottiene le metriche|
|/managers/devices/fileservers/shares/metricsDefinitions/read|Elenca o ottiene le definizioni di metrica|
|/managers/devices/fileservers/shares/read|Elenca o ottiene le condivisioni|
|/managers/devices/fileservers/shares/write|Crea o aggiorna le condivisioni|
|/managers/devices/fileservers/write|Crea o aggiorna i file server|
|/managers/devices/hardwareComponentGroups/changeControllerPowerState/action|Modifica lo stato di alimentazione del controller dei gruppi di componenti hardware|
|/managers/devices/hardwareComponentGroups/read|Elenca i gruppi di componenti hardware|
|/managers/devices/install/action|Installa aggiornamenti in un dispositivo.|
|/managers/devices/installUpdates/action|Installa aggiornamenti nei dispositivi|
|/managers/devices/iscsiservers/backup/action|Esegue il backup di un server iSCSI.|
|/managers/devices/iscsiservers/delete|Elimina i server iSCSI|
|/managers/devices/iscsiservers/disks/delete|Elimina i dischi|
|/managers/devices/iscsiservers/disks/metrics/read|Elenca o ottiene le metriche|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|Elenca o ottiene le definizioni di metrica|
|/managers/devices/iscsiservers/disks/read|Elenca o ottiene i dischi|
|/managers/devices/iscsiservers/disks/write|Crea o aggiorna i dischi|
|/managers/devices/iscsiservers/metrics/read|Elenca o ottiene le metriche|
|/managers/devices/iscsiservers/metricsDefinitions/read|Elenca o ottiene le definizioni di metrica|
|/managers/devices/iscsiservers/read|Elenca o ottiene i server iSCSI|
|/managers/devices/iscsiservers/write|Crea o aggiorna i server iSCSI|
|/managers/devices/jobs/cancel/action|Annulla un processo in esecuzione|
|/managers/devices/jobs/read|Elenca o ottiene i processi|
|/managers/devices/listFailoverSets/action|Elenca i set di failover per un dispositivo esistente.|
|/managers/devices/listFailoverTargets/action|Elenca le destinazioni di failover dei dispositivi|
|/managers/devices/metrics/read|Elenca o ottiene le metriche|
|/managers/devices/metricsDefinitions/read|Elenca o ottiene le definizioni di metrica|
|/managers/devices/migrationSourceConfigurations/confirmMigration/action|Conferma una migrazione completata correttamente e ne esegue il commit.|
|/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action|Recupera lo stato di conferma della migrazione.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action|Recupera lo stato del processo di stima della migrazione.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action|Recupera lo stato della migrazione.|
|/managers/devices/migrationSourceConfigurations/import/action|Importa le configurazioni di origine per la migrazione|
|/managers/devices/migrationSourceConfigurations/startMigration/action|Avvia la migrazione usando le configurazioni di origine|
|/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action|Avvia un processo per stimare la durata del processo di migrazione.|
|/managers/devices/networkSettings/read|Elenca o ottiene le impostazioni di rete|
|/managers/devices/networkSettings/write|Crea nuove impostazioni di rete o aggiorna quelle esistenti|
|/managers/devices/publicEncryptionKey/action|Elenca le chiavi di crittografia pubbliche di Gestione dispositivi|
|/managers/devices/publishSupportPackage/action|Pubblica il pacchetto per il supporto di un dispositivo per la risoluzione dei problemi da parte del supporto tecnico Microsoft.|
|/managers/devices/read|Elenca o ottiene i dispositivi|
|/managers/devices/scanForUpdates/action|Verifica la disponibilità di aggiornamenti per un dispositivo.|
|/managers/devices/securitySettings/read|Elenca le impostazioni di protezione|
|/managers/devices/securitySettings/syncRemoteManagementCertificate/action|Sincronizza il certificato di gestione remota per un dispositivo.|
|/managers/devices/securitySettings/update/action|Aggiorna le impostazioni di protezione.|
|/managers/devices/securitySettings/write|Crea nuove impostazioni di protezione o aggiorna quelle esistenti|
|/managers/devices/sendTestAlertEmail/action|Invia messaggi di posta elettronica di avviso di test ai destinatari di posta elettronica configurati.|
|/managers/devices/timeSettings/read|Elenca o ottiene le impostazioni ora|
|/managers/devices/timeSettings/write|Crea nuove impostazioni ora o aggiorna quelle esistenti|
|/managers/devices/updateSummary/read|Elenca o ottiene il riepilogo aggiornamenti|
|/managers/devices/volumeContainers/delete|Elenca un contenitore del volume esistente (solo serie 8000)|
|/managers/devices/volumeContainers/listEncryptionKeys/action|Elenca le chiavi di crittografia dei contenitori del volume|
|/managers/devices/volumeContainers/metrics/read|Elenca le metriche|
|/managers/devices/volumeContainers/metricsDefinitions/read|Elenca le definizioni di metrica|
|/managers/devices/volumeContainers/read|Elenca i contenitori del volume (solo serie 8000)|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|Sostituisce le chiavi di crittografia dei contenitori del volume|
|/managers/devices/volumeContainers/volumes/delete|Elimina i volumi esistenti|
|/managers/devices/volumeContainers/volumes/metrics/read|Elenca le metriche|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|Elenca le definizioni di metrica|
|/managers/devices/volumeContainers/volumes/read|Elenca i volumi|
|/managers/devices/volumeContainers/volumes/write|Crea nuovi volumi o aggiorna quelli esistenti|
|/managers/devices/volumeContainers/write|Crea nuovi contenitori del volume o aggiorna quelli esistenti (solo serie 8000)|
|/managers/devices/write|Crea o aggiorna i dispositivi|
|/managers/encryptionSettings/read|Elenca o ottiene le impostazioni di crittografia|
|/Managers/extendedInformation/delete|L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault?|
|/Managers/extendedInformation/read|L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault?|
|/Managers/extendedInformation/write|L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault?|
|/managers/getActivationKey/action|Ottiene la chiave di attivazione per Gestione dispositivi.|
|/managers/getEncryptionKey/action|Ottiene la chiave di crittografia per Gestione dispositivi.|
|/managers/listActivationKey/action|Ottiene la chiave di attivazione di Gestione dispositivi StorSimple.|
|/managers/listPrivateEncryptionKey/action|Ottiene la chiave di crittografia privata per uno strumento Gestione dispositivi StorSimple.|
|/managers/listPublicEncryptionKey/action|Elenca le chiavi di crittografia pubbliche di uno strumento Gestione dispositivi StorSimple.|
|/managers/metrics/read|Elenca o ottiene le metriche|
|/managers/metricsDefinitions/read|Elenca o ottiene le definizioni di metrica|
|/managers/provisionCloudAppliance/action|Crea una nuova appliance cloud.|
|/managers/read|Elenca o ottiene gli strumenti di gestione dei dispositivi|
|/Managers/read|L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault'|
|/managers/regenarateRegistationCertificate/action|Rigenera il certificato di registrazione per Gestione dispositivi.|
|/managers/regenerateActivationKey/action|Rigenera la chiave di attivazione per Gestione dispositivi.|
|/managers/storageAccountCredentials/delete|Elimina le credenziali dell'account di archiviazione|
|/managers/storageAccountCredentials/listAccessKey/action|Elenca le chiavi di accesso delle credenziali dell'account di archiviazione|
|/managers/storageAccountCredentials/read|Elenca o ottiene le credenziali dell'account di archiviazione|
|/managers/storageAccountCredentials/write|Crea o aggiorna le credenziali dell'account di archiviazione|
|/managers/storageDomains/delete|Elimina i domini di archiviazione|
|/managers/storageDomains/read|Elenca o ottiene i domini di archiviazione|
|/managers/storageDomains/write|Crea o aggiorna i domini di archiviazione|
|/managers/write|Crea o aggiorna gli strumenti di gestione dei dispositivi|
|/Managers/write|L'operazione Crea insieme di credenziali crea una risorsa di Azure di tipo 'vault'|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Operazione | DESCRIZIONE |
|---|---|
|/locations/quotas/Read|Legge la quota sottoscrizione di Analisi di flusso di Azure|
|/operations/Read|Legge le operazioni di Analisi di flusso di Azure|
|/Register/action|Registra la sottoscrizione con il provider di risorse di Analisi di flusso di Azure|
|/streamingjobs/Delete|Elimina un processo di Analisi di flusso|
|/streamingjobs/functions/Delete|Elimina la funzione del processo di Analisi di flusso di Azure|
|/streamingjobs/functions/operationresults/Read|Legge i risultati dell'operazione per la funzione di processo di Analisi di flusso di Azure|
|/streamingjobs/functions/Read|Legge la funzione del processo di Analisi di flusso di Azure|
|/streamingjobs/functions/RetrieveDefaultDefinition/action|Recupera la definizione predefinita di una funzione di processo di Analisi di flusso di Azure|
|/streamingjobs/functions/Test/action|Esegue il test della funzione del processo di Analisi di flusso di Azure|
|/streamingjobs/functions/Write|Scrive la funzione del processo di Analisi di flusso di Azure|
|/streamingjobs/inputs/Delete|Elimina un input processo di Analisi di flusso|
|/streamingjobs/inputs/operationresults/Read|Legge i risultati dell'operazione per l'input di processo di Analisi di flusso di Azure|
|/streamingjobs/inputs/Read|Legge un input processo di Analisi di flusso|
|/streamingjobs/inputs/Sample/action|Campiona l'input di processo di Analisi di flusso di Azure|
|/streamingjobs/inputs/Test/action|Esegue il test dell'input processo di Analisi di flusso di Azure|
|/streamingjobs/inputs/Write|Scrive un input processo di Analisi di flusso|
|/streamingjobs/metricdefinitions/Read|Legge le definizioni della metrica|
|/streamingjobs/operationresults/Read|Legge i risultati dell'operazione per il processo di Analisi di flusso di Azure|
|/streamingjobs/outputs/Delete|Elimina un output processo di Analisi di flusso|
|/streamingjobs/outputs/operationresults/Read|Legge i risultati dell'operazione per l'output di processo di Analisi di flusso di Azure|
|/streamingjobs/outputs/Read|Legge un output processo di Analisi di flusso|
|/streamingjobs/outputs/Test/action|Esegue il test dell'output processo di Analisi di flusso di Azure|
|/streamingjobs/outputs/Write|Scrive un output processo di Analisi di flusso|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Legge un'impostazione di diagnostica.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Scrive un'impostazione di diagnostica.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|Ottiene i registri disponibili per i processi di streaming|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per i processi di streaming|
|/streamingjobs/Read|Legge un processo di Analisi di flusso|
|/streamingjobs/Start/action|Avvia un processo di Analisi di flusso|
|/streamingjobs/Stop/action|Arresta un processo di Analisi di flusso|
|/streamingjobs/transformations/Delete|Elimina una trasformazione processo di Analisi di flusso|
|/streamingjobs/transformations/Read|Crea una trasformazione processo di Analisi di flusso|
|/streamingjobs/transformations/Write|Scrive una trasformazione processo di Analisi di flusso|
|/streamingjobs/Write|Scrive un processo di Analisi di flusso|

## <a name="microsoftsubscription"></a>Microsoft.Subscription

| Operazione | DESCRIZIONE |
|---|---|
|/SubscriptionDefinitions/read|Ottiene una definizione di sottoscrizione di Azure all'interno di un gruppo di gestione.|
|/SubscriptionDefinitions/write|Crea una definizione di sottoscrizione di Azure|

## <a name="microsoftsupport"></a>Microsoft.Support

| Operazione | DESCRIZIONE |
|---|---|
|/register/action|Esegue la registrazione al provider di risorse di supporto|
|/supportTickets/read|Ottiene i dettagli del ticket di supporto (inclusi stato, gravità, dettagli di contatto e comunicazioni) oppure ottiene l'elenco dei ticket di supporto dalle diverse sottoscrizioni.|
|/supportTickets/write|Crea o aggiorna un ticket di supporto. È possibile creare un ticket di supporto per problemi associati ai settori Tecnico, Fatturazione, Quote o Gestione della sottoscrizione. È possibile aggiornare la gravità, i dettagli di contatto e le comunicazioni dei ticket di supporto esistenti.|

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

| Operazione | DESCRIZIONE |
|---|---|
|/environments/accesspolicies/delete|Elimina i criteri di accesso.|
|/environments/accesspolicies/read|Ottiene le proprietà dei criteri di accesso.|
|/environments/accesspolicies/write|Crea nuovi criteri di accesso per un ambiente o aggiorna criteri di accesso esistenti.|
|/environments/delete|Elimina l'ambiente.|
|/environments/eventsources/delete|Elimina l'origine evento.|
|/environments/eventsources/eventsources/providers/Microsoft.Insights/ diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per le origini evento|
|/environments/eventsources/read|Ottiene le proprietà di una origine evento.|
|/environments/eventsources/write|Crea una nuova origine evento per un ambiente o aggiorna un'origine evento esistente.|
|/environments/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/environments/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/environments/providers/Microsoft.Insights/metricDefinitions/read|Ottiene la metrica disponibile per l'ambiente|
|/environments/read|Ottiene le proprietà di un ambiente.|
|/environments/referencedatasets/delete|Elimina il set di dati di riferimento.|
|/environments/referencedatasets/read|Ottiene le proprietà di un set di dati di riferimento.|
|/environments/referencedatasets/write|Crea un nuovo set di dati di riferimento per un ambiente o aggiorna un set di dati di riferimento esistente.|
|/environments/status/read|Ottiene lo stato dell'ambiente, lo stato delle operazioni associate, ad esempio i dati in ingresso.|
|/environments/write|Crea un nuovo ambiente o aggiorna un ambiente esistente.|
|/register/action|Registra la sottoscrizione per il provider di risorse Time Series Insights e consente la creazione di ambienti Time Series Insights.|

## <a name="microsoftweb"></a>microsoft.web

| Operazione | DESCRIZIONE |
|---|---|
|/apimanagementaccounts/apiacls/read|Ottiene gli ACL API dell'account di Gestione API.|
|/apimanagementaccounts/apis/apiacls/delete|Elimina le ACL API degli account di Gestione API.|
|/apimanagementaccounts/apis/apiacls/read|Ottiene gli ACL API dell'account di Gestione API.|
|/apimanagementaccounts/apis/apiacls/write|Aggiorna ACL API di account di Gestione API.|
|/apimanagementaccounts/apis/connectionacls/read|Ottiene gli ACL di connessione delle API degli account di Gestione API.|
|/apimanagementaccounts/apis/connections/confirmconsentcode/action|Conferma le connessioni API di account di Gestione API con codice di consenso.|
|/apimanagementaccounts/apis/connections/connectionacls/delete|Elimina le ACL di connessione delle connessioni API degli account di Gestione API.|
|/apimanagementaccounts/apis/connections/connectionacls/read|Ottiene gli ACL di connessione delle connessioni API degli account di Gestione API.|
|/apimanagementaccounts/apis/connections/connectionacls/write|Aggiorna ACL di connessione delle connessioni delle API degli account di Gestione API.|
|/apimanagementaccounts/apis/connections/delete|Elimina le connessioni API degli account di Gestione API.|
|/apimanagementaccounts/apis/connections/getconsentlinks/action|Ottiene collegamenti di consenso per connessioni API di account di Gestione API.|
|/apimanagementaccounts/apis/connections/listconnectionkeys/action|Elenca le connessioni API degli account di Gestione API con chiavi di connessione.|
|/apimanagementaccounts/apis/connections/listsecrets/action|Elenca le connessioni API degli account di gestione API con segreti.|
|/apimanagementaccounts/apis/connections/read|Ottiene le connessioni API degli account di Gestione API.|
|/apimanagementaccounts/apis/connections/write|Aggiorna le connessioni API degli account di Gestione API.|
|/apimanagementaccounts/apis/delete|Elimina le API degli account di Gestione API.|
|/apimanagementaccounts/apis/localizeddefinitions/delete|Elimina le definizioni localizzate delle API degli account di Gestione API.|
|/apimanagementaccounts/apis/localizeddefinitions/read|Ottiene le definizioni localizzate delle API degli account di Gestione API.|
|/apimanagementaccounts/apis/localizeddefinitions/write|Aggiorna le definizioni localizzate delle API degli account di Gestione API.|
|/apimanagementaccounts/apis/read|Ottiene le API degli account di Gestione API.|
|/apimanagementaccounts/apis/write|Aggiorna le API degli account di Gestione API.|
|/apimanagementaccounts/connectionacls/read|Ottiene gli ACL di connessione degli account di Gestione API.|
|/availablestacks/read|Ottiene gli stack disponibili.|
|/billingmeters/read|Ottiene l'elenco dei contatori di fatturazione.|
|/certificates/Delete|Elimina un certificato esistente.|
|/certificates/Read|Ottiene l'elenco dei certificati.|
|/certificates/Write|Aggiunge un nuovo certificato o ne aggiorna uno esistente.|
|/checknameavailability/read|Verifica se il nome della risorsa è disponibile.|
|/classicmobileservices/read|Ottiene servizi mobili classici.|
|/connectionGateways/Delete|Elimina un gateway di connessione.|
|/connectionGateways/Join/Action|Unisce un gateway di connessione.|
|/connectiongateways/liststatus/action|Elenca lo stato dei gateway di connessione.|
|/connectionGateways/ListStatus/Action|Elenca lo stato di un gateway di connessione.|
|/connectionGateways/Move/Action|Sposta un gateway di connessione.|
|/connectionGateways/Read|Ottiene l'elenco dei gateway di connessione.|
|/connectionGateways/Write|Crea o aggiorna un gateway di connessione.|
|/connections/confirmconsentcode/action|Conferma il codice di consenso delle connessioni.|
|/connections/Delete|Elimina una connessione.|
|/connections/Join/Action|Unisce una connessione.|
|/connections/listconsentlinks/action|Elenca i collegamenti di consenso per le connessioni.|
|/connections/Move/Action|Sposta una connessione.|
|/connections/Read|Ottiene l'elenco delle connessioni.|
|/connections/Write|Crea o aggiorna una connessione.|
|/customApis/Delete|Elimina un'API personalizzata.|
|/customApis/extractApiDefinitionFromWsdl/Action|Estrae la definizione dell'API da WSDL.|
|/customApis/Join/Action|Aggiunge un'API personalizzata.|
|/customApis/listWsdlInterfaces/Action|Elenca le interfacce WSDL per un'API personalizzata.|
|/customApis/Move/Action|Sposta un'API personalizzata.|
|/customApis/Read|Ottiene l'elenco delle API personalizzate.|
|/customApis/Write|Crea o aggiorna un'API personalizzata.|
|/deploymentlocations/read|Ottiene i percorsi di distribuzione.|
|/geoRegions/Read|Ottiene l'elenco delle aree geografiche.|
|/hostingenvironments/capacities/read|Ottiene le capacità degli ambienti di hosting.|
|/hostingEnvironments/Delete|Elimina un ambiente del servizio app|
|/hostingenvironments/diagnostics/read|Ottiene la diagnostica degli ambienti di hosting.|
|/hostingenvironments/inboundnetworkdependenciesendpoints/read|Ottiene gli endpoint di rete di tutte le dipendenze in ingresso.|
|/hostingenvironments/metricdefinitions/read|Ottiene le definizioni metrica degli ambienti di hosting.|
|/hostingenvironments/multirolepools/metricdefinitions/read|Ottiene le definizioni metrica dei pool multiruolo degli ambienti di hosting.|
|/hostingenvironments/multirolepools/metrics/read|Ottiene la metrica dei pool multiruolo degli ambienti di hosting.|
|/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/ metricDefinitions/Read|Ottiene la metrica disponibile per il pool multiruolo dell'ambiente di servizio app|
|/hostingEnvironments/multiRolePools/Read|Ottiene le proprietà di un pool front-end in un ambiente del servizio app|
|/hostingenvironments/multirolepools/skus/read|Ottiene gli SKU dei pool multiruolo degli ambienti di hosting.|
|/hostingenvironments/multirolepools/usages/read|Ottiene gli utilizzi dei pool multiruolo degli ambienti di hosting.|
|/hostingEnvironments/multiRolePools/Write|Crea un nuovo pool front-end o ne aggiorna uno esistente in un ambiente del servizio app|
|/hostingenvironments/operations/read|Ottiene le operazioni degli ambienti di hosting.|
|/hostingenvironments/outboundnetworkdependenciesendpoints/read|Ottiene gli endpoint di rete di tutte le dipendenze in uscita.|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/hostingEnvironments/Read|Ottiene le proprietà di un ambiente del servizio app|
|/hostingEnvironments/reboot/Action|Riavvia tutti i computer in un ambiente del servizio app|
|/hostingenvironments/resume/action|Ripristina l'esecuzione degli ambienti di hosting.|
|/hostingenvironments/serverfarms/read|Ottiene i piani di servizio app degli ambienti di hosting.|
|/hostingenvironments/sites/read|Ottiene le app Web degli ambienti di hosting.|
|/hostingenvironments/suspend/action|Sospende l'esecuzione degli ambienti di hosting.|
|/hostingenvironments/usages/read|Ottiene gli utilizzi degli ambienti di hosting.|
|/hostingenvironments/workerpools/metricdefinitions/read|Ottiene le definizioni metrica dei pool di lavoro degli ambienti di hosting.|
|/hostingenvironments/workerpools/metrics/read|Ottiene la metrica dei pool di lavoro degli ambienti di hosting.|
|/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read|Ottiene la metrica disponibile per il pool di lavoro dell'ambiente di servizio app|
|/hostingEnvironments/workerPools/Read|Ottiene le proprietà di un pool di lavoro in un ambiente del servizio app|
|/hostingenvironments/workerpools/skus/read|Ottiene gli SKU dei pool di lavoro degli ambienti di hosting.|
|/hostingenvironments/workerpools/usages/read|Ottiene gli utilizzi dei pool di lavoro degli ambienti di hosting.|
|/hostingEnvironments/workerPools/Write|Crea un nuovo pool di lavoro in un ambiente del servizio app o ne aggiorna uno esistente|
|/hostingEnvironments/Write|Crea un nuovo ambiente del servizio app o ne aggiorna uno esistente|
|/ishostingenvironmentnameavailable/read|Definisce se il nome dell'ambiente di hosting è disponibile.|
|/ishostnameavailable/read|Verifica se il nome host è disponibile.|
|/isusernameavailable/read|Verifica se il nome utente è disponibile.|
|/listSitesAssignedToHostName/Read|Ottiene i nomi dei siti assegnati al nome host.|
|/locations/apioperations/read|Ottiene le operazioni delle API dei percorsi.|
|/locations/connectiongatewayinstallations/read|Ottiene le installazioni dei gateway di connessione dei percorsi.|
|/locations/extractapidefinitionfromwsdl/action|Estrae la definizione dell'API da WSDL per le posizioni.|
|/locations/listwsdlinterfaces/action|Elenca le interfacce WSDL per le posizioni.|
|/locations/managedapis/apioperations/read|Ottiene le operazioni delle API gestite per le posizioni.|
|/locations/managedapis/Join/Action|Aggiunge un'API gestita.|
|/locations/managedapis/read|Ottiene le API gestite dei percorsi.|
|/operations/read|Ottiene operazioni.|
|/publishingusers/read|Ottiene gli utenti di pubblicazione.|
|/publishingusers/write|Aggiorna gli utenti di pubblicazione.|
|/recommendations/Read|Ottiene l'elenco di consigli per le sottoscrizioni.|
|/register/action|Registra il provider di risorse Microsoft.Web per la sottoscrizione.|
|/resourcehealthmetadata/read|Ottiene i metadati di Integrità risorse.|
|/serverfarms/capabilities/read|Ottiene le capacità dei piani di servizio app.|
|/serverfarms/Delete|Eliminare un piano di servizio app esistente|
|/serverfarms/firstpartyapps/settings/delete|Elimina le impostazioni delle app proprietarie dei piani di servizio app.|
|/serverfarms/firstpartyapps/settings/read|Ottiene le impostazioni delle app proprietarie dei piani di servizio app.|
|/serverfarms/firstpartyapps/settings/write|Aggiorna le impostazioni delle app proprietarie dei piani di servizio app.|
|/serverfarms/hybridconnectionnamespaces/relays/delete|Elimina gli inoltri di spazi dei nomi delle connessioni ibride per i piani di servizio app.|
|/serverfarms/hybridconnectionnamespaces/relays/read|Ottiene gli inoltri di spazi dei nomi delle connessioni ibride per i piani di servizio app.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|Ottiene le app Web degli inoltri spazio dei nomi connessione ibrida dei piani di servizio app.|
|/serverfarms/hybridconnectionplanlimits/read|Ottiene i limiti del piano di connessione ibrida per i piani di servizio app.|
|/serverfarms/hybridconnectionrelays/read|Ottiene gli inoltri connessione ibrida dei piani di servizio app.|
|/serverfarms/metricdefinitions/read|Ottiene le definizioni metrica dei piani di servizio app.|
|/serverfarms/metrics/read|Ottiene la metrica dei piani di servizio app.|
|/serverfarms/operationresults/read|Ottiene i risultati dell'operazione per i piani di servizio app.|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read|Ottiene la metrica disponibile per un piano di servizio app|
|/serverfarms/Read|Ottiene le proprietà per un piano di servizio app|
|/serverfarms/restartSites/Action|Riavvia tutte le app Web in un piano di servizio app|
|/serverfarms/sites/read|Ottiene le app Web dei piani di servizio app.|
|/serverfarms/skus/read|Ottiene gli SKU dei piani di servizio app.|
|/serverfarms/usages/read|Ottiene gli utilizzi dei piani di servizio app.|
|/serverfarms/virtualnetworkconnections/gateways/write|Aggiorna i gateway delle connessioni rete virtuale dei piani di servizio app.|
|/serverfarms/virtualnetworkconnections/read|Ottiene le connessioni rete virtuale dei piani di servizio app.|
|/serverfarms/virtualnetworkconnections/routes/delete|Elimina i percorsi delle connessioni rete virtuale dei piani di servizio app.|
|/serverfarms/virtualnetworkconnections/routes/read|Ottiene i percorsi delle connessioni rete virtuale dei piani di servizio app.|
|/serverfarms/virtualnetworkconnections/routes/write|Aggiorna i percorsi delle connessioni rete virtuale dei piani di servizio app.|
|/serverfarms/workers/reboot/action|Riavvia i ruoli di lavoro dei piani di servizio app.|
|/serverfarms/Write|Crea un nuovo piano di servizio app o ne aggiorna uno esistente|
|/sites/analyzecustomhostname/read|Analizza il nome host personalizzato.|
|/sites/applySlotConfig/Action|Applica la configurazione slot dell'app Web dallo slot di destinazione all'app Web corrente|
|/sites/backup/Action|Crea il backup di una nuova app Web|
|/sites/backup/read|Ottiene il backup delle app Web.|
|/sites/backup/write|Aggiorna il backup delle app Web.|
|/sites/backups/delete|Elimina i backup delle app Web.|
|/sites/backups/list/action|Elenca i backup delle app Web.|
|/sites/backups/Read|Ottiene le proprietà del backup di un'app Web|
|/sites/backups/restore/action|Ripristina i backup delle app Web.|
|/sites/config/delete|Elimina la configurazione delle app Web.|
|/sites/config/list/Action|Elenca le impostazioni dell'app Web sensibili alla sicurezza, ad esempio le credenziali di pubblicazione, le impostazioni dell'app e le stringhe di connessione|
|/sites/config/Read|Ottiene le impostazioni di configurazione delle app Web|
|/sites/config/Write|Aggiorna le impostazioni di configurazione dell'app Web|
|/sites/continuouswebjobs/delete|Elimina i processi Web continui delle app Web.|
|/sites/continuouswebjobs/read|Ottiene i processi Web continui delle app Web.|
|/sites/continuouswebjobs/start/action|Avvia i processi Web continui delle app Web.|
|/sites/continuouswebjobs/stop/action|Interrompe i processi Web continui delle app Web.|
|/sites/Delete|Eliminare un'app Web esistente|
|/sites/deployments/delete|Elimina le distribuzioni delle app Web.|
|/sites/deployments/log/read|Ottiene il registro delle distribuzioni delle app Web.|
|/sites/deployments/read|Ottiene le distribuzioni delle app Web.|
|/sites/deployments/write|Aggiorna le distribuzioni delle app Web.|
|/sites/diagnostics/analyses/execute/Action|Esegue l'analisi diagnostica delle app Web.|
|/sites/diagnostics/analyses/read|Ottiene l'analisi diagnostica delle app Web.|
|/sites/diagnostics/aspnetcore/read|Ottiene la diagnostica delle app Web per l'app ASP.NET Core.|
|/sites/diagnostics/autoheal/read|Ottiene la funzione AutoHeal di diagnostica delle app Web.|
|/sites/diagnostics/deployment/read|Ottiene la distribuzione di diagnostica delle app Web.|
|/sites/diagnostics/deployments/read|Ottiene le distribuzioni di diagnostica delle app Web.|
|/sites/diagnostics/detectors/execute/Action|Esegue la funzione di rilevamento di diagnostica delle app Web.|
|/sites/diagnostics/detectors/read|Ottiene la funzione di rilevamento di diagnostica delle app Web.|
|/sites/diagnostics/failedrequestsperuri/read|Ottiene le richieste non riuscite della diagnostica delle app Web per ogni URI.|
|/sites/diagnostics/frebanalysis/read|Ottiene l'analisi FREB della diagnostica delle app Web.|
|/sites/diagnostics/loganalyzer/read|Ottiene l'analizzatore dei log di diagnostica delle app Web.|
|/sites/diagnostics/read|Ottiene le categorie di diagnostica delle app Web.|
|/sites/diagnostics/runtimeavailability/read|Ottiene la disponibilità del runtime di diagnostica delle app Web.|
|/sites/diagnostics/servicehealth/read|Ottiene l'integrità di servizio della diagnostica delle app Web.|
|/sites/diagnostics/sitecpuanalysis/read|Ottiene l'analisi della CPU del sito di diagnostica delle app Web.|
|/sites/diagnostics/sitecrashes/read|Ottiene gli arresti anomali del sito di diagnostica delle app Web.|
|/sites/diagnostics/sitelatency/read|Ottiene la latenza del sito di diagnostica delle app Web.|
|/sites/diagnostics/sitememoryanalysis/read|Ottiene l'analisi della memoria del sito di diagnostica delle app Web.|
|/sites/diagnostics/siterestartsettingupdate/read|Ottiene l'aggiornamento dell'impostazione di riavvio del sito di diagnostica delle app Web.|
|/sites/diagnostics/siterestartuserinitiated/read|Ottiene il riavvio del sito di diagnostica delle app Web eseguito dall'utente.|
|/sites/diagnostics/siteswap/read|Ottiene lo scambio del sito di diagnostica delle app Web.|
|/sites/diagnostics/threadcount/read|Ottiene il conteggio dei thread del sito di diagnostica delle app Web.|
|/sites/diagnostics/workeravailability/read|Ottiene la disponibilità del processo di lavoro di diagnostica delle app Web.|
|/sites/diagnostics/workerprocessrecycle/read|Ottiene il riciclo del processo di lavoro di diagnostica delle app Web.|
|/sites/domainownershipidentifiers/read|Ottiene gli identificatori di proprietà del dominio delle app Web.|
|/sites/domainownershipidentifiers/write|Aggiorna gli identificatori di proprietà del dominio delle app Web.|
|/sites/functions/action|Funzioni delle app Web.|
|/sites/functions/delete|Elimina funzioni delle app Web.|
|/sites/functions/listsecrets/action|Elenca i segreti delle funzioni delle app Web.|
|/sites/functions/masterkey/read|Ottiene la chiave master delle funzioni delle app Web.|
|/sites/functions/read|Ottiene le funzioni delle app Web.|
|/sites/functions/token/read|Ottiene il token delle funzioni delle app Web.|
|/sites/functions/write|Aggiorna le funzioni delle app Web.|
|/sites/hostnamebindings/delete|Elimina i binding nome host delle app Web.|
|/sites/hostnamebindings/read|Ottiene i binding nome host delle app Web.|
|/sites/hostnamebindings/write|Aggiorna i binding nome host delle app Web.|
|/sites/hybridconnection/delete|Elimina la connessione ibrida delle app Web.|
|/sites/hybridconnection/read|Ottiene la connessione ibrida delle app Web.|
|/sites/hybridconnection/write|Aggiorna la connessione ibrida delle app Web.|
|/sites/hybridconnectionnamespaces/relays/delete|Elimina gli inoltri degli spazi dei nomi delle connessioni ibride delle app Web.|
|/sites/hybridconnectionnamespaces/relays/listkeys/action|Elenca le chiavi degli inoltri degli spazi dei nomi delle connessioni ibride delle app Web.|
|/sites/hybridconnectionnamespaces/relays/read|Ottiene gli inoltri degli spazi dei nomi delle connessioni ibride delle app Web.|
|/sites/hybridconnectionnamespaces/relays/write|Aggiorna gli inoltri degli spazi dei nomi delle connessioni ibride delle app Web.|
|/sites/hybridconnectionrelays/read|Ottiene i relay di connessione ibrida delle app Web.|
|/sites/instances/deployments/delete|Elimina le distribuzioni delle istanze delle app Web.|
|/sites/instances/deployments/read|Ottiene le distribuzioni delle istanze delle app Web.|
|/sites/instances/extensions/log/read|Ottiene i log delle estensioni delle istanze delle app Web.|
|/sites/instances/extensions/read|Ottiene le estensioni delle istanze delle app Web.|
|/sites/instances/processes/delete|Elimina i processi delle istanze delle app Web.|
|/sites/instances/processes/read|Ottiene i processi delle istanze delle app Web.|
|/sites/instances/read|Ottiene le istanze delle app Web.|
|/sites/listsyncfunctiontriggerstatus/action|Elenca le app Web di stato dei trigger delle funzioni.|
|/sites/metricdefinitions/read|Ottiene le definizioni di metrica per le app Web.|
|/sites/metrics/read|Ottiene la metrica di app Web.|
|/sites/metricsdefinitions/read|Ottiene le definizioni di metrica per le app Web.|
|/sites/migratemysql/action|Migra le app Web MySql.|
|/sites/migratemysql/read|Ottiene la migrazione MySql delle app Web.|
|/sites/networktrace/action|App Web di traccia della rete.|
|/sites/newpassword/action|Consente di creare una nuova password per app Web.|
|/sites/operationresults/read|Ottiene i risultati delle operazioni delle app Web.|
|/sites/operations/read|Ottiene le operazioni delle app Web.|
|/sites/perfcounters/read|Ottiene i contatori delle prestazioni delle app Web.|
|/sites/premieraddons/delete|Elimina i componenti aggiuntivi Premier delle app Web.|
|/sites/premieraddons/read|Ottiene i componenti aggiuntivi Premier delle app Web.|
|/sites/premieraddons/write|Aggiorna i componenti aggiuntivi Premier delle app Web.|
|/sites/processes/read|Ottiene i processi delle app Web.|
|/sites/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/sites/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/sites/providers/Microsoft.Insights/metricDefinitions/Read|Ottiene la metrica disponibile per l'app Web|
|/sites/publiccertificates/delete|Elimina i certificati pubblici delle app Web.|
|/sites/publiccertificates/read|Ottiene i certificati pubblici delle app Web.|
|/sites/publiccertificates/write|Aggiorna i certificati pubblici delle app Web.|
|/sites/publish/Action|Pubblica un'app Web|
|/sites/publishxml/Action|Ottiene l'XML del profilo di pubblicazione di un'app Web|
|/sites/publishxml/read|Ottiene l'XML di pubblicazione delle app Web.|
|/sites/Read|Ottiene le proprietà di un'app Web|
|/sites/recommendationhistory/read|Ottiene la cronologia consigli delle app Web.|
|/sites/recommendations/disable/action|Disattiva i consigli delle app Web.|
|/sites/recommendations/Read|Ottiene l'elenco dei consigli per l'app Web.|
|/sites/recover/action|Recupera le app Web.|
|/sites/resetSlotConfig/Action|Ripristina la configurazione dell'app Web|
|/sites/resourcehealthmetadata/read|Ottiene i metadati di Integrità risorse delle app Web.|
|/sites/restart/Action|Riavvia un'app Web|
|/sites/restore/read|Ottiene il ripristino delle app Web.|
|/sites/restore/write|Ripristina le app Web.|
|/sites/siteextensions/delete|Elimina le estensioni del sito delle app Web.|
|/sites/siteextensions/read|Ottiene le estensioni del sito delle app Web.|
|/sites/siteextensions/write|Aggiorna le estensioni del sito delle app Web.|
|/sites/slots/analyzecustomhostname/read|Ottiene l'analisi nome host personalizzato degli slot per le app Web.|
|/sites/slots/applySlotConfig/Action|Applica la configurazione slot dell'app Web dallo slot di destinazione allo slot corrente.|
|/sites/slots/backup/Action|Crea un nuovo backup di slot di app Web.|
|/sites/slots/backup/read|Ottiene il backup degli slot per le app Web.|
|/sites/slots/backup/write|Aggiorna il backup degli slot per le app Web.|
|/sites/slots/backups/delete|Elimina i backup degli slot per le app Web.|
|/sites/slots/backups/list/action|Elenca i backup degli slot per le app Web.|
|/sites/slots/backups/Read|Ottiene le proprietà del backup di uno slot delle app Web|
|/sites/slots/backups/restore/action|Ripristina i backup degli slot per le app Web.|
|/sites/slots/config/delete|Elimina la configurazione degli slot per le app Web.|
|/sites/slots/config/list/Action|Elenca le impostazioni importanti per la sicurezza degli slot per le app Web, quali le credenziali di pubblicazione, le impostazioni delle app e le stringhe di connessione|
|/sites/slots/config/Read|Ottiene le impostazioni di configurazione degli slot per le app Web|
|/sites/slots/config/Write|Aggiorna le impostazioni di configurazione degli slot per le app Web|
|/sites/slots/continuouswebjobs/delete|Elimina i processi Web continui degli slot per le app Web.|
|/sites/slots/continuouswebjobs/read|Ottiene i processi Web continui degli slot per le app Web.|
|/sites/slots/continuouswebjobs/start/action|Avvia i processi Web continui degli slot per le app Web.|
|/sites/slots/continuouswebjobs/stop/action|Arresta i processi Web continui degli slot per le app Web.|
|/sites/slots/Delete|Elimina uno slot di app Web esistente|
|/sites/slots/deployments/delete|Elimina le distribuzioni degli slot per le app Web.|
|/sites/slots/deployments/log/read|Ottiene il registro delle distribuzioni degli slot per le app Web.|
|/sites/slots/deployments/read|Ottiene le distribuzioni degli slot per le app Web.|
|/sites/slots/deployments/write|Aggiorna le distribuzioni degli slot per le app Web.|
|/sites/slots/diagnostics/analyses/execute/Action|Esegue l'analisi diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/analyses/read|Ottiene l'analisi diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/aspnetcore/read|Ottiene la diagnostica degli slot delle app Web per l'app ASP.NET Core.|
|/sites/slots/diagnostics/autoheal/read|Ottiene la funzione AutoHeal di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/deployment/read|Ottiene la distribuzione di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/deployments/read|Ottiene le distribuzioni di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/detectors/execute/Action|Esegue la funzione di rilevamento di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/detectors/read|Ottiene la funzione di rilevamento di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/frebanalysis/read|Ottiene l'analisi FREB della diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/loganalyzer/read|Ottiene l'analisi dei log di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/read|Ottiene la diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/runtimeavailability/read|Ottiene la disponibilità del runtime di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/servicehealth/read|Ottiene l'integrità di servizio di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/sitecpuanalysis/read|Ottiene l'analisi della CPU del sito di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/sitecrashes/read|Ottiene gli arresti anomali del sito di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/sitelatency/read|Ottiene la latenza del sito di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/sitememoryanalysis/read|Ottiene l'analisi della memoria del sito di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/siterestartsettingupdate/read|Ottiene l'aggiornamento dell'impostazione di riavvio del sito di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/siterestartuserinitiated/read|Ottiene il riavvio del sito di diagnostica degli slot delle app Web eseguito dall'utente.|
|/sites/slots/diagnostics/siteswap/read|Ottiene lo scambio del sito di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/threadcount/read|Ottiene il conteggio dei thread del sito di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/workeravailability/read|Ottiene la disponibilità del processo di lavoro di diagnostica degli slot delle app Web.|
|/sites/slots/diagnostics/workerprocessrecycle/read|Ottiene il riciclo del processo di lavoro di diagnostica degli slot delle app Web.|
|/sites/slots/domainownershipidentifiers/read|Ottiene gli identificatori di proprietà del dominio degli slot delle app Web.|
|/sites/slots/hostnamebindings/delete|Elimina le associazioni nome host degli slot per le app Web.|
|/sites/slots/hostnamebindings/read|Ottiene le associazioni nome host degli slot per le app Web.|
|/sites/slots/hostnamebindings/write|Aggiorna le associazioni nome host degli slot per le app Web.|
|/sites/slots/hybridconnection/delete|Elimina la connessione ibrida degli slot per le app Web.|
|/sites/slots/hybridconnection/read|Ottiene la connessione ibrida degli slot per le app Web.|
|/sites/slots/hybridconnection/write|Aggiorna la connessione ibrida degli slot per le app Web.|
|/sites/slots/hybridconnectionnamespaces/relays/delete|Elimina gli inoltri degli spazi dei nomi delle connessioni ibride degli slot delle app Web.|
|/sites/slots/hybridconnectionnamespaces/relays/write|Aggiorna gli inoltri degli spazi dei nomi delle connessioni ibride degli slot delle app Web.|
|/sites/slots/hybridconnectionrelays/read|Ottiene gli inoltri della connessione ibrida degli slot per le app Web.|
|/sites/slots/instances/deployments/read|Ottiene le distribuzioni delle istanze degli slot per le app Web.|
|/sites/slots/instances/processes/delete|Elimina i processi delle istanze degli slot per le app Web.|
|/sites/slots/instances/processes/read|Ottiene i processi delle istanze degli slot per le app Web.|
|/sites/slots/instances/read|Ottiene le istanze degli slot per le app Web.|
|/sites/slots/metricdefinitions/read|Ottiene le definizioni metrica degli slot per le app Web.|
|/sites/slots/metrics/read|Ottiene la metrica degli slot per le app Web.|
|/sites/slots/migratemysql/read|Ottiene la migrazione MySql degli slot delle app Web.|
|/sites/slots/networktrace/action|Analisi di rete degli slot delle app Web.|
|/sites/slots/newpassword/action|Consente di creare una nuova password per slot di app Web.|
|/sites/slots/operationresults/read|Ottiene i risultati delle operazioni degli slot di app Web.|
|/sites/slots/operations/read|Ottiene le operazioni degli slot delle app Web.|
|/sites/slots/perfcounters/read|Ottiene i contatori delle prestazioni degli slot delle app Web.|
|/sites/slots/phplogging/read|Ottiene la registrazione PHP degli slot per le app Web.|
|/sites/slots/premieraddons/delete|Elimina i componenti aggiuntivi Premier degli slot per le app Web.|
|/sites/slots/premieraddons/read|Ottiene i componenti aggiuntivi Premier degli slot per le app Web.|
|/sites/slots/premieraddons/write|Aggiorna i componenti aggiuntivi Premier degli slot per le app Web.|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read|Ottiene l'impostazione di diagnostica per la risorsa|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write|Crea o aggiorna l'impostazione di diagnostica per la risorsa|
|/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read|Ottiene la metrica disponibile per lo slot dell'app Web|
|/sites/slots/publiccertificates/read|Ottiene i certificati pubblici degli slot delle app Web.|
|/sites/slots/publiccertificates/write|Crea o aggiorna i certificati pubblici degli slot delle app Web.|
|/sites/slots/publish/Action|Pubblica uno slot di app Web|
|/sites/slots/publishxml/Action|Ottiene l'XML del profilo di pubblicazione per uno slot di app Web|
|/sites/slots/Read|Ottiene le proprietà di uno slot di distribuzione di app Web|
|/sites/slots/resetSlotConfig/Action|Ripristina la configurazione dello slot di app Web|
|/sites/slots/resourcehealthmetadata/read|Ottiene i metadati di Integrità risorse degli slot delle app Web.|
|/sites/slots/restart/Action|Riavvia uno slot di app Web|
|/sites/slots/restore/read|Ottiene il ripristino degli slot per le app Web.|
|/sites/slots/restore/write|Ripristina gli slot per le app Web.|
|/sites/slots/siteextensions/delete|Elimina le estensioni del sito degli slot delle app Web.|
|/sites/slots/siteextensions/read|Ottiene le estensioni del sito degli slot delle app Web.|
|/sites/slots/siteextensions/write|Aggiorna le estensioni del sito degli slot delle app Web.|
|/sites/slots/slotsdiffs/Action|Ottiene le differenze di configurazione tra l'app Web e gli slot|
|/sites/slots/slotsswap/Action|Scambia gli slot di distribuzione di un'app Web|
|/sites/slots/snapshots/read|Ottiene gli snapshot degli slot delle app Web.|
|/sites/slots/sourcecontrols/Delete|Elimina le impostazioni di configurazione del controllo codice sorgente degli slot per le app Web|
|/sites/slots/sourcecontrols/Read|Ottiene le impostazioni di configurazione del controllo codice sorgente degli slot per le app Web|
|/sites/slots/sourcecontrols/Write|Aggiorna le impostazioni di configurazione del controllo codice sorgente degli slot per le app Web|
|/sites/slots/start/Action|Avvia uno slot di app Web|
|/sites/slots/stop/Action|Arresta uno slot di app Web|
|/sites/slots/sync/action|Sincronizza gli slot di app Web.|
|/sites/slots/triggeredwebjobs/delete|Elimina i processi Web attivati degli slot per le app Web.|
|/sites/slots/triggeredwebjobs/read|Ottiene i processi Web attivati degli slot per le app Web.|
|/sites/slots/triggeredwebjobs/run/action|Esegue i processi Web attivati degli slot per le app Web.|
|/sites/slots/usages/read|Ottiene gli utilizzi degli slot per le app Web.|
|/sites/slots/virtualnetworkconnections/delete|Elimina le connessioni rete virtuale degli slot per le app Web.|
|/sites/slots/virtualnetworkconnections/gateways/write|Aggiorna i gateway delle connessioni rete virtuale degli slot per le app Web.|
|/sites/slots/virtualnetworkconnections/read|Ottiene le connessioni rete virtuale degli slot per le app Web.|
|/sites/slots/virtualnetworkconnections/write|Aggiorna le connessioni rete virtuale degli slot per le app Web.|
|/sites/slots/webjobs/read|Ottiene i processi Web degli slot per le app Web.|
|/sites/slots/Write|Crea un nuovo slot di app Web o ne aggiorna uno esistente|
|/sites/slotsdiffs/Action|Ottiene le differenze di configurazione tra l'app Web e gli slot|
|/sites/slotsswap/Action|Scambia gli slot di distribuzione di un'app Web|
|/sites/snapshots/read|Ottiene snapshot delle app Web.|
|/sites/sourcecontrols/Delete|Elimina le impostazioni di configurazione del controllo codice sorgente dell'app Web|
|/sites/sourcecontrols/Read|Ottiene le impostazioni di configurazione del controllo codice sorgente dell'app Web|
|/sites/sourcecontrols/Write|Aggiorna le impostazioni di configurazione del controllo codice sorgente dell'app Web|
|/sites/start/Action|Avvia un'app Web|
|/sites/stop/Action|Arresta un'app Web|
|/sites/sync/action|App Web di sincronizzazione.|
|/sites/syncfunctiontriggers/action|Sincronizza i trigger di funzione per le app Web.|
|/sites/triggeredwebjobs/delete|Elimina i processi Web attivati delle app Web.|
|/sites/triggeredwebjobs/history/read|Ottiene la cronologia dei processi Web attivati delle app Web.|
|/sites/triggeredwebjobs/read|Ottiene i processi Web attivati delle app Web.|
|/sites/triggeredwebjobs/run/action|Esegue i processi Web attivati delle app Web.|
|/sites/usages/read|Ottiene gli utilizzi delle app Web.|
|/sites/virtualnetworkconnections/delete|Elimina le connessioni di rete virtuale delle app Web.|
|/sites/virtualnetworkconnections/gateways/read|Ottiene i gateway delle connessioni di rete virtuale delle app Web.|
|/sites/virtualnetworkconnections/gateways/write|Aggiorna i gateway delle connessioni di rete virtuale delle app Web.|
|/sites/virtualnetworkconnections/read|Ottiene le connessioni di rete virtuale delle app Web.|
|/sites/virtualnetworkconnections/write|Aggiorna le connessioni di rete virtuale delle app Web.|
|/sites/webjobs/read|Ottiene i processi Web delle app Web.|
|/sites/Write|Crea una nuova app Web o ne aggiorna una esistente|
|/skus/read|Ottiene SKU.|
|/sourcecontrols/read|Ottiene i controlli del codice sorgente.|
|/sourcecontrols/write|Aggiorna i controlli del codice sorgente.|
|/unregister/action|Annulla la registrazione del provider di risorse Microsoft.Web per la sottoscrizione.|
|/validate/action|Convalida il provider di risorse Microsoft.Web per la sottoscrizione.|
|/verifyhostingenvironmentvnet/action|Verifica la rete virtuale dell'ambiente di hosting.|

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

| Operazione | DESCRIZIONE |
|---|---|
|/components/read|Legge risorse delle operazioni|
|/healthInstances/read|Legge risorse delle operazioni|
|/Operations/read|Legge risorse delle operazioni|
|/workloads/delete|Elimina una risorsa del carico di lavoro|
|/workloads/read|Legge una risorsa del carico di lavoro|
|/workloads/write|Scrive una risorsa del carico di lavoro|

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un ruolo personalizzato](role-based-access-control-custom-roles.md).
- [Ruoli RBAC incorporati](role-based-access-built-in-roles.md).
- Informazioni sulla gestione delle assegnazioni di accesso [per utente](role-based-access-control-manage-assignments.md) o [per risorsa](role-based-access-control-configure.md) 
- Informazioni su come [visualizzare i log attività per controllare le azioni sulle risorse](~/articles/azure-resource-manager/resource-group-audit.md)
