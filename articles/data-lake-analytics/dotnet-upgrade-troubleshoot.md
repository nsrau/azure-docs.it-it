---
title: Come risolvere i problemi relativi agli errori dei processi U-SQL di Azure Data Lake Analytics a causa dell'aggiornamento di .NET 4.7.2
description: Risolvere gli errori di processo U-SQL a causa dell'aggiornamento a .NET 4.7.2.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: 851a405e5143ea5bb3a26de76f713914aa4bb569
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648519"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics viene eseguito l'aggiornamento al .NET Framework v 4.7.2

L'Azure Data Lake Analytics runtime predefinito sta eseguendo l'aggiornamento da .NET Framework v 4.5.2 a .NET Framework v 4.7.2. Questa modifica introduce un piccolo rischio di modifiche di rilievo se il codice U-SQL USA assembly personalizzati e gli assembly personalizzati usano le librerie .NET.

Questo aggiornamento da .NET Framework 4.5.2 alla versione 4.7.2 significa che i .NET Framework distribuiti in un runtime U-SQL (il runtime predefinito) ora saranno sempre 4.7.2. Non è disponibile un'opzione Side-by-side per le versioni .NET Framework.

Al termine dell'aggiornamento a .NET 4.7.2, il codice gestito del sistema verrà eseguito come versione 4.7.2, le librerie fornite dall'utente, ad esempio gli assembly personalizzati di U-SQL, vengono eseguite nella modalità compatibile con le versioni precedenti, appropriato per la versione che l'assembly è stato generato. per.

- Se le DLL di assembly vengono generate per la versione 4.5.2, il Framework distribuito li considererà come librerie 4.5.2, fornendo (con alcune eccezioni) la semantica di 4.5.2.
- È ora possibile usare gli assembly personalizzati U-SQL che usano le funzionalità della versione 4.7.2, se la destinazione è la .NET Framework 4.7.2.

A causa di questo aggiornamento a .NET 4.7.2, è possibile introdurre modifiche di rilievo ai processi U-SQL che usano assembly personalizzati .NET. Si consiglia di verificare i problemi di compatibilità con le versioni precedenti utilizzando la procedura riportata di seguito.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Come verificare i problemi di compatibilità con le versioni precedenti

Verificare la possibilità di problemi di interruzione della compatibilità con le versioni precedenti eseguendo i controlli di compatibilità .NET sul codice .NET negli assembly personalizzati di U-SQL.

> [!Note]
> Lo strumento non rileva le modifiche di rilievo effettive. identifica solo le API .NET che possono causare problemi, per determinati input. Se si riceve una notifica di un problema, il codice potrebbe essere ancora corretto. Tuttavia, è consigliabile archiviare altri dettagli.

1. Eseguire il controllo della compatibilità con le versioni precedenti nelle DLL .NET
   1. Uso dell'estensione di Visual Studio all' [interno dell'estensione di Visual Studio di .NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Download e uso dello strumento autonomo da [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). Le istruzioni per l'esecuzione dello strumento autonomo sono disponibili in [GitHub dotnetapiport modifiche di rilievo](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. Per 4.7.2. Compatibility Read isRetargeting = = true sono le modifiche di rilievo.
2. Se lo strumento indica se il codice potrebbe essere influenzato da una delle possibili incompatibilità con le versioni precedenti (alcuni esempi comuni di incompatibilità sono elencati di seguito), è possibile controllare ulteriormente
   1. Analisi del codice e identificazione se il codice passa valori alle API interessate
   1. Eseguire un controllo di Runtime. La distribuzione di runtime non viene eseguita side-by-side in Anna. È possibile eseguire un controllo di runtime prima dell'aggiornamento, usando l'esecuzione locale di VisualStudio con una .NET Framework locale 4.7.2 rispetto a un set di dati rappresentativo.
3. Se l'incompatibilità con le versioni precedenti ha effetti negativi, eseguire le operazioni necessarie per correggerlo, ad esempio per correggere i dati o la logica del codice.

Nella maggior parte dei casi, l'incompatibilità con le versioni precedenti non ha alcun effetto.

## <a name="timeline"></a>Sequenza temporale

È possibile verificare la distribuzione del nuovo runtime in questa pagina [risoluzione dei problemi](runtime-troubleshoot.md)e osservando eventuali processi precedentemente riusciti.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>Cosa accade se non è possibile riesaminare il codice nel tempo

È possibile inviare il processo a una versione precedente del runtime (che è stata compilata come destinazione 4.5.2), tuttavia, a causa della mancanza di .NET Framework funzionalità side-by-Side, viene comunque eseguita solo in modalità di compatibilità 4.5.2. A causa di questo comportamento, è possibile che si verifichino comunque alcuni problemi di compatibilità con le versioni precedenti.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Quali sono i problemi più comuni di compatibilità con le versioni precedenti che possono verificarsi

Le incompatibilità con le versioni precedenti più comuni che il controllo è in grado di identificare sono (questo elenco è stato generato eseguendo il controllo nei processi Anna interni), le librerie interessate (Nota: è possibile chiamare le librerie solo indirettamente, quindi è importante eseguire le azioni necessarie #1 per verificare se i processi sono interessati) e le possibili azioni da risolvere. Nota: in quasi tutti i casi per i nostri processi, gli avvisi sono diventati falsi positivi a causa della natura limitata della maggior parte delle modifiche di rilievo.

- Per completare l'attività risultante è necessario che la proprietà IAsyncResult. CompletedSynchronously sia corretta
  - Quando si chiama TaskFactory. FromAsync, l'implementazione della proprietà IAsyncResult. CompletedSynchronously deve essere corretta per il completamento dell'attività risultante. Ovvero la proprietà deve restituire true se, e solo se, l'implementazione è stata completata in modo sincrono. In precedenza, la proprietà non è stata controllata.
  - Librerie interessate: mscorlib, System. Threading. Tasks
  - Azione consigliata: assicurarsi che TaskFactory. FromAsync restituisca true correttamente

- DataObject. GetData ora recupera i dati come UTF-8
  - Per le app destinate a .NET Framework 4 o eseguite in .NET Framework 4.5.1 o versioni precedenti, DataObject. GetData recupera i dati in formato HTML come stringa ASCII. Di conseguenza, i caratteri non ASCII (i caratteri i cui codici ASCII sono maggiori di 0x7F) sono rappresentati da due caratteri casuali. #N # #N # per le app destinate a .NET Framework 4,5 o versioni successive ed eseguite nel .NET Framework 4.5.2, `DataObject.GetData` recupera i dati in formato HTML come UTF-8, che rappresenta i caratteri superiori a 0x7F correttamente.
  - Librerie interessate: Glo
  - Azione consigliata: assicurarsi che i dati recuperati siano il formato desiderato

- XmlWriter genera una coppia di surrogati non validi
  - Per le app destinate a .NET Framework 4.5.2 o versioni precedenti, la scrittura di una coppia di surrogati non valida usando la gestione del fallback delle eccezioni non genera sempre un'eccezione. Per le app destinate a .NET Framework 4,6, il tentativo di scrivere una coppia di surrogati non valida genera una `ArgumentException`.
  - Librerie interessate: System. XML, System. XML. ReaderWriter
  - Azione consigliata: assicurarsi di non scrivere una coppia di surrogati non valida che provocherà l'eccezione dell'argomento

- HtmlTextWriter non esegue il rendering di `<br/>` elemento correttamente
  - A partire da .NET Framework 4,6, la chiamata `HtmlTextWriter.RenderBeginTag()` e `HtmlTextWriter.RenderEndTag()` con un elemento `<BR />` inserirà correttamente solo un `<BR />` (invece di due)
  - Librerie interessate: System. Web
  - Azione consigliata: assicurarsi di inserire la quantità di `<BR />` che si prevede di visualizzare, in modo che nel processo di produzione non venga visualizzato alcun comportamento casuale

- La chiamata di CreateDefaultAuthorizationContext con un argomento null è stata modificata
  - L'implementazione dell'oggetto AuthorizationContext restituito da una chiamata al `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` con un argomento authorizationPolicies null è stata modificata nell'.NET Framework 4,6.
  - Librerie interessate: System. IdentityModel
  - Azione consigliata: assicurarsi di gestire il nuovo comportamento previsto quando sono presenti criteri di autorizzazione null
  
- RSACng ora carica correttamente le chiavi RSA della dimensione della chiave non standard
  - Nelle versioni .NET Framework precedenti a 4.6.2, i clienti con dimensioni delle chiavi non standard per i certificati RSA non sono in grado di accedere a tali chiavi tramite i metodi di estensione `GetRSAPublicKey()` e `GetRSAPrivateKey()`. Viene generata un'`CryptographicException` con il messaggio "la dimensione della chiave richiesta non è supportata". Con il .NET Framework 4.6.2 questo problema è stato risolto. Analogamente, `RSA.ImportParameters()` e `RSACng.ImportParameters()` ora funzionano con dimensioni della chiave non standard senza generare `CryptographicException`.
  - Librerie interessate: mscorlib, System. Core
  - Azione consigliata: verificare che le chiavi RSA funzionino come previsto

- I controlli di due punti del percorso sono più severi
  - In .NET Framework 4.6.2 sono state apportate alcune modifiche per supportare i percorsi non supportati in precedenza (sia nella lunghezza che nel formato). Verifica che la sintassi del separatore di unità (due punti) sia stata resa più corretta, con l'effetto collaterale di bloccare alcuni percorsi URI in alcune API del percorso SELECT in cui sono stati tollerati.
  - Librerie interessate: mscorlib, System. Runtime. Extensions
  - Azione consigliata:

- Chiamate a costruttori ClaimsIdentity
  - A partire da .NET Framework 4.6.2, è stata apportata una modifica al modo in cui `T:System.Security.Claims.ClaimsIdentity` costruttori con un parametro `T:System.Security.Principal.IIdentity` impostano la proprietà `P:System.Security.Claims.ClaimsIdentify.Actor`. Se l'argomento `T:System.Security.Principal.IIdentity` è un oggetto `T:System.Security.Claims.ClaimsIdentity` e la proprietà `P:System.Security.Claims.ClaimsIdentify.Actor` di tale oggetto `T:System.Security.Claims.ClaimsIdentity` non è `null`, la proprietà `P:System.Security.Claims.ClaimsIdentify.Actor` viene associata tramite il metodo `M:System.Security.Claims.ClaimsIdentity.Clone`. Nel Framework 4.6.1 e nelle versioni precedenti, la proprietà `P:System.Security.Claims.ClaimsIdentify.Actor` è associata come riferimento esistente. A causa di questa modifica, a partire dalla .NET Framework 4.6.2, la proprietà `P:System.Security.Claims.ClaimsIdentify.Actor` del nuovo oggetto `T:System.Security.Claims.ClaimsIdentity` non è uguale alla proprietà `P:System.Security.Claims.ClaimsIdentify.Actor` dell'argomento `T:System.Security.Principal.IIdentity` del costruttore. Nel .NET Framework 4.6.1 e versioni precedenti, è uguale.
  - Librerie interessate: mscorlib
  - Azione consigliata: verificare che ClaimsIdentity funzioni come previsto in un nuovo Runtime

- La serializzazione dei caratteri di controllo con DataContractJsonSerializer è ora compatibile con ECMAScript V6 e V8
  - In .NET Framework 4.6.2 e versioni precedenti, DataContractJsonSerializer non serializza alcuni caratteri di controllo speciali, ad esempio \b, \f e \t, in modo compatibile con gli standard ECMAScript V6 e V8. A partire da .NET Framework 4,7, la serializzazione di questi caratteri di controllo è compatibile con ECMAScript V6 e V8.
  - Librerie interessate: System. Runtime. Serialization. JSON
  - Azione consigliata: garantire lo stesso comportamento con DataContractJsonSerializer
