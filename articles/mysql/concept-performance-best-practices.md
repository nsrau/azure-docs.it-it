---
title: Procedure consigliate per le prestazioni-database di Azure per MySQL
description: Questo articolo descrive le procedure consigliate per monitorare e ottimizzare le prestazioni del database di Azure per MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: c29c043a3af46086751629b31ce68217e7226442
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354992"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Procedure consigliate per le prestazioni ottimali del database di Azure per MySQL-server singolo

Informazioni sulle procedure consigliate per ottenere prestazioni ottimali durante l'uso di database di Azure per MySQL-singolo server. Quando si aggiungono nuove funzionalità alla piattaforma, verranno rifinite le procedure consigliate descritte in questa sezione.

## <a name="physical-proximity"></a>Prossimità fisica

 Assicurarsi di distribuire un'applicazione e il database nella stessa area. Un controllo rapido prima di avviare un'esecuzione di benchmark delle prestazioni consiste nel determinare la latenza di rete tra il client e il database usando una semplice query SELECT 1. 

## <a name="accelerated-networking"></a>Rete accelerata

Usare la rete accelerata per il server applicazioni se si usa la macchina virtuale di Azure, Azure Kubernetes o servizi app. La funzionalità Rete accelerata abilita Single Root I/O Virtualization (SR-IOV) per le macchine virtuali (VM), migliorandone le prestazioni di rete. Questo percorso a prestazioni elevate esclude l'host dal percorso dati, riducendo così la latenza, l'instabilità e l'utilizzo della CPU e può essere usato con i carichi di lavoro di rete più impegnativi nei tipi di VM supportati.

## <a name="connection-efficiency"></a>Efficienza della connessione

La creazione di una nuova connessione è sempre un'attività costosa e dispendiosa in termini di tempo. Quando un'applicazione richiede una connessione al database, l'allocazione delle connessioni al database inattivo esistente viene assegnata in ordine di priorità anziché crearne una nuova.  Di seguito sono riportate alcune opzioni per le buone procedure di connessione:

- **ProxySQL**: USA [ProxySQL](https://proxysql.com/) che fornisce pool di connessioni integrato e [bilancia il carico del carico di lavoro](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) a più repliche di lettura, in base alle esigenze, con eventuali modifiche nel codice dell'applicazione.

- **Heimdall data proxy**: in alternativa, è possibile usare anche Heimdall data proxy, una soluzione proxy proprietaria indipendente dal fornitore. Supporta la memorizzazione nella cache delle query e la suddivisione in lettura/scrittura con il rilevamento del ritardo di replica. È anche possibile fare riferimento a come [accelerare le prestazioni di MySQL con il proxy Heimdall](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349).  

- **Connessione persistente o Long-Lived**: se l'applicazione dispone di transazioni o query brevi in genere con tempi di esecuzione < 5-10 ms, sostituire le connessioni brevi con le connessioni permanenti. La sostituzione di connessioni brevi con connessioni permanenti richiede solo modifiche minime al codice, ma ha un effetto significativo in termini di miglioramento delle prestazioni in molti scenari di applicazione tipici. Assicurarsi di impostare il timeout o chiudere la connessione al termine della transazione.

- **Replica**: se si usa la replica, usare [ProxySQL](https://proxysql.com/) per bilanciare il carico tra il server primario e il server di replica secondaria leggibile. Informazioni su [come configurare ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847).

## <a name="data-import-configurations"></a>Configurazioni di importazione dati

- È possibile ridimensionare temporaneamente l'istanza a dimensioni dello SKU superiori prima di avviare un'operazione di importazione dei dati e quindi ridimensionarla quando l'importazione ha esito positivo.
- È possibile importare i dati con tempi di inattività minimi usando il [servizio migrazione del database di Azure](https://datamigration.microsoft.com/) per le migrazioni in linea o non in linea. 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Consigli sulla memoria per database di Azure per MySQL

Una procedura consigliata per le prestazioni di database di Azure per MySQL consiste nell'allocare una quantità di RAM sufficiente, in modo che i working set risiedano quasi completamente in memoria. 

- Controllare se la percentuale di memoria usata per raggiungere i [limiti](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) usando le [metriche per il server MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring). 
- Configurare gli avvisi per tali numeri per assicurarsi che, quando i server raggiungono i limiti, è possibile eseguire le azioni richieste per risolvere il problema. In base ai limiti definiti, verificare se è in aumento la scalabilità verticale dello SKU del database, a una dimensione di calcolo superiore o a un piano tariffario migliore che comporta un notevole aumento delle prestazioni. 
- Scalabilità verticale fino a quando i numeri delle prestazioni non vengono più eliminati in modo significativo dopo un'operazione di ridimensionamento. Per informazioni sul monitoraggio delle metriche di un'istanza di database, vedere [metriche del database MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring#metrics).

## <a name="next-steps"></a>Passaggi successivi

- [Procedura consigliata per le operazioni server con database di Azure per MySQL](concept-operation-excellence-best-practices.md) <br/>
- [Procedura consigliata per il monitoraggio del database di Azure per MySQL](concept-monitoring-best-practices.md)<br/>
- [Introduzione a database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>
