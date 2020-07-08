---
title: Conversione di modelli
description: Descrive il processo di conversione di un modello per il rendering
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: db05c7a5a45221485ecb62c2c90d56be52d5ef48
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808620"
---
# <a name="convert-models"></a>Convertire i modelli

Il rendering remoto di Azure consente di eseguire il rendering di modelli molto complessi. Per ottenere le massime prestazioni, i dati devono essere pre-elaborati in modo da essere in un formato ottimale. A seconda della quantità di dati, questo passaggio potrebbe richiedere alcuni minuti. Se questo tempo è trascorso durante il caricamento del modello, non sarebbe praticabile. Inoltre, sarebbe inutile ripetere questo processo per più sessioni. Per questi motivi, il servizio ARR fornisce un *servizio di conversione*dedicato, che può essere eseguito in anticipo.
Una volta convertito, un modello può essere caricato da un account di archiviazione di Azure.

## <a name="supported-source-formats"></a>Formati di origine supportati

Il servizio di conversione supporta i formati seguenti:

- **FBX** (versione 2011 e successive)
- **GLTF** (versione 2. x)
- **GLB** (versione 2. x)

Esistono differenze minime tra i formati per quanto riguarda la conversione della proprietà Material, come elencato nel capitolo [mapping materiale per i formati di modello](../../reference/material-mapping.md).

## <a name="the-conversion-process"></a>Processo di conversione

1. [Preparare due contenitori di archiviazione BLOB di Azure](blob-storage.md): uno per l'input, uno per l'output
1. Caricare il modello nel contenitore di input (facoltativamente in un sottopercorso)
1. Attivare il processo di conversione tramite [l'API REST di conversione del modello](conversion-rest-api.md)
1. Eseguire il polling del servizio per lo stato della conversione
1. Al termine, caricare un modello
    - da un account di archiviazione collegato (vedere la procedura "collegare gli account di archiviazione" per [creare un account](../create-an-account.md#link-storage-accounts) per collegare il proprio account di archiviazione)
    - oppure fornendo una *firma di accesso condiviso (SAS)*.

Tutti i dati del modello (input e output) vengono archiviati nell'archivio BLOB di Azure fornito dall'utente. Il rendering remoto di Azure offre il controllo completo sulla gestione degli asset.

## <a name="pricing"></a>Prezzi

Per informazioni sui prezzi per la conversione, vedere la pagina relativa ai prezzi per il [rendering remoto](https://azure.microsoft.com/pricing/details/remote-rendering) .


## <a name="conversion-parameters"></a>Parametri di conversione

Per le varie opzioni di conversione, vedere [questo capitolo](configure-model-conversion.md).

## <a name="examples"></a>Esempi

- [Guida introduttiva: convertire un modello per il rendering](../../quickstarts/convert-model.md) è un'introduzione dettagliata su come convertire un modello.
- Gli [script di PowerShell di esempio](../../samples/powershell-example-scripts.md)che illustrano l'uso del servizio di conversione sono disponibili nel [repository di esempi arr](https://github.com/Azure/azure-remote-rendering) nella cartella *Scripts* .

## <a name="next-steps"></a>Passaggi successivi

- [Usare l'archiviazione BLOB di Azure per la conversione dei modelli](blob-storage.md)
- [API REST per la conversione di modelli](conversion-rest-api.md)
- [Configurare la conversione di modelli](configure-model-conversion.md)
- [Mapping del materiale per i formati di modello](../../reference/material-mapping.md)
