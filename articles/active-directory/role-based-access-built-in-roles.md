---
title: 'Controllo degli accessi in base al ruolo: ruoli predefiniti | Microsoft Docs'
description: Questo argomento descrive i ruoli predefiniti per il controllo degli accessi in base al ruolo.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/25/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 386e8479a64af20469e3e03180348f674b08ef8f


---
# <a name="rbac-built-in-roles"></a>Controllo degli accessi in base al ruolo: ruoli predefiniti
Il controllo degli accessi in base al ruolo di Azure presenta i seguenti ruoli predefiniti che possono essere assegnati a utenti, gruppi e servizi. Non è possibile modificare le definizioni dei ruoli predefiniti. Si possono tuttavia creare [ruoli personalizzati nel controllo degli accessi in base al ruolo di Azure](role-based-access-control-custom-roles.md) per soddisfare le esigenze specifiche dell'organizzazione.

## <a name="roles-in-azure"></a>Ruoli in Azure
La tabella seguente contiene descrizioni brevi dei ruoli predefiniti. Fare clic sul nome del ruolo per visualizzare un elenco dettagliato delle proprietà **actions** e **notactions** per il ruolo. La proprietà **actions** specifica le azioni consentite sulle risorse di Azure. Nelle stringhe delle azioni è possibile utilizzare caratteri jolly. La proprietà **notactions** specifica le azioni non consentite.

> [!NOTE]
> Le definizioni dei ruoli di Azure sono in continua evoluzione. Questo articolo viene aggiornato il più possibile, ma le definizioni dei ruoli più recenti sono sempre disponibili in Azure PowerShell. Usare i cmdlet `(get-azurermroledefinition "<role name>").actions` o `(get-azurermroledefinition "<role name>").notactions` applicabili.
> 
> 

| Nome del ruolo | Descrizione |
| --- | --- |
| [Collaboratore servizio Gestione API](#api-management-service-contributor) |È in grado di gestire i servizi Gestione API |
| [Collaboratore componente di Application Insights](#application-insights-component-contributor) |È in grado di gestire i componenti di Application Insights |
| [Operatore di automazione](#automation-operator) |È in grado di avviare, arrestare, sospendere e riprendere i processi |
| [Collaboratore BizTalk](#biztalk-contributor) |È in grado di gestire i servizi BizTalk |
| [Collaboratore database ClearDB MySQL](#cleardb-mysql-db-contributor) |È in grado di gestire i database ClearDB MySQL |
| [Collaboratore](#contributor) |È in grado di gestire tutto ad eccezione degli accessi. |
| [Collaboratore Data Factory](#data-factory-contributor) |Può creare e gestire data factory e le relative risorse figlio. |
| [Utente DevTest Labs](#devtest-labs-user) |Può visualizzare tutti gli elementi e connettere, avviare, riavviare e arrestare macchine virtuali |
| [Collaboratore zona DNS](#dns-zone-contributor) |È in grado di gestire zone e record DNS |
| [Collaboratore account DocumentDB](#documentdb-account-contributor) |Può gestire gli account DocumentDB |
| [Collaboratore account Intelligent Systems](#intelligent-systems-account-contributor) |È in grado di gestire account Intelligent Systems |
| [Collaboratore di rete](#network-contributor) |È in grado di gestire tutte le risorse di rete |
| [Collaboratore account New Relic APM](#new-relic-apm-account-contributor) |Può gestire account e applicazioni di New Relic Application Performance Management |
| [Proprietario](#owner) |È in grado di gestire tutti gli elementi, compresi gli accessi |
| [Lettore](#reader) |È in grado di visualizzare tutti gli elementi, ma non può apportare modifiche |
| [Collaboratore cache Redis](#redis-cache-contributor) |È in grado di gestire le cache Redis |
| [Collaboratore raccolte di processi dell'unità di pianificazione](#scheduler-job-collections-contributor) |È in grado di gestire raccolte di processi dell'utilità di pianificazione |
| [Collaboratore servizi di ricerca](#search-service-contributor) |È in grado di gestire servizi di ricerca |
| [Gestore della sicurezza SQL](#security-manager) |Può gestire i componenti di protezione, i criteri di sicurezza e le macchine virtuali |
| [Collaboratore database SQL](#sql-db-contributor) |Può gestire i database SQL, ma non i criteri correlati alla sicurezza |
| [Gestione della sicurezza SQL](#sql-security-manager) |Può gestire i criteri correlati alla sicurezza di SQL Server e database SQL |
| [Collaboratore SQL Server](#sql-server-contributor) |È in grado di gestire server e database SQL, ma non i criteri di sicurezza correlati |
| [Collaboratore account di archiviazione classico](#classic-storage-account-contributor) |È in grado di gestire gli account di archiviazione classici |
| [Collaboratore account di archiviazione](#storage-account-contributor) |È in grado di gestire gli account di archiviazione |
| [Amministratore accessi utente](#user-access-administrator) |Consente di gestire l’accesso degli utenti alle risorse di Azure |
| [Collaboratore macchine virtuali classiche](#classic-virtual-machine-contributor) |È in grado di gestire macchine virtuali classiche, ma non la rete virtuale o l'account di archiviazione a cui sono connesse |
| [Collaboratore macchine virtuali](#virtual-machine-contributor) |È in grado di gestire macchine virtuali, ma non la rete virtuale o l'account di archiviazione a cui sono connesse |
| [Collaboratore reti virtuali classiche](#classic-network-contributor) |È in grado di gestire reti virtuali classiche e IP riservati |
| [Collaboratore piani Web](#web-plan-contributor) |È in grado di gestire piani Web |
| [Collaboratore siti Web](#website-contributor) |È in grado di gestire siti Web, ma non i piani Web a cui sono connessi |

## <a name="role-permissions"></a>Autorizzazioni ruoli
Nelle tabelle seguenti vengono descritte le autorizzazioni specifiche assegnate a ogni ruolo. Può trattarsi di proprietà **Actions**, che concedono le autorizzazioni, e **NotActions**, che le limitano.

### <a name="api-management-service-contributor"></a>Collaboratore servizio Gestione API
È in grado di gestire i servizi Gestione API

| **Actions** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/* |È in grado di creare e gestire i servizi Gestione API |
| Microsoft.Authorization/*/read |Autorizzazione Lettura |
| Microsoft.Insights/alertRules/* |Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="application-insights-component-contributor"></a>Collaboratore componente di Application Insights
È in grado di gestire i componenti di Application Insights

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* |Creare e gestire regole di avviso |
| Microsoft.Insights/components/* |È in grado di creare e gestire i componenti di Insights |
| Microsoft.Insights/webtests/* |È in grado di creare e gestire i test Web |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="automation-operator"></a>Operatore di automazione
È in grado di avviare, arrestare, sospendere e riprendere i processi

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Automation/automationAccounts/jobs/read |Leggere processi di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/resume/action |Riprendere un processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/stop/action |Arrestare un processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/streams/read |Leggere flussi di processi di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/suspend/action |Sopendere un processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/write |Scrivere processi di account di automazione |
| Microsoft.Automation/automationAccounts/jobSchedules/read |Leggere una pianificazione di processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobSchedules/write |Leggere una pianificazione di processo di account di automazione |
| Microsoft.Automation/automationAccounts/read |Leggere account di automazione |
| Microsoft.Automation/automationAccounts/runbooks/read |Leggere runbook di automazione |
| Microsoft.Automation/automationAccounts/schedules/read |Leggere pianificazioni di account di automazione |
| Microsoft.Automation/automationAccounts/schedules/write |Scrivere pianificazioni di account di automazione |
| Microsoft.Insights/components/* |È in grado di creare e gestire i componenti di Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="biztalk-contributor"></a>Collaboratore BizTalk
È in grado di gestire i servizi BizTalk

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.BizTalkServices/BizTalk/* |È in grado di creare e gestire i servizi BizTalk |
| Microsoft.Insights/alertRules/* |Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="cleardb-mysql-db-contributor"></a>Collaboratore database ClearDB MySQL
È in grado di gestire i database ClearDB MySQL

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* |Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |
| successbricks.cleardb/databases/* |È in grado di creare e gestire i database ClearDB MySQL |

### <a name="contributor"></a>Collaboratore
Può gestire tutto ad eccezione degli accessi.

| **Actions** |  |
| --- | --- |
| * |È in grado di creare e gestire ogni tipo di risorsa |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete |Non può eliminare ruoli e assegnazioni di ruoli |
| Microsoft.Authorization/*/Write |Non può creare ruoli e assegnazioni di ruoli |

### <a name="data-factory-contributor"></a>Collaboratore Data Factory
Creare e gestire data factory e le relative risorse figlio.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.DataFactory/dataFactories/* |Creare e gestire data factory e le relative risorse figlio. |
| Microsoft.Insights/alertRules/* |Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="devtest-labs-user"></a>Utente DevTest Labs
Può visualizzare tutti gli elementi e connettere, avviare, riavviare e arrestare macchine virtuali

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Compute/availabilitySets/read |Leggere le proprietà dei set di disponibilità |
| Microsoft.Compute/virtualMachines/*/read |Leggere le proprietà di una macchina virtuale (dimensioni della VM, stato di runtime, estensioni della VM e così via) |
| Microsoft.Compute/virtualMachines/deallocate/action |Deallocare macchine virtuali |
| Microsoft.Compute/virtualMachines/read |Leggere le proprietà di una macchina virtuale |
| Microsoft.Compute/virtualMachines/restart/action |Ottenere macchine virtuali |
| Microsoft.Compute/virtualMachines/start/action |Avviare macchine virtuali |
| Microsoft.DevTestLab/*/read |Leggere le proprietà di un lab |
| Microsoft.DevTestLab/labs/createEnvironment/action |Creare un ambiente lab |
| Microsoft.DevTestLab/labs/formulas/delete |Eliminare le formule |
| Microsoft.DevTestLab/labs/formulas/read |Leggere le formule |
| Microsoft.DevTestLab/labs/formulas/write |Aggiunge o modifica le formule |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action |Valutare i criteri lab |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |Aggiungere un pool di indirizzi back-end di bilanciamento del carico |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |Aggiungere una regola NAT di bilanciamento del carico in entrata |
| Microsoft.Network/networkInterfaces/*/read |Legge le proprietà di un'interfaccia di rete, ad esempio tutti i servizi di bilanciamento del carico di cui fa parte l'interfaccia di rete |
| Microsoft.Network/networkInterfaces/join/action |Aggiungere una macchina virtuale a un'interfaccia di rete |
| Microsoft.Network/networkInterfaces/read |Leggere le interfacce di rete |
| Microsoft.Network/networkInterfaces/write |Scrivere interfacce di rete |
| Microsoft.Network/publicIPAddresses/*/read |Leggere le proprietà di un indirizzo IP pubblico |
| Microsoft.Network/publicIPAddresses/join/action |Aggiungere un indirizzo IP pubblico |
| Microsoft.Network/publicIPAddresses/read |Leggere indirizzi IP pubblici di rete |
| Microsoft.Network/virtualNetworks/subnets/join/action |Aggiungere una rete virtuale |
| Microsoft.Resources/deployments/operations/read |Leggere le operazioni di distribuzione |
| Microsoft.Resources/deployments/read |Leggere le distribuzioni |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Storage/storageAccounts/listKeys/action |Ottenere chiavi degli account di archiviazione |

### <a name="dns-zone-contributor"></a>Collaboratore zona DNS
È in grado di gestire zone e record DNS.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/\*/lettura |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/\* |Creare e gestire regole di avviso |
| Microsoft.Network/dnsZones/\* |Creazione e gestione di zone e record DNS |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/\* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/\* |Creare e gestire ticket di supporto |

### <a name="documentdb-account-contributor"></a>Collaboratore account DocumentDB
Può gestire gli account DocumentDB

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.DocumentDb/databaseAccounts/* |Creare e gestire account DocumentDB |
| Microsoft.Insights/alertRules/* |Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="intelligent-systems-account-contributor"></a>Collaboratore account Intelligent Systems
È in grado di gestire account Intelligent Systems

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* |Creare e gestire regole di avviso |
| Microsoft.IntelligentSystems/accounts/* |Creare e gestire account di Intelligent Systems |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="network-contributor"></a>Collaboratore di rete
È in grado di gestire tutte le risorse di rete

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* |Creare e gestire regole di avviso |
| Microsoft.Network/* |Creare e gestire reti |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="new-relic-apm-account-contributor"></a>Collaboratore account New Relic APM
Può gestire account e applicazioni di New Relic Application Performance Management

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* |Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |
| NewRelic.APM/accounts/* |Creare e gestire account di gestione delle prestazioni delle applicazioni New Relic |

### <a name="owner"></a>Proprietario
È in grado di gestire tutti gli elementi, compresi gli accessi

| **Actions** |  |
| --- | --- |
| * |È in grado di creare e gestire ogni tipo di risorsa |

### <a name="reader"></a>Lettore
È in grado di visualizzare tutti gli elementi, ma non può apportare modifiche

| **Actions** |  |
| --- | --- |
| */lettura |Legge risorse di tutti i tipi, eccetto i segreti. |

### <a name="redis-cache-contributor"></a>Collaboratore cache Redis
È in grado di gestire le cache Redis

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Cache/redis/* |Creare e gestire cache Redis |
| Microsoft.Insights/alertRules/* |Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="scheduler-job-collections-contributor"></a>Collaboratore raccolte di processi dell'unità di pianificazione
È in grado di gestire raccolte di processi dell'utilità di pianificazione

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* |Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Scheduler/jobcollections/* |Creare e gestire raccolte di processi |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="search-service-contributor"></a>Collaboratore servizi di ricerca
È in grado di gestire servizi di ricerca

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* |Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Search/searchServices/* |È in grado di creare e gestire servizi di ricerca |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="security-manager"></a>Gestore della sicurezza SQL
Può gestire i componenti di protezione, i criteri di sicurezza e le macchine virtuali

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.ClassicCompute/*/read |Leggere le informazioni di configurazione delle macchine virtuali di calcolo classiche |
| Microsoft.ClassicCompute/virtualMachines/*/write |Scrivere la configurazione delle macchine virtuali |
| Microsoft.ClassicNetwork/*/read |Leggere le informazioni della configurazione sulla rete classica |
| Microsoft.Insights/alertRules/* |Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Security/* |Creare e gestire criteri e componenti di protezione |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="sql-db-contributor"></a>Collaboratore database SQL
Può gestire i database SQL, ma non i criteri correlati alla sicurezza

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* |Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Sql/servers/databases/* |Creare e gestire database SQL |
| Microsoft.Sql/servers/read |È in grado di leggere i server SQL |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Impossibile modificare i criteri di controllo |
| Microsoft.Sql/servers/databases/auditingSettings/* |Impossibile modificare le impostazioni di controllo |
| Microsoft.Sql/servers/databases/auditRecords/read |Non può leggere i record di controllo |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Impossibile modificare i criteri di connessione |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Impossibile modificare i criteri di mascheratura dei dati |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Impossibile modificare i criteri di avviso di sicurezza |
| Microsoft.Sql/servers/databases/securityMetrics/* |Impossibile modificare i criteri di protezione |

### <a name="sql-security-manager"></a>Gestione della sicurezza SQL
Può gestire i criteri correlati alla sicurezza di SQL Server e database SQL

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere l’autorizzazione Microsoft |
| Microsoft.Insights/alertRules/* |Creare e gestire le regole di avviso di Insight |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Sql/servers/auditingPolicies/* |Creare e gestire criteri di controllo di server SQL |
| Microsoft.Sql/servers/auditingSettings/* |Creare e gestire le impostazioni di controllo di SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Creare e gestire i criteri di controllo dei database SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* |Creare e gestire le impostazioni di controllo dei database di SQL Server |
| Microsoft.Sql/servers/databases/auditRecords/read |Legge i record di controllo |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Creare e gestire i criteri di connessione dei database dei server SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Creare e gestire i criteri della maschera dei dati dei database dei server SQL |
| Microsoft.Sql/servers/databases/read |Leggere database SQL |
| Microsoft.Sql/servers/databases/schemas/read |Leggere schemi di database di lettura di server SQL |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read |Leggere colonne della tabella del database di server SQL |
| Microsoft.Sql/servers/databases/schemas/tables/read |Leggere tabelle di database di server SQL |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Creare e gestire i criteri degli avvisi di sicurezza dei database di SQL Server |
| Microsoft.Sql/servers/databases/securityMetrics/* |Creare e gestire le metriche di sicurezza dei database di server SQL |
| Microsoft.Sql/servers/read |È in grado di leggere i server SQL |
| Microsoft.Sql/servers/securityAlertPolicies/* |Creare e gestire i criteri degli avvisi di sicurezza di SQL Server |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="sql-server-contributor"></a>Collaboratore SQL Server
Può gestire server e database SQL, ma non i criteri di protezione correlati

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorizzazione Lettura |
| Microsoft.Insights/alertRules/* |Creare e gestire le regole di avviso di Insight |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Sql/servers/* |Creare e gestire server SQL |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* |Non è in grado di modificare i criteri di controllo di server SQL |
| Microsoft.Sql/servers/auditingSettings/* |Non è in grado di modificare le impostazioni di controllo di SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Non è in grado di modificare i criteri di controllo dei database di server SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* |Non è in grado di modificare le impostazioni di controllo dei database di SQL Server |
| Microsoft.Sql/servers/databases/auditRecords/read |Non può leggere i record di controllo |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Non è in grado di modificare i criteri di connessione dei database di server SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Non è in grado di modificare i criteri di mascheratura dei dati dei database di server SQL |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Non è in grado di modificare i criteri degli avvisi di sicurezza dei database di SQL server |
| Microsoft.Sql/servers/databases/securityMetrics/* |Non è in grado di modificare le metriche di protezione dei database di server SQL |
| Microsoft.Sql/servers/securityAlertPolicies/* |Non è in grado di modificare i criteri degli avvisi di sicurezza di SQL Server |

### <a name="classic-storage-account-contributor"></a>Collaboratore account di archiviazione classico
È in grado di gestire gli account di archiviazione classici

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorizzazione Lettura |
| Microsoft.ClassicStorage/storageAccounts/* |Creare e gestire account di archiviazione |
| Microsoft.Insights/alertRules/* |Creare e gestire le regole di avviso di Insight |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="storage-account-contributor"></a>Collaboratore account di archiviazione
Può gestire gli account di archiviazione, ma non accedervi.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Leggere tutte le autorizzazioni |
| Microsoft.Insights/alertRules/* |Creare e gestire le regole di avviso di Insight |
| Microsoft.Insights/diagnosticSettings/* |Gestire le impostazioni di diagnostica |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Storage/storageAccounts/* |Creare e gestire account di archiviazione |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="user-access-administrator"></a>Amministratore accessi utente
Consente di gestire l’accesso degli utenti alle risorse di Azure

| **Actions** |  |
| --- | --- |
| */lettura |Legge risorse di tutti i tipi, eccetto i segreti. |
| Microsoft.Authorization/* |Gestire l'autorizzazione |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="classic-virtual-machine-contributor"></a>Collaboratore macchine virtuali classiche
È in grado di gestire macchine virtuali classiche, ma non la rete virtuale o gli account di archiviazione a cui sono connesse

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorizzazione Lettura |
| Microsoft.ClassicCompute/domainNames/* |Creare e gestire nomi di dominio di calcolo classici |
| Microsoft.ClassicCompute/virtualMachines/* |Creare e gestire macchine virtuali |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action |Partecipare a gruppi di sicurezza di rete |
| Microsoft.ClassicNetwork/reservedIps/link/action |Collegare IP riservati |
| Microsoft.ClassicNetwork/reservedIps/read |Leggere indirizzi IP riservati |
| Microsoft.ClassicNetwork/virtualNetworks/join/action |Partecipare a reti virtuali |
| Microsoft.ClassicNetwork/virtualNetworks/read |Leggere reti virtuali |
| Microsoft.ClassicStorage/storageAccounts/disks/read |Leggere dischi di account di archiviazione |
| Microsoft.ClassicStorage/storageAccounts/images/read |Leggere immagini di account di archiviazione |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action |Ottenere chiavi degli account di archiviazione |
| Microsoft.ClassicStorage/storageAccounts/read |Leggere account di archiviazione classici |
| Microsoft.Insights/alertRules/* |Creare e gestire le regole di avviso di Insight |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="virtual-machine-contributor"></a>Collaboratore macchine virtuali
È in grado di gestire macchine virtuali, ma non la rete virtuale o account di archiviazione a cui sono connesse

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorizzazione Lettura |
| Microsoft.Compute/availabilitySets/* |Creare e gestire set di disponibilità di calcolo |
| Microsoft.Compute/locations/* |Creare e gestire percorsi di calcolo |
| Microsoft.Compute/virtualMachines/* |Creare e gestire macchine virtuali |
| Microsoft.Compute/virtualMachineScaleSets/* |Creare e gestire i set di scalabilità delle macchine virtuali |
| Microsoft.Insights/alertRules/* |Creare e gestire le regole di avviso di Insight |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action |Partecipare a pool di indirizzi backend di gateway delle applicazioni di rete |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |Partecipare a pool di indirizzi backend di servizi di bilanciamento del carico |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action |Aggiungere pool NAT di bilanciamento del carico in entrata |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |Partecipa a regole NAT in entrata di servizi di bilanciamento del carico |
| Microsoft.Network/loadBalancers/read |Leggere servizi di bilanciamento del carico |
| Microsoft.Network/locations/* |Creare e gestire percorsi di rete |
| Microsoft.Network/networkInterfaces/* |Creare e gestire interfacce di rete |
| Microsoft.Network/networkSecurityGroups/join/action |Partecipare a gruppi di sicurezza di rete |
| Microsoft.Network/networkSecurityGroups/read |Leggere gruppi di sicurezza di rete |
| Microsoft.Network/publicIPAddresses/join/action |Partecipare a indirizzi IP pubblici di rete |
| Microsoft.Network/publicIPAddresses/read |Leggere indirizzi IP pubblici di rete |
| Microsoft.Network/virtualNetworks/read |Leggere reti virtuali |
| Microsoft.Network/virtualNetworks/subnets/join/action |Partecipare a subnet di reti virtuali |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Storage/storageAccounts/listKeys/action |Ottenere chiavi degli account di archiviazione |
| Microsoft.Storage/storageAccounts/read |Leggere account di archiviazione |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="classic-network-contributor"></a>Collaboratore reti virtuali classiche
È in grado di gestire reti virtuali classiche e IP riservati

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorizzazione Lettura |
| Microsoft.ClassicNetwork/* |Creare e gestire reti classiche |
| Microsoft.Insights/alertRules/* |Creare e gestire le regole di avviso di Insight |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |

### <a name="web-plan-contributor"></a>Collaboratore piani Web
È in grado di gestire piani Web

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorizzazione Lettura |
| Microsoft.Insights/alertRules/* |Creare e gestire le regole di avviso di Insight |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |
| Microsoft.Web/serverFarms/* |Creare e gestire server farm |

### <a name="website-contributor"></a>Collaboratore siti Web
È in grado di gestire siti Web, ma non i piani cui sono connessi

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Autorizzazione Lettura |
| Microsoft.Insights/alertRules/* |Creare e gestire le regole di avviso di Insight |
| Microsoft.Insights/components/* |È in grado di creare e gestire i componenti di Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read |Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* |Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read |Leggere gruppi di risorse |
| Microsoft.Support/* |Creare e gestire ticket di supporto |
| Microsoft.Web/certificates/* |Creare e gestire certificati dei siti Web |
| Microsoft.Web/listSitesAssignedToHostName/read |Leggere siti di lettura assegnati a un nome host |
| Microsoft.Web/serverFarms/join/action |Partecipare a server farm |
| Microsoft.Web/serverFarms/read |Leggere server farm |
| Microsoft.Web/sites/* |Creare e gestire siti Web. Per creare un sito sono anche necessarie le autorizzazione di scrittura associate al piano di servizio app |

## <a name="see-also"></a>Vedere anche
* [Controllo degli accessi in base al ruolo](role-based-access-control-configure.md): introduzione al controllo degli accessi in base al ruolo nel portale di Azure.
* [Ruoli personalizzati nel controllo degli accessi in base al ruolo di Azure](role-based-access-control-custom-roles.md): informazioni su come creare ruoli personalizzati per esigenze di accesso specifiche.
* [Creare un report della cronologia delle modifiche relative all'accesso](role-based-access-control-access-change-history-report.md): tenere traccia delle modifiche nelle assegnazioni dei ruoli nel controllo degli accessi in base al ruolo.
* [Risoluzione dei problemi del controllo degli accessi in base al ruolo](role-based-access-control-troubleshooting.md): suggerimenti per la risoluzione di problemi comuni.




<!--HONumber=Nov16_HO3-->


