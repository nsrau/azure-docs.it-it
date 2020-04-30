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
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81683172"
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

Controllare se è `certs` presente una sottodirectory in OPENSSLDIR. Nell'esempio precedente, sarebbe `/usr/lib/ssl/certs`.

* Se è `/usr/lib/ssl/certs` presente e contiene molti singoli file di certificato (con `.crt` estensione `.pem` o), non è necessario eseguire ulteriori azioni.

* Se il valore di OPENSSLDIR è `/usr/lib/ssl` diverso da e/o se è presente un singolo file di bundle del certificato anziché più file singoli, è necessario impostare una variabile di ambiente SSL appropriata per indicare la posizione in cui è possibile trovare i certificati.

## <a name="examples"></a>Esempi

- OPENSSLDIR è `/opt/ssl`. È `certs` presente una sottodirectory con `.crt` molti `.pem` file o.
Impostare la variabile `SSL_CERT_DIR` di ambiente in `/opt/ssl/certs` modo che punti a prima di eseguire un programma che usa l'SDK di riconoscimento vocale. Ad esempio:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR è `/etc/pki/tls` (come nei sistemi basati su RHEL/CentOS). È `certs` presente una sottodirectory con un file di bundle di certificati `ca-bundle.crt`, ad esempio.
Impostare la variabile `SSL_CERT_FILE` di ambiente in modo che punti a tale file prima di eseguire un programma che usa l'SDK di riconoscimento vocale. Ad esempio:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> È anche importante notare che alcune distribuzioni di Linux non hanno una variabile di ambiente TMP o TMPDIR definita. In questo modo l'SDK per la sintesi vocale Scarica l'elenco di revoche di certificati (CRL) ogni volta, anziché memorizzare il CRL su disco per riutilizzarlo fino alla scadenza. Per migliorare le prestazioni iniziali della connessione, è possibile [creare una variabile di ambiente denominata TMPDIR e impostarla sul percorso della directory temporanea scelta.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su Speech SDK](speech-sdk.md)
