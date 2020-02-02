---
title: Risoluzione dei problemi dell'hub Azure Internet Error 504101 GatewayTimeout
description: Informazioni su come correggere l'errore 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960672"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Questo articolo descrive le cause e le soluzioni per gli errori di **504101 GatewayTimeout** .

## <a name="symptoms"></a>Sintomi

Quando si tenta di richiamare un metodo diretto dall'hub Internet in un dispositivo, la richiesta ha esito negativo con l'errore **504101 GatewayTimeout**.

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

Si è verificato un errore nell'hub Internet e non è stato possibile verificare se il metodo diretto è stato completato prima del timeout.

### <a name="cause-2"></a>Causa 2

Quando si usa una versione precedente dell' C# SDK di Azure (< 1.19.0), il collegamento AMQP tra il dispositivo e l'hub Internet può essere eliminato automaticamente a causa di un bug.

## <a name="solution"></a>Soluzione

### <a name="solution-1"></a>Soluzione 1

Eseguire un nuovo tentativo.

### <a name="solution-2"></a>Soluzione 2

Eseguire l'aggiornamento alla versione più recente dell' C# SDK di Azure.