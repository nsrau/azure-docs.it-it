---
title: Proteggere i dati e le operazioni di ricerca di Azure | Documenti Microsoft
description: "Sicurezza di ricerca di Azure si basa su conformità SOC 2, la crittografia, autenticazione e identità di accesso utente e gli identificatori di protezione di gruppo nei filtri di ricerca di Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/14/2017
ms.author: heidist
ms.openlocfilehash: 23616c70a5fd336b743f5acfad2601a6c3e23fc4
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="data-security-and-controlled-access-to-azure-search-operations"></a>Protezione dei dati e l'accesso controllato alle operazioni di ricerca di Azure

Ricerca di Azure è [SOC 2 conforme](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports), con un sicurezza fisica spanning sicurezza globale architettura, la crittografia dei dati, un archivio crittografato e misure di sicurezza a livello di piattaforma software. Punto di vista operativo, ricerca di Azure accetta solo le richieste autenticate. Facoltativamente, è possibile aggiungere i controlli di accesso per utente sul contenuto. In questo articolo interessa sicurezza a ogni livello, ma è incentrato principalmente su come vengono protetti i dati e operazioni di ricerca di Azure.

![Diagramma a blocchi di livelli di sicurezza](media/search-security-overview/azsearch-security-diagram.png)

Mentre ricerca di Azure eredita la protezione e le misure di sicurezza della piattaforma Azure, il meccanismo principale utilizzato dal servizio stesso è l'autenticazione basata su chiave, in cui il tipo di chiave determina il livello di accesso. Una chiave è una chiave amministratore o una chiave di query per l'accesso in sola lettura.

L'accesso al servizio si basa su una sezione di autorizzazioni concesse dalla chiave (completa o sola lettura), oltre a un contesto che definisce un ambito di operazioni. Ogni richiesta è costituito da una chiave obbligatoria, un'operazione e un oggetto. Quando concatenate, i due livelli di autorizzazione e il contesto è sufficiente per fornire una protezione full spettro nelle operazioni del servizio. 

## <a name="physical-security"></a>Sicurezza fisica

Data center Microsoft forniscono la sicurezza fisica leader del settore e sono conformi a una gamma completa di standard e normative. Per ulteriori informazioni, visualizzare il [data center globali di](https://www.microsoft.com/cloud-platform/global-datacenters) pagina o breve video nel data center sicurezza.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]

## <a name="encrypted-transmission-and-storage"></a>Archiviazione e la trasmissione crittografata

Ricerca di Azure è in ascolto sulla porta HTTPS 443. Nella piattaforma, le connessioni a servizi di Azure sono crittografate. 

Nello spazio di archiviazione back-end utilizzato per gli indici e altri costrutti, ricerca di Azure sfrutta le funzionalità di crittografia di tali piattaforme. Completa [AICPA SOC 2 conformità](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) è disponibile per tutti i servizi (nuovi ed esistenti), di ricerca in tutti i data center offerta ricerca di Azure. Per esaminare il report completo, passare a [- Azure e Azure per enti pubblici SOC 2 tipo II Report](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports).

La crittografia è trasparente, con le chiavi di crittografia gestita internamente e applicati universalmente. Non è possibile disattivarla per servizi di ricerca specifico o gli indici, né gestire direttamente le chiavi, né fornire la propria. 

## <a name="azure-wide-logical-security"></a>Sicurezza di Azure a livello logico

Diversi meccanismi di sicurezza sono disponibili nello Stack di Azure e pertanto è automaticamente disponibile per le risorse di ricerca di Azure che crei.

+ [Blocchi a livello di risorse per evitare l'eliminazione o di sottoscrizione](../azure-resource-manager/resource-group-lock-resources.md)
+ [In base al ruolo accesso controllo (RBAC) per controllare l'accesso alle informazioni e le operazioni amministrative](../active-directory/role-based-access-control-what-is.md)

Tutti i servizi di Azure supportano i controlli di accesso basato sui ruoli (RBAC) per l'impostazione di livelli di accesso in modo coerente in tutti i servizi. Ad esempio, la visualizzazione di dati sensibili, ad esempio la chiave di amministrazione, è limitata ai ruoli proprietario e collaboratore, mentre la visualizzazione stato del servizio è disponibile per i membri di qualsiasi ruolo. RBAC fornisce ruoli proprietario, collaboratore e lettore. Per impostazione predefinita, tutti gli amministratori dei servizi sono membri del ruolo Proprietario.

## <a name="service-authentication"></a>Autenticazione del servizio

Ricerca di Azure fornisce la propria metodologia di autenticazione. L'autenticazione si verifica a ogni richiesta ed è basata su una chiave di accesso che determina l'ambito delle operazioni. Una chiave di accesso valido è considerata una prova la richiesta proviene da un'entità attendibile. 

L'autenticazione al servizio esiste a due livelli: completo dei diritti, query di sola lettura. Il tipo di chiave determina il livello di accesso.

|Chiave|DESCRIZIONE|Limiti|  
|---------|-----------------|------------|  
|Admin|Concede diritti completi per tutte le operazioni, inclusa la possibilità di gestire il servizio, creare ed eliminare **indici**, **indicizzatori**, e **origini dati**.<br /><br /> Amministrazione due **chiavi api**, noto come *primario* e *secondario* keys nel portale, vengono generati quando il servizio viene creato e può essere rigenerato singolarmente su richiesta . Presenza di due chiavi consente il rollover di una chiave quando si utilizza la seconda chiave per l'accesso continuo al servizio.<br /><br /> Le chiavi amministratore vengono specificate solo nelle intestazioni di richiesta HTTP. Non è possibile posizionare un amministratore **chiave api** in un URL.|Numero massimo di 2 per ogni servizio|  
|Query|Concede l'accesso in sola lettura a indici e documenti e in genere vengono distribuiti alle applicazioni client che inviano richieste di ricerca.<br /><br /> Le chiavi di query vengono create su richiesta. È possibile crearli manualmente nel portale o a livello di programmazione tramite le [API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Le chiavi di query possono essere specificate in un'intestazione di richiesta HTTP per operazione di ricerca, suggerimento o ricerca. In alternativa, è possibile passare una chiave di query come un parametro in un URL. A seconda di come l'applicazione client formula la richiesta, può risultare più semplice passare la chiave come un parametro di query:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01&api-key=A8DA81E03F809FE166ADDB183E9ED84D`|50 per servizio|  

 In modo visivo, non viene fatta distinzione tra chiave amministratore o una chiave di query. Entrambe le chiavi sono stringhe composte da 32 in modo casuale generato caratteri alfanumerici. Se si perde traccia di quale tipo di chiave specificato nell'applicazione, è possibile [controllare i valori di chiave nel portale di](https://portal.azure.com) o utilizzare il [API REST](https://docs.microsoft.com/rest/api/searchmanagement/) per restituire il valore e tipo di chiave.  

> [!NOTE]  
>  Viene considerato un livello di protezione insufficienti per passare dati sensibili, ad esempio un `api-key` nell'URI della richiesta. Per questo motivo, ricerca di Azure accetta solo una chiave di query come un `api-key` nella query stringa ed è consigliabile evitare questa operazione, a meno che il contenuto dell'indice deve essere disponibile pubblicamente. Come regola generale, è consigliabile passare il `api-key` come intestazione della richiesta.  

### <a name="how-to-find-the-access-keys-for-your-service"></a>Come individuare le chiavi di accesso per il servizio

È possibile ottenere le chiavi di accesso nel portale o tramite il [API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/). Per ulteriori informazioni, vedere [la gestione delle chiavi](search-manage.md#manage-api-keys).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Elenco di [una ricerca in servizi](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) per la sottoscrizione.
3. Selezionare il servizio e nella pagina del servizio, cercare **impostazioni** >**chiavi** per visualizzare le chiavi di query e di amministrazione.

![Sezione di pagina del portale, le impostazioni, chiavi](media/search-security-overview/settings-keys.png)

## <a name="index-access"></a>Accesso di indice

In ricerca di Azure, un singolo indice non è un oggetto a protezione diretta. Accesso a un indice viene invece determinata a livello di servizio (lettura o accesso in scrittura), insieme al contesto di un'operazione.

Nel caso di accesso degli utenti finali, è possibile strutturare le richieste di query dell'applicazione per connettersi utilizzando una chiave di query, che rende le richieste di sola lettura e include l'indice specifico usato dall'app. In una richiesta di query, non è presente alcun concetto di unione degli indici o l'accesso a più indici contemporaneamente in modo da un singolo indice di destinazione tutte le richieste per definizione. Di conseguenza, la struttura della richiesta di query stessa (una chiave e un indice con singola destinazione) definisce il limite di sicurezza.

Accesso di amministratore e lo sviluppatore agli indici è indifferenziato: sia necessario l'accesso in scrittura per creare, eliminare e aggiornare gli oggetti gestiti dal servizio. Chiunque disponga di una chiave di amministratore per il servizio può leggere, modificare o eliminare qualsiasi indice nello stesso servizio. Per la protezione contro accidentale o intenzionali l'eliminazione di indici, il controllo del codice sorgente interne per le risorse di codice è la soluzione per l'inversione di un indice indesiderato eliminazione o alla modifica. Ricerca di Azure è il failover all'interno del cluster per assicurare la disponibilità, ma non archivia o eseguire il codice proprietario usato per creare o caricare gli indici.

Per le soluzioni multi-tenancy che richiedono i limiti di sicurezza a livello di indice, tali soluzioni includono in genere un livello intermedio, i clienti che utilizzano per gestire l'isolamento di indice. Per ulteriori informazioni sul caso di utilizzo di multi-tenant, vedere [Progettazione modelli per applicazioni SaaS multi-tenant e ricerca di Azure](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access-from-client-apps"></a>Accesso di amministratore da applicazioni client

L'API REST gestione di Ricerca di Azure è un'estensione di Gestione risorse di Azure e ne condivide le dipendenze. Di conseguenza, Active Directory è un prerequisito per l'amministrazione dei servizi di Ricerca di Azure. Tutte le richieste amministrative dal codice client devono essere autenticate tramite Azure Active Directory prima che la richiesta raggiunga Gestione risorse.

Le richieste di dati contro l'endpoint del servizio di ricerca di Azure, ad esempio Create Index (Azure API REST di ricerca) o eseguire ricerche nei documenti (Azure API REST di ricerca), utilizzare una chiave api nell'intestazione della richiesta.

Se il codice dell'applicazione gestisce operazioni di amministrazione del servizio, nonché le operazioni di dati nei documenti o gli indici di ricerca, è possibile implementare due approcci di autenticazione nel codice: la chiave di accesso nativa di ricerca di Azure e l'autenticazione di Active Directory metodologia richiesto dal gestore delle risorse. 

Per informazioni su come strutturare una richiesta di ricerca di Azure, vedere [REST del servizio ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/). Per ulteriori informazioni sui requisiti di autenticazione per la gestione delle risorse, vedere [usare Gestione risorse l'API di autenticazione per le sottoscrizioni di accesso](../azure-resource-manager/resource-manager-api-authentication.md).

## <a name="user-access-to-index-content"></a>Accesso al contenuto dell'indice

Per ogni utente l'accesso al contenuto di un indice viene implementata tramite i filtri di sicurezza nella query, la restituzione di documenti è associata a un'identità di sicurezza specificato. Invece di ruoli predefiniti e le assegnazioni di ruolo, il controllo di accesso basate sulle identità viene implementato come un filtro che elimina i risultati dei documenti e contenuto in base alle identità di ricerca. Nella tabella seguente vengono descritti due approcci diversi per i risultati della ricerca la rimozione del contenuto non autorizzato.

| Approccio | DESCRIZIONE |
|----------|-------------|
|[Per motivi di sicurezza in base ai filtri di identità](search-security-trimming-for-azure-search.md)  | Documenta il flusso di lavoro di base per l'implementazione del controllo di accesso di identità utente. Vengono illustrati gli identificatori di sicurezza aggiunta a un indice e quindi illustra filtro tale campo per tagliare i risultati del contenuto non consentito. |
|[Per motivi di sicurezza in base alle identità di Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | In questo articolo si espande l'articolo precedente, fornendo i passaggi per il recupero di identità da Azure Active Directory (AAD), uno del [libero servizi](https://azure.microsoft.com/free/) nella piattaforma cloud di Azure. |

## <a name="table-permissioned-operations"></a>Tabella: Operazioni state impostate le autorizzazioni

Nella tabella seguente sono riepilogate le operazioni consentite in ricerca di Azure e la chiave Sblocca l'accesso a una particolare operazione.

| Operazione | Autorizzazioni |
|-----------|-------------------------|
| Creare un servizio | Titolare della sottoscrizione di Azure|
| Scalabilità di un servizio | Chiave di amministrazione, RBAC proprietario o collaboratore della risorsa  |
| Eliminare un servizio | Chiave di amministrazione, RBAC proprietario o collaboratore della risorsa |
| Creare, modificare, eliminare gli oggetti del servizio: <br>Gli indici e le parti componenti (inclusi definizioni di Analizzatore, i profili di punteggio, le opzioni CORS), gli indicizzatori, origini dati, sinonimi, suggerimento. | Chiave di amministrazione, RBAC proprietario o collaboratore della risorsa  |
| Un indice di query | Chiave di query o di amministrazione (RBAC non applicabile) |
| Eseguire una query informazioni di sistema, ad esempio la restituzione delle statistiche, conteggi e gli elenchi di oggetti. | Chiave di amministrazione, RBAC sulla risorsa (proprietario, collaboratore, Reader) |
| Gestire le chiavi amministratore | Chiave di amministrazione, RBAC proprietario o collaboratore della risorsa. |
| Gestisci chiavi di query |  Chiave di amministrazione, RBAC proprietario o collaboratore della risorsa. Lettore RBAC è possibile visualizzare le chiavi di query. |


## <a name="see-also"></a>Vedere anche 

+ [Ottenere avviato .NET (viene illustrato l'utilizzo di una chiave amministratore per creare un indice)](search-create-index-dotnet.md)
+ [Ottenere avviato REST (viene illustrato l'utilizzo di una chiave amministratore per creare un indice)](search-create-index-rest-api.md)
+ [Controllo di accesso basate sulle identità utilizzando i filtri di ricerca di Azure](search-security-trimming-for-azure-search.md)
+ [Controllo accesso basato su identità di Active Directory utilizzando i filtri di ricerca di Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtri in Ricerca di Azure](search-filters.md)