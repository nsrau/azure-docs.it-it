---
title: Output di analisi di sinapsi di Azure da analisi di flusso di Azure
description: Questo articolo descrive Azure sinapsi Analytics come output per analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 5d281bf33542354e2904805deed2f1deaf2725a2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875739"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics-preview"></a>Output di analisi delle sinapsi di Azure da analisi di flusso di Azure (anteprima)

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (in precedenza SQL Data Warehouse) è un servizio di analisi senza limiti che riunisce funzionalità aziendali di data warehousing e analisi di Big Data. 

I processi di Analisi di flusso di Azure possono inviare l'output a una tabella del pool SQL in Azure Synapse Analytics e possono elaborare velocità effettive fino a 200 MB/sec. In questo modo, vengono soddisfatte le più elevate esigenze di analisi in tempo reale e di elaborazione dei dati del percorso critico tipiche di carichi di lavoro quali la creazione di report e dashboard.  

La tabella del pool SQL deve esistere prima che sia possibile aggiungerla come output al processo di Analisi di flusso. Lo schema della tabella deve corrispondere ai campi e ai relativi tipi nell'output del processo. 

Per usare Azure Synapse come output, è necessario assicurarsi di aver configurato l'account di archiviazione. Per configurare l'account di archiviazione, passare alle impostazioni dell'account di archiviazione. Sono consentiti solo i tipi di account di archiviazione che supportano le tabelle: Utilizzo generico v2 e utilizzo generico v1. Abilitare solo il livello Standard. Il livello Premium non è supportato.

## <a name="output-configuration"></a>Configurazione di output

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output di Azure Synapse Analytics.

|Nome proprietà|Descrizione|
|-|-|
|Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questo database. |
|Database |Nome del pool SQL in cui si sta inviando l'output. |
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
* [Guida introduttiva: creare un processo di analisi di flusso di Azure in Visual Studio Code](quick-create-vs-code.md)