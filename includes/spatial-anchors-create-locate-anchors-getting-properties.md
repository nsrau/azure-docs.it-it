---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67179858"
---
Non è possibile aggiornare la posizione di un ancoraggio dopo che è stata creato nel servizio, ma è necessario creare un nuovo ancoraggio ed eliminare quello precedente per tenere traccia di una nuova posizione.

Se non è necessario individuare un ancoraggio per aggiornarne le proprietà, è possibile usare il metodo `GetAnchorPropertiesAsync()`, che restituisce un oggetto `CloudSpatialAnchor` con le proprietà.
