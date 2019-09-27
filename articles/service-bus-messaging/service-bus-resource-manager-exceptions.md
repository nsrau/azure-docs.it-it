---
title: Eccezioni Gestione risorse del bus di servizio di Azure | Microsoft Docs
description: Elenco di eccezioni del bus di servizio emerse da Azure Resource Manager e le azioni consigliate.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: darosa
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2019
ms.author: aschhab
ms.openlocfilehash: e666503b9e8888e7d61445639fe0f3adeeffe55f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329305"
---
# <a name="service-bus-resource-manager-exceptions"></a>Eccezioni Gestione risorse del bus di servizio

Questo articolo elenca le eccezioni generate durante l'interazione con il bus di servizio di Azure usando modelli di Azure Resource Manager tramite o chiamate dirette.

> [!IMPORTANT]
> Questo documento viene aggiornato di frequente. Verificare la disponibilità di aggiornamenti.

Di seguito sono riportate le varie eccezioni/errori che vengono rilevati tramite la Azure Resource Manager.

## <a name="error-bad-request"></a>Errore: Richiesta non valida

"Richiesta non valida" indica che la richiesta ricevuta dalla Gestione risorse non è riuscita.

| Codice errore | Codice di errore | Messaggio di errore | Descrizione | Indicazione |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Richiesta non valida | 40000 | Sottocodice = 40000. Impossibile impostare la proprietà *' Property Name '* durante la creazione di una coda perché lo spazio dei nomi *' namespace name '* utilizza il livello ' Basic '. Questa operazione è supportata solo nel livello ' standard ' o ' Premium '. | Nel livello Basic del bus di servizio di Azure non è possibile impostare o aggiornare le proprietà seguenti: <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>requiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Argomenti </li> </ul> | Per sfruttare questa funzionalità, provare a eseguire l'aggiornamento dal livello Basic al livello standard o Premium. |
| Richiesta non valida | 40000 | Sottocodice = 40000. Impossibile modificare il valore della proprietà' requiresDuplicateDetection ' di una coda esistente. | Il rilevamento dei duplicati deve essere abilitato o disabilitato al momento della creazione dell'entità. Una volta creato, il parametro di configurazione del rilevamento duplicato non può essere modificato. | Per abilitare il rilevamento dei duplicati in una coda o una sottoscrizione creata in precedenza, è possibile creare una nuova coda con rilevamento dei duplicati e quindi procedere dalla coda originale alla nuova coda. |
| Richiesta non valida | 40000 | Sottocodice = 40000. Il valore 16384 specificato non è valido. La proprietà' MaxSizeInMegabytes ' deve essere uno dei valori seguenti: 1024; 2048; 3072; 4096; 5120. | Il valore MaxSizeInMegabytes non è valido. | Verificare che MaxSizeInMegabytes sia uno dei seguenti: 1024, 2048, 3072, 4096, 5120. |
| Richiesta non valida | 40000 | Sottocodice = 40000. Non è possibile modificare il partizionamento per la coda. | Non è possibile modificare il partizionamento per l'entità. | Creare una nuova entità e abilitare le partizioni. | 
| Richiesta non valida | nessuno | Lo spazio dei nomi *' namespace name '* non esiste. | Lo spazio dei nomi non esiste nella sottoscrizione di Azure. | Per correggere l'errore, provare il seguente <ul> <li> Assicurarsi che la sottoscrizione di Azure sia corretta. </li> <li> Verificare che lo spazio dei nomi esista. </li> <li> Verificare che il nome dello spazio dei nomi sia corretto (nessun errore di ortografia o stringa null). </li> </ul> | 
| Richiesta non valida | 40400 | Sottocodice = 40400. L'entità di destinazione dell'invio automatico non esiste. | La destinazione per l'entità di destinazione di autoinoltring non esiste. | L'entità di destinazione (coda o argomento) deve esistere prima della creazione dell'origine. Riprovare dopo aver creato l'entità di destinazione. |


## <a name="error-code-429"></a>Codice errore: 429

| Codice errore | Codice di errore | Messaggio di errore | Descrizione | Indicazione |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Sottocodice = 50004. La richiesta è stata terminata perché *è in* corso la limitazione dello spazio dei nomi. | Questa condizione di errore viene raggiunta quando il numero di richieste in ingresso supera il limite della risorsa. | Attendere alcuni secondi e riprovare. <br/> <br/> Altre informazioni sulle [quote](service-bus-quotas.md) e sui [limiti delle richieste di Azure Resource Manager](../azure-resource-manager/resource-manager-request-limits.md)|
| 429 | 40901 | Sottocodice = 40901. È in corso un'altra operazione in conflitto. | È in corso un'altra operazione in conflitto sulla stessa risorsa/entità | Attendere il completamento dell'operazione corrente in corso prima di riprovare. |
| 429 | 40900 | Sottocodice = 40900. Conflitto. Si sta richiedendo un'operazione non consentita nello stato corrente della risorsa. | Questa condizione può essere raggiunta quando vengono effettuate più richieste per eseguire le operazioni sulla stessa entità (coda, argomento, sottoscrizione o regola) allo stesso tempo | Attendere alcuni secondi e riprovare |
| 429 | nessuno | Si è verificato un conflitto di risorse. È possibile che sia in corso un'altra operazione in conflitto. Se si tenta di eseguire un'operazione non riuscita, la pulitura in background è ancora in sospeso. Riprovare. | Questa condizione può essere raggiunta quando si verifica un'operazione in sospeso sulla stessa entità. | Attendere il completamento dell'operazione precedente prima di riprovare. |

