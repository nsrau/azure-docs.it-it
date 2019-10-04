---
title: Archivio di coppie chiave-valore di Configurazione app di Azure | Microsoft Docs
description: Panoramica sulla modalità di archiviazione dei dati di configurazione in Configurazione app di Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: c7a7e7994ef5e16640f59efdc672f6793bc4f18d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706462"
---
# <a name="keys-and-values"></a>Chiavi e valori

Configurazione app di Azure archivia i dati di configurazione sotto forma di coppie chiave-valore. Le coppie chiave-valore costituiscono un modo semplice e flessibile per rappresentare vari tipi di impostazioni delle applicazioni già noti agli sviluppatori.

## <a name="keys"></a>Chiavi

La chiavi fungono da nome per le coppie chiave-valore e vengono usate per archiviare e recuperare i valori corrispondenti. In genere le chiavi vengono organizzate in uno spazio dei nomi gerarchico usando un delimitatore di caratteri, ad esempio `/` o `:`. Usare la convenzione più indicata per l'applicazione. Configurazione app tratta le chiavi come un insieme. Non analizza le chiavi per individuare come sono strutturati i relativi nomi, né applica alcuna regola.

L'utilizzo dei dati configurazione all'interno dei framework applicazione potrebbe imporre schemi di denominazione specifici per le coppie chiave-valore. Ad esempio, il framework Spring Cloud di Java definisce che le risorse `Environment` che forniscono le impostazioni per un'applicazione Spring vengano parametrizzate da variabili che includono il *nome dell'applicazione* e il *profilo*. Le chiavi per Spring Cloud relative ai dati di configurazione iniziano in genere con questi due elementi, separati da un delimitatore.

Le chiavi archiviate in Configurazione sono stringhe basate su Unicode con distinzione tra maiuscole e minuscole. Le chiavi *app1* e *App1* sono distinte in un archivio di configurazione app. Tenere presente questo aspetto quando si usano le impostazioni di configurazione all'interno di un'applicazione, perché alcuni framework gestiscono le chiavi di configurazione senza fare distinzione tra maiuscole e minuscole. Ad esempio, il sistema di configurazione ASP.NET Core tratta le chiavi come stringhe senza distinzione tra maiuscole e minuscole. Per evitare comportamenti imprevedibili quando si eseguono query su Configurazione app all'interno di un'applicazione ASP.NET Core, non usare chiavi che differiscono solo per quanto riguarda l'uso di maiuscole e minuscole.

Nei nomi delle chiavi immessi in Configurazione app è possibile usare qualsiasi carattere Unicode, ad eccezione di `*`, `,` e `\`. Questi caratteri sono riservati. Per includere un carattere riservato, è necessario usare `\{Reserved Character}` per specificare il carattere di escape. Per una coppia chiave-valore è previsto un limite di dimensioni di 10.000 caratteri. Questo limite include tutti i caratteri della chiave, il relativo valore e tutti gli attributi facoltativi associati. Entro questo limite è possibile avere molti livelli gerarchici per le chiavi.

### <a name="design-key-namespaces"></a>Progettare gli spazi dei nomi delle chiavi

Per assegnare i nomi alle chiavi usate per i dati di configurazione sono disponibili due approcci generali: piatto o gerarchico. Questi metodi sono simili dal punto di vista dell'utilizzo dell'applicazione, ma l'approccio gerarchico per l'assegnazione dei nomi offre numerosi vantaggi:

* Più facile da leggere. Invece di un'unica sequenza lunga di caratteri, i delimitatori nei nomi di chiavi gerarchici fungono da spazi in una frase, fornendo inoltre interruzioni naturali tra le parole.
* Più facile da gestire. Una gerarchia di nomi di chiavi rappresenta gruppi logici di dati di configurazione.
* Più facile da usare. È più semplice scrivere una query che controlla la presenza di criteri nelle chiavi di una struttura gerarchica e recupera solo una parte dei dati di configurazione. Molti framework di programmazione più recenti includono anche il supporto nativo per i dati di configurazione gerarchici in modo che l'applicazione possa usare set di configurazione specifici.

È possibile organizzare le chiavi gerarchicamente in Configurazione app in molti modi. Queste chiavi possono essere considerate come [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Ogni chiave gerarchica è un *percorso* di risorsa composto da uno o più componenti, uniti da delimitatori. Scegliere il carattere da usare come delimitatore in base ai requisiti dell'applicazione, del linguaggio di programmazione o del framework. Usare più delimitatori per chiavi diverse in Configurazione app.

Ecco diversi esempi di come strutturare i nomi delle chiavi in una gerarchia:

* In base ai servizi dei componenti

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* In base alle aree di distribuzione

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>Chiavi di etichetta

Le coppie chiave-valore di Configurazione app possono facoltativamente avere un attributo etichetta. Le etichette vengono usate per distinguere le coppie chiave-valore con la stessa chiave. Una chiave *app1* con le etichette *A* e *B* corrisponde a due chiavi distinte in un archivio di configurazione app. Per impostazione predefinita, l'etichetta di una coppia chiave-valore è vuota o `null`.

L'etichetta fornisce un modo pratico per creare varianti di una chiave. Un uso comune delle etichette consiste nello specificare più ambienti per la stessa chiave:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Creare versioni di coppie chiave-valore

Configurazione app non crea automaticamente nuove versioni delle coppie chiave-valore quando vengono modificate. Usare quindi le etichette per creare più versioni di una coppia chiave-valore. È ad esempio possibile immettere un numero di versione dell'applicazione o un ID commit di Git nelle etichette per identificare le coppie chiave-valore associate a una particolare build software.

Nelle etichette è possibile usare qualsiasi carattere Unicode, ad eccezione di `*`, `,` e `\`. Questi caratteri sono riservati. Per includere un carattere riservato, è necessario usare `\{Reserved Character}` per specificare il carattere di escape.

### <a name="query-key-values"></a>Eseguire query su coppie chiave-valore

Ogni coppia chiave-valore viene identificata in modo univoco dalla chiave e da un'etichetta che può essere `null`. Per eseguire una query su un archivio di configurazione app e trovare le coppie chiave-valore, si specifica un criterio. L'archivio di configurazione app restituisce tutte le coppie chiave-valore che corrispondono al criterio, oltre ai relativi valori e attributi. Usare i criteri delle chiavi seguenti nelle chiamate dell'API REST a Configurazione app:

| Chiave | |
|---|---|
| `key` è omesso oppure `key=*` | Corrisponde a tutte le chiavi |
| `key=abc` | Corrisponde esattamente al nome della chiave **abc** |
| `key=abc*` | Corrisponde ai nomi delle chiavi che iniziano con **abc** |
| `key=*abc` | Corrisponde ai nomi delle chiavi che terminano con **abc** |
| `key=*abc*` | Corrisponde ai nomi delle chiavi che contengono **abc** |
| `key=abc,xyz` | Corrisponde ai nomi delle chiavi **abc** o **xyz**, con il limite di cinque CSV |

È anche possibile includere i criteri delle etichette seguenti:

| Etichetta | |
|---|---|
| `label` è omesso oppure `label=*` | Corrisponde a qualsiasi etichetta, incluso `null` |
| `label=%00` | Corrisponde all'etichetta `null` |
| `label=1.0.0` | Corrisponde esattamente all'etichetta **1.0.0** |
| `label=1.0.*` | Corrisponde alle etichette che iniziano con **1.0.** |
| `label=*.0.0` | Corrisponde alle etichette che terminano con **.0.0** |
| `label=*.0.*` | Corrisponde alle etichette che contengono **.0.** |
| `label=%00,1.0.0` | Corrisponde alle etichette `null` o **1.0.0**, con il limite di cinque CSV |

## <a name="values"></a>Valori

Anche i valori assegnati alle chiavi sono stringhe Unicode. Per i valori è possibile usare tutti i caratteri Unicode. A ogni valore è associato un tipo di contenuto facoltativo definito dall'utente. Usare questo attributo per archiviare informazioni, ad esempio uno schema di codifica, su un valore che ne consentano l'elaborazione corretta nell'applicazione.

I dati di configurazione archiviati in un archivio di configurazione app, che include tutte le chiavi e tutti i valori, vengono crittografati sia quando sono inattivi che quando sono in movimento. Configurazione app non è una soluzione da usare in sostituzione di Azure Key Vault. Non usarla quindi per archiviare i segreti dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

* [Snapshot temporizzato](./concept-point-time-snapshot.md)  
* [Gestione delle funzionalità](./concept-feature-management.md)  
