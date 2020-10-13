---
author: baanders
description: file di inclusione che descrive come convalidare i modelli di dispositivi gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 0146660fa50f9a136cd82483200ca2e6c6cc0b89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87985878"
---
> [!TIP]
> Dopo aver creato un modello, è consigliabile convalidare i modelli offline prima di caricarli nell'istanza di Azure Digital gemelli.

È disponibile un esempio indipendente dal linguaggio per la convalida dei documenti del modello per verificare che DTDL sia corretto. Si trova qui: [**DTDL validator Sample**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

L'esempio di validator DTDL si basa su una libreria parser .NET DTDL, disponibile in NuGet come libreria lato client: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). È anche possibile usare direttamente la libreria per progettare una soluzione di convalida personalizzata. Quando si usa la libreria del parser, assicurarsi di usare una versione compatibile con la versione che esegue i dispositivi gemelli digitali di Azure. Durante l'anteprima, si tratta della versione *3.7.0*.

Per ulteriori informazioni sull'esempio di validator e sulla libreria del parser, inclusi esempi di utilizzo, vedere [*procedura: analizzare e convalidare i modelli*](../articles/digital-twins/how-to-parse-models.md).