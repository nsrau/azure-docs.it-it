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
ms.openlocfilehash: ffd9e54c0f39b4256dbc83a336328797a8b53c45
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608234"
---
## <a name="register-extensions"></a>Registrare le estensioni

Fatta eccezione per i trigger di timer e HTTP, associazioni di funzioni in versione di runtime 2.x vengono implementati come pacchetti di estensione. Nella versione 2.x del runtime di funzioni di Azure, è necessario registrare in modo esplicito le estensioni per i tipi di associazione usati nelle funzioni. Le eccezioni sono le associazioni HTTP e i trigger di timer, che non richiedono le estensioni.

È possibile scegliere di installare le estensioni delle associazioni singolarmente oppure è possibile aggiungere un riferimento di bundle dell'estensione per il file di progetto host. JSON. Bundle di estensione rimuove la probabilità che i problemi di compatibilità dei pacchetti quando si usano più tipi di associazione. È l'approccio consigliato per la registrazione delle estensioni di binding. Bundle di estensione rimuove anche il requisito di installazione di .NET Core 2.x SDK. 

### <a name="extension-bundles"></a>bundle di estensione

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Per altre informazioni, vedere [le estensioni delle associazioni di funzioni di Azure registra](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Prima di aggiungere al file Functions binding, è necessario aggiungere il bundle di estensione per l'host. JSON.

### <a name="register-individual-extensions"></a>Registrare le singole estensioni

Se è necessario installare le estensioni che non sono in un bundle, è possibile registrare manualmente i pacchetti di estensione singolo per le associazioni specifiche. 

> [!NOTE]
> Per registrare manualmente le estensioni usando `func extensions install`, è necessario disporre di .NET Core 2.x SDK installato.

Dopo avere aggiornato il file *function.json* per includere tutte le associazioni necessarie per la funzione, eseguire il seguente comando nella cartella del progetto.

```bash
func extensions install
```

Il comando legge il file *function.json* per scoprire quali pacchetti sono necessari, li installa e ricompila il progetto di estensione. Consente di aggiungere eventuali nuove associazioni alla versione corrente, ma non aggiorna le associazioni esistenti. Usare l'opzione `--force` per aggiornare le associazioni esistenti alla versione più recente quando si installano quelle nuove.