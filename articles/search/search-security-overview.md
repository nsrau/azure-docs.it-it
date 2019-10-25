---
title: Sicurezza e privacy dei dati
titleSuffix: Azure Cognitive Search
description: Azure ricerca cognitiva è conforme a SOC 2, HIPAA e ad altre certificazioni. Connessione e crittografia dei dati, autenticazione e accesso alle identità tramite gli identificatori di sicurezza di utenti e gruppi nelle espressioni di filtro.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2e509535473fa50fd3150965e1513e056ead18a6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794340"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Sicurezza e privacy dei dati in Azure ricerca cognitiva

Le funzionalità di sicurezza complete e i controlli di accesso sono incorporati in Azure ricerca cognitiva per garantire che il contenuto privato rimanga in questo modo. Questo articolo enumera le funzionalità di sicurezza e la conformità agli standard incorporati in Azure ricerca cognitiva.

L'architettura di sicurezza di Azure ricerca cognitiva si estende sulla sicurezza fisica, le trasmissioni crittografate, l'archiviazione crittografata e la conformità agli standard a livello di piattaforma. Dal ricerca cognitiva operativo Azure accetta solo le richieste autenticate. Facoltativamente, è possibile aggiungere controlli di accesso per utente sul contenuto tramite i filtri di sicurezza. Questo articolo illustra la sicurezza a ogni livello, ma si concentra principalmente sul modo in cui i dati e le operazioni vengono protetti in Azure ricerca cognitiva.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Conformità agli standard: ISO 27001, SOC 2, HIPAA

Azure ricerca cognitiva è certificato per gli standard seguenti, come [annunciato nel 2018 giugno](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 Type 2 compliance](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) (Conformità SOC 2 Type 2) Per il report completo, andare a [Azure - and Azure Government SOC 2 Type II Report](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports) (Report SOC 2 Type II per Azure e Azure per enti pubblici). 
+ [Health Insurance Portability and Accountability Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [Codice GxP (21 parte 11 della FDA)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS livello 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Dati non riservati (DLM) IRAP in Australia](https://asd.gov.au/infosec/irap/certified_clouds.htm)

La conformità agli standard si applica alle funzionalità disponibili a livello generale. Le funzionalità di anteprima sono certificate quando avviene la transizione all'ambito della disponibilità generale e non devono essere usate in soluzioni con requisiti di standard restrittivi. La certificazione della conformità è documentata in [Overview of Microsoft Azure compliance](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) (Panoramica della conformità di Microsoft Azure) e nel [Trust Center](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Trasmissione e risorsa di archiviazione crittografate

La crittografia si estende nell'intera pipeline di indicizzazione: dalle connessioni, tramite trasmissione e fino ai dati indicizzati archiviati in Azure ricerca cognitiva.

| Livello di sicurezza | Description |
|----------------|-------------|
| Crittografia in transito <br>(HTTPS/SSL/TLS) | Azure ricerca cognitiva è in ascolto sulla porta HTTPS 443. In tutta la piattaforma le connessioni ai servizi di Azure vengono crittografate. <br/><br/>Tutte le interazioni ricerca cognitiva di Azure da client a servizio sono compatibili con SSL/TLS 1,2.  Assicurarsi di usare TLSv1.2 per le connessioni SSL al servizio.|
| Crittografia di dati inattivi <br>Chiavi gestite da Microsoft | La crittografia è completamente incorporata nel processo di indicizzazione, senza impatti significativi sul tempo necessario per il completamento dell'indicizzazione o sulle dimensioni dell'indice. Viene applicata automaticamente a tutta l'indicizzazione, inclusi gli aggiornamenti incrementali di un indice non completamente crittografato (creato prima di gennaio 2018).<br><br>Internamente la crittografia si basa su [Crittografia del servizio di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), con la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit.<br><br> La crittografia è interna all'ricerca cognitiva di Azure, con certificati e chiavi di crittografia gestite internamente da Microsoft e applicati universalmente. Non è possibile attivare o disattivare la crittografia, gestire o sostituire le proprie chiavi oppure visualizzare le impostazioni di crittografia nel portale o a livello di codice.<br><br>La crittografia dei servizi inattivi è stata annunciata il 24 gennaio 2018 e si applica a tutti i livelli di servizio, incluso il livello gratuito, in tutte le aree. Per la crittografia completa, gli indici creati prima di tale data devono essere eliminati e ricompilati per poter applicare la crittografia. In caso contrario, vengono crittografati solo i nuovi dati aggiunti dopo il 24 gennaio.|
| Crittografia di dati inattivi <br>Chiavi gestite dal cliente | La crittografia con chiavi gestite dal cliente è una funzionalità di **Anteprima** non disponibile per i servizi gratuiti. Per i servizi a pagamento, è disponibile solo per i servizi di ricerca creati il o dopo il 2019 gennaio, usando la versione più recente dell'API di anteprima (API-Version = 2019-05-06-Preview).<br><br>Gli indici di ricerca cognitiva e le mappe sinonimi di Azure possono ora essere crittografati a riposo con chiavi gestite da chiavi personalizzate in Azure Key Vault. Per altre informazioni, vedere [gestire le chiavi di crittografia in Azure ricerca cognitiva](search-security-manage-encryption-keys.md).<br>Questa funzionalità non sostituisce la crittografia predefinita inattiva, ma è invece applicata in aggiunta.<br>L'abilitazione di questa funzionalità aumenterà le dimensioni degli indici e diminuirà le prestazioni delle query. In base alle osservazioni date, è possibile prevedere un aumento del 30%-60% nei tempi di esecuzione delle query, anche se le prestazioni effettive variano a seconda della definizione dell'indice e dei tipi di query. A causa di questo effetto sulle prestazioni, si consiglia di abilitare questa funzionalità solo negli indici che lo richiedono effettivamente.

## <a name="azure-wide-user-access-controls"></a>Controlli di accesso utente a livello di Azure

Diversi meccanismi di sicurezza sono disponibili in Azure e pertanto sono automaticamente disponibili per le risorse di Azure ricerca cognitiva create.

+ [Blocchi a livello di sottoscrizione o di risorsa per evitare l'eliminazione](../azure-resource-manager/resource-group-lock-resources.md)
+ [Controllo degli accessi in base al ruolo per controllare l'accesso alle informazioni e alle operazioni amministrative](../role-based-access-control/overview.md)

Tutti i servizi di Azure supportano i controlli degli accessi in base al ruolo per impostare i livelli di accesso in modo coerente in tutti i servizi. Ad esempio, la visualizzazione di dati sensibili, come la chiave amministratore, è consentita solo ai ruoli Proprietario e Collaboratore, mentre la visualizzazione dello stato del servizio è disponibile per i membri di qualsiasi ruolo. Il controllo degli accessi in base al ruolo include i ruoli Proprietario, Collaboratore e Lettore. Per impostazione predefinita, tutti gli amministratori dei servizi sono membri del ruolo Proprietario.

<a name="service-access-and-authentication"></a>

## <a name="service-access-and-authentication"></a>Accesso al servizio e autenticazione

Sebbene ricerca cognitiva di Azure erediti le misure di sicurezza della piattaforma Azure, fornisce anche la propria autenticazione basata su chiavi. Una chiave API è una stringa composta da lettere e numeri generati casualmente. Il tipo di chiave (amministratore o di query) determina il livello di accesso. L'invio di una chiave valida è considerato come prova che la richiesta ha origine da un'entità attendibile. 

Esistono due livelli di accesso al servizio di ricerca, abilitati da due tipi di chiavi:

* Accesso amministratore (valido per qualsiasi operazione di lettura/scrittura sul servizio)
* Accesso alle query (valido per le operazioni di sola lettura, ad esempio query, sulla raccolta Documents di un indice)

Le *chiavi amministratore* vengono create quando viene effettuato il provisioning del servizio. Esistono due chiavi amministratore, designate come *primaria* e *secondaria* per distinguerle, ma di fatto sono intercambiabili. Ogni servizio ha due chiavi amministratore in modo che sia possibile eseguire il rollover di una senza perdere l'accesso al servizio. È possibile [rigenerare la chiave di amministrazione](search-security-api-keys.md#regenerate-admin-keys) periodicamente per le procedure consigliate per la sicurezza di Azure, ma non è possibile aggiungere il numero totale di chiavi amministrative. Sono disponibili al massimo due chiavi amministrative per servizio di ricerca.

Le *chiavi di query* vengono create in base alle necessità e sono progettate per le applicazioni client che inviano query. È possibile creare fino a 50 chiavi di query. Nel codice dell'applicazione è possibile specificare l'URL di ricerca e una chiave API di query per consentire l'accesso in sola lettura alla raccolta Documents di un indice specifico. L'endpoint, una chiave API per l'accesso di sola lettura e un indice di destinazione definiscono insieme l'ambito e il livello di accesso della connessione dall'applicazione client.

L'autenticazione è necessaria per ogni richiesta, dove ogni richiesta è costituita da una chiave obbligatoria, un'operazione e un oggetto. Se concatenati, i due livelli di autorizzazione (completa o di sola lettura) più il contesto (ad esempio un'operazione di query su un indice) sono sufficienti per fornire una sicurezza ad ampio spettro per le operazioni del servizio. Per altre informazioni sulle chiavi, vedere [Create and manage api-keys](search-security-api-keys.md) (Creare e gestire le chiavi API).

## <a name="index-access"></a>Accesso all'indice

In ricerca cognitiva di Azure, un singolo indice non è un oggetto a protezione diretta. L'accesso a un indice è invece determinato a livello di servizio (accesso in lettura o scrittura), con il contesto di un'operazione.

Per l'accesso utente finale, è possibile strutturare le richieste di query per la connessione tramite una chiave di query, che imposta qualsiasi richiesta come di sola lettura, e includere l'indice specifico usato dall'app. In una richiesta di query non è previsto il join degli indici o l'accesso a più indici simultaneamente, quindi tutte le richiesta specificano come destinazione un solo indice per definizione. Di conseguenza, la costruzione della richiesta della query (una chiave più un singolo indice di destinazione) definisce di per sé il limite di sicurezza.

L'accesso amministratore e sviluppatore agli indici è indifferenziato: entrambi richiedono l'accesso in scrittura per creare, eliminare e aggiornare gli oggetti gestiti dal servizio. Chiunque abbia una chiave amministratore per il servizio può leggere, modificare o eliminare qualsiasi indice nello stesso servizio. Per proteggersi dall'eliminazione accidentale o intenzionale degli indici, il controllo del codice sorgente interno per gli asset del codice è la risoluzione che consente di invertire l'eliminazione o la modifica indesiderata di un indice. Azure ricerca cognitiva dispone di failover all'interno del cluster per garantire la disponibilità, ma non archivia o esegue il codice proprietario usato per creare o caricare gli indici.

Le soluzioni multi-tenancy che richiedono limiti di sicurezza a livello di indice includono in genere un livello intermedio, che i clienti usano per gestire l'isolamento degli indici. Per altre informazioni sul caso d'uso multi-tenant, vedere [modelli di progettazione per applicazioni SaaS multi-tenant e ricerca cognitiva di Azure](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access"></a>Accesso amministratore

[L'accesso in base al ruolo (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) determina se è possibile accedere ai controlli sul servizio e il relativo contenuto. Se si è un proprietario o un collaboratore in un servizio ricerca cognitiva di Azure, è possibile usare il portale o il modulo di PowerShell **AZ. search** per creare, aggiornare o eliminare oggetti nel servizio. È anche possibile usare l' [API REST di gestione di Azure ricerca cognitiva](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

## <a name="user-access"></a>Accesso utente

Per impostazione predefinita, l'accesso utente a un indice è determinato dalla chiave di accesso per la richiesta della query. La maggior parte degli sviluppatori crea e assegna [*chiavi di query*](search-security-api-keys.md) per le richieste di ricerca lato client. Una chiave di query concede l'accesso in lettura a tutti i contenuti all'interno dell'indice.

Se è necessario il controllo per utente granulare sul contenuto, è possibile creare filtri di sicurezza sulle query, che restituiscono i documenti associati a una determinata identità di sicurezza. Invece di ruoli e assegnazioni di ruolo predefiniti, il controllo di accesso basato sull'identità viene implementato come un *filtro* che limita i risultati della ricerca di documenti e contenuto in base alle identità. La tabella seguente descrive due approcci per limitare i risultati della ricerca di contenuto non autorizzato.

| Approccio | Description |
|----------|-------------|
|[Limitazione per motivi di sicurezza in base ai filtri delle identità](search-security-trimming-for-azure-search.md)  | Documenta il flusso di lavoro di base per implementare il controllo di accesso dell'identità utente. Illustra l'aggiunta di ID di sicurezza a un indice e quindi illustra l'applicazione di filtri a tale campo per limitare i risultati di contenuto non consentito. |
|[Limitazione per motivi di sicurezza in base alle identità di Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Questo articolo è un approfondimento dell'articolo precedente e contiene la procedura per recuperare le identità da Azure Active Directory (AAD), uno dei [servizi gratuiti](https://azure.microsoft.com/free/) della piattaforma cloud Azure. |

## <a name="table-permissioned-operations"></a>Tabella: operazioni con autorizzazione

Nella tabella seguente sono riepilogate le operazioni consentite in Azure ricerca cognitiva e la chiave che sblocca l'accesso a una determinata operazione.

| Operazione | autorizzazioni |
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


## <a name="see-also"></a>Vedi anche

+ [Introduzione a .NET (illustra l'uso di una chiave amministratore per creare un indice)](search-create-index-dotnet.md)
+ [Introduzione a REST (illustra l'uso di una chiave amministratore per creare un indice)](search-create-index-rest-api.md)
+ [Controllo degli accessi in base all'identità con i filtri ricerca cognitiva di Azure](search-security-trimming-for-azure-search.md)
+ [Active Directory il controllo degli accessi in base all'identità usando i filtri ricerca cognitiva di Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtri in ricerca cognitiva di Azure](search-filters.md)