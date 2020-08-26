---
title: Output di Azure Data Lake Storage generazione 1 da analisi di flusso di Azure
description: Questo articolo descrive Azure Data Lake Storage generazione 1 come opzione di output per analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 98dfd51783ad3bc0b89f441f89436d3c3d928104
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875717"
---
# <a name="azure-data-lake-storage-gen-1-output-from-azure-stream-analytics"></a>Output di Azure Data Lake Storage generazione 1 da analisi di flusso di Azure

Analisi di flusso supporta Azure Data Lake Storage output di [generazione 1](../data-lake-store/data-lake-store-overview.md) . Azure Data Lake Storage è un repository con iperscalabilità a livello aziendale per carichi di lavoro di analisi di Big Data. È possibile usare Data Lake Storage per archiviare dati di qualsiasi dimensione, tipo e velocità di inserimento per le analisi esplorative e operative. Analisi di flusso deve essere autorizzato ad accedere a Data Lake Storage.

Azure Data Lake Storage output di analisi di flusso non è disponibile nelle aree Azure Cina 21Vianet e Azure Germania (T-Systems International).

## <a name="output-configuration"></a>Configurazione di output

La tabella seguente elenca i nomi di proprietà e le relative descrizioni per configurare l'output di Data Lake Storage Gen1.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output | Nome descrittivo usato nelle query per indirizzare l'output delle query a Data Lake Store. |
| Subscription | Sottoscrizione contenente l'account di Azure Data Lake Storage. |
| Nome account | Nome dell'account di Data Lake Store a cui si sta inviando l'output. Viene visualizzato un elenco a discesa degli account di Data Lake Store disponibili nella sottoscrizione. |
| Schema prefisso percorso | Percorso del file usato per scrivere i file nell'account di Data Lake Store specificato. È possibile specificare una o più istanze delle variabili {date} e {time}:<br /><ul><li>Esempio 1: folder1/logs/{date}/{time}</li><li>Esempio 2: folder1/logs/{date}</li></ul><br />Il timestamp della struttura di cartelle creata segue l'ora UTC e non l'ora locale.<br /><br />Se il modello di percorso del file non contiene una barra (/) finale, l'ultimo modello nel percorso del file viene considerato come prefisso del nome file. <br /><br />Vengono creati nuovi file nei casi seguenti:<ul><li>Modifica dello schema di output</li><li>Riavvio interno o esterno di un processo</li></ul> |
| Formato data | Facoltativa. Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG |
|Formato ora | Facoltativa. Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro.|
| Codifica | Se il formato è CSV o JSON, è necessario specificare un formato di codifica. Al momento UTF-8 è l'unico formato di codifica supportato.|
| Delimitatore | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale.|
| Format | Applicabile solo per la serializzazione JSON. **Separato da righe** specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Se si seleziona **Separato da righe**, JSON viene letto un oggetto alla volta. L'intero contenuto non è di per sé un oggetto JSON valido.  **Matrice** specifica che l'output viene formattato come matrice di oggetti JSON. Questa matrice viene chiusa solo quando il processo viene arrestato o Analisi di flusso di Azure passa all'intervallo di tempo successivo. In generale, è preferibile usare JSON separato da righe, perché non richiede alcuna particolare gestione durante la scrittura del file di output.|
| Modalità di autenticazione | È possibile autorizzare l'accesso all'account di Data Lake Storage usando l'[identità gestita](stream-analytics-managed-identities-adls.md) o il token utente. Quando si concede l'accesso, è possibile revocarlo modificando la password dell'account utente, eliminando l'output di Data Lake Storage per questo processo o eliminando il processo di Analisi di flusso. |

## <a name="partitioning"></a>Partizionamento

Per la chiave di partizione, usare i token {date} e {Time} nel modello di prefisso del percorso. Scegliere un formato di data, ad esempio AAAA/MM/GG, GG/MM/AAAA o MM-gg-aaaa. Usare HH per il formato di ora. Il numero di writer di output segue il partizionamento dell'input per le [query completamente eseguibili](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Dimensione del batch di output

Per la dimensione massima dei messaggi, vedere [limiti data Lake storage](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits). Per ottimizzare le dimensioni del batch, utilizzare fino a 4 MB per operazione di scrittura.

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con l'interfaccia della riga di comando di Azure](quick-create-azure-cli.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con un modello di Resource Manager](quick-create-azure-resource-manager.md)
* [Guida introduttiva: creare un processo di analisi di flusso usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Guida introduttiva: Creare un processo di Analisi di flusso di Azure con Visual Studio Code](stream-analytics-quick-create-vs.md)
* [Guida introduttiva: creare un processo di analisi di flusso di Azure in Visual Studio Code](quick-create-vs-code.md)