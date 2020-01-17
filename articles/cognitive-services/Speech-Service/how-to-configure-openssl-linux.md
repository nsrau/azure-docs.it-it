---
title: Come configurare OpenSSL per Linux
titleSuffix: Azure Cognitive Services
description: Informazioni su come configurare OpenSSL per Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: cadf31dede8ee81323076013d00b9431f597bda6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156489"
---
# <a name="configure-openssl-for-linux"></a>Configurare OpenSSL per Linux

Quando si usa una versione di SDK vocale prima di 1.9.0, [openssl](https://www.openssl.org) viene configurato dinamicamente per la versione del sistema host. Nelle versioni successive di Speech SDK, OpenSSL (versione [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) è collegato in modo statico alla libreria principale dell'SDK di riconoscimento vocale.

## <a name="troubleshoot-connectivity"></a>Risolvere i problemi di connettività

Se si verificano errori di connessione quando si usa la versione 1.9.0 di Speech SDK, verificare che la directory `ssl/certs` esista nella directory `/usr/lib`, disponibile nella file system Linux. Se la directory `ssl/certs` *non esiste*, controllare la posizione in cui OpenSSL è installato nel sistema, usando il comando seguente:

```bash
which openssl
```

Individuare quindi la directory OpenSSL `certs` e copiare il contenuto della directory nella directory `/usr/lib/ssl/certs`. Quindi, riprovare per verificare se sono stati risolti problemi di connettività.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'SDK di riconoscimento vocale](speech-sdk.md)