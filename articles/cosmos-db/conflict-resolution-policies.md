---
title: Risoluzione dei conflitti in Azure Cosmos DB
description: Questo articolo descrive le categorie di conflitto e i criteri di risoluzione dei conflitti in Azure Cosmos DB.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 2f219ed6c3155e8b7d3d978116e1748f6c115fea
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243886"
---
# <a name="conflict-types-and-resolution-policies"></a>Tipi di conflitto e criteri di risoluzione dei conflitti

I conflitti e i criteri di risoluzione dei conflitti sono applicabili se l'account Cosmos è configurato con più aree di scrittura.

Per gli account Cosmos DB configurati con più aree di scrittura, i conflitti di aggiornamento possono verificarsi quando più processi di scrittura aggiornano contemporaneamente lo stesso elemento in più aree. I conflitti di aggiornamento vengono classificati nei tre tipi seguenti:

1. I **conflitti di inserimento** possono verificarsi quando un'applicazione inserisce contemporaneamente due o più elementi con lo stesso indice univoco (ad esempio, proprietà ID) da due o più aree. In questo caso, tutte le operazioni di scrittura possono avere esito positivo inizialmente nelle rispettive aree ma, in base ai criteri di risoluzione dei conflitti scelti, viene eseguito il commit di un solo elemento con l'ID originale.
2. I **conflitti di sostituzione** possono verificarsi quando un'applicazione aggiorna un singolo elemento contemporaneamente da due o più aree.
3. I **conflitti di eliminazione** possono verificarsi quando un'applicazione elimina contemporaneamente un elemento da un'area e lo aggiorna da qualsiasi altra area.

## <a name="conflict-resolution-policies"></a>Criteri di risoluzione dei conflitti

Cosmos DB offre un meccanismo flessibile basato sui criteri per la risoluzione dei conflitti di aggiornamento. In un contenitore Cosmos è possibile selezionare tra i seguenti due criteri di risoluzione dei conflitti:

- **Last-Write-Wins (LWW)** che, per impostazione predefinita, utilizza una proprietà timestamp definita dal sistema (basata sul protocollo dell'orologio di sincronizzazione dell'ora). In alternativa, quando si usa l'API SQL, Cosmos DB consente di specificare qualsiasi altra proprietà numerica personalizzata (detta anche “percorso di risoluzione dei conflitti”) da utilizzare per la risoluzione dei conflitti.  

Se due o più elementi sono in conflitto su operazioni di inserimento o sostituzione, l'elemento che contiene il valore massimo per il “percorso di risoluzione dei conflitti” diventa il “vincitore”. Se più elementi hanno lo stesso valore numerico per il percorso di risoluzione dei conflitti, la versione “vincitrice” selezionata viene determinata dal sistema. Tutte le aree convergono sicuramente in un singolo vincitore e si ritrovano con la versione identica dell'elemento eseguito il commit. In caso di conflitti di eliminazione, la versione eliminata prevale sempre sui conflitti di inserimento o sostituzione, indipendentemente dal valore del percorso di risoluzione dei conflitti.

> [!NOTE]
> LWW è il criterio di risoluzione dei conflitti predefinito ed è disponibile per le API SQL, Table, MongoDB, Cassandra e Gremlin.

Per ulteriori informazioni, vedere [esempi d'uso dei criteri di risoluzione dei conflitti di LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Personalizzato** Questo criterio è progettato per la semantica definita dall'applicazione per la riconciliazione dei conflitti. Durante l'impostazione di questo criterio per il contenitore Cosmos, è necessario anche registrare una stored procedure di tipo merge, che viene richiamata automaticamente al rilevamento di conflitti di aggiornamento in una transazione di database sul lato server. Il sistema fornisce esattamente una volta la garanzia per l'esecuzione di una routine di tipo merge come parte del protocollo di impegno.  

Tuttavia, se si configura il contenitore con l'opzione di risoluzione personalizzata ma non si riesce a registrare una routine di tipo merge sul contenitore oppure la routine di merge genera un'eccezione in fase di esecuzione, i conflitti vengono scritti nel feed dei conflitti. L'applicazione dovrà quindi risolvere manualmente i conflitti nel feed dei conflitti. Per ulteriori informazioni, vedere [esempi di utilizzo dei criteri di risoluzione personalizzati e come usare i feed dei conflitti](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

> [!NOTE]
> Il criterio personalizzato per la risoluzione dei conflitti è disponibile solo per gli account API SQL.

## <a name="next-steps"></a>Passaggi successivi

Successivamente è possibile imparare a configurare i criteri di risoluzione dei conflitti con i seguenti articoli:

* [Come usare il criterio di risoluzione dei conflitti LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Come usare il criterio di risoluzione dei conflitti personalizzato](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Come usare il feed dei conflitti](how-to-manage-conflicts.md#read-from-conflict-feed)
