---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75564757"
---
## <a name="register-extensions"></a>Registrare le estensioni

Ad eccezione dei trigger HTTP e timer, le associazioni di funzioni in fase di esecuzione versione 2.x e successive vengono implementate come pacchetti di estensione. Nella versione 2.x e successive del runtime di Funzioni di Azure, è necessario registrare in modo esplicito le estensioni per i tipi di associazione usati nelle funzioni. Le eccezioni sono le associazioni HTTP e i trigger timer, che non richiedono estensioni.

È possibile scegliere di installare le estensioni di associazione singolarmente oppure aggiungere un riferimento al pacchetto di estensione al file di progetto host.json. I pacchetti di estensione eliminano la possibilità di avere problemi di compatibilità dei pacchetti quando si usano più tipi di associazione. È l'approccio consigliato per la registrazione delle estensioni di associazione. I pacchetti di estensione eliminano anche il requisito di installazione di .NET Core 2.x SDK. 

### <a name="extension-bundles"></a>Pacchetti di estensione

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Per altre informazioni, vedere [Registrare le estensioni](../articles/azure-functions/functions-bindings-register.md#extension-bundles)di associazione di Funzioni di Azure.To learn more, see Register Azure Functions binding extensions . È necessario aggiungere pacchetti di estensione a host.json prima di aggiungere associazioni al file function.json.

### <a name="register-individual-extensions"></a>Registrare singole estensioni

Se è necessario installare estensioni non in un bundle, è possibile registrare manualmente singoli pacchetti di estensione per associazioni specifiche. 

> [!NOTE]
> Per registrare manualmente `func extensions install`le estensioni utilizzando , è necessario che sia installato .NET Core 2.x SDK.

Dopo avere aggiornato il file *function.json* per includere tutte le associazioni necessarie per la funzione, eseguire il seguente comando nella cartella del progetto.

```bash
func extensions install
```

Il comando legge il file *function.json* per scoprire quali pacchetti sono necessari, li installa e ricompila il progetto di estensione. Consente di aggiungere eventuali nuove associazioni alla versione corrente, ma non aggiorna le associazioni esistenti. Usare l'opzione `--force` per aggiornare le associazioni esistenti alla versione più recente quando si installano quelle nuove.
