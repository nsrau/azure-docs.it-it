---
title: Azure Service Fabric diagnosticare gli errori di pacchetto di codice | Microsoft Docs
description: Informazioni su come risolvere i problemi più comuni errori di pacchetto di codice con Azure Service Fabric
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
ms.openlocfilehash: 235952388d2c044cc141b3020c67944c4250ea3d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276951"
---
# <a name="diagnose-common-code-package-errors-with-service-fabric"></a>Diagnosticare errori di pacchetto di codice comuni con Service Fabric

Questo articolo illustra che cosa significa per causare l'interruzione imprevista di un pacchetto di codice e fornisce informazioni approfondite possibili cause di codici di errore comuni, nonché procedure di risoluzione dei problemi che possono potenzialmente ridurre il problema.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Quando viene processo o contenitore terminata in modo imprevisto?

Quando Service Fabric riceve una richiesta di avvio di un pacchetto di codice, inizia a preparare l'ambiente nel sistema locale in base alle opzioni di configurazione impostate in App e dei manifesti del servizio. Queste operazioni di preparazione può includere riservando gli endpoint di rete o le risorse, la configurazione di regole del firewall o configurare i limiti di governance delle risorse. Una volta che l'ambiente è stato configurato correttamente, Service Fabric tenta di portare il pacchetto di codice. Questo passaggio viene considerato riuscito se il runtime del sistema operativo o di un contenitore viene segnalato che il processo o il contenitore sia stato attivato correttamente. Se l'attivazione ha esito negativo, si verrà visualizzato un messaggio di integrità in SFX con la dicitura

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Dopo aver inserito correttamente il pacchetto di codice, Service Fabric inizia a monitorare la sua durata. A questo punto, un processo o contenitore può terminare in qualsiasi momento per diversi motivi. Non è stato possibile avere è riuscito a inizializzare una DLL, il sistema operativo potrebbe aver esaurito lo spazio dell'heap del desktop e così via. Se è stato terminato il pacchetto di codice, si verrà visualizzato un messaggio di integrità in SFX con la dicitura

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Il codice di uscita fornito in questo stato di messaggio è l'unico indizio fornito dal processo/contenitore per quanto riguarda il motivo per cui è stato terminato e potrebbe essere stata generata da qualsiasi livello dello stack. È difficile da comprendere se il codice di uscita è correlato, ad esempio, a un errore del sistema operativo, un problema di .NET, o è stato generato dal codice. Di conseguenza, questo articolo è utilizzabile come punto di partenza per diagnosticare l'origine dei codici di uscita di terminazione e possibili soluzioni, tenendo presente che queste sono soluzioni generali per scenari comuni e potrebbero non essere applicabili all'errore è visualizzato.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Come è possibile stabilire se Service Fabric terminato il pacchetto di codice?

Service Fabric può essere responsabile della chiusura del pacchetto di codice per svariati motivi. Ad esempio, può decidere di salvare il pacchetto di codice in un altro nodo per il bilanciamento del carico a scopo. È possibile indicare se il pacchetto di codice è stato terminato da Service Fabric se viene visualizzato uno dei codici di uscita nella tabella seguente:

>[!NOTE]
> Se il processo/contenitore termina con un codice di uscita diverso da quelli nella tabella seguente, Service Fabric non è responsabile per terminarla.

Codice di uscita | Descrizione
--------- | -----------
7147 | Questi codici di errore indicano che Service Fabric si arresta normalmente il processo/contenitore inviando un segnale Ctrl + C.
7148 | Questi codici di errore indicano che Service Fabric terminato il processo/contenitore. In alcuni casi, questo codice di errore può indicare che il processo/contenitore non ha risposto in modo tempestivo dopo l'invio di un segnale Ctrl + C, in modo che doveva essere terminato.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Altri codici di errore comuni e le potenziali correzioni

Codice di uscita | Valore hex | Descrizione breve | Causa radice | Correzione di potenziali
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Questo errore potenzialmente può significare che il computer ha esaurito lo spazio dell'heap del desktop. Questa causa è probabilmente in particolare se si dispone di un numero elevato di processi che appartengono all'applicazione in esecuzione nel nodo. | Se il programma non è stato compilato per rispondere ai segnali di Ctrl + C, è possibile abilitare l'impostazione "EnableActivateNoWindow" nel manifesto del Cluster. Abilitazione di questa impostazione significa il pacchetto di codice verrebbe eseguito senza visualizzare una finestra dell'interfaccia utente grafica e riceverebbe non segnali Ctrl + C, ma potrebbe ridurre la quantità di spazio dell'heap del desktop di che ogni processo consuma. Se il pacchetto di codice deve ricevere i segnali di Ctrl + C, è possibile aumentare le dimensioni dell'heap del desktop del nodo.
3762504530 | 0xe0434352 | N/D | Questo valore è il codice di errore per un'eccezione non gestita dal codice gestito (vale a dire, .NET). | Se viene visualizzato il codice di uscita, implica che l'applicazione ha generato un'eccezione rimane non gestita e ha terminato il processo. I log dell'applicazione e i dump di debug deve essere il primo passaggio per determinare la causa dell'errore.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [la diagnosi di altri scenari comuni](service-fabric-diagnostics-common-scenarios.md)
* Ottenere una panoramica più dettagliata dei log di monitoraggio di Azure e sui vantaggi offerti, leggere [What ' s i log di monitoraggio di Azure?](../operations-management-suite/operations-management-suite-overview.md)
* Altre informazioni sui log di monitoraggio di Azure [avvisi](../log-analytics/log-analytics-alerts.md) per semplificare il rilevamento e la diagnostica.
* Acquisire familiarità con le [ricerca log e l'esecuzione di query](../log-analytics/log-analytics-log-searches.md) funzionalità fornite come parte dei log di monitoraggio di Azure
