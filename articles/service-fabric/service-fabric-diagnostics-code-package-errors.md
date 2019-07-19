---
title: Diagnosticare gli errori comuni dei pacchetti di codice tramite Service Fabric | Microsoft Docs
description: Informazioni su come risolvere gli errori comuni dei pacchetti di codice con Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: grzuber
manager: gkhanna
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 320a55e8b14648b1d7e256855582ab31846a63cf
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249213"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Diagnosticare gli errori comuni dei pacchetti di codice tramite Service Fabric

Questo articolo descrive il modo in cui un pacchetto di codice termina in modo imprevisto. Fornisce informazioni dettagliate sulle possibili cause dei codici di errore comuni, oltre ai passaggi per la risoluzione dei problemi.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Quando un processo o un contenitore termina in modo imprevisto?

Quando Azure Service Fabric riceve una richiesta di avvio di un pacchetto di codice, inizia a preparare l'ambiente nel sistema locale in base alle opzioni impostate nei manifesti dell'app e del servizio. Queste preparazioni possono includere la riserva di endpoint o risorse di rete, la configurazione delle regole del firewall o la configurazione di vincoli di governance delle risorse. 

Dopo che l'ambiente è stato configurato correttamente, Service Fabric tenta di visualizzare il pacchetto di codice. Questo passaggio viene considerato riuscito se il runtime del sistema operativo o del contenitore segnala che il processo o il contenitore è stato attivato correttamente. Se l'attivazione ha esito negativo, verrà visualizzato un messaggio di integrità in SFX simile al seguente:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Dopo che il pacchetto di codice è stato attivato correttamente, Service Fabric inizia a monitorarne la durata. A questo punto, un processo o un contenitore può terminare in qualsiasi momento per diversi motivi. Ad esempio, potrebbe non essere stato in grado di inizializzare una DLL o il sistema operativo potrebbe avere esaurito lo spazio dell'heap del desktop. Se il pacchetto di codice è stato terminato, viene visualizzato il messaggio di integrità seguente in SFX:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Il codice di uscita in questo messaggio di integrità è l'unico indizio che il processo o il contenitore fornisce sul motivo per cui è terminato. Potrebbe essere generato da qualsiasi livello dello stack. Questo codice di uscita, ad esempio, potrebbe essere correlato a un errore del sistema operativo o a un problema .NET oppure potrebbe essere stato generato dal codice. Usare questo articolo come punto di partenza per la diagnosi dell'origine dei codici di uscita di terminazione e delle possibili soluzioni. Tenere tuttavia presente che si tratta di soluzioni generali per gli scenari comuni e che potrebbero non essere valide per l'errore che si sta osservando.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Come è possibile stabilire se Service Fabric terminato il pacchetto di codice?

Service Fabric potrebbe essere responsabile della terminazione del pacchetto di codice per diversi motivi. Ad esempio, potrebbe decidere di inserire il pacchetto di codice in un altro nodo a scopo di bilanciamento del carico. È possibile verificare che Service Fabric terminato il pacchetto di codice se viene visualizzato uno dei codici di uscita nella tabella seguente.

>[!NOTE]
> Se il processo o il contenitore termina con un codice di uscita diverso dai codici della tabella seguente, Service Fabric non è responsabile della terminazione.

Codice di uscita | DESCRIZIONE
--------- | -----------
7147 | Indica che Service Fabric arrestare correttamente il processo o il contenitore inviando un segnale CTRL + C.
7148 | Indica che Service Fabric ha terminato il processo o il contenitore. In alcuni casi, questo codice di errore indica che il processo o il contenitore non ha risposto in modo tempestivo dopo l'invio di un segnale CTRL + C ed era necessario terminarlo.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Altri codici di errore comuni e relative correzioni potenziali

Codice di uscita | Valore esadecimale | Breve descrizione | Causa radice | Potenziale correzione
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Questo errore talvolta indica che il computer ha esaurito lo spazio dell'heap del desktop. Questa causa è particolarmente probabile se sono presenti numerosi processi che appartengono all'applicazione in esecuzione nel nodo. | Se il programma non è stato compilato per rispondere ai segnali CTRL + C, è possibile abilitare l'impostazione **EnableActivateNoWindow** nel manifesto del cluster. L'abilitazione di questa impostazione indica che il pacchetto di codice verrà eseguito senza una finestra GUI e non riceverà segnali CTRL + C. Questa azione riduce anche la quantità di spazio dell'heap del desktop utilizzato da ogni processo. Se il pacchetto di codice deve ricevere segnali CTRL + C, è possibile aumentare le dimensioni dell'heap del desktop del nodo.
3762504530 | 0xe0434352 | N/D | Questo valore rappresenta il codice di errore per un'eccezione non gestita dal codice gestito, ovvero .NET. | Questo codice di uscita indica che l'applicazione ha generato un'eccezione che rimane non gestita e che ha terminato il processo. Come primo passaggio per determinare la causa dell'errore, eseguire il debug dei log e dei file di dump dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [diagnostica di altri scenari comuni](service-fabric-diagnostics-common-scenarios.md).
* Per una panoramica più dettagliata dei log di monitoraggio di Azure e delle relative offerte, vedere [Panoramica di monitoraggio di Azure](../operations-management-suite/operations-management-suite-overview.md).
* Altre informazioni sugli [avvisi](../log-analytics/log-analytics-alerts.md) dei log di monitoraggio di Azure per facilitare il rilevamento e la diagnostica.
* Acquisire familiarità con le funzionalità di [ricerca e query nei log](../log-analytics/log-analytics-log-searches.md) disponibili nell'ambito dei log di monitoraggio di Azure.
