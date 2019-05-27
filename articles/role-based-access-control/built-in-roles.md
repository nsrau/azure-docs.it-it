---
title: Ruoli predefiniti per le risorse di Azure | Microsoft Docs
description: Descrive i ruoli predefiniti per il controllo degli accessi in base al ruolo e le risorse di Azure. Elenca Actions, NotActions, DataActions e NotDataActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 5d964ebd40ac1bd8817bc93d5e4f78096f221be2
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65977758"
---
# <a name="built-in-roles-for-azure-resources"></a>Ruoli predefiniti per le risorse di Azure

Il [controllo degli accessi in base al ruolo](overview.md) ha diversi ruoli predefiniti per le risorse di Azure che è possibile assegnare a utenti, gruppi, entità servizio e identità gestite. Le assegnazioni di ruolo sono il modo in cui si controlla l'accesso alle risorse di Azure. Se i ruoli predefiniti non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare [ruoli personalizzati per le risorse di Azure](custom-roles.md).

Questo articolo illustra i ruoli predefiniti per le risorse di Azure, che sono in continua evoluzione. Per ottenere i ruoli più recenti, usare [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) o [az role definition list](/cli/azure/role/definition#az-role-definition-list). Se si cercano ruoli di amministratore per Azure Active Directory, vedere [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Descrizione dei ruoli predefiniti

La tabella seguente fornisce una breve descrizione di ogni ruolo predefinito. Fare clic sul nome del ruolo per vedere l'elenco di `Actions`, `NotActions`, `DataActions` e `NotDataActions` per ogni ruolo. Per informazioni sul significato di queste azioni e su come si applicano ai piani di gestione e ai piani dati, vedere [Informazioni sulle definizioni del ruolo per le risorse di Azure](role-definitions.md).


| Ruolo predefinito | Descrizione |
| --- | --- |
| [Proprietario](#owner) | Consente di gestire tutto, incluso l'accesso alle risorse. |
| [Collaboratore](#contributor) | Consente di gestire tutto, tranne l'accesso alle risorse. |
| [Lettore](#reader) | Consente di visualizzare tutti gli elementi, ma senza apportare alcuna modifica. |
| [AcrDelete](#acrdelete) | Eliminazione registro contenitori di Azure |
| [AcrImageSigner](#acrimagesigner) | firmatario immagine acr |
| [AcrPull](#acrpull) | acr pull |
| [AcrPush](#acrpush) | acr push |
| [AcrQuarantineReader](#acrquarantinereader) | lettore di dati di quarantena acr |
| [AcrQuarantineWriter](#acrquarantinewriter) | writer di dati di quarantena acr |
| [Collaboratore servizio Gestione API](#api-management-service-contributor) | Può gestire il servizio e le API. |
| [Ruolo operatore del servizio Gestione API](#api-management-service-operator-role) | Può gestire il servizio ma non le API. |
| [Ruolo lettura del servizio Gestione API](#api-management-service-reader-role) | Consente l'accesso di sola lettura al servizio e alle API. |
| [Collaboratore componente di Application Insights](#application-insights-component-contributor) | È in grado di gestire i componenti di Application Insights |
| [Debugger di snapshot di Application Insights](#application-insights-snapshot-debugger) | Concede l'autorizzazione utente per visualizzare e scaricare gli snapshot di debug raccolti con Application Insights Snapshot Debugger. Si noti che queste autorizzazioni non sono incluse nei ruoli [Proprietario](#owner) e [Collaboratore](#contributor). |
| [Operatore processo di automazione](#automation-job-operator) | Consente di creare e gestire i processi tramite i runbook di Automazione. |
| [Operatore di automazione](#automation-operator) | Gli operatori di automazione possono avviare, arrestare, sospendere e riprendere processi. |
| [Operatore runbook di automazione](#automation-runbook-operator) | Consente di leggere le proprietà del runbook per permettere di creare processi del runbook. |
| [Collaboratore di avere](#avere-contributor) | Creare e gestire un cluster di vFXT Avere. |
| [Operatore di avere](#avere-operator) | Usato dal cluster vFXT Avere per gestire il cluster |
| [Ruolo di amministratore del cluster del servizio Azure Kubernetes](#azure-kubernetes-service-cluster-admin-role) | Elencare l'azione delle credenziali di amministratore del cluster. |
| [Ruolo di utente del cluster del servizio Azure Kubernetes](#azure-kubernetes-service-cluster-user-role) | Elencare l'azione delle credenziali di utente del cluster. |
| [Lettore di dati di mappe di Azure (anteprima)](#azure-maps-data-reader-preview) | Concede l'accesso per la lettura dei dati correlati alle mappe da un account Mappe di Azure. |
| [Proprietario della registrazione di Azure Stack](#azure-stack-registration-owner) | Consente di gestire le registrazioni di Azure Stack. |
| [Collaboratore di backup](#backup-contributor) | Consente di gestire il servizio di backup, ma non di creare insiemi di credenziali e concedere l'accesso ad altri utenti. |
| [Operatore di backup](#backup-operator) | Consente di gestire i servizi di backup, ma non di rimuovere il backup, creare insiemi di credenziali e concedere l'accesso ad altri utenti. |
| [Lettore di backup](#backup-reader) | Può visualizzare i servizi di backup, ma non può apportare modifiche. |
| [Lettore per la fatturazione](#billing-reader) | Consente l'accesso in lettura ai dati di fatturazione. |
| [Collaboratore BizTalk](#biztalk-contributor) | Consente di gestire i servizi BizTalk, ma non di accedervi. |
| [Accesso al nodo membro Blockchain (anteprima)](#blockchain-member-node-access-preview) | Consente l'accesso ai nodi di tipo membro della blockchain |
| [Collaboratore endpoint rete CDN](#cdn-endpoint-contributor) | Può gestire gli endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti. |
| [Lettore endpoint rete CDN](#cdn-endpoint-reader) | Può visualizzare gli endpoint della rete CDN, ma non può apportare modifiche. |
| [Collaboratore profilo rete CDN](#cdn-profile-contributor) | Può gestire i profili e i rispettivi endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti. |
| [Lettore profilo rete CDN](#cdn-profile-reader) | Può visualizzare i profili e i rispettivi endpoint della rete CDN, ma non può apportare modifiche. |
| [Collaboratore reti virtuali classiche](#classic-network-contributor) | Consente di gestire le reti classiche, ma non di accedervi. |
| [Collaboratore account di archiviazione classico](#classic-storage-account-contributor) | Consente di gestire gli account di archiviazione classici, ma non di accedervi. |
| [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico](#classic-storage-account-key-operator-service-role) | Gli operatori della chiave dell'account di archiviazione classico sono autorizzati a elencare e rigenerare le chiavi negli account di archiviazione classici |
| [Collaboratore macchine virtuali classiche](#classic-virtual-machine-contributor) | Consente di gestire le macchine virtuali classiche, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse. |
| [Collaboratore Servizi cognitivi](#cognitive-services-contributor) | Consente di creare, leggere, aggiornare, eliminare e gestire le chiavi di Servizi cognitivi. |
| [Lettore di dati di servizi cognitivi (anteprima)](#cognitive-services-data-reader-preview) | Consente di leggere i dati di Servizi cognitivi. |
| [Utente Servizi cognitivi](#cognitive-services-user) | Consente di leggere ed elencare le chiavi di Servizi cognitivi. |
| [Ruolo Lettore dell'account Cosmos DB](#cosmos-db-account-reader-role) | Può leggere i dati degli account Azure Cosmos DB. Vedere [Collaboratore account DocumentDB](#documentdb-account-contributor) per la gestione degli account Azure Cosmos DB. |
| [Operatore di COSMOS DB](#cosmos-db-operator) | Consente di gestire gli account Azure Cosmos DB, ma non accedere ai dati in essi contenuti. Impedisce l'accesso alle chiavi di account e le stringhe di connessione. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Può inviare una richiesta di ripristino per un database di Cosmos DB o un contenitore per un account |
| [Collaboratore Gestione costi](#cost-management-contributor) | Può visualizzare i costi e gestire la configurazione dei costi, ad esempio budget ed esportazioni |
| [Lettore Gestione costi](#cost-management-reader) | Può visualizzare la configurazione e i dati dei costi, ad esempio budget ed esportazioni |
| [Collaboratore Data Box](#data-box-contributor) | Consente di gestire tutto il servizio Data Box, ad eccezione della concessione dell'accesso ad altri utenti. |
| [Lettore Data Box](#data-box-reader) | Consente di gestire il servizio Data Box, ad eccezione della creazione di ordini, della modifica dei dettagli dell'ordine e della concessione dell'accesso ad altri utenti. |
| [Collaboratore Data Factory](#data-factory-contributor) | Consente di creare e gestire data factory, oltre alle risorse figlio in esse contenute. |
| [Sviluppatore di Data Lake Analytics](#data-lake-analytics-developer) | Consente di inviare, monitorare e gestire i propri processi, ma non di creare o eliminare account Data Lake Analytics. |
| [Pulizia dati](#data-purger) | Può eliminare i dati di analisi |
| [Utente DevTest Labs](#devtest-labs-user) | Consente di connettere, avviare, riavviare e arrestare le macchine virtuali in Azure DevTest Labs. |
| [Collaboratore zona DNS](#dns-zone-contributor) | Consente di gestire le zone DNS e i set di record in DNS di Azure, ma non di controllare chi è autorizzato ad accedervi. |
| [Collaboratore account DocumentDB](#documentdb-account-contributor) | È in grado di gestire account Azure Cosmos DB. Azure Cosmos DB era precedentemente noto come DocumentDB. |
| [Proprietario dei dati di hub eventi](#event-hubs-data-owner) | Consente di accedere alle risorse di hub eventi di Azure | 
| [Collaboratore per sottoscrizioni di eventi di Griglia di eventi](#eventgrid-eventsubscription-contributor) | Consente di gestire le operazioni di sottoscrizione di eventi EventGrid. |
| [Ruolo con autorizzazioni di lettura per sottoscrizioni di eventi di Griglia di eventi](#eventgrid-eventsubscription-reader) | Consente di leggere le sottoscrizioni di eventi EventGrid. |
| [Operatore del Cluster HDInsight](#hdinsight-cluster-operator) | Consente di leggere e modificare le configurazioni dei cluster HDInsight. |
| [Collaboratore Servizi di dominio HDInsight](#hdinsight-domain-services-contributor) | Può leggere, creare, modificare ed eliminare operazioni correlate ai Servizi di dominio necessarie per HDInsight Enterprise Security Package |
| [Collaboratore account Intelligent Systems](#intelligent-systems-account-contributor) | Consente di gestire gli account Sistemi intelligenti, ma non di accedervi. |
| [Collaboratore di Key Vault](#key-vault-contributor) | Consente di gestire gli insiemi di credenziali delle chiavi, ma non di accedervi. |
| [Lab Creator](#lab-creator) (Creatore di lab) | Consente di creare, gestire ed eliminare i lab gestiti con gli account di Azure Lab. |
| [Collaboratore di Log Analytics](#log-analytics-contributor) | Il ruolo Collaboratore di Log Analytics può leggere tutti i dati di monitoraggio e modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log dall'Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di soluzioni e la configurazione di Diagnostica di Azure in tutte le risorse di Azure. |
| [Lettore di Log Analytics](#log-analytics-reader) | Il ruolo Lettore di Log Analytics può visualizzare ed eseguire ricerche in tutti i dati di monitoraggio e può visualizzare le impostazioni di monitoraggio, inclusa la visualizzazione della configurazione di Diagnostica di Azure in tutte le risorse di Azure. |
| [Collaboratore per app per la logica](#logic-app-contributor) | Consente di gestire le app per la logica, ma non di accedervi. |
| [Operatore per app per la logica](#logic-app-operator) | Consente di leggere, abilitare e disabilitare l'app per la logica. |
| [Ruolo di Operatore applicazione gestita](#managed-application-operator-role) | Consente di leggere ed eseguire azioni sulle risorse dell'applicazione gestita |
| [Lettore applicazioni gestite](#managed-applications-reader) | Consente di leggere le risorse in un accesso di app gestita e JIT richiesta. |
| [Managed Identity Contributor](#managed-identity-contributor) (Collaboratore per identità gestita) | Crea, legge, aggiorna ed elimina l'identità assegnata all'utente |
| [Managed Identity Operator](#managed-identity-operator) (Operatore per identità gestita) | Legge e assegna l'identità assegnata all'utente |
| [Collaboratore gruppo di gestione](#management-group-contributor) | Ruolo Collaboratore gruppo di gestione |
| [Lettore gruppo di gestione](#management-group-reader) | Ruolo Lettore gruppo di gestione |
| [Collaboratore al monitoraggio](#monitoring-contributor) | Può leggere tutti i dati del monitoraggio e modificare le impostazioni di monitoraggio. Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Autore delle metriche di monitoraggio](#monitoring-metrics-publisher) | Abilitare la pubblicazione di metriche nelle risorse di Azure |
| [Lettore di monitoraggio](#monitoring-reader) | Può leggere tutti i dati del monitoraggio (metriche, log e così via). Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Collaboratore di rete](#network-contributor) | Consente di gestire le reti, ma non di accedervi. |
| [Collaboratore account New Relic APM](#new-relic-apm-account-contributor) | Consente di gestire gli account e le applicazioni di APR New Relic, ma non di accedervi. |
| [Lettore e accesso ai dati](#reader-and-data-access) | Consente di visualizzare tutti gli elementi ma non consente di eliminare o creare un account di archiviazione o una risorsa contenuta. Consente anche l'accesso in lettura/scrittura a tutti i dati contenuti in un account di archiviazione tramite l'accesso alle chiavi dell'account di archiviazione. |
| [Collaboratore cache Redis](#redis-cache-contributor) | Consente di gestire le cache Redis, ma non di accedervi. |
| [Collaboratore ai criteri delle risorse (anteprima)](#resource-policy-contributor-preview) | (Anteprima) Utenti di cui sono state recuperate informazioni da EA, con diritti per la creazione/modifica di criteri delle risorse, la creazione di ticket di supporto e la lettura di risorse/gerarchia. |
| [Collaboratore raccolte di processi dell'unità di pianificazione](#scheduler-job-collections-contributor) | Consente di gestire le raccolte di processi dell'utilità di pianificazione, ma non di accedervi. |
| [Collaboratore servizi di ricerca](#search-service-contributor) | Consente di gestire i servizi di Ricerca, ma non di accedervi. |
| [Amministrazione della protezione](#security-admin) | Solo in Centro sicurezza: è possibile visualizzare i criteri di sicurezza e gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare gli avvisi e le raccomandazioni, ignorare gli avvisi e le raccomandazioni |
| [Gestore sicurezza (legacy)](#security-manager-legacy) | Questo è un ruolo legacy. Usare invece Amministratore della protezione |
| [Ruolo con autorizzazioni di lettura per la sicurezza](#security-reader) | Solo in Centro sicurezza: è possibile visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non è possibile apportare modifiche |
| [Proprietario dei dati del Bus di servizio](#service-bus-data-owner) | Consente l'accesso completo alle risorse del Bus di servizio di Azure |
| [Collaboratore al ripristino sito](#site-recovery-contributor) | Consente di gestire il servizio Site Recovery ad eccezione della creazione dell'insieme di credenziali e dell'assegnazione di ruolo. |
| [Operatore del ripristino sito](#site-recovery-operator) | Consente di eseguire il failover e il failback ma non di eseguire altre operazioni di gestione di Site Recovery. |
| [Reader di ripristino sito](#site-recovery-reader) | Consente di visualizzare lo stato di Site Recovery ma non di eseguire altre operazioni di gestione. |
| [Collaboratore Account Anchor spaziali](#spatial-anchors-account-contributor) | Consente di gestire gli ancoraggi nello spazio nell'account, ma non di eliminarli |
| [Proprietario dell'Account Anchor spaziali](#spatial-anchors-account-owner) | Consente di gestire gli ancoraggi nello spazio nell'account, incluse operazioni di eliminazione |
| [Gli ancoraggi spaziali Account lettore](#spatial-anchors-account-reader) | Consente di individuare e leggere le proprietà degli ancoraggi nello spazio nell'account |
| [Collaboratore database SQL](#sql-db-contributor) | Consente di gestire i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza o i rispettivi server SQL padre. |
| [Collaboratore di istanza gestita di SQL](#sql-managed-instance-contributor) | Consente di gestire le istanze gestite di SQL e necessaria la configurazione di rete, ma non è possibile concedere l'accesso ad altri utenti. |
| [Gestione della sicurezza SQL](#sql-security-manager) | Consente di gestire i criteri relativi alla sicurezza di server e database SQL, ma non di accedervi. |
| [Collaboratore SQL Server](#sql-server-contributor) | Consente di gestire i server e i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza. |
| [Collaboratore account di archiviazione](#storage-account-contributor) | Consente di gestire gli account di archiviazione, ma non di accedervi. |
| [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione](#storage-account-key-operator-service-role) | Gli operatori della chiave dell'account di archiviazione sono autorizzati a elencare e rigenerare le chiavi negli account di archiviazione |
| [Collaboratore ai dati del BLOB di archiviazione](#storage-blob-data-contributor) | Consente l'accesso in lettura, scrittura ed eliminazione ai contenitori BLOB e ai dati di Archiviazione di Azure |
| [Proprietario dei dati del BLOB di archiviazione](#storage-blob-data-owner) | Consente di ottenere l'accesso completo ai contenitori e ai dati dei BLOB di Archiviazione di Azure, inclusa l'assegnazione del controllo di accesso POSIX. |
| [Lettore dei dati del BLOB di archiviazione](#storage-blob-data-reader) | Consente l'accesso in lettura ai contenitori BLOB e ai dati di Archiviazione di Azure |
| [Collaboratore ai dati della coda di archiviazione](#storage-queue-data-contributor) | Consente l'accesso in lettura, scrittura ed eliminazione alle code e ai messaggi delle code di Archiviazione di Azure |
| [Processore di messaggi dati della coda di archiviazione](#storage-queue-data-message-processor) | Consente di visualizzare l'anteprima, ricevere ed eliminare l'accesso ai messaggi della coda di Archiviazione di Azure |
| [Mittente del messaggio di dati di archiviazione coda](#storage-queue-data-message-sender) | Consente l'invio di messaggi della coda di Archiviazione di Azure |
| [Lettore di dati della coda di archiviazione](#storage-queue-data-reader) | Consente l'accesso in lettura alle code e ai messaggi delle code di Archiviazione di Azure |
| [Collaboratore alla richiesta di supporto](#support-request-contributor) | Consente di creare e gestire le richieste di supporto. |
| [Collaboratore Gestione traffico](#traffic-manager-contributor) | Consente di gestire i profili di Gestione traffico, ma non di controllare chi è autorizzato ad accedervi. |
| [Amministratore accessi utente](#user-access-administrator) | Consente di gestire gli accessi utente alle risorse di Azure. |
| [Virtual Machine Administrator Login](#virtual-machine-administrator-login) (Accesso amministratore macchina virtuale) | Consente di visualizzare le macchine virtuali nel portale e di accedere come amministratore |
| [Collaboratore macchine virtuali](#virtual-machine-contributor) | Consente di gestire le macchine virtuali, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse. |
| [Virtual Machine User Login](#virtual-machine-user-login) (Accesso utente macchina virtuale) | Consente di visualizzare le macchine virtuali nel portale e di accedere come utente normale. |
| [Collaboratore piani Web](#web-plan-contributor) | Consente di gestire i piani Web per i siti Web, ma non di accedervi. |
| [Collaboratore siti Web](#website-contributor) | Consente di gestire i siti Web (non i piani Web), ma non di accedervi. |


## <a name="owner"></a>Proprietario
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire tutto, incluso l'accesso alle risorse. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Actions** |  |
> | * | È in grado di creare e gestire ogni tipo di risorsa |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="contributor"></a>Collaboratore
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire tutto, tranne l'accesso alle risorse. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Actions** |  |
> | * | È in grado di creare e gestire ogni tipo di risorsa |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Eliminare ruoli e assegnazioni di ruoli |
> | Microsoft.Authorization/*/Write | Creare ruoli e assegnazioni di ruoli |
> | Microsoft.Authorization/elevateAccess/Action | Concede al chiamante l'accesso di tipo Amministratore Accesso utenti a livello dell'ambito del tenant |
> | Microsoft.Blueprint/blueprintAssignments/write | Crea o aggiorna gli artefatti del progetto |
> | Microsoft.Blueprint/blueprintAssignments/delete | Elimina gli artefatti del progetto |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="reader"></a>Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di visualizzare tutti gli elementi, ma senza apportare alcuna modifica. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Actions** |  |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Eliminazione registro contenitori di Azure |
> | **Id** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Eliminazione dell'artefatto in un registro contenitori. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | firmatario immagine acr |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Eseguire il push/pull di metadati considerati attendibili per un registro contenitori. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | acr pull |
> | **Id** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Eseguire il pull o ottenere immagini da un registro contenitori. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | acr push |
> | **Id** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Eseguire il pull o ottenere immagini da un registro contenitori. |
> | Microsoft.ContainerRegistry/registries/push/write | Eseguire il push o scrivere immagini in un registro contenitori. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | lettore di dati di quarantena acr |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Eseguire il pull o ottenere immagini in quarantena da un registro contenitori |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | writer di dati di quarantena acr |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Eseguire il pull o ottenere immagini in quarantena da un registro contenitori |
> | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Scrivere/modificare lo stato di quarantena di immagini in quarantena |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="api-management-service-contributor"></a>Collaboratore servizio Gestione API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può gestire il servizio e le API. |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/* | È in grado di creare e gestire il servizio Gestione API |
> | Microsoft.Authorization/*/read | Autorizzazione Lettura |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="api-management-service-operator-role"></a>Ruolo operatore del servizio Gestione API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può gestire il servizio ma non le API. |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/*/read | Leggere le istanze del servizio Gestione API |
> | Microsoft.ApiManagement/service/backup/action | Esegue il backup del servizio Gestione API nel contenitore specificato in un account di archiviazione fornito dall’utente |
> | Microsoft.ApiManagement/service/delete | Elimina l’istanza del servizio Gestione API |
> | Microsoft.ApiManagement/service/managedeployments/action | Modifica SKU/unità, aggiunge/rimuove distribuzioni regionali del servizio Gestione API |
> | Microsoft.ApiManagement/service/read | Leggere i metadati per un'istanza del servizio Gestione API |
> | Microsoft.ApiManagement/service/restore/action | Ripristinare il servizio Gestione API dal contenitore specificato in un account di archiviazione fornito dall'utente |
> | Microsoft.ApiManagement/service/updatecertificate/action | Carica il certificato SSL per un servizio Gestione API. |
> | Microsoft.ApiManagement/service/updatehostname/action | Configura, aggiorna o rimuove i nomi di dominio personalizzati per un servizio Gestione API. |
> | Microsoft.ApiManagement/service/write | Crea una nuova istanza del servizio Gestione API |
> | Microsoft.Authorization/*/read | Autorizzazione Lettura |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Ottenere le chiavi associate all'utente |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="api-management-service-reader-role"></a>Ruolo lettura del servizio Gestione API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente l'accesso di sola lettura al servizio e alle API. |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/*/read | Leggere le istanze del servizio Gestione API |
> | Microsoft.ApiManagement/service/read | Leggere i metadati per un'istanza del servizio Gestione API |
> | Microsoft.Authorization/*/read | Autorizzazione Lettura |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Ottenere le chiavi associate all'utente |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="application-insights-component-contributor"></a>Collaboratore componente di Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | È in grado di gestire i componenti di Application Insights |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.Insights/components/* | È in grado di creare e gestire i componenti di Insights |
> | Microsoft.Insights/webtests/* | È in grado di creare e gestire i test Web |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="application-insights-snapshot-debugger"></a>Debugger di snapshot di Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Concede l'autorizzazione utente per visualizzare e scaricare gli snapshot di debug raccolti con Application Insights Snapshot Debugger. Si noti che queste autorizzazioni non sono incluse nei ruoli [Proprietario](#owner) e [Collaboratore](#contributor). |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="automation-job-operator"></a>Operatore processo di automazione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di creare e gestire i processi tramite i runbook di Automazione. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Legge le risorse del ruolo di lavoro ibrido per runbook |
> | Microsoft.Automation/automationAccounts/jobs/read | Ottiene un processo di automazione di Azure |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Riprende un processo di automazione di Azure |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Arresta un processo di automazione di Azure |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Ottiene un flusso del processo di automazione di Azure |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Sospende un processo di automazione di Azure |
> | Microsoft.Automation/automationAccounts/jobs/write | Crea un processo di automazione di Azure |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Ottiene l'output di un processo |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="automation-operator"></a>Operatore di automazione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Gli operatori di automazione possono avviare, arrestare, sospendere e riprendere processi. |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Legge le risorse del ruolo di lavoro ibrido per runbook |
> | Microsoft.Automation/automationAccounts/jobs/read | Ottiene un processo di automazione di Azure |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Riprende un processo di automazione di Azure |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Arresta un processo di automazione di Azure |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Ottiene un flusso del processo di automazione di Azure |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Sospende un processo di automazione di Azure |
> | Microsoft.Automation/automationAccounts/jobs/write | Crea un processo di automazione di Azure |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Ottiene una pianificazione del processo di automazione di Azure |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Crea una pianificazione del processo di automazione di Azure |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Ottiene l'area di lavoro collegata all'account di automazione |
> | Microsoft.Automation/automationAccounts/read | Ottiene un account di automazione di Azure |
> | Microsoft.Automation/automationAccounts/runbooks/read | Ottiene un runbook di automazione di Azure |
> | Microsoft.Automation/automationAccounts/schedules/read | Ottiene un asset della pianificazione di automazione di Azure |
> | Microsoft.Automation/automationAccounts/schedules/write | Crea o aggiorna un asset della pianificazione di automazione di Azure |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Ottiene l'output di un processo |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="automation-runbook-operator"></a>Operatore runbook di automazione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di leggere le proprietà del runbook per permettere di creare processi del runbook. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Automation/automationAccounts/runbooks/read | Ottiene un runbook di automazione di Azure |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="avere-contributor"></a>Collaboratore di avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Creare e gestire un cluster di vFXT Avere. |
> | **Id** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | Microsoft.Network/virtualNetworks/subnets/read | Ottiene una definizione di subnet della rete virtuale |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Aggiunge una rete virtuale. Non Alertable. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Non alertable. |
> | Microsoft.Network/networkSecurityGroups/join/action | Aggiunge un gruppo di sicurezza di rete. Non Alertable. |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Ottiene le risorse del gruppo di risorse. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Restituisce il risultato dell'eliminazione di un BLOB |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Restituisce un BLOB o un elenco di BLOB |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Restituisce il risultato della scrittura su un BLOB |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="avere-operator"></a>Operatore di avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Usato dal cluster vFXT Avere per gestire il cluster |
> | **Id** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Actions** |  |
> | Microsoft.Compute/virtualMachines/read | Ottiene le proprietà di una macchina virtuale |
> | Microsoft.Network/networkInterfaces/read | Ottiene una definizione dell’interfaccia di rete.  |
> | Microsoft.Network/networkInterfaces/write | Crea un'interfaccia di rete o ne aggiorna una esistente.  |
> | Microsoft.Network/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | Microsoft.Network/virtualNetworks/subnets/read | Ottiene una definizione di subnet della rete virtuale |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Aggiunge una rete virtuale. Non Alertable. |
> | Microsoft.Network/networkSecurityGroups/join/action | Aggiunge un gruppo di sicurezza di rete. Non Alertable. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Restituisce il risultato dell'eliminazione di un contenitore |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Restituisce l'elenco dei contenitori |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Restituisce il risultato dell'operazione PUT sul contenitore BLOB |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Restituisce il risultato dell'eliminazione di un BLOB |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Restituisce un BLOB o un elenco di BLOB |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Restituisce il risultato della scrittura su un BLOB |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Ruolo di amministratore del cluster del servizio Azure Kubernetes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Elencare l'azione delle credenziali di amministratore del cluster. |
> | **Id** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Actions** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Elenca la credenziale clusterAdmin di un cluster gestito |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Ruolo di utente del cluster del servizio Azure Kubernetes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Elencare l'azione delle credenziali di utente del cluster. |
> | **Id** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Actions** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Elenca la credenziale clusterUser di un cluster gestito |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="azure-maps-data-reader-preview"></a>Lettore di dati per Mappe di Azure (anteprima)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Concede l'accesso per la lettura dei dati correlati alle mappe da un account Mappe di Azure. |
> | **Id** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Maps/accounts/data/read | Concede l'accesso in lettura ai dati in un account di Mappe. |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="azure-stack-registration-owner"></a>Proprietario della registrazione di Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire le registrazioni di Azure Stack. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Actions** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Recupera i dettagli completi per un prodotto Azure Stack Marketplace |
> | Microsoft.AzureStack/registrations/products/read | Ottiene le proprietà di un prodotto Azure Stack Marketplace |
> | Microsoft.AzureStack/registrations/read | Ottiene le proprietà di una registrazione di Azure Stack |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="backup-contributor"></a>Collaboratore di backup
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire il servizio di backup, ma non di creare insiemi di credenziali e concedere l'accesso ad altri utenti. |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Network/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Consente di gestire i risultati dell'operazione sulla gestione del backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Consente di creare e gestire i contenitori di backup all'interno delle infrastrutture di backup dell'insieme di credenziali dei Servizi di ripristino |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Aggiorna l'elenco di contenitori |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Consente di creare e gestire i processi di backup |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Esporta processi |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Consente di creare e gestire i metadati relativi alla gestione di backup |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Consente di creare e gestire i risultati delle operazioni di gestione di backup |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Consente di creare e gestire i criteri di backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Consente di creare e gestire gli elementi su cui è possibile eseguire il backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Consente di creare e gestire gli elementi su cui è stato eseguito il backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Consente di creare e gestire i contenitori che contengono gli elementi di backup |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Consente di creare e gestire i certificati relativi al backup nell'insieme di credenziali dei Servizi di ripristino |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Consente di creare e gestire informazioni estese relative all'insieme di credenziali |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Consente di creare e gestire le identità registrate |
> | Microsoft.RecoveryServices/Vaults/usages/* | Consente di creare e gestire l'uso dell'insieme di credenziali dei Servizi di ripristino |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Storage/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Convalida l'operazione sull'elemento protetto |
> | Microsoft.RecoveryServices/Vaults/write | L'operazione Crea insieme di credenziali crea una risorsa di Azure di tipo 'vault' |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Restituisce lo stato dell'operazione di backup dell'insieme di credenziali di Servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Restituisce tutti i server di gestione di backup registrati nell'insieme di credenziali. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Ottiene tutti i contenitori che si possono proteggere |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Controlla lo stato del backup dell'insieme di credenziali di Servizi di ripristino |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Convalida le funzioni |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Risolve l'avviso. |
> | Microsoft.RecoveryServices/operations/read | L'operazione restituisce l'elenco delle operazioni per un provider di risorse |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Ottiene lo stato dell'operazione per una determinata operazione |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Elenca tutte le finalità di protezione del backup |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="backup-operator"></a>Operatore di backup
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire i servizi di backup, ma non di rimuovere il backup, creare insiemi di credenziali e concedere l'accesso ad altri utenti. |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Network/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Restituisce lo stato dell'operazione |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Ottiene il risultato dell'operazione eseguita sul contenitore di protezione. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Esegue il backup dell'elemento protetto. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Ottiene il risultato dell'operazione eseguita sugli elementi protetti. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Restituisce lo stato dell'operazione eseguita sugli elementi protetti. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Restituisce i dettagli dell'oggetto dell'elemento protetto |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Effettua il provisioning del ripristino elementi immediato per l'elemento protetto |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Ottiene i punti di ripristino degli elementi protetti. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Ripristina i punti di ripristino degli elementi protetti. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revoca il ripristino elementi immediato per l'elemento protetto |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Crea un elemento protetto di backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Restituisce tutti i contenitori registrati |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Aggiorna l'elenco di contenitori |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Consente di creare e gestire i processi di backup |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Esporta processi |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Consente di creare e gestire i risultati delle operazioni di gestione di backup |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Ottiene i risultati dell'operazione sui criteri. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Restituisce tutti i criteri di protezione |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Consente di creare e gestire gli elementi su cui è possibile eseguire il backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Restituisce l'elenco di tutti gli elementi protetti. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Restituisce tutti i contenitori che appartengono alla sottoscrizione |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | L'operazione Aggiorna certificato risorsa aggiorna il certificato delle credenziali della risorsa o dell'insieme di credenziali. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | L'operazione Registra contenitore di servizi può essere usata per registrare un contenitore con il servizio di ripristino. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Storage/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Convalida l'operazione sull'elemento protetto |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Restituisce lo stato dell'operazione di backup dell'insieme di credenziali di Servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Ottiene lo stato dell'operazione sui criteri. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Crea un contenitore registrato |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Esegue operazioni di richiesta di informazioni per i carichi di lavoro all'interno di un contenitore |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Restituisce tutti i server di gestione di backup registrati nell'insieme di credenziali. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Crea un programma di protezione del backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Ottiene una finalità di protezione del backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Ottiene tutti i contenitori che si possono proteggere |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Ottiene tutti gli elementi in un contenitore |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Controlla lo stato del backup dell'insieme di credenziali di Servizi di ripristino |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Convalida le funzioni |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Risolve l'avviso. |
> | Microsoft.RecoveryServices/operations/read | L'operazione restituisce l'elenco delle operazioni per un provider di risorse |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Ottiene lo stato dell'operazione per una determinata operazione |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Elenca tutte le finalità di protezione del backup |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="backup-reader"></a>Lettore di backup
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può visualizzare i servizi di backup, ma non può apportare modifiche. |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Restituisce lo stato dell'operazione |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Ottiene il risultato dell'operazione eseguita sul contenitore di protezione. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Ottiene il risultato dell'operazione eseguita sugli elementi protetti. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Restituisce lo stato dell'operazione eseguita sugli elementi protetti. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Restituisce i dettagli dell'oggetto dell'elemento protetto |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Ottiene i punti di ripristino degli elementi protetti. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Restituisce tutti i contenitori registrati |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Restituisce il risultato dell'operazione di processo. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Restituisce tutti gli oggetti processo |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Esporta processi |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Restituisce il risultato dell'operazione di backup di un insieme di credenziali di Servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Ottiene i risultati dell'operazione sui criteri. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Restituisce tutti i criteri di protezione |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Restituisce l'elenco di tutti gli elementi protetti. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Restituisce tutti i contenitori che appartengono alla sottoscrizione |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Restituisce la configurazione di archiviazione dell'insieme di credenziali di Servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Restituisce la configurazione dell'insieme di credenziali di Servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Restituisce lo stato dell'operazione di backup dell'insieme di credenziali di Servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Ottiene lo stato dell'operazione sui criteri. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Restituisce tutti i server di gestione di backup registrati nell'insieme di credenziali. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Ottiene una finalità di protezione del backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Ottiene tutti gli elementi in un contenitore |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Controlla lo stato del backup dell'insieme di credenziali di Servizi di ripristino |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Risolve l'avviso. |
> | Microsoft.RecoveryServices/operations/read | L'operazione restituisce l'elenco delle operazioni per un provider di risorse |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Ottiene lo stato dell'operazione per una determinata operazione |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Elenca tutte le finalità di protezione del backup |
> | Microsoft.RecoveryServices/Vaults/usages/read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="billing-reader"></a>Lettore per la fatturazione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente l'accesso in lettura ai dati di fatturazione. |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Billing/*/read | Lettura delle informazioni di fatturazione |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="biztalk-contributor"></a>Collaboratore BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire i servizi BizTalk, ma non di accedervi. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.BizTalkServices/BizTalk/* | È in grado di creare e gestire i servizi BizTalk |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="blockchain-member-node-access-preview"></a>Accesso ai nodi di tipo membro della blockchain (anteprima)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente l'accesso ai nodi di tipo membro della blockchain |
> | **Id** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Actions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Ottiene o elenca esistenti ai nodi membro delle transazioni di Blockchain. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Si connette a un nodo transazione membro Blockchain. |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="cdn-endpoint-contributor"></a>Collaboratore endpoint rete CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può gestire gli endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="cdn-endpoint-reader"></a>Lettore endpoint rete CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può visualizzare gli endpoint della rete CDN, ma non può apportare modifiche. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="cdn-profile-contributor"></a>Collaboratore profilo rete CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può gestire i profili e i rispettivi endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="cdn-profile-reader"></a>Lettore profilo rete CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può visualizzare i profili e i rispettivi endpoint della rete CDN, ma non può apportare modifiche. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="classic-network-contributor"></a>Collaboratore reti virtuali classiche
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire le reti classiche, ma non di accedervi. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorizzazione Lettura |
> | Microsoft.ClassicNetwork/* | Creare e gestire reti classiche |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="classic-storage-account-contributor"></a>Collaboratore account di archiviazione classico
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire gli account di archiviazione classici, ma non di accedervi. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorizzazione Lettura |
> | Microsoft.ClassicStorage/storageAccounts/* | Creare e gestire account di archiviazione |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Gli operatori della chiave dell'account di archiviazione classico sono autorizzati a elencare e rigenerare le chiavi negli account di archiviazione classici |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Actions** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Rigenera le chiavi di accesso esistenti per l'account di archiviazione. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="classic-virtual-machine-contributor"></a>Collaboratore macchine virtuali classiche
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire le macchine virtuali classiche, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorizzazione Lettura |
> | Microsoft.ClassicCompute/domainNames/* | Creare e gestire nomi di dominio di calcolo classici |
> | Microsoft.ClassicCompute/virtualMachines/* | Creare e gestire macchine virtuali |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Collega un IP riservato |
> | Microsoft.ClassicNetwork/reservedIps/read | Ottiene gli IP riservati |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Unisce la rete virtuale. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Ottiene la rete virtuale. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Restituisce il disco dell'account di archiviazione. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Restituisce l'immagine dell'account di archiviazione. Operazione deprecata: usare 'Microsoft.ClassicStorage/storageAccounts/vmImages'. |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | Microsoft.ClassicStorage/storageAccounts/read | Restituisce l'account di archiviazione con l'account specificato. |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="cognitive-services-contributor"></a>Collaboratore Servizi cognitivi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di creare, leggere, aggiornare, eliminare e gestire le chiavi di Servizi cognitivi. |
> | **Id** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Ottiene le funzionalità di una sottoscrizione. |
> | Microsoft.Features/providers/features/read | Ottiene la funzionalità di una sottoscrizione in un provider di risorse specificato. |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Insights/diagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | Microsoft.Insights/logDefinitions/read | Consente di leggere le definizioni del log |
> | Microsoft.Insights/metricdefinitions/read | Consente di leggere le definizioni della metrica |
> | Microsoft.Insights/metrics/read | Esegue la lettura delle metriche |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/deployments/operations/read | Ottiene o elenca le operazioni di distribuzione. |
> | Microsoft.Resources/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | Microsoft.Resources/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="cognitive-services-data-reader-preview"></a>Ruolo con autorizzazioni di lettura per i dati di Servizi cognitivi (anteprima)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di leggere i dati di Servizi cognitivi. |
> | **Id** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="cognitive-services-user"></a>Utente Servizi cognitivi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di leggere ed elencare le chiavi di Servizi cognitivi. |
> | **Id** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Actions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Elenco delle chiavi |
> | Microsoft.Insights/alertRules/read | Legge un avviso della metrica (versione classica) |
> | Microsoft.Insights/diagnosticSettings/read | Legge un'impostazione di diagnostica della risorsa |
> | Microsoft.Insights/logDefinitions/read | Consente di leggere le definizioni del log |
> | Microsoft.Insights/metricdefinitions/read | Consente di leggere le definizioni della metrica |
> | Microsoft.Insights/metrics/read | Esegue la lettura delle metriche |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/operations/read | Ottiene o elenca le operazioni di distribuzione. |
> | Microsoft.Resources/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | Microsoft.Resources/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="cosmos-db-account-reader-role"></a>Ruolo Lettore dell'account Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può leggere i dati degli account Azure Cosmos DB. Vedere [Collaboratore account DocumentDB](#documentdb-account-contributor) per la gestione degli account Azure Cosmos DB. |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere ruoli e assegnazioni di ruolo (è possibile leggere le autorizzazioni concesse a ogni utente) |
> | Microsoft.DocumentDB/*/read | Leggere tutte le raccolte |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Legge le chiavi di sola lettura degli account di database. |
> | Microsoft.Insights/MetricDefinitions/read | Consente di leggere le definizioni della metrica |
> | Microsoft.Insights/Metrics/read | Esegue la lettura delle metriche |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="cosmos-db-operator"></a>Operatore di COSMOS DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire gli account Azure Cosmos DB, ma non accedere ai dati in essi contenuti. Impedisce l'accesso alle chiavi di account e le stringhe di connessione. |
> | **Id** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Actions** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può inviare una richiesta di ripristino per un database di Cosmos DB o un contenitore per un account |
> | **Id** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Actions** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Invia una richiesta per configurare il backup |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Invia una richiesta di ripristino |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="cost-management-contributor"></a>Collaboratore Gestione costi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può visualizzare i costi e gestire la configurazione dei costi, ad esempio budget ed esportazioni |
> | **Id** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Actions** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read | Elenca i periodi di fatturazione disponibili |
> | Microsoft.Resources/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Advisor/configurations/read | Ottiene le configurazioni |
> | Microsoft.Advisor/recommendations/read | Legge i suggerimenti |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="cost-management-reader"></a>Lettore Gestione costi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può visualizzare la configurazione e i dati dei costi, ad esempio budget ed esportazioni |
> | **Id** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Actions** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read | Elenca i periodi di fatturazione disponibili |
> | Microsoft.Resources/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Advisor/configurations/read | Ottiene le configurazioni |
> | Microsoft.Advisor/recommendations/read | Legge i suggerimenti |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="data-box-contributor"></a>Collaboratore Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire tutto il servizio Data Box, ad eccezione della concessione dell'accesso ad altri utenti. |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="data-box-reader"></a>Lettore Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire il servizio Data Box, ad eccezione della creazione di ordini, della modifica dei dettagli dell'ordine e della concessione dell'accesso ad altri utenti. |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Elenca le credenziali non crittografate correlate all'ordine. |
> | Microsoft.Databox/locations/availableSkus/action | Restituisce l'elenco degli SKU disponibili. |
> | Microsoft.Databox/locations/validateAddress/action | Convalida l'indirizzo di spedizione e fornisce indirizzi alternativi, se disponibili. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="data-factory-contributor"></a>Collaboratore Data Factory
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di creare e gestire data factory, oltre alle risorse figlio in esse contenute. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.DataFactory/dataFactories/* | Creare e gestire data factory e le relative risorse figlio. |
> | Microsoft.DataFactory/factories/* | Creare e gestire data factory e le relative risorse figlio. |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="data-lake-analytics-developer"></a>Sviluppatore di Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di inviare, monitorare e gestire i propri processi, ma non di creare o eliminare account Data Lake Analytics. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Elimina un account Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Concede le autorizzazioni per annullare i processi inviati da altri utenti. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Crea o aggiorna un account Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Crea o aggiorna un account Data Lake Store collegato a un account Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Scollega un account Data Lake Store da un account Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Crea o aggiorna un account di archiviazione collegato a un account Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Scollega un account di archiviazione da un account Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Crea o aggiorna una regola del firewall. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Elimina una regola del firewall. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Crea o aggiorna criteri di calcolo. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Elimina criteri di calcolo. |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="data-purger"></a>Pulizia dati
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può eliminare i dati di analisi |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Actions** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Ripulitura dei dati da Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Elimina i dati specificati dall'area di lavoro |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="devtest-labs-user"></a>Utente DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di connettere, avviare, riavviare e arrestare le macchine virtuali in Azure DevTest Labs. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Compute/availabilitySets/read | Ottiene le proprietà di un set di disponibilità |
> | Microsoft.Compute/virtualMachines/*/read | Leggere le proprietà di una macchina virtuale (dimensioni della VM, stato di runtime, estensioni della VM e così via) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Disabilita la macchina virtuale e rilascia le risorse di calcolo |
> | Microsoft.Compute/virtualMachines/read | Ottiene le proprietà di una macchina virtuale |
> | Microsoft.Compute/virtualMachines/restart/action | Riavvia la macchina virtuale |
> | Microsoft.Compute/virtualMachines/start/action | Avvia la macchina virtuale |
> | Microsoft.DevTestLab/*/read | Leggere le proprietà di un lab |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Attesta una macchina virtuale attestabile casualmente nel lab. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Crea macchine virtuali in un lab. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Verificare che l'utente corrente dispone di un profilo valido nel lab. |
> | Microsoft.DevTestLab/labs/formulas/delete | Elimina le formule. |
> | Microsoft.DevTestLab/labs/formulas/read | Esegue la lettura di formule. |
> | Microsoft.DevTestLab/labs/formulas/write | Aggiunge o modifica le formule. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Valuta i criteri del lab. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Consente di assumere la proprietà di una macchina virtuale esistente |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Elenca le pianificazioni di avvio/arresto applicabili, se presenti. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Ottiene una stringa che rappresenta il contenuto del file RDP per la macchina virtuale |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Viene aggiunto un pool di indirizzi back-end di bilanciamento del carico. Non Alertable. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Aggiunge una regola nat in ingresso di bilanciamento del carico. Non Alertable. |
> | Microsoft.Network/networkInterfaces/*/read | Legge le proprietà di un'interfaccia di rete, ad esempio tutti i servizi di bilanciamento del carico di cui fa parte l'interfaccia di rete |
> | Microsoft.Network/networkInterfaces/join/action | Aggiunge una macchina virtuale a un'interfaccia di rete. Non Alertable. |
> | Microsoft.Network/networkInterfaces/read | Ottiene una definizione dell’interfaccia di rete.  |
> | Microsoft.Network/networkInterfaces/write | Crea un'interfaccia di rete o ne aggiorna una esistente.  |
> | Microsoft.Network/publicIPAddresses/*/read | Leggere le proprietà di un indirizzo IP pubblico |
> | Microsoft.Network/publicIPAddresses/join/action | Aggiunge un indirizzo ip pubblico. Non Alertable. |
> | Microsoft.Network/publicIPAddresses/read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Aggiunge una rete virtuale. Non Alertable. |
> | Microsoft.Resources/deployments/operations/read | Ottiene o elenca le operazioni di distribuzione. |
> | Microsoft.Resources/deployments/read | Ottiene o elenca le distribuzioni. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Elenca le dimensioni disponibili a cui è possibile aggiornare la macchina virtuale |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="dns-zone-contributor"></a>Collaboratore zona DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire le zone DNS e i set di record in DNS di Azure, ma non di controllare chi è autorizzato ad accedervi. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.Network/dnsZones/* | Creazione e gestione di zone e record DNS |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="documentdb-account-contributor"></a>Collaboratore account DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | È in grado di gestire account Azure Cosmos DB. Azure Cosmos DB era precedentemente noto come DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.DocumentDb/databaseAccounts/* | Creare e gestire account Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="event-hubs-data-owner"></a>Proprietario dei dati di hub eventi

> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente l'accesso completo alle risorse di hub eventi di Azure. |
> | **Id** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Actions** |  |
> | Microsoft.EventHubs/* | Consente l'accesso di gestione completa per lo spazio dei nomi di hub eventi |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.EventHubs/* | Consente l'accesso completo a nomi di hub eventi |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>Collaboratore per sottoscrizioni di eventi di Griglia di eventi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire le operazioni di sottoscrizione di eventi EventGrid. |
> | **Id** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Elencare le sottoscrizioni di eventi globali per tipo di argomento |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Elencare sottoscrizioni di eventi per area |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Elenca sottoscrizioni di eventi per area per tipo di argomento |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="eventgrid-eventsubscription-reader"></a>Ruolo con autorizzazioni di lettura per sottoscrizioni di eventi di Griglia di eventi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di leggere le sottoscrizioni di eventi EventGrid. |
> | **Id** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.EventGrid/eventSubscriptions/read | Leggere una sottoscrizione evento |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Elencare le sottoscrizioni di eventi globali per tipo di argomento |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Elencare sottoscrizioni di eventi per area |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Elenca sottoscrizioni di eventi per area per tipo di argomento |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="hdinsight-cluster-operator"></a>Operatore del Cluster HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di leggere e modificare le configurazioni dei cluster HDInsight. |
> | **Id** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **Actions** |  |
> | Microsoft.HDInsight/*/read |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | Ottenere le impostazioni di gateway per il HDInsight Cluster |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Aggiornare le impostazioni di gateway per il HDInsight Cluster |
> | Microsoft.HDInsight/clusters/configurations/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Resources/deployments/operations/read | Ottiene o elenca le operazioni di distribuzione. |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="hdinsight-domain-services-contributor"></a>Collaboratore Servizi di dominio HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può leggere, creare, modificare ed eliminare operazioni correlate ai Servizi di dominio necessarie per HDInsight Enterprise Security Package |
> | **Id** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Actions** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="intelligent-systems-account-contributor"></a>Collaboratore account Intelligent Systems
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire gli account Sistemi intelligenti, ma non di accedervi. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.IntelligentSystems/accounts/* | Creare e gestire account di Intelligent Systems |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="key-vault-contributor"></a>Collaboratore di Key Vault
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire gli insiemi di credenziali delle chiavi, ma non di accedervi. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Ripulisce un Key Vault eliminato temporaneamente |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="lab-creator"></a>Lab Creator (Creatore di lab)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di creare, gestire ed eliminare i lab gestiti con gli account di Azure Lab. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Crea un lab in un account del lab. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Ottiene informazioni sulla disponibilità a livello di area per ogni categoria di dimensioni configurata in un account del lab |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="log-analytics-contributor"></a>Collaboratore di Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Il ruolo Collaboratore di Log Analytics può leggere tutti i dati di monitoraggio e modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log dall'Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di soluzioni e la configurazione di Diagnostica di Azure in tutte le risorse di Azure. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Actions** |  |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Insights/diagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="log-analytics-reader"></a>Lettore di Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Il ruolo Lettore di Log Analytics può visualizzare ed eseguire ricerche in tutti i dati di monitoraggio e può visualizzare le impostazioni di monitoraggio, inclusa la visualizzazione della configurazione di Diagnostica di Azure in tutte le risorse di Azure. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Actions** |  |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Esegue la ricerca usando il nuovo motore. |
> | Microsoft.OperationalInsights/workspaces/search/action | Esegue una query di ricerca |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera le chiavi condivise per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro. |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="logic-app-contributor"></a>Collaboratore alle app per la logica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire le app per la logica, ma non di accedervi. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | Microsoft.ClassicStorage/storageAccounts/read | Restituisce l'account di archiviazione con l'account specificato. |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Insights/diagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | Microsoft.Insights/logdefinitions/* | Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai log attività tramite il portale. Elencare categorie di log nel log attività. |
> | Microsoft.Insights/metricDefinitions/* | Definizioni delle metriche (elenco dei tipi di metriche disponibili per una risorsa). |
> | Microsoft.Logic/* | Gestisce le risorse di App per la logica. |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | Microsoft.Storage/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Web/connectionGateways/* | Crea e gestisce un gateway di connessione. |
> | Microsoft.Web/connections/* | Crea e gestisce una connessione. |
> | Microsoft.Web/customApis/* | Crea e gestisce un'API personalizzata. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Ottiene le proprietà per un piano di servizio app |
> | Microsoft.Web/sites/functions/listSecrets/action | Elenca i segreti delle funzioni delle app Web. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="logic-app-operator"></a>Operatore delle app per la logica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di leggere, abilitare e disabilitare l'app per la logica. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/*/read | Legge le regole di avviso di Insights |
> | Microsoft.Insights/diagnosticSettings/*/read | Ottiene le impostazioni di diagnostica di App per la logica |
> | Microsoft.Insights/metricDefinitions/*/read | Ottiene le metriche disponibili per App per la logica. |
> | Microsoft.Logic/*/read | Legge le risorse di App per la logica. |
> | Microsoft.Logic/workflows/disable/action | Disabilita il flusso di lavoro. |
> | Microsoft.Logic/workflows/enable/action | Abilita il flusso di lavoro. |
> | Microsoft.Logic/workflows/validate/action | Convalida il flusso di lavoro. |
> | Microsoft.Resources/deployments/operations/read | Ottiene o elenca le operazioni di distribuzione. |
> | Microsoft.Resources/subscriptions/operationresults/read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Web/connectionGateways/*/read | Legge i gateway di connessione. |
> | Microsoft.Web/connections/*/read | Legge le connessioni. |
> | Microsoft.Web/customApis/*/read | Legge l'API personalizzata. |
> | Microsoft.Web/serverFarms/read | Ottiene le proprietà per un piano di servizio app |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="managed-application-operator-role"></a>Ruolo di Operatore applicazione gestita
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di leggere ed eseguire azioni sulle risorse dell'applicazione gestita |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Actions** |  |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | Microsoft.Solutions/applications/read | Recupera un elenco di applicazioni. |
> | Microsoft.Solutions/*/action |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="managed-applications-reader"></a>Lettore applicazioni gestite
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di leggere le risorse in un accesso di app gestita e JIT richiesta. |
> | **Id** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Actions** |  |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="managed-identity-contributor"></a>Managed Identity Contributor (Collaboratore per identità gestita)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Crea, legge, aggiorna ed elimina l'identità assegnata all'utente |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Actions** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="managed-identity-operator"></a>Managed Identity Operator (Operatore per identità gestita)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Legge e assegna l'identità assegnata all'utente |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Actions** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="management-group-contributor"></a>Collaboratore gruppo di gestione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Ruolo Collaboratore gruppo di gestione |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Actions** |  |
> | Microsoft.Management/managementGroups/delete | Elimina un gruppo di gestione. |
> | Microsoft.Management/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Annulla l'associazione della sottoscrizione dal gruppo di gestione. |
> | Microsoft.Management/managementGroups/subscriptions/write | Associa una sottoscrizione esistente al gruppo di gestione. |
> | Microsoft.Management/managementGroups/write | Crea o aggiorna un gruppo di gestione. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="management-group-reader"></a>Lettore gruppo di gestione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Ruolo Lettore gruppo di gestione |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Actions** |  |
> | Microsoft.Management/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="monitoring-contributor"></a>Collaboratore al monitoraggio
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può leggere tutti i dati del monitoraggio e modificare le impostazioni di monitoraggio. Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Actions** |  |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Regole di avviso di lettura, scrittura ed eliminazione. |
> | Microsoft.Insights/components/* | Leggere, scrivere ed eliminare componenti di Application Insights. |
> | Microsoft.Insights/DiagnosticSettings/* | Impostazioni di diagnostica di lettura, scrittura ed eliminazione. |
> | Microsoft.Insights/eventtypes/* | Elenco degli eventi dei registri attività (eventi di gestione) in una sottoscrizione. Questa autorizzazione è applicabile sia all'accesso programmatico che all'accesso al portale per il registro attività. |
> | Microsoft.Insights/LogDefinitions/* | Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai log attività tramite il portale. Elencare categorie di log nel log attività. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Definizioni delle metriche (elenco dei tipi di metriche disponibili per una risorsa). |
> | Microsoft.Insights/Metrics/* | Metriche per una risorsa. |
> | Microsoft.Insights/Register/Action | Registra il provider Microsoft Insights |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Leggere, scrivere ed eliminare test Web di Application Insights. |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Pacchetti della soluzione analitica log di lettura, scrittura ed eliminazione. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Le ricerche salvate analitica di log di lettura, scrittura ed eliminazione. |
> | Microsoft.OperationalInsights/workspaces/search/action | Esegue una query di ricerca |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Recupera le chiavi condivise per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Configurazione di dati di log di lettura, scrittura ed eliminazione analitica archiviazione. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="monitoring-metrics-publisher"></a>Autore delle metriche di monitoraggio
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Abilitare la pubblicazione di metriche nelle risorse di Azure |
> | **Id** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Actions** |  |
> | Microsoft.Insights/Register/Action | Registra il provider Microsoft Insights |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Scrive metrica |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="monitoring-reader"></a>Lettore di monitoraggio
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Può leggere tutti i dati del monitoraggio (metriche, log e così via). Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Actions** |  |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | Microsoft.OperationalInsights/workspaces/search/action | Esegue una query di ricerca |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="network-contributor"></a>Collaboratore di rete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire le reti, ma non di accedervi. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.Network/* | Creare e gestire reti |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="new-relic-apm-account-contributor"></a>Collaboratore account New Relic APM
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire gli account e le applicazioni di APR New Relic, ma non di accedervi. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="reader-and-data-access"></a>Lettore e accesso ai dati
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di visualizzare tutti gli elementi ma non consente di eliminare o creare un account di archiviazione o una risorsa contenuta. Consente anche l'accesso in lettura/scrittura a tutti i dati contenuti in un account di archiviazione tramite l'accesso alle chiavi dell'account di archiviazione. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | Restituisce il token SAS dell’account per l’account di archiviazione specificato. |
> | Microsoft.Storage/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="redis-cache-contributor"></a>Collaboratore cache Redis
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire le cache Redis, ma non di accedervi. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Cache/redis/* | Creare e gestire cache Redis |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="resource-policy-contributor-preview"></a>Collaboratore ai criteri delle risorse (anteprima)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | (Anteprima) Utenti di cui sono state recuperate informazioni da EA, con diritti per la creazione/modifica di criteri delle risorse, la creazione di ticket di supporto e la lettura di risorse/gerarchia. |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Actions** |  |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | Microsoft.Authorization/policyassignments/* | Creare e gestire assegnazioni di criteri |
> | Microsoft.Authorization/policydefinitions/* | Creare e gestire definizioni di criteri |
> | Microsoft.Authorization/policysetdefinitions/* | Creare e gestire set di criteri |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="scheduler-job-collections-contributor"></a>Collaboratore raccolte di processi dell'unità di pianificazione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire le raccolte di processi dell'utilità di pianificazione, ma non di accedervi. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Scheduler/jobcollections/* | Creare e gestire raccolte di processi |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="search-service-contributor"></a>Collaboratore servizi di ricerca
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire i servizi di Ricerca, ma non di accedervi. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Search/searchServices/* | È in grado di creare e gestire servizi di ricerca |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="security-admin"></a>Amministrazione della protezione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Solo in Centro sicurezza: è possibile visualizzare i criteri di sicurezza e gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare gli avvisi e le raccomandazioni, ignorare gli avvisi e le raccomandazioni |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Authorization/policyAssignments/* | Creare e gestire assegnazioni di criteri |
> | Microsoft.Authorization/policyDefinitions/* | Creare e gestire definizioni di criteri |
> | Microsoft.Authorization/policySetDefinitions/* | Creare e gestire set di criteri |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.Management/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | Microsoft.operationalInsights/workspaces/*/read | Visualizzare i dati di analitica di log |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="security-manager-legacy"></a>Gestore sicurezza (legacy)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Questo è un ruolo legacy. Usare invece Amministratore della protezione |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.ClassicCompute/*/read | Leggere le informazioni di configurazione delle macchine virtuali classiche |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Scrivere la configurazione delle macchine virtuali classiche |
> | Microsoft.ClassicNetwork/*/read | Leggere le informazioni della configurazione sulla rete classica |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Security/* | Creare e gestire criteri e componenti di protezione |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="security-reader"></a>Ruolo con autorizzazioni di lettura per la sicurezza
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Solo in Centro sicurezza: è possibile visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non è possibile apportare modifiche |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.operationalInsights/workspaces/*/read | Visualizzare i dati di analitica di log |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Security/*/read | Leggere criteri e componenti di sicurezza |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Management/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="service-bus-data-owner"></a>Proprietario dei dati del Bus di servizio

> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente l'accesso completo alle risorse del Bus di servizio di Azure. |
> | **Id** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Actions** |  |
> | Microsoft.ServiceBus/* | Consente l'accesso di gestione completa per lo spazio dei nomi del Bus di servizio |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* | Consente l'accesso completo ai dati allo spazio dei nomi del Bus di servizio |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="site-recovery-contributor"></a>Collaboratore al ripristino sito
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire il servizio Site Recovery ad eccezione della creazione dell'insieme di credenziali e dell'assegnazione di ruolo. |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.Network/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp è un'operazione interna usata dal servizio |
> | Microsoft.RecoveryServices/Vaults/certificates/write | L'operazione Aggiorna certificato risorsa aggiorna il certificato delle credenziali della risorsa o dell'insieme di credenziali. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Consente di creare e gestire informazioni estese relative all'insieme di credenziali |
> | Microsoft.RecoveryServices/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Consente di creare e gestire le identità registrate |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Consente di creare o aggiornare le impostazioni degli avvisi di replica |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Legge gli eventi |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Consente di creare e gestire le infrastrutture di replica |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Consente di creare e gestire i processi di replica |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Consente di creare e gestire i criteri di replica |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Consente di creare e gestire i piani di ripristino |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Consente di creare e gestire la configurazione di archiviazione dell'insieme di credenziali di Servizi di ripristino |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Consente di leggere gli avvisi per l'insieme di credenziali dei servizi di ripristino |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Storage/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="site-recovery-operator"></a>Operatore del ripristino sito
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di eseguire il failover e il failback ma non di eseguire altre operazioni di gestione di Site Recovery. |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.Network/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp è un'operazione interna usata dal servizio |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Legge le impostazioni degli avvisi |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Legge gli eventi |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verifica la coerenza dell'infrastruttura |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Legge le infrastrutture |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Riassocia gateway |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Rinnova il certificato per l'infrastruttura |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Legge le reti |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Legge i mapping di rete |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Legge i contenitori di protezione |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Legge gli elementi da proteggere |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Applica punto di ripristino |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Commit del failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Failover pianificato |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Legge gli elementi protetti |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Legge i punti di ripristino di replica |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Ripristina replica |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Riprotegge l'elemento protetto |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Failover di test |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Pulizia del failover di test |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aggiorna servizio Mobility |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Legge i mapping dei contenitori di protezione |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Legge i provider dei servizi di ripristino |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Aggiorna i provider |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Legge le classificazioni di archiviazione |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Legge i mapping delle classificazioni di archiviazione |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Legge i vCenter |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Consente di creare e gestire i processi di replica |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Legge i criteri |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Piano di ripristino del commit del failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Piano di ripristino del failover pianificato |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Legge i piani di ripristino |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Piano di ripristino di riprotezione |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Piano di ripristino del failover di test |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Piano di ripristino della pulizia del failover di test |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Piano di ripristino del failover |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Consente di leggere gli avvisi per l'insieme di credenziali dei servizi di ripristino |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Storage/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="site-recovery-reader"></a>Reader di ripristino sito
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di visualizzare lo stato di Site Recovery ma non di eseguire altre operazioni di gestione. |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp è un'operazione interna usata dal servizio |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Legge le impostazioni degli avvisi |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Legge gli eventi |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Legge le infrastrutture |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Legge le reti |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Legge i mapping di rete |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Legge i contenitori di protezione |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Legge gli elementi da proteggere |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Legge gli elementi protetti |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Legge i punti di ripristino di replica |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Legge i mapping dei contenitori di protezione |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Legge i provider dei servizi di ripristino |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Legge le classificazioni di archiviazione |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Legge i mapping delle classificazioni di archiviazione |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Legge i vCenter |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Legge i processi |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Legge i criteri |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Legge i piani di ripristino |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="spatial-anchors-account-contributor"></a>Collaboratore per l'account per gli ancoraggi spaziali
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire gli ancoraggi nello spazio nell'account, ma non di eliminarli |
> | **Id** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Creare ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Individuare nelle vicinanze Anchor spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Ottiene le proprietà di ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Individuare gli ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Inviare i dati di diagnostica per contribuire a migliorare la qualità del servizio Azure spaziali ancoraggi |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Aggiornare le proprietà spaziali ancoraggi |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="spatial-anchors-account-owner"></a>Proprietario dell'account per gli ancoraggi spaziali
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire gli ancoraggi nello spazio nell'account, incluse operazioni di eliminazione |
> | **Id** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Creare ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Eliminare gli ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Individuare nelle vicinanze Anchor spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Ottiene le proprietà di ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Individuare gli ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Inviare i dati di diagnostica per contribuire a migliorare la qualità del servizio Azure spaziali ancoraggi |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Aggiornare le proprietà spaziali ancoraggi |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="spatial-anchors-account-reader"></a>Ruolo Lettore dell'account per gli ancoraggi spaziali
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di individuare e leggere le proprietà degli ancoraggi nello spazio nell'account |
> | **Id** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Individuare nelle vicinanze Anchor spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Ottiene le proprietà di ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Individuare gli ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Inviare i dati di diagnostica per contribuire a migliorare la qualità del servizio Azure spaziali ancoraggi |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="sql-db-contributor"></a>Collaboratore database SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza o i rispettivi server SQL padre. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire regole di avviso |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Creare e gestire database SQL |
> | Microsoft.Sql/servers/read | Restituisce l'elenco di server o ottiene le proprietà per il server specificato |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Insights/metrics/read | Esegue la lettura delle metriche |
> | Microsoft.Insights/metricDefinitions/read | Consente di leggere le definizioni della metrica |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Modificare i criteri di controllo |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Modificare le impostazioni di controllo |
> | Microsoft.Sql/servers/databases/auditRecords/read | Recupera i record di controllo BLOB del database |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Modificare i criteri di connessione |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Modificare i criteri di mascheratura dei dati |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Modificare i criteri di avviso di sicurezza |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Modificare i criteri di protezione |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="sql-managed-instance-contributor"></a>Collaboratore di istanza gestita di SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire le istanze gestite di SQL e necessaria la configurazione di rete, ma non è possibile concedere l'accesso ad altri utenti. |
> | **Id** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **Actions** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Network/virtualNetworks/subnets/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Insights/metrics/read | Esegue la lettura delle metriche |
> | Microsoft.Insights/metricDefinitions/read | Consente di leggere le definizioni della metrica |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="sql-security-manager"></a>Gestione della sicurezza SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire i criteri relativi alla sicurezza di server e database SQL, ma non di accedervi. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere l'autorizzazione Microsoft |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Non alertable. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Creare e gestire criteri di controllo di server SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Creare e gestire le impostazioni di controllo di SQL Server |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Recupera i dettagli dei criteri di controllo BLOB del server esteso configurati in uno specifico server |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Creare e gestire i criteri di controllo dei database SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Creare e gestire le impostazioni di controllo dei database di SQL Server |
> | Microsoft.Sql/servers/databases/auditRecords/read | Legge i record di controllo |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Creare e gestire i criteri di connessione dei database dei server SQL |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Creare e gestire i criteri della maschera dei dati dei database dei server SQL |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Recupera i dettagli dei criteri di controllo BLOB esteso configurati in uno specifico database |
> | Microsoft.Sql/servers/databases/read | Restituisce l'elenco dei database o ottiene le proprietà per il database specificato |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Ottenere uno schema di database. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Ottenere una colonna del database. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Ottenere una tabella di database. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Creare e gestire i criteri degli avvisi di sicurezza dei database di SQL Server |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Creare e gestire le metriche di sicurezza dei database di server SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Restituisce l'elenco di server o ottiene le proprietà per il server specificato |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Creare e gestire i criteri degli avvisi di sicurezza di SQL Server |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="sql-server-contributor"></a>Collaboratore SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire i server e i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Creare e gestire server SQL |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Insights/metrics/read | Esegue la lettura delle metriche |
> | Microsoft.Insights/metricDefinitions/read | Consente di leggere le definizioni della metrica |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Modificare i criteri di controllo di SQL Server |
> | Microsoft.Sql/servers/auditingSettings/* | Modificare le impostazioni di controllo di SQL Server |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Modificare i criteri di controllo dei database di SQL Server |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Modificare le impostazioni di controllo dei database di SQL Server |
> | Microsoft.Sql/servers/databases/auditRecords/read | Legge i record di controllo |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Modificare i criteri di connessione dei database di SQL Server |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Modificare i criteri di mascheratura dei dati dei database di SQL server |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Modificare i criteri degli avvisi di sicurezza dei database di SQL Server |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Modificare le metriche di protezione dei database di SQL Server |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Modificare i criteri degli avvisi di sicurezza di SQL Server |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="storage-account-contributor"></a>Collaboratore account di archiviazione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | La gestione degli account di archiviazione. Non fornisce l'accesso ai dati nell'account di archiviazione. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere tutte le autorizzazioni |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Insights/diagnosticSettings/* | Gestire le impostazioni di diagnostica |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Non alertable. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Storage/storageAccounts/* | Creare e gestire account di archiviazione |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="storage-account-key-operator-service-role"></a>Ruolo del servizio dell'operatore della chiave dell'account di archiviazione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di elencare e rigenerare chiavi di accesso di account di archiviazione. |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Rigenerare le chiavi di accesso per l'account di archiviazione specificato. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="storage-blob-data-contributor"></a>Collaboratore ai dati dei BLOB di archiviazione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Leggere, scrivere ed eliminare i BLOB e contenitori di archiviazione di Azure. Per informazioni su quali azioni sono necessari per una determinata operazione sui dati, vedere [le autorizzazioni per la chiamata di operazioni sui dati blob e coda](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Eliminare un contenitore. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Restituisce un contenitore o un elenco di contenitori. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Modificare i metadati o proprietà di un contenitore. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Eliminare un BLOB. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Restituisce un elenco di BLOB o un blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Scrivere un blob. |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="storage-blob-data-owner"></a>Proprietario dei dati dei BLOB di archiviazione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Fornisce accesso completo ai contenitori blob di archiviazione di Azure e i dati, inclusa l'assegnazione di controllo di accesso POSIX. Per informazioni su quali azioni sono necessari per una determinata operazione sui dati, vedere [le autorizzazioni per la chiamata di operazioni sui dati blob e coda](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **Id** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Autorizzazioni complete su contenitori. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Autorizzazioni complete su BLOB. |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="storage-blob-data-reader"></a>Ruolo con autorizzazioni di lettura per i dati dei BLOB di archiviazione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Leggere ed elencare i BLOB e contenitori di archiviazione di Azure. Per informazioni su quali azioni sono necessari per una determinata operazione sui dati, vedere [le autorizzazioni per la chiamata di operazioni sui dati blob e coda](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Restituisce un contenitore o un elenco di contenitori. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Restituisce un elenco di BLOB o un blob. |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="storage-queue-data-contributor"></a>Collaboratore ai dati della coda di archiviazione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Leggere, scrivere ed eliminare le code di archiviazione di Azure e i messaggi in coda. Per informazioni su quali azioni sono necessari per una determinata operazione sui dati, vedere [le autorizzazioni per la chiamata di operazioni sui dati blob e coda](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Eliminare una coda. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Restituisce una coda o un elenco di code. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Modificare i metadati della coda o le proprietà. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Eliminare uno o più messaggi da una coda. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Visualizza o recuperare uno o più messaggi da una coda. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Aggiungere un messaggio a una coda. |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="storage-queue-data-message-processor"></a>Ruolo con autorizzazioni di elaborazione per i messaggi sui dati della coda di archiviazione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Visualizza, recuperare ed eliminare un messaggio da una coda di archiviazione di Azure. Per informazioni su quali azioni sono necessari per una determinata operazione sui dati, vedere [le autorizzazioni per la chiamata di operazioni sui dati blob e coda](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **Id** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Visualizza un messaggio. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Recuperare ed eliminare un messaggio. |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="storage-queue-data-message-sender"></a>Mittente dei messaggi sui dati della coda di archiviazione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Aggiungere messaggi a una coda di archiviazione di Azure. Per informazioni su quali azioni sono necessari per una determinata operazione sui dati, vedere [le autorizzazioni per la chiamata di operazioni sui dati blob e coda](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **Id** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Aggiungere un messaggio a una coda. |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="storage-queue-data-reader"></a>Ruolo con autorizzazioni di lettura per i dati della coda di archiviazione
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Leggere ed elencare le code di archiviazione di Azure e i messaggi in coda. Per informazioni su quali azioni sono necessari per una determinata operazione sui dati, vedere [le autorizzazioni per la chiamata di operazioni sui dati blob e coda](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Restituisce una coda o un elenco di code. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Visualizza o recuperare uno o più messaggi da una coda. |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="support-request-contributor"></a>Collaboratore alla richiesta di supporto
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di creare e gestire le richieste di supporto. |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorizzazione Lettura |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="traffic-manager-contributor"></a>Collaboratore Gestione traffico
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire i profili di Gestione traffico, ma non di controllare chi è autorizzato ad accedervi. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="user-access-administrator"></a>Amministratore accessi utente
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire gli accessi utente alle risorse di Azure. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Actions** |  |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | Microsoft.Authorization/* | Gestire l'autorizzazione |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="virtual-machine-administrator-login"></a>Virtual Machine Administrator Login (Accesso amministratore macchina virtuale)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di visualizzare le macchine virtuali nel portale e di accedere come amministratore |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Actions** |  |
> | Microsoft.Network/publicIPAddresses/read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | Microsoft.Network/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | Microsoft.Network/loadBalancers/read | Ottiene una definizione del servizio di bilanciamento del carico |
> | Microsoft.Network/networkInterfaces/read | Ottiene una definizione dell’interfaccia di rete.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Consente di accedere a una macchina virtuale come utente normale |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Consente di accedere a una macchina virtuale con privilegi di amministratore di Windows o di utente root di Linux |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="virtual-machine-contributor"></a>Collaboratore macchine virtuali
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire le macchine virtuali, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorizzazione Lettura |
> | Microsoft.Compute/availabilitySets/* | Creare e gestire set di disponibilità di calcolo |
> | Microsoft.Compute/locations/* | Creare e gestire percorsi di calcolo |
> | Microsoft.Compute/virtualMachines/* | Creare e gestire macchine virtuali |
> | Microsoft.Compute/virtualMachineScaleSets/* | Creare e gestire i set di scalabilità delle macchine virtuali |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Viene aggiunto un pool di indirizzi back-end di gateway applicazione. Non Alertable. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Viene aggiunto un pool di indirizzi back-end di bilanciamento del carico. Non Alertable. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Viene aggiunto un servizio di bilanciamento del carico pool NAT in ingresso. Non alertable. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Aggiunge una regola nat in ingresso di bilanciamento del carico. Non Alertable. |
> | Microsoft.Network/loadBalancers/probes/join/action | Consente l'uso di probe di un servizio di bilanciamento del carico. Con questa autorizzazione, ad esempio, la proprietà healthProbe di un set di scalabilità di macchine virtuali può fare riferimento al probe. Non alertable. |
> | Microsoft.Network/loadBalancers/read | Ottiene una definizione del servizio di bilanciamento del carico |
> | Microsoft.Network/locations/* | Creare e gestire percorsi di rete |
> | Microsoft.Network/networkInterfaces/* | Creare e gestire interfacce di rete |
> | Microsoft.Network/networkSecurityGroups/join/action | Aggiunge un gruppo di sicurezza di rete. Non Alertable. |
> | Microsoft.Network/networkSecurityGroups/read | Ottiene una definizione del gruppo di sicurezza di rete |
> | Microsoft.Network/publicIPAddresses/join/action | Aggiunge un indirizzo ip pubblico. Non Alertable. |
> | Microsoft.Network/publicIPAddresses/read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | Microsoft.Network/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Aggiunge una rete virtuale. Non Alertable. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Crea un programma di protezione del backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Restituisce i dettagli dell'oggetto dell'elemento protetto |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Crea un elemento protetto di backup |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Restituisce tutti i criteri di protezione |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Crea i criteri di protezione |
> | Microsoft.RecoveryServices/Vaults/read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | Microsoft.RecoveryServices/Vaults/usages/read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | Microsoft.RecoveryServices/Vaults/write | L'operazione Crea insieme di credenziali crea una risorsa di Azure di tipo 'vault' |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | Microsoft.Storage/storageAccounts/read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="virtual-machine-user-login"></a>Virtual Machine User Login (Accesso utente macchina virtuale)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di visualizzare le macchine virtuali nel portale e di accedere come utente normale. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Actions** |  |
> | Microsoft.Network/publicIPAddresses/read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | Microsoft.Network/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | Microsoft.Network/loadBalancers/read | Ottiene una definizione del servizio di bilanciamento del carico |
> | Microsoft.Network/networkInterfaces/read | Ottiene una definizione dell’interfaccia di rete.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Consente di accedere a una macchina virtuale come utente normale |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="web-plan-contributor"></a>Collaboratore piani Web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire i piani Web per i siti Web, ma non di accedervi. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorizzazione Lettura |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Web/serverFarms/* | Creare e gestire server farm |
> | Microsoft.Web/hostingEnvironments/Join/Action | Unisce un ambiente del servizio App |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="website-contributor"></a>Collaboratore siti Web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Descrizione** | Consente di gestire i siti Web (non i piani Web), ma non di accedervi. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorizzazione Lettura |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Insights/components/* | È in grado di creare e gestire i componenti di Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Web/certificates/* | Creare e gestire certificati dei siti Web |
> | Microsoft.Web/listSitesAssignedToHostName/read | Ottiene i nomi dei siti assegnati al nome host. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Ottiene le proprietà per un piano di servizio app |
> | Microsoft.Web/sites/* | Creare e gestire siti Web. Per creare un sito sono anche necessarie le autorizzazione di scrittura associate al piano di servizio app |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

## <a name="next-steps"></a>Passaggi successivi

- [Ruoli personalizzati per le risorse di Azure](custom-roles.md)
- [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e il portale di Azure](role-assignments-portal.md)
- [Autorizzazioni nel Centro sicurezza di Azure](../security-center/security-center-permissions.md)
