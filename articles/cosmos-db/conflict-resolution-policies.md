---
title: Tipi di conflitto e criteri di risoluzione dei conflitti in Azure Cosmos DB
description: Questo articolo descrive le categorie di conflitto e i criteri di risoluzione dei conflitti in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ba55d88de3a5a4087db30613b22a7d2441de9be1
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334379"
---
# <a name="conflict-types-and-resolution-policies-when-using-multiple-write-regions"></a>Tipi di conflitti e criteri di risoluzione quando si usano più aree di scrittura
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

I conflitti e i criteri di risoluzione dei conflitti sono applicabili se l'account Azure Cosmos DB è configurato con più aree di scrittura.

Per gli account Azure Cosmos configurati con più aree di scrittura, è possibile che si verifichino conflitti di aggiornamento quando i writer aggiornano contemporaneamente lo stesso elemento in più aree. I conflitti di aggiornamento possono essere dei tre tipi seguenti:

* **Conflitti di inserimento** : questi conflitti possono verificarsi quando un'applicazione inserisce contemporaneamente due o più elementi con lo stesso indice univoco in due o più aree. Ad esempio, questo conflitto può verificarsi con una proprietà ID.

* **Sostituisci conflitti** : questi conflitti possono verificarsi quando un'applicazione aggiorna contemporaneamente lo stesso elemento in due o più aree.

* **Conflitti di eliminazione** : questi conflitti possono verificarsi quando un'applicazione Elimina simultaneamente un elemento in un'area e lo aggiorna in un'altra area.

## <a name="conflict-resolution-policies"></a>Criteri di risoluzione dei conflitti

Azure Cosmos DB offre un meccanismo flessibile basato sui criteri per risolvere i conflitti di scrittura. È possibile scegliere tra due criteri di risoluzione dei conflitti in un contenitore di Azure Cosmos:

* **Ultima scrittura WINS (LWW)** : per impostazione predefinita, questo criterio di risoluzione usa una proprietà timestamp definita dal sistema. Si basa sul protocollo di sincronizzazione dell'ora dell'orologio. Se si usa l'API SQL, è possibile specificare qualsiasi altra proprietà numerica personalizzata (ad esempio, la propria nozione di un timestamp) da usare per la risoluzione dei conflitti. Una proprietà numerica personalizzata viene anche definita *percorso di risoluzione dei conflitti*. 

  Se due o più elementi sono in conflitto su operazioni di inserimento o sostituzione, l'elemento con il valore massimo per il percorso di risoluzione dei conflitti diventa il vincitore. Il sistema determina il vincitore se più elementi hanno lo stesso valore numerico per il percorso di risoluzione dei conflitti. Tutte le aree convergeranno a un singolo vincitore e finiranno con la stessa versione dell'elemento di cui è stato eseguito il commit. Quando sono coinvolti i conflitti di eliminazione, la versione eliminata prevale sempre sui conflitti di inserimento o sostituzione. Questo risultato si verifica indipendentemente dal valore del percorso di risoluzione dei conflitti.

  > [!NOTE]
  > WINS Last Write è il criterio di risoluzione dei conflitti predefinito e USA `_ts` il timestamp per le API seguenti: SQL, MongoDB, Cassandra, Gremlin e Table. La proprietà numerica personalizzata è disponibile solo per l'API SQL.

  Per altre informazioni, vedere gli [esempi d'uso dei criteri di risoluzione dei conflitti LWW](how-to-manage-conflicts.md).

* **Personalizzata** : questo criterio di risoluzione è progettato per la semantica definita dall'applicazione per la riconciliazione dei conflitti. Quando si impostano questi criteri nel contenitore Azure Cosmos, è necessario registrare anche un *stored procedure di merge*. Questa procedura viene richiamata automaticamente quando vengono rilevati conflitti in una transazione di database nel server. Il sistema fornisce esattamente una volta la garanzia per l'esecuzione di una routine di tipo merge come parte del protocollo di impegno.  

  Se si configura il contenitore con l'opzione di risoluzione personalizzata e non si riesce a registrare una procedura di merge nel contenitore o se la procedura di merge genera un'eccezione in fase di esecuzione, i conflitti vengono scritti nel *feed dei conflitti*. L'applicazione deve quindi risolvere manualmente i conflitti nel feed dei conflitti. Per altre informazioni, vedere gli [esempi di utilizzo dei criteri di risoluzione personalizzati e del feed dei conflitti](how-to-manage-conflicts.md).

  > [!NOTE]
  > Il criterio personalizzato per la risoluzione dei conflitti è disponibile solo per gli account API SQL.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare i criteri di risoluzione dei conflitti:

* [Come configurare più aree di scrittura per le applicazioni](how-to-multi-master.md)
* [Come gestire i criteri di risoluzione dei conflitti](how-to-manage-conflicts.md)
* [Come leggere dal feed dei conflitti](how-to-manage-conflicts.md#read-from-conflict-feed)
