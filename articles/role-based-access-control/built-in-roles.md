---
title: Ruoli predefiniti di Azure - Controllo degli accessi in base al ruolo di Azure
description: Questo articolo descrive i ruoli predefiniti di Azure per il controllo degli accessi in base al ruolo di Azure. Elenca Actions, NotActions, DataActions e NotDataActions.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 11/03/2020
ms.custom: generated
ms.openlocfilehash: a48a13fa18025254ee31344868d10181ae87c65e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685478"
---
# <a name="azure-built-in-roles"></a>Ruoli predefiniti di Azure

Il [controllo degli accessi in base al ruolo di Azure](overview.md) ha diversi ruoli predefiniti di Azure che è possibile assegnare a utenti, gruppi, entità servizio e identità gestite. Le assegnazioni di ruolo sono il modo in cui si controlla l'accesso alle risorse di Azure. Se i ruoli predefiniti non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare [ruoli personalizzati di Azure](custom-roles.md).

Questo articolo illustra i ruoli predefiniti di Azure, che sono in continua evoluzione. Per ottenere i ruoli più recenti, usare [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) o [az role definition list](/cli/azure/role/definition#az-role-definition-list). Se si cercano ruoli di amministratore per Azure Active Directory (Azure AD), vedere [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../active-directory/roles/permissions-reference.md).

La tabella seguente contiene una breve descrizione e l'ID univoco di ogni ruolo predefinito. Fare clic sul nome del ruolo per vedere l'elenco di `Actions`, `NotActions`, `DataActions` e `NotDataActions` per ogni ruolo. Per informazioni sul significato di queste azioni e su come si applicano ai piani di gestione e ai piani dati, vedere [Informazioni sulle definizioni dei ruoli di Azure](role-definitions.md).

## <a name="all"></a>Tutti

> [!div class="mx-tableFixed"]
> | Ruolo predefinito | Descrizione | ID |
> | --- | --- | --- |
> | **Generale** |  |  |
> | [Collaboratore](#contributor) | Concede l'accesso completo per la gestione di tutte le risorse, ma non consente di assegnare i ruoli in RBAC di Azure. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Proprietario](#owner) | Concede l'accesso completo per la gestione di tutte le risorse, inclusa la possibilità di assegnare ruoli in controllo degli accessi in base al ruolo | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Lettore](#reader) | Consente di visualizzare tutte le risorse, ma non di apportare modifiche. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Amministratore accessi utente](#user-access-administrator) | Consente di gestire gli accessi utente alle risorse di Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Calcolo** |  |  |
> | [Collaboratore macchine virtuali classiche](#classic-virtual-machine-contributor) | Consente di gestire le macchine virtuali classiche, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Virtual Machine Administrator Login](#virtual-machine-administrator-login) (Accesso amministratore macchina virtuale) | Consente di visualizzare le macchine virtuali nel portale e di accedere come amministratore | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Collaboratore macchine virtuali](#virtual-machine-contributor) | Consente di gestire le macchine virtuali, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Virtual Machine User Login](#virtual-machine-user-login) (Accesso utente macchina virtuale) | Consente di visualizzare le macchine virtuali nel portale e di accedere come utente normale. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Rete** |  |  |
> | [Collaboratore endpoint rete CDN](#cdn-endpoint-contributor) | Può gestire gli endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Lettore endpoint rete CDN](#cdn-endpoint-reader) | Può visualizzare gli endpoint della rete CDN, ma non può apportare modifiche. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Collaboratore profilo rete CDN](#cdn-profile-contributor) | Può gestire i profili e i rispettivi endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [Lettore profilo rete CDN](#cdn-profile-reader) | Può visualizzare i profili e i rispettivi endpoint della rete CDN, ma non può apportare modifiche. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Collaboratore reti virtuali classiche](#classic-network-contributor) | Consente di gestire le reti classiche, ma non di accedervi. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Collaboratore zona DNS](#dns-zone-contributor) | Consente di gestire le zone DNS e i set di record in DNS di Azure, ma non di controllare chi è autorizzato ad accedervi. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Collaboratore di rete](#network-contributor) | Consente di gestire le reti, ma non di accedervi. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Collaboratore zona DNS privato](#private-dns-zone-contributor) | Consente di gestire le risorse della zona DNS privata, ma non le reti virtuali a cui sono collegate. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Collaboratore Gestione traffico](#traffic-manager-contributor) | Consente di gestire i profili di Gestione traffico, ma non di controllare chi è autorizzato ad accedervi. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Collaboratore per Avere](#avere-contributor) | Può creare e gestire un cluster Avere vFXT. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Operatore di Avere](#avere-operator) | Usato dal cluster Avere vFXT per gestire il cluster | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Collaboratore di backup](#backup-contributor) | Consente di gestire il servizio di backup, ma non di creare insiemi di credenziali e concedere l'accesso ad altri utenti | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Operatore di backup](#backup-operator) | Consente di gestire i servizi di backup, ma non di rimuovere il backup, creare insiemi di credenziali e concedere l'accesso ad altri utenti. | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Lettore di backup](#backup-reader) | Può visualizzare i servizi di backup, ma non può apportare modifiche. | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Collaboratore account di archiviazione classico](#classic-storage-account-contributor) | Consente di gestire gli account di archiviazione classici, ma non di accedervi. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico](#classic-storage-account-key-operator-service-role) | Gli operatori della chiave dell'account di archiviazione classico sono autorizzati a elencare e rigenerare le chiavi negli account di archiviazione classici | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Collaboratore Data Box](#data-box-contributor) | Consente di gestire tutto il servizio Data Box, ad eccezione della concessione dell'accesso ad altri utenti. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Lettore Data Box](#data-box-reader) | Consente di gestire il servizio Data Box, ad eccezione della creazione di ordini, della modifica dei dettagli dell'ordine e della concessione dell'accesso ad altri utenti. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Sviluppatore di Data Lake Analytics](#data-lake-analytics-developer) | Consente di inviare, monitorare e gestire i propri processi, ma non di creare o eliminare account Data Lake Analytics. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Lettore e accesso ai dati](#reader-and-data-access) | Consente di visualizzare tutti gli elementi ma non consente di eliminare o creare un account di archiviazione o una risorsa contenuta. Consente anche l'accesso in lettura/scrittura a tutti i dati contenuti in un account di archiviazione tramite l'accesso alle chiavi dell'account di archiviazione. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Collaboratore account di archiviazione](#storage-account-contributor) | Consente di gestire gli account di archiviazione. Consente di accedere alla chiave dell'account, che può essere usata per accedere ai dati usando l'autorizzazione con chiave condivisa. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione](#storage-account-key-operator-service-role) | Consente di elencare e rigenerare le chiavi di accesso dell'account di archiviazione. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Collaboratore ai dati del BLOB di archiviazione](#storage-blob-data-contributor) | Consente di leggere, scrivere ed eliminare i contenitori e i BLOB di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Proprietario dei dati del BLOB di archiviazione](#storage-blob-data-owner) | Concede l'accesso completo ai contenitori e ai dati dei BLOB di Archiviazione di Azure, inclusa l'assegnazione del controllo di accesso POSIX. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Lettore dei dati del BLOB di archiviazione](#storage-blob-data-reader) | Consente di leggere ed elencare i contenitori e i BLOB di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Delegante di BLOB di archiviazione](#storage-blob-delegator) | Ottiene una chiave di delega utente, che può quindi essere usata per creare una firma di accesso condiviso per un contenitore o un BLOB firmato con credenziali di Azure AD. Per altre informazioni, vedere [Creare una firma di accesso condiviso di delega utente](/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Collaboratore per la condivisione SMB di dati per file di archiviazione](#storage-file-data-smb-share-contributor) | Consente l'accesso in lettura, scrittura ed eliminazione a file e directory nelle condivisioni file di Azure. Questo ruolo non ha un equivalente predefinito nei file server Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Collaboratore con privilegi elevati per la condivisione SMB di dati per file di archiviazione](#storage-file-data-smb-share-elevated-contributor) | Consente la lettura, scrittura, eliminazione e modifica degli ACL nei file e nelle directory delle condivisioni file di Azure. Questo ruolo è equivalente a un ACL di modifica della condivisione file nei file server Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Ruolo con autorizzazioni di lettura per la condivisione SMB di dati per file di archiviazione](#storage-file-data-smb-share-reader) | Consente l'accesso in lettura a file e directory nelle condivisioni file di Azure. Questo ruolo è equivalente a un ACL di lettura della condivisione file nei file server Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Collaboratore ai dati della coda di archiviazione](#storage-queue-data-contributor) | Lettura, scrittura ed eliminazione delle code e dei messaggi delle code di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Ruolo con autorizzazioni di elaborazione per i messaggi sui dati della coda di archiviazione](#storage-queue-data-message-processor) | Visualizzazione in anteprima, recupero ed eliminazione di un messaggio da una coda di Archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Mittente dei messaggi sui dati della coda di archiviazione](#storage-queue-data-message-sender) | Consente di aggiungere messaggi a una coda di Archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Ruolo con autorizzazioni di lettura per i dati della coda di archiviazione](#storage-queue-data-reader) | Consente di leggere ed elencare le code e i messaggi delle code di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Lettore di dati per Mappe di Azure](#azure-maps-data-reader) | Concede l'accesso per la lettura dei dati correlati alle mappe da un account Mappe di Azure. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Collaboratore servizi di ricerca](#search-service-contributor) | Consente di gestire i servizi di Ricerca, ma non di accedervi. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Lettore AccessKey SignalR](#signalr-accesskey-reader) | Leggere le chiavi di accesso al servizio SignalR | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [Server App SignalR (anteprima)](#signalr-app-server-preview) | Consente all'app server di accedere al servizio SignalR con le opzioni di autenticazione AAD. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [Collaboratore SignalR](#signalr-contributor) | Creare, leggere, aggiornare ed eliminare le risorse del servizio SignalR | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [Collaboratore senza server SignalR (anteprima)](#signalr-serverless-contributor-preview) | Consente al servizio app di accedere in modalità senza server con le opzioni di autenticazione AAD. | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [Proprietario del servizio SignalR (anteprima)](#signalr-service-owner-preview) | Accesso completo alle API REST del servizio Azure SignalR | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [Lettore del servizio SignalR (anteprima)](#signalr-service-reader-preview) | Accesso in sola lettura alle API REST del servizio Azure SignalR | ddde6b66-c0df-4114-A159-3618637b3035 |
> | [Collaboratore piani Web](#web-plan-contributor) | Consente di gestire i piani Web per i siti Web, ma non di accedervi. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Collaboratore siti Web](#website-contributor) | Consente di gestire i siti Web (non i piani Web), ma non di accedervi. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Contenitori** |  |  |
> | [AcrDelete](#acrdelete) | acr delete | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | firmatario immagine acr | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr pull | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | lettore di dati di quarantena acr | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | writer di dati di quarantena acr | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Ruolo di amministratore del cluster del servizio Azure Kubernetes](#azure-kubernetes-service-cluster-admin-role) | Elencare l'azione delle credenziali di amministratore del cluster. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Ruolo di utente del cluster del servizio Azure Kubernetes](#azure-kubernetes-service-cluster-user-role) | Elencare l'azione delle credenziali di utente del cluster. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Ruolo Collaboratore servizio di Azure Kubernetes](#azure-kubernetes-service-contributor-role) | Concede l'accesso per la lettura e la scrittura di cluster di servizi Kubernetes di Azure | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Amministratore RBAC del servizio Kubernetes di Azure](#azure-kubernetes-service-rbac-admin) | Consente di gestire tutte le risorse in cluster/spazio dei nomi, ad eccezione di Aggiorna o Elimina le quote e gli spazi dei nomi delle risorse. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Amministrazione del cluster RBAC del servizio Kubernetes di Azure](#azure-kubernetes-service-rbac-cluster-admin) | Consente di gestire tutte le risorse del cluster. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Lettore RBAC del servizio Kubernetes di Azure](#azure-kubernetes-service-rbac-reader) | Consente l'accesso in sola lettura per visualizzare la maggior parte degli oggetti in uno spazio dei nomi. Non consente la visualizzazione di ruoli o associazioni di ruolo. Questo ruolo non consente la visualizzazione dei segreti, perché la lettura del contenuto dei segreti consente l'accesso alle credenziali ServiceAccount nello spazio dei nomi, che consente l'accesso all'API come qualsiasi ServiceAccount nello spazio dei nomi (un tipo di escalation dei privilegi). L'applicazione di questo ruolo nell'ambito del cluster fornirà l'accesso a tutti gli spazi dei nomi. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Writer RBAC del servizio Kubernetes di Azure](#azure-kubernetes-service-rbac-writer) | Consente l'accesso in lettura/scrittura alla maggior parte degli oggetti in uno spazio dei nomi. Questo ruolo non consente la visualizzazione o la modifica di ruoli o associazioni di ruolo. Tuttavia, questo ruolo consente l'accesso ai segreti e l'esecuzione di pod come qualsiasi ServiceAccount nello spazio dei nomi, quindi può essere usato per ottenere i livelli di accesso all'API di qualsiasi ServiceAccount nello spazio dei nomi. L'applicazione di questo ruolo nell'ambito del cluster fornirà l'accesso a tutti gli spazi dei nomi. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Database** |  |  |
> | [Ruolo Lettore dell'account Cosmos DB](#cosmos-db-account-reader-role) | Può leggere i dati degli account Azure Cosmos DB. Vedere [Collaboratore account DocumentDB](#documentdb-account-contributor) per la gestione degli account Azure Cosmos DB. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Operatore di Cosmos DB](#cosmos-db-operator) | Consente di gestire gli account Azure Cosmos DB, ma non di accedere ai dati contenuti negli stessi. Impedisce l'accesso a chiavi dell'account e stringhe di connessione. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Può inviare una richiesta di ripristino per un database di Cosmos DB o un contenitore per un account | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Collaboratore account DocumentDB](#documentdb-account-contributor) | È in grado di gestire account Azure Cosmos DB. Azure Cosmos DB era precedentemente noto come DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Collaboratore cache Redis](#redis-cache-contributor) | Consente di gestire le cache Redis, ma non di accedervi. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Collaboratore database SQL](#sql-db-contributor) | Consente di gestire i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza o i rispettivi server SQL padre. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Collaboratore per Istanza gestita di SQL](#sql-managed-instance-contributor) | Consente di gestire le istanze gestite di SQL e la configurazione di rete necessaria, ma non consente l'accesso ad altri utenti. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Gestione della sicurezza SQL](#sql-security-manager) | Consente di gestire i criteri relativi alla sicurezza di server e database SQL, ma non di accedervi. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Collaboratore SQL Server](#sql-server-contributor) | Consente di gestire i server e i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analisi** |  |  |
> | [Proprietario dei dati di Hub eventi di Azure](#azure-event-hubs-data-owner) | Consente l'accesso completo alle risorse di Hub eventi di Azure. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Ricevitore dei dati di Hub eventi di Azure](#azure-event-hubs-data-receiver) | Consente l'accesso per la ricezione alle risorse dell'Hub eventi di Azure. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Mittente dei dati di Hub eventi di Azure](#azure-event-hubs-data-sender) | Consente l'accesso per l'invio alle risorse dell'Hub eventi di Azure. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Collaboratore Data Factory](#data-factory-contributor) | Consente di creare e gestire data factory, oltre alle risorse figlio in esse contenute. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Pulizia dati](#data-purger) | Può eliminare i dati di analisi | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Operatore di cluster HDInsight](#hdinsight-cluster-operator) | Consente di leggere e modificare le configurazioni dei cluster HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Collaboratore Servizi di dominio HDInsight](#hdinsight-domain-services-contributor) | Può leggere, creare, modificare ed eliminare operazioni correlate ai Servizi di dominio necessarie per HDInsight Enterprise Security Package | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Collaboratore di Log Analytics](#log-analytics-contributor) | Il ruolo Collaboratore di Log Analytics può leggere tutti i dati di monitoraggio e modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log dall'Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di soluzioni e la configurazione di Diagnostica di Azure in tutte le risorse di Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Lettore di Log Analytics](#log-analytics-reader) | Il ruolo Lettore di Log Analytics può visualizzare ed eseguire ricerche in tutti i dati di monitoraggio e può visualizzare le impostazioni di monitoraggio, inclusa la visualizzazione della configurazione di Diagnostica di Azure in tutte le risorse di Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Collaboratore del Registro schemi (anteprima)](#schema-registry-contributor-preview) | Lettura, scrittura ed eliminazione di gruppi e schemi del Registro schemi. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Lettore del Registro schemi (anteprima)](#schema-registry-reader-preview) | Lettura ed elenco di gruppi e schemi del Registro schemi. | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **Blockchain** |  |  |
> | [Accesso ai nodi di tipo membro della blockchain (anteprima)](#blockchain-member-node-access-preview) | Consente l'accesso ai nodi di tipo membro della blockchain | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Intelligenza artificiale e Machine Learning** |  |  |
> | [Collaboratore Servizi cognitivi](#cognitive-services-contributor) | Consente di creare, leggere, aggiornare, eliminare e gestire le chiavi di Servizi cognitivi. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Ruolo con autorizzazioni di lettura per i dati di Servizi cognitivi (anteprima)](#cognitive-services-data-reader-preview) | Consente di leggere i dati di Servizi cognitivi. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Utente Servizi cognitivi](#cognitive-services-user) | Consente di leggere ed elencare le chiavi di Servizi cognitivi. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Realtà mista** |  |  |
> | [Amministratore per il rendering remoto](#remote-rendering-administrator) | Fornisce agli utenti funzionalità di conversione, gestione delle sessioni, rendering e diagnostica per il rendering remoto di Azure | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Client di rendering remoto](#remote-rendering-client) | Consente agli utenti di gestire le funzionalità di sessione, rendering e diagnostica per il rendering remoto di Azure. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Collaboratore per l'account per gli ancoraggi spaziali](#spatial-anchors-account-contributor) | Consente di gestire gli ancoraggi nello spazio nell'account, ma non di eliminarli | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Proprietario dell'account per gli ancoraggi spaziali](#spatial-anchors-account-owner) | Consente di gestire gli ancoraggi nello spazio nell'account, incluse le operazioni di eliminazione | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Ruolo Lettore dell'account per gli ancoraggi spaziali](#spatial-anchors-account-reader) | Consente di individuare e leggere le proprietà degli ancoraggi nello spazio nell'account | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integrazione** |  |  |
> | [Collaboratore servizio Gestione API](#api-management-service-contributor) | Può gestire il servizio e le API. | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Ruolo operatore del servizio Gestione API](#api-management-service-operator-role) | Può gestire il servizio ma non le API. | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Ruolo lettura del servizio Gestione API](#api-management-service-reader-role) | Consente l'accesso di sola lettura al servizio e alle API. | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Proprietario dei dati di Configurazione dell'app](#app-configuration-data-owner) | Consente l'accesso completo ai dati di Configurazione dell'app. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Ruolo con autorizzazioni di lettura per i dati di Configurazione dell'app](#app-configuration-data-reader) | Consente l'accesso in lettura ai dati di Configurazione dell'app. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Proprietario dei dati del bus di servizio di Azure](#azure-service-bus-data-owner) | Consente l'accesso completo alle risorse del bus di servizio di Azure. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Ricevitore dei dati del bus di servizio di Azure](#azure-service-bus-data-receiver) | Consente l'accesso per la ricezione alle risorse del bus di servizio di Azure. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Mittente dei dati del bus di servizio di Azure](#azure-service-bus-data-sender) | Consente l'accesso per l'invio alle risorse del bus di servizio di Azure. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Proprietario della registrazione di Azure Stack](#azure-stack-registration-owner) | Consente di gestire le registrazioni di Azure Stack. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Collaboratore per sottoscrizioni di eventi di Griglia di eventi](#eventgrid-eventsubscription-contributor) | Consente di gestire le operazioni di sottoscrizione di eventi EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [Ruolo con autorizzazioni di lettura per sottoscrizioni di eventi di Griglia di eventi](#eventgrid-eventsubscription-reader) | Consente di leggere le sottoscrizioni di eventi EventGrid. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Collaboratore dati FHIR](#fhir-data-contributor) | Il ruolo consente all'utente o all'entità di accesso completo ai dati di FHIR | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [Esportatore dati FHIR](#fhir-data-exporter) | Role consente all'utente o all'entità di leggere ed esportare i dati di FHIR | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [Lettore dati FHIR](#fhir-data-reader) | Il ruolo consente all'utente o all'entità di leggere i dati di FHIR | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [FHIR Data writer](#fhir-data-writer) | Role consente a un utente o a un'entità di leggere e scrivere dati FHIR | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [Collaboratore ambiente del servizio di integrazione](#integration-service-environment-contributor) | Consente di gestire gli ambienti del servizio di integrazione, ma non di accedervi. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [Sviluppatore ambiente del servizio di integrazione](#integration-service-environment-developer) | Consente agli sviluppatori di creare e aggiornare i flussi di lavoro, gli account di integrazione e le connessioni API negli ambienti di Integration Services. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Collaboratore account Intelligent Systems](#intelligent-systems-account-contributor) | Consente di gestire gli account Sistemi intelligenti, ma non di accedervi. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Collaboratore per app per la logica](#logic-app-contributor) | Consente di gestire le app per la logica, ma non di modificarne l'accesso. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Operatore per app per la logica](#logic-app-operator) | Consente di leggere, abilitare e disabilitare le app per la logica, ma non di modificarle o aggiornarle. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identità** |  |  |
> | [Managed Identity Contributor](#managed-identity-contributor) (Collaboratore per identità gestita) | Crea, legge, aggiorna ed elimina l'identità assegnata all'utente | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Managed Identity Operator](#managed-identity-operator) (Operatore per identità gestita) | Legge e assegna l'identità assegnata all'utente | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Sicurezza** |  |  |
> | [Collaboratore di Azure Sentinel](#azure-sentinel-contributor) | Collaboratore di Azure Sentinel | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Ruolo con autorizzazioni di lettura di Azure Sentinel](#azure-sentinel-reader) | Ruolo con autorizzazioni di lettura di Azure Sentinel | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Risponditore di Azure Sentinel](#azure-sentinel-responder) | Risponditore di Azure Sentinel | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Amministratore Key Vault (anteprima)](#key-vault-administrator-preview) | Eseguire tutte le operazioni del piano dati in un insieme di credenziali delle chiavi e in tutti gli oggetti al suo interno, inclusi i certificati, le chiavi e i segreti. Non è possibile gestire le risorse di Key Vault o gestire le assegnazioni di ruolo. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Ufficiale certificati Key Vault (anteprima)](#key-vault-certificates-officer-preview) | Eseguire qualsiasi azione sui certificati di un insieme di credenziali delle chiavi, eccetto le autorizzazioni Manage. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Collaboratore di Key Vault](#key-vault-contributor) | Consente di gestire gli insiemi di credenziali delle chiavi, ma non di assegnare i ruoli in Azure RBAC e di accedere a segreti, chiavi o certificati. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault Crypto Officer (anteprima)](#key-vault-crypto-officer-preview) | Eseguire qualsiasi azione sulle chiavi di un insieme di credenziali delle chiavi, eccetto le autorizzazioni Manage. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Crittografia del servizio di crittografia Key Vault (anteprima)](#key-vault-crypto-service-encryption-preview) | Legge i metadati delle chiavi ed esegue operazioni di wrapping/Unwrap. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Utente di crittografia Key Vault (anteprima)](#key-vault-crypto-user-preview) | Eseguire operazioni di crittografia usando chiavi. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Lettore di Key Vault (anteprima)](#key-vault-reader-preview) | Leggere i metadati di insiemi di credenziali delle chiavi e i relativi certificati, chiavi e segreti. Non è possibile leggere i valori sensibili come il contenuto del segreto o il materiale della chiave. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Amministratore di Key Vault Secrets (anteprima)](#key-vault-secrets-officer-preview) | Eseguire qualsiasi azione sui segreti di un insieme di credenziali delle chiavi, eccetto le autorizzazioni di gestione. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | b86a8fe4-44CE-4948-aee5-eccb2c155cd7 |
> | [Utente di Key Vault Secrets (anteprima)](#key-vault-secrets-user-preview) | Leggere il contenuto del segreto. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [Amministrazione della protezione](#security-admin) | Visualizzazione e aggiornamento delle autorizzazioni per il Centro sicurezza. Ha le stesse autorizzazioni del Ruolo con autorizzazioni di lettura per la sicurezza e può anche aggiornare i criteri di sicurezza e rimuovere gli avvisi e le raccomandazioni. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Collaboratore per valutazioni della sicurezza](#security-assessment-contributor) | Consente di eseguire il push delle valutazioni nel Centro sicurezza | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Gestore sicurezza (legacy)](#security-manager-legacy) | Questo è un ruolo legacy. Usare invece Amministratore della sicurezza. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Ruolo con autorizzazioni di lettura per la sicurezza](#security-reader) | Visualizzazione delle autorizzazioni per il Centro sicurezza. Può visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non può apportare modifiche. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [Utente DevTest Labs](#devtest-labs-user) | Consente di connettere, avviare, riavviare e arrestare le macchine virtuali in Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Lab Creator](#lab-creator) (Creatore di lab) | Consente di creare nuovi Lab con gli account di Azure Lab. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Monitoraggio** |  |  |
> | [Collaboratore componente di Application Insights](#application-insights-component-contributor) | È in grado di gestire i componenti di Application Insights | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Debugger di snapshot di Application Insights](#application-insights-snapshot-debugger) | Concede l'autorizzazione utente per visualizzare e scaricare gli snapshot di debug raccolti con Application Insights Snapshot Debugger. Si noti che queste autorizzazioni non sono incluse nei ruoli [Proprietario](#owner) e [Collaboratore](#contributor). Quando si assegna agli utenti il ruolo Snapshot Debugger di Application Insights, è necessario concedere il ruolo direttamente all'utente. Il ruolo non viene riconosciuto quando viene aggiunto a un ruolo personalizzato. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Collaboratore al monitoraggio](#monitoring-contributor) | Può leggere tutti i dati del monitoraggio e modificare le impostazioni di monitoraggio. Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Autore delle metriche di monitoraggio](#monitoring-metrics-publisher) | Abilitare la pubblicazione di metriche nelle risorse di Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Lettore di monitoraggio](#monitoring-reader) | Può leggere tutti i dati del monitoraggio (metriche, log e così via). Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Collaboratore per le cartelle di lavoro](#workbook-contributor) | Può salvare le cartelle di lavoro condivise. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Ruolo con autorizzazioni di lettura per le cartelle di lavoro](#workbook-reader) | Può leggere le cartelle di lavoro. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Gestione e governance** |  |  |
> | [Operatore processo di automazione](#automation-job-operator) | Consente di creare e gestire i processi tramite i runbook di Automazione. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Operatore di automazione](#automation-operator) | Gli operatori di automazione possono avviare, arrestare, sospendere e riprendere processi. | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Operatore runbook di automazione](#automation-runbook-operator) | Consente di leggere le proprietà del runbook per permettere di creare processi del runbook. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Onboarding di Azure Connected Machine](#azure-connected-machine-onboarding) | Può eseguire l'onboarding di Azure Connected Machine. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Amministratore delle risorse di Azure Connected Machine](#azure-connected-machine-resource-administrator) | Può leggere, scrivere, eliminare e ripetere l'onboarding di Azure Connected Machine. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Lettore per la fatturazione](#billing-reader) | Consente l'accesso in lettura ai dati di fatturazione. | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Collaboratore di progetto](#blueprint-contributor) | Può gestire le definizioni di progetto, ma non assegnarle. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Operatore di progetto](#blueprint-operator) | Può assegnare i progetti pubblicati esistenti, ma non creare nuovi progetti. Si noti che funziona solo se l'assegnazione viene eseguita con un'identità gestita assegnata dall'utente. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Collaboratore Gestione costi](#cost-management-contributor) | Può visualizzare i costi e gestire la configurazione dei costi, ad esempio budget ed esportazioni | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Lettore Gestione costi](#cost-management-reader) | Può visualizzare la configurazione e i dati dei costi, ad esempio budget ed esportazioni | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Amministratore impostazioni gerarchia](#hierarchy-settings-administrator) | Consente agli utenti di modificare ed eliminare le impostazioni della gerarchia | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Cluster Kubernetes-onboarding di Azure Arc](#kubernetes-cluster---azure-arc-onboarding) | Definizione del ruolo per autorizzare qualsiasi utente/servizio a creare una risorsa connectedClusters | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Ruolo collaboratore per applicazioni gestite](#managed-application-contributor-role) | Consente la creazione di risorse di applicazioni gestite. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Ruolo di Operatore applicazione gestita](#managed-application-operator-role) | Consente di leggere ed eseguire azioni sulle risorse dell'applicazione gestita | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Lettore applicazioni gestite](#managed-applications-reader) | Consente di leggere le risorse in un accesso di app gestita e JIT richiesta. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Ruolo con autorizzazioni di eliminazione assegnazioni di registrazione dei servizi gestiti](#managed-services-registration-assignment-delete-role) | Il Ruolo con autorizzazioni di eliminazione assegnazioni di registrazione dei servizi gestiti consente agli utenti del tenant di gestione di eliminare l'assegnazione della registrazione assegnata al proprio tenant. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Collaboratore gruppo di gestione](#management-group-contributor) | Ruolo Collaboratore gruppo di gestione | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Lettore gruppo di gestione](#management-group-reader) | Ruolo Lettore gruppo di gestione | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Collaboratore account New Relic APM](#new-relic-apm-account-contributor) | Consente di gestire gli account e le applicazioni di APR New Relic, ma non di accedervi. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Ruolo con autorizzazioni di scrittura per i dati di Policy Insights (anteprima)](#policy-insights-data-writer-preview) | Consente l'accesso in lettura ai criteri delle risorse e l'accesso in scrittura agli eventi dei criteri dei componenti delle risorse. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Collaboratore per i criteri delle risorse](#resource-policy-contributor) | Utenti con diritti di creazione/modifica di criteri delle risorse, creazione di ticket di supporto e lettura di risorse/gerarchia. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Collaboratore al ripristino sito](#site-recovery-contributor) | Consente di gestire il servizio Site Recovery ad eccezione della creazione dell'insieme di credenziali e dell'assegnazione di ruolo. | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Operatore del ripristino sito](#site-recovery-operator) | Consente di eseguire il failover e il failback ma non di eseguire altre operazioni di gestione di Site Recovery. | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Reader di ripristino sito](#site-recovery-reader) | Consente di visualizzare lo stato di Site Recovery ma non di eseguire altre operazioni di gestione. | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Collaboratore alla richiesta di supporto](#support-request-contributor) | Consente di creare e gestire le richieste di supporto. | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Collaboratore per tag](#tag-contributor) | Consente di gestire i tag sulle entità senza concedere l'accesso alle entità stesse. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Altri** |  |  |
> | [Proprietario di dati dei dispositivi gemelli digitali di Azure](#azure-digital-twins-data-owner) | Ruolo accesso completo per il piano dati dei dispositivi gemelli digitali | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Lettore dati di dispositivi gemelli digitali di Azure](#azure-digital-twins-data-reader) | Ruolo di sola lettura per le proprietà del piano dati gemelli digitali | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [Collaboratore BizTalk](#biztalk-contributor) | Consente di gestire i servizi BizTalk, ma non di accedervi. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Utente di virtualizzazione desktop](#desktop-virtualization-user) | Consente all'utente di usare le applicazioni in un gruppo di applicazioni. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Collaboratore raccolte di processi dell'unità di pianificazione](#scheduler-job-collections-contributor) | Consente di gestire le raccolte di processi dell'utilità di pianificazione, ma non di accedervi. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>Generale


### <a name="contributor"></a>Collaboratore

Concede l'accesso completo per la gestione di tutte le risorse, ma non consente di assegnare i ruoli in RBAC di Azure. [Scopri di più](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | * | È in grado di creare e gestire ogni tipo di risorsa |
> | **NotActions** |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | Eliminazione di ruoli, assegnazioni di criteri, definizioni dei criteri e definizioni dei set di criteri |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | Creazione di ruoli, assegnazioni di ruoli, assegnazioni di criteri, definizioni dei criteri e definizioni dei set di criteri |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | Concede al chiamante l'accesso di tipo Amministratore Accesso utenti a livello dell'ambito del tenant |
> | [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/Write | Crea o aggiorna eventuali assegnazioni del progetto |
> | [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/Delete | Elimina eventuali assegnazioni del progetto |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC.",
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

Concede l'accesso completo per la gestione di tutte le risorse, inclusa la possibilità di assegnare ruoli in controllo degli accessi in base al ruolo [Scopri di più](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
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
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
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

Consente di visualizzare tutte le risorse, ma non di apportare modifiche. [Scopri di più](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
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
  "description": "View all resources, but does not allow you to make any changes.",
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

Consente di gestire gli accessi utente alle risorse di Azure. [Scopri di più](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/* | Gestire l'autorizzazione |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

## <a name="compute"></a>Calcolo


### <a name="classic-virtual-machine-contributor"></a>Collaboratore macchine virtuali classiche

Consente di gestire le macchine virtuali classiche, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/DomainNames/* | Creare e gestire nomi di dominio di calcolo classici |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Creare e gestire macchine virtuali |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/Action |  |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/Link/Action | Collega un IP riservato |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/Read | Ottiene gli IP riservati |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/Action | Unisce la rete virtuale. |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/Read | Ottiene la rete virtuale. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/Read | Restituisce il disco dell'account di archiviazione. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/Read | Restituisce l'immagine dell'account di archiviazione. Operazione deprecata: usare 'Microsoft.ClassicStorage/storageAccounts/vmImages'. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/Read | Restituisce l'account di archiviazione con l'account specificato. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
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

Visualizzare le macchine virtuali nel portale e accedere come amministratore [altre informazioni](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Ottiene la definizione della rete virtuale |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/Read | Ottiene una definizione del servizio di bilanciamento del carico |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Ottiene una definizione dell’interfaccia di rete.  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/Action | Consente di accedere a una macchina virtuale come utente normale |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/Action | Consente di accedere a una macchina virtuale con privilegi di amministratore di Windows o di utente root di Linux |
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

Consente di gestire le macchine virtuali, ma non di accedervi né di gestire la rete virtuale o l'account di archiviazione a cui sono connesse. [Scopri di più](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Creare e gestire set di disponibilità di calcolo |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/locations/* | Creare e gestire percorsi di calcolo |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Eseguire tutte le azioni della macchina virtuale, tra cui creare, aggiornare, eliminare, avviare, riavviare e spegnere macchine virtuali. Eseguire script predefiniti nelle macchine virtuali. |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Creare e gestire i set di scalabilità delle macchine virtuali |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/disks/Write | Crea un nuovo disco o ne aggiorna uno esistente |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/disks/Read | Ottiene le proprietà di un disco |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/disks/Delete | Elimina il disco |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/schedules/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/Action | Aggiunge un pool di indirizzi back-end del gateway applicazione. Senza avvisi. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/Action | Aggiunge un pool di indirizzi di back-end del servizio di bilanciamento del carico. Senza avvisi. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/Action | Aggiunge un pool NAT in entrata del servizio di bilanciamento del carico. Senza avvisi. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/Action | Aggiunge una regola NAT in entrata del servizio di bilanciamento del carico. Senza avvisi. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/Probes/join/Action | Consente l'uso di probe di un servizio di bilanciamento del carico. Con questa autorizzazione, ad esempio, la proprietà healthProbe di un set di scalabilità di macchine virtuali può fare riferimento al probe. Senza avvisi. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/Read | Ottiene una definizione del servizio di bilanciamento del carico |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/locations/* | Creare e gestire percorsi di rete |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | Creare e gestire interfacce di rete |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/Action | Aggiunge un gruppo di sicurezza di rete. Senza avvisi. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/Read | Ottiene una definizione del gruppo di sicurezza di rete |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/Action | Aggiunge un indirizzo IP pubblico. Senza avvisi. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Ottiene la definizione della rete virtuale |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/join/Action | Aggiunge una rete virtuale. Senza avvisi. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Write | Crea un programma di protezione del backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Restituisce i dettagli dell'oggetto dell'elemento protetto |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Crea un elemento protetto di backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Restituisce tutti i criteri di protezione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Write | Crea i criteri di protezione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Write | L'operazione Crea insieme di credenziali crea una risorsa di Azure di tipo 'vault' |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Consente di visualizzare le macchine virtuali nel portale e di accedere come utente normale. [Scopri di più](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Ottiene la definizione della rete virtuale |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/Read | Ottiene una definizione del servizio di bilanciamento del carico |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Ottiene una definizione dell’interfaccia di rete.  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/Action | Consente di accedere a una macchina virtuale come utente normale |
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

## <a name="networking"></a>Rete


### <a name="cdn-endpoint-contributor"></a>Collaboratore endpoint rete CDN

Può gestire gli endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/Endpoints/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
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
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/Endpoints/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
  "description": "Can view CDN endpoints, but can't make changes.",
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

Può gestire i profili e i rispettivi endpoint della rete CDN, ma non può concedere l'accesso ad altri utenti. [Scopri di più](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
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
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
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

Consente di gestire le reti classiche, ma non di accedervi. [Scopri di più](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Creare e gestire reti classiche |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Consente di gestire le zone DNS e i set di record in DNS di Azure, ma non di controllare chi è autorizzato ad accedervi. [Scopri di più](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | Creazione e gestione di zone e record DNS |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/* | Creare e gestire reti |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

### <a name="private-dns-zone-contributor"></a>Collaboratore zona DNS privato

Consente di gestire le risorse della zona DNS privata, ma non le reti virtuali a cui sono collegate. [Scopri di più](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Ottiene la definizione della rete virtuale |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/Action | Aggiunge una rete virtuale. Senza avvisi. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
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
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Collaboratore Gestione traffico

Consente di gestire i profili di Gestione traffico, ma non di controllare chi è autorizzato ad accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

## <a name="storage"></a>Archiviazione


### <a name="avere-contributor"></a>Collaboratore per Avere

Può creare e gestire un cluster Avere vFXT. [Scopri di più](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/*/Read |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/Disks/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/*/Read |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Ottiene la definizione della rete virtuale |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/Read | Ottiene una definizione di subnet della rete virtuale |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/join/Action | Aggiunge una rete virtuale. Senza avvisi. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Senza avvisi. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/Action | Aggiunge un gruppo di sicurezza di rete. Senza avvisi. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/*/Read |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Creare e gestire account di archiviazione |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Resources/Read | Ottiene le risorse del gruppo di risorse. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Delete | Restituisce il risultato dell'eliminazione di un BLOB |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Read | Restituisce un BLOB o un elenco di BLOB |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Write | Restituisce il risultato della scrittura su un BLOB |
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
        "Microsoft.Compute/proximityPlacementGroups/*",
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

### <a name="avere-operator"></a>Operatore di Avere

Usato dal cluster vFXT per gestire il cluster [ulteriori informazioni](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/Read | Ottiene le proprietà di una macchina virtuale |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Ottiene una definizione dell’interfaccia di rete.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Write | Crea un'interfaccia di rete o ne aggiorna una esistente.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Ottiene la definizione della rete virtuale |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/Read | Ottiene una definizione di subnet della rete virtuale |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/join/Action | Aggiunge una rete virtuale. Senza avvisi. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/Action | Aggiunge un gruppo di sicurezza di rete. Senza avvisi. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Delete | Restituisce il risultato dell'eliminazione di un contenitore |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Read | Restituisce l'elenco dei contenitori |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Write | Restituisce il risultato dell'operazione PUT sul contenitore BLOB |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Delete | Restituisce il risultato dell'eliminazione di un BLOB |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Read | Restituisce un BLOB o un elenco di BLOB |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Write | Restituisce il risultato della scrittura su un BLOB |
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

Consente di gestire il servizio di backup, ma non di creare insiemi di credenziali e concedere l'accesso ad altri utenti altre [informazioni](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Ottiene la definizione della rete virtuale |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | Consente di gestire i risultati dell'operazione sulla gestione del backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Consente di creare e gestire i contenitori di backup all'interno delle infrastrutture di backup dell'insieme di credenziali dei Servizi di ripristino |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/Action | Aggiorna l'elenco di contenitori |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Consente di creare e gestire i processi di backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Esporta processi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Consente di creare e gestire i risultati delle operazioni di gestione di backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | Consente di creare e gestire i criteri di backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Consente di creare e gestire gli elementi su cui è possibile eseguire il backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | Consente di creare e gestire gli elementi su cui è stato eseguito il backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | Consente di creare e gestire i contenitori che contengono gli elementi di backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/* | Consente di creare e gestire i certificati relativi al backup nell'insieme di credenziali dei Servizi di ripristino |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Consente di creare e gestire informazioni estese relative all'insieme di credenziali |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Consente di creare e gestire le identità registrate |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Consente di creare e gestire l'uso dell'insieme di credenziali dei Servizi di ripristino |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupConfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/Action | Convalida l'operazione sull'elemento protetto |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Write | L'operazione Crea insieme di credenziali crea una risorsa di Azure di tipo 'vault' |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Restituisce lo stato dell'operazione di backup dell'insieme di credenziali di Servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Restituisce tutti i server di gestione di backup registrati nell'insieme di credenziali. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/Read | Ottiene tutti i contenitori che si possono proteggere |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/Action | Controlla lo stato del backup dell'insieme di credenziali di Servizi di ripristino |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/Action |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/Action | Convalida le funzioni |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Risolve l'avviso. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | L'operazione restituisce l'elenco delle operazioni per un provider di risorse |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/OperationStatus/Read | Ottiene lo stato dell'operazione per una determinata operazione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Elenca tutte le finalità di protezione del backup |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Consente di gestire i servizi di backup, eccetto la rimozione del backup, la creazione dell'insieme di credenziali e l'accesso ad altri utenti altre [informazioni](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Ottiene la definizione della rete virtuale |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/Read | Restituisce lo stato dell'operazione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/Read | Ottiene il risultato dell'operazione eseguita sul contenitore di protezione. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/Action | Esegue il backup dell'elemento protetto. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Ottiene il risultato dell'operazione eseguita sugli elementi protetti. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Restituisce lo stato dell'operazione eseguita sugli elementi protetti. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Restituisce i dettagli dell'oggetto dell'elemento protetto |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Effettua il provisioning del ripristino elementi immediato per l'elemento protetto |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Ottiene i punti di ripristino degli elementi protetti. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Restore/Action | Ripristina i punti di ripristino degli elementi protetti. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Revoca il ripristino elementi immediato per l'elemento protetto |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Crea un elemento protetto di backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Read | Restituisce tutti i contenitori registrati |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/Action | Aggiorna l'elenco di contenitori |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Consente di creare e gestire i processi di backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Esporta processi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Consente di creare e gestire i risultati delle operazioni di gestione di backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/Read | Ottiene i risultati dell'operazione sui criteri. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Restituisce tutti i criteri di protezione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Consente di creare e gestire gli elementi su cui è possibile eseguire il backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/Read | Restituisce l'elenco di tutti gli elementi protetti. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/Read | Restituisce tutti i contenitori che appartengono alla sottoscrizione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/Write | L'operazione Aggiorna certificato risorsa aggiorna il certificato delle credenziali della risorsa o dell'insieme di credenziali. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Write | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Write | L'operazione Registra contenitore di servizi può essere usata per registrare un contenitore con il servizio di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/Action | Convalida l'operazione sull'elemento protetto |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Restituisce lo stato dell'operazione di backup dell'insieme di credenziali di Servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Operations/Read | Ottiene lo stato dell'operazione sui criteri. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Write | Crea un contenitore registrato |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/inquire/Action | Esegue operazioni di richiesta di informazioni per i carichi di lavoro all'interno di un contenitore |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Restituisce tutti i server di gestione di backup registrati nell'insieme di credenziali. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Write | Crea un programma di protezione del backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Read | Ottiene una finalità di protezione del backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/Read | Ottiene tutti i contenitori che si possono proteggere |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Items/Read | Ottiene tutti gli elementi in un contenitore |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/Action | Controlla lo stato del backup dell'insieme di credenziali di Servizi di ripristino |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/Action |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/Action | Convalida le funzioni |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Risolve l'avviso. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | L'operazione restituisce l'elenco delle operazioni per un provider di risorse |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/OperationStatus/Read | Ottiene lo stato dell'operazione per una determinata operazione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Elenca tutte le finalità di protezione del backup |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Consente di visualizzare i servizi di backup, ma non di apportare [ulteriori](../backup/backup-rbac-rs-vault.md) modifiche

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/Read | GetAllocatedStamp è un'operazione interna usata dal servizio |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/Read | Restituisce lo stato dell'operazione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/Read | Ottiene il risultato dell'operazione eseguita sul contenitore di protezione. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Ottiene il risultato dell'operazione eseguita sugli elementi protetti. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Restituisce lo stato dell'operazione eseguita sugli elementi protetti. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Restituisce i dettagli dell'oggetto dell'elemento protetto |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Ottiene i punti di ripristino degli elementi protetti. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Read | Restituisce tutti i contenitori registrati |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/Read | Restituisce il risultato dell'operazione di processo. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/Read | Restituisce tutti gli oggetti processo |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Esporta processi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/Read | Restituisce il risultato dell'operazione di backup di un insieme di credenziali di Servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/Read | Ottiene i risultati dell'operazione sui criteri. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Restituisce tutti i criteri di protezione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/Read | Restituisce l'elenco di tutti gli elementi protetti. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/Read | Restituisce tutti i contenitori che appartengono alla sottoscrizione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Restituisce i riepiloghi per gli elementi protetti e i server protetti di un'istanza di Servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/Read | Restituisce la configurazione di archiviazione dell'insieme di credenziali di Servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupConfig/Read | Restituisce la configurazione dell'insieme di credenziali di Servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Restituisce lo stato dell'operazione di backup dell'insieme di credenziali di Servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Operations/Read | Ottiene lo stato dell'operazione sui criteri. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Restituisce tutti i server di gestione di backup registrati nell'insieme di credenziali. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Read | Ottiene una finalità di protezione del backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Items/Read | Ottiene tutti gli elementi in un contenitore |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/Action | Controlla lo stato del backup dell'insieme di credenziali di Servizi di ripristino |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Risolve l'avviso. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | L'operazione restituisce l'elenco delle operazioni per un provider di risorse |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/OperationStatus/Read | Ottiene lo stato dell'operazione per una determinata operazione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Elenca tutte le finalità di protezione del backup |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/Action | Convalida le funzioni |
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
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
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
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Creare e gestire account di archiviazione |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Gli operatori di chiave dell'account di archiviazione classico sono autorizzati a elencare e rigenerare le chiavi negli account di archiviazione classici [altre informazioni](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/Action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/RegenerateKey/Action | Rigenera le chiavi di accesso esistenti per l'account di archiviazione. |
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

Consente di gestire tutto il servizio Data Box, ad eccezione della concessione dell'accesso ad altri utenti. [Scopri di più](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/* |  |
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

Consente di gestire il servizio Data Box, ad eccezione della creazione di ordini, della modifica dei dettagli dell'ordine e della concessione dell'accesso ad altri utenti. [Scopri di più](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/*/Read |  |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/Jobs/listsecrets/Action |  |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/Jobs/listCredentials/Action | Elenca le credenziali non crittografate correlate all'ordine. |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/Action | Restituisce l'elenco degli SKU disponibili. |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/Action | Questo metodo esegue tutti i tipi di convalida. |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/Action | Questo metodo restituisce le configurazioni per l'area. |
> | [Microsoft. databox](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/Action | Convalida l'indirizzo di spedizione e fornisce indirizzi alternativi, se disponibili. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Consente di inviare, monitorare e gestire i propri processi, ma non di creare o eliminare account Data Lake Analytics. [Scopri di più](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [Microsoft. analisi data Lake](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [Microsoft. analisi data Lake](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | Elimina un account Analisi Data Lake. |
> | [Microsoft. analisi data Lake](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/Action | Concede le autorizzazioni per annullare i processi inviati da altri utenti. |
> | [Microsoft. analisi data Lake](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | Crea o aggiorna un account Analisi Data Lake. |
> | [Microsoft. analisi data Lake](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | Crea o aggiorna un account Archivio Data Lake collegato a un account Analisi Data Lake. |
> | [Microsoft. analisi data Lake](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | Scollega un account Archivio Data Lake da un account Analisi Data Lake. |
> | [Microsoft. analisi data Lake](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | Crea o aggiorna un account di archiviazione collegato a un account Analisi Data Lake. |
> | [Microsoft. analisi data Lake](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | Scollega un account di archiviazione da un account Analisi Data Lake. |
> | [Microsoft. analisi data Lake](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | Crea o aggiorna una regola del firewall. |
> | [Microsoft. analisi data Lake](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | Elimina una regola del firewall. |
> | [Microsoft. analisi data Lake](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | Crea o aggiorna i criteri di calcolo. |
> | [Microsoft. analisi data Lake](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | Elimina criteri di calcolo. |
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
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/Action | Restituisce il token SAS dell’account per l’account di archiviazione specificato. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
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

Consente di gestire gli account di archiviazione. Consente di accedere alla chiave dell'account, che può essere usata per accedere ai dati usando l'autorizzazione con chiave condivisa. [Scopri di più](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Senza avvisi. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Creare e gestire account di archiviazione |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Consente di elencare e rigenerare le chiavi di accesso dell'account di archiviazione. [Scopri di più](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/Action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/RegenerateKey/Action | Rigenera le chiavi di accesso per l'account di archiviazione specificato. |
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

### <a name="storage-blob-data-contributor"></a>Collaboratore ai dati del BLOB di archiviazione

Consente di leggere, scrivere ed eliminare i contenitori e i BLOB di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Scopri di più](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Delete | Elimina un contenitore. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Read | Restituisce un contenitore o un elenco di contenitori. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Write | Modifica i metadati o le proprietà di un contenitore. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Restituisce una chiave di delega utente per il servizio BLOB. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Delete | Eliminare un BLOB. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Read | Restituisce un BLOB o un elenco di BLOB. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Move/Action | Sposta il BLOB da un percorso a un altro |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Write | Consente di scrivere in un BLOB. |
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
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
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

### <a name="storage-blob-data-owner"></a>Proprietario dei dati del BLOB di archiviazione

Concede l'accesso completo ai contenitori e ai dati dei BLOB di Archiviazione di Azure, inclusa l'assegnazione del controllo di accesso POSIX. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Scopri di più](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/* | Autorizzazioni complete per i contenitori. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Restituisce una chiave di delega utente per il servizio BLOB. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/* | Autorizzazioni complete per i BLOB. |
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

### <a name="storage-blob-data-reader"></a>Lettore dei dati del BLOB di archiviazione

Consente di leggere ed elencare i contenitori e i BLOB di archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Scopri di più](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Read | Restituisce un contenitore o un elenco di contenitori. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Restituisce una chiave di delega utente per il servizio BLOB. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Read | Restituisce un BLOB o un elenco di BLOB. |
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

### <a name="storage-blob-delegator"></a>Delegante di BLOB di archiviazione

Ottiene una chiave di delega utente, che può quindi essere usata per creare una firma di accesso condiviso per un contenitore o un BLOB firmato con credenziali di Azure AD. Per altre informazioni, vedere [Creare una firma di accesso condiviso di delega utente](/rest/api/storageservices/create-user-delegation-sas). [Scopri di più](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Restituisce una chiave di delega utente per il servizio BLOB. |
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

### <a name="storage-file-data-smb-share-contributor"></a>Collaboratore per la condivisione SMB di dati per file di archiviazione

Consente l'accesso in lettura, scrittura ed eliminazione a file e directory nelle condivisioni file di Azure. Questo ruolo non ha un equivalente predefinito nei file server Windows. [Scopri di più](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/Read | Restituisce un file, una cartella o un elenco di file/cartelle. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Write | Restituisce il risultato della scrittura di un file o della creazione di una cartella. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/Delete | Restituisce il risultato dell'eliminazione di un file o una cartella. |
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

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Collaboratore con privilegi elevati per la condivisione SMB di dati per file di archiviazione

Consente la lettura, scrittura, eliminazione e modifica degli ACL nei file e nelle directory delle condivisioni file di Azure. Questo ruolo è equivalente a un ACL di modifica della condivisione file nei file server Windows. [Scopri di più](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/Read | Restituisce un file, una cartella o un elenco di file/cartelle. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Write | Restituisce il risultato della scrittura di un file o della creazione di una cartella. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/Delete | Restituisce il risultato dell'eliminazione di un file o una cartella. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/ModifyPermissions/Action | Restituisce il risultato della modifica delle autorizzazioni per un file o una cartella. |
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

### <a name="storage-file-data-smb-share-reader"></a>Ruolo con autorizzazioni di lettura per la condivisione SMB di dati per file di archiviazione

Consente l'accesso in lettura a file e directory nelle condivisioni file di Azure. Questo ruolo è equivalente a un ACL di lettura della condivisione file nei file server Windows. [Scopri di più](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/Read | Restituisce un file, una cartella o un elenco di file/cartelle. |
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

### <a name="storage-queue-data-contributor"></a>Collaboratore ai dati della coda di archiviazione

Lettura, scrittura ed eliminazione delle code e dei messaggi delle code di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Scopri di più](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Delete | Elimina una coda. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Read | Restituisce una coda o un elenco di code. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Write | Modifica le proprietà o i metadati della coda. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Delete | Elimina uno o più messaggi da una coda. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Read | Visualizza in anteprima o recupera uno o più messaggi da una coda. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Write | Aggiunge un messaggio a una coda. |
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

### <a name="storage-queue-data-message-processor"></a>Ruolo con autorizzazioni di elaborazione per i messaggi sui dati della coda di archiviazione

Visualizzazione in anteprima, recupero ed eliminazione di un messaggio da una coda di Archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Scopri di più](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Read | Visualizza in anteprima un messaggio. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Process/Action | Recupera ed elimina un messaggio. |
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

### <a name="storage-queue-data-message-sender"></a>Mittente dei messaggi sui dati della coda di archiviazione

Consente di aggiungere messaggi a una coda di Archiviazione di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Scopri di più](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Add/Action | Aggiunge un messaggio a una coda. |
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

### <a name="storage-queue-data-reader"></a>Ruolo con autorizzazioni di lettura per i dati della coda di archiviazione

Consente di leggere ed elencare le code e i messaggi delle code di Azure. Per informazioni sulle azioni necessarie per una determinata operazione sui dati, vedere le [autorizzazioni per la chiamata di operazioni sui dati BLOB e della coda](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Scopri di più](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Read | Restituisce una coda o un elenco di code. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Read | Visualizza in anteprima o recupera uno o più messaggi da una coda. |
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


### <a name="azure-maps-data-reader"></a>Lettore di dati per Mappe di Azure

Concede l'accesso per la lettura dei dati correlati alle mappe da un account Mappe di Azure.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/Accounts/*/Read |  |
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
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Collaboratore servizi di ricerca

Consente di gestire i servizi di Ricerca, ma non di accedervi. [Scopri di più](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. search](resource-provider-operations.md#microsoftsearch)/searchServices/* | È in grado di creare e gestire servizi di ricerca |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

### <a name="signalr-accesskey-reader"></a>Lettore AccessKey SignalR

Leggere le chiavi di accesso al servizio SignalR

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/Read |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/Action | Visualizza il valore delle chiavi di accesso di SignalR nel portale di gestione o tramite API |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
  "description": "Read SignalR Service Access Keys",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/04165923-9d83-45d5-8227-78b77b0a687e",
  "name": "04165923-9d83-45d5-8227-78b77b0a687e",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*/read",
        "Microsoft.SignalRService/SignalR/listkeys/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR AccessKey Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-app-server-preview"></a>Server App SignalR (anteprima)

Consente all'app server di accedere al servizio SignalR con le opzioni di autenticazione AAD.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/AccessKey/Action | Generare una AccessKey temporanea per la firma di ClientTokens. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/ServerConnection/Write | Avviare una connessione al server. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app server access SignalR Service with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/420fcaa2-552c-430f-98ca-3264be4806c7",
  "name": "420fcaa2-552c-430f-98ca-3264be4806c7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/serverConnection/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR App Server (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-contributor"></a>Collaboratore SignalR

Creare, leggere, aggiornare ed eliminare le risorse del servizio SignalR

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
  "description": "Create, Read, Update, and Delete SignalR service resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "name": "8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*",
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
  "roleName": "SignalR Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-serverless-contributor-preview"></a>Collaboratore senza server SignalR (anteprima)

Consente al servizio app di accedere in modalità senza server con le opzioni di autenticazione AAD.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/Action | Genera un ClientToken per l'avvio di una connessione client. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app access service in serverless mode with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd53cd77-2268-407a-8f46-7e7863d0f521",
  "name": "fd53cd77-2268-407a-8f46-7e7863d0f521",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/clientToken/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Serverless Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-owner-preview"></a>Proprietario del servizio SignalR (anteprima)

Accesso completo alle API REST del servizio Azure SignalR

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Hub/Send/Action | Trasmettere messaggi a tutte le connessioni client nell'hub. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Send/Action | Trasmettere il messaggio al gruppo. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Read | Controllare l'esistenza del gruppo o l'esistenza dell'utente nel gruppo. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Write | Gruppo join/leave. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Send/Action | Inviare messaggi direttamente a una connessione client. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Read | Controllare l'esistenza della connessione client. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Write | Chiudere la connessione client. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Send/Action | Inviare messaggi a un utente che può essere costituito da più connessioni client. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Read | Verificare l'esistenza dell'utente. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Write |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "name": "7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Owner (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-reader-preview"></a>Lettore del servizio SignalR (anteprima)

Accesso in sola lettura alle API REST del servizio Azure SignalR

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Read | Controllare l'esistenza del gruppo o l'esistenza dell'utente nel gruppo. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Read | Controllare l'esistenza della connessione client. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Read | Verificare l'esistenza dell'utente. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ddde6b66-c0df-4114-a159-3618637b3035",
  "name": "ddde6b66-c0df-4114-a159-3618637b3035",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/user/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Collaboratore piani Web

Consente di gestire i piani Web per i siti Web, ma non di accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | Creare e gestire server farm |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/join/Action | Aggiunge un ambiente del servizio app |
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
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | È in grado di creare e gestire i componenti di Insights |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Certificates/* | Creare e gestire certificati dei siti Web |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/Read | Ottiene i nomi dei siti assegnati al nome host. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/Action | Aggiunge un piano di servizio app |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/Read | Ottiene le proprietà per un piano di servizio app |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/sites/* | Creare e gestire siti Web. Per creare un sito sono anche necessarie le autorizzazione di scrittura associate al piano di servizio app |
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

## <a name="containers"></a>Contenitori


### <a name="acrdelete"></a>AcrDelete

eliminazione di ACR ulteriori [informazioni](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Artifacts/Delete | Elimina l'artefatto in un registro contenitori. |
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

firmatario dell'immagine ACR [altre informazioni](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Sign/Write | Eseguire il push/pull di metadati considerati attendibili per un registro contenitori. |
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

Pull ACR- [altre informazioni](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/pull/Read | Eseguire il pull o ottenere immagini da un registro contenitori. |
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

push ACR ulteriori [informazioni](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/pull/Read | Eseguire il pull o ottenere immagini da un registro contenitori. |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/push/Write | Eseguire il push o scrivere immagini in un registro contenitori. |
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
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Quarantine/Read | Eseguire il pull o ottenere immagini in quarantena da un registro contenitori |
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
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Quarantine/Read | Eseguire il pull o ottenere immagini in quarantena da un registro contenitori |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Quarantine/Write | Scrivere/modificare lo stato di quarantena di immagini in quarantena |
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

Elencare l'azione delle credenziali di amministratore del cluster. [Scopri di più](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/Action | Elenca la credenziale clusterAdmin di un cluster gestito |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/Action | Recupera il profilo di accesso per un cluster gestito in base al nome del ruolo con un'operazione di elenco delle credenziali |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Recupera un cluster gestito |
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
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
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

Elencare l'azione delle credenziali di utente del cluster. [Scopri di più](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Elenca la credenziale clusterUser di un cluster gestito |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Recupera un cluster gestito |
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
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
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

### <a name="azure-kubernetes-service-contributor-role"></a>Ruolo Collaboratore servizio di Azure Kubernetes

Concessione dell'accesso per la lettura e la scrittura di cluster di servizi Kubernetes di Azure [altre informazioni](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Recupera un cluster gestito |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Write | Crea un nuovo cluster gestito o ne aggiorna uno esistente |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
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
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>Amministratore RBAC del servizio Kubernetes di Azure

Consente di gestire tutte le risorse in cluster/spazio dei nomi, ad eccezione di Aggiorna o Elimina le quote e gli spazi dei nomi delle risorse. [Scopri di più](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Crea o aggiorna una distribuzione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Elenca la credenziale clusterUser di un cluster gestito |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Write | Scrive resourcequotas |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Delete | Elimina resourcequotas |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Write | Scrive gli spazi dei nomi |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Delete | Elimina gli spazi dei nomi |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Amministrazione del cluster RBAC del servizio Kubernetes di Azure

Consente di gestire tutte le risorse del cluster. [Scopri di più](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Crea o aggiorna una distribuzione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Elenca la credenziale clusterUser di un cluster gestito |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>Lettore RBAC del servizio Kubernetes di Azure

Consente l'accesso in sola lettura per visualizzare la maggior parte degli oggetti in uno spazio dei nomi. Non consente la visualizzazione di ruoli o associazioni di ruolo. Questo ruolo non consente la visualizzazione dei segreti, perché la lettura del contenuto dei segreti consente l'accesso alle credenziali ServiceAccount nello spazio dei nomi, che consente l'accesso all'API come qualsiasi ServiceAccount nello spazio dei nomi (un tipo di escalation dei privilegi). L'applicazione di questo ruolo nell'ambito del cluster fornirà l'accesso a tutti gli spazi dei nomi. [Scopri di più](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Crea o aggiorna una distribuzione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/controllerrevisions/Read | Legge controllerrevisions |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/daemonsets/Read | Legge gli elementi daemonset |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/Deployments/Read | Legge le distribuzioni |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/ReplicaSets/Read | Legge ReplicaSets |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/statefulsets/Read | Legge statefulsets |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/AutoScaling/horizontalpodautoscalers/Read | Legge horizontalpodautoscalers |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/Read | Legge cronjobs |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/Jobs/Read | Legge i processi |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/Read | Legge configmaps |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Endpoints/Read | Legge gli endpoint |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events.K8S.io/Events/Read | Letture eventi |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events/Read | Letture eventi |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/daemonsets/Read | Legge gli elementi daemonset |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/Deployments/Read | Legge le distribuzioni |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/ingresses/Read | Letture in ingresso |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/networkpolicies/Read | Legge networkpolicies |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/ReplicaSets/Read | Legge ReplicaSets |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/Read | Legge limitranges |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Read | Legge gli spazi dei nomi |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.K8S.io/ingresses/Read | Letture in ingresso |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.K8S.io/networkpolicies/Read | Legge networkpolicies |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/Read | Legge persistentvolumeclaims |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/Read | Esegue la lettura di Pod |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Policy/poddisruptionbudgets/Read | Legge poddisruptionbudgets |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/Read | Legge replicationcontrollers |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/Read | Legge replicationcontrollers |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Read | Legge resourcequotas |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/ServiceAccounts/Read | Legge ServiceAccounts |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Services/Read | Legge i servizi |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read-only access to see most objects in a namespace. It does not allow viewing roles or role bindings. This role does not allow viewing Secrets, since reading the contents of Secrets enables access to ServiceAccount credentials in the namespace, which would allow API access as any ServiceAccount in the namespace (a form of privilege escalation). Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/apps/deployments/read",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/read",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/read",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/read",
        "Microsoft.ContainerService/managedClusters/batch/jobs/read",
        "Microsoft.ContainerService/managedClusters/configmaps/read",
        "Microsoft.ContainerService/managedClusters/endpoints/read",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/read",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/read",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/read",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read",
        "Microsoft.ContainerService/managedClusters/pods/read",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/read",
        "Microsoft.ContainerService/managedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Writer RBAC del servizio Kubernetes di Azure

Consente l'accesso in lettura/scrittura alla maggior parte degli oggetti in uno spazio dei nomi. Questo ruolo non consente la visualizzazione o la modifica di ruoli o associazioni di ruolo. Tuttavia, questo ruolo consente l'accesso ai segreti e l'esecuzione di pod come qualsiasi ServiceAccount nello spazio dei nomi, quindi può essere usato per ottenere i livelli di accesso all'API di qualsiasi ServiceAccount nello spazio dei nomi. L'applicazione di questo ruolo nell'ambito del cluster fornirà l'accesso a tutti gli spazi dei nomi. [Scopri di più](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Crea o aggiorna una distribuzione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/controllerrevisions/Read | Legge controllerrevisions |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/daemonsets/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/Deployments/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/ReplicaSets/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/statefulsets/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/AutoScaling/horizontalpodautoscalers/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/Jobs/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Endpoints/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events.K8S.io/Events/Read | Letture eventi |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events/Read | Letture eventi |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/daemonsets/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/Deployments/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/ingresses/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/networkpolicies/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/ReplicaSets/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/Read | Legge limitranges |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Read | Legge gli spazi dei nomi |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.K8S.io/ingresses/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.K8S.io/networkpolicies/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Policy/poddisruptionbudgets/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Read | Legge resourcequotas |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Secrets/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/ServiceAccounts/* |  |
> | [Microsoft. servizio contenitore](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Services/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read/write access to most objects in a namespace.This role does not allow viewing or modifying roles or role bindings. However, this role allows accessing Secrets and running Pods as any ServiceAccount in the namespace, so it can be used to gain the API access levels of any ServiceAccount in the namespace. Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/apps/deployments/*",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/*",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/*",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/*",
        "Microsoft.ContainerService/managedClusters/batch/jobs/*",
        "Microsoft.ContainerService/managedClusters/configmaps/*",
        "Microsoft.ContainerService/managedClusters/endpoints/*",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/*",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/*",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/*",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/*",
        "Microsoft.ContainerService/managedClusters/pods/*",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/secrets/*",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/*",
        "Microsoft.ContainerService/managedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Database


### <a name="cosmos-db-account-reader-role"></a>Ruolo Lettore dell'account Cosmos DB

Può leggere i dati degli account Azure Cosmos DB. Vedere [Collaboratore account DocumentDB](#documentdb-account-contributor) per la gestione degli account Azure Cosmos DB. [Scopri di più](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/Read | Leggere tutte le raccolte |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/Action | Legge le chiavi di sola lettura degli account di database. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/Read | Consente di leggere le definizioni della metrica |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Esegue la lettura delle metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

### <a name="cosmos-db-operator"></a>Operatore di Cosmos DB

Consente di gestire gli account Azure Cosmos DB, ma non di accedere ai dati contenuti negli stessi. Impedisce l'accesso a chiavi dell'account e stringhe di connessione. [Scopri di più](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Senza avvisi. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
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

È possibile inviare una richiesta di ripristino per un database Cosmos DB o un contenitore per un account [ulteriori informazioni](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/Action | Invia una richiesta per configurare il backup |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/Restore/Action | Invia una richiesta di ripristino |
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

È in grado di gestire account Azure Cosmos DB. Azure Cosmos DB era precedentemente noto come DocumentDB. [Scopri di più](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Creare e gestire account Azure Cosmos DB |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Senza avvisi. |
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
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. cache](resource-provider-operations.md#microsoftcache)/Register/Action | Registra il provider di risorse 'Microsoft.Cache' con una sottoscrizione |
> | [Microsoft. cache](resource-provider-operations.md#microsoftcache)/Redis/* | Creare e gestire cache Redis |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
        "Microsoft.Cache/register/action",
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

Consente di gestire i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza o i rispettivi server SQL padre. [Scopri di più](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/* | Creare e gestire database SQL |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Read | Restituisce l'elenco di server o ottiene le proprietà per il server specificato |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Esegue la lettura delle metriche |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Consente di leggere le definizioni della metrica |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditingSettings/* | Modificare le impostazioni di controllo |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditRecords/Read | Recupera i record di controllo BLOB del database |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/dataMaskingPolicies/* | Modificare i criteri di mascheratura dei dati |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityAlertPolicies/* | Modificare i criteri di avviso di sicurezza |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityMetrics/* | Modificare i criteri di protezione |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
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
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
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

### <a name="sql-managed-instance-contributor"></a>Collaboratore per Istanza gestita di SQL

Consente di gestire le istanze gestite di SQL e la configurazione di rete necessaria, ma non consente l'accesso ad altri utenti.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/locations/instanceFailoverGroups/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Esegue la lettura delle metriche |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Consente di leggere le definizioni della metrica |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/Delete | Elimina un server gestito specifico Azure Active Directory solo l'oggetto di autenticazione |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/Write | Aggiunge o aggiorna un server gestito specifico Azure Active Directory solo l'oggetto di autenticazione |
> | **DataActions** |  |
> | *nessuna* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
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
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write"
      ],
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

Consente di gestire i criteri relativi alla sicurezza di server e database SQL, ma non di accedervi. [Scopri di più](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Aggiunge una risorsa come un account di archiviazione o un database SQL a una subnet. Senza avvisi. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/locations/administratorAzureAsyncOperation/Read | Ottiene il risultato delle operazioni dell'amministratore asincrono di Azure per l'istanza gestita. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/auditingSettings/* | Creare e gestire le impostazioni di controllo di SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/extendedAuditingSettings/Read | Recupera i dettagli dei criteri di controllo BLOB del server esteso configurati in uno specifico server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditingSettings/* | Creare e gestire le impostazioni di controllo dei database di SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditRecords/Read | Recupera i record di controllo BLOB del database |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/dataMaskingPolicies/* | Creare e gestire i criteri della maschera dei dati dei database dei server SQL |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/extendedAuditingSettings/Read | Recupera i dettagli dei criteri di controllo BLOB esteso configurati in uno specifico database |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/Read | Restituisce l'elenco dei database o ottiene le proprietà per il database specificato |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Read | Recupera uno schema del database. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Tables/Columns/Read | Recupera una colonna del database. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Tables/Read | Recupera una tabella del database. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityAlertPolicies/* | Creare e gestire i criteri degli avvisi di sicurezza dei database di SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityMetrics/* | Creare e gestire le metriche di sicurezza dei database di server SQL |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/transparentDataEncryption/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/firewallRules/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Read | Restituisce l'elenco di server o ottiene le proprietà per il server specificato |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/securityAlertPolicies/* | Creare e gestire i criteri degli avvisi di sicurezza di SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureADOnlyAuthentications/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Read | Restituisce l'elenco delle istanze gestite o ottiene le proprietà per l'istanza gestita specificata. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
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
        "Microsoft.Sql/locations/administratorAzureAsyncOperation/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
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
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*"
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

Consente di gestire i server e i database SQL, ma non di accedervi né di gestirne i criteri relativi alla sicurezza. [Scopri di più](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/* | Creare e gestire server SQL |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Esegue la lettura delle metriche |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Consente di leggere le definizioni della metrica |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/auditingSettings/* | Modificare le impostazioni di controllo di SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditingSettings/* | Modificare le impostazioni di controllo dei database di SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/auditRecords/Read | Recupera i record di controllo BLOB del database |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/dataMaskingPolicies/* | Modificare i criteri di mascheratura dei dati dei database di SQL server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityAlertPolicies/* | Modificare i criteri degli avvisi di sicurezza dei database di SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/securityMetrics/* | Modificare le metriche di protezione dei database di SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/securityAlertPolicies/* | Modificare i criteri degli avvisi di sicurezza di SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureADOnlyAuthentications/Delete | Elimina un server specifico Azure Active Directory solo l'oggetto di autenticazione |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureADOnlyAuthentications/Write | Aggiunge o aggiorna un server specifico Azure Active Directory solo l'oggetto di autenticazione |
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
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
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
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/write"
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

## <a name="analytics"></a>Analytics


### <a name="azure-event-hubs-data-owner"></a>Proprietario dei dati di Hub eventi di Azure

Consente l'accesso completo alle risorse di Hub eventi di Azure. [Scopri di più](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
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

### <a name="azure-event-hubs-data-receiver"></a>Ricevitore dei dati di Hub eventi di Azure

Consente l'accesso per la ricezione alle risorse dell'Hub eventi di Azure. [Scopri di più](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Eventhubs/consumergroups/Read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Receive/Action |  |
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

### <a name="azure-event-hubs-data-sender"></a>Mittente dei dati di Hub eventi di Azure

Consente l'accesso per l'invio alle risorse dell'Hub eventi di Azure. [Scopri di più](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Eventhubs/Read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Send/Action |  |
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

Consente di creare e gestire data factory, oltre alle risorse figlio in esse contenute. [Scopri di più](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Creare e gestire data factory e le relative risorse figlio. |
> | [Microsoft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | Creare e gestire data factory e le relative risorse figlio. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Write | Creare o aggiornare un oggetto eventSubscription |
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

È possibile ripulire i dati di analisi [altre informazioni](../azure-monitor/platform/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/Purge/Action | Ripulitura dei dati da Application Insights |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Visualizzare i dati di analisi dei log |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Purge/Action | Elimina i dati specificati dall'area di lavoro |
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

### <a name="hdinsight-cluster-operator"></a>Operatore di cluster HDInsight

Consente di leggere e modificare le configurazioni dei cluster HDInsight. [Scopri di più](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/*/Read |  |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/getGatewaySettings/Action | Recuperare le impostazioni del gateway per il cluster HDInsight |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/updateGatewaySettings/Action | Aggiornare le impostazioni del gateway per il cluster HDInsight |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/Configurations/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Ottiene o elenca le operazioni di distribuzione. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Consente di leggere, creare, modificare ed eliminare le operazioni relative ai servizi di dominio necessarie per HDInsight Enterprise Security Package [altre informazioni](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)/*/Read |  |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/Read |  |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
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

Il ruolo Collaboratore di Log Analytics può leggere tutti i dati di monitoraggio e modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log dall'Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di soluzioni e la configurazione di Diagnostica di Azure in tutte le risorse di Azure. [Scopri di più](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/Extensions/* |  |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/Extensions/* |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/Extensions/Write | Installa o aggiorna le estensioni di Azure Arc |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/ResourceGroups/Deployments/* |  |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
        "Microsoft.HybridCompute/machines/extensions/write",
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

Il ruolo Lettore di Log Analytics può visualizzare ed eseguire ricerche in tutti i dati di monitoraggio e può visualizzare le impostazioni di monitoraggio, inclusa la visualizzazione della configurazione di Diagnostica di Azure in tutte le risorse di Azure. [Scopri di più](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/query/Action | Esegue la ricerca usando il nuovo motore. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Esegue una query di ricerca |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/sharedKeys/Read | Recupera le chiavi condivise per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro. |
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

### <a name="schema-registry-contributor-preview"></a>Collaboratore del Registro schemi (anteprima)

Lettura, scrittura ed eliminazione di gruppi e schemi del Registro schemi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemas/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read, write, and delete Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5dffeca3-4936-4216-b2bc-10343a5abb25",
  "name": "5dffeca3-4936-4216-b2bc-10343a5abb25",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-reader-preview"></a>Lettore del Registro schemi (anteprima)

Lettura ed elenco di gruppi e schemi del Registro schemi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemagroups/Read | Ottiene l'elenco delle descrizioni delle risorse SchemaGroup |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemas/Read | Recuperare schemi |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and list Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "name": "2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockchain


### <a name="blockchain-member-node-access-preview"></a>Accesso ai nodi di tipo membro della blockchain (anteprima)

Consente di accedere ai nodi membro blockchain [altre informazioni](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/Read | Recupera o elenca i nodi della transazione di tipo membro della blockchain esistenti. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/Connect/Action | Stabilisce la connessione a un nodo della transazione di tipo membro della blockchain. |
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

## <a name="ai--machine-learning"></a>Intelligenza artificiale e Machine Learning


### <a name="cognitive-services-contributor"></a>Collaboratore Servizi cognitivi

Consente di creare, leggere, aggiornare, eliminare e gestire le chiavi di Servizi cognitivi. [Scopri di più](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft. Features](resource-provider-operations.md#microsoftfeatures)/features/Read | Ottiene le funzionalità di una sottoscrizione. |
> | [Microsoft. Features](resource-provider-operations.md#microsoftfeatures)/Providers/features/Read | Ottiene la funzionalità di una sottoscrizione in un provider di risorse specificato. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/Read | Consente di leggere le definizioni del log |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/Read | Consente di leggere le definizioni della metrica |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Esegue la lettura delle metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Ottiene o elenca le operazioni di distribuzione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/ResourceGroups/Deployments/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

### <a name="cognitive-services-data-reader-preview"></a>Ruolo con autorizzazioni di lettura per i dati di Servizi cognitivi (anteprima)

Consente di leggere i dati di Servizi cognitivi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
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

Consente di leggere ed elencare le chiavi di Servizi cognitivi. [Scopri di più](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/Action | Elenco delle chiavi |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Legge un avviso della metrica (versione classica) |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/Read | Legge un'impostazione di diagnostica della risorsa |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/Read | Consente di leggere le definizioni del log |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/Read | Consente di leggere le definizioni della metrica |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Esegue la lettura delle metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Ottiene o elenca le operazioni di distribuzione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
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

## <a name="mixed-reality"></a>Realtà mista


### <a name="remote-rendering-administrator"></a>Amministratore per il rendering remoto

Fornisce agli utenti le funzionalità di conversione, gestione sessione, rendering e diagnostica per il rendering remoto di Azure [altre informazioni](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Action | Avviare la conversione asset |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Read | Ottenere le proprietà di conversione delle risorse |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Delete | Arresta conversione asset |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Read | Ottenere le proprietà della sessione |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Action | Avvia sessioni |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Delete | Arresta sessioni |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/Read | Connettersi a una sessione |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Diagnostic/Read | Connettersi al controllo di rendering remoto |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>Client di rendering remoto

Consente agli utenti di gestire le funzionalità di sessione, rendering e diagnostica per il rendering remoto di Azure. [Scopri di più](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Read | Ottenere le proprietà della sessione |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Action | Avvia sessioni |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Delete | Arresta sessioni |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/Read | Connettersi a una sessione |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Diagnostic/Read | Connettersi al controllo di rendering remoto |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Collaboratore per l'account per gli ancoraggi spaziali

Consente di gestire gli ancoraggi spaziali nell'account, ma non di eliminarli per [saperne di più](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/Action | Creare ancoraggi nello spazio |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Individuare gli ancoraggi nello spazio vicini |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Recuperare le proprietà degli ancoraggi nello spazio |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Individuare gli ancoraggi nello spazio |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Inviare i dati di diagnostica per contribuire a migliorare la qualità del servizio di ancoraggi nello spazio di Azure |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Write | Aggiornare le proprietà degli ancoraggi nello spazio |
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

### <a name="spatial-anchors-account-owner"></a>Proprietario dell'account per gli ancoraggi spaziali

Consente di gestire gli ancoraggi spaziali nell'account, inclusa l'eliminazione di [altre informazioni](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/Action | Creare ancoraggi nello spazio |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Delete | Eliminare gli ancoraggi nello spazio |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Individuare gli ancoraggi nello spazio vicini |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Recuperare le proprietà degli ancoraggi nello spazio |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Individuare gli ancoraggi nello spazio |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Inviare i dati di diagnostica per contribuire a migliorare la qualità del servizio di ancoraggi nello spazio di Azure |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Write | Aggiornare le proprietà degli ancoraggi nello spazio |
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

### <a name="spatial-anchors-account-reader"></a>Ruolo Lettore dell'account per gli ancoraggi spaziali

Consente di individuare e leggere le proprietà degli ancoraggi spaziali nell'account ulteriori [informazioni](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Individuare gli ancoraggi nello spazio vicini |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Recuperare le proprietà degli ancoraggi nello spazio |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Individuare gli ancoraggi nello spazio |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Inviare i dati di diagnostica per contribuire a migliorare la qualità del servizio di ancoraggi nello spazio di Azure |
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

## <a name="integration"></a>Integrazione


### <a name="api-management-service-contributor"></a>Collaboratore servizio Gestione API

È possibile gestire il servizio e le API [altre informazioni](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/* | È in grado di creare e gestire il servizio Gestione API |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Può gestire il servizio ma non le API [altre informazioni](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/*/Read | Leggere le istanze del servizio Gestione API |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/backup/Action | Esegue il backup del servizio Gestione API nel contenitore specificato in un account di archiviazione fornito dall’utente |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Delete | Elimina l’istanza del servizio Gestione API |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/managedeployments/Action | Modifica SKU/unità, aggiunge/rimuove distribuzioni regionali del servizio Gestione API |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Read | Leggere i metadati per un'istanza del servizio Gestione API |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Restore/Action | Ripristinare il servizio Gestione API dal contenitore specificato in un account di archiviazione fornito dall'utente |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/updatecertificate/Action | Carica il certificato TLS/SSL per un servizio Gestione API. |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/updatehostname/Action | Configura, aggiorna o rimuove i nomi di dominio personalizzati per un servizio Gestione API. |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Write | Creare o aggiornare un'istanza del servizio Gestione API di Azure |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Users/Keys/Read | Recuperare le chiavi associate all'utente |
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

Accesso in sola lettura al servizio e alle API [altre informazioni](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/*/Read | Leggere le istanze del servizio Gestione API |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Read | Leggere i metadati per un'istanza del servizio Gestione API |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Users/Keys/Read | Recuperare le chiavi associate all'utente |
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

### <a name="app-configuration-data-owner"></a>Proprietario dei dati di Configurazione dell'app

Consente l'accesso completo ai dati di Configurazione dell'app. [Scopri di più](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Read |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Write |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Delete |  |
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

### <a name="app-configuration-data-reader"></a>Ruolo con autorizzazioni di lettura per i dati di Configurazione dell'app

Consente l'accesso in lettura ai dati di Configurazione dell'app. [Scopri di più](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Read |  |
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

### <a name="azure-service-bus-data-owner"></a>Proprietario dei dati del bus di servizio di Azure

Consente l'accesso completo alle risorse del bus di servizio di Azure. [Scopri di più](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
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

### <a name="azure-service-bus-data-receiver"></a>Ricevitore dei dati del bus di servizio di Azure

Consente l'accesso per la ricezione alle risorse del bus di servizio di Azure. [Scopri di più](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Queues/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/Read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Receive/Action |  |
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

### <a name="azure-service-bus-data-sender"></a>Mittente dei dati del bus di servizio di Azure

Consente l'accesso per l'invio alle risorse del bus di servizio di Azure. [Scopri di più](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Queues/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/Read |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Send/Action |  |
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

### <a name="azure-stack-registration-owner"></a>Proprietario della registrazione di Azure Stack

Consente di gestire le registrazioni di Azure Stack.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/Read | Ottenere le proprietà di una sottoscrizione di Azure Stack Edge |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Products/*/Action |  |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Products/Read | Ottiene le proprietà di un prodotto del marketplace Azure Stack |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Read | Ottiene le proprietà di una registrazione di Azure Stack |
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
        "Microsoft.AzureStack/edgeSubscriptions/read",
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

### <a name="eventgrid-eventsubscription-contributor"></a>Collaboratore per sottoscrizioni di eventi di Griglia di eventi

Consente di gestire le operazioni di sottoscrizione di eventi EventGrid. [Scopri di più](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* |  |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/Read | Elenca le sottoscrizioni di eventi globali per tipo di argomento |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/Read | Elenca sottoscrizioni di eventi per area |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/Read | Elenca sottoscrizioni di eventi per area per tipo di argomento |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Consente di leggere le sottoscrizioni di eventi EventGrid. [Scopri di più](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Read | Leggere un oggetto eventSubscription |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/Read | Elenca le sottoscrizioni di eventi globali per tipo di argomento |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/Read | Elenca sottoscrizioni di eventi per area |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/Read | Elenca sottoscrizioni di eventi per area per tipo di argomento |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
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

### <a name="fhir-data-contributor"></a>Collaboratore dati FHIR

Il ruolo consente all'utente o all'entità di accesso completo ai dati di FHIR [altre informazioni](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/Services/Fhir/Resources/* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>Esportatore dati FHIR

Il ruolo consente all'utente o all'entità di leggere ed esportare i dati di FHIR [altre informazioni](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/Services/Fhir/Resources/Read | Leggere le risorse di FHIR (include la ricerca e la cronologia delle versioni).  |
> | Microsoft. HealthcareApis/Services/Fhir/Resources/Export/Action | Operazione di esportazione ($export). |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>Lettore dati FHIR

Il ruolo consente all'utente o all'entità di leggere i dati di FHIR [altre informazioni](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/Services/Fhir/Resources/Read | Leggere le risorse di FHIR (include la ricerca e la cronologia delle versioni).  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>FHIR Data writer

Il ruolo consente all'utente o all'entità di leggere e scrivere i dati di FHIR [altre informazioni](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/Services/Fhir/Resources/* |  |
> | **NotDataActions** |  |
> | Microsoft. HealthcareApis/Services/Fhir/Resources/hardDelete/Action | Eliminazione hardware (inclusa la cronologia delle versioni). |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>Collaboratore ambiente del servizio di integrazione

Consente di gestire gli ambienti del servizio di integrazione, ma non di accedervi. [Scopri di più](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
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
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>Sviluppatore ambiente del servizio di integrazione

Consente agli sviluppatori di creare e aggiornare i flussi di lavoro, gli account di integrazione e le connessioni API negli ambienti di Integration Services. [Scopri di più](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/Read | Legge l'ambiente del servizio di integrazione. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/join/Action | Aggiunge l'ambiente del servizio di integrazione. |
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
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Collaboratore account Intelligent Systems

Consente di gestire gli account Sistemi intelligenti, ma non di accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | Microsoft.IntelligentSystems/accounts/* | Creare e gestire account di Intelligent Systems |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Consente di gestire le app per la logica, ma non di modificarne l'accesso. [Scopri di più](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Elenca le chiavi di accesso per gli account di archiviazione. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/Read | Restituisce l'account di archiviazione con l'account specificato. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai log attività tramite il portale. Elencare categorie di log nel log attività. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | Definizioni delle metriche (elenco dei tipi di metriche disponibili per una risorsa). |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/* | Gestisce le risorse di App per la logica. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/Action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Crea e gestisce un gateway di connessione. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connections/* | Crea e gestisce una connessione. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/* | Crea e gestisce un'API personalizzata. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/Action | Aggiunge un piano di servizio app |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/Read | Ottiene le proprietà per un piano di servizio app |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/sites/Functions/listSecrets/Action | Elenca i segreti delle funzioni. |
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

Consente di leggere, abilitare e disabilitare le app per la logica, ma non di modificarle o aggiornarle. [Scopri di più](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/Read | Legge le regole di avviso di Insights |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/Read | Ottiene le impostazioni di diagnostica di App per la logica |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/Read | Ottiene le metriche disponibili per App per la logica. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/*/Read | Legge le risorse di App per la logica. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/workflows/Disable/Action | Disabilita il flusso di lavoro. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/workflows/Enable/Action | Abilita il flusso di lavoro. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/workflows/Validate/Action | Convalida il flusso di lavoro. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Ottiene o elenca le operazioni di distribuzione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/Read | Legge i gateway di connessione. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connections/*/Read | Legge le connessioni. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/*/Read | Legge l'API personalizzata. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/Read | Ottiene le proprietà per un piano di servizio app |
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

## <a name="identity"></a>Identità


### <a name="managed-identity-contributor"></a>Managed Identity Contributor (Collaboratore per identità gestita)

Creazione, lettura, aggiornamento ed eliminazione dell'identità assegnata dall'utente [altre informazioni](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Read | Ottiene l'identità assegnata a un utente esistente |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Write | Crea una nuova identità assegnata a un utente esistente o aggiorna i tag associati a un'identità assegnata a un utente esistente |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Delete | Elimina l'identità assegnata a un utente esistente |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Lettura e assegnazione dell'identità assegnata dall'utente [altre informazioni](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/Read |  |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/Assign/Action |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

## <a name="security"></a>Sicurezza


### <a name="azure-sentinel-contributor"></a>Collaboratore di Azure Sentinel

Collaboratore sentinella di Azure [altre informazioni](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/query/Action | Esegue la ricerca usando il nuovo motore. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Visualizzare i dati di analisi dei log |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/* |  |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Ottiene soluzione OMS esistente |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/query/read | Esegue query sui dati nell'area di lavoro |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/query/*/read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Ottiene le origini dati in un'area di lavoro. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
        "Microsoft.OperationalInsights/workspaces/*/read",
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

### <a name="azure-sentinel-reader"></a>Ruolo con autorizzazioni di lettura di Azure Sentinel

Azure Sentinel Reader ulteriori [informazioni](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/Read |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/Action | Controllare l'autorizzazione utente e la licenza |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/query/Action | Indicatori di intelligence per le minacce per le query |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/Action | Indicatori di intelligence per le minacce per le query |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/query/Action | Esegue la ricerca usando il nuovo motore. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Visualizzare i dati di analisi dei log |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/LinkedServices/Read | Ottiene i servizi collegati nell'area di lavoro specificata. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/Read | Ottiene una query di ricerca salvata |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Ottiene soluzione OMS esistente |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/query/read | Esegue query sui dati nell'area di lavoro |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/query/*/read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Ottiene le origini dati in un'area di lavoro. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Leggere una cartella di lavoro |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
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

### <a name="azure-sentinel-responder"></a>Risponditore di Azure Sentinel

Risponditore sentinella di Azure [altre informazioni](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/Read |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/Action | Controllare l'autorizzazione utente e la licenza |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/Cases/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/Incidents/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/appendTags/Action | Aggiungere tag all'indicatore di intelligence per le minacce |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/query/Action | Indicatori di intelligence per le minacce per le query |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/Action | Intelligence per le minacce per i tag bulk |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/appendTags/Action | Aggiungere tag all'indicatore di intelligence per le minacce |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/replaceTags/Action | Sostituisci tag dell'indicatore di intelligence per le minacce |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/Action | Indicatori di intelligence per le minacce per le query |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/query/Action | Esegue la ricerca usando il nuovo motore. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Visualizzare i dati di analisi dei log |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Ottiene le origini dati in un'area di lavoro. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/Read | Ottiene una query di ricerca salvata |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Ottiene soluzione OMS esistente |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/query/read | Esegue query sui dati nell'area di lavoro |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/query/*/read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Ottiene le origini dati in un'area di lavoro. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Leggere una cartella di lavoro |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/bulkTag/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/replaceTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
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

### <a name="key-vault-administrator-preview"></a>Amministratore Key Vault (anteprima)

Eseguire tutte le operazioni del piano dati in un insieme di credenziali delle chiavi e in tutti gli oggetti al suo interno, inclusi i certificati, le chiavi e i segreti. Non è possibile gestire le risorse di Key Vault o gestire le assegnazioni di ruolo. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft./CheckNameAvailability/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Controlla che il nome dell'insieme di credenziali delle chiavi sia valido e che non sia in uso |
> | [Microsoft./DeletedVaults/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Visualizza le proprietà di Key Vault eliminati temporaneamente |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/locations/*/Read |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft./Operations/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Elenca le operazioni disponibili nel provider di risorse Microsoft.KeyVault |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft./Vaults/Vault](resource-provider-operations.md#microsoftkeyvault)* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer-preview"></a>Ufficiale certificati Key Vault (anteprima)

Eseguire qualsiasi azione sui certificati di un insieme di credenziali delle chiavi, eccetto le autorizzazioni Manage. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft./CheckNameAvailability/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Controlla che il nome dell'insieme di credenziali delle chiavi sia valido e che non sia in uso |
> | [Microsoft./DeletedVaults/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Visualizza le proprietà di Key Vault eliminati temporaneamente |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/locations/*/Read |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft./Operations/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Elenca le operazioni disponibili nel provider di risorse Microsoft.KeyVault |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft./Vaults/certificatecas/Vault](resource-provider-operations.md#microsoftkeyvault)* |  |
> | [Microsoft./Vaults/Certificates/Vault](resource-provider-operations.md#microsoftkeyvault)* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Collaboratore di Key Vault

Consente di gestire gli insiemi di credenziali delle chiavi, ma non di assegnare i ruoli in Azure RBAC e di accedere a segreti, chiavi o certificati. [Scopri di più](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Vault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | **NotActions** |  |
> | [Microsoft./locations/deletedVaults/Purge/Action Vault](resource-provider-operations.md#microsoftkeyvault) | Ripulisce un Key Vault eliminato temporaneamente |
> | [Microsoft./HsmPools/Vault](resource-provider-operations.md#microsoftkeyvault)* |  |
> | [Microsoft./ManagedHsms/Vault](resource-provider-operations.md#microsoftkeyvault)* |  |
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
        "Microsoft.KeyVault/hsmPools/*",
        "Microsoft.KeyVault/managedHsms/*"
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

### <a name="key-vault-crypto-officer-preview"></a>Key Vault Crypto Officer (anteprima)

Eseguire qualsiasi azione sulle chiavi di un insieme di credenziali delle chiavi, eccetto le autorizzazioni Manage. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft./CheckNameAvailability/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Controlla che il nome dell'insieme di credenziali delle chiavi sia valido e che non sia in uso |
> | [Microsoft./DeletedVaults/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Visualizza le proprietà di Key Vault eliminati temporaneamente |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/locations/*/Read |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft./Operations/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Elenca le operazioni disponibili nel provider di risorse Microsoft.KeyVault |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft./Vaults/Keys/Vault](resource-provider-operations.md#microsoftkeyvault)* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-preview"></a>Crittografia del servizio di crittografia Key Vault (anteprima)

Legge i metadati delle chiavi ed esegue operazioni di wrapping/Unwrap. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft./Vaults/Keys/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Elencare le chiavi nell'insieme di credenziali specificato oppure leggere le proprietà e il materiale pubblico di una chiave. Per le chiavi asimmetriche, questa operazione espone la chiave pubblica e include la possibilità di eseguire algoritmi a chiave pubblica, ad esempio la crittografia e la verifica della firma. Chiavi private e chiavi simmetriche non vengono mai esposte. |
> | [Microsoft./Vaults/Keys/wrap/Action Vault](resource-provider-operations.md#microsoftkeyvault) | Eseguire il wrapping di una chiave simmetrica con una chiave Key Vault. Si noti che se la chiave di Key Vault è asimmetrica, questa operazione può essere eseguita con l'accesso in lettura. |
> | [Microsoft./Vaults/Keys/Unwrap/Action Vault](resource-provider-operations.md#microsoftkeyvault) | Annullare il wrapping di una chiave simmetrica con una chiave di Key Vault. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user-preview"></a>Utente di crittografia Key Vault (anteprima)

Eseguire operazioni di crittografia usando chiavi. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft./Vaults/Keys/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Elencare le chiavi nell'insieme di credenziali specificato oppure leggere le proprietà e il materiale pubblico di una chiave. Per le chiavi asimmetriche, questa operazione espone la chiave pubblica e include la possibilità di eseguire algoritmi a chiave pubblica, ad esempio la crittografia e la verifica della firma. Chiavi private e chiavi simmetriche non vengono mai esposte. |
> | [Microsoft./Vaults/Keys/Update/Action Vault](resource-provider-operations.md#microsoftkeyvault) | Aggiorna gli attributi specificati associati alla chiave specificata. |
> | [Microsoft./Vaults/Keys/backup/Action Vault](resource-provider-operations.md#microsoftkeyvault) | Creare il file di backup di una chiave. Il file può essere usato per ripristinare la chiave in un Key Vault della stessa sottoscrizione. Potrebbero essere applicate le restrizioni. |
> | [Microsoft./Vaults/Keys/Encrypt/Action Vault](resource-provider-operations.md#microsoftkeyvault) | Crittografare il testo non crittografato con una chiave. Si noti che, se la chiave è asimmetrica, questa operazione può essere eseguita dalle entità con accesso in lettura. |
> | [Microsoft./Vaults/Keys/Decrypt/Action Vault](resource-provider-operations.md#microsoftkeyvault) | Decrittografare il testo crittografato con una chiave. |
> | [Microsoft./Vaults/Keys/wrap/Action Vault](resource-provider-operations.md#microsoftkeyvault) | Eseguire il wrapping di una chiave simmetrica con una chiave Key Vault. Si noti che se la chiave di Key Vault è asimmetrica, questa operazione può essere eseguita con l'accesso in lettura. |
> | [Microsoft./Vaults/Keys/Unwrap/Action Vault](resource-provider-operations.md#microsoftkeyvault) | Annullare il wrapping di una chiave simmetrica con una chiave di Key Vault. |
> | [Microsoft./Vaults/Keys/Sign/Action Vault](resource-provider-operations.md#microsoftkeyvault) | Firmare un hash con una chiave. |
> | [Microsoft./Vaults/Keys/Verify/Action Vault](resource-provider-operations.md#microsoftkeyvault) | Verificare un hash. Si noti che, se la chiave è asimmetrica, questa operazione può essere eseguita dalle entità con accesso in lettura. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader-preview"></a>Lettore di Key Vault (anteprima)

Leggere i metadati di insiemi di credenziali delle chiavi e i relativi certificati, chiavi e segreti. Non è possibile leggere i valori sensibili come il contenuto del segreto o il materiale della chiave. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft./CheckNameAvailability/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Controlla che il nome dell'insieme di credenziali delle chiavi sia valido e che non sia in uso |
> | [Microsoft./DeletedVaults/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Visualizza le proprietà di Key Vault eliminati temporaneamente |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/locations/*/Read |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft./Operations/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Elenca le operazioni disponibili nel provider di risorse Microsoft.KeyVault |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft./Vaults/Secrets/readMetadata/Action Vault](resource-provider-operations.md#microsoftkeyvault) | Elencare o visualizzare le proprietà di un segreto ma non il relativo valore. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer-preview"></a>Amministratore di Key Vault Secrets (anteprima)

Eseguire qualsiasi azione sui segreti di un insieme di credenziali delle chiavi, eccetto le autorizzazioni di gestione. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft./CheckNameAvailability/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Controlla che il nome dell'insieme di credenziali delle chiavi sia valido e che non sia in uso |
> | [Microsoft./DeletedVaults/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Visualizza le proprietà di Key Vault eliminati temporaneamente |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/locations/*/Read |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/*/Read |  |
> | [Microsoft./Operations/Read Vault](resource-provider-operations.md#microsoftkeyvault) | Elenca le operazioni disponibili nel provider di risorse Microsoft.KeyVault |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft./Vaults/Secrets/Vault](resource-provider-operations.md#microsoftkeyvault)* |  |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user-preview"></a>Utente di Key Vault Secrets (anteprima)

Leggere il contenuto del segreto. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure".

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft./Vaults/Secrets/getSecret/Action Vault](resource-provider-operations.md#microsoftkeyvault) | Ottenere il valore di un segreto. |
> | [Microsoft./Vaults/Secrets/readMetadata/Action Vault](resource-provider-operations.md#microsoftkeyvault) | Elencare o visualizzare le proprietà di un segreto ma non il relativo valore. |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Amministrazione della protezione

Visualizzazione e aggiornamento delle autorizzazioni per il Centro sicurezza. Ha le stesse autorizzazioni del Ruolo con autorizzazioni di lettura per la sicurezza e può anche aggiornare i criteri di sicurezza e rimuovere gli avvisi e le raccomandazioni. [Scopri di più](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Creare e gestire assegnazioni di criteri |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/PolicyDefinitions/* | Creare e gestire definizioni di criteri |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Creare e gestire set di criteri |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Elenca i gruppi di gestione per l'utente autenticato. |
> | [Microsoft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Visualizzare i dati di analisi dei log |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/* | Creare e gestire criteri e componenti di protezione |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
        "Microsoft.Authorization/policyExemptions/*",
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

### <a name="security-assessment-contributor"></a>Collaboratore per valutazioni della sicurezza

Consente di eseguire il push delle valutazioni nel Centro sicurezza

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/assessments/Write | Creare o aggiornare valutazioni della sicurezza per la sottoscrizione |
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

Questo è un ruolo legacy. Usare invece Amministratore della sicurezza.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/Read | Leggere le informazioni di configurazione delle macchine virtuali classiche |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/Write | Scrivere la configurazione delle macchine virtuali classiche |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/Read | Leggere le informazioni della configurazione sulla rete classica |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/* | Creare e gestire criteri e componenti di protezione |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Visualizzazione delle autorizzazioni per il Centro sicurezza. Può visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non può apportare modifiche. [Scopri di più](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Legge un avviso della metrica (versione classica) |
> | [Microsoft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Visualizzare i dati di analisi dei log |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/*/Read |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/*/Read | Leggere criteri e componenti di sicurezza |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/*/Read |  |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Elenca i gruppi di gestione per l'utente autenticato. |
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
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*/read",
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

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>Utente DevTest Labs

Consente di connettere, avviare, riavviare e arrestare le macchine virtuali in Azure DevTest Labs. [Scopri di più](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/Read | Ottiene le proprietà di un set di disponibilità |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read | Leggere le proprietà di una macchina virtuale (dimensioni della VM, stato di runtime, estensioni della VM e così via) |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/Action | Disabilita la macchina virtuale e rilascia le risorse di calcolo |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/Read | Ottiene le proprietà di una macchina virtuale |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/Action | Riavvia la macchina virtuale |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/Start/Action | Avvia la macchina virtuale |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/Read | Leggere le proprietà di un lab |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/claimAnyVm/Action | Attesta una macchina virtuale attestabile casualmente nel lab. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/createEnvironment/Action | Crea macchine virtuali in un lab. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/ensureCurrentUserProfile/Action | Consente di verificare che nel lab sia presente un profilo valido per l'utente corrente. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/Formulas/Delete | Elimina le formule. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/Formulas/Read | Esegue la lettura di formule. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/Formulas/Write | Aggiunge o modifica le formule. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/policySets/evaluatePolicies/Action | Valuta i criteri del lab. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/Claim/Action | Consente di assumere la proprietà di una macchina virtuale esistente |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/VirtualMachines/listApplicableSchedules/Action | Elenca le pianificazioni di avvio/arresto applicabili, se presenti. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/getRdpFileContents/Action | Ottiene una stringa che rappresenta il contenuto del file RDP per la macchina virtuale |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/Action | Aggiunge un pool di indirizzi di back-end del servizio di bilanciamento del carico. Senza avvisi. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/Action | Aggiunge una regola NAT in entrata del servizio di bilanciamento del carico. Senza avvisi. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/Read | Legge le proprietà di un'interfaccia di rete, ad esempio tutti i servizi di bilanciamento del carico di cui fa parte l'interfaccia di rete |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/Action | Aggiunge una macchina virtuale a un'interfaccia di rete. Senza avvisi. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Ottiene una definizione dell’interfaccia di rete.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Write | Crea un'interfaccia di rete o ne aggiorna una esistente.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/Read | Leggere le proprietà di un indirizzo IP pubblico |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/Action | Aggiunge un indirizzo IP pubblico. Senza avvisi. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Ottiene una definizione dell’indirizzo IP pubblico. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/join/Action | Aggiunge una rete virtuale. Senza avvisi. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Ottiene o elenca le operazioni di distribuzione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Ottiene o elenca le distribuzioni. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Restituisce le chiavi di accesso per l'account di archiviazione specificato. |
> | **NotActions** |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/Read | Elenca le dimensioni disponibili a cui è possibile aggiornare la macchina virtuale |
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

### <a name="lab-creator"></a>Lab Creator (Creatore di lab)

Consente di creare nuovi Lab con gli account di Azure Lab. [Scopri di più](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/Read |  |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/Action | Crea un lab in un account del lab. |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/Action | Consente di ottenere i prezzi e la disponibilità di combinazioni di dimensioni, aree geografiche e sistemi operativi per l'account lab. |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/Action | Ottiene informazioni sulle restrizioni di base e l'utilizzo per questa sottoscrizione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
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

## <a name="monitor"></a>Monitorare


### <a name="application-insights-component-contributor"></a>Collaboratore componente di Application Insights

È possibile gestire i componenti di Application Insights [ulteriori informazioni](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire le regole di avviso classiche |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Creare e gestire nuove regole di avviso |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | È in grado di creare e gestire i componenti di Insights |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Creare e gestire i test Web di Insights |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Concede l'autorizzazione utente per visualizzare e scaricare gli snapshot di debug raccolti con Application Insights Snapshot Debugger. Si noti che queste autorizzazioni non sono incluse nei ruoli [Proprietario](#owner) e [Collaboratore](#contributor). Quando si assegna agli utenti il ruolo Snapshot Debugger di Application Insights, è necessario concedere il ruolo direttamente all'utente. Il ruolo non viene riconosciuto quando viene aggiunto a un ruolo personalizzato. [Scopri di più](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/*/Read |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

### <a name="monitoring-contributor"></a>Collaboratore al monitoraggio

Può leggere tutti i dati del monitoraggio e modificare le impostazioni di monitoraggio. Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). [Scopri di più](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/Alerts/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | È in grado di creare e gestire i componenti di Insights |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Crea, aggiorna o legge l'impostazione di diagnostica per Analysis Server |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/EventTypes/* | Elenco degli eventi dei registri attività (eventi di gestione) in una sottoscrizione. Questa autorizzazione è applicabile sia all'accesso programmatico che all'accesso al portale per il registro attività. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai log attività tramite il portale. Elencare categorie di log nel log attività. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Definizioni delle metriche (elenco dei tipi di metriche disponibili per una risorsa). |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | Metriche per una risorsa. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Registra il provider Microsoft Insights |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Creare e gestire i test Web di Insights |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Write | Crea una nuova area di lavoro o collega a un'area di lavoro esistente fornendo l'ID cliente dall’area di lavoro esistente. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/intelligencepacks/* | Consente di leggere, scrivere ed eliminare pacchetti di soluzioni dell'analisi dei log. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/* | Consente di leggere, scrivere ed eliminare ricerche salvate dell'analisi dei log. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Esegue una query di ricerca |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/sharedKeys/Action | Recupera le chiavi condivise per l'area di lavoro. Queste chiavi servono per collegare gli agenti di Microsoft Operational Insights all’area di lavoro. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/storageinsightconfigs/* | Consente di leggere, scrivere ed eliminare le configurazioni di dati di archiviazione dell'analisi dei log. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/Monitors/* | Ottenere informazioni sui monitoraggi integrità della macchina virtuale guest.  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
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
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
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
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
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

Abilita la pubblicazione di metriche sulle risorse di Azure [altre informazioni](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Registra il provider Microsoft Insights |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | Scrive metrica |
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

Può leggere tutti i dati del monitoraggio (metriche, log e così via). Vedere anche [Introduzione a ruoli, autorizzazioni e sicurezza con il monitoraggio di Azure](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). [Scopri di più](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Esegue una query di ricerca |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

### <a name="workbook-contributor"></a>Collaboratore per le cartelle di lavoro

Può salvare le cartelle di lavoro condivise. [Scopri di più](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Write | Creare o aggiornare una cartella di lavoro |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Delete | Eliminare una cartella di lavoro |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Leggere una cartella di lavoro |
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

### <a name="workbook-reader"></a>Ruolo con autorizzazioni di lettura per le cartelle di lavoro

Può leggere le cartelle di lavoro. [Scopri di più](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Leggere una cartella di lavoro |
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

## <a name="management--governance"></a>Gestione e governance


### <a name="automation-job-operator"></a>Operatore processo di automazione

Consente di creare e gestire i processi tramite i runbook di Automazione. [Scopri di più](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/Read | Legge le risorse del ruolo di lavoro ibrido per runbook |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Read | Ottiene un processo di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Resume/Action | Riprende un processo di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Stop/Action | Arresta un processo di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Streams/Read | Ottiene un flusso del processo di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Suspend/Action | Sospende un processo di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Write | Crea un processo di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/output/Read | Ottiene l'output di un processo |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Gli operatori di automazione possono avviare, arrestare, sospendere e riprendere i processi ulteriori [informazioni](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/Read | Legge le risorse del ruolo di lavoro ibrido per runbook |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Read | Ottiene un processo di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Resume/Action | Riprende un processo di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Stop/Action | Arresta un processo di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Streams/Read | Ottiene un flusso del processo di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Suspend/Action | Sospende un processo di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Write | Crea un processo di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/Read | Ottiene una pianificazione del processo di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/Write | Crea una pianificazione del processo di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/Read | Ottiene l'area di lavoro collegata all'account di automazione |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Read | Ottiene un account di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/Read | Ottiene un runbook di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/Read | Ottiene un asset della pianificazione di automazione di Azure |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/Write | Crea o aggiorna un asset della pianificazione di automazione di Azure |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/output/Read | Ottiene l'output di un processo |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Consente di leggere le proprietà del runbook per permettere di creare processi del runbook. [Scopri di più](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/Read | Ottiene un runbook di automazione di Azure |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

### <a name="azure-connected-machine-onboarding"></a>Onboarding di Azure Connected Machine

Può eseguire l'onboarding di Azure Connected Machine. [Scopri di più](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/Read | Legge qualsiasi computer Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/Write | Scrive sui computer Azure Arc |
> | [Microsoft. GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/Read | Assegnazione di configurazione guest Get. |
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

### <a name="azure-connected-machine-resource-administrator"></a>Amministratore delle risorse di Azure Connected Machine

Può leggere, scrivere, eliminare e ripetere l'onboarding di Azure Connected Machine.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/Read | Legge qualsiasi computer Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/Write | Scrive sui computer Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/Delete | Elimina un computer Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/Reconnect/Action |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/Extensions/Write | Installa o aggiorna le estensioni di Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/Read |  |
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
        "Microsoft.HybridCompute/machines/extensions/write",
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

### <a name="billing-reader"></a>Lettore per la fatturazione

Consente l'accesso in lettura ai dati di fatturazione ulteriori [informazioni](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Billing](resource-provider-operations.md#microsoftbilling)/*/Read | Lettura delle informazioni di fatturazione |
> | [Microsoft. Commerce](resource-provider-operations.md#microsoftcommerce)/*/Read |  |
> | [Microsoft. consumer](resource-provider-operations.md#microsoftconsumption)/*/Read |  |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Elenca i gruppi di gestione per l'utente autenticato. |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/Read |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

### <a name="blueprint-contributor"></a>Collaboratore di progetto

Può gestire le definizioni di progetto, ma non assegnarle. [Scopri di più](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint)/Blueprints/* | Crea e gestisce definizioni di progetto o artefatti del progetto. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

### <a name="blueprint-operator"></a>Operatore di progetto

Può assegnare i progetti pubblicati esistenti, ma non creare nuovi progetti. Si noti che funziona solo se l'assegnazione viene eseguita con un'identità gestita assegnata dall'utente. [Scopri di più](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | Crea e gestisce le assegnazioni del progetto. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Consente di visualizzare i costi e gestire la configurazione dei costi (ad esempio, Budget, esportazioni) [altre informazioni](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. consumer](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft. Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/Read |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/Configurations/Read | Ottiene le configurazioni |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/Read | Legge i suggerimenti |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Elenca i gruppi di gestione per l'utente autenticato. |
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

Consente di visualizzare i dati sui costi e la configurazione (ad esempio, Budget, esportazioni) [altre informazioni](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. consumer](resource-provider-operations.md#microsoftconsumption)/*/Read |  |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/Read |  |
> | [Microsoft. Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/Read |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/Configurations/Read | Ottiene le configurazioni |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/Read | Legge i suggerimenti |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Elenca i gruppi di gestione per l'utente autenticato. |
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

### <a name="hierarchy-settings-administrator"></a>Amministratore impostazioni gerarchia

Consente agli utenti di modificare ed eliminare le impostazioni della gerarchia

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Settings/Write | Crea o aggiorna le impostazioni gerarchia del gruppo di gestione. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Settings/Delete | Elimina le impostazioni gerarchia del gruppo di gestione. |
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
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Cluster Kubernetes-onboarding di Azure Arc

Definizione del ruolo per autorizzare qualsiasi utente/servizio a creare una risorsa connectedClusters [altre informazioni](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Crea o aggiorna una distribuzione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Ottiene i risultati dell'operazione di sottoscrizione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Ottiene l'elenco delle sottoscrizioni. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | Scrive connectedClusters |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Read | Leggi connectedClusters |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Ruolo collaboratore per applicazioni gestite

Consente la creazione di risorse di applicazioni gestite.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/* |  |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Register/Action | Esegue la registrazione in Microsoft Solutions. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
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
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/Read | Recupera un elenco di applicazioni. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/*/Action |  |
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
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
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

### <a name="managed-services-registration-assignment-delete-role"></a>Ruolo con autorizzazioni di eliminazione assegnazioni di registrazione dei servizi gestiti

Il Ruolo con autorizzazioni di eliminazione assegnazioni di registrazione dei servizi gestiti consente agli utenti del tenant di gestione di eliminare l'assegnazione della registrazione assegnata al proprio tenant. [Scopri di più](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/Read | Recupera un elenco di assegnazioni della registrazione di servizi gestiti. |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/Delete | Rimuove l'assegnazione della registrazione di servizi gestiti. |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/Read | Legge lo stato dell'operazione per la risorsa. |
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

Ruolo Collaboratore gruppo di gestione ulteriori [informazioni](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Delete | Elimina un gruppo di gestione. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Elenca i gruppi di gestione per l'utente autenticato. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/Delete | Annulla l'associazione della sottoscrizione dal gruppo di gestione. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/Write | Associa una sottoscrizione esistente al gruppo di gestione. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Write | Crea o aggiorna un gruppo di gestione. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/Read | Elenca la sottoscrizione sotto il gruppo di gestione specificato. |
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
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
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
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Elenca i gruppi di gestione per l'utente autenticato. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/Read | Elenca la sottoscrizione sotto il gruppo di gestione specificato. |
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
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
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

### <a name="new-relic-apm-account-contributor"></a>Collaboratore account New Relic APM

Consente di gestire gli account e le applicazioni di APR New Relic, ma non di accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

### <a name="policy-insights-data-writer-preview"></a>Ruolo con autorizzazioni di scrittura per i dati di Policy Insights (anteprima)

Consente l'accesso in lettura ai criteri delle risorse e l'accesso in scrittura agli eventi dei criteri dei componenti delle risorse. [Scopri di più](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/Read | Ottiene informazioni su un'assegnazione di criteri. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/PolicyDefinitions/Read | Ottiene informazioni su una definizione di criteri. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/Read | Ottenere informazioni su un'esenzione dei criteri. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/Read | Ottiene informazioni su una definizione di set di criteri. |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/Action | Controlla lo stato di conformità di un componente specifico in base ai criteri dei dati. |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/Action | Registra gli eventi dei criteri per il componente della risorsa. |
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
        "Microsoft.Authorization/policyexemptions/read",
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

Utenti con diritti di creazione/modifica di criteri delle risorse, creazione di ticket di supporto e lettura di risorse/gerarchia. [Scopri di più](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | */lettura | Legge risorse di tutti i tipi, eccetto i segreti. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Creare e gestire assegnazioni di criteri |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/PolicyDefinitions/* | Creare e gestire definizioni di criteri |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Creare e gestire set di criteri |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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
        "Microsoft.Authorization/policyexemptions/*",
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

### <a name="site-recovery-contributor"></a>Collaboratore al ripristino sito

Consente di gestire Site Recovery servizio ad eccezione della creazione dell'insieme di credenziali e dell'assegnazione di ruolo [altre informazioni](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Ottiene la definizione della rete virtuale |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/Read | GetAllocatedStamp è un'operazione interna usata dal servizio |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/Action | AllocateStamp è un'operazione interna usata dal servizio |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/Write | L'operazione Aggiorna certificato risorsa aggiorna il certificato delle credenziali della risorsa o dell'insieme di credenziali. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Consente di creare e gestire informazioni estese relative all'insieme di credenziali |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Consente di creare e gestire le identità registrate |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/* | Consente di creare o aggiornare le impostazioni degli avvisi di replica |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Legge gli eventi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/* | Consente di creare e gestire le infrastrutture di replica |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/* | Consente di creare e gestire i processi di replica |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/* | Consente di creare e gestire i criteri di replica |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/* | Consente di creare e gestire i piani di ripristino |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Consente di creare e gestire la configurazione di archiviazione dell'insieme di credenziali di Servizi di ripristino |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/TokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Consente di leggere gli avvisi per l'insieme di credenziali dei servizi di ripristino |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationOperationStatus/Read | Legge qualsiasi stato dell'operazione di replica dell'insieme di credenziali |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Consente di eseguire il failover e il failback ma non di eseguire altre operazioni di gestione Site Recovery altre [informazioni](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Ottiene la definizione della rete virtuale |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/Read | GetAllocatedStamp è un'operazione interna usata dal servizio |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/Action | AllocateStamp è un'operazione interna usata dal servizio |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/Read | Legge le impostazioni degli avvisi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Legge gli eventi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/checkConsistency/Action | Verifica la coerenza dell'infrastruttura |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/Read | Legge le infrastrutture |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/reassociateGateway/Action | Riassocia gateway |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/renewcertificate/Action | Rinnova il certificato per l'infrastruttura |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/Read | Legge le reti |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Legge i mapping di rete |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/Read | Legge i contenitori di protezione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Legge gli elementi da proteggere |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/Action | Applica punto di ripristino |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/Action | Commit del failover |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/Action | Failover pianificato |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Legge gli elementi protetti |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Legge i punti di ripristino di replica |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/Action | Ripristina replica |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/Action | Riprotegge l'elemento protetto |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/switchprotection/Action | Cambia il contenitore di protezione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/Action | Failover di test |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/Action | Pulizia del failover di test |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/Action | Failover |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/Action | Aggiorna servizio Mobility |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Legge i mapping dei contenitori di protezione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Legge i provider dei servizi di ripristino |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/Action | Aggiorna i provider |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/Read | Legge le classificazioni di archiviazione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Legge i mapping delle classificazioni di archiviazione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationvCenters/Read | Legge i vCenter |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/* | Consente di creare e gestire i processi di replica |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/Read | Legge i criteri |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/failoverCommit/Action | Piano di ripristino del commit del failover |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/plannedFailover/Action | Piano di ripristino del failover pianificato |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/Read | Legge i piani di ripristino |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/reProtect/Action | Piano di ripristino di riprotezione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/testFailover/Action | Piano di ripristino del failover di test |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/testFailoverCleanup/Action | Piano di ripristino della pulizia del failover di test |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/unplannedFailover/Action | Piano di ripristino del failover |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Consente di leggere gli avvisi per l'insieme di credenziali dei servizi di ripristino |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/TokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Restituisce l'elenco di account di archiviazione o ottiene le proprietà per l’account di archiviazione specificato. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Consente di visualizzare lo stato di Site Recovery ma non di eseguire altre operazioni di gestione altre [informazioni](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/Read | GetAllocatedStamp è un'operazione interna usata dal servizio |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | L'operazione Ottieni informazioni estese ottiene le informazioni estese di un oggetto che rappresenta la risorsa di Azure di tipo ?vault? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Ottiene gli avvisi per l'insieme di credenziali dei servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | L'operazione Ottieni insieme di credenziali ottiene un oggetto che rappresenta la risorsa di Azure di tipo 'vault' |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | L'operazione Ottieni risultati dell'operazione può essere usata per ottenere lo stato e il risultato dell'operazione inviata in modo asincrono |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | L'operazione Ottieni contenitori può essere usata per ottenere i contenitori registrati per una risorsa. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/Read | Legge le impostazioni degli avvisi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Legge gli eventi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/Read | Legge le infrastrutture |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/Read | Legge le reti |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Legge i mapping di rete |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/Read | Legge i contenitori di protezione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Legge gli elementi da proteggere |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Legge gli elementi protetti |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Legge i punti di ripristino di replica |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Legge i mapping dei contenitori di protezione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Legge i provider dei servizi di ripristino |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/Read | Legge le classificazioni di archiviazione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Legge i mapping delle classificazioni di archiviazione |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationvCenters/Read | Legge i vCenter |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/Read | Legge i processi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/Read | Legge i criteri |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/Read | Legge i piani di ripristino |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/TokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Restituisce i dettagli di utilizzo di un insieme di credenziali di Servizi di ripristino. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | L'operazione Token dell'insieme di credenziali può essere usata per ottenere il token dell'insieme di credenziali per le operazioni di back-end a livello di insieme di credenziali. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

Consente di creare e gestire le richieste di supporto ulteriori [informazioni](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

### <a name="tag-contributor"></a>Collaboratore per tag

Consente di gestire i tag sulle entità senza concedere l'accesso alle entità stesse. [Scopri di più](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Resources/Read | Ottiene le risorse del gruppo di risorse. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resources/Read | Ottiene le risorse di una sottoscrizione. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Tags/* |  |
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
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Altri


### <a name="azure-digital-twins-data-owner"></a>Proprietario di dati dei dispositivi gemelli digitali di Azure

Ruolo accesso completo per i dispositivi digitali gemelli ulteriori [informazioni](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | Lettura, eliminazione, creazione o aggiornamento di una route di eventi |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | Lettura, creazione, aggiornamento o eliminazione di un dispositivo gemello digitale |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Commands/* | Richiama qualsiasi comando in un dispositivo gemello digitale |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/* | Leggere, creare, aggiornare o eliminare qualsiasi relazione digitale gemello |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Models/* | Leggere, creare, aggiornare o eliminare qualsiasi modello |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/* | Eseguire una query su qualsiasi grafico gemello digitale |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access role for Digital Twins data-plane",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "name": "bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/eventroutes/*",
        "Microsoft.DigitalTwins/digitaltwins/*",
        "Microsoft.DigitalTwins/digitaltwins/commands/*",
        "Microsoft.DigitalTwins/digitaltwins/relationships/*",
        "Microsoft.DigitalTwins/models/*",
        "Microsoft.DigitalTwins/query/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-digital-twins-data-reader"></a>Lettore dati di dispositivi gemelli digitali di Azure

Ruolo di sola lettura per le proprietà del piano dati gemelli digitali [altre informazioni](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Read | Leggi tutti i dispositivi gemelli digitali |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/Read | Leggi qualsiasi relazione digitale gemello |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/Read | Leggi qualsiasi route dell'evento |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Models/Read | Lettura di qualsiasi modello |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/Action | Eseguire una query su qualsiasi grafico gemello digitale |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only role for Digital Twins data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "name": "d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/digitaltwins/read",
        "Microsoft.DigitalTwins/digitaltwins/relationships/read",
        "Microsoft.DigitalTwins/eventroutes/read",
        "Microsoft.DigitalTwins/models/read",
        "Microsoft.DigitalTwins/query/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="biztalk-contributor"></a>Collaboratore BizTalk

Consente di gestire i servizi BizTalk, ma non di accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | Microsoft.BizTalkServices/BizTalk/* | È in grado di creare e gestire i servizi BizTalk |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

### <a name="desktop-virtualization-user"></a>Utente di virtualizzazione desktop

Consente all'utente di usare le applicazioni in un gruppo di applicazioni. [Scopri di più](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | *nessuna* |  |
> | **NotActions** |  |
> | *nessuna* |  |
> | **DataActions** |  |
> | Microsoft. DesktopVirtualization/applicationGroups/useApplications/Action | Usare ApplicationGroup |
> | **NotDataActions** |  |
> | *nessuna* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Collaboratore raccolte di processi dell'unità di pianificazione

Consente di gestire le raccolte di processi dell'utilità di pianificazione, ma non di accedervi.

> [!div class="mx-tableFixed"]
> | Azioni | Descrizione |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Leggere i ruoli e le assegnazioni di ruoli |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Creare e gestire un avviso classico per le metriche |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Ottiene gli stati di disponibilità per tutte le risorse nell'ambito specificato |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Creare e gestire una distribuzione |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Ottiene o elenca i gruppi di risorse. |
> | [Microsoft. Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | Creare e gestire raccolte di processi |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Creare e aggiornare un ticket di supporto |
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

## <a name="next-steps"></a>Passaggi successivi

- [Associare il provider di risorse al servizio](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Ruoli personalizzati di Azure](custom-roles.md)
- [Autorizzazioni nel Centro sicurezza di Azure](../security-center/security-center-permissions.md)
