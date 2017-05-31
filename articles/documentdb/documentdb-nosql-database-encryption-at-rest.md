---
title: Crittografia di dati inattivi del database - Azure Cosmos DB | Microsoft Docs
description: Informazioni sulla crittografia predefinita per tutti i dati offerta da Azure Cosmos DB.
services: cosmosdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8248b429cf9b7fdfc709a68c4d1e88b7b660020b
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---

# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Crittografia di dati inattivi del database in Azure Cosmos DB

Il termine "crittografia di dati inattivi" denota in genere la crittografia dei dati su dispositivi di archiviazione non volatili, come ad esempio unità a stato solido (SSD) e dischi rigidi (HDD).  Azure Cosmos DB archivia i propri database primari in unità SSD e gli allegati multimediali e i backup nei BLOB di Azure, il cui backup in genere è eseguito in unità HDD.  Con il rilascio della crittografia di dati inattivi per Azure Cosmos DB, tutti i database, gli allegati multimediali e i backup vengono ora crittografati.  Ciò significa che vengono crittografati sia i dati in transito (attraverso la rete) sia quelli inattivi (archiviazione non volatile) fornendo una crittografia end-to-end.

Con il servizio PaaS si è cercato di semplificare molto l'uso di Azure Cosmos DB.  A tal fine viene eseguita la crittografia di tutti i dati utente inattivi e in transito archiviati in Azure Cosmos DB senza che sia necessaria alcuna azione da parte dell'utente.  Un'altra possibilità è abilitare la crittografia dei dati per impostazione predefinita.  Non sono presenti controlli da abilitare o disabilitare e questa funzionalità viene fornita continuando a soddisfare i [contratti di servizio relativi alla disponibilità e alle prestazioni](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/).

## <a name="how-does-encryption-at-rest-work"></a>Come funziona la crittografia dei dati inattivi?

La crittografia dei dati inattivi viene implementata attraverso una serie di tecnologie di protezione, inclusi i sistemi di archiviazione protetta delle chiavi, le reti crittografate e le API di crittografia.  Il diagramma seguente mostra come viene separata l'archiviazione dei dati crittografati e la gestione delle chiavi.  I sistemi che decrittografano ed elaborano i dati devono comunicare con i sistemi di gestione delle chiavi.

![Diagramma di progettazione](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

Il flusso di base di una richiesta dell'utente è il seguente:
- L'account del database utente viene preparato e le chiavi di archiviazione vengono recuperate tramite una richiesta al provider di risorse (RP) del servizio di gestione.
- Un utente crea una connessione ad Azure Cosmos DB tramite il trasporto HTTPS/protetto; gli SDK consentono di estrapolare i dettagli.
- L'utente invia un documento JSON da archiviare tramite la connessione protetta creata in precedenza.
- Il documento JSON è indicizzato, a meno che l'utente non abbia disabilitato l'indicizzazione.
- Sia il documento JSON sia i dati dell'indice vengono scritti in un archivio protetto.
- Periodicamente i dati vengono letti dalla risorsa di archiviazione protetta e viene eseguito il backup nell'archivio BLOB crittografato di Azure.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="q-how-much-more-does-azure-storage-cost-if-sse-is-enabled"></a>D: A quanto ammonta il costo aggiuntivo dell'Archiviazione di Azure se si abilita Crittografia del servizio di archiviazione?
R: Non sono previsti costi aggiuntivi.

### <a name="q-who-manages-the-encryption-keys"></a>D: Chi gestisce le chiavi di crittografia?
R: Le chiavi vengono gestite da Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>D: Con quale frequenza vengono ruotate le chiavi di crittografia?
R: Microsoft ha un set di linee guida interne seguite da DocumentDB.  Anche se non vengono pubblicate le linee guida specifiche, Microsoft pubblica le linee guida Security Development Lifecycle (SDL) [https://www.microsoft.com/sdl/default.aspx], considerate un subset di indicazioni interne con utili procedure consigliate per gli sviluppatori.

### <a name="q-can-i-use-my-own-encryption-keys"></a>D: È possibile usare le proprie chiavi di crittografia?
R: Azure Cosmos DB è un servizio PaaS e l'intento è quello di garantire la semplicità d'uso.  Spesso questa domanda viene usata come domanda indiretta per soddisfare una conformità come PCI-DSS.  Nell'ambito della compilazione di questa funzionalità, la collaborazione con i revisori della conformità consente ai clienti che usano Azure Cosmos DB di soddisfare i propri requisiti senza dover gestire personalmente le chiavi.
Ecco perché attualmente viene offerta agli utenti l'opzione di farsi carico dell'onere di gestione delle chiavi.


### <a name="q-what-regions-have-encryption-turned-on"></a>D: In quali aree è attiva la crittografia?
R: La crittografia è attiva in tutte le aree di Azure Cosmos DB per tutti i dati utente.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>D: La crittografia influisce sui contratti di servizio per latenza delle prestazioni e velocità effettiva?
R: Non si registrano impatti o modifiche in relazione ai contratti di servizio per le prestazioni ora che la crittografia dei dati inattivi è abilitata per tutti gli account nuovi ed esistenti.  Per altre informazioni sulle garanzie più recenti, vedere [Contratto di servizio per Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/documentdb).

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>D: L'emulatore locale supporta la crittografia dei dati inattivi?
R: L'emulatore è uno strumento di sviluppo e test autonomo e non usa i servizi di gestione delle chiavi usati dal servizio gestito di DocumentDB. È consigliabile abilitare BitLocker sulle unità in cui si archiviano i dati di test sensibili dell'emulatore. L'emulatore supporta la modifica della directory predefinita dei dati [https://docs.microsoft.com/azure/documentdb/documentdb-nosql-local-emulator] e l'uso di un percorso noto.

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica della sicurezza di Azure Cosmos DB e dei miglioramenti più recenti, vedere [Sicurezza database - Azure Cosmos DB](documentdb-nosql-database-security.md).

Per altre informazioni sulle certificazioni Microsoft, vedere il [Centro protezione di Azure](https://azure.microsoft.com/en-us/support/trust-center/).

