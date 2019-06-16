---
title: Interfaccia della riga di comando - API Knowledge Exploration Service
titlesuffix: Azure Cognitive Services
description: Usare l'interfaccia della riga di comando per compilare file di indice e grammatica da dati strutturati e quindi distribuirli come servizi Web.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 018552982a8ece3bbbaea2d60e2a6e64f681f822
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60815134"
---
# <a name="command-line-interface"></a>Interfaccia della riga di comando

L'interfaccia della riga di comando di Knowledge Exploration Service (KES) offre la possibilità di compilare file di indice e grammatica da dati strutturati e distribuirli come servizi Web.  Usa la sintassi generale: `kes.exe <command> <required_args> [<optional_args>]`.  È possibile eseguire `kes.exe` senza argomenti per visualizzare un elenco dei comandi oppure `kes.exe <command>` per visualizzare un elenco degli argomenti disponibili per il comando specificato.  Di seguito è riportato un elenco dei comandi disponibili:

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="buildindex-command"></a>Comando build_index

Il comando **build_index** compila un file di indice binario da un file di definizione dello schema e un file di dati degli oggetti da indicizzare.  Il file di indice risultante può essere usato per valutare espressioni di query strutturate o per generare interpretazioni di query in linguaggio naturale in combinazione con un file di grammatica compilato.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parametro      | Descrizione               |
|----------------|---------------------------|
| `<schemaFile>` | Percorso dello schema di input |
| `<dataFile>`   | Percorso dei dati di input   |
| `<indexFile>`  | Percorso dell'indice di output |
| `--description <description>` | Stringa di descrizione |
| `--remote <vmSize>`           | Dimensioni della VM per la compilazione remota |

Questi file possono essere specificati da percorsi di file locali o percorsi URL di BLOB di Azure.  Il file di schema descrive la struttura degli oggetti da indicizzare e le operazioni da supportare (vedere [Formato dello schema](SchemaFormat.md)).  Il file di dati enumera gli oggetti e i valori di attributo da indicizzare (vedere [Formato dati](DataFormat.md)).  Quando la compilazione ha esito positivo, il file di indice di output contiene una rappresentazione compressa dei dati di input che supporta le operazioni desiderate.  

Facoltativamente è possibile specificare una stringa di descrizione per identificare in seguito un indice binario usando il comando **describe_index**.  

Per impostazione predefinita, l'indice viene compilato nel computer locale.  All'esterno dell'ambiente di Azure, le compilazioni locali sono limitate a file di dati contenenti un massimo di 10.000 oggetti.  Quando si specifica il flag --remote, l'indice verrà compilato in una macchina virtuale di Azure temporanea delle dimensioni specificate.  Questo consente di compilare in modo efficiente indici di grandi dimensioni usando macchine virtuali di Azure con più memoria.  Per evitare il paging, che rallenta il processo di compilazione, è consigliabile usare una VM con una quantità di RAM di tre volte superiore rispetto alle dimensioni del file di dati di input.  Per un elenco delle dimensioni di VM disponibili, vedere [Dimensioni delle macchine virtuali](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Per compilazioni più veloci, preordinare gli oggetti nel file di dati riducendo la probabilità.

<a name="build_grammar-command"></a>

## <a name="buildgrammar-command"></a>Comando build_grammar

Il comando **build_grammar** compila una grammatica specificata in XML in un file di grammatica binario.  Il file di grammatica risultante può essere usato in combinazione con un file di indice per generare interpretazioni di query in linguaggio naturale.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parametro       | Descrizione               |
|-----------------|---------------------------|
| `<xmlFile>`     | Percorso della specifica di grammatica XML di input |
| `<grammarFile>` | Percorso dell'output di grammatica compilato         |

Questi file possono essere specificati da percorsi di file locali o percorsi URL di BLOB di Azure.  La specifica di grammatica descrive il set ponderato di espressioni in linguaggio naturale e le relative interpretazioni semantiche (vedere [Formato di grammatica](GrammarFormat.md)).  Quando la compilazione ha esito positivo, il file di grammatica di output contiene una rappresentazione binaria della specifica di grammatica per consentire la decodifica veloce.

<a name="host_service-command"/>

## <a name="hostservice-command"></a>Comando host_service

Il comando **host_service** ospita un'istanza del servizio KES nel computer locale.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parametro       | Descrizione                |
|-----------------|----------------------------|
| `<grammarFile>` | Percorso della grammatica binaria di input         |
| `<indexFile>`   | Percorso dell'indice binario di input           |
| `--port <port>` | Numero di porta locale.  Predefinito: 8000 |

Questi file possono essere specificati da percorsi di file locali o percorsi URL di BLOB di Azure.  Un servizio Web verrà ospitato in http://localhost:&lt ;porta&gt; /.  Vedere [Interfaccia API Web](WebAPI.md) per un elenco delle operazioni supportate.

All'esterno dell'ambiente di Azure, i servizi ospitati in locale sono limitati a file di indice di dimensioni fino a 1 MB, 10 richieste al secondo e 1.000 chiamate in totale.  Per superare queste limitazioni, eseguire **host_service** all'interno di una macchina virtuale di Azure oppure distribuire un servizio cloud di Azure usando **deploy_service**.

<a name="deploy_service-command"/>

## <a name="deployservice-command"></a>Comando deploy_service

Il comando **deploy_service** distribuisce un'istanza del servizio KES in un servizio cloud di Azure.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parametro       | Descrizione                  |
|-----------------|------------------------------|
| `<grammarFile>` | Percorso della grammatica binaria di input           |
| `<indexFile>`   | Percorso dell'indice binario di input             |
| `<serviceName>` | Nome del servizio cloud di destinazione |
| `<vmSize>`      | Dimensioni della macchina virtuale del servizio cloud     |
| `--slot <slot>` | Slot del servizio cloud: "staging" (impostazione predefinita), "production" |

Questi file possono essere specificati da percorsi di file locali o percorsi URL di BLOB di Azure.  Il nome del servizio specifica un servizio cloud di Azure preconfigurato (vedere [Come creare e distribuire un servizio cloud](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  Il comando distribuirà automaticamente il servizio KES al servizio cloud di Azure specificato, usando macchine virtuali delle dimensioni specificate.  Per evitare il paging, che riduce significativamente le prestazioni, è consigliabile usare una macchina virtuale con 1 GB di RAM in più rispetto alle dimensioni del file di indice di input.  Per un elenco delle dimensioni di VM disponibili, vedere [Dimensioni dei servizi cloud](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Per impostazione predefinita, il servizio viene distribuito nell'ambiente di gestione temporanea. Facoltativamente, è possibile eseguire l'override dell'impostazione tramite il parametro --slot.  Vedere [Interfaccia API Web](WebAPI.md) per un elenco delle operazioni supportate.

<a name="describe_index-command"/>

## <a name="describeindex-command"></a>Comando describe_index

Il comando **describe_index** restituisce informazioni su un file di indice, inclusi lo schema e una descrizione.

`kes.exe describe_index <indexFile>`

| Parametro     | Descrizione      |
|---------------|------------------|
| `<indexFile>` | Percorso dell'indice di input |

Questo file può essere specificato da un percorso file locale o da un percorso URL di un BLOB di Azure.  La stringa di descrizione di output può essere specificata tramite il parametro --description del comando **build_index**.

<a name="describe_grammar-command"/>

## <a name="describegrammar-command"></a>Comando describe_grammar

Il comando **describe_grammar** restituisce la specifica di grammatica originale usata per compilare la grammatica binaria.

`kes.exe describe_grammar <grammarFile>`

| Parametro       | Descrizione      |
|-----------------|------------------|
| `<grammarFile>` | Percorso della grammatica di input |

Questo file può essere specificato da un percorso file locale o da un percorso URL di un BLOB di Azure.

