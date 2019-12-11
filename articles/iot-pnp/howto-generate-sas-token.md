---
title: Generare un token di sicurezza per accedere al repository Plug and Play Preview | Microsoft Docs
description: Generare un token di firma di accesso condiviso da usare quando si accede a un archivio del modello Plug and Play di anteprima a livello di codice.
author: Philmea
ms.author: philmea
ms.date: 08/06/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f6c4f5b9784eeff9d03b6e93953674736fb78c6c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976027"
---
# <a name="generate-sas-token"></a>Generare il token di firma di accesso condiviso

Questa guida illustra come generare un token di firma di accesso condiviso a livello di codice da usare con le API del repository del modello Plug and Play di anteprima.

## <a name="python"></a>Python

Il frammento di codice seguente illustra come generare un token SAS con Python:

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

Il frammento di codice seguente illustra come generare un token SAS usando C\#:

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

## <a name="use-the-sas-token"></a>Usare il token SAS

Dopo aver generato un token di firma di accesso condiviso, è possibile usarlo per effettuare una richiesta HTTP POST. ad esempio:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Se si assegna a un client un token SAS, il client non ha la chiave primaria della risorsa e non può invertire l'hash per ottenerlo. Un token di firma di accesso condiviso consente di controllare ciò che il client può accedere e per quanto tempo. Quando si modifica la chiave primaria nel criterio, eventuali token di firma di accesso condiviso creati da esso vengono invalidati.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come generare i token di sicurezza da usare per accedere ai repository del modello Plug and Play di anteprima, un passaggio successivo suggerito consiste nell'acquisire altre informazioni nella Guida per gli [sviluppatori di modeling plug and Play Preview](concepts-developer-guide.md).
