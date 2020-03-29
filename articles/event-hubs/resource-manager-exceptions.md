---
title: Hub eventi di Azure - Eccezioni di Resource Manager Documenti Microsoft
description: Elenco delle eccezioni di Hub eventi di Azure rilevate da Azure Resource Manager e dalle azioni suggerite.
services: service-bus-messaging
documentationcenter: na
author: spelluru
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2019
ms.author: spelluru
ms.openlocfilehash: e6ee1137fce97cbe5a64aa5287223f6ba09dcf47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74936087"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Hub eventi di Azure - Eccezioni di Resource ManagerAzure Event Hubs - Resource Manager exceptions
Questo articolo elenca le eccezioni generate durante l'interazione con hub eventi di Azure usando Azure Resource Manager tramite modelli o chiamate dirette.

> [!IMPORTANT]
> Questo documento viene aggiornato di frequente. Si prega di controllare di nuovo per gli aggiornamenti.

Nelle sezioni seguenti vengono fornite varie eccezioni/errori che vengono rilevati tramite Azure Resource Manager.The following sections provide various exceptions/errors that are surfaced through Azure Resource Manager.

## <a name="error-code-conflict"></a>Codice di errore: Conflitto

| Codice di errore | Sottocodice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Conflitto | 40300 | Il numero massimo di risorse di tipo EventHub è stato raggiunto o superato. In realtà: : Max consentito: # | Lo spazio dei nomi ha raggiunto la [quota](event-hubs-quotas.md) per il numero di hub eventi che può contenere. | Eliminare tutti gli hub eventi inutilizzati o estranei dallo spazio dei nomi o prendere in considerazione l'aggiornamento a un [cluster dedicato.](event-hubs-dedicated-overview.md) |
| Conflitto | none | La configurazione di ripristino di emergenza non può essere eliminata perché è in corso la replica. Eseguire il failover o interrompere l'associazione prima di tentare di eliminare la configurazione di ripristino di emergenza. | [La replica GeoDR](event-hubs-geo-dr.md) è in corso, pertanto la configurazione non può essere eliminata in questo momento. | Per sbloccare l'eliminazione della configurazione, attendere il completamento della replica, attivare un failover o interrompere l'associazione GeoDR. |
| Conflitto | none | Aggiornamento dello spazio dei nomi non riuscito con conflitto nel back-end. | È in corso un'altra operazione su questo spazio dei nomi. | Attendere il completamento dell'operazione corrente, quindi riprovare. |

## <a name="error-code-429"></a>Codice di errore: 429

| Codice di errore | Sottocodice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | none | Provisioning dello spazio dei nomi in transizioneNamespace provisioning in transition | È in corso un'altra operazione su questo spazio dei nomi. | Attendere il completamento dell'operazione corrente, quindi riprovare. |
| 429 | none | Operazione di ripristino di emergenza in corso. | È attualmente in corso un'operazione [GeoDR](event-hubs-geo-dr.md) su questo spazio dei nomi o un'associazione. | Attendere il completamento dell'operazione GeoDR corrente, quindi riprovare. |

## <a name="error-code-badrequest"></a>Codice di errore: BadRequest

| Codice di errore | Sottocodice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| RichiestaNonValida | 40000 | Impossibile modificare PartitionCount per un hub eventi. | Il livello di base o standard degli hub eventi di Azure non supporta la modifica delle partizioni. | Creare un nuovo hub eventi con il numero desiderato di partizioni nello spazio dei nomi di livello standard o di base. La scalabilità orizzontale delle partizioni è supportata per [i cluster dedicati.](event-hubs-dedicated-overview.md) |
| RichiestaNonValida | 40000 | Il valore ''' per MessageRetentionInDays non è valido per il livello Basic. il valore non può superare '1' giorno/i. | Gli spazi dei nomi Hub eventi di livello base supportano solo la conservazione dei messaggi fino a 1 giorno. | Se si desidera utilizzare più di un giorno di conservazione dei messaggi, [creare uno spazio dei nomi Hub eventi standard.](event-hubs-create.md) | 
| RichiestaNonValida | none | Il nome specificato non è disponibile. | I nomi degli spazi dei nomi devono essere univoci e il nome specificato è già utilizzato. | Se si è il proprietario dello spazio dei nomi esistente con il nome specificato, è possibile eliminarlo, con la perdita di dati. Quindi, riprovare con lo stesso nome. Se lo spazio dei nomi non è sicuro da eliminare (o non si è il proprietario), scegliere un altro nome dello spazio dei nomi. |
| RichiestaNonValida | none | La sottoscrizione specificata ha raggiunto la quota di spazi dei nomi. | La sottoscrizione ha raggiunto la [quota](event-hubs-quotas.md) per il numero di spazi dei nomi che può contenere. | Valutare la possibilità di eliminare gli spazi dei nomi inutilizzati in questa sottoscrizione, creare un'altra sottoscrizione o eseguire l'aggiornamento a un [cluster dedicato.](event-hubs-dedicated-overview.md) |
| RichiestaNonValida | none | Impossibile aggiornare uno spazio dei nomi secondario | Lo spazio dei nomi non può essere aggiornato perché è lo spazio dei nomi secondario in [un'associazione GeoDR](event-hubs-geo-dr.md). | Se appropriato, apportare la modifica allo spazio dei nomi primario in questa associazione. In caso contrario, interrompere l'associazione GeoDR per apportare la modifica. |
| RichiestaNonValida | none | Impossibile impostare l'aumento automatico nello SKU di base | Non è possibile abilitare l'opzione Ingrandita automatica negli spazi dei nomi Hub eventi di livello base. | Per [abilitare il gonfiatore automatico](event-hubs-auto-inflate.md) in uno spazio dei nomi, assicurarsi che sia di livello standard. |
| RichiestaNonValida | none | La capacità di creazione dello spazio dei nomi non è sufficiente. Contattare l'amministratore di Hub eventi. | L'area selezionata è alla capacità e non è possibile creare altri spazi dei nomi. | Selezionare un'altra area per ospitare lo spazio dei nomi. |
| RichiestaNonValida | none | L'operazione non può essere eseguita sul tipo di entità 'ConsumerGroup' perché lo spazio dei nomi 'nome dello spazio dei nomi' utilizza il livello 'Basic'.  | Gli spazi dei nomi Hub eventi di livello base dispongono di un [quota]((event-hubs-quotas.md-event-hubs-basic-and-standard---quotas-and-limits) di un gruppo di consumer (impostazione predefinita). La creazione di più gruppi di consumer non è supportata. | Continuare a usare il gruppo di consumer predefinito ($Default) o, se ne sono necessari altri, è consigliabile usare uno spazio dei nomi Hub eventi di livello standard. | 
| RichiestaNonValida | none | Lo spazio dei nomi 'nome dello spazio dei nomi' non esiste. | Impossibile trovare lo spazio dei nomi fornito. | Verificare che il nome dello spazio dei nomi sia corretto e che sia reperibile nella sottoscrizione. In caso contrario, [creare uno spazio dei nomi Hub eventi](event-hubs-create.md). | 
| RichiestaNonValida | none | La proprietà location della risorsa non corrisponde al relativo spazio dei nomi che lo contiene. | La creazione di un hub eventi in un'area specifica non è riuscita perché non corrisponde all'area dello spazio dei nomi. | Provare a creare l'hub eventi nella stessa area dello spazio dei nomi. | 

## <a name="error-code-internal-server-error"></a>Codice di errore: Errore interno del server

| Codice di errore | Sottocodice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Internal Server Error | none | Errore interno del server. | Il servizio Hub eventi ha rilevato un errore interno. | Ripetere l'operazione non riuscita. Se l'operazione continua a non riuscire, contattare il supporto tecnico. |