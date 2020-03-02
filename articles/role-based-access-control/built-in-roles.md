---
title: Ruoli predefiniti di Azure per RBAC di Azure
description: Descrive i ruoli predefiniti di Azure per il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Elenca Actions, NotActions, DataActions e NotDataActions.
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
ms.date: 02/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: b7b944880074b64f6a9e66e177082e52632e9c9d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205892"
---
# <a name="azure-built-in-roles"></a>Ruoli predefiniti di Azure

Il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](overview.md) include diversi ruoli predefiniti di Azure che è possibile assegnare a utenti, gruppi, entità servizio e identità gestite. Le assegnazioni di ruolo sono il modo in cui si controlla l'accesso alle risorse di Azure. Se i ruoli predefiniti non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare [ruoli personalizzati di Azure](custom-roles.md).

Questo articolo illustra i ruoli predefiniti per le risorse di Azure, che sono in continua evoluzione. Per ottenere i ruoli più recenti, usare [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) o [az role definition list](/cli/azure/role/definition#az-role-definition-list). Se si cercano ruoli di amministratore per Azure Active Directory, vedere [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="descriptions-and-ids"></a>Descrizioni e ID

La tabella seguente fornisce una breve descrizione e l'ID univoco di ogni ruolo predefinito. Fare clic sul nome del ruolo per vedere l'elenco di `Actions`, `NotActions`, `DataActions` e `NotDataActions` per ogni ruolo. Per informazioni sul significato di queste azioni e su come si applicano ai piani di gestione e ai piani dati, vedere [Informazioni sulle definizioni del ruolo per le risorse di Azure](role-definitions.md).


> [!div class="mx-tableFixed"]
> | Ruolo predefinito | Descrizione | ID |
> | --- | --- | --- |
> | [AcrDelete](#acrdelete) | eliminazione di ACR | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | firmatario immagine acr | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr pull | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | lettore di dati di quarantena acr | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | writer di dati di quarantena acr | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Collaboratore servizio Gestione API](#api-management-service-contributor) | Può gestire il servizio e le API. | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Ruolo operatore del servizio Gestione API](#api-management-service-operator-role) | Può gestire il servizio ma non le API. | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Ruolo lettura del servizio Gestione API](#api-management-service-reader-role) | Consente l'accesso di sola lettura al servizio e alle API. | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Proprietario dati di configurazione dell'app](#app-configuration-data-owner) | Consente l'accesso completo ai dati di configurazione dell'app. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Lettore dati di configurazione dell'app](#app-configuration-data-reader) | Consente l'accesso in lettura ai dati di configurazione dell'app. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Collaboratore componente di Application Insights](#application-insights-component-contributor) | È in grado di gestire i componenti di Application Insights | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Debugger di snapshot di Application Insights](#application-insights-snapshot-debugger) | Concede l'autorizzazione utente per visualizzare e scaricare gli snapshot di debug raccolti con Application Insights Snapshot Debugger. Si noti che queste autorizzazioni non sono incluse nei ruoli [Proprietario](#owner) e [Collaboratore](#contributor). | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Operatore processo di automazione](#automation-job-operator) | Consente di creare e gestire i processi tramite i runbook di Automazione. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Operatore di automazione](#automation-operator) | Gli operatori di automazione possono avviare, arrestare, sospendere e riprendere processi. | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Operatore runbook di automazione](#automation-runbook-operator) | Consente di leggere le proprietà del runbook per permettere di creare processi del runbook. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Collaboratore](#avere-contributor) | Consente di creare e gestire un cluster vFXT. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Operatore di stato](#avere-operator) | Usato dal cluster vFXT per la gestione del cluster | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Onboarding del computer connesso di Azure](#azure-connected-machine-onboarding) | Consente di caricare i computer connessi ad Azure. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Amministratore delle risorse del computer connesso ad Azure](#azure-connected-machine-resource-administrator) | Può leggere, scrivere, eliminare e ricaricare i computer connessi di Azure. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Proprietario dati di hub eventi di Azure](#azure-event-hubs-data-owner) | Consente l'accesso completo alle risorse di hub eventi di Azure. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Ricevitore di dati di hub eventi di Azure](#azure-event-hubs-data-receiver) | Consente di ricevere l'accesso alle risorse di hub eventi di Azure. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Mittente dati di hub eventi di Azure](#azure-event-hubs-data-sender) | Consente di inviare l'accesso alle risorse di hub eventi di Azure. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Ruolo di amministratore del cluster del servizio Azure Kubernetes](#azure-kubernetes-service-cluster-admin-role) | Elencare l'azione delle credenziali di amministratore del cluster. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Ruolo di utente del cluster del servizio Azure Kubernetes](#azure-kubernetes-service-cluster-user-role) | Elencare l'azione delle credenziali di utente del cluster. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Lettore dati di Maps di Azure (anteprima)](#azure-maps-data-reader-preview) | Concede l'accesso per leggere i dati correlati alla mappa da un account Azure maps. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Collaboratore sentinella di Azure](#azure-sentinel-contributor) | Collaboratore sentinella di Azure | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Lettore sentinella di Azure](#azure-sentinel-reader) | Lettore sentinella di Azure | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Risponditore sentinella di Azure](#azure-sentinel-responder) | Risponditore sentinella di Azure | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Proprietario dati del bus di servizio di Azure](#azure-service-bus-data-owner) | Consente l'accesso completo alle risorse del bus di servizio di Azure. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Ricevitore dati del bus di servizio di Azure](#azure-service-bus-data-receiver) | Consente di ricevere l'accesso alle risorse del bus di servizio di Azure. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Mittente dati del bus di servizio di Azure](#azure-service-bus-data-sender) | Consente di inviare l'accesso alle risorse del bus di servizio di Azure. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Proprietario della registrazione di Azure Stack](#azure-stack-registration-owner) | Consente di gestire le registrazioni di Azure Stack. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Collaboratore di backup](#backup-contributor) | Consente di gestire il servizio di backup, ma non di creare insiemi di credenziali e concedere l'accesso ad altri utenti | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Operatore di backup](#backup-operator) | Consente di gestire i servizi di backup, ma non di rimuovere il backup, creare insiemi di credenziali e concedere l'accesso ad altri utenti. | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Lettore di backup](#backup-reader) | Può visualizzare i servizi di backup, ma non può apportare modifiche. | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Lettore per la fatturazione](#billing-reader) | Consente l'accesso in lettura ai dati di fatturazione. | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Collaboratore BizTalk](#biztalk-contributor) | Consente di gestire i servizi BizTalk, ma non di accedervi. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Accesso al nodo membro blockchain (anteprima)](#blockchain-member-node-access-preview) | Consente l'accesso ai nodi membro blockchain | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | [Collaboratore di progetto](#blueprint-contributor) | Può gestire le definizioni di progetto, ma non assegnarle. | 41077137-E803-4205-871C-5a86e6a753b4 |
> | [Operatore di progetto](#blueprint-operator) | Consente di assegnare progetti pubblicati esistenti, ma non di creare nuovi progetti. Nota: questa operazione funziona solo se l'assegnazione viene eseguita con un'identità gestita assegnata dall'utente. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Collaboratore endpoint rete CDN](#cdn-endpoint-contributor) | Può gestire gli endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Lettore endpoint rete CDN](#cdn-endpoint-reader) | Può visualizzare gli endpoint della rete CDN, ma non può apportare modifiche. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Collaboratore profilo rete CDN](#cdn-profile-contributor) | Può gestire i profili e i rispettivi endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [Lettore profilo rete CDN](#cdn-profile-reader) | Può visualizzare i profili e i rispettivi endpoint della rete CDN, ma non può apportare modifiche. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Collaboratore reti virtuali classiche](#classic-network-contributor) | Consente di gestire le reti classiche, ma non di accedervi. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Collaboratore account di archiviazione classico](#classic-storage-account-contributor) | Consente di gestire gli account di archiviazione classici, ma non di accedervi. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico](#classic-storage-account-key-operator-service-role) | Gli operatori della chiave dell'account di archiviazione classico sono autorizzati a elencare e rigenerare le chiavi negli account di archiviazione classici | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Collaboratore macchine virtuali classiche](#classic-virtual-machine-contributor) | Consente di gestire le macchine virtuali classiche, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Collaboratore Servizi cognitivi](#cognitive-services-contributor) | Consente di creare, leggere, aggiornare, eliminare e gestire le chiavi di Servizi cognitivi. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Lettore dati di servizi cognitivi (anteprima)](#cognitive-services-data-reader-preview) | Consente di leggere i dati di servizi cognitivi. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Utente Servizi cognitivi](#cognitive-services-user) | Consente di leggere ed elencare le chiavi di Servizi cognitivi. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | [Collaboratore](#contributor) | Consente di gestire tutto tranne che per concedere l'accesso alle risorse. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Ruolo Lettore dell'account Cosmos DB](#cosmos-db-account-reader-role) | Può leggere i dati degli account Azure Cosmos DB. Vedere [Collaboratore account DocumentDB](#documentdb-account-contributor) per la gestione degli account Azure Cosmos DB. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Operatore Cosmos DB](#cosmos-db-operator) | Consente di gestire gli account Azure Cosmos DB, ma non di accedere ai dati in essi contenuti. Impedisce l'accesso alle chiavi dell'account e alle stringhe di connessione. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Può inviare una richiesta di ripristino per un database di Cosmos DB o un contenitore per un account | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Collaboratore Gestione costi](#cost-management-contributor) | Può visualizzare i costi e gestire la configurazione dei costi, ad esempio budget ed esportazioni | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Lettore Gestione costi](#cost-management-reader) | Può visualizzare la configurazione e i dati dei costi, ad esempio budget ed esportazioni | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Collaboratore Data Box](#data-box-contributor) | Consente di gestire tutto il servizio Data Box, ad eccezione della concessione dell'accesso ad altri utenti. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Lettore Data Box](#data-box-reader) | Consente di gestire il servizio Data Box, ad eccezione della creazione di ordini, della modifica dei dettagli dell'ordine e della concessione dell'accesso ad altri utenti. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Collaboratore Data Factory](#data-factory-contributor) | Consente di creare e gestire data factory, oltre alle risorse figlio in esse contenute. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Sviluppatore di Data Lake Analytics](#data-lake-analytics-developer) | Consente di inviare, monitorare e gestire i propri processi, ma non di creare o eliminare account Data Lake Analytics. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Pulizia dati](#data-purger) | Può eliminare i dati di analisi | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Utente DevTest Labs](#devtest-labs-user) | Consente di connettere, avviare, riavviare e arrestare le macchine virtuali in Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Collaboratore zona DNS](#dns-zone-contributor) | Consente di gestire le zone DNS e i set di record in DNS di Azure, ma non di controllare chi è autorizzato ad accedervi. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Collaboratore account DocumentDB](#documentdb-account-contributor) | È in grado di gestire account Azure Cosmos DB. Azure Cosmos DB era precedentemente noto come DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Collaboratore per sottoscrizioni di eventi di Griglia di eventi](#eventgrid-eventsubscription-contributor) | Consente di gestire le operazioni di sottoscrizione di eventi EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [Ruolo con autorizzazioni di lettura per sottoscrizioni di eventi di Griglia di eventi](#eventgrid-eventsubscription-reader) | Consente di leggere le sottoscrizioni di eventi EventGrid. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Operatore cluster HDInsight](#hdinsight-cluster-operator) | Consente di leggere e modificare le configurazioni del cluster HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Collaboratore Servizi di dominio HDInsight](#hdinsight-domain-services-contributor) | Può leggere, creare, modificare ed eliminare operazioni correlate ai Servizi di dominio necessarie per HDInsight Enterprise Security Package | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Collaboratore account Intelligent Systems](#intelligent-systems-account-contributor) | Consente di gestire gli account Sistemi intelligenti, ma non di accedervi. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Collaboratore di Key Vault](#key-vault-contributor) | Consente di gestire gli insiemi di credenziali delle chiavi, ma non di accedervi. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Lab Creator](#lab-creator) (Creatore di lab) | Consente di creare, gestire ed eliminare i lab gestiti con gli account di Azure Lab. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | [Collaboratore di Log Analytics](#log-analytics-contributor) | Il ruolo Collaboratore di Log Analytics può leggere tutti i dati di monitoraggio e modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log dall'Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di soluzioni e la configurazione di Diagnostica di Azure in tutte le risorse di Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Lettore di Log Analytics](#log-analytics-reader) | Il ruolo Lettore di Log Analytics può visualizzare ed eseguire ricerche in tutti i dati di monitoraggio e può visualizzare le impostazioni di monitoraggio, inclusa la visualizzazione della configurazione di Diagnostica di Azure in tutte le risorse di Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Collaboratore per app per la logica](#logic-app-contributor) | Consente di gestire le app per la logica, ma non di modificarne l'accesso. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Operatore per app per la logica](#logic-app-operator) | Consente di leggere, abilitare e disabilitare le app per la logica, ma non di modificarle o aggiornarle. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | [Ruolo Collaboratore applicazione gestita](#managed-application-contributor-role) | Consente di creare risorse di applicazioni gestite. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Ruolo di Operatore applicazione gestita](#managed-application-operator-role) | Consente di leggere ed eseguire azioni sulle risorse dell'applicazione gestita | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Lettore applicazioni gestite](#managed-applications-reader) | Consente di leggere le risorse in un accesso di app gestita e JIT richiesta. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Managed Identity Contributor](#managed-identity-contributor) (Collaboratore per identità gestita) | Crea, legge, aggiorna ed elimina l'identità assegnata all'utente | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Managed Identity Operator](#managed-identity-operator) (Operatore per identità gestita) | Legge e assegna l'identità assegnata all'utente | f1a07417-d97a-45cb-824c-7a7467783830 |
> | [Ruolo eliminazione assegnazione registrazione servizi gestiti](#managed-services-registration-assignment-delete-role) | Il ruolo eliminazione di assegnazione di registrazione dei servizi gestiti consente agli utenti di gestire i tenant di eliminare l'assegnazione di registrazione assegnata al tenant. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Collaboratore gruppo di gestione](#management-group-contributor) | Ruolo Collaboratore gruppo di gestione | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Lettore gruppo di gestione](#management-group-reader) | Ruolo Lettore gruppo di gestione | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Collaboratore al monitoraggio](#monitoring-contributor) | Può leggere tutti i dati del monitoraggio e modificare le impostazioni di monitoraggio. Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Autore delle metriche di monitoraggio](#monitoring-metrics-publisher) | Abilitare la pubblicazione di metriche nelle risorse di Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Lettore di monitoraggio](#monitoring-reader) | Può leggere tutti i dati del monitoraggio (metriche, log e così via). Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Collaboratore di rete](#network-contributor) | Consente di gestire le reti, ma non di accedervi. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Collaboratore account New Relic APM](#new-relic-apm-account-contributor) | Consente di gestire gli account e le applicazioni di APR New Relic, ma non di accedervi. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Proprietario](#owner) | Consente di gestire tutto, incluso l'accesso alle risorse. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Writer di dati di policy Insights (anteprima)](#policy-insights-data-writer-preview) | Consente l'accesso in lettura ai criteri delle risorse e l'accesso in scrittura agli eventi dei criteri dei componenti delle risorse. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Lettore](#reader) | Consente di visualizzare tutti gli elementi, ma senza apportare alcuna modifica. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Lettore e accesso ai dati](#reader-and-data-access) | Consente di visualizzare tutti gli elementi ma non consente di eliminare o creare un account di archiviazione o una risorsa contenuta. Consente anche l'accesso in lettura/scrittura a tutti i dati contenuti in un account di archiviazione tramite l'accesso alle chiavi dell'account di archiviazione. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Collaboratore cache Redis](#redis-cache-contributor) | Consente di gestire le cache Redis, ma non di accedervi. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Collaboratore criteri risorse](#resource-policy-contributor) | Utenti con diritti per creare/modificare i criteri delle risorse, creare un ticket di supporto e leggere risorse/gerarchie. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Collaboratore raccolte di processi dell'unità di pianificazione](#scheduler-job-collections-contributor) | Consente di gestire le raccolte di processi dell'utilità di pianificazione, ma non di accedervi. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Collaboratore servizi di ricerca](#search-service-contributor) | Consente di gestire i servizi di Ricerca, ma non di accedervi. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Amministrazione della protezione](#security-admin) | è possibile visualizzare i criteri di sicurezza e gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare gli avvisi e le raccomandazioni, ignorare gli avvisi e le raccomandazioni. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Collaboratore valutazione della sicurezza](#security-assessment-contributor) | Consente di eseguire il push delle valutazioni nel centro sicurezza | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Gestore sicurezza (legacy)](#security-manager-legacy) | Questo è un ruolo legacy. Usare invece l'amministratore della sicurezza. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Ruolo con autorizzazioni di lettura per la sicurezza](#security-reader) | Consente di visualizzare le raccomandazioni e gli avvisi, visualizzare i criteri di sicurezza, visualizzare gli Stati di sicurezza, ma non apportare modifiche. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | [Collaboratore al ripristino sito](#site-recovery-contributor) | Consente di gestire il servizio Site Recovery ad eccezione della creazione dell'insieme di credenziali e dell'assegnazione di ruolo. | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Operatore del ripristino sito](#site-recovery-operator) | Consente di eseguire il failover e il failback ma non di eseguire altre operazioni di gestione di Site Recovery. | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Reader di ripristino sito](#site-recovery-reader) | Consente di visualizzare lo stato di Site Recovery ma non di eseguire altre operazioni di gestione. | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Collaboratore account ancoraggi spaziali](#spatial-anchors-account-contributor) | Consente di gestire gli ancoraggi spaziali nell'account, ma non di eliminarli | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Proprietario dell'account di ancoraggi spaziali](#spatial-anchors-account-owner) | Consente di gestire gli ancoraggi spaziali nell'account, inclusa l'eliminazione | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Lettore di account ancoraggi spaziali](#spatial-anchors-account-reader) | Consente di individuare e leggere le proprietà degli ancoraggi spaziali nell'account | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | [Collaboratore database SQL](#sql-db-contributor) | Consente di gestire i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza o i rispettivi server SQL padre. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Collaboratore Istanza gestita SQL](#sql-managed-instance-contributor) | Consente di gestire le istanze gestite di SQL e la configurazione di rete richiesta, ma non di concedere l'accesso ad altri utenti. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Gestione della sicurezza SQL](#sql-security-manager) | Consente di gestire i criteri relativi alla sicurezza di server e database SQL, ma non di accedervi. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Collaboratore SQL Server](#sql-server-contributor) | Consente di gestire i server e i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | [Collaboratore account di archiviazione](#storage-account-contributor) | Consente la gestione degli account di archiviazione. Consente di accedere alla chiave dell'account, che può essere usata per accedere ai dati tramite l'autorizzazione della chiave condivisa. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione](#storage-account-key-operator-service-role) | Consente di elencare e rigenerare le chiavi di accesso all'account di archiviazione. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Collaboratore ai dati del BLOB di archiviazione](#storage-blob-data-contributor) | Leggere, scrivere ed eliminare i contenitori e i BLOB di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere [autorizzazioni per la chiamata di operazioni di dati BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Proprietario dei dati del BLOB di archiviazione](#storage-blob-data-owner) | Fornisce accesso completo ai contenitori BLOB e ai dati di archiviazione di Azure, inclusa l'assegnazione del controllo di accesso POSIX. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere [autorizzazioni per la chiamata di operazioni di dati BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Lettore dei dati del BLOB di archiviazione](#storage-blob-data-reader) | Leggere ed elencare i contenitori e i BLOB di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere [autorizzazioni per la chiamata di operazioni di dati BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Delegatore BLOB di archiviazione](#storage-blob-delegator) | Ottenere una chiave di delega utente, che può quindi essere usata per creare una firma di accesso condiviso per un contenitore o un BLOB firmato con Azure AD credenziali. Per altre informazioni, vedere [creare una firma di](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)accesso condiviso di delega utente. | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Collaboratore condivisione SMB dati file di archiviazione](#storage-file-data-smb-share-contributor) | Consente l'accesso in lettura, scrittura ed eliminazione su file/directory in condivisioni file di Azure. Questo ruolo non ha un equivalente incorporato nei file server Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [File di archiviazione dati condivisione SMB con privilegi elevati](#storage-file-data-smb-share-elevated-contributor) | Consente la lettura, la scrittura, l'eliminazione e la modifica degli ACL nei file e nelle directory delle condivisioni file di Azure. Questo ruolo è equivalente a un ACL di condivisione file delle modifiche nei file server Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Lettore condivisione SMB dati file di archiviazione](#storage-file-data-smb-share-reader) | Consente l'accesso in lettura a file/directory in condivisioni file di Azure. Questo ruolo è equivalente a un ACL di condivisione file di Read nei file server Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Collaboratore dati coda di archiviazione](#storage-queue-data-contributor) | Lettura, scrittura ed eliminazione di code e messaggi di Accodamento di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere [autorizzazioni per la chiamata di operazioni di dati BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Processore dei messaggi dati della coda di archiviazione](#storage-queue-data-message-processor) | Visualizzazione, recupero ed eliminazione di un messaggio da una coda di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere [autorizzazioni per la chiamata di operazioni di dati BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Mittente messaggio dati coda di archiviazione](#storage-queue-data-message-sender) | Aggiungere messaggi a una coda di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere [autorizzazioni per la chiamata di operazioni di dati BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Lettore dati della coda di archiviazione](#storage-queue-data-reader) | Leggere ed elencare le code e i messaggi della coda di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere [autorizzazioni per la chiamata di operazioni di dati BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | [Collaboratore alla richiesta di supporto](#support-request-contributor) | Consente di creare e gestire le richieste di supporto. | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Collaboratore Gestione traffico](#traffic-manager-contributor) | Consente di gestire i profili di Gestione traffico, ma non di controllare chi è autorizzato ad accedervi. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | [Amministratore accessi utente](#user-access-administrator) | Consente di gestire gli accessi utente alle risorse di Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | [Virtual Machine Administrator Login](#virtual-machine-administrator-login) (Accesso amministratore macchina virtuale) | Consente di visualizzare le macchine virtuali nel portale e di accedere come amministratore | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Collaboratore macchine virtuali](#virtual-machine-contributor) | Consente di gestire le macchine virtuali, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Virtual Machine User Login](#virtual-machine-user-login) (Accesso utente macchina virtuale) | Consente di visualizzare le macchine virtuali nel portale e di accedere come utente normale. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | [Collaboratore piani Web](#web-plan-contributor) | Consente di gestire i piani Web per i siti Web, ma non di accedervi. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Collaboratore siti Web](#website-contributor) | Consente di gestire i siti Web (non i piani Web), ma non di accedervi. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | [Collaboratore cartella di lavoro](#workbook-contributor) | Consente di salvare le cartelle di lavoro condivise. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Lettore cartella di lavoro](#workbook-reader) | Consente di leggere le cartelle di lavoro. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |


## <a name="general"></a>Generale


### <a name="contributor"></a>Contributor

Consente di gestire tutto tranne che per concedere l'accesso alle risorse.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | * | È in grado di creare e gestire ogni tipo di risorsa |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Eliminazione di ruoli, assegnazioni di criteri, definizioni dei criteri e definizioni dei set di criteri |
> | Microsoft.Authorization/*/Write | Creazione di ruoli, assegnazioni di ruolo, assegnazioni dei criteri, definizioni dei criteri e definizioni dei set di criteri |
> | Microsoft.Authorization/elevateAccess/Action | Concede al chiamante l'accesso di tipo Amministratore Accesso utenti a livello dell'ambito del tenant |
> | Microsoft.Blueprint/blueprintAssignments/write | Crea o aggiorna le assegnazioni del progetto |
> | Microsoft.Blueprint/blueprintAssignments/delete | Elimina tutte le assegnazioni di progetto |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Proprietario

Consente di gestire tutto, incluso l'accesso alle risorse.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | * | È in grado di creare e gestire ogni tipo di risorsa |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything, including access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Reader

Consente di visualizzare tutti gli elementi, ma senza apportare alcuna modifica.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything, but not make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Amministratore accessi utente

Consente di gestire gli accessi utente alle risorse di Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>Intelligenza artificiale e Machine Learning


### <a name="cognitive-services-contributor"></a>Collaboratore Servizi cognitivi

Consente di creare, leggere, aggiornare, eliminare e gestire le chiavi di Servizi cognitivi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Lettore dati di servizi cognitivi (anteprima)

Consente di leggere i dati di servizi cognitivi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Utente Servizi cognitivi

Consente di leggere ed elencare le chiavi di Servizi cognitivi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Analytics


### <a name="azure-event-hubs-data-owner"></a>Proprietario dati di hub eventi di Azure

Consente l'accesso completo alle risorse di hub eventi di Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.EventHub/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Ricevitore di dati di hub eventi di Azure

Consente di ricevere l'accesso alle risorse di hub eventi di Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. EventHub/*/Eventhubs/consumergroups/Read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Mittente dati di hub eventi di Azure

Consente di inviare l'accesso alle risorse di hub eventi di Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. EventHub/*/Eventhubs/Read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft. EventHub/*/Send/Action |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Collaboratore Data Factory

Consente di creare e gestire data factory, oltre alle risorse figlio in esse contenute.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.DataFactory/dataFactories/* | Creare e gestire data factory e le relative risorse figlio. |
> | Microsoft.DataFactory/factories/* | Creare e gestire data factory e le relative risorse figlio. |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.EventGrid/eventSubscriptions/write | Crea o aggiorna un sottoscrizione evento |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Pulizia dati

Può eliminare i dati di analisi

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Ripulitura dei dati da Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read | Visualizzare i dati di log Analytics |
> | Microsoft.OperationalInsights/workspaces/purge/action | Elimina i dati specificati dall'area di lavoro |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>Operatore cluster HDInsight

Consente di leggere e modificare le configurazioni del cluster HDInsight.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. HDInsight/*/Read |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | Ottenere le impostazioni del gateway per il cluster HDInsight |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Aggiornare le impostazioni del gateway per il cluster HDInsight |
> | Microsoft. HDInsight/Clusters/Configurations/* |  |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>Collaboratore Servizi di dominio HDInsight

Può leggere, creare, modificare ed eliminare operazioni correlate ai Servizi di dominio necessarie per HDInsight Enterprise Security Package

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Collaboratore di Log Analytics

Il ruolo Collaboratore di Log Analytics può leggere tutti i dati di monitoraggio e modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log dall'Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di soluzioni e la configurazione di Diagnostica di Azure in tutte le risorse di Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Lettore di Log Analytics

Il ruolo Lettore di Log Analytics può visualizzare ed eseguire ricerche in tutti i dati di monitoraggio e può visualizzare le impostazioni di monitoraggio, inclusa la visualizzazione della configurazione di Diagnostica di Azure in tutte le risorse di Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockchain


### <a name="blockchain-member-node-access-preview"></a>Accesso al nodo membro blockchain (anteprima)

Consente l'accesso ai nodi membro blockchain

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Ottiene o elenca i nodi di transazione del membro blockchain esistenti. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Stabilisce la connessione a un nodo di transazione membro blockchain. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Calcolo


### <a name="classic-virtual-machine-contributor"></a>Collaboratore macchine virtuali classiche

Consente di gestire le macchine virtuali classiche, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they’re connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Virtual Machine Administrator Login (Accesso amministratore macchina virtuale)

Consente di visualizzare le macchine virtuali nel portale e di accedere come amministratore

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Collaboratore macchine virtuali

Consente di gestire le macchine virtuali, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Compute/availabilitySets/* | Creare e gestire set di disponibilità di calcolo |
> | Microsoft.Compute/locations/* | Creare e gestire percorsi di calcolo |
> | Microsoft.Compute/virtualMachines/* | Creare e gestire macchine virtuali |
> | Microsoft.Compute/virtualMachineScaleSets/* | Creare e gestire i set di scalabilità delle macchine virtuali |
> | Microsoft.Compute/disks/write | Crea un nuovo disco o ne aggiorna uno esistente |
> | Microsoft.Compute/disks/read | Ottiene le proprietà di un disco |
> | Microsoft.Compute/disks/delete | Elimina il disco |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Aggiunge un pool di indirizzi back-end del gateway applicazione. Non avvisabile. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Aggiunge un pool di indirizzi back-end del servizio di bilanciamento del carico. Non avvisabile. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Aggiunge un pool NAT in ingresso del servizio di bilanciamento del carico. Non avvisabile. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Aggiunge una regola NAT in ingresso del servizio di bilanciamento del carico. Non avvisabile. |
> | Microsoft.Network/loadBalancers/probes/join/action | Consente l'uso di probe di un servizio di bilanciamento del carico. Con questa autorizzazione, ad esempio, la proprietà healthProbe di un set di scalabilità di macchine virtuali può fare riferimento al probe. Non avvisabile. |
> | Microsoft.Network/loadBalancers/read | Ottiene una definizione del servizio di bilanciamento del carico |
> | Microsoft.Network/locations/* | Creare e gestire percorsi di rete |
> | Microsoft.Network/networkInterfaces/* | Creare e gestire interfacce di rete |
> | Microsoft.Network/networkSecurityGroups/join/action | Aggiunge un gruppo di sicurezza di rete. Non avvisabile. |
> | Microsoft.Network/networkSecurityGroups/read | Ottiene una definizione del gruppo di sicurezza di rete |
> | Microsoft.Network/publicIPAddresses/join/action | Aggiunge un indirizzo IP pubblico. Non avvisabile. |
> | Microsoft.Network/publicIPAddresses/read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | Microsoft.Network/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Viene aggiunto a una rete virtuale. Non avvisabile. |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Virtual Machine User Login (Accesso utente macchina virtuale)

Consente di visualizzare le macchine virtuali nel portale e di accedere come utente normale.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Contenitori


### <a name="acrdelete"></a>AcrDelete

eliminazione di ACR

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Elimina l'artefatto in un registro contenitori. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

firmatario immagine acr

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Eseguire il push/pull di metadati considerati attendibili per un registro contenitori. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

acr pull

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Eseguire il pull o ottenere immagini da un registro contenitori. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

acr push

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Eseguire il pull o ottenere immagini da un registro contenitori. |
> | Microsoft.ContainerRegistry/registries/push/write | Eseguire il push o scrivere immagini in un registro contenitori. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

lettore di dati di quarantena acr

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. ContainerRegistry/registri/quarantena/lettura | Eseguire il pull o ottenere immagini in quarantena da un registro contenitori |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

writer di dati di quarantena acr

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. ContainerRegistry/registri/quarantena/lettura | Eseguire il pull o ottenere immagini in quarantena da un registro contenitori |
> | Microsoft. ContainerRegistry/registri/quarantena/scrittura | Scrivere/modificare lo stato di quarantena di immagini in quarantena |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Ruolo di amministratore del cluster del servizio Azure Kubernetes

Elencare l'azione delle credenziali di amministratore del cluster.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Elenca la credenziale clusterAdmin di un cluster gestito |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Recupera il profilo di accesso per un cluster gestito in base al nome del ruolo con un'operazione di elenco delle credenziali |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Ruolo di utente del cluster del servizio Azure Kubernetes

Elencare l'azione delle credenziali di utente del cluster.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Elenca la credenziale clusterUser di un cluster gestito |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Database


### <a name="cosmos-db-account-reader-role"></a>Ruolo Lettore dell'account Cosmos DB

Può leggere i dati degli account Azure Cosmos DB. Vedere [Collaboratore account DocumentDB](#documentdb-account-contributor) per la gestione degli account Azure Cosmos DB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Operatore Cosmos DB

Consente di gestire gli account Azure Cosmos DB, ma non di accedere ai dati in essi contenuti. Impedisce l'accesso alle chiavi dell'account e alle stringhe di connessione.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Non avvisabile. |
> | **NotActions** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Può inviare una richiesta di ripristino per un database di Cosmos DB o un contenitore per un account

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Invia una richiesta per configurare il backup |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Invia una richiesta di ripristino |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>Collaboratore account DocumentDB

È in grado di gestire account Azure Cosmos DB. Azure Cosmos DB era precedentemente noto come DocumentDB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.DocumentDb/databaseAccounts/* | Creare e gestire account Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Non avvisabile. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Collaboratore cache Redis

Consente di gestire le cache Redis, ma non di accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Cache/redis/* | Creare e gestire cache Redis |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>Collaboratore database SQL

Consente di gestire i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza o i rispettivi server SQL padre.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>Collaboratore Istanza gestita SQL

Consente di gestire le istanze gestite di SQL e la configurazione di rete richiesta, ma non di concedere l'accesso ad altri utenti.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can’t give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>Gestione della sicurezza SQL

Consente di gestire i criteri relativi alla sicurezza di server e database SQL, ma non di accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Non avvisabile. |
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
> | Microsoft. SQL/managedInstances/databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Creare e gestire criteri di controllo di server SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Creare e gestire le impostazioni di controllo di SQL Server |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Recupera i dettagli dei criteri di controllo BLOB del server esteso configurati in uno specifico server |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Creare e gestire i criteri di controllo dei database SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Creare e gestire le impostazioni di controllo dei database di SQL Server |
> | Microsoft.Sql/servers/databases/auditRecords/read | Recupera i record di controllo BLOB del database |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Creare e gestire i criteri di connessione dei database dei server SQL |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Creare e gestire i criteri della maschera dei dati dei database dei server SQL |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Recupera i dettagli dei criteri di controllo BLOB esteso configurati in uno specifico database |
> | Microsoft.Sql/servers/databases/read | Restituisce l'elenco dei database o ottiene le proprietà per il database specificato |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Ottenere uno schema di database. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Ottenere una colonna di database. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Ottenere una tabella di database. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Creare e gestire i criteri degli avvisi di sicurezza dei database di SQL Server |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Creare e gestire le metriche di sicurezza dei database di server SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft. SQL/Servers/databases/transparentDataEncryption/* |  |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>Collaboratore SQL Server

Consente di gestire i server e i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | Microsoft.Sql/servers/databases/auditRecords/read | Recupera i record di controllo BLOB del database |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="developer-tools"></a>Strumenti sviluppatori


### <a name="app-configuration-data-owner"></a>Proprietario dati di configurazione dell'app

Consente l'accesso completo ai dati di configurazione dell'app.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft. AppConfiguration/configurationStores/*/Read |  |
> | Microsoft. AppConfiguration/configurationStores/*/Write |  |
> | Microsoft. AppConfiguration/configurationStores/*/Delete |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Lettore dati di configurazione dell'app

Consente l'accesso in lettura ai dati di configurazione dell'app.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft. AppConfiguration/configurationStores/*/Read |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Lab Creator (Creatore di lab)

Consente di creare, gestire ed eliminare i lab gestiti con gli account di Azure Lab.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Crea un lab in un account del lab. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Ottiene informazioni sulla disponibilità a livello di area per ogni categoria di dimensioni configurata in un account del lab |
> | Microsoft. LabServices/labAccounts/getPricingAndAvailability/Action | Ottenere i prezzi e la disponibilità di combinazioni di dimensioni, aree geografiche e sistemi operativi per l'account Lab. |
> | Microsoft. LabServices/labAccounts/getRestrictionsAndUsage/Action | Ottenere restrizioni e utilizzo di base per questa sottoscrizione |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, manage, delete your managed labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>Utente DevTest Labs

Consente di connettere, avviare, riavviare e arrestare le macchine virtuali in Azure DevTest Labs.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Verificare che l'utente corrente disponga di un profilo valido nel Lab. |
> | Microsoft.DevTestLab/labs/formulas/delete | Elimina le formule. |
> | Microsoft.DevTestLab/labs/formulas/read | Esegue la lettura di formule. |
> | Microsoft.DevTestLab/labs/formulas/write | Aggiunge o modifica le formule. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Valuta i criteri del lab. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Consente di assumere la proprietà di una macchina virtuale esistente |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Elenca le pianificazioni di avvio/arresto applicabili, se presenti. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Ottiene una stringa che rappresenta il contenuto del file RDP per la macchina virtuale |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Aggiunge un pool di indirizzi back-end del servizio di bilanciamento del carico. Non avvisabile. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Aggiunge una regola NAT in ingresso del servizio di bilanciamento del carico. Non avvisabile. |
> | Microsoft.Network/networkInterfaces/*/read | Legge le proprietà di un'interfaccia di rete, ad esempio tutti i servizi di bilanciamento del carico di cui fa parte l'interfaccia di rete |
> | Microsoft.Network/networkInterfaces/join/action | Aggiunge una macchina virtuale a un'interfaccia di rete. Non avvisabile. |
> | Microsoft.Network/networkInterfaces/read | Ottiene una definizione dell’interfaccia di rete.  |
> | Microsoft.Network/networkInterfaces/write | Crea un'interfaccia di rete o ne aggiorna una esistente.  |
> | Microsoft.Network/publicIPAddresses/*/read | Leggere le proprietà di un indirizzo IP pubblico |
> | Microsoft.Network/publicIPAddresses/join/action | Aggiunge un indirizzo IP pubblico. Non avvisabile. |
> | Microsoft.Network/publicIPAddresses/read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Viene aggiunto a una rete virtuale. Non avvisabile. |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="hybrid"></a>Ibrido


### <a name="azure-connected-machine-onboarding"></a>Onboarding del computer connesso di Azure

Consente di caricare i computer connessi ad Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. HybridCompute/machines/Read | Leggi tutti i computer Azure Arc |
> | Microsoft. HybridCompute/machines/Write | Scrive un computer Azure Arc |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Assegnazione di configurazione guest Get. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Amministratore delle risorse del computer connesso ad Azure

Può leggere, scrivere, eliminare e ricaricare i computer connessi di Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. HybridCompute/machines/Read | Leggi tutti i computer Azure Arc |
> | Microsoft. HybridCompute/machines/Write | Scrive un computer Azure Arc |
> | Microsoft. HybridCompute/machines/Delete | Elimina un computer Azure Arc |
> | Microsoft. HybridCompute/machines/Riconnetti/azione | Riconnette un computer Azure Arc |
> | Microsoft. HybridCompute/*/Read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Proprietario della registrazione di Azure Stack

Consente di gestire le registrazioni di Azure Stack.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. AzureStack/registrations/Products/*/Action |  |
> | Microsoft.AzureStack/registrations/products/read | Ottiene le proprietà di un prodotto del marketplace Azure Stack |
> | Microsoft.AzureStack/registrations/read | Ottiene le proprietà di una registrazione di Azure Stack |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Identity


### <a name="managed-identity-contributor"></a>Managed Identity Contributor (Collaboratore per identità gestita)

Crea, legge, aggiorna ed elimina l'identità assegnata all'utente

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Ottiene l'identità assegnata a un utente esistente |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Crea una nuova identità assegnata a un utente esistente o aggiorna i tag associati a un'identità assegnata a un utente esistente |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Elimina l'identità assegnata a un utente esistente |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Managed Identity Operator (Operatore per identità gestita)

Legge e assegna l'identità assegnata all'utente

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Integration


### <a name="api-management-service-contributor"></a>Collaboratore servizio Gestione API

Può gestire il servizio e le API.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/* | È in grado di creare e gestire il servizio Gestione API |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>Ruolo operatore del servizio Gestione API

Può gestire il servizio ma non le API.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/*/read | Leggere le istanze del servizio Gestione API |
> | Microsoft.ApiManagement/service/backup/action | Esegue il backup del servizio Gestione API nel contenitore specificato in un account di archiviazione fornito dall’utente |
> | Microsoft.ApiManagement/service/delete | Elimina l’istanza del servizio Gestione API |
> | Microsoft.ApiManagement/service/managedeployments/action | Modifica SKU/unità, aggiunge/rimuove distribuzioni regionali del servizio Gestione API |
> | Microsoft.ApiManagement/service/read | Leggere i metadati per un'istanza del servizio Gestione API |
> | Microsoft.ApiManagement/service/restore/action | Ripristinare il servizio Gestione API dal contenitore specificato in un account di archiviazione fornito dall'utente |
> | Microsoft.ApiManagement/service/updatecertificate/action | Carica il certificato SSL per un servizio Gestione API. |
> | Microsoft.ApiManagement/service/updatehostname/action | Configura, aggiorna o rimuove i nomi di dominio personalizzati per un servizio Gestione API. |
> | Microsoft.ApiManagement/service/write | Crea o aggiorna l'istanza del servizio gestione API |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>Ruolo lettura del servizio Gestione API

Consente l'accesso di sola lettura al servizio e alle API.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/*/read | Leggere le istanze del servizio Gestione API |
> | Microsoft.ApiManagement/service/read | Leggere i metadati per un'istanza del servizio Gestione API |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Proprietario dati del bus di servizio di Azure

Consente l'accesso completo alle risorse del bus di servizio di Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Ricevitore dati del bus di servizio di Azure

Consente di ricevere l'accesso alle risorse del bus di servizio di Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/receive/action |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Mittente dati del bus di servizio di Azure

Consente di inviare l'accesso alle risorse del bus di servizio di Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/send/action |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Collaboratore account Intelligent Systems

Consente di gestire gli account Sistemi intelligenti, ma non di accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Collaboratore alle app per la logica

Consente di gestire le app per la logica, ma non di modificarne l'accesso.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | Microsoft.ClassicStorage/storageAccounts/read | Restituisce l'account di archiviazione con l'account specificato. |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Insights/metricAlerts/* |  |
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
> | Microsoft.Web/sites/functions/listSecrets/action | Elencare i segreti per le funzioni. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Operatore delle app per la logica

Consente di leggere, abilitare e disabilitare le app per la logica, ma non di modificarle o aggiornarle.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/*/read | Legge le regole di avviso di Insights |
> | Microsoft. Insights/metricAlerts/*/Read |  |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="internet-of-things"></a>Internet delle cose


### <a name="eventgrid-eventsubscription-contributor"></a>Collaboratore per sottoscrizioni di eventi di Griglia di eventi

Consente di gestire le operazioni di sottoscrizione di eventi EventGrid.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Elenca le sottoscrizioni di eventi globali per tipo di argomento |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Elenca sottoscrizioni di eventi per area |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>Ruolo con autorizzazioni di lettura per sottoscrizioni di eventi di Griglia di eventi

Consente di leggere le sottoscrizioni di eventi EventGrid.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.EventGrid/eventSubscriptions/read | Leggi un sottoscrizione evento |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Elenca le sottoscrizioni di eventi globali per tipo di argomento |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Elenca sottoscrizioni di eventi per area |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Elenca sottoscrizioni di eventi per area per tipo di argomento |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Gestione e governance


### <a name="application-insights-component-contributor"></a>Collaboratore componente di Application Insights

È in grado di gestire i componenti di Application Insights

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso classiche |
> | Microsoft.Insights/metricAlerts/* | Crea e Gestisci nuove regole di avviso |
> | Microsoft.Insights/components/* | È in grado di creare e gestire i componenti di Insights |
> | Microsoft.Insights/webtests/* | Creare e gestire i test Web di Insights |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Debugger di snapshot di Application Insights

Concede l'autorizzazione utente per visualizzare e scaricare gli snapshot di debug raccolti con Application Insights Snapshot Debugger. Si noti che queste autorizzazioni non sono incluse nei ruoli [Proprietario](#owner) e [Collaboratore](#contributor).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-job-operator"></a>Operatore processo di automazione

Consente di creare e gestire i processi tramite i runbook di Automazione.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Operatore di automazione

Gli operatori di automazione possono avviare, arrestare, sospendere e riprendere processi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Operatore runbook di automazione

Consente di leggere le proprietà del runbook per permettere di creare processi del runbook.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Lettore per la fatturazione

Consente l'accesso in lettura ai dati di fatturazione.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Collaboratore progetto

Può gestire le definizioni di progetto, ma non assegnarle.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft. Blueprint/cianografies/* | Creare e gestire definizioni di progetto o artefatti del progetto. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Operatore Blueprint

Consente di assegnare progetti pubblicati esistenti, ma non di creare nuovi progetti. 

> [!NOTE] 
> Questa operazione funziona solo se l'assegnazione viene eseguita con un'identità gestita assegnata dall'utente.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft. Blueprint/blueprintAssignments/* | Creare e gestire le assegnazioni di progetto. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Collaboratore Gestione costi

Può visualizzare i costi e gestire la configurazione dei costi, ad esempio budget ed esportazioni

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Advisor/configurations/read | Ottiene le configurazioni |
> | Microsoft.Advisor/recommendations/read | Legge i suggerimenti |
> | Microsoft.Management/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Lettore Gestione costi

Può visualizzare la configurazione e i dati dei costi, ad esempio budget ed esportazioni

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Ottiene l'elenco delle sottoscrizioni. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Advisor/configurations/read | Ottiene le configurazioni |
> | Microsoft.Advisor/recommendations/read | Legge i suggerimenti |
> | Microsoft.Management/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Ruolo Collaboratore applicazione gestita

Consente di creare risorse di applicazioni gestite.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | Microsoft. Solutions/Applications/* |  |
> | Microsoft.Solutions/register/action | Esegue la registrazione in Microsoft Solutions. |
> | Microsoft. resources/subscriptions/resourceGroups/* |  |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Ruolo di Operatore applicazione gestita

Consente di leggere ed eseguire azioni sulle risorse dell'applicazione gestita

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Lettore applicazioni gestite

Consente di leggere le risorse in un accesso di app gestita e JIT richiesta.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Ruolo eliminazione assegnazione registrazione servizi gestiti

Il ruolo eliminazione di assegnazione di registrazione dei servizi gestiti consente agli utenti di gestire i tenant di eliminare l'assegnazione di registrazione assegnata al tenant.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. ManagedServices/registrationAssignments/Read | Recupera un elenco di assegnazioni di registrazione dei servizi gestiti. |
> | Microsoft. ManagedServices/registrationAssignments/Delete | Rimuove l'assegnazione di registrazione dei servizi gestiti. |
> | Microsoft. ManagedServices/operationStatuses/Read | Legge lo stato dell'operazione per la risorsa. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Collaboratore gruppo di gestione

Ruolo Collaboratore gruppo di gestione

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Lettore gruppo di gestione

Ruolo Lettore gruppo di gestione

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Management/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Collaboratore al monitoraggio

Può leggere tutti i dati del monitoraggio e modificare le impostazioni di monitoraggio. Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Insights/components/* | È in grado di creare e gestire i componenti di Insights |
> | Microsoft.Insights/DiagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | Microsoft.Insights/eventtypes/* | Elenco degli eventi dei registri attività (eventi di gestione) in una sottoscrizione. Questa autorizzazione è applicabile sia all'accesso programmatico che all'accesso al portale per il registro attività. |
> | Microsoft.Insights/LogDefinitions/* | Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai log attività tramite il portale. Elencare categorie di log nel log attività. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Definizioni delle metriche (elenco dei tipi di metriche disponibili per una risorsa). |
> | Microsoft.Insights/Metrics/* | Metriche per una risorsa. |
> | Microsoft.Insights/Register/Action | Registra il provider Microsoft Insights |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Creare e gestire i test Web di Insights |
> | Microsoft. Insights/cartelle di lavoro/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Pacchetti di soluzioni di log Analytics di lettura/scrittura/eliminazione. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Lettura/scrittura/eliminazione delle ricerche salvate di log Analytics. |
> | Microsoft.OperationalInsights/workspaces/search/action | Esegue una query di ricerca |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Recupera le chiavi condivise per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Lettura/scrittura/eliminazione delle configurazioni di Insight di archiviazione di log Analytics. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft. AlertsManagement/smartDetectorAlertRules/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.WorkloadMonitor/notificationSettings/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Autore delle metriche di monitoraggio

Abilitare la pubblicazione di metriche nelle risorse di Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Lettore di monitoraggio

Può leggere tutti i dati del monitoraggio (metriche, log e così via). Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Collaboratore account New Relic APM

Consente di gestire gli account e le applicazioni di APR New Relic, ma non di accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Writer di dati di policy Insights (anteprima)

Consente l'accesso in lettura ai criteri delle risorse e l'accesso in scrittura agli eventi dei criteri dei componenti delle risorse.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. Authorization/policyassignments/Read | Ottiene informazioni su un'assegnazione di criteri. |
> | Microsoft. Authorization/PolicyDefinitions/Read | Ottiene informazioni su una definizione di criteri. |
> | Microsoft. Authorization/policysetdefinitions/Read | Ottiene informazioni su una definizione di set di criteri. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft. PolicyInsights/checkDataPolicyCompliance/Action | Verificare lo stato di conformità di un determinato componente in base ai criteri di dati. |
> | Microsoft. PolicyInsights/policyEvents/logDataEvents/Action | Registrare gli eventi del criterio componente risorsa. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Collaboratore per i criteri delle risorse

Utenti con diritti per creare/modificare i criteri delle risorse, creare un ticket di supporto e leggere risorse/gerarchie.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Collaboratore raccolte di processi dell'unità di pianificazione

Consente di gestire le raccolte di processi dell'utilità di pianificazione, ma non di accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Collaboratore al ripristino sito

Consente di gestire il servizio Site Recovery ad eccezione della creazione dell'insieme di credenziali e dell'assegnazione di ruolo.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
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
> | Microsoft. RecoveryServices/Vaults/replicationOperationStatus/Read | Legge lo stato dell'operazione di replica dell'insieme di credenziali |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Operatore del ripristino sito

Consente di eseguire il failover e il failback ma non di eseguire altre operazioni di gestione di Site Recovery.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
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
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Cambia il contenitore di protezione |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Reader di ripristino sito

Consente di visualizzare lo stato di Site Recovery ma non di eseguire altre operazioni di gestione.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Collaboratore alla richiesta di supporto

Consente di creare e gestire le richieste di supporto.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Collaboratore cartella di lavoro

Consente di salvare le cartelle di lavoro condivise.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. Insights/cartelle di lavoro/scrittura | Creazione o aggiornamento di una cartella di lavoro |
> | Microsoft. Insights/cartelle di lavoro/eliminazione | Eliminare una cartella di lavoro |
> | Microsoft. Insights/cartelle di lavoro/lettura | Lettura di una cartella di lavoro |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Lettore cartella di lavoro

Consente di leggere le cartelle di lavoro.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. Insights/cartelle di lavoro/lettura | Lettura di una cartella di lavoro |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Realtà mista


### <a name="spatial-anchors-account-contributor"></a>Collaboratore account ancoraggi spaziali

Consente di gestire gli ancoraggi spaziali nell'account, ma non di eliminarli

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Creare ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Individuare gli ancoraggi spaziali vicini |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Ottenere le proprietà degli ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Individuare ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Inviare i dati di diagnostica per contribuire a migliorare la qualità del servizio ancoraggi spaziali di Azure |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Aggiornare le proprietà degli ancoraggi spaziali |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Proprietario dell'account di ancoraggi spaziali

Consente di gestire gli ancoraggi spaziali nell'account, inclusa l'eliminazione

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Creare ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Elimina ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Individuare gli ancoraggi spaziali vicini |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Ottenere le proprietà degli ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Individuare ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Inviare i dati di diagnostica per contribuire a migliorare la qualità del servizio ancoraggi spaziali di Azure |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Aggiornare le proprietà degli ancoraggi spaziali |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Lettore di account ancoraggi spaziali

Consente di individuare e leggere le proprietà degli ancoraggi spaziali nell'account

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Individuare gli ancoraggi spaziali vicini |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Ottenere le proprietà degli ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Individuare ancoraggi spaziali |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Inviare i dati di diagnostica per contribuire a migliorare la qualità del servizio ancoraggi spaziali di Azure |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Rete


### <a name="cdn-endpoint-contributor"></a>Collaboratore endpoint rete CDN

Può gestire gli endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can’t grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>Lettore endpoint rete CDN

Può visualizzare gli endpoint della rete CDN, ma non può apportare modifiche.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can’t make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>Collaboratore profilo rete CDN

Può gestire i profili e i rispettivi endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can’t grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>Lettore profilo rete CDN

Può visualizzare i profili e i rispettivi endpoint della rete CDN, ma non può apportare modifiche.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can’t make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Collaboratore reti virtuali classiche

Consente di gestire le reti classiche, ma non di accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>Collaboratore zona DNS

Consente di gestire le zone DNS e i set di record in DNS di Azure, ma non di controllare chi è autorizzato ad accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Collaboratore di rete

Consente di gestire le reti, ma non di accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Collaboratore Gestione traffico

Consente di gestire i profili di Gestione traffico, ma non di controllare chi è autorizzato ad accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Sicurezza


### <a name="azure-sentinel-contributor"></a>Collaboratore sentinella di Azure

Collaboratore sentinella di Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. SecurityInsights/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Esegue la ricerca usando il nuovo motore. |
> | Microsoft.OperationalInsights/workspaces/read | Ottiene un'area di lavoro esistente |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* |  |
> | Microsoft.OperationsManagement/solutions/read | Ottiene soluzione OMS esistente |
> | Microsoft.OperationalInsights/workspaces/query/read | Esegue query sui dati nell'area di lavoro |
> | Microsoft. OperationalInsights/Workspaces/query/*/read |  |
> | Microsoft. OperationalInsights/aree di lavoro/origini dati/lettura | Ottiene le origini dati in un'area di lavoro. |
> | Microsoft. Insights/cartelle di lavoro/* |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Lettore sentinella di Azure

Lettore sentinella di Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. SecurityInsights/*/Read |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Esegue la ricerca usando il nuovo motore. |
> | Microsoft.OperationalInsights/workspaces/read | Ottiene un'area di lavoro esistente |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Ottiene una query di ricerca salvata |
> | Microsoft.OperationsManagement/solutions/read | Ottiene soluzione OMS esistente |
> | Microsoft.OperationalInsights/workspaces/query/read | Esegue query sui dati nell'area di lavoro |
> | Microsoft. OperationalInsights/Workspaces/query/*/read |  |
> | Microsoft. OperationalInsights/aree di lavoro/origini dati/lettura | Ottiene le origini dati in un'area di lavoro. |
> | Microsoft. Insights/cartelle di lavoro/lettura | Lettura di una cartella di lavoro |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Risponditore sentinella di Azure

Risponditore sentinella di Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. SecurityInsights/*/Read |  |
> | Microsoft. SecurityInsights/Cases/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Esegue la ricerca usando il nuovo motore. |
> | Microsoft.OperationalInsights/workspaces/read | Ottiene un'area di lavoro esistente |
> | Microsoft. OperationalInsights/aree di lavoro/origini dati/lettura | Ottiene le origini dati in un'area di lavoro. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Ottiene una query di ricerca salvata |
> | Microsoft.OperationsManagement/solutions/read | Ottiene soluzione OMS esistente |
> | Microsoft.OperationalInsights/workspaces/query/read | Esegue query sui dati nell'area di lavoro |
> | Microsoft. OperationalInsights/Workspaces/query/*/read |  |
> | Microsoft. OperationalInsights/aree di lavoro/origini dati/lettura | Ottiene le origini dati in un'area di lavoro. |
> | Microsoft. Insights/cartelle di lavoro/lettura | Lettura di una cartella di lavoro |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Collaboratore di Key Vault

Consente di gestire gli insiemi di credenziali delle chiavi, ma non di accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Amministrazione della protezione

è possibile visualizzare i criteri di sicurezza e gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare gli avvisi e le raccomandazioni, ignorare gli avvisi e le raccomandazioni.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Authorization/policyAssignments/* | Creare e gestire assegnazioni di criteri |
> | Microsoft.Authorization/policyDefinitions/* | Creare e gestire definizioni di criteri |
> | Microsoft.Authorization/policySetDefinitions/* | Creare e gestire set di criteri |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Management/managementGroups/read | Elenca i gruppi di gestione per l'utente autenticato. |
> | Microsoft.operationalInsights/workspaces/*/read | Visualizzare i dati di log Analytics |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Collaboratore valutazione della sicurezza

Consente di eseguire il push delle valutazioni nel centro sicurezza

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft. Security/Assessment/scrittura | Creare o aggiornare valutazioni della sicurezza per la sottoscrizione |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Gestore sicurezza (legacy)

Questo è un ruolo legacy. Usare invece l'amministratore della sicurezza.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.ClassicCompute/*/read | Leggere le informazioni di configurazione delle macchine virtuali classiche |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Scrivere la configurazione delle macchine virtuali classiche |
> | Microsoft.ClassicNetwork/*/read | Leggere le informazioni della configurazione sulla rete classica |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Ruolo con autorizzazioni di lettura per la sicurezza

Consente di visualizzare le raccomandazioni e gli avvisi, visualizzare i criteri di sicurezza, visualizzare gli Stati di sicurezza, ma non apportare modifiche.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.operationalInsights/workspaces/*/read | Visualizzare i dati di log Analytics |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Archiviazione


### <a name="avere-contributor"></a>Collaboratore

Consente di creare e gestire un cluster vFXT.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft. Network/*/Read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | Microsoft.Network/virtualNetworks/subnets/read | Ottiene una definizione di subnet della rete virtuale |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Viene aggiunto a una rete virtuale. Non avvisabile. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Non avvisabile. |
> | Microsoft.Network/networkSecurityGroups/join/action | Aggiunge un gruppo di sicurezza di rete. Non avvisabile. |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft. storage/*/Read |  |
> | Microsoft.Storage/storageAccounts/* | Creare e gestire account di archiviazione |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Operatore di stato

Usato dal cluster vFXT per la gestione del cluster

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Compute/virtualMachines/read | Ottiene le proprietà di una macchina virtuale |
> | Microsoft.Network/networkInterfaces/read | Ottiene una definizione dell’interfaccia di rete.  |
> | Microsoft.Network/networkInterfaces/write | Crea un'interfaccia di rete o ne aggiorna una esistente.  |
> | Microsoft.Network/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | Microsoft.Network/virtualNetworks/subnets/read | Ottiene una definizione di subnet della rete virtuale |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Viene aggiunto a una rete virtuale. Non avvisabile. |
> | Microsoft.Network/networkSecurityGroups/join/action | Aggiunge un gruppo di sicurezza di rete. Non avvisabile. |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Collaboratore di backup

Consente di gestire il servizio di backup, ma non di creare insiemi di credenziali e concedere l'accesso ad altri utenti

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Network/virtualNetworks/read | Ottiene la definizione della rete virtuale |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Consente di gestire i risultati dell'operazione sulla gestione del backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Consente di creare e gestire i contenitori di backup all'interno delle infrastrutture di backup dell'insieme di credenziali dei Servizi di ripristino |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Aggiorna l'elenco di contenitori |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Consente di creare e gestire i processi di backup |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Esporta processi |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Operatore di backup

Consente di gestire i servizi di backup, ma non di rimuovere il backup, creare insiemi di credenziali e concedere l'accesso ad altri utenti.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Lettore di backup

Può visualizzare i servizi di backup, ma non può apportare modifiche.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Collaboratore account di archiviazione classico

Consente di gestire gli account di archiviazione classici, ma non di accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico

Gli operatori della chiave dell'account di archiviazione classico sono autorizzati a elencare e rigenerare le chiavi negli account di archiviazione classici

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Rigenera le chiavi di accesso esistenti per l'account di archiviazione. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Collaboratore Data Box

Consente di gestire tutto il servizio Data Box, ad eccezione della concessione dell'accesso ad altri utenti.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Lettore Data Box

Consente di gestire il servizio Data Box, ad eccezione della creazione di ordini, della modifica dei dettagli dell'ordine e della concessione dell'accesso ad altri utenti.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Elenca le credenziali non crittografate correlate all'ordine. |
> | Microsoft.Databox/locations/availableSkus/action | Restituisce l'elenco degli SKU disponibili. |
> | Microsoft. databox/locations/validateInputs/Action | Questo metodo esegue tutti i tipi di convalida. |
> | Microsoft. databox/locations/regionConfiguration/Action | Questo metodo restituisce le configurazioni per l'area. |
> | Microsoft.Databox/locations/validateAddress/action | Convalida l'indirizzo di spedizione e fornisce indirizzi alternativi, se disponibili. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Sviluppatore di Data Lake Analytics

Consente di inviare, monitorare e gestire i propri processi, ma non di creare o eliminare account Data Lake Analytics.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | Microsoft.DataLakeAnalytics/accounts/Delete | Elimina un account Analisi Data Lake. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Concede le autorizzazioni per annullare i processi inviati da altri utenti. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Crea o aggiorna un account Analisi Data Lake. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Crea o aggiorna un account Archivio Data Lake collegato a un account Analisi Data Lake. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Scollega un account Archivio Data Lake da un account Analisi Data Lake. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Crea o aggiorna un account di archiviazione collegato a un account Analisi Data Lake. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Scollega un account di archiviazione da un account Analisi Data Lake. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Crea o aggiorna una regola del firewall. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Elimina una regola del firewall. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Crea o aggiorna i criteri di calcolo. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Elimina criteri di calcolo. |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Lettore e accesso ai dati

Consente di visualizzare tutti gli elementi ma non consente di eliminare o creare un account di archiviazione o una risorsa contenuta. Consente anche l'accesso in lettura/scrittura a tutti i dati contenuti in un account di archiviazione tramite l'accesso alle chiavi dell'account di archiviazione.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Collaboratore account di archiviazione

Consente la gestione degli account di archiviazione. Consente di accedere alla chiave dell'account, che può essere usata per accedere ai dati tramite l'autorizzazione della chiave condivisa.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.Insights/diagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Non avvisabile. |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Ruolo del servizio dell'operatore della chiave dell'account di archiviazione

Consente di elencare e rigenerare le chiavi di accesso all'account di archiviazione.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Rigenera le chiavi di accesso per l'account di archiviazione specificato. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Collaboratore dati BLOB di archiviazione

Leggere, scrivere ed eliminare i contenitori e i BLOB di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere [autorizzazioni per la chiamata di operazioni di dati BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Eliminare un contenitore. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Restituisce un contenitore o un elenco di contenitori. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Modificare i metadati o le proprietà di un contenitore. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Restituisce una chiave di delega utente per il servizio BLOB. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Eliminare un BLOB. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Restituisce un BLOB o un elenco di BLOB. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Scrivere in un BLOB. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Proprietario dati BLOB di archiviazione

Fornisce accesso completo ai contenitori BLOB e ai dati di archiviazione di Azure, inclusa l'assegnazione del controllo di accesso POSIX. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere [autorizzazioni per la chiamata di operazioni di dati BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Autorizzazioni complete per i contenitori. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Restituisce una chiave di delega utente per il servizio BLOB. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Autorizzazioni complete per i BLOB. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Lettore dati BLOB di archiviazione

Leggere ed elencare i contenitori e i BLOB di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere [autorizzazioni per la chiamata di operazioni di dati BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Restituisce un contenitore o un elenco di contenitori. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Restituisce una chiave di delega utente per il servizio BLOB. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Restituisce un BLOB o un elenco di BLOB. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Delegatore BLOB di archiviazione

Ottenere una chiave di delega utente, che può quindi essere usata per creare una firma di accesso condiviso per un contenitore o un BLOB firmato con Azure AD credenziali. Per altre informazioni, vedere [creare una firma di](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)accesso condiviso di delega utente.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Restituisce una chiave di delega utente per il servizio BLOB. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Collaboratore condivisione SMB dati file di archiviazione

Consente l'accesso in lettura, scrittura ed eliminazione su file/directory in condivisioni file di Azure. Questo ruolo non ha un equivalente incorporato nei file server Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Restituisce un file o una cartella o un elenco di file/cartelle. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Restituisce il risultato della scrittura di un file o della creazione di una cartella. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Restituisce il risultato dell'eliminazione di un file o di una cartella. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>File di archiviazione dati condivisione SMB con privilegi elevati

Consente la lettura, la scrittura, l'eliminazione e la modifica degli ACL nei file e nelle directory delle condivisioni file di Azure. Questo ruolo è equivalente a un ACL di condivisione file delle modifiche nei file server Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Restituisce un file o una cartella o un elenco di file/cartelle. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Restituisce il risultato della scrittura di un file o della creazione di una cartella. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Restituisce il risultato dell'eliminazione di un file o di una cartella. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Restituisce il risultato della modifica dell'autorizzazione per un file o una cartella. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Lettore condivisione SMB dati file di archiviazione

Consente l'accesso in lettura a file/directory in condivisioni file di Azure. Questo ruolo è equivalente a un ACL di condivisione file di Read nei file server Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Restituisce un file o una cartella o un elenco di file/cartelle. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Collaboratore dati coda di archiviazione

Lettura, scrittura ed eliminazione di code e messaggi di Accodamento di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere [autorizzazioni per la chiamata di operazioni di dati BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Eliminare una coda. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Restituisce una coda o un elenco di code. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Modificare le proprietà o i metadati della coda. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Eliminare uno o più messaggi da una coda. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Visualizza o recupera uno o più messaggi da una coda. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Aggiungere un messaggio a una coda. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Processore dei messaggi dati della coda di archiviazione

Visualizzazione, recupero ed eliminazione di un messaggio da una coda di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere [autorizzazioni per la chiamata di operazioni di dati BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Visualizza un messaggio. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Recuperare ed eliminare un messaggio. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Mittente messaggio dati coda di archiviazione

Aggiungere messaggi a una coda di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere [autorizzazioni per la chiamata di operazioni di dati BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Aggiungere un messaggio a una coda. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Lettore dati della coda di archiviazione

Leggere ed elencare le code e i messaggi della coda di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere [autorizzazioni per la chiamata di operazioni di dati BLOB e di Accodamento](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Restituisce una coda o un elenco di code. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Visualizza o recupera uno o più messaggi da una coda. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-reader-preview"></a>Lettore di dati per Mappe di Azure (anteprima)

Concede l'accesso per leggere i dati correlati alla mappa da un account Azure maps.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft.Maps/accounts/data/read | Concede l'accesso in lettura ai dati in un account di Mappe. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Collaboratore servizi di ricerca

Consente di gestire i servizi di Ricerca, ma non di accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Collaboratore piani Web

Consente di gestire i piani Web per i siti Web, ma non di accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.Insights/alertRules/* | Creare e gestire le regole di avviso di Insight |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | Microsoft.Resources/deployments/* | Creare e gestire distribuzioni di gruppi di risorse |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Ottiene o elenca i gruppi di risorse. |
> | Microsoft.Support/* | Creare e gestire ticket di supporto |
> | Microsoft.Web/serverFarms/* | Creare e gestire server farm |
> | Microsoft.Web/hostingEnvironments/Join/Action | Aggiunge un ambiente del servizio app |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Collaboratore siti Web

Consente di gestire i siti Web (non i piani Web), ma non di accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Altro


### <a name="biztalk-contributor"></a>Collaboratore BizTalk

Consente di gestire i servizi BizTalk, ma non di accedervi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.BizTalkServices/BizTalk/* | È in grado di creare e gestire i servizi BizTalk |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Associare il provider di risorse al servizio](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Ruoli personalizzati di Azure](custom-roles.md)
- [Autorizzazioni nel Centro sicurezza di Azure](../security-center/security-center-permissions.md)
