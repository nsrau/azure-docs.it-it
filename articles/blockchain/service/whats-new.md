---
title: Novità Note sulla versione-servizio Azure blockchain
description: Informazioni sulle novità del servizio Azure blockchain, ad esempio le note sulla versione più recenti, le versioni, i problemi noti e le modifiche imminenti.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 47e0da5a729519f2af3c5b2a2fd3e0f7485624cf
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948460"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Novità del servizio Azure blockchain

> Consente di ricevere una notifica relativa a quando rivisitare la pagina per gli aggiornamenti copiando e incollando questo URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` nell' [ ![ icona lettore](./media/whats-new/feed-icon-16x16.png)](/api/search/rss?locale=en-us&search=%2522Release%2bnotes%2b-%2bAzure%2bBlockchain%2bService%2522)feed RSS lettore feed RSS.

Il servizio Azure blockchain riceve i miglioramenti su base continuativa. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Nuove funzionalità
- Aggiornamenti della versione
- Problemi noti

---

## <a name="june-2020"></a>Giugno 2020

### <a name="version-upgrades"></a>Aggiornamenti della versione

- Aggiornamento della versione quorum a 2.6.0. Con la versione 2.6.0, è possibile inviare transazioni private firmate. Per ulteriori informazioni sull'invio di transazioni private, vedere la [documentazione dell'API quorum](https://docs.goquorum.com/en/latest/Getting%20Started/api/).
- Aggiornamento della versione tessera a 0.10.5.

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>Dimensioni del contratto e dimensioni delle transazioni aumentate a 128 KB

Tipo: modifica della configurazione

La dimensione del contratto (MaxCodeSize) è stata aumentata a 128 KB, in modo da poter distribuire contratti intelligenti di dimensioni maggiori. Inoltre, le dimensioni delle transazioni (txnSizeLimit) sono state aumentate a 128 KB. Le modifiche alla configurazione si applicano ai nuovi consorzi creati nel servizio Azure blockchain dopo il 19 2020 giugno.

### <a name="trietimeout-value-reduced"></a>Valore TrieTimeout ridotto

Tipo: modifica della configurazione

Il valore TrieTimeout è stato ridotto in modo che lo stato in memoria venga scritto più spesso sul disco. Il valore inferiore garantisce un ripristino più rapido di un nodo nel caso raro di un arresto anomalo del nodo.

## <a name="may-2020"></a>Maggio 2020

### <a name="version-upgrades"></a>Aggiornamenti della versione

- Aggiornamento della versione di Ubuntu a 18,04
- Aggiornamento della versione quorum a 2.5.0
- Aggiornamento della versione tessera 0.10.4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Il servizio Azure blockchain supporta l'invio di transazioni rawPrivate

Tipo: funzionalità

I clienti possono firmare transazioni private al di fuori dell'account nel nodo.

### <a name="two-phase-member-provisioning"></a>Provisioning di membri a due fasi

Tipo: miglioramento

Due fasi consentono di ottimizzare gli scenari in cui un membro viene creato in un consorzio esistente lungo. L'infrastruttura membro viene sottoposta a provisioning nella prima fase. Nella seconda fase, il membro viene sincronizzato con blockchain. Il provisioning in due fasi aiuta a evitare un errore di creazione del membro dovuto a timeout.

## <a name="known-issues"></a>Problemi noti

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>la funzione ETH. estimateGas genera un'eccezione nel quorum v 2.6.0

In quorum v 2.6.0, le chiamate alla funzione *ETH. estimateGas* senza fornire il parametro del *valore* aggiuntivo provocano l' *arresto anomalo* di un gestore di metodo. Il team del quorum è stato notificato ed è prevista una correzione per la fine del 2020 luglio. È possibile utilizzare le soluzioni alternative seguenti fino a quando non è disponibile una correzione:

- Evitare di usare *ETH. estimateGas* poiché può influire sulle prestazioni. Per ulteriori informazioni sui problemi di prestazioni ETH. estimateGas, vedere [chiamata della funzione ETH. estimateGas per ridurre le prestazioni](#calling-ethestimategas-function-reduces-performance). Includere un valore di gas per ogni transazione. La maggior parte delle librerie chiamerà ETH. estimateGas se non viene specificato un valore di gas che causa l'arresto anomalo del 2.6.0 quorum v.
- Se è necessario chiamare *ETH. estimateGas*, il team del quorum suggerisce di passare il *valore* del parametro aggiuntivo come *0* come soluzione alternativa.

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>Il data mining si interrompe se meno di quattro nodi validator

Le reti di produzione devono contenere almeno quattro nodi validator. Il quorum consiglia che siano necessari almeno quattro nodi validator per soddisfare la tolleranza di errore di arresto anomalo di IBFT (3F + 1). È necessario avere almeno due nodi di livello *standard* del servizio blockchain di Azure per ottenere quattro nodi validator. Viene eseguito il provisioning di un nodo standard con due nodi validator.  

Se la rete blockchain nel servizio blockchain di Azure non dispone di quattro nodi validator, il data mining potrebbe arrestarsi sulla rete. È possibile rilevare che il data mining è stato interrotto impostando un avviso sui blocchi elaborati. In una rete integro, il blocco elaborato sarà di 60 blocchi per nodo ogni cinque minuti.

Come mitigazione, il team del servizio Azure blockchain deve riavviare il nodo. I clienti devono aprire una richiesta di supporto per riavviare il nodo. Il team del servizio Azure blockchain sta lavorando per rilevare e correggere automaticamente i problemi di data mining.

Usare il livello *Standard* per le distribuzioni di produzione. Usare il livello *Basic* per lo sviluppo, il test e i modelli di verifica. Dopo la creazione di membri non è possibile passare dal piano tariffario Basic a quello Standard.

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>Blockchain Data Manager richiede un nodo di livello standard

Utilizzare il livello *standard* se si utilizza blockchain Data Manager. Il livello *Basic* ha solo 4 GB di memoria. Di conseguenza, non è in grado di ridimensionarsi in base all'utilizzo richiesto da blockchain Data Manager e da altri servizi in esecuzione su di esso.

Usare il livello *Basic* per lo sviluppo, il test e i modelli di verifica. Dopo la creazione di membri non è possibile passare dal piano tariffario Basic a quello Standard.

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>Un volume elevato di chiamate di account di sblocco causa l'arresto anomalo di Geth

Un volume elevato di chiamate di sblocco account durante l'invio di una transazione può causare l'arresto anomalo di Geth in un nodo di transazione. Di conseguenza, è necessario arrestare l'inserimento delle transazioni. In caso contrario, la coda delle transazioni in sospeso aumenta.

Geth viene riavviato automaticamente entro meno di un minuto. A seconda del nodo, la sincronizzazione potrebbe richiedere molto tempo. Il team del servizio Azure blockchain Abilita una funzionalità di archiviazione che consente di ridurre il tempo di sincronizzazione.

Per identificare gli arresti anomali del Geth, è possibile controllare i log di qualsiasi messaggio di errore nei messaggi di blockchain nei registri applicazioni. È anche possibile controllare se i blocchi elaborati diminuiscono mentre le transazioni in sospeso aumentano.

Per attenuare il problema, inviare le transazioni firmate invece di inviare transazioni non firmate con un comando per sbloccare l'account. Per le transazioni già firmate esternamente, non è necessario sbloccare l'account.

Se si desidera inviare transazioni non firmate, sbloccare l'account per il tempo infinito inviando 0 come parametro time nel comando Unlock. È possibile bloccare l'account dopo l'invio di tutte le transazioni.  

Di seguito sono riportati i parametri Geth usati dal servizio Azure blockchain. Non è possibile modificare questi parametri.

- Periodo di blocco Istanbul: 5 secondi
- Limite di gas Floor: 700 milioni
- Limite di gas massima: 800 milioni

### <a name="large-volume-of-private-transactions-reduces-performance"></a>Un volume elevato di transazioni private riduce le prestazioni

Se si usa il livello Basic del servizio Azure blockchain e le transazioni private, Tess potrebbe arrestarsi in modo anomalo.

È possibile rilevare l'arresto anomalo della tessera riesaminando i registri applicazioni di blockchain e cercando il messaggio `Tessera crashed. Restarting Tessera...` .

Il servizio Azure blockchain riavvia la tessera quando si verifica un arresto anomalo. Il riavvio richiede circa un minuto.

Utilizzare il livello *standard* se si invia un volume elevato di transazioni private. Usare il livello *Basic* per lo sviluppo, il test e i modelli di verifica. Dopo la creazione di membri non è possibile passare dal piano tariffario Basic a quello Standard.

### <a name="calling-ethestimategas-function-reduces-performance"></a>La chiamata della funzione ETH. estimateGas consente di ridurre le prestazioni

La chiamata della funzione *ETH. estimateGas* più volte riduce drasticamente le transazioni al secondo. Non utilizzare la funzione *ETH. estimateGas* per ogni invio di transazione. La funzione *ETH. estimateGas* è a elevato utilizzo di memoria.

Se possibile, usare un valore di gas conservativo per inviare le transazioni e ridurre al minimo l'uso di *ETH. estimateGas*.

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>Cicli senza limiti nei contratti intelligenti riduce le prestazioni

Evitare i cicli non associati nei contratti intelligenti poiché possono ridurre le prestazioni. Per altre informazioni, vedere le seguenti risorse:

- [Evitare cicli non vincolati](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [Procedure consigliate per la sicurezza con Smart Contract](https://github.com/ConsenSys/smart-contract-best-practices)
- [Linee guida per Smart Contract fornite dal quorum](http://docs.goquorum.com/en/latest/Security/Framework/Decentralized%20Application/Smart%20Contracts%20Security/)
- [Linee guida sui limiti e i cicli di gas forniti dalla solidità](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)