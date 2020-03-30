---
title: Generare un token di sicurezza per accedere al repository IoT Plug and Play Preview . Documenti Microsoft
description: Generare un token di firma di accesso condiviso da utilizzare quando si accede a un repository di modelli IoT Plug and Play Preview a livello di codice.
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159201"
---
# <a name="generate-sas-token"></a>Generare il token di firma di accesso condiviso

Questa guida alle procedure illustra come generare a livello di codice un token sAS (Shared Access Signature) da usare con le API del repository del modello Plug and Play Preview IoT.

## <a name="python"></a>Python

Il frammento di codice seguente illustra come generare un token di firma di accesso condiviso usando Python:The following snippet shows you how to generate a SAS token using Python:

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

Il frammento di codice seguente illustra come\#generare un token di firma di accesso condiviso usando C:The following snippet shows you how to generate a SAS token using C:

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>Usare il token di firma di accesso condivisoUse the SAS token

Dopo aver generato un token di firma di accesso condiviso, è possibile usarlo per effettuare una richiesta HTTP POST. Ad esempio:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Se si fornisce a un client un token di firma di accesso condiviso, il client non dispone della chiave primaria della risorsa e non può invertire l'hash per ottenerlo. Un token di firma di accesso condiviso consente di controllare gli elementi a cui il client può accedere e per quanto tempo. Quando si modifica la chiave primaria nei criteri, tutti i token di firma di accesso condiviso creati da esso vengono invalidati.

## <a name="next-steps"></a>Passaggi successivi

Ora che hai imparato a generare token di sicurezza da utilizzare per accedere ai repository del modello IoT Plug and Play Preview, un passaggio successivo consigliato è quello di saperne di più nella Guida per gli sviluppatori di [modellazione di IoT Plug and Play Preview](concepts-developer-guide.md).
