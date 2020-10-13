---
author: baanders
description: file di inclusione che descrive come convalidare i modelli di dispositivi gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: da42f847bdd57c4dae0cde673c9c9e08d51a758d
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998316"
---
> [!TIP]
> Dopo aver creato un modello, è consigliabile convalidare i modelli offline prima di caricarli nell'istanza di Azure Digital gemelli.

È disponibile un esempio indipendente dal linguaggio per la convalida dei documenti del modello per verificare che DTDL sia corretto prima di caricarlo nell'istanza. Si trova qui: [**DTDL validator Sample**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

* L'esempio di validator DTDL si basa su una libreria parser .NET DTDL, disponibile in NuGet come libreria lato client: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). È anche possibile usare direttamente la libreria per progettare una soluzione di convalida personalizzata. Quando si usa la libreria del parser, assicurarsi di usare una versione compatibile con la versione che esegue i dispositivi gemelli digitali di Azure. Durante l'anteprima, si tratta della versione *3.12.4*.

Per ulteriori informazioni sull'esempio di validator e sulla libreria del parser, inclusi esempi di utilizzo, vedere [*procedura: analizzare e convalidare i modelli*](../articles/digital-twins/how-to-parse-models.md).