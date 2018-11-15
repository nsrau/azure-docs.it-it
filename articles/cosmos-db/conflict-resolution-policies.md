---
title: Tipi di conflitto e criteri di risoluzione dei conflitti in Azure Cosmos DB
description: Questo articolo descrive le categorie di conflitto e i criteri di risoluzione dei conflitti in Azure Cosmos DB.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: c682b61a39224f2c80db8fe5fa153ea5e5d82922
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958555"
---
# <a name="conflict-types-and-resolution-policies"></a>Tipi di conflitto e criteri di risoluzione dei conflitti

I conflitti e i criteri di risoluzione dei conflitti sono applicabili se l'account Cosmos è configurato con più aree di scrittura.

Per gli account Cosmos DB configurati con più aree di scrittura, i conflitti di aggiornamento possono verificarsi quando più processi di scrittura aggiornano contemporaneamente lo stesso elemento in più aree. I conflitti di aggiornamento vengono classificati nei tre tipi seguenti:

1. **Conflitti di inserimento**: questi conflitti possono verificarsi quando un'applicazione inserisce contemporaneamente due o più elementi con lo stesso indice univoco (ad esempio, proprietà ID) da due o più aree. In questo caso, tutte le operazioni di scrittura possono avere esito positivo inizialmente nelle rispettive aree ma, in base ai criteri di risoluzione dei conflitti scelti, viene eseguito il commit di un solo elemento con l'ID originale.

1. **Conflitti di sostituzione**: questi conflitti possono verificarsi quando un'applicazione aggiorna un singolo elemento contemporaneamente da due o più aree.

1. **Conflitti di eliminazione**: questi conflitti possono verificarsi quando un'applicazione elimina contemporaneamente un elemento da un'area e lo aggiorna da un'altra area.

## <a name="conflict-resolution-policies"></a>Criteri di risoluzione dei conflitti

Cosmos DB offre un meccanismo flessibile basato su criteri per risolvere i conflitti di aggiornamento. In un contenitore Cosmos è possibile selezionare tra i seguenti due criteri di risoluzione dei conflitti:

- **Last-Write-Wins (LWW)** : per impostazione predefinita, questo criterio di risoluzione usa una proprietà timestamp definita dal sistema (basata sul protocollo dell'orologio di sincronizzazione dell'ora). In alternativa, quando si usa l'API SQL, Cosmos DB consente di specificare altre proprietà numeriche personalizzate (dette anche "percorsi di risoluzione dei conflitti") da usare per la risoluzione dei conflitti.  

  Se due o più elementi sono in conflitto su operazioni di inserimento o sostituzione, l'elemento che contiene il valore massimo per il “percorso di risoluzione dei conflitti” diventa il “vincitore”. Se più elementi hanno lo stesso valore numerico per il percorso di risoluzione dei conflitti, la versione “vincitrice” selezionata viene determinata dal sistema. Tutte le aree convergono sicuramente in un singolo vincitore e si ritrovano con la versione identica dell'elemento eseguito il commit. In caso di conflitti di eliminazione, la versione eliminata prevale sempre sui conflitti di inserimento o sostituzione, indipendentemente dal valore del percorso di risoluzione dei conflitti.

  > [!NOTE]
  > LWW è il criterio di risoluzione dei conflitti predefinito ed è disponibile per gli account delle API SQL, Tabella, MongoDB, Cassandra e Gremlin.

  Per ulteriori informazioni, vedere [esempi d'uso dei criteri di risoluzione dei conflitti di LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Personalizzato**: questo criterio di risoluzione è stato progettato per la semantica definita dall'applicazione per la riconciliazione dei conflitti. Durante l'impostazione di questo criterio per il contenitore Cosmos, è necessario anche registrare una stored procedure di tipo merge, che viene richiamata automaticamente in caso di rilevamento di conflitti di aggiornamento in una transazione di database sul server. Il sistema fornisce esattamente una volta la garanzia per l'esecuzione di una routine di tipo merge come parte del protocollo di impegno.  

  Tuttavia, se si configura il contenitore con l'opzione di risoluzione personalizzata ma non si riesce a registrare una routine di tipo merge sul contenitore oppure la routine di merge genera un'eccezione in fase di esecuzione, i conflitti vengono scritti nel feed dei conflitti. L'applicazione dovrà quindi risolvere manualmente i conflitti nel feed dei conflitti. Per ulteriori informazioni, vedere [esempi di utilizzo dei criteri di risoluzione personalizzati e come usare i feed dei conflitti](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > Il criterio personalizzato per la risoluzione dei conflitti è disponibile solo per gli account API SQL.

## <a name="next-steps"></a>Passaggi successivi

Successivamente è possibile imparare a configurare i criteri di risoluzione dei conflitti con i seguenti articoli:

* [Come usare il criterio di risoluzione dei conflitti LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Come usare il criterio di risoluzione dei conflitti personalizzato](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Come usare il feed dei conflitti](how-to-manage-conflicts.md#read-from-conflict-feed)
