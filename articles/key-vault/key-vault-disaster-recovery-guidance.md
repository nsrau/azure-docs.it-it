---
title: Operazioni da eseguire in caso di interruzione di un servizio Azure con impatto sull'insieme di credenziali delle chiavi di Azure | Microsoft Docs
description: Informazioni sulle operazioni da eseguire in caso di un'interruzione del servizio Azure con impatto sull'insieme di credenziali delle chiavi di Azure.
services: key-vault
documentationcenter: ''
author: adamglick
manager: mbaldwin
editor: ''

ms.service: key-vault
ms.workload: key-vault
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: sumedhb;aglick

---
# Disponibilità e ridondanza dell'insieme di credenziali delle chiavi di Azure
L'insieme di credenziali delle chiavi di Azure dispone di più livelli di ridondanza, per garantire che le chiavi e i segreti rimangano disponibili per l'applicazione anche quando si verificano errori di singoli componenti del servizio.

I contenuti dell'insieme di credenziali delle chiavi vengono replicati sia all'interno dell'area sia in un'area secondaria distante almeno 240 chilometri, ma all'interno della stessa area geografica. Questo garantisce un'elevata durabilità delle chiavi e dei segreti.

Se si verificano errori di singoli componenti del servizio dell'insieme di credenziali delle chiavi, per gestire la richiesta subentrano componenti alternativi all'interno dell'area in modo che non si verifichi alcuna riduzione delle prestazioni delle funzionalità. A tale scopo, non è necessario alcun intervento in quanto l'azione verrà eseguita automaticamente e in modo trasparente per l'utente.

Nella rara eventualità che l'intera area di Azure risulti non disponibile, le richieste eseguite all'insieme di credenziali delle chiavi di tale area vengono indirizzate automaticamente a un'area secondaria ("failover"). Quando l'area primaria diventa di nuovo disponibile, le richieste vengono reindirizzate a tale area ("failback"). Anche in questo caso non è richiesta alcuna azione, poiché questa operazione viene eseguita in modo automatico.

Esistono alcune limitazioni che è necessario tenere presenti:

* Nel caso del failover di un'area, il failover del servizio può richiedere alcuni minuti. È possibile che le richieste eseguite durante questo periodo abbiano esito negativo fino al completamento del failover.
* Dopo il completamento del failover, l'insieme di credenziali delle chiavi è in modalità di ***sola lettura***. Le richieste supportate in questa modalità sono le seguenti:
  * list key vaults
  * get properties of key vaults
  * list secrets
  * get secrets
  * list keys
  * get (properties of) keys
  * encrypt
  * decrypt
  * wrap
  * unwrap
  * verify
  * sign
  * backup
* Dopo il failback di un failover, tutti i tipi di richiesta, ad esempio le richieste di lettura ***e*** scrittura, risultano disponibili.

<!---HONumber=AcomDC_0831_2016-->