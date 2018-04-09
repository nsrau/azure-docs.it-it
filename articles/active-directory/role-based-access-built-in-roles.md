---
title: Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure | Microsoft Docs
description: Questo argomento descrive i ruoli predefiniti per il controllo degli accessi in base al ruolo. I ruoli vengono continuamente aggiunti, controllare la validità della documentazione.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 0e8f1d97f51fbfedbca1619ef5e7f28a3a0570b9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure
Il controllo degli accessi in base al ruolo di Azure presenta i seguenti ruoli predefiniti che possono essere assegnati a utenti, gruppi e servizi. Non è possibile modificare le definizioni dei ruoli predefiniti. Si possono tuttavia creare [ruoli personalizzati nel controllo degli accessi in base al ruolo di Azure](role-based-access-control-custom-roles.md) per soddisfare le esigenze specifiche dell'organizzazione.

## <a name="built-in-role-descriptions"></a>Descrizione dei ruoli predefiniti
La tabella seguente contiene descrizioni brevi dei ruoli predefiniti. Fare clic sul nome del ruolo per visualizzare un elenco dettagliato delle proprietà **actions** e **notactions** per il ruolo. La proprietà **actions** specifica le azioni consentite sulle risorse di Azure. Nelle stringhe delle azioni è possibile utilizzare caratteri jolly. La proprietà **notactions** specifica le azioni non consentite.

L'azione definisce quale tipo di operazioni è possibile eseguire su un tipo di risorsa specifico. Ad esempio: 
- **Write** (Scrittura) consente di eseguire le operazioni PUT, POST, PATCH e DELETE.
- **Read** (Lettura) consente di eseguire operazioni GET.

Questo articolo tratta solo i ruoli diversi che esistono oggi. Quando si assegna un ruolo a un utente, tuttavia, è possibile limitare ulteriormente le azioni consentite definendo un ambito. Ciò è utile se si intende creare un collaboratore di siti Web, ma solo per un gruppo di risorse.

> [!NOTE]
> Le definizioni dei ruoli di Azure sono in continua evoluzione. Questo articolo viene aggiornato il più possibile, ma le definizioni dei ruoli più recenti sono sempre disponibili in Azure PowerShell. Usare il cmdlet [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) per elencare tutti i ruoli correnti. È possibile esaminare un ruolo specifico usando `(get-azurermroledefinition "<role name>").actions` o `(get-azurermroledefinition "<role name>").notactions` in base alle esigenze. Usare [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) per elencare le operazioni di provider di risorse di Azure specifici.


| Ruolo predefinito | DESCRIZIONE |
| --- | --- |
| [Proprietario](#owner) | È in grado di gestire tutti gli elementi, compresi gli accessi |
| [Collaboratore](#contributor) | Può gestire tutto ad eccezione degli accessi. |
| [Lettore](#reader) | È in grado di visualizzare tutti gli elementi, ma non può apportare modifiche |
| [Collaboratore servizio Gestione API](#api-management-service-contributor) | È in grado di gestire i servizi Gestione API |
| [Ruolo operatore del servizio Gestione API](#api-management-service-operator-role) | È in grado di gestire i servizi Gestione API |
| [Ruolo lettura del servizio Gestione API](#api-management-service-reader-role) | È in grado di gestire i servizi Gestione API |
| [Collaboratore componente di Application Insights](#application-insights-component-contributor) | È in grado di gestire i componenti di Application Insights |
| [Debugger di snapshot di Application Insights](#application-insights-snapshot-debugger) | Concede all'utente l'autorizzazione per l'uso delle funzionalità del debugger di snapshot di Application Insights |
| [Operatore processo di automazione](#automation-job-operator) | Consente di creare e gestire i processi tramite i runbook di automazione. |
| [Operatore di automazione](#automation-operator) | È in grado di avviare, arrestare, sospendere e riprendere i processi |
| [Operatore runbook di automazione](#automation-runbook-operator) | Consente di leggere le proprietà del runbook per permettere di creare processi del runbook. |
| [Proprietario della registrazione di Azure Stack](#azure-stack-registration-owner) | Consente di gestire le registrazioni di Azure Stack. |
| [Collaboratore di backup](#backup-contributor) | Consente di gestire tutte le azioni per la gestione del backup, ad eccezione della creazione dell'insieme di credenziali di Servizi di ripristino e l'accesso ad altri utenti |
| [Operatore di backup](#backup-operator) | Consente di gestire tutte le azioni di gestione dei backup, ad eccezione della creazione dell'insieme di credenziali, della rimozione del backup e dell'accesso ad altri utenti |
| [Lettore di backup](#backup-reader) | Consente di monitorare la gestione di backup nell'insieme di credenziali dei Servizi di ripristino |
| [Lettore per la fatturazione](#billing-reader) | Consente di visualizzare tutte le informazioni di fatturazione |
| [Collaboratore BizTalk](#biztalk-contributor) | È in grado di gestire i servizi BizTalk |
| [Collaboratore endpoint rete CDN](#cdn-endpoint-contributor) | Può gestire gli endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti. |
| [Lettore endpoint rete CDN](#cdn-endpoint-reader) | Può visualizzare gli endpoint della rete CDN, ma non può apportare modifiche. |
| [Collaboratore profilo rete CDN](#cdn-profile-contributor) | Può gestire i profili e i rispettivi endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti. |
| [Lettore profilo rete CDN](#cdn-profile-reader) | Può visualizzare i profili e i rispettivi endpoint della rete CDN, ma non può apportare modifiche. |
| [Collaboratore reti virtuali classiche](#classic-network-contributor) | È in grado di gestire reti virtuali classiche e IP riservati |
| [Collaboratore account di archiviazione classico](#classic-storage-account-contributor) | È in grado di gestire gli account di archiviazione classici |
| [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico](#classic-storage-account-key-operator-service-role) | Gli operatori della chiave dell'account di archiviazione classico sono autorizzati a elencare e rigenerare le chiavi negli account di archiviazione classici |
| [Collaboratore macchine virtuali classiche](#classic-virtual-machine-contributor) | È in grado di gestire macchine virtuali classiche, ma non la rete virtuale o l'account di archiviazione a cui sono connesse |
| [Collaboratore database ClearDB MySQL](#cleardb-mysql-db-contributor) | È in grado di gestire i database ClearDB MySQL |
| [Ruolo Lettore dell'account Cosmos DB](#cosmos-db-account-reader-role) | Può leggere i dati degli account Azure Cosmos DB. Vedere [Collaboratore account DocumentDB](#documentdb-account-contributor) per la gestione degli account Azure Cosmos DB. |
| [Collaboratore Data Factory](#data-factory-contributor) | Creare e gestire data factory e le relative risorse figlio. |
| [Sviluppatore di Data Lake Analytics](#data-lake-analytics-developer) | Consente di inviare, monitorare e gestire i propri processi, ma non di creare o eliminare account Data Lake Analytics. |
| [Utente DevTest Labs](#devtest-labs-user) | Può visualizzare tutti gli elementi e connettere, avviare, riavviare e arrestare macchine virtuali |
| [Collaboratore zona DNS](#dns-zone-contributor) | È in grado di gestire zone e record DNS. |
| [Collaboratore account DocumentDB](#documentdb-account-contributor) | È in grado di gestire account Azure Cosmos DB. Azure Cosmos DB era precedentemente noto come DocumentDB. |
| [Collaboratore account Intelligent Systems](#intelligent-systems-account-contributor) | È in grado di gestire account Intelligent Systems |
| [Collaboratore di Key Vault](#key-vault-contributor) | Consente di gestire gli insiemi di credenziali delle chiavi, ma non di accedervi. |
| [Lab Creator](#lab-creator) (Creatore di lab) | Consente di creare, gestire ed eliminare i lab gestiti con gli account di Azure Lab. |
| [Collaboratore di Log Analytics](#log-analytics-contributor) | Il ruolo Collaboratore di Log Analytics può leggere tutti i dati di monitoraggio e modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log dall'Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di soluzioni e la configurazione di Diagnostica di Azure in tutte le risorse di Azure. |
| [Lettore di Log Analytics](#log-analytics-reader) | Il ruolo Lettore di Log Analytics può visualizzare ed eseguire ricerche in tutti i dati di monitoraggio e può visualizzare le impostazioni di monitoraggio, inclusa la visualizzazione della configurazione di Diagnostica di Azure in tutte le risorse di Azure. |
| [Collaboratore per app per la logica](#logic-app-contributor) | Consente di gestire le app per la logica, ma non di accedervi. |
| [Operatore per app per la logica](#logic-app-operator) | Consente di leggere, abilitare e disabilitare l'app per la logica. |
| [Managed Identity Contributor](#managed-identity-contributor) (Collaboratore per identità gestita) | Crea, legge, aggiorna ed elimina l'identità assegnata all'utente |
| [Managed Identity Operator](#managed-identity-operator) (Operatore per identità gestita) | Legge e assegna l'identità assegnata all'utente |
| [Collaboratore al monitoraggio](#monitoring-contributor) | Può leggere tutti i dati del monitoraggio e modificare le impostazioni di monitoraggio. Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Lettore di monitoraggio](#monitoring-reader) | Può leggere tutti i dati del monitoraggio (metriche, log e così via). Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Collaboratore di rete](#network-contributor) | È in grado di gestire tutte le risorse di rete |
| [Collaboratore account New Relic APM](#new-relic-apm-account-contributor) | Consente di gestire gli account e le applicazioni di APR New Relic, ma non di accedervi. |
| [Collaboratore cache Redis](#redis-cache-contributor) | È in grado di gestire le cache Redis |
| [Collaboratore raccolte di processi dell'unità di pianificazione](#scheduler-job-collections-contributor) | È in grado di gestire raccolte di processi dell'utilità di pianificazione |
| [Collaboratore servizi di ricerca](#search-service-contributor) | È in grado di gestire servizi di ricerca |
| [Amministrazione della protezione](#security-admin) | Solo in Centro sicurezza: è possibile visualizzare i criteri di sicurezza e gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare gli avvisi e le raccomandazioni, ignorare gli avvisi e le raccomandazioni |
| [Gestore della sicurezza SQL](#security-manager) | Può gestire i componenti di protezione, i criteri di sicurezza e le macchine virtuali |
| [Ruolo con autorizzazioni di lettura per la sicurezza](#security-reader) | Solo in Centro sicurezza: è possibile visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non è possibile apportare modifiche |
| [Collaboratore al ripristino sito](#site-recovery-contributor) | Consente di gestire tutte le azioni per il ripristino del sito, ad eccezione della creazione dell'insieme di credenziali di Servizi di ripristino e l'assegnazione dei diritti di accesso ad altri utenti |
| [Operatore del ripristino sito](#site-recovery-operator) | Può eseguire failover e failback ma non può eseguire altre azioni di gestione di ripristino del sito o assegnare l'accesso ad altri utenti |
| [Reader di ripristino sito](#site-recovery-reader) | Può monitorare lo stato di ripristino del sito nell'insieme di credenziali di servizi di ripristino e generare i ticket di supporto |
| [Collaboratore database SQL](#sql-db-contributor) | Può gestire i database SQL, ma non i criteri correlati alla sicurezza |
| [Gestione della sicurezza SQL](#sql-security-manager) | Può gestire i criteri correlati alla sicurezza di SQL Server e database SQL |
| [Collaboratore SQL Server](#sql-server-contributor) | Può gestire server e database SQL, ma non i criteri di protezione correlati |
| [Collaboratore account di archiviazione](#storage-account-contributor) | Può gestire gli account di archiviazione, ma non accedervi. |
| [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione](#storage-account-key-operator-service-role) | Gli operatori della chiave dell'account di archiviazione sono autorizzati a elencare e rigenerare le chiavi negli account di archiviazione |
| [Collaboratore alla richiesta di supporto](#support-request-contributor) | Consente di creare e gestire i ticket di supporto nell'ambito della sottoscrizione |
| [Collaboratore Gestione traffico](#traffic-manager-contributor) | Consente di gestire i profili di Gestione traffico, ma non di controllare chi è autorizzato ad accedervi. |
| [Amministratore accessi utente](#user-access-administrator) | Consente di gestire l'accesso degli utenti alle risorse di Azure |
| [Virtual Machine Administrator Login](#virtual-machine-administrator-login) (Accesso amministratore macchina virtuale) | Gli utenti con questo ruolo hanno la possibilità di accedere a una macchina virtuale con privilegi di amministratore di Windows o di utente root Linux. |
| [Collaboratore macchine virtuali](#virtual-machine-contributor) | È in grado di gestire macchine virtuali, ma non la rete virtuale o l'account di archiviazione a cui sono connesse |
| [Virtual Machine User Login](#virtual-machine-user-login) (Accesso utente macchina virtuale) | Gli utenti con questo ruolo hanno la possibilità di accedere a una macchina virtuale come utente normale. |
| [Collaboratore piani Web](#web-plan-contributor) | È in grado di gestire piani Web |
| [Collaboratore siti Web](#website-contributor) | È in grado di gestire siti Web, ma non i piani cui sono connessi |

Nelle tabelle seguenti vengono descritte le autorizzazioni specifiche assegnate a ogni ruolo. Può trattarsi di proprietà **Actions**, che concedono le autorizzazioni, e **NotActions**, che le limitano.

## <a name="owner"></a>Proprietario
È in grado di gestire tutti gli elementi, compresi gli accessi

| **Actions** |  |
| --- | --- |
| * | È in grado di creare e gestire ogni tipo di risorsa |

## <a name="contributor"></a>Collaboratore
Può gestire tutto ad eccezione degli accessi.

| **Actions** |  |
| --- | --- |
| * | È in grado di creare e gestire ogni tipo di risorsa |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete | Non può eliminare ruoli e assegnazioni di ruolo |
| Microsoft.Authorization/*/Write | Non può creare ruoli e assegnazioni di ruolo |
| Microsoft.Authorization/elevateAccess/Action | Concede al chiamante l'accesso di tipo Amministratore Accesso utenti a livello dell'ambito del tenant |

## <a name="reader"></a>Reader
È in grado di visualizzare tutti gli elementi, ma non può apportare modifiche

| **Actions** |  |
| --- | --- |
| */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |

## <a name="api-management-service-contributor"></a>Collaboratore servizio Gestione API
È in grado di gestire i servizi Gestione API

| **Actions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/* | È in grado di creare e gestire il servizio Gestione API |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="api-management-service-operator-role"></a>Ruolo operatore del servizio Gestione API
È in grado di gestire i servizi Gestione API

| **Actions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Leggere le istanze del servizio Gestione API |
| Microsoft.ApiManagement/service/backup/action | Eseguire il backup del servizio Gestione API nel contenitore specificato in un account di archiviazione fornito dall'utente |
| Microsoft.ApiManagement/service/delete | Eliminare un'istanza del servizio Gestione API |
| Microsoft.ApiManagement/service/managedeployments/action | Modificare gli SKU/le unità; aggiungere o rimuovere distribuzioni regionali del servizio Gestione API |
| Microsoft.ApiManagement/service/read | Leggere i metadati per un'istanza del servizio Gestione API |
| Microsoft.ApiManagement/service/restore/action | Ripristinare il servizio Gestione API dal contenitore specificato in un account di archiviazione fornito dall'utente |
| Microsoft.ApiManagement/service/updatecertificate/action | Carica il certificato SSL per un servizio Gestione API. |
| Microsoft.ApiManagement/service/updatehostname/action | Configurare, aggiornare o rimuovere i nomi di dominio personalizzati per un servizio Gestione API |
| Microsoft.ApiManagement/service/write | Creare una nuova istanza del servizio Gestione API |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Ottiene l'elenco di chiavi utente |

## <a name="api-management-service-reader-role"></a>Ruolo lettura del servizio Gestione API
È in grado di gestire i servizi Gestione API

| **Actions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Leggere le istanze del servizio Gestione API |
| Microsoft.ApiManagement/service/read | Leggere i metadati per un'istanza del servizio Gestione API |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Ottiene l'elenco di chiavi utente |

## <a name="application-insights-component-contributor"></a>Collaboratore componente di Application Insights
È in grado di gestire i componenti di Application Insights

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Insights/components/* | È in grado di creare e gestire i componenti di Insights |
| Microsoft.Insights/webtests/* | È in grado di creare e gestire i test Web |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="application-insights-snapshot-debugger"></a>Debugger di snapshot di Application Insights
Concede all'utente l'autorizzazione per l'uso delle funzionalità del debugger di snapshot di Application Insights

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Insights/components/*/read |  |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="automation-job-operator"></a>Operatore processo di automazione
Consente di creare e gestire i processi tramite i runbook di Automazione.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Automation/automationAccounts/jobs/read | Ottiene un processo di automazione di Azure |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Riprende un processo di automazione di Azure |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Arresta un processo di automazione di Azure |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Legge le risorse del ruolo di lavoro ibrido per runbook |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Ottiene un flusso del processo di automazione di Azure |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Sospende un processo di automazione di Azure |
| Microsoft.Automation/automationAccounts/jobs/write | Crea un processo di automazione di Azure |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="automation-operator"></a>Operatore di automazione
È in grado di avviare, arrestare, sospendere e riprendere i processi

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Automation/automationAccounts/jobs/read | Leggere processi di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Riprendere un processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Arrestare un processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Leggere flussi di processi di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Sospendere un processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobs/write | Scrivere processi di account di automazione |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Leggere una pianificazione di processo di account di automazione |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Leggere una pianificazione di processo di account di automazione |
| Microsoft.Automation/automationAccounts/linkedWorkspace/read | Ottiene l'area di lavoro collegata all'account di automazione |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Legge le risorse del ruolo di lavoro ibrido per runbook |
| Microsoft.Automation/automationAccounts/read | Leggere account di automazione |
| Microsoft.Automation/automationAccounts/runbooks/read | Leggere runbook di automazione |
| Microsoft.Automation/automationAccounts/schedules/read | Leggere pianificazioni di account di automazione |
| Microsoft.Automation/automationAccounts/schedules/write | Scrivere pianificazioni di account di automazione |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="automation-runbook-operator"></a>Operatore runbook di automazione
Consente di leggere le proprietà del runbook per permettere di creare processi del runbook.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Automation/automationAccounts/runbooks/read | Ottiene un runbook di automazione di Azure |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="azure-stack-registration-owner"></a>Proprietario della registrazione di Azure Stack
Consente di gestire le registrazioni di Azure Stack.

| **Actions** |  |
| --- | --- |
| Microsoft.AzureStack/registrations/products/listDetails/action | Recupera i dettagli completi per un prodotto Azure Stack Marketplace |
| Microsoft.AzureStack/registrations/products/read | Ottiene le proprietà di un prodotto Azure Stack Marketplace |
| Microsoft.AzureStack/registrations/read | Ottiene le proprietà di una registrazione di Azure Stack |

## <a name="backup-contributor"></a>Collaboratore di backup
Consente di gestire tutte le azioni per la gestione del backup, ad eccezione della creazione dell'insieme di credenziali di Servizi di ripristino e l'accesso ad altri utenti

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Network/virtualNetworks/read | Leggere reti virtuali |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Consente di gestire i risultati dell'operazione sulla gestione del backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Consente di creare e gestire i contenitori di backup all'interno delle infrastrutture di backup dell'insieme di credenziali dei Servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Consente di creare e gestire i processi di backup |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Consente di esportare i processi di backup in un file Excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Consente di creare e gestire i metadati relativi alla gestione di backup |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Consente di creare e gestire i risultati delle operazioni di gestione di backup |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | Consente di creare e gestire i criteri di backup |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Consente di creare e gestire gli elementi su cui è possibile eseguire il backup |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Consente di creare e gestire gli elementi su cui è stato eseguito il backup |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Consente di creare e gestire i contenitori che contengono gli elementi di backup |
| Microsoft.RecoveryServices/Vaults/certificates/* | Consente di creare e gestire i certificati relativi al backup nell'insieme di credenziali dei Servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Consente di creare e gestire informazioni estese relative all'insieme di credenziali |
| Microsoft.RecoveryServices/Vaults/read | Consente di leggere l'insieme di credenziali dei servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Consente di gestire le operazioni di individuazione per il recupero dei nuovi contenitori creati |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Consente di creare e gestire le identità registrate |
| Microsoft.RecoveryServices/Vaults/usages/* | Consente di creare e gestire l'uso dell'insieme di credenziali dei Servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino. |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Storage/storageAccounts/read | Leggere account di archiviazione |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Restituisce il risultato dell'operazione di esportazione del processo. |
| Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="backup-operator"></a>Operatore di backup
Consente di gestire tutte le azioni di gestione dei backup, ad eccezione della creazione dell'insieme di credenziali, della rimozione del backup e dell'accesso ad altri utenti

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Network/virtualNetworks/read | Leggere reti virtuali |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Consente di leggere i risultati dell'operazione nella gestione dei backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | Consente di leggere i risultati di un'operazione di lettura nei contenitori di protezione |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/backup/action | Consente di eseguire l'operazione di backup su richiesta su un elemento su cui è stato eseguito il backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | Consente di leggere il risultato dell'operazione eseguita su un elemento su cui è stato eseguito il backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | Restituisce lo stato dell'operazione eseguita sugli elementi protetti. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Consente di leggere gli elementi su cui è stato eseguito il backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | Consente di leggere il punto di ripristino di un elemento su cui è stato eseguito il backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/restore/action | Consente di eseguire un'operazione di ripristino usando un punto di ripristino di un elemento su cui è stato eseguito il backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | Consente di creare un elemento di backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Consente di leggere i contenitori che contengono l'elemento di backup |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Consente di creare e gestire i processi di backup |
| Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Annulla il processo |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Restituisce il risultato dell'operazione di processo. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Restituisce tutti gli oggetti processo |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Consente di esportare i processi di backup in un file Excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Consente di leggere i metadati relativi alla gestione di backup |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Consente di creare e gestire i risultati delle operazioni di gestione di backup |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Consente di leggere i risultati delle operazioni eseguite sui criteri di backup |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Consente di leggere i criteri di backup |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Consente di creare e gestire gli elementi su cui è possibile eseguire il backup |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Restituisce l'elenco di tutti gli elementi da proteggere. |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Consente di leggere gli elementi su cui è stato eseguito il backup |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Consente di leggere i contenitori su cui è stato eseguito il backup che contengono gli elementi di backup |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Consente di leggere informazioni estese relative all'insieme di credenziali |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | Consente di scrivere informazioni estese relative all'insieme di credenziali |
| Microsoft.RecoveryServices/Vaults/read | Consente di leggere l'insieme di credenziali dei servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Consente di gestire le operazioni di individuazione per il recupero dei nuovi contenitori creati |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Consente di leggere i risultati dell'operazione eseguita sugli elementi registrati dell'insieme di credenziali |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Consente di leggere gli elementi registrati dell'insieme di credenziali |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Consente di scrivere gli elementi registrati nell'insieme di credenziali |
| Microsoft.RecoveryServices/Vaults/usages/read | Consente di leggere l'uso dell'insieme di credenziali dei Servizi di ripristino |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Storage/storageAccounts/read | Leggere account di archiviazione |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Effettua il provisioning del ripristino elementi immediato per l'elemento protetto |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revoca il ripristino elementi immediato per l'elemento protetto |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Restituisce il risultato dell'operazione di esportazione del processo. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
| Microsoft.RecoveryServices/Vaults/certificates/write | L'operazione Aggiorna certificato risorsa aggiorna il certificato delle credenziali della risorsa o dell'insieme di credenziali. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="backup-reader"></a>Lettore di backup
Consente di monitorare la gestione di backup nell'insieme di credenziali dei Servizi di ripristino

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Consente di leggere i risultati dell'operazione nella gestione dei backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | Consente di leggere i risultati di un'operazione di lettura nei contenitori di protezione |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | Consente di leggere il risultato dell'operazione eseguita su un elemento su cui è stato eseguito il backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | Restituisce lo stato dell'operazione eseguita sugli elementi protetti. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Consente di leggere gli elementi su cui è stato eseguito il backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Consente di leggere i contenitori che contengono l'elemento di backup |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Consente di leggere i risultati dei processi di backup |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Consente di leggere i processi di backup |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Consente di esportare i processi di backup in un file Excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Consente di leggere i metadati relativi alla gestione di backup |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Consente di leggere i risultati dell'operazione di gestione di backup |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Consente di leggere i risultati delle operazioni eseguite sui criteri di backup |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Consente di leggere i criteri di backup |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Consente di leggere gli elementi su cui è stato eseguito il backup |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Consente di leggere i contenitori su cui è stato eseguito il backup che contengono gli elementi di backup |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Consente di leggere informazioni estese relative all'insieme di credenziali |
| Microsoft.RecoveryServices/Vaults/read | Consente di leggere l'insieme di credenziali dei servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Consente di leggere i risultati delle operazioni di individuazione per il recupero dei nuovi contenitori creati |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Consente di leggere i risultati dell'operazione eseguita sugli elementi registrati dell'insieme di credenziali |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Consente di leggere gli elementi registrati dell'insieme di credenziali |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | Ottiene i punti di ripristino degli elementi protetti. |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Restituisce il risultato dell'operazione di esportazione del processo. |
| Microsoft.RecoveryServices/Vaults/usages/read | Consente di leggere l'uso dell'insieme di credenziali dei Servizi di ripristino |

## <a name="billing-reader"></a>Lettore per la fatturazione
Consente di visualizzare tutte le informazioni di fatturazione

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Billing/*/read | Lettura delle informazioni di fatturazione |
| Microsoft.Consumption/*/read |  |
| Microsoft.Commerce/*/read |  |
| Microsoft.Management/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="biztalk-contributor"></a>Collaboratore BizTalk
È in grado di gestire i servizi BizTalk

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.BizTalkServices/BizTalk/* | È in grado di creare e gestire i servizi BizTalk |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="cdn-endpoint-contributor"></a>Collaboratore endpoint rete CDN
Può gestire gli endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/* |  |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="cdn-endpoint-reader"></a>Lettore endpoint rete CDN
Può visualizzare gli endpoint della rete CDN, ma non può apportare modifiche.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/*/read |  |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="cdn-profile-contributor"></a>Collaboratore profilo rete CDN
Può gestire i profili e i rispettivi endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/* |  |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="cdn-profile-reader"></a>Lettore profilo rete CDN
Può visualizzare i profili e i rispettivi endpoint della rete CDN, ma non può apportare modifiche.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/*/read |  |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="classic-network-contributor"></a>Collaboratore reti virtuali classiche
È in grado di gestire reti virtuali classiche e IP riservati

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.ClassicNetwork/* | Creare e gestire reti classiche |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="classic-storage-account-contributor"></a>Collaboratore account di archiviazione classico
È in grado di gestire gli account di archiviazione classici

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.ClassicStorage/storageAccounts/* | Creare e gestire account di archiviazione |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="classic-storage-account-key-operator-service-role"></a>Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico
Gli operatori della chiave dell'account di archiviazione classico sono autorizzati a elencare e rigenerare le chiavi negli account di archiviazione classici

| **Actions** |  |
| --- | --- |
| Microsoft.ClassicStorage/storageAccounts/listkeys/action | Elenca le chiavi di accesso per gli account di archiviazione. |
| Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Rigenera le chiavi di accesso esistenti per l'account di archiviazione. |

## <a name="classic-virtual-machine-contributor"></a>Collaboratore macchine virtuali classiche
È in grado di gestire macchine virtuali classiche, ma non la rete virtuale o l'account di archiviazione a cui sono connesse

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.ClassicCompute/domainNames/* | Creare e gestire nomi di dominio di calcolo classici |
| Microsoft.ClassicCompute/virtualMachines/* | Creare e gestire macchine virtuali |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | Partecipare a gruppi di sicurezza di rete |
| Microsoft.ClassicNetwork/reservedIps/link/action | Collegare IP riservati |
| Microsoft.ClassicNetwork/reservedIps/read | Leggere indirizzi IP riservati |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Partecipare a reti virtuali |
| Microsoft.ClassicNetwork/virtualNetworks/read | Leggere reti virtuali |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Leggere dischi di account di archiviazione |
| Microsoft.ClassicStorage/storageAccounts/images/read | Leggere immagini di account di archiviazione |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Ottenere chiavi degli account di archiviazione |
| Microsoft.ClassicStorage/storageAccounts/read | Leggere account di archiviazione classici |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="cleardb-mysql-db-contributor"></a>Collaboratore database ClearDB MySQL
È in grado di gestire i database ClearDB MySQL

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |
| successbricks.cleardb/databases/* | È in grado di creare e gestire i database ClearDB MySQL |

## <a name="cosmos-db-account-reader-role"></a>Ruolo Lettore dell'account Cosmos DB
Può leggere i dati degli account Azure Cosmos DB. Vedere [Collaboratore account DocumentDB](#documentdb-account-contributor) per la gestione degli account Azure Cosmos DB.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere ruoli e assegnazioni di ruolo (è possibile leggere le autorizzazioni concesse a ogni utente) |
| Microsoft.DocumentDB/*/read | Leggere tutte le raccolte |
| Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Leggere il riquadro delle chiavi readonly |
| Microsoft.Insights/MetricDefinitions/read | Leggere le definizioni delle metriche |
| Microsoft.Insights/Metrics/read | Leggere le metriche dell'account |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="data-factory-contributor"></a>Collaboratore Data Factory
Creare e gestire data factory e le relative risorse figlio.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.DataFactory/dataFactories/* | Creare e gestire data factory e le relative risorse figlio. |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="data-lake-analytics-developer"></a>Sviluppatore di Data Lake Analytics
Consente di inviare, monitorare e gestire i propri processi, ma non di creare o eliminare account Data Lake Analytics.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.BigAnalytics/accounts/* |  |
| Microsoft.DataLakeAnalytics/accounts/* |  |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
| Microsoft.Resources/deployments/* | Crea e gestisce distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Support/* | Crea e gestisce ticket di supporto |

| **NotActions** |  |
| --- | --- |
| Microsoft.BigAnalytics/accounts/Delete |  |
| Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
| Microsoft.BigAnalytics/accounts/Write |  |
| Microsoft.DataLakeAnalytics/accounts/Delete | Elimina un account Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Concede le autorizzazioni per annullare i processi inviati da altri utenti. |
| Microsoft.DataLakeAnalytics/accounts/Write | Crea o aggiorna un account Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Crea o aggiorna un account Data Lake Store collegato a un account Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Scollega un account Data Lake Store da un account Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Crea o aggiorna un account di archiviazione collegato a un account Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Scollega un account di archiviazione da un account Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Crea o aggiorna una regola del firewall. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Elimina una regola del firewall. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Crea o aggiorna criteri di calcolo. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Elimina criteri di calcolo. |

## <a name="devtest-labs-user"></a>Utente DevTest Labs
Può visualizzare tutti gli elementi e connettere, avviare, riavviare e arrestare macchine virtuali

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Compute/availabilitySets/read | Leggere le proprietà dei set di disponibilità |
| Microsoft.Compute/virtualMachines/*/read | Leggere le proprietà di una macchina virtuale (dimensioni della VM, stato di runtime, estensioni della VM e così via) |
| Microsoft.Compute/virtualMachines/deallocate/action | Deallocare macchine virtuali |
| Microsoft.Compute/virtualMachines/read | Leggere le proprietà di una macchina virtuale |
| Microsoft.Compute/virtualMachines/restart/action | Ottenere macchine virtuali |
| Microsoft.Compute/virtualMachines/start/action | Avviare macchine virtuali |
| Microsoft.DevTestLab/*/read | Leggere le proprietà di un lab |
| Microsoft.DevTestLab/labs/createEnvironment/action | Creare un ambiente lab |
| Microsoft.DevTestLab/labs/claimAnyVm/action | Attesta una macchina virtuale attestabile casualmente nel lab. |
| Microsoft.DevTestLab/labs/formulas/delete | Eliminare le formule |
| Microsoft.DevTestLab/labs/formulas/read | Leggere le formule |
| Microsoft.DevTestLab/labs/formulas/write | Aggiunge o modifica le formule |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Valutare i criteri lab |
| Microsoft.DevTestLab/labs/virtualMachines/claim/action | Consente di assumere la proprietà di una macchina virtuale esistente |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Aggiungere un pool di indirizzi back-end di bilanciamento del carico |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Aggiungere una regola NAT di bilanciamento del carico in entrata |
| Microsoft.Network/networkInterfaces/*/read | Legge le proprietà di un'interfaccia di rete, ad esempio tutti i servizi di bilanciamento del carico di cui fa parte l'interfaccia di rete |
| Microsoft.Network/networkInterfaces/join/action | Aggiungere una macchina virtuale a un'interfaccia di rete |
| Microsoft.Network/networkInterfaces/read | Leggere le interfacce di rete |
| Microsoft.Network/networkInterfaces/write | Scrivere interfacce di rete |
| Microsoft.Network/publicIPAddresses/*/read | Leggere le proprietà di un indirizzo IP pubblico |
| Microsoft.Network/publicIPAddresses/join/action | Aggiungere un indirizzo IP pubblico |
| Microsoft.Network/publicIPAddresses/read | Leggere indirizzi IP pubblici di rete |
| Microsoft.Network/virtualNetworks/subnets/join/action | Aggiungere una rete virtuale |
| Microsoft.Resources/deployments/operations/read | Leggere le operazioni di distribuzione |
| Microsoft.Resources/deployments/read | Leggere le distribuzioni |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Storage/storageAccounts/listKeys/action | Ottenere chiavi degli account di archiviazione |

| **NotActions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/vmSizes/read | Elenca le dimensioni disponibili a cui è possibile aggiornare la macchina virtuale |

## <a name="dns-zone-contributor"></a>Collaboratore zona DNS
È in grado di gestire zone e record DNS.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Network/dnsZones/* | Creazione e gestione di zone e record DNS |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="documentdb-account-contributor"></a>Collaboratore account DocumentDB
È in grado di gestire account Azure Cosmos DB. Azure Cosmos DB era precedentemente noto come DocumentDB.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.DocumentDb/databaseAccounts/* | Creare e gestire account Azure Cosmos DB |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="intelligent-systems-account-contributor"></a>Collaboratore account Intelligent Systems
È in grado di gestire account Intelligent Systems

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.IntelligentSystems/accounts/* | Creare e gestire account di Intelligent Systems |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="key-vault-contributor"></a>Collaboratore di Key Vault
Consente di gestire gli insiemi di credenziali delle chiavi, ma non di accedervi.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.KeyVault/* |  |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

| **NotActions** |  |
| --- | --- |
| Microsoft.KeyVault/locations/deletedVaults/purge/action | Ripulisce un Key Vault eliminato temporaneamente |
| Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Lab Creator (Creatore di lab)
Consente di creare, gestire ed eliminare i lab gestiti con gli account di Azure Lab.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.ManagedLab/labAccounts/createLab/action | Crea un lab in un account di Lab. |
| Microsoft.ManagedLab/labAccounts/*/read |  |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="log-analytics-contributor"></a>Collaboratore di Log Analytics
Il ruolo Collaboratore di Log Analytics può leggere tutti i dati di monitoraggio e modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log dall'Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di soluzioni e la configurazione di Diagnostica di Azure in tutte le risorse di Azure.

| **Actions** |  |
| --- | --- |
| */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
| Microsoft.Automation/automationAccounts/* |  |
| Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Elenca le chiavi di accesso per gli account di archiviazione. |
| Microsoft.Compute/virtualMachines/extensions/* |  |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Insights/diagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
| Microsoft.OperationalInsights/* |  |
| Microsoft.OperationsManagement/* |  |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
| Microsoft.Storage/storageAccounts/listKeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="log-analytics-reader"></a>Lettore di Log Analytics
Il ruolo Lettore di Log Analytics può visualizzare ed eseguire ricerche in tutti i dati di monitoraggio e può visualizzare le impostazioni di monitoraggio, inclusa la visualizzazione della configurazione di Diagnostica di Azure in tutte le risorse di Azure.

| **Actions** |  |
| --- | --- |
| */lettura | Leggere risorse di tutti i tipi, eccetto i segreti. |
| Microsoft.OperationalInsights/workspaces/analytics/query/action | Esegue la ricerca usando il nuovo motore. |
| Microsoft.OperationalInsights/workspaces/search/action | Esegue una query di ricerca |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

| **NotActions** |  |
| --- | --- |
| Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera le chiavi condivise per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro. |

## <a name="logic-app-contributor"></a>Collaboratore alle app per la logica
Consente di gestire le app per la logica, ma non di accedervi.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Elenca le chiavi di accesso per gli account di archiviazione. |
| Microsoft.ClassicStorage/storageAccounts/read | Restituisce l'account di archiviazione con l'account specificato. |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Insights/diagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
| Microsoft.Insights/logdefinitions/* | Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai registri attività tramite il portale. Elencare categorie di log nel log attività. |
| Microsoft.Insights/metricDefinitions/* | Definizioni delle metriche (elenco dei tipi di metriche disponibili per una risorsa). |
| Microsoft.Logic/* | Gestisce le risorse di App per la logica. |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Storage/storageAccounts/listkeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
| Microsoft.Storage/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |
| Microsoft.Web/connectionGateways/* | Crea e gestisce un gateway di connessione. |
| Microsoft.Web/connections/* | Crea e gestisce una connessione. |
| Microsoft.Web/customApis/* | Crea e gestisce un'API personalizzata. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | Ottiene le proprietà per un piano di servizio app |
| Microsoft.Web/sites/functions/listSecrets/action | Elenca i segreti delle funzioni delle app Web. |

## <a name="logic-app-operator"></a>Operatore delle app per la logica
Consente di leggere, abilitare e disabilitare l'app per la logica.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/*/read | Legge le regole di avviso di Insights |
| Microsoft.Insights/diagnosticSettings/*/read | Ottiene le impostazioni di diagnostica di App per la logica |
| Microsoft.Insights/metricDefinitions/*/read | Ottiene le metriche disponibili per App per la logica. |
| Microsoft.Logic/*/read | Legge le risorse di App per la logica. |
| Microsoft.Logic/workflows/disable/action | Disabilita il flusso di lavoro. |
| Microsoft.Logic/workflows/enable/action | Abilita il flusso di lavoro. |
| Microsoft.Logic/workflows/validate/action | Convalida il flusso di lavoro. |
| Microsoft.Resources/deployments/operations/read | Ottiene o elenca le operazioni di distribuzione. |
| Microsoft.Resources/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |
| Microsoft.Web/connectionGateways/*/read | Legge i gateway di connessione. |
| Microsoft.Web/connections/*/read | Legge le connessioni. |
| Microsoft.Web/customApis/*/read | Legge l'API personalizzata. |
| Microsoft.Web/serverFarms/read | Ottiene le proprietà per un piano di servizio app |

## <a name="managed-identity-contributor"></a>Managed Identity Contributor (Collaboratore per identità gestita)
Crea, legge, aggiorna ed elimina l'identità assegnata all'utente

| **Actions** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="managed-identity-operator"></a>Managed Identity Operator (Operatore per identità gestita)
Legge e assegna l'identità assegnata all'utente

| **Actions** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="monitoring-contributor"></a>Collaboratore al monitoraggio
Può leggere tutti i dati del monitoraggio e modificare le impostazioni di monitoraggio. Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Actions** |  |
| --- | --- |
| */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
| Microsoft.AlertsManagement/alerts/* |  |
| Microsoft.AlertsManagement/alertsSummary/* |  |
| Microsoft.Insights/AlertRules/* | Regole di avviso di lettura, scrittura ed eliminazione. |
| Microsoft.Insights/components/* | Leggere, scrivere ed eliminare componenti di Application Insights. |
| Microsoft.Insights/DiagnosticSettings/* | Impostazioni di diagnostica di lettura, scrittura ed eliminazione. |
| Microsoft.Insights/eventtypes/* | Elenco degli eventi dei registri attività (eventi di gestione) in una sottoscrizione. Questa autorizzazione è applicabile sia all'accesso programmatico che all'accesso al portale per il registro attività. |
| Microsoft.Insights/LogDefinitions/* | Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai registri attività tramite il portale. Elencare categorie di log nel log attività. |
| Microsoft.Insights/MetricDefinitions/* | Definizioni delle metriche (elenco dei tipi di metriche disponibili per una risorsa). |
| Microsoft.Insights/Metrics/* | Metriche per una risorsa. |
| Microsoft.Insights/Register/Action | Registra il provider Microsoft.Insights. |
| Microsoft.Insights/webtests/* | Leggere, scrivere ed eliminare test Web di Application Insights. |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Leggere, scrivere ed eliminare pacchetti di soluzioni Log Analytics. |
| Microsoft.OperationalInsights/workspaces/savedSearches/* | Leggere, scrivere ed eliminare ricerche salvate di Log Analytics. |
| Microsoft.OperationalInsights/workspaces/search/action | Cercare aree di lavoro di Log Analytics. |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action | Elencare chiavi per un'area di lavoro di Log Analytics. |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Leggere, scrivere ed eliminare configurazione di dati di archiviazione di Log Analytics. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |
| Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Lettore di monitoraggio
Può leggere tutti i dati del monitoraggio (metriche, log e così via). Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Actions** |  |
| --- | --- |
| */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
| Microsoft.OperationalInsights/workspaces/search/action | Ricerca di dati in Log Analytics |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="network-contributor"></a>Collaboratore di rete
È in grado di gestire tutte le risorse di rete

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Network/* | Creare e gestire reti |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="new-relic-apm-account-contributor"></a>Collaboratore account New Relic APM
Consente di gestire gli account e le applicazioni di APR New Relic, ma non di accedervi.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |
| NewRelic.APM/accounts/* |  |

## <a name="redis-cache-contributor"></a>Collaboratore cache Redis
È in grado di gestire le cache Redis

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Cache/redis/* | Creare e gestire cache Redis |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="scheduler-job-collections-contributor"></a>Collaboratore raccolte di processi dell'unità di pianificazione
È in grado di gestire raccolte di processi dell'utilità di pianificazione

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Scheduler/jobcollections/* | Creare e gestire raccolte di processi |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="search-service-contributor"></a>Collaboratore servizi di ricerca
È in grado di gestire servizi di ricerca

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Search/searchServices/* | È in grado di creare e gestire servizi di ricerca |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="security-admin"></a>Amministrazione della protezione
Solo in Centro sicurezza: è possibile visualizzare i criteri di sicurezza e gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare gli avvisi e le raccomandazioni, ignorare gli avvisi e le raccomandazioni

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Authorization/policyAssignments/* | Creare e gestire assegnazioni di criteri |
| Microsoft.Authorization/policyDefinitions/* | Creare e gestire definizioni di criteri |
| Microsoft.Authorization/policySetDefinitions/* | Creare e gestire set di criteri |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.operationalInsights/workspaces/*/read | Visualizzare i dati di Log Analytics |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Security/*/read | Leggere criteri e componenti di sicurezza |
| Microsoft.Security/locations/alerts/dismiss/action | Ignora un avviso di sicurezza |
| Microsoft.Security/locations/tasks/dismiss/action | Ignora un consiglio per la sicurezza |
| Microsoft.Security/policies/write | Aggiorna i criteri di sicurezza |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="security-manager"></a>Gestore della sicurezza SQL
Può gestire i componenti di protezione, i criteri di sicurezza e le macchine virtuali

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.ClassicCompute/*/read | Leggere le informazioni di configurazione delle macchine virtuali classiche |
| Microsoft.ClassicCompute/virtualMachines/*/write | Scrivere la configurazione delle macchine virtuali classiche |
| Microsoft.ClassicNetwork/*/read | Leggere le informazioni della configurazione sulla rete classica |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Security/* | Creare e gestire criteri e componenti di protezione |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="security-reader"></a>Ruolo con autorizzazioni di lettura per la sicurezza
Solo in Centro sicurezza: è possibile visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non è possibile apportare modifiche

| **Actions** |  |
| --- | --- |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.operationalInsights/workspaces/*/read | Visualizzare i dati di Log Analytics |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Support/* | Creare e gestire ticket di supporto |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Security/*/read | Leggere criteri e componenti di sicurezza |

## <a name="site-recovery-contributor"></a>Collaboratore al ripristino sito
Consente di gestire tutte le azioni per il ripristino del sito, ad eccezione della creazione dell'insieme di credenziali di Servizi di ripristino e l'assegnazione dei diritti di accesso ad altri utenti

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Network/virtualNetworks/read | Leggere reti virtuali |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp è un'operazione interna usata dal servizio |
| Microsoft.RecoveryServices/Vaults/certificates/write | Consente di aggiornare il certificato delle credenziali dell'insieme di credenziali |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Consente di creare e gestire informazioni estese relative all'insieme di credenziali |
| Microsoft.RecoveryServices/Vaults/read | Consente di leggere l'insieme di credenziali dei servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Consente di gestire le operazioni di individuazione per il recupero dei nuovi contenitori creati |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Consente di creare e gestire le identità registrate |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Consente di creare o aggiornare le impostazioni degli avvisi di replica |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Consente di leggere gli eventi di replica |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | Consente di creare e gestire le infrastrutture di replica |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Consente di creare e gestire i processi di replica |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | Consente di creare e gestire i criteri di replica |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Consente di creare e gestire i piani di ripristino |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Consente di creare e gestire la configurazione di archiviazione dell'insieme di credenziali di Servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Consente di leggere le informazioni relative al token dell'insieme di credenziali di Servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/usages/read | Consente di leggere i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali. |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Consente di leggere gli avvisi per l'insieme di credenziali dei servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Consente di leggere la configurazione delle notifiche dell'insieme di credenziali dei servizi di ripristino |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Storage/storageAccounts/read | Leggere account di archiviazione |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="site-recovery-operator"></a>Operatore del ripristino sito
Può eseguire failover e failback ma non può eseguire altre azioni di gestione di ripristino del sito o assegnare l'accesso ad altri utenti

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.Network/virtualNetworks/read | Leggere reti virtuali |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp è un'operazione interna usata dal servizio |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Consente di leggere informazioni estese relative all'insieme di credenziali |
| Microsoft.RecoveryServices/Vaults/read | Consente di leggere l'insieme di credenziali dei servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Consente di gestire le operazioni di individuazione per il recupero dei nuovi contenitori creati |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Consente di leggere lo stato e il risultato di un'operazione inviata |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Consente di leggere i contenitori registrati per una risorsa |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Consente di leggere le impostazioni degli avvisi di replica |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Consente di leggere gli eventi di replica |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Consente di verificare la coerenza delle infrastrutture |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Consente di leggere le infrastrutture di replica |
| Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Consente di riassociare un gateway di replica |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Consente di rinnovare il certificato dell'infrastruttura di replica |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Consente di leggere le reti delle infrastrutture di replica |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | Consente di leggere il mapping delle reti delle infrastrutture di replica |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Consente di leggere i contenitori di protezione |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Restituisce l'elenco di tutti gli elementi da proteggere |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Applica punto di ripristino |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Commit del failover |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Failover pianificato |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Restituisce l'elenco di tutti gli elementi protetti |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Consente di ottenere l'elenco di punti di recupero disponibili |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Ripristina replica |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/reProtect/action | Consente di avviare nuovamente la protezione per un elemento protetto |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/action | Consente di avviare il failover di test di un elemento protetto |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Pulizia del failover di test |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aggiorna servizio Mobility |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Consente di leggere i mapping dei contenitori di protezione |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Consente di leggere tutti i provider dei servizi di ripristino |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/action | Consente di aggiornare tutti i provider dei servizi di ripristino |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Consente di configurare classificazioni di archiviazione per le infrastrutture di replica |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | Consente di leggere tutti i mapping di classificazioni di archiviazione |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Consente di leggere le informazioni registrate di vCenter |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Consente di creare e gestire i processi di replica |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Consente di leggere i criteri di replica |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Consente di eseguire il commit di failover per il failover del piano di ripristino |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Consente di avviare il failover di un piano di ripristino |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Consente di leggere i piani di ripristino |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Consente di iniziare a proteggere nuovamente un piano di ripristino |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Consente di avviare il failover di test di un piano di ripristino |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Consente di avviare la pulizia di un failover di test del piano di ripristino |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Consente di avviare il failover non pianificato di un piano di ripristino |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Consente di leggere gli avvisi per l'insieme di credenziali dei servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Consente di leggere la configurazione delle notifiche dell'insieme di credenziali dei servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Consente di leggere la configurazione di archiviazione di un insieme di credenziali di Servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Consente di leggere le informazioni relative al token dell'insieme di credenziali di Servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/usages/read | Consente di leggere i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Storage/storageAccounts/read | Leggere account di archiviazione |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="site-recovery-reader"></a>Reader di ripristino sito
Può monitorare lo stato di ripristino del sito nell'insieme di credenziali di servizi di ripristino e generare i ticket di supporto

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Consente di leggere informazioni estese relative all'insieme di credenziali |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Consente di leggere gli avvisi per l'insieme di credenziali dei servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Consente di leggere la configurazione delle notifiche dell'insieme di credenziali dei servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/read | Consente di leggere l'insieme di credenziali dei servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Consente di gestire le operazioni di individuazione per il recupero dei nuovi contenitori creati |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Consente di leggere lo stato e il risultato di un'operazione inviata |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Consente di leggere i contenitori registrati per una risorsa |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Consente di leggere le impostazioni degli avvisi di replica |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Consente di leggere gli eventi di replica |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Consente di leggere le infrastrutture di replica |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Consente di leggere le reti delle infrastrutture di replica |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | Consente di leggere il mapping delle reti delle infrastrutture di replica |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Consente di leggere i contenitori di protezione |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Restituisce l'elenco di tutti gli elementi da proteggere |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Restituisce l'elenco di tutti gli elementi protetti |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Consente di ottenere l'elenco di punti di recupero disponibili |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Consente di leggere i mapping dei contenitori di protezione |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Consente di leggere tutti i provider dei servizi di ripristino |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Consente di configurare classificazioni di archiviazione per le infrastrutture di replica |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | Consente di leggere tutti i mapping di classificazioni di archiviazione |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Consente di leggere le informazioni registrate di vCenter |
| Microsoft.RecoveryServices/vaults/replicationJobs/read | Consente di leggere lo stato dei processi di replica |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Consente di leggere i criteri di replica |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Consente di leggere i piani di ripristino |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Consente di leggere la configurazione di archiviazione di un insieme di credenziali di Servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Consente di leggere le informazioni relative al token dell'insieme di credenziali di Servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/usages/read | Consente di leggere i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="sql-db-contributor"></a>Collaboratore database SQL
Può gestire i database SQL, ma non i criteri correlati alla sicurezza

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/databases/* | Creare e gestire database SQL |
| Microsoft.Sql/servers/read | È in grado di leggere i server SQL |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Impossibile modificare i criteri di controllo |
| Microsoft.Sql/servers/databases/auditingSettings/* | Impossibile modificare le impostazioni di controllo |
| Microsoft.Sql/servers/databases/auditRecords/read | Non può leggere i record di controllo |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Impossibile modificare i criteri di connessione |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Impossibile modificare i criteri di mascheratura dei dati |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Impossibile modificare i criteri di avviso di sicurezza |
| Microsoft.Sql/servers/databases/securityMetrics/* | Impossibile modificare i criteri di protezione |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>Gestione della sicurezza SQL
Può gestire i criteri correlati alla sicurezza di SQL Server e database SQL

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere l'autorizzazione Microsoft |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Sql/servers/auditingPolicies/* | Creare e gestire criteri di controllo di server SQL |
| Microsoft.Sql/servers/auditingSettings/* | Creare e gestire le impostazioni di controllo di SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Creare e gestire i criteri di controllo dei database SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* | Creare e gestire le impostazioni di controllo dei database di SQL Server |
| Microsoft.Sql/servers/databases/auditRecords/read | Legge i record di controllo |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Creare e gestire i criteri di connessione dei database dei server SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Creare e gestire i criteri della maschera dei dati dei database dei server SQL |
| Microsoft.Sql/servers/databases/read | Leggere database SQL |
| Microsoft.Sql/servers/databases/schemas/read | Leggere schemi di database di lettura di server SQL |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Leggere colonne della tabella del database di server SQL |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/read | Leggere tabelle di database di server SQL |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Creare e gestire i criteri degli avvisi di sicurezza dei database di SQL Server |
| Microsoft.Sql/servers/databases/securityMetrics/* | Creare e gestire le metriche di sicurezza dei database di server SQL |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/firewallRules/* |  |
| Microsoft.Sql/servers/read | È in grado di leggere i server SQL |
| Microsoft.Sql/servers/securityAlertPolicies/* | Creare e gestire i criteri degli avvisi di sicurezza di SQL Server |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="sql-server-contributor"></a>Collaboratore SQL Server
Può gestire server e database SQL, ma non i criteri di protezione correlati

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/* | Creare e gestire server SQL |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* | Non è in grado di modificare i criteri di controllo di server SQL |
| Microsoft.Sql/servers/auditingSettings/* | Non è in grado di modificare le impostazioni di controllo di SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Non è in grado di modificare i criteri di controllo dei database di server SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* | Non è in grado di modificare le impostazioni di controllo dei database di SQL Server |
| Microsoft.Sql/servers/databases/auditRecords/read | Non può leggere i record di controllo |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Non è in grado di modificare i criteri di connessione dei database di server SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Non è in grado di modificare i criteri di mascheratura dei dati dei database di server SQL |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Non è in grado di modificare i criteri degli avvisi di sicurezza dei database di SQL server |
| Microsoft.Sql/servers/databases/securityMetrics/* | Non è in grado di modificare le metriche di protezione dei database di server SQL |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/securityAlertPolicies/* | Non è in grado di modificare i criteri degli avvisi di sicurezza di SQL Server |

## <a name="storage-account-contributor"></a>Collaboratore account di archiviazione
Può gestire gli account di archiviazione, ma non accedervi.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere tutte le autorizzazioni |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Insights/diagnosticSettings/* | Gestire le impostazioni di diagnostica |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Storage/storageAccounts/* | Creare e gestire account di archiviazione |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="storage-account-key-operator-service-role"></a>Ruolo del servizio dell'operatore della chiave dell'account di archiviazione
Gli operatori della chiave dell'account di archiviazione sono autorizzati a elencare e rigenerare le chiavi negli account di archiviazione

| **Actions** |  |
| --- | --- |
| Microsoft.Storage/storageAccounts/listkeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
| Microsoft.Storage/storageAccounts/regeneratekey/action | Rigenera le chiavi di accesso per l'account di archiviazione specificato. |

## <a name="support-request-contributor"></a>Collaboratore alla richiesta di supporto
Consente di creare e gestire i ticket di supporto nell'ambito della sottoscrizione

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="traffic-manager-contributor"></a>Collaboratore Gestione traffico
Consente di gestire i profili di Gestione traffico, ma non di controllare chi è autorizzato ad accedervi.

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Network/trafficManagerProfiles/* |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="user-access-administrator"></a>Amministratore accessi utente
Consente di gestire l'accesso degli utenti alle risorse di Azure

| **Actions** |  |
| --- | --- |
| */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
| Microsoft.Authorization/* | Gestire l'autorizzazione |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="virtual-machine-administrator-login"></a>Virtual Machine Administrator Login (Accesso amministratore macchina virtuale)
-   Gli utenti con questo ruolo hanno la possibilità di accedere a una macchina virtuale con privilegi di amministratore di Windows o di utente root Linux.

| **Actions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="virtual-machine-contributor"></a>Collaboratore macchine virtuali
È in grado di gestire macchine virtuali, ma non la rete virtuale o l'account di archiviazione a cui sono connesse

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.Compute/availabilitySets/* | Creare e gestire set di disponibilità di calcolo |
| Microsoft.Compute/locations/* | Creare e gestire percorsi di calcolo |
| Microsoft.Compute/virtualMachines/* | Creare e gestire macchine virtuali |
| Microsoft.Compute/virtualMachineScaleSets/* | Creare e gestire i set di scalabilità delle macchine virtuali |
| Microsoft.DevTestLab/schedules/* |  |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Partecipare a pool di indirizzi backend di gateway delle applicazioni di rete |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Partecipare a pool di indirizzi backend di servizi di bilanciamento del carico |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Aggiungere pool NAT di bilanciamento del carico in entrata |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Partecipa a regole NAT in entrata di servizi di bilanciamento del carico |
| Microsoft.Network/loadBalancers/probes/join/action | Consente l'uso di probe di un servizio di bilanciamento del carico. Con questa autorizzazione, ad esempio, la proprietà healthProbe di un set di scalabilità di macchine virtuali può fare riferimento al probe. |
| Microsoft.Network/loadBalancers/read | Leggere servizi di bilanciamento del carico |
| Microsoft.Network/locations/* | Creare e gestire percorsi di rete |
| Microsoft.Network/networkInterfaces/* | Creare e gestire interfacce di rete |
| Microsoft.Network/networkSecurityGroups/join/action | Partecipare a gruppi di sicurezza di rete |
| Microsoft.Network/networkSecurityGroups/read | Leggere gruppi di sicurezza di rete |
| Microsoft.Network/publicIPAddresses/join/action | Partecipare a indirizzi IP pubblici di rete |
| Microsoft.Network/publicIPAddresses/read | Leggere indirizzi IP pubblici di rete |
| Microsoft.Network/virtualNetworks/read | Leggere reti virtuali |
| Microsoft.Network/virtualNetworks/subnets/join/action | Partecipare a subnet di reti virtuali |
| Microsoft.RecoveryServices/locations/* |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/*/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Restituisce i dettagli dell'oggetto dell'elemento protetto |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | Crea un elemento protetto di backup |
| Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Crea una finalità di protezione di backup |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Restituisce tutti i criteri di protezione |
| Microsoft.RecoveryServices/Vaults/backupPolicies/write | Crea i criteri di protezione |
| Microsoft.RecoveryServices/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
| Microsoft.RecoveryServices/Vaults/usages/read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
| Microsoft.RecoveryServices/Vaults/write | L'operazione Crea insieme di credenziali crea una risorsa di Azure di tipo 'vault' |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Storage/storageAccounts/listKeys/action | Ottenere chiavi degli account di archiviazione |
| Microsoft.Storage/storageAccounts/read | Leggere account di archiviazione |
| Microsoft.Support/* | Creare e gestire ticket di supporto |

## <a name="virtual-machine-user-login"></a>Virtual Machine User Login (Accesso utente macchina virtuale)
Gli utenti con questo ruolo hanno la possibilità di accedere a una macchina virtuale come utente normale.

| **Actions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="web-plan-contributor"></a>Collaboratore piani Web
È in grado di gestire piani Web

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |
| Microsoft.Web/serverFarms/* | Creare e gestire server farm |

## <a name="website-contributor"></a>Collaboratore siti Web
È in grado di gestire siti Web, ma non i piani cui sono connessi

| **Actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizzazione Lettura |
| Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
| Microsoft.Insights/components/* | È in grado di creare e gestire i componenti di Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leggere l'integrità delle risorse |
| Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leggere gruppi di risorse |
| Microsoft.Support/* | Creare e gestire ticket di supporto |
| Microsoft.Web/certificates/* | Creare e gestire certificati dei siti Web |
| Microsoft.Web/listSitesAssignedToHostName/read | Leggere siti di lettura assegnati a un nome host |
| Microsoft.Web/serverFarms/join/action | Partecipare a server farm |
| Microsoft.Web/serverFarms/read | Leggere server farm |
| Microsoft.Web/sites/* | Creare e gestire siti Web. Per creare un sito sono anche necessarie le autorizzazione di scrittura associate al piano di servizio app |

## <a name="see-also"></a>Vedere anche 
* [Controllo degli accessi in base al ruolo](role-based-access-control-configure.md): introduzione al controllo degli accessi in base al ruolo nel portale di Azure.
* [Ruoli personalizzati nel controllo degli accessi in base al ruolo di Azure](role-based-access-control-custom-roles.md): informazioni su come creare ruoli personalizzati per esigenze di accesso specifiche.
* [Creare un report della cronologia delle modifiche relative all'accesso](role-based-access-control-access-change-history-report.md): tenere traccia delle modifiche nelle assegnazioni dei ruoli nel controllo degli accessi in base al ruolo.
* [Risoluzione dei problemi del controllo degli accessi in base al ruolo](role-based-access-control-troubleshooting.md): suggerimenti per la risoluzione di problemi comuni.
* [Autorizzazioni nel Centro sicurezza di Azure](../security-center/security-center-permissions.md)
