---
title: includere il file
description: includere file
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75564757"
---
## <a name="register-extensions"></a>Registrare le estensioni

Fatta eccezione per i trigger HTTP e timer, le associazioni di funzioni in Runtime versione 2. x e successive vengono implementate come pacchetti di estensione. Nella versione 2. x e oltre al runtime di funzioni di Azure, è necessario registrare in modo esplicito le estensioni per i tipi di binding usati nelle funzioni. Le eccezioni sono associazioni HTTP e trigger timer, che non richiedono estensioni.

È possibile scegliere di installare le estensioni di binding singolarmente oppure è possibile aggiungere un riferimento al bundle di estensione all'host.jsnel file di progetto. I bundle di estensione rimuovono la possibilità di problemi di compatibilità dei pacchetti quando si usano più tipi di binding. Si tratta dell'approccio consigliato per la registrazione delle estensioni di binding. I bundle di estensione eliminano anche il requisito di installazione di .NET Core 2. x SDK. 

### <a name="extension-bundles"></a>Bundle di estensione

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Per altre informazioni, vedere [registrare le estensioni di binding di funzioni di Azure](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Prima di aggiungere binding al function.jssu file, è necessario aggiungere i bundle di estensione al host.js.

### <a name="register-individual-extensions"></a>Registrare singole estensioni

Se è necessario installare le estensioni che non sono incluse in un bundle, è possibile registrare manualmente i singoli pacchetti di estensione per binding specifici. 

> [!NOTE]
> Per registrare manualmente le estensioni usando `func extensions install` , è necessario che sia installato .NET Core 2. x SDK.

Dopo avere aggiornato il file *function.json* per includere tutte le associazioni necessarie per la funzione, eseguire il seguente comando nella cartella del progetto.

```bash
func extensions install
```

Il comando legge il file *function.json* per scoprire quali pacchetti sono necessari, li installa e ricompila il progetto di estensione. Consente di aggiungere eventuali nuove associazioni alla versione corrente, ma non aggiorna le associazioni esistenti. Usare l'opzione `--force` per aggiornare le associazioni esistenti alla versione più recente quando si installano quelle nuove.
