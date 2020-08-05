---
title: Panoramica della sicurezza
titleSuffix: Azure Cognitive Search
description: Azure ricerca cognitiva è conforme a SOC 2, HIPAA e ad altre certificazioni. Connessione e crittografia dei dati, autenticazione e accesso alle identità tramite gli identificatori di sicurezza di utenti e gruppi nelle espressioni di filtro.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: 9e4181956d81ddbe0a385987689a8cb0248ac535
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553955"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Sicurezza in Azure ricerca cognitiva-Panoramica

Questo articolo descrive le principali funzionalità di sicurezza di Azure ricerca cognitiva che possono proteggere il contenuto e le operazioni.

+ A livello di archiviazione, la crittografia dei dati inattivi è incorporata per tutti i contenuti gestiti dal servizio salvati su disco, inclusi indici, mappe sinonimi e definizioni di indicizzatori, origini dati e skillsets. Azure ricerca cognitiva supporta anche l'aggiunta di chiavi gestite dal cliente (CMK) per la crittografia supplementare del contenuto indicizzato. Per i servizi creati dopo il 1 2020 agosto, la crittografia CMK estende ai dati nei dischi temporanei per la crittografia completa del contenuto indicizzato.

+ La sicurezza in ingresso protegge l'endpoint del servizio di ricerca a livelli di sicurezza crescenti: dalle chiavi API della richiesta, alle regole in ingresso nel firewall, agli endpoint privati che proteggono completamente il servizio dalla rete Internet pubblica.

+ La sicurezza in uscita si applica agli indicizzatori che effettuano il pull del contenuto da origini esterne. Per le richieste in uscita, configurare un'identità gestita per eseguire la ricerca in un servizio attendibile quando si accede ai dati da archiviazione di Azure, SQL di Azure, Cosmos DB o altre origini dati di Azure. Un'identità gestita sostituisce le credenziali o le chiavi di accesso per la connessione. La sicurezza in uscita non è illustrata in questo articolo. Per altre informazioni su questa funzionalità, vedere [connettersi a un'origine dati usando un'identità gestita](search-howto-managed-identities-data-sources.md).

Guarda questo video veloce per una panoramica dell'architettura della sicurezza e di ogni categoria di funzionalità.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

<a name="encryption"></a>

## <a name="encrypted-transmissions-and-storage"></a>Trasmissioni e archiviazione crittografate

In Azure ricerca cognitiva la crittografia inizia con connessioni e trasmissioni e si estende ai contenuti archiviati su disco. Per i servizi di ricerca sulla rete Internet pubblica, Azure ricerca cognitiva è in ascolto sulla porta HTTPS 443. Tutte le connessioni da client a servizio utilizzano la crittografia TLS 1,2. Le versioni precedenti (1,0 o 1,1) non sono supportate.

Per i dati gestiti internamente dal servizio di ricerca, nella tabella seguente vengono descritti i [modelli di crittografia dei dati](../security/fundamentals/encryption-atrest.md#data-encryption-models). Alcune funzionalità, ad esempio archivio informazioni, arricchimento incrementale e indicizzazione basata su indicizzatore, lettura o scrittura in strutture di dati di altri servizi di Azure. Questi servizi hanno i propri livelli di supporto della crittografia separati da Azure ricerca cognitiva.

| Modello | Chiavi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Requisiti&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Restrizioni | Si applica a |
|------------------|-------|-------------|--------------|------------|
| crittografia lato server | Chiavi gestite da Microsoft | Nessuno (predefinito) | Nessuno, disponibile in tutti i livelli, in tutte le aree, per il contenuto creato dopo il 24 2018 gennaio. | Contenuto (mappe indici e sinonimi) e definizioni (indicizzatori, origini dati, skillsets) |
| crittografia lato server | chiavi gestite dal cliente | Insieme di credenziali chiave di Azure | Disponibile nei livelli fatturabili, in tutte le aree, per il contenuto creato dopo il 2019 gennaio. | Contenuto (mappe indici e sinonimi) sui dischi dati |
| crittografia doppia lato server | chiavi gestite dal cliente | Insieme di credenziali chiave di Azure | Disponibile su livelli fatturabili, in aree selezionate, nei servizi di ricerca dopo il 1 2020 agosto. | Contenuto (mappe indici e sinonimi) su dischi dati e dischi temporanei |

### <a name="service-managed-keys"></a>Chiavi gestite dal servizio

La crittografia gestita dal servizio è un'operazione interna di Microsoft, basata su [crittografia del servizio di archiviazione di Azure](../storage/common/storage-service-encryption.md), che usa la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit. Si verifica automaticamente in tutte le indicizzazione, inclusi gli aggiornamenti incrementali degli indici che non sono completamente crittografati (creati prima del gennaio 2018).

### <a name="customer-managed-keys-cmk"></a>Chiavi gestite dal cliente (CMK)

Le chiavi gestite dal cliente richiedono un servizio fatturabile aggiuntivo, Azure Key Vault, che può trovarsi in un'area diversa, ma nella stessa sottoscrizione di Azure ricerca cognitiva. L'abilitazione della crittografia CMK aumenterà le dimensioni degli indici e diminuirà le prestazioni di query. In base alle osservazioni date, è possibile prevedere un aumento del 30%-60% nei tempi di esecuzione delle query, anche se le prestazioni effettive variano a seconda della definizione dell'indice e dei tipi di query. A causa di questo effetto sulle prestazioni, si consiglia di abilitare questa funzionalità solo negli indici che lo richiedono effettivamente. Per altre informazioni, vedere [configurare le chiavi di crittografia gestite dal cliente in Azure ricerca cognitiva](search-security-manage-encryption-keys.md).

<a name="double-encryption"></a>

### <a name="double-encryption"></a>Crittografia doppia 

In Azure ricerca cognitiva la crittografia doppia è un'estensione di CMK. Si tratta di una crittografia a due riduzioni (una volta per CMK e di nuovo da chiavi gestite dal servizio) e di un ambito completo, che include l'archiviazione a lungo termine scritta in un disco dati e l'archiviazione a breve termine scritta nei dischi temporanei. La differenza tra CMK prima del 1 2020 agosto e dopo e che rende CMK una doppia funzionalità di crittografia in Azure ricerca cognitiva è la crittografia aggiuntiva dei dati inattivi sui dischi temporanei.

La crittografia doppia è attualmente disponibile nei nuovi servizi creati in queste aree dopo il 1 ° agosto:

+ Stati Uniti occidentali 2
+ Stati Uniti orientali
+ Stati Uniti centro-meridionali
+ US Gov Virginia
+ US Gov Arizona

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>Sicurezza in ingresso ed Endpoint Protection

Le funzionalità di sicurezza in ingresso proteggono l'endpoint del servizio di ricerca aumentando i livelli di sicurezza e complessità. Per prima cosa, per tutte le richieste è necessaria una chiave API per l'accesso autenticato. In secondo luogo, è possibile impostare facoltativamente le regole del firewall che limitano l'accesso a indirizzi IP specifici. Per la protezione avanzata, una terza opzione consiste nell'abilitare il collegamento privato di Azure per schermare l'endpoint di servizio da tutto il traffico Internet.

### <a name="public-access-using-api-keys"></a>Accesso pubblico tramite chiavi API

Per impostazione predefinita, è possibile accedere a un servizio di ricerca tramite il cloud pubblico, usando l'autenticazione basata su chiavi per l'amministratore o l'accesso a query all'endpoint del servizio di ricerca. Una chiave API è una stringa composta da lettere e numeri generati casualmente. Il tipo di chiave (amministratore o di query) determina il livello di accesso. L'invio di una chiave valida è considerato come prova che la richiesta ha origine da un'entità attendibile. 

Sono disponibili due livelli di accesso al servizio di ricerca, abilitati dalle chiavi API seguenti:

+ Chiave amministratore (consente l'accesso in lettura/scrittura per le operazioni di [creazione-lettura-aggiornamento-eliminazione](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) nel servizio di ricerca)

+ Chiave di query (consente l'accesso in sola lettura alla raccolta Documents di un indice)

Le *chiavi amministratore* vengono create quando viene eseguito il provisioning del servizio. Esistono due chiavi amministratore, designate come *primaria* e *secondaria* per distinguerle, ma di fatto sono intercambiabili. Ogni servizio ha due chiavi amministratore in modo che sia possibile eseguire il rollover di una senza perdere l'accesso al servizio. È possibile [rigenerare la chiave di amministrazione](search-security-api-keys.md#regenerate-admin-keys) periodicamente per le procedure consigliate per la sicurezza di Azure, ma non è possibile aggiungere il numero totale di chiavi amministrative. Sono disponibili al massimo due chiavi amministrative per servizio di ricerca.

Le *chiavi di query* vengono create in base alle necessità e sono progettate per le applicazioni client che inviano query. È possibile creare fino a 50 chiavi di query. Nel codice dell'applicazione è possibile specificare l'URL di ricerca e una chiave API di query per consentire l'accesso in sola lettura alla raccolta Documents di un indice specifico. L'endpoint, una chiave API per l'accesso di sola lettura e un indice di destinazione definiscono insieme l'ambito e il livello di accesso della connessione dall'applicazione client.

L'autenticazione è necessaria per ogni richiesta, dove ogni richiesta è costituita da una chiave obbligatoria, un'operazione e un oggetto. Se concatenati, i due livelli di autorizzazione (completa o di sola lettura) più il contesto (ad esempio un'operazione di query su un indice) sono sufficienti per fornire una sicurezza ad ampio spettro per le operazioni del servizio. Per altre informazioni sulle chiavi, vedere [Create and manage api-keys](search-security-api-keys.md) (Creare e gestire le chiavi API).

### <a name="ip-restricted-access"></a>Accesso con restrizioni IP

Per controllare ulteriormente l'accesso al servizio di ricerca, è possibile creare regole del firewall in ingresso che consentano l'accesso a un indirizzo IP specifico o a un intervallo di indirizzi IP. Tutte le connessioni client devono essere effettuate tramite un indirizzo IP consentito o la connessione viene negata.

Per [configurare l'accesso in ingresso](service-configure-firewall.md), è possibile usare il portale. 

In alternativa, è possibile usare le API REST di gestione. L'API versione 2020-03-13, con il parametro [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule) , consente di limitare l'accesso al servizio identificando gli indirizzi IP, singolarmente o in un intervallo, a cui si vuole concedere l'accesso al servizio di ricerca. 

### <a name="private-endpoint-no-internet-traffic"></a>Endpoint privato (nessun traffico Internet)

Un [endpoint privato](../private-link/private-endpoint-overview.md) per ricerca cognitiva di Azure consente a un client in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di accedere in modo sicuro ai dati in un indice di ricerca tramite un [collegamento privato](../private-link/private-link-overview.md). 

L'endpoint privato usa un indirizzo IP dello spazio di indirizzi della rete virtuale per le connessioni al servizio di ricerca. Il traffico di rete tra il client e il servizio di ricerca attraversa la rete virtuale e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica. Un VNET consente la comunicazione sicura tra le risorse, con la rete locale e Internet. 

Anche se questa soluzione è la più sicura, l'uso di servizi aggiuntivi è un costo aggiunto, quindi è necessario assicurarsi di avere una chiara comprensione dei vantaggi prima di immergersi in. Per ulteriori informazioni sui costi, vedere la [pagina](https://azure.microsoft.com/pricing/details/private-link/)relativa ai prezzi. Per ulteriori informazioni sull'interazione tra questi componenti, guardare il video nella parte superiore di questo articolo. Il code coverage dell'opzione di endpoint privato inizia da 5:48 nel video. Per istruzioni su come configurare l'endpoint, vedere [creare un endpoint privato per Azure ricerca cognitiva](service-create-private-endpoint.md).

## <a name="index-access"></a>Accesso all'indice

In ricerca cognitiva di Azure, un singolo indice non è un oggetto a protezione diretta. L'accesso a un indice viene invece determinato a livello di servizio (accesso in lettura o in scrittura al servizio), insieme al contesto di un'operazione.

Per l'accesso utente finale, è possibile strutturare le richieste di query per la connessione tramite una chiave di query, che imposta qualsiasi richiesta come di sola lettura, e includere l'indice specifico usato dall'app. In una richiesta di query non è previsto il join degli indici o l'accesso a più indici simultaneamente, quindi tutte le richiesta specificano come destinazione un solo indice per definizione. Di conseguenza, la costruzione della richiesta della query (una chiave più un singolo indice di destinazione) definisce di per sé il limite di sicurezza.

L'accesso amministratore e sviluppatore agli indici è indifferenziato: entrambi richiedono l'accesso in scrittura per creare, eliminare e aggiornare gli oggetti gestiti dal servizio. Chiunque abbia una chiave amministratore per il servizio può leggere, modificare o eliminare qualsiasi indice nello stesso servizio. Per proteggersi dall'eliminazione accidentale o intenzionale degli indici, il controllo del codice sorgente interno per gli asset del codice è la risoluzione che consente di invertire l'eliminazione o la modifica indesiderata di un indice. Azure ricerca cognitiva dispone di failover all'interno del cluster per garantire la disponibilità, ma non archivia o esegue il codice proprietario usato per creare o caricare gli indici.

Le soluzioni multi-tenancy che richiedono limiti di sicurezza a livello di indice includono in genere un livello intermedio, che i clienti usano per gestire l'isolamento degli indici. Per altre informazioni sul caso d'uso multi-tenant, vedere [modelli di progettazione per applicazioni SaaS multi-tenant e ricerca cognitiva di Azure](search-modeling-multitenant-saas-applications.md).

## <a name="user-access"></a>Accesso utente

Il modo in cui un utente accede a un indice e altri oggetti è determinato dal tipo di chiave API nella richiesta. La maggior parte degli sviluppatori crea e assegna [*chiavi di query*](search-security-api-keys.md) per le richieste di ricerca lato client. Una chiave di query concede l'accesso in sola lettura al contenuto ricercabile all'interno dell'indice.

Se è necessario un controllo granulare per utente sui risultati della ricerca, è possibile creare filtri di sicurezza nelle query, restituendo i documenti associati a una determinata identità di sicurezza. Anziché i ruoli predefiniti e le assegnazioni di ruolo, il controllo degli accessi in base all'identità viene implementato come un *filtro* che elimina i risultati della ricerca di documenti e contenuti in base alle identità. La tabella seguente descrive due approcci per limitare i risultati della ricerca di contenuto non autorizzato.

| Approccio | Description |
|----------|-------------|
|[Limitazione per motivi di sicurezza in base ai filtri delle identità](search-security-trimming-for-azure-search.md)  | Documenta il flusso di lavoro di base per implementare il controllo di accesso dell'identità utente. Illustra l'aggiunta di ID di sicurezza a un indice e quindi illustra l'applicazione di filtri a tale campo per limitare i risultati di contenuto non consentito. |
|[Limitazione per motivi di sicurezza in base alle identità di Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Questo articolo è un approfondimento dell'articolo precedente e contiene la procedura per recuperare le identità da Azure Active Directory (AAD), uno dei [servizi gratuiti](https://azure.microsoft.com/free/) della piattaforma cloud Azure. |

## <a name="administrative-rights"></a>Diritti amministrativi

[L'accesso in base al ruolo (RBAC)](../role-based-access-control/overview.md) è un sistema di autorizzazione basato su [Azure Resource Manager](../azure-resource-manager/management/overview.md) per il provisioning delle risorse di Azure. In ricerca cognitiva di Azure, Gestione risorse viene usato per creare o eliminare il servizio, gestire le chiavi API e ridimensionare il servizio. Di conseguenza, le assegnazioni di ruolo di Azure determineranno chi può eseguire tali attività, indipendentemente dal fatto che stiano usando il [portale](search-manage.md), [PowerShell](search-manage-powershell.md)o le [API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

Al contrario, i diritti di amministratore sul contenuto ospitato nel servizio, ad esempio la possibilità di creare o eliminare un indice, vengono condotti tramite le chiavi API, come descritto nella [sezione precedente](#index-access).

> [!TIP]
> Usando i meccanismi a livello di Azure, è possibile bloccare una sottoscrizione o una risorsa per evitare l'eliminazione accidentale o non autorizzata del servizio di ricerca da parte degli utenti con diritti di amministratore. Per altre informazioni, vedere [bloccare le risorse per impedire l'eliminazione imprevista](../azure-resource-manager/management/lock-resources.md).

## <a name="certifications-and-compliance"></a>Certificazioni e conformità

Azure ricerca cognitiva è stato certificato conforme per più standard globali, regionali e specifici del settore sia per il cloud pubblico che per Azure per enti pubblici. Per l'elenco completo, scaricare il [white paper sulle **offerte di conformità Microsoft Azure** ](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) dalla pagina dei report di controllo ufficiale.

Per garantire la conformità, è possibile usare i [criteri di Azure](../governance/policy/overview.md) per implementare le procedure consigliate per la sicurezza elevata del benchmark di [sicurezza di Azure](../security/benchmarks/introduction.md). Il benchmark di sicurezza di Azure è una raccolta di consigli sulla sicurezza, codificati in controlli di sicurezza che si riferiscono a azioni chiave che è necessario intraprendere per attenuare le minacce ai servizi e ai dati. Attualmente sono presenti 11 controlli di sicurezza, tra cui la [sicurezza della rete](../security/benchmarks/security-control-network-security.md), la [registrazione e il monitoraggio](../security/benchmarks/security-control-logging-monitoring.md)e la [protezione dei dati](../security/benchmarks/security-control-data-protection.md) , per citarne alcuni.

Criteri di Azure è una funzionalità incorporata in Azure che consente di gestire la conformità per più standard, inclusi quelli del benchmark di sicurezza di Azure. Per i benchmark noti, i criteri di Azure forniscono definizioni predefinite che forniscono entrambi i criteri, nonché una risposta praticabile che risolve la mancata conformità. 

Per ricerca cognitiva di Azure, esiste attualmente una definizione predefinita. Per la registrazione diagnostica. Con questa funzionalità incorporata, è possibile assegnare un criterio che identifichi qualsiasi servizio di ricerca privo di registrazione diagnostica e quindi lo attiva. Per altre informazioni, vedere [controlli di conformità normativi di criteri di Azure per ricerca cognitiva di Azure](security-controls-policy.md).

## <a name="see-also"></a>Vedere anche

+ [Concetti fondamentali della sicurezza di Azure](../security/fundamentals/index.yml)
+ [Sicurezza di Azure](https://azure.microsoft.com/overview/security)
+ [Centro sicurezza di Azure](../security-center/index.yml)