---
title: Sicurezza e privacy dei dati - Ricerca di Azure
description: Ricerca di Azure è conforme alle certificazioni SOC 2, HIPAA e altre. Connessione, crittografia dei dati, autenticazione e accesso all'identità tramite gli ID di sicurezza di utenti e gruppi nei filtri di Ricerca di Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 11b2fb5a246dfa8f5b1295a11cc57de36120898e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283422"
---
# <a name="security-and-data-privacy-in-azure-search"></a>Sicurezza e privacy dei dati in Ricerca di Azure

Funzionalità di sicurezza complete e controlli di accesso sono integrati in Ricerca di Azure per continuare a garantire la riservatezza del contenuto privato. Questo articolo enumera le funzionalità di sicurezza e la conformità agli standard integrati in Ricerca di Azure.

L'architettura di sicurezza di Ricerca di Azure include sicurezza fisica, trasmissioni crittografate, spazio di archiviazione crittografato e conformità agli standard a livello di piattaforma. Dal punto di vista operativo, Ricerca di Azure accetta solo richieste autenticate. Facoltativamente, è possibile aggiungere controlli di accesso per utente sul contenuto tramite i filtri di sicurezza. Questo articolo descrive la sicurezza a ogni livello e in particolare illustra come vengono protetti i dati e le operazioni in Ricerca di Azure.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Conformità agli standard: ISO 27001, SOC 2, HIPAA

Ricerca di Azure è certificata per gli standard seguenti, come [annunciato a giugno 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 Type 2 compliance](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) (Conformità SOC 2 Type 2) Per il report completo, andare a [Azure - and Azure Government SOC 2 Type II Report](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports) (Report SOC 2 Type II per Azure e Azure per enti pubblici). 
+ [Health Insurance Portability and Accountability Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [Codice GxP (21 parte 11 della FDA)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS livello 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Dati non riservati (DLM) IRAP in Australia](https://asd.gov.au/infosec/irap/certified_clouds.htm)

La conformità agli standard si applica alle funzionalità disponibili a livello generale. Le funzionalità di anteprima sono certificate quando avviene la transizione all'ambito della disponibilità generale e non devono essere usate in soluzioni con requisiti di standard restrittivi. La certificazione della conformità è documentata in [Overview of Microsoft Azure compliance](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) (Panoramica della conformità di Microsoft Azure) e nel [Trust Center](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Trasmissione e risorsa di archiviazione crittografate

La crittografia viene applicata all'intera pipeline di indicizzazione: dalle connessioni alla trasmissione fino ai dati indicizzati archiviati in Ricerca di Azure.

| Livello di sicurezza | DESCRIZIONE |
|----------------|-------------|
| Crittografia in transito <br>(HTTPS/SSL/TLS) | Ricerca di Azure è in ascolto sulla porta HTTPS 443. In tutta la piattaforma le connessioni ai servizi di Azure vengono crittografate. <br/><br/>Tutte le interazioni Ricerca di Azure da client a servizio supportano SSL/TLS 1.2.  Assicurarsi di usare TLSv1.2 per le connessioni SSL al servizio.|
| Crittografia di dati inattivi | La crittografia è completamente incorporata nel processo di indicizzazione, senza impatti significativi sul tempo necessario per il completamento dell'indicizzazione o sulle dimensioni dell'indice. Viene applicata automaticamente a tutta l'indicizzazione, inclusi gli aggiornamenti incrementali di un indice non completamente crittografato (creato prima di gennaio 2018).<br><br>Internamente la crittografia si basa su [Crittografia del servizio di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), con la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit.|

La crittografia è interna in Ricerca di Azure, con certificati e chiavi di crittografia gestiti internamente da Microsoft e applicati universalmente. Non è possibile attivare o disattivare la crittografia, gestire o sostituire le proprie chiavi oppure visualizzare le impostazioni di crittografia nel portale o a livello di codice. 

La crittografia dei dati inattivi è stata annunciata il 24 gennaio 2018 e si applica a tutti i livelli di servizio, inclusi i servizi condivisi (gratuiti), in tutte le aree. Per la crittografia completa, gli indici creati prima di tale data devono essere eliminati e ricompilati per poter applicare la crittografia. In caso contrario, vengono crittografati solo i nuovi dati aggiunti dopo il 24 gennaio.

## <a name="azure-wide-user-access-controls"></a>Controlli di accesso utente a livello di Azure

In Azure sono disponibili diversi meccanismi di sicurezza che sono automaticamente disponibili anche per le risorse di Ricerca di Azure create.

+ [Blocchi a livello di sottoscrizione o di risorsa per evitare l'eliminazione](../azure-resource-manager/resource-group-lock-resources.md)
+ [Controllo degli accessi in base al ruolo per controllare l'accesso alle informazioni e alle operazioni amministrative](../role-based-access-control/overview.md)

Tutti i servizi di Azure supportano i controlli degli accessi in base al ruolo per impostare i livelli di accesso in modo coerente in tutti i servizi. Ad esempio, la visualizzazione di dati sensibili, come la chiave amministratore, è consentita solo ai ruoli Proprietario e Collaboratore, mentre la visualizzazione dello stato del servizio è disponibile per i membri di qualsiasi ruolo. Il controllo degli accessi in base al ruolo include i ruoli Proprietario, Collaboratore e Lettore. Per impostazione predefinita, tutti gli amministratori dei servizi sono membri del ruolo Proprietario.

<a name="service-access-and-authentication"></a>

## <a name="service-access-and-authentication"></a>Accesso al servizio e autenticazione

Ricerca di Azure non solo eredita le misure di sicurezza della piattaforma Azure, ma fornisce anche la propria autenticazione basata su chiavi. Una chiave API è una stringa composta da lettere e numeri generati casualmente. Il tipo di chiave (amministratore o di query) determina il livello di accesso. L'invio di una chiave valida è considerato come prova che la richiesta ha origine da un'entità attendibile. 

Esistono due livelli di accesso al servizio di ricerca, abilitati da due tipi di chiavi:

* Accesso amministratore (valido per qualsiasi operazione di lettura/scrittura sul servizio)
* Accesso di query (valida per le operazioni di sola lettura, ad esempio le query, nella raccolta documenti di un indice)

Le *chiavi amministratore* vengono create quando viene effettuato il provisioning del servizio. Esistono due chiavi amministratore, designate come *primaria* e *secondaria* per distinguerle, ma di fatto sono intercambiabili. Ogni servizio ha due chiavi amministratore in modo che sia possibile eseguire il rollover di una senza perdere l'accesso al servizio. È possibile [regenerate admin key](search-security-api-keys.md#regenerate-admin-keys) periodicamente per sicurezza di Azure le procedure consigliate, ma è possibile aggiungere per il conteggio di chiavi amministratore totale. Sono presenti un massimo di due chiavi amministratore per ogni servizio di ricerca.

*Chiavi di query* vengono creati secondo necessità e sono progettate per applicazioni client che inviano le query. È possibile creare fino a 50 chiavi di query. Nel codice dell'applicazione, specificare l'URL di ricerca e una chiave api di query per consentire l'accesso di sola lettura alla raccolta documenti di un indice specifico. L'endpoint, una chiave API per l'accesso di sola lettura e un indice di destinazione definiscono insieme l'ambito e il livello di accesso della connessione dall'applicazione client.

L'autenticazione è necessaria per ogni richiesta, dove ogni richiesta è costituita da una chiave obbligatoria, un'operazione e un oggetto. Se concatenati, i due livelli di autorizzazione (completa o di sola lettura) più il contesto (ad esempio un'operazione di query su un indice) sono sufficienti per fornire una sicurezza ad ampio spettro per le operazioni del servizio. Per altre informazioni sulle chiavi, vedere [Create and manage api-keys](search-security-api-keys.md) (Creare e gestire le chiavi API).

## <a name="index-access"></a>Accesso all'indice

In Ricerca di Azure un singolo indice non è un oggetto a protezione diretta. L'accesso a un indice è invece determinato a livello di servizio (accesso in lettura o scrittura), con il contesto di un'operazione.

Per l'accesso utente finale, è possibile strutturare le richieste di query per la connessione tramite una chiave di query, che imposta qualsiasi richiesta come di sola lettura, e includere l'indice specifico usato dall'app. In una richiesta di query non è previsto il join degli indici o l'accesso a più indici simultaneamente, quindi tutte le richiesta specificano come destinazione un solo indice per definizione. Di conseguenza, la costruzione della richiesta della query (una chiave più un singolo indice di destinazione) definisce di per sé il limite di sicurezza.

L'accesso amministratore e sviluppatore agli indici è indifferenziato: entrambi richiedono l'accesso in scrittura per creare, eliminare e aggiornare gli oggetti gestiti dal servizio. Chiunque abbia una chiave amministratore per il servizio può leggere, modificare o eliminare qualsiasi indice nello stesso servizio. Per proteggersi dall'eliminazione accidentale o intenzionale degli indici, il controllo del codice sorgente interno per gli asset del codice è la risoluzione che consente di invertire l'eliminazione o la modifica indesiderata di un indice. In Ricerca di Azure è disponibile il failover nel cluster per assicurare la disponibilità, ma il codice proprietario usato per creare o caricare gli indici non viene archiviato o eseguito.

Le soluzioni multi-tenancy che richiedono limiti di sicurezza a livello di indice includono in genere un livello intermedio, che i clienti usano per gestire l'isolamento degli indici. Per altre informazioni sul caso d'uso multi-tenant, vedere [Modelli di progettazione per le applicazioni SaaS multi-tenant e Ricerca di Azure](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access"></a>Accesso come amministratore

[Accesso basato sui ruoli (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) determina se si hanno accesso ai controlli tramite il servizio e il relativo contenuto. Se si è proprietario o collaboratore per un servizio di ricerca di Azure, è possibile usare il portale o PowerShell **Az.Search** modulo da creare, aggiornare o eliminare oggetti nel servizio. È anche possibile usare la [API REST gestione ricerca di Azure](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

## <a name="user-access"></a>Accesso utente

Per impostazione predefinita, l'accesso utente a un indice è determinato dalla chiave di accesso per la richiesta della query. La maggior parte degli sviluppatori crea e assegna [*chiavi di query*](search-security-api-keys.md) per le richieste di ricerca lato client. Una chiave di query concede l'accesso in lettura a tutti i contenuti all'interno dell'indice.

Se è necessario il controllo per utente granulare sul contenuto, è possibile creare filtri di sicurezza sulle query, che restituiscono i documenti associati a una determinata identità di sicurezza. Invece di ruoli e assegnazioni di ruolo predefiniti, il controllo di accesso basato sull'identità viene implementato come un *filtro* che limita i risultati della ricerca di documenti e contenuto in base alle identità. La tabella seguente descrive due approcci per limitare i risultati della ricerca di contenuto non autorizzato.

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
| Gestire le chiavi di query |  Chiave amministratore, proprietario di Controllo degli accessi in base al ruolo o Collaboratore nella sottoscrizione.  |

## <a name="physical-security"></a>Sicurezza fisica

I data center Microsoft garantiscono la sicurezza fisica leader di settore e sono conformi a un portafoglio completo di standard e normative. Per altre informazioni, vedere la pagina [Data center globali](https://www.microsoft.com/cloud-platform/global-datacenters) o guardare un breve video sulla sicurezza dei data center.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Vedere anche 

+ [Introduzione a .NET (illustra l'uso di una chiave amministratore per creare un indice)](search-create-index-dotnet.md)
+ [Introduzione a REST (illustra l'uso di una chiave amministratore per creare un indice)](search-create-index-rest-api.md)
+ [Controllo degli accessi in base all'identità tramite i filtri di Ricerca di Azure](search-security-trimming-for-azure-search.md)
+ [Controllo degli accessi in base all'identità di Active Directory tramite i filtri di Ricerca di Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtri in Ricerca di Azure](search-filters.md)