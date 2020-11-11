---
title: Output di funzioni di Azure da analisi di flusso di Azure
description: Questo articolo descrive le funzioni di Azure come output per analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: ffaf66cbb5d9fa0b294f5749a1923684f16c2979
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488090"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>Output di funzioni di Azure da analisi di flusso di Azure

Funzioni di Azure è un servizio di calcolo senza server che è possibile usare per eseguire codice su richiesta senza dover gestire l'infrastruttura o effettuare il provisioning in modo esplicito. Consente di implementare il codice attivato da eventi generati nei servizi di Azure o di un partner. La possibilità di rispondere ai trigger rende Funzioni di Azure l'output naturale per Analisi di flusso di Azure. Questo adattatore di output consente agli utenti di collegare Analisi di flusso a Funzioni di Azure ed eseguire uno script o una porzione di codice in risposta a diversi eventi.

L'output di funzioni di Azure da analisi di flusso non è disponibile nelle aree Azure Cina 21Vianet e Azure Germania (T-Systems International). Non è inoltre supportata la connessione a funzioni di Azure all'interno di una rete virtuale (VNet) da un processo di analisi di flusso in esecuzione in un cluster multi-tenant.

Analisi di flusso di Azure richiama Funzioni di Azure tramite trigger HTTP. L'adattatore di output di Funzioni di Azure è disponibile con le proprietà configurabili seguenti:

| Nome proprietà | Descrizione |
| --- | --- |
| App per le funzioni |Nome dell'app Funzioni di Azure. |
| Funzione |Nome della funzione nell'app Funzioni di Azure. |
| Chiave |Per usare una funzione di Azure da un'altra sottoscrizione, è necessario fornire la chiave per accedere alla funzione. |
| Dimensioni massime batch |Proprietà che consente di impostare le dimensioni massime per ogni batch di output inviato a Funzioni di Azure. L'unità di input è espressa in byte. Per impostazione predefinita, questo valore è 262.144 byte (256 KB). |
| Numero massimo di batch  |Proprietà che consente di specificare il numero massimo di eventi in ogni batch che vengono inviati a Funzioni di Azure. Il valore predefinito è 100. |

Analisi di flusso di Azure prevede lo stato HTTP 200 dall'app Funzioni per i batch elaborati correttamente.

Quando Analisi di flusso di Azure riceve l'eccezione 413 ("Entità richiesta HTTP troppo grande") da una funzione di Azure, riduce la dimensione dei batch che invia a Funzioni di Azure. Usare questa eccezione nel codice della funzione di Azure per fare in modo che Analisi di flusso di Azure non invii batch troppo grandi. Assicurarsi anche che i valori relativi alle dimensioni e al numero massimo di batch usati nella funzione siano conformi ai valori inseriti nel portale di Analisi di flusso.

> [!NOTE]
> Durante il test della connessione, Analisi di flusso invia un batch vuoto a Funzioni di Azure per verificare se la connessione tra i due funziona. Assicurarsi che l'app Funzioni gestisca le richieste batch vuote per consentire l'esito positivo del test della connessione.

Quando non avviene alcun evento in un intervallo di tempo, non viene generato alcun output. Di conseguenza, non viene chiamata la funzione **computeResult**. Questo comportamento è coerente con le funzioni di aggregazione finestra predefinite.

## <a name="partitioning"></a>Partizionamento

La chiave di partizione è basata sulla clausola PARTITION BY nella query. Il numero di writer di output segue il partizionamento dell'input per le [query completamente parallele](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Dimensione del batch di output

La dimensione del batch predefinita è 262.144 byte (256 KB). Il numero di eventi predefinito per ogni batch è 100. Le dimensioni batch sono configurabili e possono essere aumentate o ridotte nelle opzioni di output di Analisi di flusso.

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con l'interfaccia della riga di comando di Azure](quick-create-azure-cli.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con un modello di Resource Manager](quick-create-azure-resource-manager.md)
* [Guida introduttiva: creare un processo di analisi di flusso usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Guida introduttiva: Creare un processo di Analisi di flusso di Azure con Visual Studio Code](stream-analytics-quick-create-vs.md)
* [Guida introduttiva: creare un processo di analisi di flusso di Azure in Visual Studio Code](quick-create-visual-studio-code.md)