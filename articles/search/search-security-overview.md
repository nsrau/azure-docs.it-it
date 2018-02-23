---
title: Proteggere i dati e le operazioni in Ricerca di Azure | Microsoft Docs
description: "La sicurezza in Ricerca di Azure si basa sulla conformità SOC 2, sulla crittografia, sull'autenticazione e sull'accesso all'identità tramite gli ID di sicurezza di utenti e gruppi nei filtri di Ricerca di Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: cgronlun
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/19/2018
ms.author: heidist
ms.openlocfilehash: c3aa4883e33b1f3494f8502fe7f8b12f7d64a72f
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="security-and-controlled-access-in-azure-search"></a>Sicurezza e accesso controllato in Ricerca di Azure

Ricerca di Azure è [conforme a SOC 2](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports), con un'architettura di sicurezza completa che include sicurezza fisica, trasmissioni crittografate, risorsa di archiviazione crittografata e misure di sicurezza software a livello di piattaforma. Dal punto di vista operativo, Ricerca di Azure accetta solo richieste autenticate. Facoltativamente, è possibile aggiungere controlli di accesso per utente sul contenuto. Questo articolo descrive la sicurezza a ogni livello e in particolare illustra come vengono protetti i dati e le operazioni in Ricerca di Azure.

![Diagramma a blocchi dei livelli di sicurezza](media/search-security-overview/azsearch-security-diagram.png)

## <a name="physical-security"></a>Sicurezza fisica

I data center Microsoft garantiscono la sicurezza fisica leader di settore e sono conformi a un portafoglio completo di standard e normative. Per altre informazioni, vedere la pagina [Data center globali](https://www.microsoft.com/cloud-platform/global-datacenters) o guardare un breve video sulla sicurezza dei data center.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]

## <a name="encrypted-transmission-and-storage"></a>Trasmissione e risorsa di archiviazione crittografate

La crittografia viene applicata all'intera pipeline di indicizzazione: dalle connessioni alla trasmissione fino ai dati indicizzati archiviati in Ricerca di Azure.

| Livello di sicurezza | DESCRIZIONE |
|----------------|-------------|
| Crittografia in transito | Ricerca di Azure è in ascolto sulla porta HTTPS 443. In tutta la piattaforma le connessioni ai servizi di Azure vengono crittografate. |
| Crittografia di dati inattivi | La crittografia è completamente incorporata nel processo di indicizzazione, senza impatti significativi sul tempo necessario per il completamento dell'indicizzazione o sulle dimensioni dell'indice. Viene applicata automaticamente a tutta l'indicizzazione, inclusi gli aggiornamenti incrementali di un indice non completamente crittografato (creato prima di gennaio 2018).<br><br>Internamente la crittografia si basa su [Crittografia del servizio di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), con la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit.|
| [Conformità SOC 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) | Tutti i servizi di ricerca sono completamente conformi a SOC 2 di AICPA, in tutti i data center che forniscono Ricerca di Azure. Per vedere il report completo, andare a [Azure - and Azure Government SOC 2 Type II Report](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports) (Report SOC 2 di tipo II per Azure e Azure per enti pubblici). |

La crittografia è interna in Ricerca di Azure, con certificati e chiavi di crittografia gestiti internamente da Microsoft e applicati universalmente. Non è possibile attivare o disattivare la crittografia, gestire o sostituire le proprie chiavi oppure visualizzare le impostazioni di crittografia nel portale o a livello di codice. 

La crittografia dei dati inattivi è stata annunciata il 24 gennaio 2018 e si applica a tutti i livelli di servizio, inclusi i servizi condivisi (gratuiti), in tutte le aree. Per la crittografia completa, gli indici creati prima di tale data devono essere eliminati e ricompilati per poter applicare la crittografia. In caso contrario, vengono crittografati solo i nuovi dati aggiunti dopo il 24 gennaio.

## <a name="azure-wide-logical-security"></a>Sicurezza logica a livello di Azure

In Azure Stack sono disponibili diversi meccanismi di sicurezza che sono automaticamente disponibili anche per le risorse di Ricerca di Azure create.

+ [Blocchi a livello di sottoscrizione o di risorsa per evitare l'eliminazione](../azure-resource-manager/resource-group-lock-resources.md)
+ [Controllo degli accessi in base al ruolo per controllare l'accesso alle informazioni e alle operazioni amministrative](../active-directory/role-based-access-control-what-is.md)

Tutti i servizi di Azure supportano i controlli degli accessi in base al ruolo per impostare i livelli di accesso in modo coerente in tutti i servizi. Ad esempio, la visualizzazione di dati sensibili, come la chiave amministratore, è consentita solo ai ruoli Proprietario e Collaboratore, mentre la visualizzazione dello stato del servizio è disponibile per i membri di qualsiasi ruolo. Il controllo degli accessi in base al ruolo include i ruoli Proprietario, Collaboratore e Lettore. Per impostazione predefinita, tutti gli amministratori dei servizi sono membri del ruolo Proprietario.

## <a name="service-access-and-authentication"></a>Accesso al servizio e autenticazione

Ricerca di Azure non solo eredita le misure di sicurezza della piattaforma Azure, ma fornisce anche la propria autenticazione basata su chiavi. Il tipo di chiave (amministratore o di query) determina il livello di accesso. L'invio di una chiave valida è considerato come prova che la richiesta ha origine da un'entità attendibile. 

L'autenticazione è necessaria per ogni richiesta, dove ogni richiesta è costituita da una chiave obbligatoria, un'operazione e un oggetto. Se concatenati, i due livelli di autorizzazione (completa o di sola lettura) più il contesto sono sufficienti per fornire una sicurezza ad ampio spettro per le operazioni del servizio. 

|Chiave|DESCRIZIONE|Limiti|  
|---------|-----------------|------------|  
|Admin|Concede diritti completi a tutte le operazioni, inclusa la possibilità di gestire il servizio, creare ed eliminare indici, indicizzatori e origini dati.<br /><br /> Due **api-key** amministratore, chiamate chiavi *primaria* e *secondaria* nel portale, vengono generate quando il servizio viene creato e possono essere generate di nuovo singolarmente su richiesta. Avere due chiavi consente di eseguire il rollover di una chiave mentre si usa la seconda per l'accesso continuo al servizio.<br /><br /> Le chiavi amministratore vengono specificate solo nelle intestazioni delle richieste HTTP. Non è possibile inserire un elemento api-key amministratore in un URL.|Un massimo di 2 per servizio|  
|Query|Concede l'accesso in sola lettura agli indici e ai documenti e viene in genere distribuita alle applicazioni client che inviano richieste di ricerca.<br /><br /> Le chiavi di query vengono create su richiesta. È possibile crearle manualmente nel portale o a livello di codice tramite l'[API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Le chiavi di query possono essere specificate nell'intestazione di una richiesta HTTP per un'operazione di ricerca o suggerimento. In alternativa, è possibile passare una chiave di query come parametro in un URL. A seconda di come l'applicazione client formula la richiesta, può risultare più semplice passare la chiave come parametro di query:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01&api-key=A8DA81E03F809FE166ADDB183E9ED84D`|50 per servizio|  

 Non esiste alcuna distinzione visiva tra una chiave amministratore o una chiave di query. Entrambe le chiavi sono stringhe composte da 32 caratteri alfanumerici generati in modo casuale. Se si è persa traccia del tipo di chiave specificato nell'applicazione, è possibile [controllare i valori delle chiavi nel portale](https://portal.azure.com) o usare l'[API REST](https://docs.microsoft.com/rest/api/searchmanagement/) per restituire il valore e il tipo di chiave.  

> [!NOTE]  
>  Passare dati sensibili, ad esempio un elemento `api-key`, nell'URI della richiesta è considerato una procedura di sicurezza non ottimale. Per questo motivo, Ricerca di Azure accetta solo una chiave di query come `api-key` nella stringa di query ed è consigliabile evitare di eseguire questa operazione, a meno che i contenuti dell'indice non debbano essere disponibili pubblicamente. Come regola generale, è consigliabile passare l'elemento `api-key` come intestazione della richiesta.  

### <a name="how-to-find-the-access-keys-for-your-service"></a>Come trovare le chiavi di accesso per il servizio

È possibile ottenere le chiavi di accesso nel portale o tramite l'[API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/). Per altre informazioni, vedere [Gestire le chiavi](search-manage.md#manage-api-keys).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Elencare i [servizi di ricerca](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) per la sottoscrizione.
3. Selezionare il servizio nella pagina dei servizi e trovare **Impostazioni** >**Chiavi** per visualizzare le chiavi amministratore e di query.

![Pagina del portale, sezione Impostazioni, Chiavi](media/search-security-overview/settings-keys.png)

## <a name="index-access"></a>Accesso all'indice

In Ricerca di Azure un singolo indice non è un oggetto a protezione diretta. L'accesso a un indice è invece determinato a livello di servizio (accesso in lettura o scrittura), con il contesto di un'operazione.

Nel caso dell'accesso utente finale, è possibile strutturare le richieste di query nell'applicazione per la connessione tramite una chiave di query, che imposta qualsiasi richiesta come di sola lettura, e includere l'indice specifico usato dall'app. In una richiesta di query non è previsto il join degli indici o l'accesso a più indici simultaneamente, quindi tutte le richiesta specificano come destinazione un solo indice per definizione. Di conseguenza, la struttura della richiesta della query (una chiave più un singolo indice di destinazione) definisce di per sé il limite di sicurezza.

L'accesso amministratore e sviluppatore agli indici è indifferenziato: entrambi richiedono l'accesso in scrittura per creare, eliminare e aggiornare gli oggetti gestiti dal servizio. Chiunque abbia una chiave amministratore per il servizio può leggere, modificare o eliminare qualsiasi indice nello stesso servizio. Per proteggersi dall'eliminazione accidentale o intenzionale degli indici, il controllo del codice sorgente interno per gli asset del codice è la risoluzione che consente di invertire l'eliminazione o la modifica indesiderata di un indice. In Ricerca di Azure è disponibile il failover nel cluster per assicurare la disponibilità, ma il codice proprietario usato per creare o caricare gli indici non viene archiviato o eseguito.

Le soluzioni multi-tenancy che richiedono limiti di sicurezza a livello di indice includono in genere un livello intermedio, che i clienti usano per gestire l'isolamento degli indici. Per altre informazioni sul caso d'uso multi-tenant, vedere [Modelli di progettazione per le applicazioni SaaS multi-tenant e Ricerca di Azure](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access-from-client-apps"></a>Accesso amministratore dalle app client

L'API REST gestione di Ricerca di Azure è un'estensione di Gestione risorse di Azure e ne condivide le dipendenze. Di conseguenza, Active Directory è un prerequisito per l'amministrazione dei servizi di Ricerca di Azure. Tutte le richieste amministrative dal codice client devono essere autenticate tramite Azure Active Directory prima che la richiesta raggiunga Resource Manager.

Le richieste di dati all'endpoint del servizio Ricerca di Azure, ad esempio la creazione di un indice (API REST del servizio Ricerca di Azure) o la ricerca nei documenti (API REST del servizio Ricerca di Azure), usano un elemento api-key nell'intestazione della richiesta.

Se il codice dell'applicazione gestisce le operazioni di amministrazione del servizio, oltre alle operazioni sui dati negli indici di ricerca o nei documenti, implementare due approcci all'autenticazione nel codice: la chiave di accesso nativa di Ricerca di Azure e la metodologia di autenticazione di Active Directory richiesta da Resource Manager. 

Per informazioni sulla struttura di una richiesta in Ricerca di Azure, vedere [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/) (REST del servizio Ricerca di Azure). Per altre informazioni sui requisiti di autenticazione per Resource Manager, vedere [Usare l'API di autenticazione di Resource Manager per accedere alle sottoscrizioni](../azure-resource-manager/resource-manager-api-authentication.md).

## <a name="user-access-to-index-content"></a>Accesso utente al contenuto dell'indice

L'accesso per utente ai contenuti di un indice viene implementato tramite filtri di sicurezza sulle query, che restituiscono i documenti associati a una determinata identità di sicurezza. Invece di ruoli e assegnazioni di ruolo predefiniti, il controllo di accesso basato sull'identità viene implementato come un filtro che limita i risultati della ricerca di documenti e contenuto in base alle identità. La tabella seguente descrive due approcci per limitare i risultati della ricerca di contenuto non autorizzato.

| Approccio | DESCRIZIONE |
|----------|-------------|
|[Limitazione per motivi di sicurezza in base ai filtri delle identità](search-security-trimming-for-azure-search.md)  | Documenta il flusso di lavoro di base per implementare il controllo di accesso dell'identità utente. Illustra l'aggiunta di ID di sicurezza a un indice e quindi illustra l'applicazione di filtri a tale campo per limitare i risultati di contenuto non consentito. |
|[Limitazione per motivi di sicurezza in base alle identità di Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Questo articolo è un approfondimento dell'articolo precedente e contiene la procedura per recuperare le identità da Azure Active Directory (AAD), uno dei [servizi gratuiti](https://azure.microsoft.com/free/) della piattaforma cloud Azure. |

## <a name="table-permissioned-operations"></a>Tabella: operazioni con autorizzazione

La tabella seguente riepiloga le operazioni consentite in Ricerca di Azure e la chiave che sblocca l'accesso a una determinata operazione.

| Operazione | Autorizzazioni |
|-----------|-------------------------|
| Creare un servizio | Titolare della sottoscrizione di Azure|
| Ridimensionare un servizio | Chiave amministratore, proprietario di Controllo degli accessi in base al ruolo o Collaboratore nella sottoscrizione  |
| Eliminare un servizio | Chiave amministratore, proprietario di Controllo degli accessi in base al ruolo o Collaboratore nella sottoscrizione |
| Creare, modificare, eliminare oggetti nel servizio: <br>indici e parti componenti (inclusi definizioni degli analizzatori, profili di punteggio, opzioni CORS), indicizzatori, origini dati, sinonimi, strumenti suggerimenti. | Chiave amministratore, proprietario di Controllo degli accessi in base al ruolo o Collaboratore nella sottoscrizione  |
| Eseguire una query su un indice | Chiave amministratore o di query (Controllo degli accessi in base al ruolo non applicabile) |
| Eseguire una query sulle informazioni sul sistema, ad esempio per restituire statistiche, conteggi ed elenchi di oggetti. | Chiave amministratore, Controllo degli accessi in base al ruolo nella risorsa (Proprietario, Collaboratore, Lettore) |
| Gestire le chiavi amministratore | Chiave amministratore, proprietario di Controllo degli accessi in base al ruolo o Collaboratore nella sottoscrizione. |
| Gestire le chiavi di query |  Chiave amministratore, proprietario di Controllo degli accessi in base al ruolo o Collaboratore nella sottoscrizione. Il lettore di Controllo degli accessi in base al ruolo può visualizzare le chiavi di query. |


## <a name="see-also"></a>Vedere anche 

+ [Introduzione a .NET (illustra l'uso di una chiave amministratore per creare un indice)](search-create-index-dotnet.md)
+ [Introduzione a REST (illustra l'uso di una chiave amministratore per creare un indice)](search-create-index-rest-api.md)
+ [Controllo degli accessi in base all'identità tramite i filtri di Ricerca di Azure](search-security-trimming-for-azure-search.md)
+ [Controllo degli accessi in base all'identità di Active Directory tramite i filtri di Ricerca di Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtri in Ricerca di Azure](search-filters.md)