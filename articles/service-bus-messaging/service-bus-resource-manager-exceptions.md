---
title: Eccezioni di Azure Service Bus Resource Manager Documenti Microsoft
description: Elenco delle eccezioni del bus di servizio rilevate da Azure Resource Manager e dalle azioni suggerite.
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
ms.openlocfilehash: 0f328651ac4422226071d2de12e9cbc787ef64be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978267"
---
# <a name="service-bus-resource-manager-exceptions"></a>Eccezioni di Gestione risorse bus di servizioService Bus Resource Manager exceptions

Questo articolo elenca le eccezioni generate durante l'interazione con il bus di servizio di Azure usando Azure Resource Manager, tramite modelli o chiamate dirette.

> [!IMPORTANT]
> Questo documento viene aggiornato di frequente. Si prega di controllare di nuovo per gli aggiornamenti.

Di seguito sono riportate le varie eccezioni/errori che vengono rilevate tramite Azure Resource Manager.Below are the various exceptions/errors that are surfaced through the Azure Resource Manager.

## <a name="error-bad-request"></a>Errore: richiesta non valida

"Richiesta non valida" implica che la richiesta ricevuta da Gestione risorse non è riuscita convalida.

| Codice di errore | Sottocodice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Bad Request | 40000 | Sottocodice 40000. Impossibile impostare la proprietà *'nome proprietà'* durante la creazione di una coda perché lo spazio dei nomi *'nome dello spazio dei nomi'* utilizza il livello 'Basic'. Questa operazione è supportata solo nel livello 'Standard' o 'Premium'. | Nel livello di base del bus di servizio di Azure non è possibile impostare o aggiornare le proprietà seguenti: <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Argomenti </li> </ul> | Prendi in considerazione l'aggiornamento dal livello Basic a Standard o Premium per usare questa funzionalità. |
| Bad Request | 40000 | Sottocodice 40000. Il valore della proprietà 'requiresDuplicateDetection' di un oggetto Queue(o Topic) esistente non può essere modificato. | Il rilevamento duplicati deve essere abilitato/disabilitato al momento della creazione dell'entità. Il parametro di configurazione del rilevamento duplicati non può essere modificato dopo la creazione. | Per abilitare il rilevamento duplicati in una coda/argomento creato in precedenza, è possibile creare una nuova coda/argomento con il rilevamento duplicati e quindi inoltrare dalla coda originale alla nuova coda/argomento. |
| Bad Request | 40000 | Sottocodice 40000. Il valore specificato 16384 non è valido. La proprietà 'MaxSizeInMegabytes' deve essere uno dei valori seguenti: 1024;2048;3072;4096;5120. | Il valore MaxSizeInMegabytes non è valido. | Assicurarsi che MaxSizeInMegabytes sia uno dei seguenti: 1024, 2048, 3072, 4096, 5120. |
| Bad Request | 40000 | Sottocodice 40000. Impossibile modificare il partizionamento per Coda/Argomento. | Il partizionamento non può essere modificato per l'entità. | Creare una nuova entità (coda o argomento) e abilitare le partizioni. | 
| Bad Request | none | Lo spazio dei nomi *'nome dello spazio dei nomi'* non esiste. | Lo spazio dei nomi non esiste nella sottoscrizione di Azure.The namespace does not exist within your Azure subscription. | Per risolvere questo errore, provare <ul> <li> Verificare che la sottoscrizione di Azure sia corretta. </li> <li> Verificare che lo spazio dei nomi esista. </li> <li> Verificare che il nome dello spazio dei nomi sia corretto (nessun errore di ortografia o stringhe null). </li> </ul> | 
| Bad Request | 40400 | Sottocodice 40400. L'entità di destinazione dell'inoltro automatico non esiste. | La destinazione per l'entità di destinazione di inoltro automatico non esiste. | L'entità di destinazione (coda o argomento) deve esistere prima della creazione dell'origine. Riprovare dopo aver creato l'entità di destinazione. |
| Bad Request | 40000 | Sottocodice 40000. Il tempo di blocco in dotazione supera il massimo consentito di 5 minuti. | Il tempo per cui un messaggio può essere bloccato deve essere compreso tra 1 minuto (minimo) e 5 minuti (massimo). | Assicurarsi che il tempo di blocco fornito sia compreso tra 1 min e 5 min. |
| Bad Request | 40000 | Sottocodice 40000. Le proprietà DelayedPersistence e RequiresDuplicateDetection non possono essere abilitate insieme. | Le entità con rilevamento duplicati abilitato devono essere persistenti, pertanto la persistenza non può essere ritardata. | Ulteriori informazioni sul [rilevamento duplicati](duplicate-detection.md) |
| Bad Request | 40000 | Sottocodice 40000. Il valore della proprietà RequiresSession di una queue esistente non può essere modificato. | Il supporto per le sessioni deve essere abilitato al momento della creazione dell'entità. Una volta create, non è possibile abilitare/disabilitare le sessioni in un'entità esistente (coda o sottoscrizione) | Eliminare e ricreare una nuova coda (o sottoscrizione) con la proprietà "RequiresSession" abilitata. |
| Bad Request | 40000 | Sottocodice 40000. 'URI_PATH' contiene caratteri non consentiti dal bus di servizio. I segmenti di entità possono contenere solo lettere, numeri, punti (.), trattini (-) e caratteri di sottolineatura(_). | I segmenti di entità possono contenere solo lettere, numeri, punti (.), trattini (-) e caratteri di sottolineatura(_). Eventuali altri caratteri causano l'esito negativo della richiesta. | Assicurarsi che non siano presenti caratteri non validi nel percorso URI. |


## <a name="error-code-429"></a>Codice di errore: 429

Proprio come in HTTP, "Codice di errore 429" indica "troppe richieste". Implica che la risorsa specifica (spazio dei nomi) viene limitata a causa di un numero eccessivo di richieste (o a causa di operazioni in conflitto) su tale risorsa.

| Codice di errore | Sottocodice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Sottocodice: 50004. La richiesta è stata terminata perché lo spazio dei nomi *dello spazio dei nomi* è limitato. | Questa condizione di errore viene raggiunta quando il numero di richieste in ingresso supera il limite della risorsa. | Attendere alcuni secondi e riprovare. <br/> <br/> Altre informazioni sulle quote e sui limiti delle richieste di [Azure Resource ManagerLearn](../azure-resource-manager/management/request-limits-and-throttling.md) more about the [quotas](service-bus-quotas.md) and Azure Resource Manager request limits|
| 429 | 40901 | Sottocodice 40901. È in corso un'altra operazione in conflitto. | Un'altra operazione in conflitto è in corso sulla stessa risorsa/entità | Attendere il completamento dell'operazione in corso corrente prima di riprovare. |
| 429 | 40900 | Sottocodice 40900. Conflitto. Si sta richiedendo un'operazione non consentita nello stato corrente della risorsa. | Questa condizione può essere raggiunta quando vengono effettuate più richieste per eseguire le operazioni sulla stessa entità (coda, argomento, sottoscrizione o regola) contemporaneamente. | Attendere alcuni secondi e riprovare |
| 429 | 40901 | Richiesta sull'entità 'nome entità' in conflitto con un'altra richiestaRequest on entity *'entity name'* conflicted with another request | Un'altra operazione in conflitto è in corso sulla stessa risorsa/entità | Attendere il completamento dell'operazione precedente prima di riprovare |
| 429 | 40901 | È in corso un'altra richiesta di aggiornamento per l'entità *'nome entità'.* | Un'altra operazione in conflitto è in corso sulla stessa risorsa/entità | Attendere il completamento dell'operazione precedente prima di riprovare |
| 429 | none | Si è verificato un conflitto di risorse. Potrebbe essere in corso un'altra operazione in conflitto. Se si tratta di un nuovo tentativo per un'operazione non riuscita, la pulizia in background è ancora in sospeso. Riprovare più tardi. | Questa condizione può essere raggiunta quando è presente un'operazione in sospeso sulla stessa entità. | Attendere il completamento dell'operazione precedente prima di riprovare. |


## <a name="error-code-not-found"></a>Codice di errore: Non trovato

Questa classe di errori indica che la risorsa non è stata trovata.

| Codice di errore | Sottocodice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Non trovato | none | Impossibile trovare *l'entità 'nome entità'.* | Entità rispetto alla quale non è stata trovata l'operazione. | Verificare se l'entità esiste e riprovare. |
| Non trovato | none | Non trovato. L'operazione non esiste. | L'operazione che si sta tentando di eseguire non esiste. | Controllare l'operazione e riprovare. |
| Non trovato | none | La richiesta in ingresso non viene riconosciuta come richiesta put dei criteri dello spazio dei nomi. | Il corpo della richiesta in ingresso è null e pertanto non può essere eseguito come richiesta put. | Controllare il corpo della richiesta per assicurarsi che non sia null. | 
| Non trovato | none | Impossibile trovare l'entità di messaggistica *'nome entità'.* | Impossibile trovare l'entità con cui si sta tentando di eseguire l'operazione. | Verificare se l'entità esiste e riprovare. |

## <a name="error-code-internal-server-error"></a>Codice di errore: Errore interno del server

Questa classe di errori indica che si è verificato un errore interno del server

| Codice di errore | Sottocodice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Internal Server Error | 50000 | Sottocodice 50000. Internal Server Error| Può accadere per vari motivi. Alcuni dei sintomi sono - <ul> <li> La richiesta o il corpo del client è danneggiato e causa un errore. </li> <li> Timeout della richiesta client a causa di problemi di elaborazione nel servizio. </li> </ul> | Per risolvere il problema <ul> <li> Assicurarsi che i parametri delle richieste non siano null o in formato non corretto. </li> <li> ripetere la richiesta. </li> </ul> |

## <a name="error-code-unauthorized"></a>Codice di errore: Non autorizzato

Questa classe di errori indica l'assenza di autorizzazione per eseguire il comando.

| Codice di errore | Sottocodice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Non autorizzata | none | Operazione non valida nello spazio dei nomi secondario. Lo spazio dei nomi secondario è di sola lettura. | L'operazione è stata eseguita sullo spazio dei nomi secondario, che viene configurato come spazio dei nomi readonly. | Ripetere il comando per eseguire lo spazio dei nomi primario. Ulteriori informazioni sullo [spazio dei nomi secondarioLearn](service-bus-geo-dr.md) more about secondary namespace |
| Non autorizzata | none | MissingToken: l'intestazione di autorizzazione non è stata trovata. | Questo errore si verifica quando l'autorizzazione ha valori null o non corretti. | Verificare che il valore del token indicato nell'intestazione di autorizzazione sia corretto e non null. |