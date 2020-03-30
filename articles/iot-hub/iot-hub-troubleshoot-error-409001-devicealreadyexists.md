---
title: Risoluzione dei problemi dell'errore dell'hub IoT di Azure 409001 DeviceAlreadyExistsTroubleshooting Azure IoT Hub error 409001 DeviceAlreadyExists
description: Capire come risolvere l'errore 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960789"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

In questo articolo vengono descritte le cause e le soluzioni per gli errori **409001 DeviceAlreadyExists.**

## <a name="symptoms"></a>Sintomi

Quando si tenta di registrare un dispositivo nell'hub IoT, la richiesta ha esito negativo con l'errore **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Causa

Esiste già un dispositivo con lo stesso ID dispositivo nell'hub IoT.There's already a device with the same device ID in the IoT hub. 

## <a name="solution"></a>Soluzione

Utilizzare un ID dispositivo diverso e riprovare.