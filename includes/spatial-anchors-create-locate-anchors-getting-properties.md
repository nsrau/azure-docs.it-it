---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110812"
---
È possibile aggiornare la posizione di un ancoraggio una volta che è stata creata nel servizio, è necessario creare un nuovo punto di ancoraggio ed eliminare quello precedente per tenere traccia di una nuova posizione.

Se non è necessario individuare un punto di ancoraggio per aggiornare le relative proprietà, è possibile usare la `GetAnchorPropertiesAsync()` metodo, che restituisce un `CloudSpatialAnchor` oggetto con proprietà.
