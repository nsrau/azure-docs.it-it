---
title: Risoluzione dei problemi dell'errore dell'hub IoT di Azure 404001 DeviceNotFoundTroubleshooting Azure IoT Hub error 404001 DeviceNotFound
description: Comprendere come risolvere l'errore 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960828"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

In questo articolo vengono descritte le cause e le soluzioni per gli errori **404001 DeviceNotFound.**

## <a name="symptoms"></a>Sintomi

Durante una comunicazione da cloud a dispositivo (C2D), ad esempio un messaggio C2D, un aggiornamento gemello o un metodo diretto, l'operazione non riesce con errore **404001 DeviceNotFound**.

## <a name="cause"></a>Causa

L'operazione non è riuscita perché il dispositivo non è stato trovato dall'hub IoT. Il dispositivo non è registrato o disabilitato.

## <a name="solution"></a>Soluzione

Registrare l'ID del dispositivo utilizzato, quindi riprovare.