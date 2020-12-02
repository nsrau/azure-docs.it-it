---
title: Crittografia dei dati inattivi di Azure-sicurezza di Azure
description: Questo articolo fornisce una panoramica della crittografia dei dati inattivi di Azure, delle funzionalità generali e delle considerazioni generali.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 092320db9b7fe2b1f3fe142f84ad201d40dc6e2e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492285"
---
# <a name="azure-data-encryption-at-rest"></a>Crittografia dei dati inattivi di Azure

Microsoft Azure include gli strumenti per salvaguardare i dati in base alle esigenze di sicurezza e conformità dell'azienda. Questo documento è incentrato su:

- come proteggere i dati inattivi in Microsoft Azure,
- i vari componenti che compongono l'implementazione di protezione dei dati,
- i pro e i contro dei vari principali approcci alla protezione della gestione.

La crittografia dei dati inattivi è un requisito di sicurezza comune. In Azure, le organizzazioni possono crittografare i dati inattivi senza rischi o senza sostenere il costo di una soluzione di gestione delle chiavi personalizzata. Le organizzazioni hanno la possibilità di lasciare che sia Azure a gestire completamente la crittografia dei dati inattivi. Sono inoltre disponibili varie opzioni per gestire con precisione la crittografia o le chiavi di crittografia.

## <a name="what-is-encryption-at-rest"></a>Che cos'è la crittografia dei dati inattivi?

La crittografia dei dati inattivi consiste nella codifica crittografica (crittografia) dei dati quando questi vengono resi persistenti. La crittografia dei dati inattivi di Azure usa la crittografia simmetrica per crittografare e decrittografare rapidamente grandi quantità di dati in base a un semplice modello concettuale:

- Viene usata una chiave di crittografia simmetrica per crittografare i dati quando vengono scritti nella risorsa di archiviazione.
- La chiave di crittografia viene usata per decrittografare i dati durante la loro preparazione per l'uso in memoria.
- I dati possono essere partizionati ed è possibile usare chiavi diverse per ogni partizione.
- Le chiavi devono essere archiviate in una posizione sicura con controllo di accesso basato su identità e criteri di controllo. Le chiavi di crittografia dei dati vengono spesso crittografate con una chiave di crittografia della chiave in Azure Key Vault per limitare ulteriormente l'accesso.

A livello pratico, gli scenari di gestione e di controllo delle chiavi, nonché le garanzie di scalabilità e disponibilità, richiedono costrutti aggiuntivi. Di seguito sono descritti i concetti e i componenti relativi alla crittografia dei dati inattivi di Microsoft Azure.

## <a name="the-purpose-of-encryption-at-rest"></a>Scopo della crittografia dei dati inattivi

La crittografia dei dati inattivi offre la protezione dei dati per i dati archiviati (inattivi). Gli attacchi contro i dati inattivi includono i tentativi di ottenere l'accesso fisico all'hardware in cui sono archiviati i dati e quindi compromettere i dati contenuti. In tal caso, un disco rigido del server potrebbe essere stato gestito in modo errato durante la manutenzione, consentendo a un utente malintenzionato di rimuovere il disco rigido. L'autore dell'attacco potrà quindi inserire il disco rigido in un computer sotto il proprio controllo per tentare di accedere ai dati.

La crittografia dei dati inattivi è progettata per impedire a un utente malintenzionato di accedere ai dati non crittografati, garantendo che i dati siano crittografati quando sono su disco. Se un utente malintenzionato ottiene un disco rigido con i dati crittografati ma non le chiavi di crittografia, l'autore dell'attacco deve decodificare la crittografia per riuscire a leggere i dati. Questo tipo di attacco è molto più complesso e richiede maggiori risorse rispetto all'accesso a dati non crittografati su un disco rigido. Per questo motivo, la crittografia dei dati inattivi è estremamente utile e costituisce un requisito di alta priorità per molte organizzazioni.

La crittografia dei dati inattivi può essere necessaria anche per la necessità di un'organizzazione per la governance dei dati e il lavoro di conformità. Regolamenti governativi e di settore, come HIPAA, PCI e FedRAMP, definiscono specifiche misure di sicurezza relativi ai requisiti di protezione e crittografia dei dati. La crittografia dei dati inattivi è una misura obbligatoria necessaria per la conformità ad alcune di queste normative. Per ulteriori informazioni sull'approccio di Microsoft alla convalida FIPS 140-2, vedere [Federal Information Processing Standard (FIPS) publication 140-2](/microsoft-365/compliance/offering-fips-140-2).

Oltre a soddisfare i requisiti normativi e di conformità, la crittografia dei dati inattivi rappresenta una misura di protezione e difesa avanzata. Microsoft Azure offre una piattaforma conforme per servizi, applicazioni e dati. Offre inoltre funzionalità complete per la sicurezza fisica e delle strutture, il controllo di accesso ai dati e il controllo. Tuttavia, è importante fornire altre misure di sicurezza "sovrapposte" nel caso in cui una delle altre misure di sicurezza abbia esito negativo e la crittografia dei servizi inattivi fornisca tale misura di sicurezza.

Microsoft si impegna a mettere a disposizione opzioni per la crittografia dei dati inattivi per i servizi cloud e offre ai clienti il controllo delle chiavi di crittografia e dei log di uso delle chiavi. Microsoft ha inoltre in progetto di applicare la crittografia dei dati inattivi a tutti i dati dei clienti per impostazione predefinita.

## <a name="azure-encryption-at-rest-components"></a>Componenti della crittografia dei dati inattivi di Azure

Come descritto in precedenza, l'obiettivo della crittografia dei dati inattivi è crittografare i dati resi persistenti su disco con una chiave di crittografia segreta. Per raggiungere tale obiettivo, sono necessarie funzionalità per la creazione, l'archiviazione, il controllo dell'accesso e la gestione delle chiavi di crittografia. Anche se i dettagli possono variare, i servizi per la crittografia dei dati inattivi di Azure possono essere descritti nei termini illustrati nel diagramma seguente.

![Componenti](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Insieme di credenziali chiave di Azure

La posizione di archiviazione delle chiavi di crittografia e il controllo dell'accesso a queste chiavi è essenziale per un modello di crittografia dei dati inattivi. Le chiavi devono essere estremamente protette ma gestibili dagli utenti specificati e disponibili per servizi specifici. Per i servizi di Azure, la soluzione di archiviazione delle chiavi consigliata è Azure Key Vault, che fornisce un'esperienza di gestione comune per tutti i servizi. Le chiavi sono archiviate e gestite in insiemi di credenziali delle chiavi e l'accesso a un insieme di credenziali delle chiavi può essere assegnato a utenti o servizi. Azure Key Vault supporta la creazione di chiavi da parte dei clienti o l'importazione di chiavi dei clienti per l'uso in scenari con chiavi di crittografia gestite dal cliente.

### <a name="azure-active-directory"></a>Azure Active Directory

Le autorizzazioni per l'uso delle chiavi archiviate in Azure Key Vault (per la gestione o per accedervi per la crittografia e la decrittografia dei dati inattivi) possono essere assegnate agli account di Azure Active Directory.

### <a name="key-hierarchy"></a>Gerarchia delle chiavi

Viene usata più di una chiave di crittografia in un'implementazione della crittografia per i dati inattivi. L'archiviazione di una chiave di crittografia in Azure Key Vault garantisce l'accesso alla chiave sicura e la gestione centralizzata delle chiavi. Tuttavia, l'accesso locale al servizio alle chiavi di crittografia è più efficiente per la crittografia e la decrittografia bulk rispetto all'interazione con Key Vault per ogni operazione sui dati, consentendo una crittografia più avanzata e prestazioni migliori. Limitare l'uso di una sola chiave di crittografia riduce il rischio di compromissione della chiave e il costo della ripetizione della crittografia quando è necessario sostituire una chiave. Le crittografie di Azure nei modelli REST usano una gerarchia delle chiavi costituita dai tipi di chiavi seguenti per soddisfare tutte le esigenze:

- **Chiave DEK (Data Encryption Key)**: una chiave AES256 simmetrica usata per crittografare una partizione o un blocco di dati.  Una singola risorsa può avere diverse partizioni e più chiavi DEK. La crittografia di ogni blocco di dati con una chiave diversa rende più complessi gli attacchi di crittoanalisi. È necessario l'accesso alle chiavi DEK per il provider di risorse o l'istanza dell'applicazione che esegue la crittografia e la decrittografia di un blocco specifico. Quando una chiave DEK viene sostituita con una nuova chiave, devono essere nuovamente crittografati con la nuova chiave solo i dati nel blocco associato.
- Chiave di **crittografia della chiave (KEK)** : chiave di crittografia usata per crittografare le chiavi di crittografia dei dati. L'uso di una chiave di crittografia della chiave che non lascia mai Key Vault consente di crittografare e controllare le chiavi di crittografia dei dati. L'entità che ha accesso alla chiave KEK può essere diversa dall'entità che richiede la chiave DEK. Un'entità può gestire l'accesso alla chiave DEK per limitare l'accesso di ogni chiave DEK a una partizione specifica. Poiché è necessaria la chiave KEK per decrittografare le chiavi DEK, la chiave KEK è di fatto un singolo punto che consente di eliminare in modo efficace le chiavi DEK eliminando la chiave KEK.

Le chiavi di crittografia dei dati, crittografate con le chiavi di crittografia della chiave vengono archiviate separatamente e solo un'entità con accesso alla chiave di crittografia della chiave può decrittografare queste chiavi di crittografia dei dati. Sono supportati diversi modelli di archiviazione delle chiavi. Per ulteriori informazioni, vedere [modelli di crittografia dei dati](encryption-models.md) .

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Crittografia dei dati inattivi nei servizi cloud Microsoft

I servizi cloud Microsoft vengono usati in tutti e tre i modelli cloud: IaaS, PaaS e SaaS. Di seguito sono riportati alcuni esempi di come si integrano in ciascun modello:

- Servizi software, detti software come server o SaaS, che hanno applicazioni fornite dal cloud, ad esempio Microsoft 365.
- Servizi della piattaforma, con cui i clienti sfruttano il cloud nelle proprie applicazioni, che usano il cloud per elementi come le funzionalità di archiviazione, analisi e bus di servizio.
- Servizi di infrastruttura, o IaaS (Infrastructure as a Service), in cui i clienti distribuiscono sistemi operativi e applicazioni ospitati nel cloud ed eventualmente sfruttano altri servizi cloud.

### <a name="encryption-at-rest-for-saas-customers"></a>Crittografia dei dati inattivi per i clienti SaaS

Per i clienti SaaS (Software as a Service) in genere la crittografia dei dati inattivi è abilitata o disponibile in ogni servizio. Microsoft 365 offre diverse opzioni per la verifica o l'abilitazione della crittografia dei clienti. Per informazioni sui servizi di Microsoft 365, vedere [crittografia in Microsoft 365](/microsoft-365/compliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Crittografia dei dati inattivi per i clienti PaaS

I dati del cliente della piattaforma distribuita come servizio (PaaS) si trovano in genere in un servizio di archiviazione, ad esempio l'archiviazione BLOB, ma possono anche essere memorizzati nella cache o archiviati nell'ambiente di esecuzione dell'applicazione, ad esempio una macchina virtuale. Per visualizzare le opzioni disponibili per la crittografia dei dati inattivi, esaminare la tabella seguente per le piattaforme di archiviazione e per le applicazioni in uso.

### <a name="encryption-at-rest-for-iaas-customers"></a>Crittografia dei dati inattivi per i clienti IaaS

I clienti IaaS (Infrastructure as a Service) possono avere un'ampia gamma di servizi e applicazioni in uso. I servizi IaaS possono abilitare la crittografia dei dati inattivi nelle macchine virtuali e nei dischi rigidi virtuali ospitati in Azure usando Crittografia dischi di Azure.

#### <a name="encrypted-storage"></a>Archiviazione crittografata

Come le soluzioni PaaS, quelle IaaS possono sfruttare altri servizi di Azure che archiviano dati inattivi crittografati. In questi casi, è possibile abilitare il supporto per la crittografia dei dati inattivi come specificato da ogni servizio di Azure in uso. Nella tabella seguente sono enumerate le principali piattaforme per l'archiviazione, i servizi e le applicazioni, insieme al modello di crittografia dei dati inattivi supportato.

#### <a name="encrypted-compute"></a>Calcolo crittografato

Tutti i Managed Disks, gli snapshot e le immagini vengono crittografati utilizzando crittografia del servizio di archiviazione utilizzando una chiave gestita dal servizio. Una soluzione di crittografia inattiva più completa garantisce che i dati non vengano mai mantenuti in formato non crittografato. Durante l'elaborazione dei dati in una macchina virtuale, è possibile salvare in modo permanente i dati nel file di paging di Windows o nel file di scambio Linux, in un dump di arresto anomalo o in un registro applicazioni. Per assicurarsi che questi dati inattivi vengano crittografati, le applicazioni IaaS possono usare Crittografia dischi di Azure su una macchina virtuale IaaS di Azure (Windows o Linux) e un disco virtuale.

#### <a name="custom-encryption-at-rest"></a>Crittografia dei dati inattivi personalizzata

È consigliabile che, quando possibile, le applicazioni IaaS sfruttino Crittografia dischi di Azure e le opzioni di crittografia dei dati inattivi fornite da qualsiasi servizio di Azure in uso. In alcuni casi, ad esempio per requisiti di crittografia irregolari o l'archiviazione non basata su Azure, uno sviluppatore di un'applicazione IaaS potrebbe avere bisogno di implementare autonomamente la crittografia dei dati inattivi. Gli sviluppatori di soluzioni IaaS possono ottimizzare l'integrazione con la gestione di Azure le aspettative dei clienti sfruttando determinati componenti di Azure. In particolare, gli sviluppatori devono usare il servizio Azure Key Vault per fornire l'archiviazione protetta delle chiavi, nonché offrire ai clienti opzioni di gestione delle chiavi coerenti con quelle della maggior parte dei servizi della piattaforma Azure. Le soluzioni personalizzate devono anche usare le identità dei servizi gestiti di Azure per consentire agli account del servizio di accedere alle chiavi di crittografia. Per informazioni per gli sviluppatori su Azure Key Vault e le identità dei servizi gestiti, vedere i rispettivi SDK.

## <a name="azure-resource-providers-encryption-model-support"></a>Supporto per il modello di crittografia dei provider di risorse di Azure

Ogni servizio di Microsoft Azure supporta uno o più modelli di crittografia dei dati inattivi. Per alcuni servizi, tuttavia, uno o più dei modelli di crittografia potrebbero non essere applicabili. I servizi che supportano scenari con chiavi gestite dal cliente potrebbero supportare solo un subset dei tipi di chiavi supportati da Azure Key Vault per le chiavi di crittografia della chiave. I servizi possono inoltre rilasciare il supporto per questi scenari e per i tipi di chiave con pianificazioni diverse. In questa sezione viene descritto il supporto per la crittografia dei dati inattivi al momento della stesura del presente documento per ognuno dei principali servizi di archiviazione dati di Azure.

### <a name="azure-disk-encryption"></a>Crittografia dischi di Azure

Tutti i clienti che usano le funzionalità IaaS (Infrastructure as a Service) di Azure possono ottenere la crittografia dei dati inattivi per le macchine virtuali IaaS e i dischi tramite Crittografia dischi di Azure. Per altre informazioni su crittografia dischi di Azure, vedere la [documentazione relativa a crittografia dischi di Azure](./azure-disk-encryption-vms-vmss.md).

#### <a name="azure-storage"></a>Archiviazione di Azure

Tutti i servizi di archiviazione di Azure (archiviazione BLOB, archiviazione code, archiviazione tabelle e File di Azure) supportano la crittografia lato server. alcuni servizi supportano inoltre le chiavi gestite dal cliente e la crittografia lato client.

- Lato server: tutti i servizi di archiviazione di Azure abilitano la crittografia lato server per impostazione predefinita con chiavi gestite dal servizio, in modo trasparente all'applicazione. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](../../storage/common/storage-service-encryption.md). Archiviazione BLOB di Azure e File di Azure supportano anche chiavi RSA a 2048 bit gestite dal cliente in Azure Key Vault. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).
- Lato client: BLOB di Azure, tabelle e code di Azure supportano la crittografia lato client. Quando usano la crittografia lato client, i clienti crittografano i dati e li caricano come un BLOB crittografato. La gestione delle chiavi viene eseguita dal cliente. Per altre informazioni, vedere [Crittografia lato client e Azure Key Vault per Archiviazione di Microsoft Azure](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Database SQL di Azure

Database SQL di Azure attualmente supporta la crittografia dei dati inattivi per gli scenari di crittografia sul lato client e sul lato del servizio gestito da Microsoft.

Il supporto per la crittografia server viene attualmente fornito tramite la funzionalità di SQL denominata Transparent Data Encryption. Quando un cliente di Database SQL di Azure abilita TDE, le chiavi vengono create e gestite automaticamente. La crittografia dei dati inattivi può essere abilitata a livello di database e di server. A partire da giugno 2017, la funzionalità [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) è abilitata per impostazione predefinita nei nuovi database creati. Database SQL di Azure supporta chiavi RSA a 2048 bit gestite dal cliente in Azure Key Vault. Per altre informazioni, vedere [Transparent Data Encryption con supporto BYOK (Bring Your Own Key) per il database SQL di Azure e Azure SQL Data Warehouse](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql).

La crittografia lato client dei dati di Database SQL di Azure è supportata tramite la funzionalità [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted usa una chiave creata e archiviata dal client. I clienti possono archiviare la chiave master in un archivio certificati di Windows, in Azure Key Vault o in un modulo di protezione hardware locale. Utilizzando SQL Server Management Studio, gli utenti SQL scelgono la chiave che si desidera utilizzare per crittografare la colonna.

## <a name="conclusion"></a>Conclusione

La protezione dei dati dei clienti archiviati nei servizi di Azure è di importanza fondamentale per Microsoft. Per tutti i servizi di Azure ospitati è prevista la disponibilità di opzioni di crittografia dei dati inattivi. I servizi di Azure supportano chiavi gestite dal servizio, chiavi gestite dal cliente o la crittografia lato client. I servizi di Azure migliorano in generale la crittografia a livello di disponibilità e le nuove opzioni sono pianificate per l'anteprima e la disponibilità generale nei prossimi mesi.

## <a name="next-steps"></a>Passaggi successivi

- Vedere [modelli di crittografia dei dati](encryption-models.md) per ulteriori informazioni sulle chiavi gestite dal servizio e sulle chiavi gestite dal cliente.
- Informazioni su come Azure usa la [crittografia doppia](double-encryption.md) per attenuare le minacce che derivano dalla crittografia dei dati.
- Scopri cosa fa Microsoft per garantire l' [integrità e la sicurezza della piattaforma](platform.md) degli host che attraversano le pipeline di compilazione, integrazione, messa in funzione e ripristino di hardware e firmware.
