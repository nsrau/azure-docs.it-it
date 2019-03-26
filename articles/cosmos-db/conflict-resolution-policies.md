---
title: Tipi di conflitto e criteri di risoluzione dei conflitti con più aree di scrittura in Azure Cosmos DB
description: Questo articolo descrive le categorie di conflitto e i criteri di risoluzione dei conflitti in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 24201cfd657d4f23eb962b7407ed20262d780cf7
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407420"
---
# <a name="conflict-types-and-resolution-policies"></a>Tipi di conflitto e criteri di risoluzione dei conflitti

I conflitti e i criteri di risoluzione dei conflitti sono applicabili se l'account Azure Cosmos DB è configurato con più aree di scrittura.

Per gli account Azure Cosmos configurati con più aree di scrittura, possono verificarsi conflitti di aggiornamento quando i writer aggiornano contemporaneamente lo stesso elemento in più aree. Conflitti di aggiornamento possono essere di tre tipi seguenti:

* **Conflitti di inserimento**: Questi conflitti possono verificarsi quando un'applicazione inserisce contemporaneamente due o più elementi con lo stesso indice univoco in due o più aree. Ad esempio, questo conflitto può verificarsi con la proprietà ID.

* **Conflitti di sostituzione**: Questi conflitti possono verificarsi quando un'applicazione aggiorna lo stesso elemento contemporaneamente in due o più aree.

* **Conflitti di eliminazione**: Questi conflitti possono verificarsi quando un'applicazione aggiorna in un'altra area e consente di eliminare un elemento in un'unica area simultaneamente.

## <a name="conflict-resolution-policies"></a>Criteri di risoluzione dei conflitti

Azure Cosmos DB offre un meccanismo basato sui criteri flessibile per risolvere i conflitti di scrittura. È possibile scegliere tra due criteri di risoluzione dei conflitti in un contenitore di Azure Cosmos:

- **Priorità ultima scrittura (LWW)**: questo criterio di risoluzione per impostazione predefinita usa una proprietà Timestamp definita dal sistema. Si basa sul protocollo di sincronizzazione dell'ora dell'orologio. Se si usa l'API SQL, è possibile specificare qualsiasi altra numeriche proprietà personalizzata (ad esempio, il proprio nozione di un timestamp) da utilizzare per la risoluzione dei conflitti. Una proprietà numerica personalizzata è detta anche il *percorso di risoluzione dei conflitti*. 

  Se due o più elementi sono in conflitto su operazioni di inserimento o sostituzione, l'elemento con il valore massimo per il percorso di risoluzione dei conflitti diventa il vincitore. Il sistema determina il vincitore se più elementi hanno lo stesso valore numerico per il percorso di risoluzione dei conflitti. Tutte le aree convergono sicuramente in un singolo vincitore e si ritrovano con la stessa versione dell'elemento di cui si esegue il commit. Quando sono coinvolti i conflitti di eliminazione, la versione eliminata prevale sempre sui conflitti di inserimento o sostituzione. Questo risultato si verifica indipendentemente dal valore del percorso di risoluzione dei conflitti.

  > [!NOTE]
  > La priorità dell'ultima scrittura è il criterio di risoluzione dei conflitti predefinito. È disponibile per le API seguenti: SQL, MongoDB, Cassandra, Gremlin e tabella.

  Per altre informazioni, vedere gli [esempi d'uso dei criteri di risoluzione dei conflitti LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Personalizzato**: questo criterio di risoluzione è stato progettato per la semantica definita dall'applicazione per la riconciliazione dei conflitti. Quando si imposta questo criterio per il contenitore di Azure Cosmos, è anche necessario registrare un' *stored procedure di tipo merge*. Questa procedura viene richiamata automaticamente quando vengono rilevati i conflitti in una transazione di database nel server. Il sistema fornisce esattamente una volta la garanzia per l'esecuzione di una routine di tipo merge come parte del protocollo di impegno.  

  Se si configura il contenitore con l'opzione di risoluzione personalizzato e non si riesce a registrare una procedura di tipo merge per il contenitore o la procedura di tipo merge viene generata un'eccezione in fase di esecuzione, i conflitti vengono scritti i *conflitti feed*. L'applicazione deve quindi risolvere manualmente i conflitti nel feed dei conflitti. Per altre informazioni, vedere gli [esempi di utilizzo dei criteri di risoluzione personalizzati e del feed dei conflitti](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > Il criterio personalizzato per la risoluzione dei conflitti è disponibile solo per gli account API SQL.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare i criteri di risoluzione conflitto:

* [Come configurare funzionalità multimaster nelle applicazioni](how-to-multi-master.md)
* [Come usare il criterio di risoluzione dei conflitti LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Come usare il criterio di risoluzione dei conflitti personalizzato](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Come leggere dal feed in conflitto](how-to-manage-conflicts.md#read-from-conflict-feed)
