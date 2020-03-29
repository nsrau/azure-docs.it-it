---
title: Security
titleSuffix: Azure Cognitive Services
description: Informazioni sulle varie considerazioni sulla sicurezza per l'utilizzo di Servizi cognitivi.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: c86d806c408c2e8226e632a0b15e1e8729c987f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131524"
---
# <a name="azure-cognitive-services-security"></a>Sicurezza dei servizi cognitivi di AzureAzure Cognitive Services security

La sicurezza deve essere considerata una priorità assoluta quando si sviluppano tutte le applicazioni. Con l'insorgenza di applicazioni abilitate per l'intelligenza artificiale, la sicurezza è ancora più importante. In questo articolo vengono descritti vari aspetti della sicurezza di Servizi cognitivi di Azure, ad esempio l'uso della sicurezza a livello di trasporto, l'autenticazione e la configurazione sicura dei dati sensibili.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Tutti gli endpoint di Servizi cognitivi esposti su HTTP applicano TLS 1.2. Con un protocollo di sicurezza imposto, gli utenti che tentano di chiamare un endpoint di Servizi cognitivi devono rispettare queste linee guida:With an enforced security protocol, consumers attempting to call a Cognitive Services endpoint should adhere to these guidelines:

* Il sistema operativo client (OS) deve supportare TLS 1.2
* La lingua (e la piattaforma) utilizzata per effettuare la chiamata HTTP devono specificare TLS 1.2 come parte della richiesta
  * A seconda della lingua e della piattaforma, la specifica di TLS viene eseguita in modo implicito o esplicito

Per gli utenti di .NET, prendere in considerazione le <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">procedure consigliate <span class="docon docon-navigate-external x-hidden-focus"> </span>per Transport Layer Security </a>.

## <a name="authentication"></a>Authentication

Quando si parla di autenticazione, ci sono diversi malintesi comuni. L'autenticazione e l'autorizzazione sono spesso confuse l'una per l'altra. L'identità è anche un componente importante nella sicurezza. Un'identità è una raccolta di informazioni su <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">un'entità <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. I provider di identità (IdP) forniscono identità ai servizi di autenticazione. L'autenticazione è l'atto di verificare l'identità di un utente. L'autorizzazione è la specifica dei diritti di accesso e dei privilegi alle risorse per una determinata identità. Molte delle offerte di servizi cognitivi, includono il controllo degli accessi in base al ruolo (RBAC). Il controllo degli accessi in base al ruolo potrebbe essere utilizzato per semplificare alcune delle cerimonie relative alla gestione manuale dei principi. Per altre informazioni, vedere Controllo degli accessi in base al ruolo per le risorse di Azure.For more details, see [role-based access control for Azure resources.](../role-based-access-control/overview.md)

Per altre informazioni sull'autenticazione con chiavi di sottoscrizione, token di accesso e Azure Active Directory (AAD), vedere <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">autenticare le richieste a servizi cognitivi<span class="docon docon-navigate-external x-hidden-focus"></span></a>di Azure.For more information on authentication with subscription keys, access tokens and Azure Active Directory (AAD), see authenticate requests to Azure Cognitive Services .

## <a name="environment-variables-and-application-configuration"></a>Variabili di ambiente e configurazione dell'applicazione

Le variabili di ambiente sono coppie nome-valore, memorizzate all'interno di un ambiente specifico. Un'alternativa più sicura all'utilizzo di valori hardcoded per i dati sensibili consiste nell'utilizzare le variabili di ambiente. I valori hardcoded non sono sicuri e devono essere evitati.

> [!CAUTION]
> **Non** utilizzare valori hardcoded per i dati sensibili, questa operazione è una delle principali vulnerabilità della sicurezza.

> [!NOTE]
> Mentre le variabili di ambiente vengono archiviate in testo normale, sono isolate in un ambiente. Se un ambiente è compromesso, lo sono anche le variabili con l'ambiente.

### <a name="set-environment-variable"></a>Imposta variabile di ambiente

Per impostare le variabili di ambiente, `ENVIRONMENT_VARIABLE_KEY` utilizzare uno `value` dei comandi seguenti: dove è la chiave denominata ed è il valore archiviato nella variabile di ambiente.

# <a name="command-line"></a>[Riga di comando](#tab/command-line)

Creare e assegnare una variabile di ambiente persistente, dato il valore.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

In una nuova istanza del **prompt dei comandi**leggere la variabile di ambiente.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Creare e assegnare una variabile di ambiente persistente, dato il valore.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

In una nuova istanza di **Windows PowerShell**leggere la variabile di ambiente.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Creare e assegnare una variabile di ambiente persistente, dato il valore.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

In una nuova istanza di **Bash**, leggere la variabile di ambiente .

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> Dopo aver impostato una variabile di ambiente, riavviare l'ambiente di sviluppo integrato (IDE) per assicurarsi che le variabili di ambiente appena aggiunte siano disponibili.

### <a name="get-environment-variable"></a>Ottieni variabile di ambienteGet environment variable

Per ottenere una variabile di ambiente, è necessario leggerla in memoria. A seconda del linguaggio in uso, prendere in considerazione i frammenti di codice seguenti. Questi frammenti di codice illustrano `ENVIRONMENT_VARIABLE_KEY` come ottenere la `value`variabile di ambiente in base a e assegnare a una variabile denominata .

# <a name="c"></a>[C #](#tab/csharp)

Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

Per ulteriori informazioni, vedere <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

Per ulteriori informazioni, vedere <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Per ulteriori informazioni, vedere <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

Per ulteriori informazioni, vedere <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="next-steps"></a>Passaggi successivi

* Esplora i vari [servizi cognitivi](welcome.md)
* Ulteriori informazioni sulle [reti virtuali dei servizi cognitivi](cognitive-services-virtual-networks.md)
