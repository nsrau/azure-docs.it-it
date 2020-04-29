---
title: Informazioni sull'archivio chiave-valore di configurazione app Azure
description: Informazioni sul modo in cui i dati di configurazione vengono archiviati nella configurazione app Azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 0b83a35d912c97ae25bc2d69d076e8eae8ca490f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523605"
---
# <a name="keys-and-values"></a>Chiavi e valori

Configurazione app di Azure archivia i dati di configurazione sotto forma di coppie chiave-valore. Le coppie chiave-valore rappresentano una rappresentazione semplice e flessibile delle impostazioni dell'applicazione utilizzate dagli sviluppatori.

## <a name="keys"></a>Chiavi

Le chiavi servono come identificatori per le coppie chiave-valore e vengono usate per archiviare e recuperare i valori corrispondenti. In genere le chiavi vengono organizzate in uno spazio dei nomi gerarchico usando un delimitatore di caratteri, ad esempio `/` o `:`. Usare una convenzione ideale per l'applicazione. Configurazione app tratta le chiavi come un insieme. Non analizza le chiavi per individuare come sono strutturati i relativi nomi, né applica alcuna regola.

Di seguito sono riportati due esempi di nomi chiave strutturati in una gerarchia:

* In base ai servizi dei componenti

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* In base alle aree di distribuzione

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

L'uso di dati di configurazione all'interno di Framework applicazione può determinare schemi di denominazione specifici per i valori di chiave. Ad esempio, il framework Spring cloud di Java `Environment` definisce le risorse che forniscono le impostazioni a un'applicazione Spring.  Sono parametrizzate da variabili che includono il nome e *il profilo* *dell'applicazione* . Le chiavi per Spring Cloud relative ai dati di configurazione iniziano in genere con questi due elementi, separati da un delimitatore.

Le chiavi archiviate in Configurazione sono stringhe basate su Unicode con distinzione tra maiuscole e minuscole. Le chiavi *app1* e *App1* sono distinte in un archivio di Configurazione app. Tenere presente questo aspetto quando si usano le impostazioni di configurazione all'interno di un'applicazione, perché alcuni framework gestiscono le chiavi di configurazione senza fare distinzione tra maiuscole e minuscole. Non è consigliabile usare case per distinguere le chiavi.

È possibile utilizzare qualsiasi carattere Unicode nei nomi delle chiavi, `*`ad `,`eccezione di `\`, e.  Se è necessario includere uno di questi caratteri riservati, eseguire l'escape usando `\{Reserved Character}`. 

Una coppia chiave-valore prevede un limite di dimensioni combinate di 10 KB. Questo limite include tutti i caratteri della chiave, il relativo valore e tutti gli attributi facoltativi associati. Entro questo limite è possibile avere molti livelli gerarchici per le chiavi.

### <a name="design-key-namespaces"></a>Progettare gli spazi dei nomi delle chiavi

Per assegnare i nomi alle chiavi usate per i dati di configurazione sono disponibili due approcci generali: piatto o gerarchico. Questi metodi sono simili dal punto di vista dell'utilizzo dell'applicazione, ma l'approccio gerarchico per l'assegnazione dei nomi offre numerosi vantaggi:

* Più facile da leggere. I delimitatori in un nome di chiave gerarchica funzionano come spazi in una frase. fornendo inoltre interruzioni naturali tra le parole.
* Più facile da gestire. Una gerarchia di nomi di chiavi rappresenta gruppi logici di dati di configurazione.
* Più facile da usare. È più semplice scrivere una query che controlla la presenza di criteri nelle chiavi di una struttura gerarchica e recupera solo una parte dei dati di configurazione. Molti framework di programmazione più recenti includono anche il supporto nativo per i dati di configurazione gerarchici in modo che l'applicazione possa usare set di configurazione specifici.

È possibile organizzare le chiavi gerarchicamente in Configurazione app in molti modi. Queste chiavi possono essere considerate come [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Ogni chiave gerarchica è un *percorso* di risorsa composto da uno o più componenti, uniti da delimitatori. Scegliere il carattere da usare come delimitatore in base ai requisiti dell'applicazione, del linguaggio di programmazione o del framework. Usare più delimitatori per chiavi diverse in Configurazione app.

### <a name="label-keys"></a>Chiavi di etichetta

Le coppie chiave-valore di Configurazione app possono facoltativamente avere un attributo etichetta. Le etichette vengono usate per distinguere le coppie chiave-valore con la stessa chiave. Una chiave *app1* con le etichette *A* e *B* corrisponde a due chiavi distinte in un archivio di Configurazione app. Per impostazione predefinita, un valore di chiave non dispone di un'etichetta. Per fare riferimento in modo esplicito a un valore di chiave `\0` senza etichetta, usare (URL `%00`codificato come).

L'etichetta fornisce un modo pratico per creare varianti di una chiave. Un uso comune delle etichette consiste nello specificare più ambienti per la stessa chiave:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Creare versioni di coppie chiave-valore

La configurazione dell'app non prevede la versione automatica dei valori di chiave. Usare quindi le etichette per creare più versioni di una coppia chiave-valore. È ad esempio possibile immettere un numero di versione dell'applicazione o un ID commit di Git nelle etichette per identificare le coppie chiave-valore associate a una particolare build software.

Nelle etichette è possibile usare qualsiasi carattere Unicode, ad eccezione di `*`, `,` e `\`. Questi caratteri sono riservati. Per includere un carattere riservato, è necessario usare `\{Reserved Character}` per specificare il carattere di escape.

### <a name="query-key-values"></a>Eseguire query su coppie chiave-valore

Ogni coppia chiave-valore viene identificata in modo univoco dalla chiave e da un'etichetta che può essere `null`. Per eseguire una query su un archivio di Configurazione app e trovare le coppie chiave-valore, si specifica un criterio. L'archivio di Configurazione app restituisce tutte le coppie chiave-valore che corrispondono al criterio, oltre ai relativi valori e attributi. Usare i criteri delle chiavi seguenti nelle chiamate dell'API REST a Configurazione app:

| Chiave | |
|---|---|
| `key` è omesso oppure `key=*` | Corrisponde a tutte le chiavi |
| `key=abc` | Corrisponde esattamente al nome della chiave **abc** |
| `key=abc*` | Corrisponde ai nomi delle chiavi che iniziano con **abc** |
| `key=abc,xyz` | Corrisponde ai nomi delle chiavi **ABC** o **XYZ**. Limitato a cinque CSVs |

È anche possibile includere i criteri delle etichette seguenti:

| Label | |
|---|---|
| `label` è omesso oppure `label=*` | Corrisponde a qualsiasi etichetta, incluso `null` |
| `label=%00` | Corrisponde all'etichetta `null` |
| `label=1.0.0` | Corrisponde esattamente all'etichetta **1.0.0** |
| `label=1.0.*` | Corrisponde alle etichette che iniziano con **1.0.** |
| `label=%00,1.0.0` | Corrisponde alle etichette `null` o **1.0.0**, con il limite di cinque CSV |

## <a name="values"></a>Valori

Anche i valori assegnati alle chiavi sono stringhe Unicode. Per i valori è possibile usare tutti i caratteri Unicode. A ogni valore è associato un tipo di contenuto facoltativo definito dall'utente. Utilizzare questo attributo per archiviare le informazioni su un valore che consente all'applicazione di elaborarlo correttamente.

I dati di configurazione archiviati in un archivio di configurazione dell'app vengono crittografati a riposo e in transito. Le chiavi non sono crittografate a riposo. Configurazione app non è una soluzione da usare in sostituzione di Azure Key Vault. Non usarla quindi per archiviare i segreti dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

* [Snapshot temporizzato](./concept-point-time-snapshot.md)  
* [Gestione delle funzionalità](./concept-feature-management.md)  
