---
title: File di inclusione
description: File di inclusione
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 04/09/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: eae4b1e919270413d4ca6627964fad9c7f5bd9bf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180273"
---
1. Usare l'utilità della riga di comando `dps-keygen` per generare una stringa di connessione:

    Per installare l'[utilità generatore di chiavi](https://github.com/Azure/dps-keygen), eseguire il comando seguente:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Per generare una stringa di connessione, eseguire il comando seguente usando i dettagli della connessione annotati in precedenza:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Copiare la stringa di connessione dall'output di `dps-keygen` per usarla nel codice del dispositivo.