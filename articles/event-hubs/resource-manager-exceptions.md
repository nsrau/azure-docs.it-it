---
title: Hub eventi di Azure-Gestione risorse eccezioni | Microsoft Docs
description: Elenco delle eccezioni di hub eventi di Azure emerse da Azure Resource Manager e le azioni consigliate.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: d8d52f0a0c58ee756afa4d5d8599e2981edb9cdc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85312523"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Hub eventi di Azure-eccezioni Gestione risorse
Questo articolo elenca le eccezioni generate quando si interagisce con hub eventi di Azure usando modelli di Azure Resource Manager tramite o chiamate dirette.

> [!IMPORTANT]
> Questo documento viene aggiornato di frequente. Verificare la disponibilità di aggiornamenti.

Le sezioni seguenti forniscono varie eccezioni/errori che vengono rilevati tramite Azure Resource Manager.

## <a name="error-code-conflict"></a>Codice di errore: conflitto

| Codice di errore | Codice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Conflitto | 40300 | È stato raggiunto o superato il numero massimo di risorse di tipo EventHub. Effettivo: #, massimo consentito: # | Lo spazio dei nomi ha raggiunto la [quota](event-hubs-quotas.md) per il numero di hub eventi che può contenere. | Eliminare gli hub eventi non usati o estranei dallo spazio dei nomi oppure provare a eseguire l'aggiornamento a un [cluster dedicato](event-hubs-dedicated-overview.md). |
| Conflitto | Nessuno | La configurazione del ripristino di emergenza non può essere eliminata perché è in corso la replica. Eseguire il failover o interrompere l'associazione prima di provare a eliminare la configurazione di ripristino di emergenza. | La [replica di GeoDR](event-hubs-geo-dr.md) è in corso, quindi non è possibile eliminare la configurazione in questo momento. | Per sbloccare l'eliminazione della configurazione, attendere il completamento della replica, attivare un failover o interrompere l'associazione di GeoDR. |
| Conflitto | Nessuno | Aggiornamento dello spazio dei nomi non riuscito con conflitto nel back-end. | È attualmente in corso un'altra operazione su questo spazio dei nomi. | Attendere il completamento dell'operazione corrente, quindi riprovare. |

## <a name="error-code-429"></a>Codice di errore: 429

| Codice di errore | Codice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | Nessuno | Provisioning dello spazio dei nomi in transizione | È attualmente in corso un'altra operazione su questo spazio dei nomi. | Attendere il completamento dell'operazione corrente, quindi riprovare. |
| 429 | Nessuno | Operazione di ripristino di emergenza in corso. | È attualmente in corso un'operazione [GeoDR](event-hubs-geo-dr.md) su questo spazio dei nomi o l'associazione. | Attendere il completamento dell'operazione di GeoDR corrente, quindi riprovare. |

## <a name="error-code-badrequest"></a>Codice di errore: richiesta non valida

| Codice di errore | Codice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | Non è possibile modificare PartitionCount per un hub eventi. | Il livello Basic o standard di hub eventi di Azure non supporta la modifica delle partizioni. | Creare un nuovo hub eventi con il numero di partizioni desiderato nello spazio dei nomi di livello Basic o standard. La scalabilità orizzontale della partizione è supportata per i [cluster dedicati](event-hubs-dedicated-overview.md). |
| BadRequest | 40000 | Il valore ' #' per MessageRetentionInDays non è valido per il livello Basic. il valore non può essere maggiore di "1" giorno/i. | Gli spazi dei nomi di hub eventi di livello Basic supportano solo la conservazione dei messaggi fino a un giorno. | Se è stato voluto più di un giorno di conservazione dei messaggi, [creare uno spazio dei nomi di hub eventi standard](event-hubs-create.md). | 
| BadRequest | Nessuno | Il nome specificato non è disponibile. | I nomi degli spazi dei nomi devono essere univoci e il nome specificato è già stato accettato. | Se si è proprietari dello spazio dei nomi esistente con il nome specificato, è possibile eliminarlo, che causerà la perdita di dati. Quindi, riprovare con lo stesso nome. Se lo spazio dei nomi non è sicuro da eliminare (o non si è il proprietario), scegliere un altro nome di spazio dei nomi. |
| BadRequest | Nessuno | La sottoscrizione specificata ha raggiunto la quota di spazi dei nomi. | La sottoscrizione ha raggiunto la [quota](event-hubs-quotas.md) per il numero di spazi dei nomi che può ospitare. | Si consiglia di eliminare gli spazi dei nomi non usati in questa sottoscrizione, creare un'altra sottoscrizione o eseguire l'aggiornamento a un [cluster dedicato](event-hubs-dedicated-overview.md). |
| BadRequest | Nessuno | Non è possibile aggiornare uno spazio dei nomi secondario | Lo spazio dei nomi non può essere aggiornato perché è lo spazio dei nomi secondario in un' [associazione GeoDR](event-hubs-geo-dr.md). | Se necessario, apportare invece la modifica allo spazio dei nomi primario in questa associazione. In caso contrario, interrompere l'associazione di GeoDR per apportare la modifica. |
| BadRequest | Nessuno | Non è possibile impostare il gonfiaggio automatico nello SKU Basic | Non è possibile abilitare l'aumento automatico per gli spazi dei nomi di hub eventi di livello Basic. | Per [abilitare il gonfiaggio automatico](event-hubs-auto-inflate.md) in uno spazio dei nomi, assicurarsi che sia di livello standard. |
| BadRequest | Nessuno | Non è disponibile una capacità sufficiente per creare lo spazio dei nomi. Contattare l'amministratore di hub eventi. | L'area selezionata è alla capacità e non è possibile creare altri spazi dei nomi. | Selezionare un'altra area per ospitare lo spazio dei nomi. |
| BadRequest | Nessuno | Non è possibile eseguire l'operazione sul tipo di entità' gruppo Consumer ' perché lo spazio dei nomi ' namespace name ' usa il livello ' Basic '.  | Gli spazi dei nomi di hub eventi di livello Basic hanno un valore di [quota] ((hub eventi-quote. MD # Event-Hub-Basic-e-standard---quote-e-Limits) di un gruppo di consumer (impostazione predefinita). La creazione di più gruppi di consumer non è supportata. | Continuare a usare il gruppo di consumer predefinito ($Default) o, se sono necessari altri, provare a usare uno spazio dei nomi di hub eventi di livello standard. | 
| BadRequest | Nessuno | Lo spazio dei nomi ' namespace name ' non esiste. | Non è stato possibile trovare lo spazio dei nomi specificato. | Verificare che il nome dello spazio dei nomi sia corretto e che si trovi nella sottoscrizione. In caso contrario, [creare uno spazio dei nomi di hub eventi](event-hubs-create.md). | 
| BadRequest | Nessuno | La proprietà Location della risorsa non corrisponde allo spazio dei nomi che lo contiene. | La creazione di un hub eventi in un'area specifica non è riuscita perché non corrisponde all'area dello spazio dei nomi. | Provare a creare l'hub eventi nella stessa area dello spazio dei nomi. | 

## <a name="error-code-internal-server-error"></a>Codice di errore: errore interno del server

| Codice di errore | Codice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Internal Server Error | Nessuno | Errore interno del server. | Si è verificato un errore interno del servizio Hub eventi. | Ripetere l'operazione non riuscita. Se l'operazione continua ad avere esito negativo, contattare il supporto tecnico. |