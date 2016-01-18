<properties
	pageTitle="RBAC: Ruoli predefiniti | Microsoft Azure"
	description="In questo argomento vengono descritti i ruoli predefiniti per il controllo di accesso basato sui ruoli (RBAC)."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="01/04/2016"
	ms.author="inhenk"/>

#RBAC: Ruoli predefiniti

## Ruoli predefiniti

Il controllo degli accessi in base al ruolo di Azure presenta i seguenti ruoli predefiniti che possono essere assegnati a utenti, gruppi e servizi. I ruoli predefiniti non possono essere modificati. In una futura release di Azure RBAC, sarà possibile definire ruoli personalizzati componendo un set di azioni da un elenco di azioni disponibili che possono essere effettuate sulle risorse di Azure.

Fare clic sul collegamento corrispondente per visualizzare le proprietà **actions** e **not actions** di una definizione del ruolo. La proprietà **actions** specifica le azioni consentite sulle risorse di Azure. Nelle stringhe delle azioni è possibile usare caratteri jolly. La proprietà **not actions** di una definizione del ruolo specifica le azioni che devono essere escluse dalle azioni consentite.


| Nome del ruolo | Descrizione |
| --------- | ----------- |
| [Collaboratore servizio Gestione API](#api-management-service-contributor) | È in grado di gestire i servizi Gestione API |
| [Collaboratore componente di Application Insights](#application-insights-component-contributor) | È in grado di gestire i componenti di Application Insights |
| [Operatore di automazione](#automation-operator) | È in grado di avviare, arrestare, sospendere e riprendere i processi |
| [Collaboratore BizTalk](#biztalk-contributor) | È in grado di gestire i servizi BizTalk |
| [Collaboratore database ClearDB MySQL](#cleardb-mysql-db-contributor) | È in grado di gestire i database ClearDB MySQL |
| [Collaboratore](#contributor) | È in grado di gestire tutto ad eccezione degli accessi. |
| [Collaboratore Data Factory](#data-factory-contributor) | È in grado gestire le Data factory |
| [Utente DevTest Lab](#devtest-lab-user) | È in grado di visualizzare tutti gli elementi e connettere, avviare, riavviare e arrestare macchine virtuali. |
| [Collaboratore account Document DB](#document-db-account-contributor) | È in grado di gestire account Document DB |
| [Collaboratore account Intelligent Systems](#intelligent-systems-account-contributor) | È in grado di gestire account Intelligent Systems |
| [Collaboratore di rete](#network-contributor) | È in grado di gestire tutte le risorse di rete |
| [Collaboratore account NewRelic APM](#newrelic-apm-account-contributor) | È in grado di gestire account e applicazioni di NewRelic Application Performance Management |
| [Proprietario](#owner) | Il proprietario può gestire tutto, compresi gli accessi. |
| [Lettore](#reader) | I lettori possono visualizzare tutto, ma non possono apportare modifiche. |
| [Collaboratore cache Redis](#redis-cache-contributor]) | È in grado di gestire le cache Redis |
| [Collaboratore raccolte di processi dell'unità di pianificazione](#scheduler-job-collections-contributor) | È in grado di gestire raccolte di processi dell'utilità di pianificazione |
| [Collaboratore servizi di ricerca](#search-service-contributor) | È in grado di gestire servizi di ricerca |
| [Gestore della sicurezza SQL](#security-manager) | È in grado di gestire i componenti di protezione, criteri di sicurezza e le macchine virtuali |
| [Collaboratore database SQL](#sql-db-contributor) | È in grado di gestire database SQL, ma non i criteri di protezione correlati |
| [Gestione della sicurezza SQL](#sql-security-manager) | È in grado di gestire i criteri di protezione correlati di server e database SQL |
| [Collaboratore SQL Server](#sql-server-contributor) | È in grado di gestire server e database SQL, ma non i criteri di protezione correlati |
| [Collaboratore account di archiviazione classico](#classic-storage-account-contributor) | È in grado di gestire gli account di archiviazione classici |
| [Collaboratore account di archiviazione](#storage-account-contributor) | È in grado di gestire gli account di archiviazione |
| [Amministratore accessi utente](#user-access-administrator) | Consente di gestire l’accesso degli utenti alle risorse di Azure |
| [Collaboratore macchine virtuali classiche](#classic-virtual-machine-contributor) | È in grado di gestire macchine virtuali classiche, ma non la rete virtuale o gli account di archiviazione a cui sono connesse |
| [Collaboratore macchine virtuali](#virtual-machine-contributor) | È in grado di gestire macchine virtuali, ma non la rete virtuale o account di archiviazione a cui sono connesse |
| [Collaboratore reti virtuali classiche](#classic-network-contributor) | È in grado di gestire reti virtuali classiche e IP riservati |
| [Collaboratore piani Web](#web-plan-contributor) | È in grado di gestire piani Web |
| [Collaboratore siti Web](#website-contributor) | È in grado di gestire siti Web, ma non i piani cui sono connessi |

### Collaboratore servizio Gestione API
È in grado di gestire i servizi Gestione API

| **Actions** | |
| ------- | ------ |
| Microsoft.ApiManagement/Services/* | È in grado di creare e gestire i servizi Gestione API |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore componente Application Insights
È in grado di gestire i componenti di Application Insights

| **Actions** | |
| ------- | ------ |
| Microsoft.Insights/components/* | È in grado di creare e gestire i componenti di Insights |
| Microsoft.Insights/webtests/* | È in grado di creare e gestire i test Web |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Operatore di automazione
Gli operatori di automazione sono in grado di avviare, arrestare, sospendere e riprendere i processi

| **Actions** ||
| ------- | ------ |
| Microsoft.Automation/automationAccounts/read | Leggere account di automazione |
| Microsoft.Automation/automationAccounts/runbooks/read | Leggere runbook di automazione |
| Microsoft.Automation/automationAccounts/schedules/read | Leggere pianificazioni di account di automazione |
| Microsoft.Automation/automationAccounts/schedules/write | Scrivere pianificazioni di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/read | Leggere processi di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/write | Scrivere processi di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Arrestare un processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Sopendere un processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Riprendere un processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Leggere una pianificazione di processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Leggere una pianificazione di processo di account di automazione |

### Collaboratore BizTalk
È in grado di gestire i servizi BizTalk

| **Actions** ||
| ------- | ------ |
| Microsoft.BizTalkServices/BizTalk/* | È in grado di creare e gestire i servizi BizTalk |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere le risorse di un gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore database ClearDB MySQL
È in grado di gestire i database ClearDB MySQL

| **Actions** ||
| ------- | ------ |
| successbricks.cleardb/databases/* | È in grado di creare e gestire i database ClearDB MySQL |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere le risorse di un gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore
I collaboratori possono gestire tutto ad eccezione degli accessi.

| **Actions** ||
| ------- | ------ |
| * | È in grado di creare e gestire ogni tipo di risorsa |
| ****Non azioni |  |
| Microsoft.Authorization/*/Write | Impossibile creare ruoli e assegnazioni di ruoli |
| Microsoft.Authorization/*/Delete | Impossibile eliminare ruoli e assegnazioni di ruoli |

### Collaboratore Data Factory
È in grado gestire le Data factory

| **Actions** ||
| ------- | ------ |
| Microsoft.DataFactory/dataFactories/* | Creare e gestire data factory |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere le risorse di un gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Utente DevTest Lab
È in grado di visualizzare tutti gli elementi e connettere, avviare, riavviare e arrestare macchine virtuali

| **Actions** ||
| ------- | ------ |
| */read | Leggere le risorse di tutti i tipi | | Microsoft.DevTestLab/labs/labStats/action | Leggere statistiche lab | | Microsoft.DevTestLab/Environments/* | Creare e gestire ambienti |
| Microsoft.DevTestLab/labs/createEnvironment/action | Creare un ambiente lab |
| Microsoft.Compute/virtualMachines/start/action | Avviare macchine virtuali |
| Microsoft.Compute/virtualMachines/restart/action | Ottenere macchine virtuali |
| Microsoft.Compute/virtualMachines/deallocate/action | Deallocare macchine virtuali |
| Microsoft.Storage/storageAccounts/listKeys/action | Ottenere chiavi degli account di archiviazione |
| Microsoft.Network/virtualNetworks/join/action | Partecipare a reti virtuali |
| Microsoft.Network/loadBalancers/join/action | Partecipare a servizi di bilanciamento del carico |
| Microsoft.Network/publicIPAddresses/link/action | Collegare a indirizzi IP publisc |
| Microsoft.Network/networkInterfaces/link/action | Collegare a interfacce di rete |
| Microsoft.Network/networkInterfaces/write | Scrivere interfacce di rete |

### Collaboratore account Document DB
È in grado di gestire account Document DB

| **Actions** ||
| ------- | ------ |
| Microsoft.DocumentDb/databaseAccounts/* | Creare e gestire account DocumentDB |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere le risorse di un gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore account Intelligent Systems
È in grado di gestire account Intelligent Systems

| **Actions** ||
| ------- | ------ |
| Microsoft.IntelligentSystems/accounts/* | Creare e gestire account di Intelligent Systems |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere le risorse di un gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore di rete
È in grado di gestire tutte le risorse di rete

| **Actions** ||
| ------- | ------ |
| Microsoft.Network/* | Creare e gestire reti |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere le risorse di un gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore account NewRelic APM
È in grado di gestire account e applicazioni di NewRelic Application Performance Management

| **Actions** ||
| ------- | ------ |
| NewRelic.APM/accounts/* | Creare e gestire account di gestione delle prestazioni delle applicazioni NewRelic |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere le risorse di un gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Proprietario
È in grado di gestire tutti gli elementi, compresi gli accessi

| **Actions** ||
| ------- | ------ |
| * | È in grado di creare e gestire ogni tipo di risorsa |

### Reader
È in grado di visualizzare tutti gli elementi, ma non può apportare modifiche

| **Actions** ||
| ------- | ------ |
| **/read | Leggere risorse di tutti i tipi, eccetto i segreti. |

### Collaboratore cache Redis
È in grado di gestire le cache Redis

| **Actions** ||
| ------- | ------ |
| Microsoft.Cache/redis/* | Creare e gestire cache Redis |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere le risorse di un gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore raccolte di processi dell'unità di pianificazione
È in grado di gestire raccolte di processi dell'utilità di pianificazione

| **Actions** ||
| ------- | ------ |
| Microsoft.Scheduler/jobcollections/* | Creare e gestire raccolte di processi |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere le risorse di un gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore servizi di ricerca
È in grado di gestire servizi di ricerca

| **Actions** ||
| ------- | ------ |
| Microsoft.Search/searchServices/* | È in grado di creare e gestire servizi di ricerca |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere le risorse di un gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Gestore della sicurezza
È in grado di gestire i componenti di protezione, criteri di sicurezza e le macchine virtuali

| **Actions** ||
| ------- | ------ |
| Microsoft.ClassicNetwork/*/read | Leggere le informazioni della configurazione sulla rete classica |
| Microsoft.ClassicCompute/*/read | Leggere le informazioni di configurazione delle macchine virtuali di calcolo classiche |
| Microsoft.ClassicCompute/virtualMachines/*/write | Scrivere la configurazione delle macchine virtuali |
| Microsoft.Security/* | Creare e gestire criteri e componenti di protezione |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere le risorse di un gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore database SQL
È in grado di gestire database SQL, ma non i criteri di protezione correlati

| **Actions** ||
| ------- | ------ |
| Microsoft.Sql/servers/read | È in grado di leggere i server SQL |
| Microsoft.Sql/servers/databases/* | Creare e gestire database SQL |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere le risorse di un gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Support/* | Creare e gestire ticket di supporto |
| **Non azioni** | |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Impossibile modificare i criteri di controllo |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Impossibile modificare i criteri di connessione |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Impossibile modificare i criteri di mascheratura dei dati |
| Microsoft.Sql/servers/databases/securityMetrics/* | Impossibile modificare i criteri di protezione |

### Gestione della sicurezza SQL
È in grado di gestire i criteri di protezione correlati di server e database SQL

| **Actions** ||
| ------- | ------ |
| Microsoft.Sql/servers/read | È in grado di leggere i server SQL |
| Microsoft.Sql/servers/auditingPolicies/* | Creare e gestire criteri di controllo di server SQL |
| Microsoft.Sql/servers/databases/read | Leggere database SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Creare e gestire i criteri di controllo dei database SQL |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Creare e gestire i criteri di connessione dei database dei server SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Creare e gestire i criteri della maschera dei dati dei database dei server SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Creare e gestire le metriche di sicurezza dei database di server SQL |
| Microsoft.Authorization/*/read | Leggere l’autorizzazione Microsoft |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere le risorse del gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni dei gruppo di risorse delle sottoscrizioni |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Support/* | Creare e gestire ticket di supporto |
| Microsoft.Sql/servers/databases/schemas/read | Leggere schemi di database di lettura di server SQL |
| Microsoft.Sql/servers/databases/schemas/tables/read | Leggere tabelle di database di server SQL |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Leggere colonne della tabella del database di server SQL |

### Collaboratore SQL Server
È in grado di gestire server e database SQL, ma non i criteri di protezione correlati

| **Actions** ||
| ------- | ------ |
| Microsoft.Sql/servers/* | Creare e gestire server SQL |
| Microsoft.Authorization/*/read | Autorizzazione Lettura|
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere le risorse del gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni dei gruppo di risorse delle sottoscrizioni |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Support/* | Creare e gestire ticket di supporto |
| **Non azioni** | |
| Microsoft.Sql/servers/auditingPolicies/* | Non è in grado di modificare i criteri di controllo di server SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Non è in grado di modificare i criteri di controllo dei database di server SQL |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Non è in grado di modificare i criteri di connessione dei database di server SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Non è in grado di modificare i criteri di mascheratura dei dati dei database di server SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Non è in grado di modificare le metriche di protezione dei database di server SQL |

### Collaboratore account di archiviazione classico
È in grado di gestire gli account di archiviazione classici

| **Actions** ||
| ------- | ------ |
| Microsoft.ClassicStorage/storageAccounts/* | Creare e gestire account di archiviazione |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.Resources/subscriptions/resources/read | È in grado di leggere le risorse di sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere risorse dei gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni del gruppo di risorse della sottoscrizione |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore account di archiviazione
È in grado di gestire gli account di archiviazione

| **Actions** ||
| ------- | ------ |
| Microsoft.Storage/storageAccounts/* | Creare e gestire account di archiviazione |
| Microsoft.Authorization/*/read | Leggere tutte le autorizzazioni |
| Microsoft.Resources/subscriptions/resources/read | È in grado di leggere le risorse di sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere risorse dei gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni del gruppo di risorse della sottoscrizione |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Amministratore accessi utente
Consente di gestire l’accesso degli utenti alle risorse di Azure

| **Actions** ||
| ------- | ------ |
| */read | Leggere risorse di tutti i tipi, eccetto i segreti. |
| Microsoft.Authorization/* | Autorizzazione Lettura |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore macchine virtuali classiche
È in grado di gestire macchine virtuali classiche, ma non la rete virtuale o gli account di archiviazione a cui sono connesse

| **Actions** ||
| ------- | ------ |
| Microsoft.ClassicStorage/storageAccounts/read | Leggere account di archiviazione classici |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Ottenere chiavi degli account di archiviazione |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Leggere dischi di account di archiviazione |
| Microsoft.ClassicStorage/storageAccounts/images/read | Leggere immagini di account di archiviazione |
| Microsoft.ClassicNetwork/virtualNetworks/read | Leggere reti virtuali |
| Microsoft.ClassicNetwork/reservedIps/read | Leggere indirizzi IP riservati |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Partecipare a reti virtuali |
| Microsoft.ClassicNetwork/reservedIps/link/action | Collegare IP riservati |
| Microsoft.ClassicCompute/domainNames/* | Creare e gestire nomi di dominio di calcolo classici |
| Microsoft.ClassicCompute/virtualMachines/* | Creare e gestire macchine virtuali |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere risorse dei gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni del gruppo di risorse della sottoscrizione |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore macchine virtuali
È in grado di gestire macchine virtuali, ma non la rete virtuale o account di archiviazione a cui sono connesse

| **Actions** ||
| ------- | ------ |
| Microsoft.Storage/storageAccounts/read | Leggere account di archiviazione |
| Microsoft.Storage/storageAccounts/listKeys/action | Ottenere chiavi degli account di archiviazione |
| Microsoft.Network/virtualNetworks/read | Leggere reti virtuali |
| Microsoft.Network/virtualNetworks/subnets/join/action | Partecipare a subnet di reti virtuali |
| Microsoft.Network/loadBalancers/read | Leggere servizi di bilanciamento del carico |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Partecipare a pool di indirizzi backend di servizi di bilanciamento del carico |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Partecipare a regole NAT in entrata di servizi di bilanciamento del carico |
| Microsoft.Network/publicIPAddresses/read | Leggere indirizzi IP pubblici di rete |
| Microsoft.Network/publicIPAddresses/join/action | Partecipare a indirizzi IP pubblici di rete |
| Microsoft.Network/networkSecurityGroups/read | Leggere gruppi di sicurezza di rete |
| Microsoft.Network/networkSecurityGroups/join/action | Partecipare a gruppi di sicurezza di rete |
| Microsoft.Network/networkInterfaces/* | Creare e gestire interfacce di rete |
| Microsoft.Network/locations/* | Creare e gestire percorsi di rete |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Partecipare a pool di indirizzi backend di gateway delle applicazioni di rete |
| Microsoft.Compute/virtualMachines/* | Creare e gestire macchine virtuali |
| Microsoft.Compute/availabilitySets/* | Creare e gestire set di disponibilità di calcolo |
| Microsoft.Compute/locations/* | Creare e gestire percorsi di calcolo |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere risorse dei gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni del gruppo di risorse della sottoscrizione |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore reti classiche
È in grado di gestire reti virtuali classiche e IP riservati

| **Actions** ||
| ------- | ------ |
| Microsoft.ClassicNetwork/* | Creare e gestire reti classiche |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere risorse dei gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni del gruppo di risorse della sottoscrizione |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore piani Web
È in grado di gestire piani Web

| **Actions** ||
| ------- | ------ |
| Microsoft.Web/serverFarms/* | Creare e gestire server farm |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere risorse dei gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni del gruppo di risorse della sottoscrizione |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

### Collaboratore siti Web
È in grado di gestire siti Web, ma non i piani cui sono connessi

| **Actions** ||
| ------- | ------ |
| Microsoft.Web/serverFarms/read | Leggere server farm |
| Microsoft.Web/serverFarms/join/action | Partecipare a server farm |
| Microsoft.Web/sites/* | Creare e gestire siti Web |
| Microsoft.Web/certificates/* | Creare e gestire certificati dei siti Web |
| Microsoft.Web/listSitesAssignedToHostName/read | Leggere siti di lettura assegnati a un nome host |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leggere risorse dei gruppi di risorse della sottoscrizione |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Creare e gestire distribuzioni del gruppo di risorse della sottoscrizione |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Support/* | Creare e gestire ticket di supporto |
| Microsoft.Insights/components/* | È in grado di creare e gestire i componenti di Insights |

## Argomenti RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0107_2016-->