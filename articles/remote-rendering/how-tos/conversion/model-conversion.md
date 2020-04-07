---
title: Conversione del modello
description: Descrive il processo di conversione di un modello per il rendering
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 135c58a665779ffaad8750ffe618bdbe38639b66
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681493"
---
# <a name="convert-models"></a>Convertire i modelli

Il rendering remoto di Azure consente di eseguire il rendering di modelli molto complessi. Per ottenere le massime prestazioni, i dati devono essere pre-elaborati per essere in un formato ottimale. A seconda della quantità di dati, questo passaggio potrebbe richiedere un po' di tempo. Sarebbe poco pratico, se questo tempo è stato speso durante il caricamento del modello. Inoltre, sarebbe uno spreco ripetere questo processo per più sessioni. Per questi motivi, il servizio ARR fornisce un servizio di *conversione*dedicato, che è possibile eseguire in anticipo.
Una volta convertito, un modello può essere caricato da un account di archiviazione di Azure.Once converted, a model can be loaded from an Azure Storage Account.

## <a name="supported-source-formats"></a>Formati di origine supportati

Il servizio di conversione supporta i seguenti formati:

- **FBX** (versione 2011 e successive)
- **GLTF** (versione 2.x)
- **GLB** (versione 2.x)

Esistono piccole differenze tra i formati per quanto riguarda la conversione delle proprietà dei materiali, come elencato nella [mappatura](../../reference/material-mapping.md)del materiale del capitolo per i formati del modello .

## <a name="the-conversion-process"></a>Il processo di conversione

1. [Preparare due contenitori](blob-storage.md)di archiviazione BLOB di Azure: uno per l'input, uno per l'outputPrepare two Azure Blob Storage containers : one for input, one for output
1. Caricare il modello nel contenitore di input (facoltativo sottoun sottopercorso)Upload your model to the input container (optionally under a subpath)
1. Attivare il processo di conversione tramite [l'API REST di conversione del modelloTrigger](conversion-rest-api.md) the conversion process through the model conversion REST API
1. Eseguire il polling del servizio per l'avanzamento della conversione
1. Al termine, caricare un modello
    - da un account di archiviazione collegato (vedere la procedura "Collega account di archiviazione" in Creare un account per collegare l'account di archiviazione)From a linked storage account (see the "Link storage accounts" steps on [Create an Account](../create-an-account.md#link-storage-accounts) to link your storage account)
    - o fornendo una firma di *accesso condiviso (SAS)*.

Tutti i dati del modello (input e output) vengono archiviati nell'archiviazione BLOB di Azure fornita dall'utente. Il rendering remoto di Azure offre il controllo completo sulla gestione delle risorse.

## <a name="conversion-parameters"></a>Parametri di conversione

Per le varie opzioni di conversione, consultate [questo capitolo.](configure-model-conversion.md)

## <a name="examples"></a>Esempi

- [Guida introduttiva: Convertire un modello per il rendering](../../quickstarts/convert-model.md) è un'introduzione dettagliata come convertire un modello.
- [Gli script di PowerShell](../../samples/powershell-example-scripts.md)di esempio, che illustrano l'utilizzo del servizio di conversione, sono disponibili nel [repository degli esempi ARR](https://github.com/Azure/azure-remote-rendering) nella cartella *Script.*

## <a name="next-steps"></a>Passaggi successivi

- [Usare Archiviazione BLOB di Azure per la conversione dei modelliUse Azure Blob Storage for model conversion](blob-storage.md)
- [L'API REST di conversione del modelloThe model conversion REST API](conversion-rest-api.md)
- [Configurazione della conversione del modello](configure-model-conversion.md)
- [Mappatura dei materiali per i formati di modello](../../reference/material-mapping.md)
