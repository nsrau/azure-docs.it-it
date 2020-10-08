---
title: Power BI output di analisi di flusso di Azure
description: Questo articolo descrive come restituire i dati da analisi di flusso di Azure a Power BI.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: aee5cb077604e5fc95647eca0e6570ea3582a785
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822995"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>Power BI output di analisi di flusso di Azure

È possibile usare [Power BI](https://powerbi.microsoft.com/) come output per un processo di Analisi di flusso per offrire un'esperienza di visualizzazione avanzata dei risultati di analisi. È possibile usare questa funzionalità per i dashboard operativi, la generazione di report e la creazione di report basati sulle metriche.

L'output di Power BI da Stream Analytics non è attualmente disponibile nelle aree di Azure Cina 21Vianet e Germania (T-Systems International).

## <a name="output-configuration"></a>Configurazione di output

La tabella seguente elenca i nomi di proprietà e le relative descrizioni per configurare l'output di Power BI.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Consente di specificare un nome descrittivo usato nelle query per indirizzare l'output delle query a questo output di Power BI. |
| Area di lavoro del gruppo |Per abilitare la condivisione dei dati con altri utenti Power BI, è possibile selezionare i gruppi all'interno dell'account Power BI o scegliere **Area di lavoro personale** se non si vuole eseguire la scrittura in un gruppo. L'aggiornamento di un gruppo esistente richiede il rinnovo dell'autenticazione di Power BI. |
| Nome del set di dati |Consente di specificare un nome per il set di dati che dovrà essere usato dall'output di Power BI. |
| Nome tabella |Immettere un nome per la tabella nel set di dati dell'output di Power BI. L'output di Power BI da processi di Analisi di flusso può avere al momento solo una tabella in un set di dati. |
| Autorizzare connessione | Per configurare le impostazioni di output, è necessario eseguire l'autorizzazione con Power BI. Quando si concede l'accesso dell'output al dashboard di Power BI, è possibile revocarlo modificando la password dell'account utente, eliminando l'output del processo o eliminando il processo di Analisi di flusso. | 

Per una procedura dettagliata della configurazione di un output di Power BI e del dashboard, vedere l'esercitazione [Analisi di flusso di Azure e Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Non creare in modo esplicito il set di dati e la tabella nel dashboard di Power BI. Il set di dati e la tabella vengono compilati automaticamente quando il processo viene avviato e inizia a generare output in Power BI. Se la query di processo non genera alcun risultato, il set di dati e la tabella non vengono creati. Se Power BI ha già un set di dati e una tabella con lo stesso nome fornito dall'utente nel processo di Analisi di flusso, i dati esistenti vengono sovrascritti.
>

### <a name="create-a-schema"></a>Creare uno schema

Analisi di flusso di Azure crea un set di dati e uno schema di tabella di Power BI per conto dell'utente, se non esistono già. In tutti gli altri casi, la tabella viene aggiornata con nuovi valori. Attualmente, all'interno di un set di dati può esistere una sola tabella. 

Power BI usa i criteri di conservazione FIFO. I dati verranno raccolti in una tabella fino al raggiungimento di 200.000 righe.

> [!NOTE]
> Non è consigliabile usare più output per scrivere nello stesso set di dati, in quanto possono causare diversi problemi. Ogni output tenta di creare il set di dati Power BI in modo indipendente, che può generare più set di dati con lo stesso nome. Inoltre, se gli output non hanno schemi coerenti, il set di dati modifica lo schema in ogni scrittura, causando un numero eccessivo di richieste di modifica dello schema. Anche se si evitano questi problemi, più output saranno meno performanti rispetto a un singolo output Unito.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Convertire tipi di dati da Analisi di flusso di Azure a Power BI

Analisi di flusso di Azure consente di aggiornare il modello di dati in modo dinamico in fase di esecuzione se viene modificato lo schema di output. Vengono rilevate tutte le modifiche al nome e al tipo di colonna e l'aggiunta o la rimozione di colonne.

Questa tabella contiene le conversioni dei tipi di dati dai [tipi di dati di Analisi di flusso](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) ai [tipi Entity Data Model (EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) di Power BI, se non esistono né un set di dati né una tabella di Power BI.

Dall'analisi di flusso | A Power BI
-----|-----
bigint | Int64
nvarchar(max) | string
Datetime | Datetime
float | Double
Matrice di record | Tipo String, valore di tipo Constant "IRecord" o "IArray"

### <a name="update-the-schema"></a>Aggiornare lo schema

L'analisi di flusso deduce lo schema del modello di dati in base al primo set di eventi dell'output. In un secondo momento, se necessario, lo schema del modello di dati viene aggiornato per gestire gli eventi in ingresso che potrebbero non rientrare nello schema originale.

Non usare la query `SELECT *` per evitare l'aggiornamento dinamico dello schema nelle righe. Oltre a implicazioni potenziali sulle prestazioni, potrebbe anche verificarsi un problema di incertezza rispetto al tempo necessario per ottenere i risultati. Selezionare i campi esatti che devono essere visualizzati nel dashboard di Power BI. È anche necessario che i valori dei dati siano conformi al tipo di dati scelto.

Precedente/Corrente | Int64 | string | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | string | string | Double
Double | Double | string | string | Double
string | string | string | string | string 
Datetime | string | string |  Datetime | string

## <a name="output-batch-size"></a>Dimensione del batch di output

Per le dimensioni del batch di output, vedere [Power Bi limiti dell'API REST](https://msdn.microsoft.com/library/dn950053.aspx).

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con l'interfaccia della riga di comando di Azure](quick-create-azure-cli.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con un modello di Resource Manager](quick-create-azure-resource-manager.md)
* [Guida introduttiva: creare un processo di analisi di flusso usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Guida introduttiva: Creare un processo di Analisi di flusso di Azure con Visual Studio Code](stream-analytics-quick-create-vs.md)
* [Guida introduttiva: creare un processo di analisi di flusso di Azure in Visual Studio Code](quick-create-visual-studio-code.md)
