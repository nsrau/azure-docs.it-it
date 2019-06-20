---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179858"
---
È possibile aggiornare la posizione di un ancoraggio una volta che è stata creata nel servizio, è necessario creare un nuovo punto di ancoraggio ed eliminare quello precedente per tenere traccia di una nuova posizione.

Se non è necessario individuare un punto di ancoraggio per aggiornare le relative proprietà, è possibile usare la `GetAnchorPropertiesAsync()` metodo, che restituisce un `CloudSpatialAnchor` oggetto con proprietà.
