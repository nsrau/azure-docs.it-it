---
title: Archiviazione BLOB e output di Azure Data Lake Gen2 da analisi di flusso di Azure
description: Questo articolo descrive l'archiviazione BLOB e Azure Data Lake generazione 2 come output per analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 2ab45f4c64e6993f70f08f04ee413211abb0307d
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875731"
---
# <a name="blob-storage-and-azure-data-lake-gen2-output-from-azure-stream-analytics"></a>Archiviazione BLOB e output di Azure Data Lake Gen2 da analisi di flusso di Azure

Data Lake Storage Gen2 usa Archiviazione di Azure come base per la compilazione di Enterprise Data Lake (EDL) in Azure. Progettato dall'inizio per servire più petabyte di informazioni supportando al contempo centinaia di Gigabit di velocità effettiva, Data Lake Storage Gen2 consente di gestire facilmente grandi quantità di dati. Una parte fondamentale di Data Lake Storage Gen2 è l'aggiunta di uno spazio dei nomi gerarchico all'archiviazione BLOB.

L'archiviazione BLOB offre una soluzione conveniente e scalabile per archiviare grandi quantità di dati non strutturati nel cloud. Per un'introduzione all'archiviazione BLOB e al relativo utilizzo, vedere [Caricare, scaricare ed elencare BLOB con il portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="output-configuration"></a>Configurazione di output

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un BLOB o di output di ADLS Gen2.

| Nome proprietà       | Descrizione                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias di output        | Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione BLOB. |
| Account di archiviazione     | Nome dell'account di archiviazione a cui si sta inviando l'output.               |
| Chiave dell'account di archiviazione | Chiave privata associata all'account di archiviazione.                              |
| Contenitore di archiviazione   | Raggruppamento logico dei BLOB archiviati nel servizio BLOB di Azure. Quando si carica un oggetto BLOB nel servizio BLOB, è necessario specificare un contenitore per il BLOB. |
| Modello di percorso | Facoltativa. Modello di percorso del file usato per scrivere i BLOB nel contenitore specificato. <br /><br /> Nel modello del percorso è possibile scegliere di usare una o più istanze delle variabili di data e ora per specificare la frequenza di scrittura dei BLOB: <br /> {date}, {time} <br /><br />È possibile usare un partizionamento BLOB personalizzato per specificare un nome personalizzato {field} dai dati di evento ai BLOB di partizione. Il nome del campo è un valore alfanumerico e può includere spazi, trattini e caratteri di sottolineatura. Le restrizioni sui campi personalizzati includono le seguenti: <ul><li>Per i nomi dei campi non viene fatta distinzione tra maiuscole e minuscole. Ad esempio, il servizio non distingue tra le colonne "ID" e "id".</li><li>I campi annidati non sono consentiti. Usare, in alternativa, un alias della query di processo per rendere flat il campo.</li><li>Le espressioni non possono essere usate come nome di campo.</li></ul> <br />Questa funzionalità consente di usare anche configurazioni dell'identificatore di formato data/ora personalizzate nel percorso. I formati di data e ora personalizzati devono essere specificati uno alla volta, racchiusi dalla parola chiave {DateTime: \<specifier> }. Gli input consentiti per \<specifier> sono aaaa, mm, m, GG, d, HH, H, mm, M, SS o s. La parola chiave {DateTime: \<specifier> } può essere usata più volte nel percorso per creare configurazioni di data/ora personalizzate. <br /><br />Esempi: <ul><li>Esempio 1: cluster1/logs/{date}/{time}</li><li>Esempio 2: cluster1/logs/{date}</li><li>Esempio 3: cluster1/{client_id}/{date}/{time}</li><li>Esempio 4: cluster1/{datetime:ss}/{myField} in cui la query è: SELECT data.myField AS myField FROM Input;</li><li>Esempio 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />Il timestamp della struttura di cartelle creata segue l'ora UTC e non l'ora locale.<br /><br />La denominazione dei file usa questa convenzione: <br /><br />{Schema prefisso percorso}/schemaHashcode_Guid_Number.extension<br /><br />File di output di esempio:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Per altre informazioni su questa funzionalità, visitare [Partizionamento dell'output dei BLOB personalizzato in Analisi di flusso di Azure](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato data | Facoltativa. Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG |
| Formato ora | Facoltativa. Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati JSON, CSV, Avro e Parquet. |
|Numero minimo di righe |Numero minimo di righe per batch. Per Parquet, ogni batch creerà un nuovo file. Il valore predefinito corrente è 2000 righe e il valore massimo consentito è 10.000 righe.|
|Tempo massimo |Tempo massimo di attesa per batch. Dopo questo periodo di tempo il batch verrà scritto nell'output anche se il requisito relativo al numero minimo di righe non è stato soddisfatto. Il valore predefinito corrente è 1 minuto e il valore massimo consentito è 2 ore. Se l'output del BLOB ha la frequenza del modello di percorso, il tempo di attesa non può essere superiore all'intervallo di tempo della partizione.|
| Codifica    | Se il formato è CSV o JSON, è necessario specificare un formato di codifica. Al momento UTF-8 è l'unico formato di codifica supportato. |
| Delimitatore   | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format      | Applicabile solo per la serializzazione JSON. **Separato da righe** specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Se si seleziona **Separato da righe**, JSON viene letto un oggetto alla volta. L'intero contenuto non è di per sé un oggetto JSON valido. **Matrice** specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da righe, perché non richiede alcuna particolare gestione durante la scrittura del file di output. |

## <a name="blob-output-files"></a>File di output BLOB

Quando si usa l'archiviazione BLOB come output, nei casi seguenti viene creato un nuovo file nel BLOB:

* Se il file supera il numero massimo di blocchi consentiti (attualmente 50.000). È possibile raggiungere il numero massimo di blocchi consentiti senza raggiungere le dimensioni massime consentite per il BLOB. Ad esempio, se la frequenza di output è elevata, ogni blocco può contenere più byte e le dimensioni del file sono superiori. Se la frequenza di output è bassa, ogni blocco contiene meno dati e le dimensioni del file sono inferiori.
* Se l'output contiene una modifica dello schema e il formato di output richiede uno schema fisso (CSV e Avro).
* Se un processo viene riavviato, esternamente con l'arresto e l'avvio da parte di un utente oppure internamente per la manutenzione del sistema o il recupero da errori.
* Se la query è completamente partizionata e, per ogni partizione di output, viene creato un nuovo file.
* Se l'utente elimina un file o un contenitore dell'account di archiviazione.
* Se l'output è partizionato in base al tempo usando lo schema prefisso percorso, quando la query passa all'ora successiva viene usato un nuovo BLOB.
* Se l'output è partizionato da un campo personalizzato, viene creato un nuovo BLOB per ogni chiave di partizione, se inesistente.
* Se l'output è partizionato da un campo personalizzato in cui la cardinalità della chiave di partizione supera 8000 e viene creato un nuovo BLOB per ogni chiave di partizione.

## <a name="partitioning"></a>Partizionamento

Per la chiave di partizione, usare i token {date} e {time} dei campi evento nel modello di percorso. Scegliere il formato della data, ad esempio AAAA/MM/GG, GG/MM/AAAA o MM-GG-AAAA. HH viene usato per il formato dell'ora. L'output del BLOB può essere partizionato da un singolo attributo evento personalizzato {fieldName} o {DateTime: \<specifier> }. Il numero di writer di output segue il partizionamento dell'input per le [query completamente eseguibili](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Dimensione del batch di output

Per la dimensione massima dei messaggi, vedere [limiti di archiviazione di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). La dimensione massima del blocco BLOB è 4 MB e il numero massimo di blocchi BLOB è 50.000. |

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con l'interfaccia della riga di comando di Azure](quick-create-azure-cli.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con un modello di Resource Manager](quick-create-azure-resource-manager.md)
* [Guida introduttiva: creare un processo di analisi di flusso usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Guida introduttiva: Creare un processo di Analisi di flusso di Azure con Visual Studio Code](stream-analytics-quick-create-vs.md)
* [Guida introduttiva: creare un processo di analisi di flusso di Azure in Visual Studio Code](quick-create-vs-code.md)
