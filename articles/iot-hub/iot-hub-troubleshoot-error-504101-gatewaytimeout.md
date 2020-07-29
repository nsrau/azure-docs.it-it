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
ms.custom: amqp
ms.openlocfilehash: 373acc30ed652a7f540e840dfad5eeeda65ca179
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81759561"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Questo articolo descrive le cause e le soluzioni per gli errori di **504101 GatewayTimeout** .

## <a name="symptoms"></a>Sintomi

Quando si tenta di richiamare un metodo diretto dall'hub Internet in un dispositivo, la richiesta ha esito negativo con l'errore **504101 GatewayTimeout**.

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

Si è verificato un errore nell'hub Internet e non è stato possibile verificare se il metodo diretto è stato completato prima del timeout.

### <a name="cause-2"></a>Causa 2

Quando si usa una versione precedente di Azure l'SDK per C# (<1.19.0), il collegamento AMQP tra il dispositivo e l'hub Internet può essere eliminato automaticamente a causa di un bug.

## <a name="solution"></a>Soluzione

### <a name="solution-1"></a>Soluzione 1

Eseguire un nuovo tentativo.

### <a name="solution-2"></a>Soluzione 2

Eseguire l'aggiornamento alla versione più recente di Azure Internet SDK per C#.