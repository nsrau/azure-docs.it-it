---
title: Diagnosticare gli errori comuni del pacchetto di codice usando Service FabricDiagnose common code package errors by using Service Fabric
description: Informazioni su come risolvere gli errori comuni del pacchetto di codice con Azure Service Fabric
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 344fef70522240da2236a020c96308c472c9c545
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463107"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Diagnosticare gli errori comuni del pacchetto di codice usando Service FabricDiagnose common code package errors by using Service Fabric

In questo articolo viene descritto cosa significa per un pacchetto di codice terminare in modo imprevisto. Fornisce informazioni dettagliate sulle possibili cause dei codici di errore comuni, insieme alle procedure di risoluzione dei problemi.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Quando un processo o un contenitore termina in modo imprevisto?

Quando Azure Service Fabric riceve una richiesta per avviare un pacchetto di codice, inizia a preparare l'ambiente nel sistema locale in base alle opzioni impostate nei manifesti dell'app e del servizio. Questi preparativi possono includere la prenotazione di endpoint o risorse di rete, la configurazione delle regole del firewall o l'impostazione di vincoli di governance delle risorse. 

Dopo che l'ambiente è stato configurato correttamente, Service Fabric tenta di visualizzare il pacchetto di codice. Questo passaggio viene considerato riuscito se il sistema operativo o il runtime del contenitore segnala che il processo o il contenitore è stato attivato correttamente. Se l'attivazione non riesce, verrà visualizzato un messaggio di integrità in SFX analogo al seguente:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Dopo che il pacchetto di codice è stato attivato correttamente, Service Fabric inizia a monitorarne la durata. A questo punto, un processo o un contenitore può terminare in qualsiasi momento per diversi motivi. Ad esempio, potrebbe non essere riuscito a inizializzare una DLL o il sistema operativo potrebbe avere esaurito lo spazio dell'heap del desktop. Se il pacchetto di codice è terminato, in SFX dovrebbe essere visualizzato il seguente messaggio di integrità:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Il codice di uscita in questo messaggio di integrità è l'unico indizio fornito dal processo o dal contenitore sul motivo per cui è terminato. Potrebbe essere generato da qualsiasi livello dello stack. Ad esempio, questo codice di uscita potrebbe essere correlato a un errore del sistema operativo o un problema di .NET o potrebbe essere stato generato dal codice. Utilizzare questo articolo come punto di partenza per la diagnosi dell'origine dei codici di uscita di terminazione e delle possibili soluzioni. Tenere tuttavia presente che si tratta di soluzioni generali a scenari comuni e potrebbero non essere applicabili all'errore visualizzato.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Come è possibile stabilire se Service Fabric ha terminato il pacchetto di codice?

Service Fabric potrebbe essere responsabile della terminazione del pacchetto di codice per diversi motivi. Ad esempio, potrebbe decidere di inserire il pacchetto di codice in un altro nodo per il bilanciamento del carico. È possibile verificare che Service Fabric sia terminato il pacchetto di codice se viene visualizzato uno dei codici di uscita nella tabella seguente.

>[!NOTE]
> Se il processo o il contenitore termina con un codice di uscita diverso dai codici riportati nella tabella seguente, Service Fabric non è responsabile della terminazione.

Codice di uscita | Descrizione
--------- | -----------
7147 | Indica che Service Fabric arresta normalmente il processo o il contenitore inviandogli un segnale Ctrl .
7148 | Indica che Service Fabric ha terminato il processo o il contenitore. A volte, questo codice di errore indica che il processo o il contenitore non ha risposto in modo tempestivo dopo l'invio di un segnale Ctrl .C e doveva essere terminato.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Altri codici di errore comuni e le relative potenziali correzioni

Codice di uscita | Valore esadecimale | Breve descrizione | Causa radice | Potenziale correzione
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Questo errore talvolta indica che la macchina ha esaurito lo spazio dell'heap del desktop. Questa causa è particolarmente probabile se si dispone di numerosi processi che appartengono all'applicazione in esecuzione sul nodo. | Se il programma non è stato creato per rispondere ai segnali Ctrl , è possibile attivare l'impostazione **EnableActivateNoWindow** nel manifesto del cluster. Se si abilita questa impostazione, il pacchetto di codice verrà eseguito senza una finestra GUI e non riceverà segnali Ctrl . Questa azione riduce anche la quantità di spazio heap del desktop utilizzato da ogni processo. Se il pacchetto di codice deve ricevere i segnali Ctrl , è possibile aumentare le dimensioni dell'heap del desktop del nodo.
3762504530 | 0xe0434352 | N/D | Questo valore rappresenta il codice di errore per un'eccezione non gestita dal codice gestito, ovvero .NET. | Questo codice di uscita indica che l'applicazione ha generato un'eccezione che rimane non gestita e che ha terminato il processo. Come primo passaggio per determinare cosa ha generato questo errore, eseguire il debug dei log e dei file di dump dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sulla diagnosi di [altri scenari comuni](service-fabric-diagnostics-common-scenarios.md).
* Ottenere una panoramica più dettagliata dei log di Monitoraggio di Azure e di ciò che offrono leggendo [Panoramica](../operations-management-suite/operations-management-suite-overview.md)di Monitoraggio di Azure.Get a more detailed overview of Azure Monitor logs and what they offer by reading Azure Monitor overview .
* Altre informazioni sugli [avvisi](../log-analytics/log-analytics-alerts.md) dei log di Monitoraggio di Azure per gli aiuti nel rilevamento e nella diagnostica.
* Acquisire familiarità con le funzionalità di ricerca dei log e di query offerte come parte dei log di Monitoraggio di Azure.Get familiar with the log search and [querying](../log-analytics/log-analytics-log-searches.md) features offered as part of Azure Monitor logs.
