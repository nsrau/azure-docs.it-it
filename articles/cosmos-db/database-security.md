---
title: Sicurezza database - Azure Cosmos DB
description: Informazioni su come Azure Cosmos DB garantisca la protezione del database e la sicurezza dei dati.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mjbrown
ms.openlocfilehash: 30991f17970eefe1a140cdd70e1f6b305160349c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537330"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Panoramica sulla sicurezza in Azure Cosmos DB

Questo articolo illustra le procedure consigliate per la sicurezza del database e le funzionalità principali offerte da Azure Cosmos DB per prevenire, rilevare e rispondere alle violazioni del database.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Novità della sicurezza di Azure Cosmos DB

La crittografia dei dati inattivi è ora disponibile per i documenti e i backup archiviati nel database Azure Cosmos in tutte le aree di Azure. La crittografia di dati inattivi viene applicata automaticamente sai per i clienti nuovi che per quelli esistenti in queste aree. Non è necessario eseguire alcuna configurazione e latenza, velocità effettiva, disponibilità e funzionalità sono eccezionali come prima, con il vantaggio che i dati sono sicuri e protetti con la crittografia di dati inattivi.

## <a name="how-do-i-secure-my-database"></a>Come posso proteggere il mio database

La sicurezza dei dati è una responsabilità condivisa tra l'utente, il cliente e il provider di database. A seconda del provider di database scelto, l'entità della responsabilità di ognuno può variare. Se si sceglie una soluzione locale, è necessario garantire tutto ciò che è indispensabile, dalla protezione degli endpoint alla sicurezza fisica dell'hardware, il che non è semplice. Se si sceglie un provider di database cloud PaaS, ad esempio Azure Cosmos DB, l'ambito di interesse si riduce considerevolmente. L'immagine seguente, tratta dal white paper di Microsoft [Shared Responsibilities for Cloud Computing](https://aka.ms/sharedresponsibility) (Responsabilità condivise per il cloud computing), illustra come la responsabilità diminuisca con un provider PaaS, ad esempio Azure Cosmos DB.

![Responsabilità del provider di database e del cliente](./media/database-security/nosql-database-security-responsibilities.png)

Il diagramma precedente illustra i componenti della sicurezza cloud generali, ma è opportuno stabilire quali sono gli elementi specifici da tenere in considerazione per la soluzione di database e come è possibile confrontare le diverse soluzioni.

È consigliabile usare l'elenco di controllo dei requisiti seguente per confrontare i sistemi di database:

- Sicurezza di rete e impostazioni del firewall
- Autenticazione utente e controlli utente con granularità fine
- Possibilità di replicare i dati a livello globale per gli errori locali
- Possibilità di eseguire il failover da un data center a un altro
- Replica di dati locali all'interno di un data center
- Backup automatici dei dati
- Ripristino dei dati eliminati dai backup
- Proteggere e isolare i dati sensibili
- Monitoraggio per identificare gli attacchi
- Risposta agli attacchi
- Possibilità per i dati di seguire le restrizioni di governance dei dati in base al geo-fencing
- Protezione fisica dei server in data center protetti
- Certificazioni

E, anche se può sembrare ovvio, le recenti [violazioni di database su larga scala](https://thehackernews.com/2017/01/mongodb-database-security.html) rendono molto importanti i semplici requisiti seguenti:

- Server con patch che vengono mantenuti aggiornati
- Crittografia SSL/HTTPS predefinita
- Account amministrativi con password complesse

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>In che modo Azure Cosmos DB protegge il databaseHow does Azure Cosmos DB secure my database

Dall'elenco precedente risulta che Azure Cosmos DB soddisfa ogni singolo requisito di sicurezza.

La tabella seguente li illustra in dettaglio.

|Requisito di sicurezza|Approccio alla sicurezza di Azure Cosmos DB|
|---|---|
|Sicurezza di rete|L'uso di un firewall IP è il primo livello di protezione per il database. Azure Cosmos DB supporta ora controlli di accesso IP basati su criteri per il supporto del firewall in ingresso. I controlli di accesso basati su IP sono simili alle regole del firewall usate dai sistemi di database tradizionali, ma vengono espansi in modo che un account di database Cosmos di Azure sia accessibile solo da un set approvato di computer o servizi cloud. Per altre informazioni, vedere l'articolo di [supporto del firewall di Azure Cosmos DB.Learn](firewall-support.md) more in Azure Cosmos DB firewall support article.<br><br>Azure Cosmos DB consente di abilitare un indirizzo IP specifico (168.61.48.0), un intervallo di IP (168.61.48.0/8) e combinazioni di IP e intervalli. <br><br>Tutte le richieste che hanno origine da computer non compresi in questo elenco di elementi consentiti vengono bloccate da Azure Cosmos DB. Le richieste provenienti da computer e servizi cloud approvati devono quindi completare il processo di autenticazione per ottenere il controllo di accesso alle risorse.<br><br> È possibile usare i tag del servizio di [rete virtuale](../virtual-network/service-tags-overview.md) per ottenere l'isolamento rete e proteggere le risorse di Azure Cosmos DB da Internet generale. Utilizzare i tag di servizio al posto di indirizzi IP specifici quando si creano regole di sicurezza. Specificando il nome del tag del servizio (ad esempio, AzureCosmosDB) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente.|
|Autorizzazione|Azure Cosmos DB usa HMAC (Hash Message Authentication Code) per l'autorizzazione. <br><br>Per ogni richiesta viene generato un hash usando la chiave dell'account privata e il successivo hash con codifica Base 64 viene inviato con ogni chiamata ad Azure Cosmos DB. Per convalidare la richiesta, il servizio Azure Cosmos DB usa la chiave privata e le proprietà corrette per generare un hash, quindi confronta il valore con quello della richiesta. Se i due valori corrispondono, l'operazione viene autorizzata e la richiesta viene elaborata. In caso contrario, si verifica un errore di autorizzazione e la richiesta viene rifiutata.<br><br>È possibile usare una [chiave master](secure-access-to-data.md#master-keys) o un [token della risorsa](secure-access-to-data.md#resource-tokens) che consente l'accesso con granularità fine a una risorsa, ad esempio un documento.<br><br>Per altre informazioni, vedere [Protezione dell'accesso alle risorse in Azure Cosmos DB](secure-access-to-data.md).|
|Utenti e autorizzazioni|Usando la chiave master per l'account, è possibile creare risorse utente e risorse autorizzazione per ogni database. Un token della risorsa viene associato a un'autorizzazione in un database e determina se l'utente ha accesso (lettura/scrittura, sola lettura o nessun accesso) a una risorsa dell'applicazione nel database. Le risorse dell'applicazione includono contenitori, documenti, allegati, stored procedure, trigger e funzioni definite dall'utente. Il token della risorsa viene quindi usato durante l'autenticazione per concedere o negare l'accesso alla risorsa.<br><br>Per altre informazioni, vedere [Protezione dell'accesso alle risorse in Azure Cosmos DB](secure-access-to-data.md).|
|Integrazione di Active Directory (controllo degli accessi in base al ruolo)| È anche possibile fornire o limitare l'accesso all'account Cosmos, al database, al contenitore e alle offerte (velocità effettiva) usando il controllo di accesso (IAM) nel portale di Azure.You can also provide or restrict access to the Cosmos account, database, container, and offers (throughput) using Access control (IAM) in the Azure portal. IAM fornisce il controllo degli accessi in base al ruolo e si integra con Active Directory. È possibile utilizzare ruoli predefiniti o ruoli personalizzati per singoli utenti e gruppi. Per altre informazioni, vedere l'articolo [sull'integrazione](role-based-access-control.md) di Active Directory.See Active Directory integration article for more information.|
|Replica globale|Azure Cosmos DB offre la distribuzione globale chiavi in mano, che consente di replicare i dati in qualsiasi data center di Azure nel mondo, semplicemente facendo clic su un pulsante. La replica globale offre una scalabilità globale e l'accesso a bassa latenza ai dati ovunque nel mondo.<br><br>Nell'ambito della sicurezza, la replica globale assicura la protezione dei dati da errori a livello di area.<br><br>Per altre informazioni, vedere [Distribuire i dati a livello globale](distribute-data-globally.md).|
|Failover a livello di area|Se i dati sono stati replicati in più di un data center, Azure Cosmos DB esegue il rollover automatico delle operazioni, nel caso in cui un data center di un'area specifica passi alla modalità offline. È possibile creare un elenco con priorità di aree di failover usando le aree in cui i dati vengono replicati. <br><br>Per altre informazioni, vedere [Failover a livello di area in Azure Cosmos DB](high-availability.md).|
|Replica locale|Anche in un singolo data center, Azure Cosmos DB replica automaticamente i dati per la disponibilità elevata consentendo di scegliere i [livelli di coerenza](consistency-levels.md). Questa replica garantisce un [sLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) di disponibilità del 99,99% per tutti gli account di area singola e tutti gli account a più aree con coerenza rilassata e il 99,999% di disponibilità in lettura su tutti gli account di database a più aree.|
|Backup online automatizzati|Il backup dei database cosmo di Azure viene eseguito regolarmente e archiviato in un archivio con ridondanza geografica. <br><br>Per altre informazioni, vedere [Backup online automatico e ripristino con Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md).|
|Ripristinare i data eliminati|I backup online automatizzati possono essere usati per recuperare i dati eliminati per errore fino a circa 30 giorni dopo l'evento. <br><br>Per altre informazioni, vedere [Backup e ripristino online automatici con Database Cosmos di AzureLearn](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) more in Automatic online backup and restore with Azure Cosmos DB|
|Proteggere e isolare i dati sensibili|Tutti i dati nelle aree elencate nelle novità ora vengono crittografati.<br><br>I dati personali e altri dati riservati possono essere isolati in contenitori specifici e l'accesso in lettura/scrittura o in sola lettura può essere limitato a utenti specifici.|
|Monitorare gli attacchi|Usando [la registrazione di controllo e i log attività](logging.md), è possibile monitorare l'account per identificare attività normali e anomale. È possibile visualizzare quali operazioni sono state eseguite sulle risorse, chi ha avviato l'operazione, quando si è verificata l'operazione, lo stato dell'operazione e molto altro come mostrato nella schermata sotto a questa tabella.|
|Rispondere agli attacchi|Dopo avere contattato il supporto di Azure per segnalare un potenziale attacco, viene avviato un processo di risposta all'evento imprevisto in 5 fasi. L'obiettivo del processo in 5 fasi è quello di ripristinare la normale sicurezza del servizio e le normali operazioni il più rapidamente possibile dopo il rilevamento di un problema e l'avvio di un indagine.<br><br>Per altre informazioni, vedere [Microsoft Azure Security Response in the Cloud](https://aka.ms/securityresponsepaper) (Risposta della sicurezza di Microsoft Azure nel cloud).|
|Definizione del geo-fencing|Azure Cosmos DB assicura la governance dei dati per le aree sovrane, ad esempio Germania, Cina, US Gov.|
|Strutture protette|I dati in Azure Cosmos DB vengono archiviati in unità SSD nei data center protetti di Azure.<br><br>Per altre informazioni, vedere [Data center globali Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Crittografia HTTPS/SSL/TLS|Tutte le connessioni a Azure Cosmos DB supportano HTTPS. Azure Cosmos DB supporta anche TLS 1.2.Azure Cosmos DB also supports TLS 1.2.<br>È possibile applicare una versione TLS minima lato server. Per farlo, si [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)prega di contattare .|
|Crittografia di dati inattivi|Tutti i dati archiviati in Azure Cosmos DB vengono crittografati quando sono inattivi. Per altre informazioni, vedere [Crittografia dei dati inattivi in Azure Cosmos DB](./database-encryption-at-rest.md)|
|Server con patch|Essendo un database gestito, Azure Cosmos DB elimina la necessità di gestire e applicare patch ai server perché queste operazioni vengono eseguite automaticamente.|
|Account amministrativi con password complesse|Può sembrare strano vedere citato questo requisito, ma a differenza di quanto accade con alcuni concorrenti, è impossibile avere un account amministrativo senza password in Azure Cosmos DB.<br><br> La sicurezza usa SSL e l'autenticazione basata su segreti HMAC è supportata per impostazione predefinita.|
|Certificazioni di sicurezza e protezione dei dati| Per l'elenco di certificazioni più aggiornato, vedere il [sito di conformità](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) di Azure generale e il documento di conformità di [Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) più recente con tutte le certificazioni (ricerca di Cosmos). Per una lettura più mirata, consulta il post del 25 aprile 2018 [Azure #CosmosDB: sicuro, privato, conforme che include SOCS 1/2 Tipo 2, HITRUST, PCI DSS Livello 1, ISO 27001, HIPAA, FedRAMP High e molti altri.

La schermata seguente mostra come è possibile usare la registrazione di controllo e i log attività per monitorare l'account: ![Log attività per Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle chiavi master e sui token di risorsa, vedere [Protezione dell'accesso ai dati del database Cosmos di Azure.For](secure-access-to-data.md)more information about master keys and resource tokens, see Securing access to Azure Cosmos DB data .

Per altre informazioni sulla registrazione di controllo, vedere [Registrazione diagnostica di Azure Cosmos DB](logging.md).For more information about audit logging, see Azure Cosmos DB diagnostic logging .

Per altre informazioni sulle certificazioni Microsoft, vedere [Centro protezione di Azure](https://azure.microsoft.com/support/trust-center/).For more information about Microsoft certifications, see Azure Trust Center .