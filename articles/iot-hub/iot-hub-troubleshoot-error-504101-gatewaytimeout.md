---
title: Risoluzione dei problemi dell'errore 504101 GatewayTimeout dell'hub IoT di Azure
description: Informazioni su come risolvere l'errore 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 373acc30ed652a7f540e840dfad5eeeda65ca179
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759561"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

In questo articolo vengono descritte le cause e le soluzioni per gli errori **504101 GatewayTimeout.**

## <a name="symptoms"></a>Sintomi

Quando si tenta di richiamare un metodo diretto dall'hub IoT a un dispositivo, la richiesta ha esito negativo con l'errore **504101 GatewayTimeout**.

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

L'hub IoT ha rilevato un errore e non è stato possibile confermare se il metodo diretto è stato completato prima del timeout.

### <a name="cause-2"></a>Causa 2

Quando si usa una versione precedente di Azure IoT CSDK (<1.19.0), il collegamento AMQP tra il dispositivo e l'hub IoT può essere eliminato automaticamente a causa di un bug.

## <a name="solution"></a>Soluzione

### <a name="solution-1"></a>Soluzione 1

Emettere un nuovo tentativo.

### <a name="solution-2"></a>Soluzione 2

Eseguire l'aggiornamento alla versione più recente dell'SDK di Azure IOT c'è.