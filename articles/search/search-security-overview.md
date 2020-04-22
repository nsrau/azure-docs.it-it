---
title: Sicurezza e privacy dei dati
titleSuffix: Azure Cognitive Search
description: Ricerca cognitiva di Azure è conforme a SOC 2, HIPAA e altre certificazioni. La crittografia delle connessioni e dei dati, l'autenticazione e l'accesso alle identità tramite gli identificatori di sicurezza di utenti e gruppi nelle espressioni di filtro.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 4db9624fbc71e48fcc10ae1d9a1d700d301248a2
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759537"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Sicurezza e privacy dei dati in Ricerca cognitiva di AzureSecurity and data privacy in Azure Cognitive Search

Le funzionalità complete di sicurezza e i controlli di accesso sono integrati in Ricerca cognitiva di Azure per garantire che il contenuto privato rimanga tale. Questo articolo enumera le funzionalità di sicurezza e la conformità agli standard incorporata in Ricerca cognitiva di Azure.This article enumerates the security features and standards compliance built into Azure Cognitive Search.

L'architettura di sicurezza di Ricerca cognitiva di Azure comprende sicurezza fisica, trasmissioni crittografate, archiviazione crittografata e conformità agli standard a livello di piattaforma. Dal punto di vista operativo, Ricerca cognitiva di Azure accetta solo richieste autenticate. Facoltativamente, è possibile aggiungere controlli di accesso per utente sul contenuto tramite i filtri di sicurezza. Questo articolo tocca la sicurezza a ogni livello, ma si concentra principalmente sul modo in cui i dati e le operazioni sono protetti in Ricerca cognitiva di Azure.This article tes on security at each layer, but is primarily focused on how data and operations are secured in Azure Cognitive Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Conformità agli standard: ISO 27001, SOC 2, HIPAA

Ricerca cognitiva di Azure è certificata per gli standard seguenti, come [annunciato nel giugno 2018:](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/)

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 Type 2 compliance](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) (Conformità SOC 2 Type 2) Per il report completo, andare a [Azure - and Azure Government SOC 2 Type II Report](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports) (Report SOC 2 Type II per Azure e Azure per enti pubblici). 
+ [Health Insurance Portability and Accountability Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [Codice GxP (21 parte 11 della FDA)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS livello 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)

La conformità agli standard si applica alle funzionalità disponibili a livello generale. Le funzionalità di anteprima sono certificate quando avviene la transizione all'ambito della disponibilità generale e non devono essere usate in soluzioni con requisiti di standard restrittivi. La certificazione della conformità è documentata in [Overview of Microsoft Azure compliance](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) (Panoramica della conformità di Microsoft Azure) e nel [Trust Center](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Trasmissione e risorsa di archiviazione crittografate

La crittografia si estende per l'intera pipeline di indicizzazione: dalle connessioni, alla trasmissione e fino ai dati indicizzati archiviati in Ricerca cognitiva di Azure.Encryption extends all all entire indexing pipeline: from connections, through transmission, and down to indexed data stored in Azure Cognitive Search.

| Livello di sicurezza | Descrizione |
|----------------|-------------|
| Crittografia in transito <br>(HTTPS/SSL/TLS) | Ricerca cognitiva di Azure è in ascolto sulla porta HTTPS 443.Azure Cognitive Search listens on HTTPS port 443. In tutta la piattaforma le connessioni ai servizi di Azure vengono crittografate. <br/><br/>Tutte le interazioni di Ricerca cognitiva di Azure da client a servizio usano la crittografia SSL/TLS 1.2.All client-to-service Azure Cognitive Search interactions use SSL/TLS 1.2 encryption. Le versioni precedenti (1.0 o 1.1) non sono supportate.|
| Crittografia di dati inattivi <br>Chiavi gestite Microsoft | La crittografia è completamente incorporata nel processo di indicizzazione, senza impatti significativi sul tempo necessario per il completamento dell'indicizzazione o sulle dimensioni dell'indice. Viene applicata automaticamente a tutta l'indicizzazione, inclusi gli aggiornamenti incrementali di un indice non completamente crittografato (creato prima di gennaio 2018).<br><br>Internamente la crittografia si basa su [Crittografia del servizio di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), con la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit.<br><br> La crittografia è interna a Ricerca cognitiva di Azure, con certificati e chiavi di crittografia gestiti internamente da Microsoft e applicati universalmente. Non è possibile attivare o disattivare la crittografia, gestire o sostituire le proprie chiavi oppure visualizzare le impostazioni di crittografia nel portale o a livello di codice.<br><br>La crittografia dei dati inattivi è stata annunciata il 24 gennaio 2018 e si applica a tutti i livelli di servizio, incluso il livello gratuito, in tutte le aree geografiche. Per la crittografia completa, gli indici creati prima di tale data devono essere eliminati e ricompilati per poter applicare la crittografia. In caso contrario, vengono crittografati solo i nuovi dati aggiunti dopo il 24 gennaio.|
| Crittografia di dati inattivi <br>Chiavi gestite dal cliente | La crittografia con chiavi gestite dal cliente è ora generalmente disponibile per i servizi di ricerca creati in gennaio o dopo gennaio 2019.Encryption with customer managed keys is now generally available for search services created on january 2019. Non è supportato nei servizi gratuiti (condivisi).<br><br>Gli indici di Ricerca cognitiva di Azure e le mappe dei sinonimi possono ora essere crittografati inattivi con le chiavi gestite dai clienti in Archiviazione delle chiavi di Azure.Azure Cognitive Search indexes and synonym maps can now be encrypted at restwith with customer managed keys in Azure Key Vault. Per altre informazioni, vedere Gestire le chiavi di [crittografia in Ricerca cognitiva](search-security-manage-encryption-keys.md)di Azure.To learn more, see Manage encryption keys in Azure Cognitive Search .<br><br>Questa funzione non sostituisce la crittografia predefinita inattivi, ma piuttosto applicata in aggiunta ad essa.<br><br>L'abilitazione di questa funzionalità aumenterà le dimensioni dell'indice e rigrada le prestazioni delle query. In base alle osservazioni effettuate fino ad oggi, è possibile prevedere un aumento dei tempi delle query del 30%-60%, anche se le prestazioni effettive variano a seconda della definizione dell'indice e dei tipi di query. A causa di questo impatto sulle prestazioni, è consigliabile abilitare questa funzionalità solo per gli indici che la richiedono realmente.

## <a name="azure-wide-user-access-controls"></a>Controlli di accesso utente a livello di Azure

Diversi meccanismi di sicurezza sono disponibili a livello di Azure e quindi sono automaticamente disponibili per le risorse di Ricerca cognitiva di Azure create.

+ [Blocchi a livello di sottoscrizione o di risorsa per evitare l'eliminazione](../azure-resource-manager/management/lock-resources.md)
+ [Controllo degli accessi in base al ruolo per controllare l'accesso alle informazioni e alle operazioni amministrative](../role-based-access-control/overview.md)

Tutti i servizi di Azure supportano i controlli degli accessi in base al ruolo per impostare i livelli di accesso in modo coerente in tutti i servizi. Ad esempio, la visualizzazione di dati sensibili, ad esempio la chiave di amministrazione, è limitata ai ruoli Proprietario e Collaboratore.For example, viewing sensitive data, such as the admin key, is restricted to the Owner and Contributor roles. Tuttavia, la visualizzazione dello stato del servizio è disponibile per i membri di qualsiasi ruolo. Il controllo degli accessi in base al ruolo include i ruoli Proprietario, Collaboratore e Lettore. Per impostazione predefinita, tutti gli amministratori dei servizi sono membri del ruolo Proprietario.

<a name="service-access-and-authentication"></a>

## <a name="endpoint-access"></a>Accesso all'endpointEndpoint access

### <a name="public-access"></a>Accesso pubblico

Ricerca cognitiva di Azure eredita le garanzie di sicurezza della piattaforma Azure e fornisce la propria autenticazione basata su chiave. Una chiave API è una stringa composta da lettere e numeri generati casualmente. Il tipo di chiave (amministratore o di query) determina il livello di accesso. L'invio di una chiave valida è considerato come prova che la richiesta ha origine da un'entità attendibile. 

Esistono due livelli di accesso al servizio di ricerca, abilitati da due tipi di chiavi:

* Accesso amministratore (valido per qualsiasi operazione di lettura/scrittura sul servizio)
* Accesso alle query (valido per le operazioni di sola lettura, ad esempio le query, sulla raccolta di documenti di un indice)Query access (valid for read-only operations, such as queries, against the documents collection of an index)

*Le chiavi di amministrazione* vengono create quando viene eseguito il provisioning del servizio. Esistono due chiavi amministratore, designate come *primaria* e *secondaria* per distinguerle, ma di fatto sono intercambiabili. Ogni servizio ha due chiavi amministratore in modo che sia possibile eseguire il rollover di una senza perdere l'accesso al servizio. È possibile [rigenerare](search-security-api-keys.md#regenerate-admin-keys) periodicamente la chiave di amministrazione in base alle procedure consigliate per la sicurezza di Azure, ma non è possibile aggiungere al conteggio totale delle chiavi di amministrazione. Sono disponibili al massimo due chiavi di amministrazione per ogni servizio di ricerca.

*Le chiavi* di query vengono create in base alle esigenze e sono progettate per le applicazioni client che eseguono query. È possibile creare fino a 50 chiavi di query. Nel codice dell'applicazione, specificare l'URL di ricerca e una chiave API di query per consentire l'accesso in sola lettura alla raccolta di documenti di un indice specifico. L'endpoint, una chiave API per l'accesso di sola lettura e un indice di destinazione definiscono insieme l'ambito e il livello di accesso della connessione dall'applicazione client.

L'autenticazione è necessaria per ogni richiesta, dove ogni richiesta è costituita da una chiave obbligatoria, un'operazione e un oggetto. Se concatenati, i due livelli di autorizzazione (completa o di sola lettura) più il contesto (ad esempio un'operazione di query su un indice) sono sufficienti per fornire una sicurezza ad ampio spettro per le operazioni del servizio. Per altre informazioni sulle chiavi, vedere [Create and manage api-keys](search-security-api-keys.md) (Creare e gestire le chiavi API).

### <a name="restricted-access"></a>Accesso limitato

Quando si dispone di un servizio pubblico e si desidera limitare l'utilizzo del servizio è possibile utilizzare la regola di restrizione IP nella versione dell'API REST di gestione: 2020-03-13, [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule). IpRule consente di limitare l'accesso al servizio identificando gli indirizzi IP, singolarmente o in un intervallo, a cui si desidera concedere l'accesso al servizio di ricerca. 

### <a name="private-access"></a>Accesso privato

[Gli endpoint privati](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) per Ricerca cognitiva di Azure consentono a un client in una rete virtuale di accedere in modo sicuro ai dati in un indice di ricerca tramite un [collegamento privato.](https://docs.microsoft.com/azure/private-link/private-link-overview) L'endpoint privato usa un indirizzo IP dello spazio di indirizzi della rete virtuale per il servizio di ricerca. Il traffico di rete tra il client e il servizio di ricerca attraversa la rete virtuale e un collegamento privato nella rete backbone Microsoft, eliminando l'esposizione da Internet pubblico.

[La rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) di Azure consente comunicazioni sicure tra le risorse, sia con la rete locale che con Internet. 

## <a name="index-access"></a>Accesso all'indice

In Ricerca cognitiva di Azure un singolo indice non è un oggetto a protezione diretta. L'accesso a un indice è invece determinato a livello di servizio (accesso in lettura o scrittura), con il contesto di un'operazione.

Per l'accesso utente finale, è possibile strutturare le richieste di query per la connessione tramite una chiave di query, che imposta qualsiasi richiesta come di sola lettura, e includere l'indice specifico usato dall'app. In una richiesta di query non è previsto il join degli indici o l'accesso a più indici simultaneamente, quindi tutte le richiesta specificano come destinazione un solo indice per definizione. Di conseguenza, la costruzione della richiesta della query (una chiave più un singolo indice di destinazione) definisce di per sé il limite di sicurezza.

L'accesso amministratore e sviluppatore agli indici è indifferenziato: entrambi richiedono l'accesso in scrittura per creare, eliminare e aggiornare gli oggetti gestiti dal servizio. Chiunque abbia una chiave amministratore per il servizio può leggere, modificare o eliminare qualsiasi indice nello stesso servizio. Per proteggersi dall'eliminazione accidentale o intenzionale degli indici, il controllo del codice sorgente interno per gli asset del codice è la risoluzione che consente di invertire l'eliminazione o la modifica indesiderata di un indice. Ricerca cognitiva di Azure ha il failover all'interno del cluster per garantire la disponibilità, ma non archivia o esegue il codice proprietario usato per creare o caricare gli indici.

Le soluzioni multi-tenancy che richiedono limiti di sicurezza a livello di indice includono in genere un livello intermedio, che i clienti usano per gestire l'isolamento degli indici. Per altre informazioni sul caso d'uso multi-tenant, vedere [Progettare modelli per applicazioni SaaS multi-tenant e Ricerca cognitiva](search-modeling-multitenant-saas-applications.md)di Azure.For more information about the multitenant use case, see Design patterns for multitenant SaaS applications and Azure Cognitive Search .

## <a name="authentication"></a>Authentication

### <a name="admin-access"></a>Accesso amministratore

[L'accesso basato sui](https://docs.microsoft.com/azure/role-based-access-control/overview) ruoli determina se si ha accesso ai controlli sul servizio e sul relativo contenuto. Se si è un proprietario o un collaboratore in un servizio ricerca cognitiva di Azure, è possibile usare il portale o il modulo **Az.Search** di PowerShell per creare, aggiornare o eliminare oggetti nel servizio. È anche possibile usare [l'API REST Gestione ricerca cognitiva di Azure.You](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)can also use the Azure Cognitive Search Management REST API .

### <a name="user-access"></a>Accesso utente

Per impostazione predefinita, l'accesso utente a un indice è determinato dalla chiave di accesso per la richiesta della query. La maggior parte degli sviluppatori crea e assegna [*chiavi di query*](search-security-api-keys.md) per le richieste di ricerca lato client. Una chiave di query concede l'accesso in lettura a tutti i contenuti all'interno dell'indice.

Se è necessario il controllo per utente granulare sul contenuto, è possibile creare filtri di sicurezza sulle query, che restituiscono i documenti associati a una determinata identità di sicurezza. Invece dei ruoli e delle assegnazioni di ruolo predefiniti, il controllo di accesso basato sull'identità viene implementato come *filtro* che taglia i risultati della ricerca di documenti e contenuto in base alle identità. La tabella seguente descrive due approcci per limitare i risultati della ricerca di contenuto non autorizzato.

| Approccio | Descrizione |
|----------|-------------|
|[Limitazione per motivi di sicurezza in base ai filtri delle identità](search-security-trimming-for-azure-search.md)  | Documenta il flusso di lavoro di base per implementare il controllo di accesso dell'identità utente. Illustra l'aggiunta di ID di sicurezza a un indice e quindi illustra l'applicazione di filtri a tale campo per limitare i risultati di contenuto non consentito. |
|[Limitazione per motivi di sicurezza in base alle identità di Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Questo articolo è un approfondimento dell'articolo precedente e contiene la procedura per recuperare le identità da Azure Active Directory (AAD), uno dei [servizi gratuiti](https://azure.microsoft.com/free/) della piattaforma cloud Azure. |

## <a name="table-permissioned-operations"></a>Tabella: operazioni con autorizzazione

Nella tabella seguente vengono riepilogate le operazioni consentite in Ricerca cognitiva di Azure e quale chiave sblocca l'accesso a una determinata operazione.

| Operazione | Autorizzazioni |
|-----------|-------------------------|
| Creare un servizio | Titolare della sottoscrizione di Azure|
| Ridimensionare un servizio | Chiave di amministrazione, proprietario del controllo degli accessi in base al ruolo o collaboratore per la risorsaAdmin key, RBAC Owner, or Contributor on the resource  |
| Eliminare un servizio | Chiave di amministrazione, proprietario del controllo degli accessi in base al ruolo o collaboratore per la risorsaAdmin key, RBAC Owner, or Contributor on the resource |
| Creare, modificare, eliminare oggetti nel servizio: <br>indici e parti componenti (inclusi definizioni degli analizzatori, profili di punteggio, opzioni CORS), indicizzatori, origini dati, sinonimi, strumenti suggerimenti. | Chiave di amministrazione, proprietario del controllo degli accessi in base al ruolo o collaboratore per la risorsaAdmin key, RBAC Owner, or Contributor on the resource  |
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
+ [Controllo degli accessi basato sulle identità con i filtri di Ricerca cognitiva di AzureIdentity-based access control using Azure Cognitive Search filters](search-security-trimming-for-azure-search.md)
+ [Controllo degli accessi basato sull'identità di Active Directory con i filtri di Ricerca cognitiva di AzureActive Directory identity-based access control using Azure Cognitive Search filters](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search](search-filters.md)