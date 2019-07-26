---
title: Terminologia di anteprima della condivisione dati di Azure
description: Terminologia di anteprima della condivisione dati di Azure
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 01025acfa2fd939db8134a1e61c7902034a1d2c3
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424225"
---
# <a name="azure-data-share-preview-concepts"></a>Concetti di anteprima della condivisione di dati di Azure 

L'anteprima della condivisione dati di Azure introduce una nuova terminologia relativa alla condivisione dei dati. Questo articolo illustra alcuni termini usati di frequente che potrebbero essere usati in tutto il servizio. 

## <a name="data-provider"></a>Provider di dati

Un provider di dati è l'organizzazione che condivide i dati con i consumer. In genere, il provider di dati può essere un proprietario o un curatore dei dati. I provider di dati desiderano condividere dati di vari tipi. Alcuni esempi di dati che un provider di dati può condividere includono dati non elaborati, ad esempio punti di vendita o dati di serie temporali. Un provider di dati può anche voler condividere dati preelaborati e curati che già contengono analisi e informazioni dettagliate. 

## <a name="data-consumer"></a>Consumer di dati 

Un consumer di dati è l'organizzazione che riceve dati da un provider di dati. Il consumer di dati potrebbe voler unire i dati condivisi con i propri dati per derivare informazioni dettagliate. In alcuni casi, il consumer di dati potrebbe ricevere dati già elaborati. 

## <a name="data-share"></a>Condivisione dati

Una condivisione dati è un gruppo di set di dati condivisi come una singola entità. I set di dati possono provenire da diverse origini dati di Azure supportate dalla condivisione dati di Azure. Attualmente, la condivisione di dati di Azure supporta l'archiviazione BLOB di Azure e Azure Data Lake Store. 

## <a name="share-subscription"></a>Condividi sottoscrizione 

Una sottoscrizione di condivisione viene creata quando un consumer di dati accetta un invito alla condivisione dati da un provider di dati. I provider di dati possono visualizzare le sottoscrizioni di condivisione attive passando a **condivisioni inviate** nell'account di condivisione dati di Azure e selezionando **Condividi sottoscrizioni**.

Un consumer di dati può verificare se dispone di una sottoscrizione di condivisione attiva passando  alle condivisioni ricevute e visualizzando lo stato delle condivisioni ricevute. 

## <a name="snapshot"></a>Snapshot

Uno snapshot può essere creato da un consumer di dati quando accetta un invito per la condivisione di dati. Quando accettano un invito, possono attivare uno snapshot completo dei dati condivisi. Lo snapshot è una copia dei dati nel momento in cui il consumer di dati ha generato lo snapshot. 

Esistono due tipi di snapshot: completo e incrementale. Uno snapshot completo contiene tutti i dati all'interno della condivisione di dati. Uno snapshot incrementale contiene tutti i dati che sono stati aggiornati o aggiunti dopo l'attivazione dell'ultimo snapshot. 

## <a name="snapshot-settings-in-azure-data-share"></a>Impostazioni snapshot nella condivisione dati di Azure
 
Un provider di dati può abilitare un'impostazione snapshot per una condivisione di dati. Questa impostazione consente ai consumer di dati di ricevere aggiornamenti incrementali Man mano che si verificano. Questa impostazione deve essere abilitata se il provider di dati desidera che i consumer di dati ricevano gli aggiornamenti ai dati condivisi. 

Se un provider di dati Abilita questa impostazione, è possibile selezionare un intervallo di ricorrenza. L'intervallo di ricorrenza può essere ogni ora o ogni giorno. 

Un consumer di dati ha la possibilità di acconsentire esplicitamente a questa pianificazione dello snapshot per ricevere aggiornamenti incrementali, che include tutti i dati che sono stati modificati dopo la prima generazione di un nuovo snapshot. 

## <a name="invitation"></a>Invito

Un provider di dati può invitare più destinatari nella propria condivisione di dati. A tale scopo, è possibile aggiungere i destinatari alla condivisione dati. Gli inviti possono essere aggiunti anche dopo la creazione di una condivisione di dati. 

Un provider di dati può eliminare un invito dopo che è stato inviato. Si noti che se un provider di dati Elimina un invito dopo che è stato accettato, il consumer di dati può comunque disporre di una sottoscrizione di condivisione attiva. Se il provider di dati Elimina un invito e non è ancora stato accettato, il consumer di dati non sarà in grado di accettarlo. 

## <a name="recipient"></a>Destinatario

Un destinatario è un utente che riceve un invito a una condivisione di dati. In genere, un provider di dati aggiungerà i destinatari alla condivisione dati che crea. Una volta che il destinatario di un invito accetta l'invito, diventa un consumer di dati.  

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).

