---
title: Terminologia di Condivisione dati di Azure
description: Informazioni sui termini comuni usati per descrivere le risorse usate in Condivisione dati di Azure (provider di dati, consumer di dati, condivisione dati, sottoscrizione di condivisione, snapshot, invito, destinatario).
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33532380d8f98df44029eeea998130d1da5fdafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73468548"
---
# <a name="azure-data-share-concepts"></a>Concetti relativi alla condivisione dati di AzureAzure Data Share Concepts 

Condivisione dati di Azure introduce una nuova terminologia relativa alla condivisione dei dati. In questo articolo vengono illustrati alcuni termini utilizzati di frequente che possono essere visualizzati utilizzati in tutto il servizio. 

## <a name="data-provider"></a>Provider di dati

Un provider di dati è l'organizzazione che condivide i dati con i consumer. In genere il provider di dati può essere un proprietario o un curatore dei dati. I provider di dati desiderano condividere dati di vari tipi. Alcuni esempi di dati che un provider di dati potrebbe voler condividere includono dati non elaborati, ad esempio dati relativi a punti di vendita o serie temporali. Un provider di dati può anche voler condividere dati pre-elaborati e curati che contengono già analisi e informazioni dettagliate. 

## <a name="data-consumer"></a>Consumatore di dati 

Un consumer di dati è l'organizzazione che riceve dati da un provider di dati. Il consumer di dati potrebbe voler unire i dati condivisi con i propri dati per ricavare informazioni dettagliate. In alcuni casi, il consumatore di dati potrebbe ricevere dati già elaborati. 

## <a name="data-share"></a>Condivisione dati

Una condivisione dati è un gruppo di set di dati condivisi come una singola entità. I set di dati possono provengono da diverse origini dati di Azure supportate dalla condivisione dati di Azure.Datasets can be from a number of Azure data sources that are supported by Azure Data Share. Attualmente, la condivisione dati di Azure supporta Archiviazione BLOB di Azure e Archiviazione lake di Azure.Currently, Azure Data Share supports Azure Blob Storage and Azure Data Lake Store. 

## <a name="share-subscription"></a>Condividi abbonamento 

Una sottoscrizione di condivisione viene creata quando un consumer di dati accetta un invito alla condivisione dati da un provider di dati. I provider di dati possono visualizzare le sottoscrizioni di condivisione attive passando a **Condivisioni inviate** nell'account Condivisione dati di Azure e selezionando **Condividi sottoscrizioni**.

Un consumer di dati può verificare se dispone di una sottoscrizione di condivisione attiva passando a **Condivisioni ricevute** e visualizzando lo stato delle condivisioni ricevute. 

## <a name="snapshot"></a>Snapshot

Uno snapshot può essere creato da un consumer di dati quando accetta un invito alla condivisione dati. Quando accettano un invito, possono attivare uno snapshot completo dei dati condivisi con loro. Lo snapshot è una copia dei dati nel momento in cui il consumer di dati ha generato lo snapshot. 

Esistono due tipi di istantanee: completa e incrementale. Uno snapshot completo contiene tutti i dati all'interno della condivisione dati. Uno snapshot incrementale contiene tutti i dati che sono stati aggiornati/aggiunti dall'ultimo snapshot è stato attivato. 

## <a name="snapshot-settings-in-azure-data-share"></a>Snapshot settings in Azure Data Share
 
Un provider di dati può abilitare un'impostazione snapshot per una condivisione dati. Questa impostazione consente ai consumer di dati di ricevere aggiornamenti incrementali man mano che si verificano. Questa impostazione deve essere abilitata se il provider di dati desidera che i consumer di dati ricevano gli aggiornamenti ai dati condivisi. 

Se un provider di dati abilita questa impostazione, è possibile selezionare un intervallo di ricorrenza. L'intervallo di ricorrenza può essere orario o giornaliero. 

Un consumer di dati ha la possibilità di acconsentire esplicitamente a questa pianificazione snapshot per ricevere aggiornamenti incrementali, inclusi tutti i dati modificati dopo la creazione di un nuovo snapshot. 

## <a name="invitation"></a>Invito

Un provider di dati può invitare più destinatari alla condivisione dati. Possono farlo aggiungendo destinatari alla condivisione dati. Gli inviti possono essere aggiunti anche dopo la creazione di una condivisione dati. 

Un provider di dati può eliminare un invito dopo che è stato inviato se non è stato accettato. Se il provider di dati elimina un invito e non è ancora stato accettato, il consumer di dati non sarà in grado di accettarlo. 

Gli inviti possono essere risentiti fino a cinque volte al giorno. 

## <a name="recipient"></a>Recipient

Un destinatario è una persona che riceve un invito a una condivisione dati. In genere, un provider di dati aggiungerà i destinatari alla condivisione dati creata. Una volta che il destinatario di un invito accetta l'invito, diventa un consumatore di dati.  

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).
