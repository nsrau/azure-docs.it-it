---
title: Tipi di conflitto e criteri di risoluzione dei conflitti in Azure Cosmos DB
description: Questo articolo descrive le categorie di conflitto e i criteri di risoluzione dei conflitti in Azure Cosmos DB.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 5506b27ce56ca7a83ce16aab818767a392d77430
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52680295"
---
# <a name="conflict-types-and-resolution-policies"></a>Tipi di conflitto e criteri di risoluzione dei conflitti

I conflitti e i criteri di risoluzione dei conflitti sono applicabili se l'account Azure Cosmos DB è configurato con più aree di scrittura.

Per gli account Azure Cosmos DB configurati con più aree di scrittura, i conflitti di aggiornamento possono verificarsi quando i processi di scrittura aggiornano contemporaneamente lo stesso elemento in più aree. I conflitti di aggiornamento vengono classificati nei tre tipi seguenti:

* **Conflitti di inserimento**: possono verificarsi quando un'applicazione inserisce contemporaneamente due o più elementi con lo stesso indice univoco da due o più aree. Ad esempio, questo conflitto potrebbe verificarsi con una proprietà ID. Tutte le operazioni di scrittura potrebbero inizialmente avere esito positivo nelle rispettive aree locali. Ma in base ai criteri di risoluzione dei conflitti che si scelgono, alla fine viene eseguito il commit solo di un elemento con l'ID originale.

* **Conflitti di sostituzione**: possono verificarsi quando un'applicazione aggiorna un singolo elemento contemporaneamente da due o più aree.

* **Conflitti di eliminazione**: possono verificarsi quando un'applicazione elimina contemporaneamente un elemento da un'area e lo aggiorna da qualsiasi altra area.

## <a name="conflict-resolution-policies"></a>Criteri di risoluzione dei conflitti

Azure Cosmos DB offre un meccanismo flessibile basato su criteri per risolvere i conflitti di aggiornamento. In un contenitore Azure Cosmos DB è possibile selezionare tra due criteri di risoluzione dei conflitti:

- **Priorità ultima scrittura (LWW)**: questo criterio di risoluzione per impostazione predefinita usa una proprietà Timestamp definita dal sistema. Si basa sul protocollo di sincronizzazione dell'ora dell'orologio. Se si usa l'API di Azure Cosmos DB, è possibile specificare qualsiasi altra proprietà numerica personalizzata da usare per la risoluzione dei conflitti. Una proprietà numerica personalizzata è detta anche percorso di risoluzione dei conflitti. 

  Se due o più elementi sono in conflitto su operazioni di inserimento o sostituzione, l'elemento con il valore massimo per il percorso di risoluzione dei conflitti diventa il vincitore. Il sistema determina il vincitore se più elementi hanno lo stesso valore numerico per il percorso di risoluzione dei conflitti. Tutte le aree convergono sicuramente in un singolo vincitore e si ritrovano con la stessa versione dell'elemento di cui si esegue il commit. Quando sono coinvolti i conflitti di eliminazione, la versione eliminata prevale sempre sui conflitti di inserimento o sostituzione. Questo risultato si verifica indipendentemente dal valore del percorso di risoluzione dei conflitti.

  > [!NOTE]
  > La priorità dell'ultima scrittura è il criterio di risoluzione dei conflitti predefinito. È disponibile per gli account API SQL, tabelle di Azure Cosmos DB, MongoDB, Cassandra e Gremlin.

  Per altre informazioni, vedere gli [esempi d'uso dei criteri di risoluzione dei conflitti LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Personalizzato**: questo criterio di risoluzione è stato progettato per la semantica definita dall'applicazione per la riconciliazione dei conflitti. Quando si imposta questo criterio per il contenitore Azure Cosmos DB, è anche necessario registrare una stored procedure di tipo merge, una routine che viene richiamata automaticamente quando vengono rilevati conflitti in una transazione di database nel server. Il sistema fornisce esattamente una volta la garanzia per l'esecuzione di una routine di tipo merge come parte del protocollo di impegno.  

  Esistono due aspetti da ricordare se si configura il contenitore con l'opzione di risoluzione personalizzata. Se non si registra una routine di tipo merge per il contenitore oppure la routine di merge genera un'eccezione in fase di esecuzione, i conflitti vengono scritti nel feed dei conflitti. L'applicazione deve quindi risolvere manualmente i conflitti nel feed dei conflitti. Per altre informazioni, vedere gli [esempi di utilizzo dei criteri di risoluzione personalizzati e del feed dei conflitti](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > Il criterio personalizzato per la risoluzione dei conflitti è disponibile solo per gli account API SQL.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare i criteri di risoluzione dei conflitti. Vedere gli articoli seguenti:

* [Usare il criterio di risoluzione dei conflitti LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Usare il criterio di risoluzione dei conflitti personalizzato](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Usare il feed dei conflitti](how-to-manage-conflicts.md#read-from-conflict-feed)
