---
title: Sicurezza dei servizi cognitivi di Azure
titleSuffix: Azure Cognitive Services
description: Informazioni sulle diverse considerazioni sulla sicurezza per l'utilizzo di servizi cognitivi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: erhopf
ms.custom: tracking-python
ms.openlocfilehash: 51a9829a7ea19665e1081a48207f176b1a8e68c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090842"
---
# <a name="azure-cognitive-services-security"></a>Sicurezza dei servizi cognitivi di Azure

Per lo sviluppo di qualsiasi applicazione e di tutte le applicazioni, la sicurezza deve essere considerata una priorità assoluta. Con l'inizio di applicazioni abilitate per l'intelligenza artificiale, la sicurezza è ancora più importante. In questo articolo vengono illustrati i vari aspetti della sicurezza dei servizi cognitivi di Azure, ad esempio l'uso della sicurezza a livello di trasporto, l'autenticazione, la configurazione sicura dei dati sensibili e la Customer Lockbox per l'accesso ai dati dei clienti.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Tutti gli endpoint di servizi cognitivi esposti tramite HTTP applicano TLS 1,2. Con un protocollo di sicurezza applicato, i consumer che tentano di chiamare un endpoint di servizi cognitivi devono rispettare queste linee guida:

* Il sistema operativo client deve supportare TLS 1,2
* Il linguaggio e la piattaforma usati per eseguire la chiamata HTTP devono specificare TLS 1,2 come parte della richiesta
  * A seconda del linguaggio e della piattaforma, la specifica di TLS viene eseguita in modo implicito o esplicito

Per gli utenti .NET, prendere in considerazione le <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">procedure <span class="docon docon-navigate-external x-hidden-focus"></span> consigliate Transport Layer Security </a>.

## <a name="authentication"></a>Autenticazione

Quando si discute dell'autenticazione, esistono diversi equivoci comuni. L'autenticazione e l'autorizzazione sono spesso confuse tra loro. L'identità è anche un componente principale della sicurezza. Un'identità è una raccolta di informazioni su un' <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">entità <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. I provider di identità (IdP) forniscono identità ai servizi di autenticazione. L'autenticazione è l'azione di verifica dell'identità di un utente. L'autorizzazione è la specifica dei diritti di accesso e dei privilegi per le risorse per una determinata identità. Diverse offerte di servizi cognitivi includono il controllo degli accessi in base al ruolo (RBAC). È possibile utilizzare il controllo degli accessi in base al ruolo per semplificare la cerimonia relativa alla gestione manuale delle entità. Per informazioni dettagliate, vedere [controllo degli accessi in base al ruolo per le risorse di Azure](../role-based-access-control/overview.md).

Per altre informazioni sull'autenticazione con chiavi di sottoscrizione, token di accesso e Azure Active Directory (AAD), vedere <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">autenticare le richieste ai servizi <span class="docon docon-navigate-external x-hidden-focus"></span> cognitivi di Azure</a>.

## <a name="environment-variables-and-application-configuration"></a>Variabili di ambiente e configurazione dell'applicazione

Le variabili di ambiente sono coppie nome-valore, archiviate in un ambiente specifico. Un'alternativa più sicura all'uso di valori hardcoded per i dati sensibili consiste nell'usare variabili di ambiente. I valori hardcoded non sono sicuri e devono essere evitati.

> [!CAUTION]
> **Non** usare valori hardcoded per i dati sensibili, in questo modo si tratta di una principale vulnerabilità di sicurezza.

> [!NOTE]
> Sebbene le variabili di ambiente siano archiviate in testo normale, sono isolate in un ambiente. Se un ambiente è compromesso, anche le variabili con l'ambiente.

### <a name="set-environment-variable"></a>Imposta variabile di ambiente

Per impostare le variabili di ambiente, usare uno dei comandi seguenti, dove `ENVIRONMENT_VARIABLE_KEY` è la chiave denominata e `value` è il valore archiviato nella variabile di ambiente.

# <a name="command-line"></a>[Riga di comando](#tab/command-line)

Creare e assegnare la variabile di ambiente permanente, dato il valore.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

In una nuova istanza del **prompt dei comandi**leggere la variabile di ambiente.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Creare e assegnare la variabile di ambiente permanente, dato il valore.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

In una nuova istanza di **Windows PowerShell**, leggere la variabile di ambiente.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Creare e assegnare la variabile di ambiente permanente, dato il valore.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

In una nuova istanza di **bash**leggere la variabile di ambiente.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> Dopo aver impostato una variabile di ambiente, riavviare il Integrated Development Environment (IDE) per assicurarsi che siano disponibili le nuove variabili di ambiente.

### <a name="get-environment-variable"></a>Ottenere la variabile di ambiente

Per ottenere una variabile di ambiente, è necessario leggerla in memoria. A seconda del linguaggio usato, prendere in considerazione i frammenti di codice seguenti. Questi frammenti di codice illustrano come ottenere la variabile di ambiente in base a `ENVIRONMENT_VARIABLE_KEY` e assegnarla a una variabile denominata `value` .

# <a name="c"></a>[C#](#tab/csharp)

Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

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

Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

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

Per ulteriori informazioni, vedere <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

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

Per ulteriori informazioni, vedere <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Per ulteriori informazioni, vedere <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

Per ulteriori informazioni, vedere <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>Customer Lockbox

[Customer Lockbox per Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md) fornisce un'interfaccia che consente ai clienti di esaminare e approvare o rifiutare le richieste di accesso ai dati del cliente. Viene usato nei casi in cui un tecnico Microsoft deve accedere ai dati dei clienti durante una richiesta di supporto. Per informazioni sul modo in cui le richieste Customer Lockbox vengono avviate, rilevate e archiviate per verifiche e controlli successivi, vedere [Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md). 

Customer Lockbox è disponibile per questo servizio cognitivo:

* Traduttore

Per i servizi seguenti, i tecnici Microsoft non accederanno ai dati dei clienti nel livello E0: 

* Language Understanding
* Viso
* Content Moderator
* Personalizza esperienze

> [!IMPORTANT]
> Per il **riconoscimento dei moduli**, i tecnici Microsoft non accederanno ai dati dei clienti nelle risorse create dopo il 10 luglio 2020.

Per richiedere la possibilità di usare lo SKU E0, compilare e inviare il [modulo di richiesta](https://aka.ms/cogsvc-cmk). Saranno richiesti circa 3-5 giorni lavorativi per ricevere informazioni sullo stato della richiesta. A seconda della richiesta, è possibile che venga inserita in una coda e approvata quando lo spazio diventa disponibile. Una volta approvata l'uso dello SKU di E0 con LUIS, sarà necessario creare una nuova risorsa dal portale di Azure e selezionare E0 come piano tariffario. Gli utenti non saranno in grado di eseguire l'aggiornamento da F0 al nuovo SKU di E0.

Il servizio di riconoscimento vocale attualmente non supporta Customer Lockbox. Tuttavia, i dati dei clienti possono essere archiviati tramite Bring your own Storage (BYOS), consentendo di ottenere controlli dati simili per Customer Lockbox. Tenere presente che i dati del servizio vocale rimangono e vengono elaborati nell'area in cui è stata creata la risorsa di riconoscimento vocale. Questo vale per tutti i dati inattivi e i dati in transito. Quando si usano le funzionalità di personalizzazione, come Riconoscimento vocale personalizzato e la voce personalizzata, tutti i dati dei clienti vengono trasferiti, archiviati ed elaborati nella stessa area in cui risiedono le risorse di BYOS (se usate) e del servizio di riconoscimento vocale.

> [!IMPORTANT]
> Microsoft **non** usa i dati dei clienti per migliorare i propri modelli di riconoscimento vocale. Inoltre, se la registrazione dell'endpoint è disabilitata e non vengono utilizzate personalizzazioni, non vengono archiviati dati del cliente. 

## <a name="next-steps"></a>Passaggi successivi

* Esplora i vari [Servizi cognitivi](welcome.md)
* Altre informazioni sulle [reti virtuali di servizi cognitivi](cognitive-services-virtual-networks.md)