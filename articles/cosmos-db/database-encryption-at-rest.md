---
title: Crittografia dei dati inattivi in Azure Cosmos DB
description: Informazioni su come Azure Cosmos DB gestisce la crittografia dei dati inattivi e come viene implementata questa funzionalità.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: seodec18
ms.openlocfilehash: 541c6f4a6c728844524af794f5e2063f4e352cce
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592135"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Crittografia dei dati in Azure Cosmos DB 

Il termine "crittografia di dati inattivi" denota in genere la crittografia dei dati su dispositivi di archiviazione non volatili, come ad esempio unità a stato solido (SSD) e dischi rigidi (HDD). Cosmos DB archivia i propri database primari su SSD. Gli allegati multimediali e i backup vengono memorizzati nell'archiviazione BLOB di Azure, di cui in genere si esegue il backup su HDD. Con il rilascio della crittografia di dati inattivi per Cosmos DB, tutti i database, gli allegati multimediali e i backup vengono ora crittografati. Vengono crittografati sia i dati in transito (attraverso la rete), sia quelli inattivi (memoria non volatile), fornendo una crittografia end-to-end.

In qualità di servizio PaaS, Azure Cosmos DB è molto facile da usare. Poiché tutti i dati utente archiviati in Azure Cosmos DB sono crittografati sia quando sono inattivi che in transito, non è necessario intraprendere alcuna azione. In altre parole, per impostazione predefinita, la crittografia dei dati inattivi è attiva. Non è possibile attivarla o disattivarla. Azure Cosmos DB usa la crittografia AES-256 in tutte le aree in cui è in esecuzione l'account. Tale funzionalità è offerta nel soddisfare [i contratti di servizio relativi a disponibilità e prestazioni](https://azure.microsoft.com/support/legal/sla/cosmos-db). I dati archiviati nell'account Azure Cosmos vengono crittografati automaticamente e facilmente con le chiavi gestite da Microsoft (chiavi gestite dal servizio). Facoltativamente, è possibile scegliere di aggiungere un secondo livello di crittografia con chiavi personalizzate, come descritto nell'articolo relativo alle [chiavi gestite dal cliente](how-to-setup-cmk.md).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementazione della crittografia dei dati inattivi per Azure Cosmos DB

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

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>D: qual è il costo aggiuntivo di Archiviazione di Azure se la crittografia del servizio di archiviazione è abilitata?
A: Non sono previsti costi aggiuntivi.

### <a name="q-who-manages-the-encryption-keys"></a>D: chi gestisce le chiavi di crittografia?
A: le chiavi vengono gestite da Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>D: con quale frequenza vengono ruotate le chiavi di crittografia?
A: Microsoft ha un set di linee guida interne per la rotazione della chiave di crittografia, seguite da Cosmos DB. Le linee guida specifiche non vengono pubblicate. Microsoft pubblica il [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), che viene considerato un subset di linee guida interne e include procedure consigliate utili per gli sviluppatori.

### <a name="q-can-i-use-my-own-encryption-keys"></a>D: è possibile usare le proprie chiavi di crittografia?
A: Sì questa funzionalità è ora disponibile per i nuovi account Cosmos e questa operazione deve essere eseguita al momento della creazione dell'account. Per altre informazioni, vedere il documento relativo alle [chiavi gestite dal cliente](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk).

### <a name="q-what-regions-have-encryption-turned-on"></a>D: in quali aree è attiva la crittografia?
A: la crittografia è attiva in tutte le aree di Azure Cosmos DB per tutti i dati utente.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>D: la crittografia influisce sui contratti di servizio per latenza delle prestazioni e velocità effettiva?
A: non si registrano impatti o modifiche in relazione ai contratti di servizio per le prestazioni ora che la crittografia dei dati inattivi è abilitata per tutti gli account nuovi ed esistenti. Per altre informazioni sulle garanzie più recenti, vedere il [Contratto di servizio per Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db).

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>D: l'emulatore locale supporta la crittografia dei dati inattivi?
A: l'emulatore è uno strumento di sviluppo e test autonomo e non usa i servizi di gestione delle chiavi usati dal servizio gestito di Cosmos DB. È consigliabile abilitare BitLocker sulle unità in cui si archiviano i dati di test sensibili dell'emulatore. L'[emulatore supporta la modifica nella directory di dati predefinita](local-emulator.md) e usa un percorso noto.

## <a name="next-steps"></a>Passaggi successivi

* È possibile scegliere di aggiungere un secondo livello di crittografia con chiavi personalizzate. Per altre informazioni, vedere l'articolo relativo alle [chiavi gestite dal cliente](how-to-setup-cmk.md).
* Per una panoramica della sicurezza di Cosmos DB e dei miglioramenti più recenti, vedere [Sicurezza di Azure Cosmos DB](database-security.md).
* Per altre informazioni sulle certificazioni Microsoft, vedere il [Centro protezione di Azure](https://azure.microsoft.com/support/trust-center/).
