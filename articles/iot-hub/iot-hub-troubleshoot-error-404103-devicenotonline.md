---
title: Risoluzione dei problemi dell'errore dell'hub IoT di Azure 404103 DeviceNotOnlineTroubleshooting Azure IoT Hub error 404103 DeviceNotOnline
description: Capire come risolvere l'errore 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960815"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

In questo articolo vengono descritte le cause e le soluzioni per gli errori **404103 DeviceNotOnline.**

## <a name="symptoms"></a>Sintomi

Un metodo diretto a un dispositivo ha esito negativo con l'errore **404103 DeviceNotOnline** anche se il dispositivo è online. 

## <a name="cause"></a>Causa

Se si sa che il dispositivo è online e ancora ottenere l'errore, è probabile che il callback del metodo diretto non è registrato nel dispositivo.

## <a name="solution"></a>Soluzione

Per configurare correttamente il dispositivo per i callback diretti del metodo, vedere [Gestire un metodo diretto in un dispositivo](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).