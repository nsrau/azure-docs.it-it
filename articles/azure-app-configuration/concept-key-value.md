---
title: Informazioni sull'archivio chiave-valore di configurazione app Azure
description: Informazioni sul modo in cui i dati di configurazione vengono archiviati nella configurazione app Azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 9a0ed747ea0c894214a633bdbc8141e95e95b5fb
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830037"
---
# <a name="keys-and-values"></a>Chiavi e valori

App Azure configurazione archivia i dati di configurazione come valori chiave. I valori chiave sono una rappresentazione semplice e flessibile delle impostazioni dell'applicazione utilizzate dagli sviluppatori.

## <a name="keys"></a>Chiavi

Le chiavi servono come identificatori per i valori chiave e vengono usate per archiviare e recuperare i valori corrispondenti. In genere le chiavi vengono organizzate in uno spazio dei nomi gerarchico usando un delimitatore di caratteri, ad esempio `/` o `:`. Usare una convenzione ideale per l'applicazione. Configurazione app tratta le chiavi come un insieme. Non analizza le chiavi per individuare come sono strutturati i relativi nomi, né applica alcuna regola.

Di seguito è riportato un esempio di nomi di chiave strutturati in una gerarchia basata su Servizi componenti:

```aspx
    AppName:Service1:ApiEndpoint
    AppName:Service2:ApiEndpoint
```

L'uso di dati di configurazione all'interno di Framework applicazione può determinare schemi di denominazione specifici per i valori di chiave. Ad esempio, il framework Spring cloud di Java definisce `Environment` le risorse che forniscono le impostazioni a un'applicazione Spring.  Sono parametrizzate da variabili che includono il nome e *il profilo* *dell'applicazione* . Le chiavi per Spring Cloud relative ai dati di configurazione iniziano in genere con questi due elementi, separati da un delimitatore.

Le chiavi archiviate in Configurazione sono stringhe basate su Unicode con distinzione tra maiuscole e minuscole. Le chiavi *app1* e *App1* sono distinte in un archivio di Configurazione app. Tenere presente questo aspetto quando si usano le impostazioni di configurazione all'interno di un'applicazione, perché alcuni framework gestiscono le chiavi di configurazione senza fare distinzione tra maiuscole e minuscole. Non è consigliabile usare case per distinguere le chiavi.

È possibile utilizzare qualsiasi carattere Unicode nei nomi delle chiavi ad eccezione di `%` . Un nome di chiave non può essere `.` o `..` . È previsto un limite di dimensioni combinate di 10 KB per un valore chiave. Questo limite include tutti i caratteri della chiave, il relativo valore e tutti gli attributi facoltativi associati. Entro questo limite è possibile avere molti livelli gerarchici per le chiavi.

### <a name="design-key-namespaces"></a>Progettare gli spazi dei nomi delle chiavi

Per assegnare i nomi alle chiavi usate per i dati di configurazione sono disponibili due approcci generali: piatto o gerarchico. Questi metodi sono simili dal punto di vista dell'utilizzo dell'applicazione, ma l'approccio gerarchico per l'assegnazione dei nomi offre numerosi vantaggi:

* Più facile da leggere. I delimitatori in un nome di chiave gerarchica funzionano come spazi in una frase. fornendo inoltre interruzioni naturali tra le parole.
* Più facile da gestire. Una gerarchia di nomi di chiavi rappresenta gruppi logici di dati di configurazione.
* Più facile da usare. È più semplice scrivere una query che controlla la presenza di criteri nelle chiavi di una struttura gerarchica e recupera solo una parte dei dati di configurazione. Molti framework di programmazione più recenti includono anche il supporto nativo per i dati di configurazione gerarchici in modo che l'applicazione possa usare set di configurazione specifici.

È possibile organizzare le chiavi gerarchicamente in Configurazione app in molti modi. Queste chiavi possono essere considerate come [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Ogni chiave gerarchica è un *percorso* di risorsa composto da uno o più componenti, uniti da delimitatori. Scegliere il carattere da usare come delimitatore in base ai requisiti dell'applicazione, del linguaggio di programmazione o del framework. Usare più delimitatori per chiavi diverse in Configurazione app.

### <a name="label-keys"></a>Chiavi di etichetta

Le coppie chiave-valore di Configurazione app possono facoltativamente avere un attributo label. Le etichette vengono usate per differenziare le coppie chiave-valore con la stessa chiave. Una chiave *app1* con le etichette *A* e *B* corrisponde a due chiavi distinte in un archivio di Configurazione app. Per impostazione predefinita, un valore chiave non dispone di un'etichetta. Per fare riferimento in modo esplicito a un valore chiave senza etichetta, usare `\0` (URL codificato come `%00` ).

L'etichetta fornisce un modo pratico per creare varianti di una chiave. Un uso comune delle etichette consiste nello specificare più ambienti per la stessa chiave:

```
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>Valori chiave versione

Usare le etichette come modo per creare più versioni di un valore chiave. È ad esempio possibile immettere un numero di versione dell'applicazione o un ID commit di Git nelle etichette per identificare le coppie chiave-valore associate a una particolare build software.

> [!NOTE]
> Se si stanno cercando versioni delle modifiche, la configurazione dell'app mantiene automaticamente le modifiche apportate a un valore chiave in un determinato periodo di tempo. Per altri dettagli, vedere [snapshot temporizzato](./concept-point-time-snapshot.md) .

### <a name="query-key-values"></a>Chiave di query-valori

Ogni coppia chiave-valore viene identificata in modo univoco dalla chiave e da un'etichetta che può essere `\0`. Si esegue una query su un archivio di configurazione delle app per i valori di chiave specificando un modello. L'archivio di configurazione dell'app restituisce tutti i valori chiave che corrispondono al modello, inclusi i valori e gli attributi corrispondenti. Usare i criteri delle chiavi seguenti nelle chiamate dell'API REST a Configurazione app:

| Chiave | Descrizione |
|---|---|
| `key` è omesso oppure `key=*` | Corrisponde a tutte le chiavi |
| `key=abc` | Corrisponde esattamente al nome della chiave **abc** |
| `key=abc*` | Corrisponde ai nomi delle chiavi che iniziano con **abc** |
| `key=abc,xyz` | Corrisponde ai nomi delle chiavi **ABC** o **XYZ**. Limitato a cinque CSVs |

È anche possibile includere i criteri delle etichette seguenti:

| Etichetta | Descrizione |
|---|---|
| `label` è omesso oppure `label=*` | Corrisponde a qualsiasi etichetta, incluso `\0` |
| `label=%00` | Corrisponde all'etichetta `\0` |
| `label=1.0.0` | Corrisponde esattamente all'etichetta **1.0.0** |
| `label=1.0.*` | Corrisponde alle etichette che iniziano con **1.0.** |
| `label=%00,1.0.0` | Corrisponde alle etichette `\0` o **1.0.0**, con il limite di cinque CSV |

> [!NOTE]
> `*`, `,` e `\` sono caratteri riservati nelle query. Se un carattere riservato viene usato nei nomi o nelle etichette delle chiavi, è necessario eseguire l'escape usando `\{Reserved Character}` nelle query.

## <a name="values"></a>Valori

Anche i valori assegnati alle chiavi sono stringhe Unicode. Per i valori è possibile usare tutti i caratteri Unicode.

### <a name="use-content-type"></a>Usare Content-Type
Ogni chiave-valore nella configurazione dell'app dispone di un attributo Content-Type. Facoltativamente, è possibile usare questo attributo per archiviare informazioni sul tipo di valore in un valore chiave che consente all'applicazione di elaborarlo correttamente. È possibile usare qualsiasi formato per Content-Type. La configurazione dell'app usa i [tipi di supporto]( https://www.iana.org/assignments/media-types/media-types.xhtml) (noti anche come tipi MIME) per i tipi di dati incorporati, ad esempio i flag di funzionalità, i riferimenti Key Vault e i valori di chiave JSON.

## <a name="next-steps"></a>Passaggi successivi

* [Snapshot temporizzato](./concept-point-time-snapshot.md)
* [Gestione delle funzionalità](./concept-feature-management.md)
* [Gestione degli eventi](./concept-app-configuration-event.md)
