---
title: Come configurare OpenSSL per Linux
titleSuffix: Azure Cognitive Services
description: Scopri come configurare OpenSSL per Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: 350c2bf3c4d0fc0a16f1b393e7c8d8a372679797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331145"
---
# <a name="configure-openssl-for-linux"></a>Configurare OpenSSL per Linux

Quando si utilizza qualsiasi versione di Speech SDK precedente alla 1.9.0, [OpenSSL](https://www.openssl.org) viene configurato dinamicamente per la versione del sistema host. Nelle versioni successive di Speech SDK, OpenSSL (versione [1.1.1b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) è collegato in modo statico alla libreria principale di Speech SDK.

Per garantire la connettività, verificare che i certificati OpenSSL siano stati installati nel sistema. Eseguire un comando:
```bash
openssl version -d
```

L'output sui sistemi basati su Ubuntu/Debian dovrebbe essere:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Verificare se `certs` è presente una sottodirectory in OPENSSLDIR. Nell'esempio precedente, sarebbe `/usr/lib/ssl/certs`.

* Se c'è `/usr/lib/ssl/certs` e contiene molti `.crt` singoli `.pem` file di certificato (con o estensione), non c'è bisogno di ulteriori azioni.

* Se OPENSSLDIR è `/usr/lib/ssl` diverso da e/o è presente un singolo file di pacchetto di certificati anziché più file singoli, è necessario impostare una variabile di ambiente SSL appropriata per indicare dove è possibile trovare i certificati.

## <a name="examples"></a>Esempi

- OPENSSLDIR `/opt/ssl`è . C'è `certs` sottodirectory `.crt` `.pem` con molti o file.
Impostare `SSL_CERT_DIR` la variabile `/opt/ssl/certs` di ambiente in modo che punti a prima di eseguire un programma che utilizza Speech SDK. Ad esempio:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR `/etc/pki/tls` è (come nei sistemi basati su RHEL/CentOS). Esiste `certs` una sottodirectory con un file `ca-bundle.crt`del pacchetto di certificati, ad esempio .
Impostare `SSL_CERT_FILE` la variabile di ambiente in modo che punti a tale file prima di eseguire un programma che utilizza Speech SDK. Ad esempio:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su Speech SDK](speech-sdk.md)
