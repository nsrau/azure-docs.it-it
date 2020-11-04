---
title: Output di analisi di sinapsi di Azure da analisi di flusso di Azure
description: Questo articolo descrive Azure sinapsi Analytics come output per analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 0b3bec9c4d4476b95279e35953ff89177f4488d4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305840"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Output di analisi di sinapsi di Azure da analisi di flusso di Azure

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (in precedenza SQL Data Warehouse) è un servizio di analisi senza limiti che riunisce funzionalità aziendali di data warehousing e analisi di Big Data. 

I processi di analisi di flusso di Azure possono restituire una tabella del pool SQL dedicata in Azure sinapsi Analytics e possono elaborare velocità effettiva fino a 200 MB/sec. Questo supporta l'analisi in tempo reale e le esigenze di elaborazione dei dati con percorso critico più esigenti per i carichi di lavoro, come la creazione di report e il dashboard.  

La tabella del pool SQL dedicata deve esistere prima che sia possibile aggiungerla come output al processo di analisi di flusso. Lo schema della tabella deve corrispondere ai campi e ai relativi tipi nell'output del processo. 

Per usare Azure Synapse come output, è necessario assicurarsi di aver configurato l'account di archiviazione. Per configurare l'account di archiviazione, passare alle impostazioni dell'account di archiviazione. Sono consentiti solo i tipi di account di archiviazione che supportano le tabelle: Utilizzo generico v2 e utilizzo generico v1. Abilitare solo il livello Standard. Il livello Premium non è supportato.

## <a name="output-configuration"></a>Configurazione di output

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output di Azure Synapse Analytics.

|Nome proprietà|Descrizione|
|-|-|
|Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo database. |
|Database |nome del pool SQL dedicato a cui si sta inviando l'output. |
|Nome server |Nome del server di Azure Synapse.  |
|Username |Nome utente con accesso in scrittura al database. Analisi di flusso supporta solo l'autenticazione SQL. |
|Password |Password per la connessione al database. |
|Tabella  | Nome della tabella in cui viene scritto l'output. Il nome della tabella fa distinzione tra maiuscole e minuscole. Lo schema di questa tabella deve corrispondere esattamente al numero di campi e ai relativi tipi generati dall'output del processo.|

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con l'interfaccia della riga di comando di Azure](quick-create-azure-cli.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con un modello di Resource Manager](quick-create-azure-resource-manager.md)
* [Guida introduttiva: creare un processo di analisi di flusso usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Guida introduttiva: Creare un processo di Analisi di flusso di Azure con Visual Studio Code](stream-analytics-quick-create-vs.md)
* [Guida introduttiva: creare un processo di analisi di flusso di Azure in Visual Studio Code](quick-create-visual-studio-code.md)