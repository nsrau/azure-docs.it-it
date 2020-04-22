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
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683172"
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
> [!NOTE]
> Vale anche la pena notare che alcune distribuzioni di Linux non hanno una variabile di ambiente TMP o TMPDIR definita. In questo modo l'SDK vocale scaricherà l'elenco di revoche di certificati (CRL) ogni volta, anziché memorizzare nella cache il CRL su disco per il riutilizzo fino alla scadenza. Per migliorare le prestazioni iniziali della connessione è possibile creare una [variabile di ambiente denominata TMPDIR e impostarla sul percorso della directory temporanea scelta.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su Speech SDK](speech-sdk.md)
