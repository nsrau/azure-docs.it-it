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
ms.openlocfilehash: 350c2bf3c4d0fc0a16f1b393e7c8d8a372679797
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331145"
---
# <a name="configure-openssl-for-linux"></a>Configurare OpenSSL per Linux

Quando si usa una versione di SDK vocale prima di 1.9.0, [openssl](https://www.openssl.org) viene configurato dinamicamente per la versione del sistema host. Nelle versioni successive di Speech SDK, OpenSSL (versione [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) è collegato in modo statico alla libreria principale dell'SDK di riconoscimento vocale.

Per garantire la connettività, verificare che nel sistema siano stati installati i certificati OpenSSL. Eseguire un comando:
```bash
openssl version -d
```

L'output nei sistemi basati su Ubuntu/Debian dovrebbe essere:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Controllare se è presente `certs` sottodirectory in OPENSSLDIR. Nell'esempio precedente, verrebbe `/usr/lib/ssl/certs`.

* Se è presente `/usr/lib/ssl/certs` e contiene molti singoli file di certificato (con `.crt` o `.pem` estensione), non è necessario eseguire ulteriori azioni.

* Se OPENSSLDIR è diverso da `/usr/lib/ssl` e/o esiste un solo file di bundle del certificato anziché più file singoli, è necessario impostare una variabile di ambiente SSL appropriata per indicare dove sono disponibili i certificati.

## <a name="examples"></a>Esempi

- OPENSSLDIR è `/opt/ssl`. `certs` sottodirectory con molti file di `.crt` o di `.pem`.
Impostare la variabile di ambiente `SSL_CERT_DIR` in modo che punti a `/opt/ssl/certs` prima di eseguire un programma che usa l'SDK di riconoscimento vocale. Ad esempio,
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR è `/etc/pki/tls`, ad esempio nei sistemi basati su RHEL/CentOS. `certs` sottodirectory con un file di bundle di certificati, ad esempio `ca-bundle.crt`.
Impostare la variabile di ambiente `SSL_CERT_FILE` in modo che punti a tale file prima di eseguire un programma che usa l'SDK di riconoscimento vocale. Ad esempio,
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'SDK di riconoscimento vocale](speech-sdk.md)
