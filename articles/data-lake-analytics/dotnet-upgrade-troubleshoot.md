---
title: Risoluzione degli errori dei processi U-SQL di Azure Data Lake Analytics a causa dell'aggiornamento di .NET Framework 4.7.2
description: Risolvere gli errori dei processi U-SQL a causa dell'aggiornamento a .NET Framework 4.7.2.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213591"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics è in fase di aggiornamento a .NET Framework v4.7.2

Il runtime predefinito di Azure Data Lake Analytics sta eseguendo l'aggiornamento da .NET Framework v4.5.2 a .NET Framework v4.7.2.The Azure Data Lake Analytics default runtime is upgrading from .NET Framework v4.5.2 to .NET Framework v4.7.2. Questa modifica introduce un piccolo rischio di modifiche di rilievo se il codice U-SQL utilizza assembly personalizzati e tali assembly personalizzati utilizzano librerie .NET.

Questo aggiornamento da .NET Framework 4.5.2 alla versione 4.7.2 significa che .NET Framework distribuito in un runtime U-SQL (il runtime predefinito) sarà ora sempre 4.7.2. Non esiste un'opzione side-by-side per le versioni di .NET Framework.

Al termine dell'aggiornamento a .NET Framework 4.7.2, il codice gestito del sistema verrà eseguito come versione 4.7.2, le librerie fornite dall'utente, ad esempio gli assembly personalizzati U-SQL verranno eseguite nella modalità compatibile con le versioni precedenti appropriata per la versione che l'assembly è stato generato per.

- Se le DLL dell'assembly vengono generate per la versione 4.5.2, il framework distribuito le considererà come librerie 4.5.2, fornendo (con alcune eccezioni) semantica 4.5.2.
- È ora possibile usare assembly personalizzati U-SQL che utilizzano le funzionalità della versione 4.7.2, se si sceglie come destinazione .NET Framework 4.7.2.

A causa di questo aggiornamento a.NET Framework 4.7.2, esiste la possibilità di introdurre modifiche di rilievo ai processi U-SQL che utilizzano assembly personalizzati .NET. Si consiglia di verificare la presenza di problemi di compatibilità con le versioni precedenti utilizzando la procedura riportata di seguito.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Come verificare la presenza di problemi di compatibilità con le versioni precedenti

Verificare il potenziale di problemi di interruzione della compatibilità con le versioni precedenti eseguendo i controlli di compatibilità .NET sul codice .NET negli assembly personalizzati U-SQL.

> [!Note]
> Lo strumento non rileva le modifiche di rilievo effettive. identifica solo le API .NET chiamate che possono (per determinati input) causare problemi. Se ricevi una notifica di un problema, il codice potrebbe comunque andare bene, tuttavia dovresti controllare più dettagli.

1. Eseguire lo verifica compatibilità con le versioni precedenti sulle DLL .NET
   1. Utilizzo dell'estensione di Visual Studio in [.NET Portability Analyzer Visual Studio Extension](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Download e utilizzo dello strumento autonomo da [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). Le istruzioni per l'esecuzione dello strumento autonomo sono in [GitHub dotnetapiport modifiche di interruzione](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. Per la 4.7.2. compatibilità, `read isRetargeting == True` identifica i possibili problemi.
2. Se lo strumento indica se il codice può essere influenzato da una delle possibili incompatibilità all'indietro (alcuni esempi comuni di incompatibilità sono elencati di seguito), è possibile verificare ulteriormente
   1. Analisi del codice e identificazione se il codice passa valori alle API interessateAnalyzing your code and identifying if your code is passing values to the impacted APIs
   1. Eseguire un controllo di runtime. La distribuzione di runtime non viene eseguita side-by-side in ADLA. È possibile eseguire un controllo di runtime prima dell'aggiornamento, utilizzando l'esecuzione locale di VisualStudio con un.NET Framework 4.7.2 locale su un set di dati rappresentativo.
3. Se si è effettivamente interessati da un'incompatibilità con le versioni precedenti, adottare le operazioni necessarie per risolvere il problema, ad esempio la correzione dei dati o la logica del codice.

Nella maggior parte dei casi, non si dovrebbe essere influenzati da backwards-incompatibility.

## <a name="timeline"></a>Sequenza temporale

È possibile verificare la distribuzione del nuovo runtime in fase di [risoluzione dei problemi](runtime-troubleshoot.md)di runtime e esaminando qualsiasi processo di esito positivo precedente.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>Cosa succede se non riesco a far rivedere il codice in tempo

È possibile inviare il processo alla versione di runtime precedente (che viene compilata con la versione 4.5.2), tuttavia a causa della mancanza di funzionalità side-by-side di .NET Framework, verrà comunque eseguito solo in modalità di compatibilità 4.5.2. È ancora possibile riscontrare alcuni dei problemi di compatibilità con le versioni precedenti a causa di questo comportamento.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Quali sono i problemi di compatibilità con le versioni precedenti più comuni che si possono riscontrare

Le incompatibilità all'indietro più comuni che è probabile che il correttore identifichi noe sono (abbiamo generato questo elenco eseguendo il controllo sui nostri lavori ADLA interni), quali librerie sono interessate (nota: che è possibile chiamare le librerie solo indirettamente, quindi è importante intraprendere le azioni necessarie #1 per verificare se i processi sono interessati) e possibili azioni da porre rimedio. Nota: In quasi tutti i casi per i nostri lavori, gli avvertimenti si sono rivelati falsi positivi a causa della natura ristretta della maggior parte dei cambiamenti di rottura.

- La proprietà IAsyncResult.CompletedSynchronously deve essere corretta per garantire il completamento dell'attività risultante
  - Quando si chiama TaskFactory.FromAsync, l'implementazione della proprietà IAsyncResult.CompletedSynchronously deve essere corretta per consentire il completamento dell'attività risultante. Ovvero la proprietà deve restituire true unicamente se l'implementazione è stata completata in modo sincrono. Precedentemente, la proprietà non veniva verificata.
  - Librerie interessate: mscorlib, System.Threading.Tasks
  - Azione suggerita: Assicurarsi che TaskFactory.FromAsync restituisca true correttamente

- DataObject.GetData ora recupera i dati come UTF-8
  - Per le app destinate a .NET Framework 4 o che vengono eseguite in .NET Framework 4.5.1 o versioni precedenti, DataObject.GetData recupera dati in formato HTML sotto forma di stringa ASCII. Di conseguenza, i caratteri non ASCII (caratteri i cui codici ASCII sono maggiori di 0x7F) sono rappresentati da due caratteri casuali.#N #N, per `DataObject.GetData` le app destinate a .NET Framework 4.5 o versioni successive e eseguite in .NET Framework 4.5.2, recuperai correttamente i dati in formato HTML come UTF-8, che rappresenta correttamente caratteri maggiori di 0x7F.
  - Librerie interessate: Glo
  - Azione suggerita: assicurarsi che i dati recuperati siano il formato desiderato

- XmlWriter genera un'eccezione per le coppie di surrogati non valide
  - Per le app destinate a .NET Framework 4.5.2 o a versioni precedenti, se si scrive una coppia di surrogati non valida usando la gestione dei fallback delle eccezioni, non viene sempre generata un'eccezione. Per le applicazioni destinate a .NET Framework 4.6, il tentativo di scrivere una coppia di surrogati non valida genera `ArgumentException`.
  - Librerie interessate: System.Xml, System.Xml.ReaderWriter
  - Azione suggerita: assicurarsi che non si sta scrivendo una coppia di surrogati non valida che genererà un'eccezione di argomento

- HtmlTextWriter non esegue correttamente il rendering dell'elemento `<br/>`
  - A partire da .NET Framework 4.6, la chiamata di `HtmlTextWriter.RenderBeginTag()` e `HtmlTextWriter.RenderEndTag()` con un elemento `<BR />` inserirà correttamente solo un `<BR />` (invece di due)
  - Librerie interessate: System.Web
  - Azione suggerita: Assicurarsi che si `<BR />` sta inserendo la quantità di previsto di vedere in modo che nessun comportamento casuale è visto nel processo di produzione

- Modifica della chiamata di CreateDefaultAuthorizationContext con un argomento Null
  - L'implementazione dell'oggetto AuthorizationContext restituito da una chiamata al metodo `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` con un argomento authorizationPolicies è cambiata in .NET Framework 4.6.
  - Librerie interessate: System.IdentityModel
  - Azione suggerita: assicurarsi di gestire il nuovo comportamento previsto quando sono presenti criteri di autorizzazione null
  
- RSACng ora carica correttamente le chiavi RSA di dimensioni non standard
  - Nelle versioni di .NET Framework precedenti alla 4.6.2, i clienti con chiavi di dimensioni non standard per i certificati RSA non possono accedere a queste chiavi tramite i metodi di estensione `GetRSAPublicKey()` e `GetRSAPrivateKey()`. Viene `CryptographicException` generata una eccezione con il messaggio "La dimensione della chiave richiesta non è supportata". Con .NET Framework 4.6.2 questo problema è stato risolto. Allo stesso `RSA.ImportParameters()` `RSACng.ImportParameters()` modo, e ora funzionano con `CryptographicException`dimensioni di chiave non standard senza generare.
  - Librerie interessate: mscorlib, System.Core
  - Azione suggerita: Assicurarsi che i tasti RSA funzionino come previsto

- I controlli della presenza di due punti nel percorso sono più severi
  - In .NET Framework 4.6.2 sono state apportate varie modifiche per supportare i percorsi in precedenza non supportati (sia per lunghezza che per formato). I controlli della sintassi corretta del separatore appropriato per le unità (due punti) sono ora più corretti. L'effetto collaterale è però il blocco di alcuni percorsi URI in un gruppo selezionato di API Path in cui l'uso era tollerato.
  - Librerie interessate: mscorlib, System.Runtime.Extensions
  - Azione suggerita:

- Chiamate dei costruttori ClaimsIdentity
  - A partire da .NET Framework 4.6.2 è stata introdotta una modifica al modo in cui i costruttori `T:System.Security.Claims.ClaimsIdentity` con un parametro `T:System.Security.Principal.IIdentity` impostano la proprietà `P:System.Security.Claims.ClaimsIdentify.Actor`. Se l'argomento `T:System.Security.Principal.IIdentity` è un oggetto `T:System.Security.Claims.ClaimsIdentity` e la proprietà `P:System.Security.Claims.ClaimsIdentify.Actor` di tale oggetto `T:System.Security.Claims.ClaimsIdentity` non è `null`, la proprietà `P:System.Security.Claims.ClaimsIdentify.Actor` viene allegata usando il metodo `M:System.Security.Claims.ClaimsIdentity.Clone`. In Framework 4.6.1 e versioni `P:System.Security.Claims.ClaimsIdentify.Actor` precedenti, la proprietà è associata come riferimento esistente. A causa di questa modifica, a partire da.NET `P:System.Security.Claims.ClaimsIdentify.Actor` Framework 4.6.2, la proprietà del nuovo `T:System.Security.Claims.ClaimsIdentity` oggetto non è uguale alla `P:System.Security.Claims.ClaimsIdentify.Actor` proprietà dell'argomento del `T:System.Security.Principal.IIdentity` costruttore. In .NET Framework 4.6.1 e versioni precedenti è uguale.
  - Librerie interessate: mscorlib
  - Azione suggerita: Assicurarsi che ClaimsIdentity funzioni come previsto nel nuovo runtime

- La serializzazione dei caratteri di controllo con DataContractJsonSerializer è ora compatibile con ECMAScript V6 e V8
  - In .NET Framework 4.6.2 e versioni precedenti, DataContractJsonSerializer non ha serializzato alcuni caratteri di controllo speciali, ad esempio gli standard ECMAScript V6 e V8. A partire da .NET Framework 4.7, la serializzazione di questi caratteri di controllo è compatibile con ECMAScript V6 e V8.
  - Librerie interessate: System.Runtime.Serialization.Json
  - Azione suggerita: garantire lo stesso comportamento con DataContractJsonSerializerSuggested Action: Ensure same behavior with DataContractJsonSerializer
