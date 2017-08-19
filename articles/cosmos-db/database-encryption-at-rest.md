---
title: 'Crittografia di database inattivi: Azure Cosmos DB | Microsoft Docs'
description: Informazioni sulla crittografia predefinita per tutti i dati offerta da Azure Cosmos DB.
services: cosmos-db
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: d8967d4504a8ccabb444c7f3d5635e2d00f287c5
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---

# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Crittografia di dati inattivi del database in Azure Cosmos DB

Il termine "crittografia di dati inattivi" denota in genere la crittografia dei dati su dispositivi di archiviazione non volatili, come ad esempio unità a stato solido (SSD) e dischi rigidi (HDD). Cosmos DB archivia i propri database primari su SSD. Gli allegati multimediali e i backup vengono memorizzati nell'archiviazione BLOB di Azure, di cui in genere si esegue il backup su HDD. Con il rilascio della crittografia di dati inattivi per Cosmos DB, tutti i database, gli allegati multimediali e i backup vengono ora crittografati. Questo vale sia per i dati in transito (attraverso la rete), sia quelli inattivi (memoria non volatile), fornendo una crittografia end-to-end.

In qualità di servizio PaaS, Cosmos DB è molto facile da usare. Poiché tutti i dati utente archiviati in Cosmos DB sono crittografati sia quando sono inattivi che in transito, non è necessario intraprendere alcuna azione. In altre parole, per impostazione predefinita, la crittografia dei dati inattivi è attiva. Non è possibile attivarla o disattivarla. Tale funzionalità è offerta nel soddisfare [i contratti di servizio relativi a disponibilità e prestazioni](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implement-encryption-at-rest"></a>Implementare la crittografia di dati inattivi

La crittografia dei dati inattivi viene implementata attraverso una serie di tecnologie di protezione, inclusi i sistemi di archiviazione protetta delle chiavi, le reti crittografate e le API di crittografia. I sistemi che decrittografano ed elaborano i dati devono comunicare con i sistemi di gestione delle chiavi. Il diagramma mostra come viene separata l'archiviazione dei dati crittografati e la gestione delle chiavi. 

![Diagramma di progettazione](./media/database-encryption-at-rest/design-diagram.png)

Il flusso di base di una richiesta dell'utente è il seguente:
- L'account del database utente viene preparato e le chiavi di archiviazione vengono recuperate tramite una richiesta al provider di risorse del servizio di gestione.
- Un utente crea una connessione ad Azure Cosmos DB tramite il trasporto HTTPS/protetto; gli SDK consentono di estrapolare i dettagli.
- L'utente invia un documento JSON da archiviare tramite la connessione protetta creata in precedenza.
- Il documento JSON è indicizzato, a meno che l'utente non abbia disabilitato l'indicizzazione.
- Sia il documento JSON sia i dati dell'indice vengono scritti in un archivio protetto.
- Periodicamente i dati vengono letti dalla risorsa di archiviazione protetta e viene eseguito il backup nell'archivio BLOB crittografato di Azure.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>D: A quanto ammonta il costo aggiuntivo dell'Archiviazione di Azure se si abilita la crittografia del servizio di archiviazione?
R: Non sono previsti costi aggiuntivi.

### <a name="q-who-manages-the-encryption-keys"></a>D: Chi gestisce le chiavi di crittografia?
R: Le chiavi vengono gestite da Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>D: Con quale frequenza vengono ruotate le chiavi di crittografia?
R: Microsoft ha un set di linee guida interne per la rotazione della chiave di crittografia che segue Cosmos DB. Le linee guida specifiche non vengono pubblicate. Microsoft pubblica il [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), che viene considerato un subset di linee guida interne e include procedure consigliate utili per gli sviluppatori.

### <a name="q-can-i-use-my-own-encryption-keys"></a>D: È possibile usare le proprie chiavi di crittografia?
R: Cosmos DB è un servizio PaaS e l'intento è quello di garantire la semplicità d'uso. Spesso questa domanda viene usata come domanda di proxy per soddisfare un requisito conformità come PCI-DSS. Nell'ambito della compilazione di questa funzionalità, la collaborazione con i revisori della conformità consente ai clienti che usano Cosmos DB di soddisfare i propri requisiti senza dover gestire personalmente le chiavi.
Di conseguenza attualmente non viene offerta agli utenti la possibilità di farsi carico dell'onere di gestione delle chiavi.

### <a name="q-what-regions-have-encryption-turned-on"></a>D: In quali aree è attiva la crittografia?
R: La crittografia è attiva in tutte le aree di Azure Cosmos DB per tutti i dati utente.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>D: La crittografia influisce sui contratti di servizio per latenza delle prestazioni e velocità effettiva?
R: Non si registrano impatti o modifiche in relazione ai contratti di servizio per le prestazioni ora che la crittografia dei dati inattivi è abilitata per tutti gli account nuovi ed esistenti. Per altre informazioni sulle garanzie più recenti, vedere il [Contratto di servizio per Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db).

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>D: L'emulatore locale supporta la crittografia dei dati inattivi?
R: L'emulatore è uno strumento di sviluppo e test autonomo e non usa i servizi di gestione delle chiavi usati dal servizio gestito di Cosmos DB. È consigliabile abilitare BitLocker sulle unità in cui si archiviano i dati di test sensibili dell'emulatore. L'[emulatore supporta la modifica nella directory di dati predefinita](local-emulator.md) e usa un percorso noto.

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica della sicurezza di Cosmos DB e dei miglioramenti più recenti, vedere [Sicurezza database di Azure Cosmos DB](database-security.md).
Per altre informazioni sulle certificazioni Microsoft, vedere il [Centro protezione di Azure](https://azure.microsoft.com/en-us/support/trust-center/).

