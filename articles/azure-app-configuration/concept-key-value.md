---
title: Archivio di coppie chiave-valore di Configurazione app di Azure | Microsoft Docs
description: Panoramica sulla modalità di archiviazione dei dati di configurazione in Configurazione app di Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: f04067358b0b2bae745727a5dd7a1f5554f9f70e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884981"
---
# <a name="key-value-store"></a>Archivio di coppie chiave-valore

Configurazione app di Azure archivia i dati di configurazione come coppie chiave-valore, un modo semplice eppure flessibile di rappresentare vari tipi di impostazioni delle applicazioni con cui gli sviluppatori hanno familiarità.

## <a name="keys"></a>Chiavi

La chiavi fungono da nome per le coppie chiave-valore e vengono usate per archiviare e recuperare i valori corrispondenti. È pratica comune organizzare le chiavi in uno spazio dei nomi gerarchico usando un delimitatore di caratteri, ad esempio `/` o `:`, in base a una convenzione più indicata per l'applicazione. Configurazione app tratta le chiavi come un insieme. Non analizza le chiavi per individuare come sono strutturati i relativi nomi, né vi applica alcuna regola.

L'utilizzo dell'archivio di configurazione all'interno dei framework applicazione potrebbe imporre specifici schemi di denominazione per le coppie chiave-valore. Ad esempio, il framework Spring Cloud di Java definisce che le risorse `Environment` che forniscono le impostazioni per un'applicazione Spring vengano parametrizzate da variabili con il *nome dell'applicazione* e il *profilo*. Le chiavi per Spring Clould relative ai dati di configurazione iniziano in genere con questi due elementi, separati da un delimitatore.

Le chiavi archiviate in Configurazione sono stringhe basate su Unicode con distinzione tra maiuscole e minuscole. Le chiavi *app1* e *App1* sono distinte in un archivio di configurazione app. Tenere presente questo aspetto quando si usano le impostazioni di configurazione all'interno di un'applicazione, perché alcuni framework gestiscono le chiavi di configurazione senza fare distinzione tra maiuscole e minuscole. Ad esempio, il sistema di configurazione ASP.NET Core tratta le chiavi come stringhe senza distinzione tra maiuscole e minuscole. Per evitare comportamenti imprevedibili quando si eseguono query su Configurazione app all'interno di un'applicazione ASP.NET Core, non è consigliabile usare chiavi che differiscono solo per quanto riguarda l'uso di maiuscole e minuscole.

Nei nomi delle chiavi immessi in Configurazione app è consentito usare qualsiasi carattere Unicode, ad eccezione di `*`, `,` e `\` che sono riservati. Se è necessario includere un carattere riservato, bisogna impostarlo come escape usando `\{Reserved Character}`. Per una coppia chiave-valore è previsto un limite di dimensioni di 10.000 caratteri. Sono inclusi tutti i caratteri della chiave, il relativo valore e tutti gli attributi facoltativi associati. Entro questo limite è possibile avere molti livelli gerarchici per le chiavi.

### <a name="designing-key-namespaces"></a>Progettazione degli spazi dei nomi delle chiavi

Per assegnare i nomi alle chiavi usate per i dati di configurazione sono disponibili due approcci generali: piatto o gerarchico. Questi metodi sono molti simili dal punto di vista dell'utilizzo dell'applicazione, ma il secondo offre numerosi vantaggi:

* Più facile da leggere. Invece di un'unica sequenza lunga di caratteri, i delimitatori nei nomi di chiavi gerarchici fungono da spazi in una frase e forniscono interruzioni naturali tra le parole.
* Più facile da gestire. Una gerarchia di nomi di chiavi rappresenta gruppi logici di dati di configurazione.
* Più facile da usare. È più semplice scrivere una query che controlla la presenza di criteri nelle chiavi di una struttura gerarchica e recupera solo una parte dei dati di configurazione. Inoltre, molti framework di programmazione più recenti includono il supporto nativo per i dati di configurazione gerarchici per cui l'applicazione può usare specifici set di configurazione.

È possibile organizzare le chiavi gerarchicamente in Configurazione app in molti modi. Queste chiavi possono essere considerate come [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Ogni chiave gerarchica è un *percorso* di risorsa composto da uno o più componenti, uniti da delimitatori. È possibile scegliere il carattere da usare come delimitatore in base ai requisiti dell'applicazione, del linguaggio di programmazione o del framework. In Configurazione app è possibile usare più delimitatori per chiavi diverse.

Ecco diversi esempi di come strutturare i nomi delle chiavi in una gerarchia:

* In base agli ambienti

        AppName:Test:DB:Endpoint
        AppName:Staging:DB:Endpoint
        AppName:Production:DB:Endpoint

* In base ai servizi dei componenti

        AppName:Service1:Test:DB:Endpoint
        AppName:Service1:Staging:DB:Endpoint
        AppName:Service1:Production:DB:Endpoint
        AppName:Service2:Test:DB:Endpoint
        AppName:Service2:Staging:DB:Endpoint
        AppName:Service2:Production:DB:Endpoint

* In base alle aree di distribuzione

        AppName:Production:Region1:DB:Endpoint
        AppName:Production:Region2:DB:Endpoint

### <a name="versioning-key-values"></a>Versioni delle coppie chiave-valore

Le coppie chiave-valore di Configurazione app possono facoltativamente avere un attributo **label**. Le etichette vengono usate per differenziare le coppie chiave-valore con la stessa chiave. Una chiave *app1* con le etichette *v1* e *v2* forma due coppie chiave-valore distinte in un archivio di configurazione app. Per impostazione predefinita, l'etichetta di una coppia chiave-valore è vuota o `null`.

Configurazione app non crea automaticamente le versioni delle coppie chiave-valore quando vengono modificate. È possibile usare le etichette per creare più versioni di una coppia chiave-valore. È ad esempio possibile immettere un numero di versione dell'applicazione o un ID commit di Git nelle etichette per identificare le coppie chiave-valore associate a una particolare build software.

Nelle etichette è consentito usare qualsiasi carattere Unicode, ad eccezione di`*`, `,` e `\` che sono riservati. Se è necessario includere un carattere riservato, bisogna impostarlo come escape usando `\{Reserved Character}`.

### <a name="querying-key-values"></a>Query delle coppie chiave-valore

Ogni coppia chiave-valore viene identificata in modo univoco dalla chiave e da un'etichetta che può essere `null`. Per eseguire una query su un archivio di configurazione app per trovare le coppie chiave-valore si specifica un criterio. L'archivio di configurazione app restituisce tutte le coppie chiave-valore che corrispondono al criterio, oltre ai relativi valori e attributi. È possibile usare i criteri delle chiavi seguenti nelle chiamate dell'API REST a Configurazione app:

| Chiave | |
|---|---|
| `key` è omesso oppure `key=*` | Corrisponde a tutte le chiavi |
| `key=abc` | Corrisponde esattamente al nome della chiave **abc**. |
| `key=abc*` | Corrisponde ai nomi delle chiavi che iniziano con **abc** |
| `key=*abc` | Corrisponde ai nomi delle chiavi che terminano con **abc** |
| `key=*abc*` | Corrisponde ai nomi delle chiavi che contengono **abc** |
| `key=abc,xyz` | Corrisponde ai nomi delle chiavi **abc** o **xyz**, con il limite di 5 CSV |

È anche possibile includere i criteri delle etichette seguenti:

| Etichetta | |
|---|---|
| `label` è omesso oppure `label=*` | Corrisponde a qualsiasi etichetta, incluso `null` |
| `label=%00` | Corrisponde all'etichetta `null`. |
| `label=1.0.0` | Corrisponde esattamente all'etichetta **1.0.0** |
| `label=1.0.*` | Corrisponde alle etichette che iniziano con **1.0.** |
| `label=*.0.0` | Corrisponde alle etichette che terminano con **.0.0** |
| `label=*.0.*` | Corrisponde alle etichette che contengono **.0.** |
| `label=%00,1.0.0` | Corrisponde alle etichette `null` o **1.0.1**, con il limite di 5 CSV |

## <a name="values"></a>Valori

Anche i valori assegnati alle chiavi sono stringhe Unicode. Per i valori è possibile usare tutti i caratteri Unicode. A ogni valore è associato un **tipo di contenuto** facoltativo definito dall'utente. È possibile usare questo attributo per archiviare informazioni, ad esempio uno schema di codifica, su un valore che ne consentano l'elaborazione corretta nell'applicazione.

I dati di configurazione archiviati in un archivio di configurazione app, tra cui tutte le chiavi e tutti i valori, vengono crittografati nello stato inattivo e in movimento. Ciononostante, Configurazione app non è una soluzione sostitutiva di Azure Key Vault. Non archiviare i segreti dell'applicazione in tale servizio.

## <a name="next-steps"></a>Passaggi successivi

* [Concetto: Snapshot temporizzato](concept-point-time-snapshot.md)  
