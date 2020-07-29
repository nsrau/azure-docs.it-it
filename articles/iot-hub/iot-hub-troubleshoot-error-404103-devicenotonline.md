---
title: Risoluzione dei problemi dell'hub Azure Internet Error 404103 DeviceNotOnline
description: Informazioni su come correggere l'errore 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960815"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

Questo articolo descrive le cause e le soluzioni per gli errori di **404103 DeviceNotOnline** .

## <a name="symptoms"></a>Sintomi

Un metodo diretto a un dispositivo ha esito negativo con l'errore **404103 DeviceNotOnline** anche se il dispositivo è online. 

## <a name="cause"></a>Causa

Se si è certi che il dispositivo è online e si è ancora ricevuto l'errore, è probabile che il callback del metodo diretto non sia registrato nel dispositivo.

## <a name="solution"></a>Soluzione

Per configurare correttamente il dispositivo per i callback di metodi diretti, vedere [gestire un metodo diretto in un dispositivo](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).