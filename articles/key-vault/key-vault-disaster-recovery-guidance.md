<properties
	pageTitle="Operazioni da eseguire in caso di interruzione di un servizio Azure con impatto sull'insieme di credenziali delle chiavi di Azure | Microsoft Azure"
	description="Informazioni sulle operazioni da eseguire in caso di interruzione di un servizio Azure con impatto sull'insieme di credenziali delle chiavi di Azure."
	services="key-vault"
	documentationCenter=""
	authors="adamglick"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="key-vault"
	ms.workload="key-vault"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="sumedhb;aglick"/>
	

# Disponibilità e ridondanza dell'insieme di credenziali delle chiavi di Azure

Le funzionalità dell'insieme di credenziali delle chiavi di Azure hanno più livelli di ridondanza, per garantire che le chiavi e i segreti rimangano disponibili per l'applicazione anche quando si verificano errori di singoli componenti del servizio.

I contenuti dell'insieme di credenziali delle chiavi vengono replicati sia all'interno dell'area sia in un'area secondaria distante almeno 240 chilometri, ma all'interno della stessa area geografica. Questo garantisce un'elevata durabilità delle chiavi e dei segreti.

Se si verificano errori di singoli componenti del servizio dell'insieme di credenziali delle chiavi, per gestire la richiesta subentrano componenti alternativi all'interno dell'area in modo che non si verifichi alcuna riduzione delle prestazioni delle funzionalità. A tale scopo, non è necessario eseguire alcuna azione in quanto verrà eseguita automaticamente.

Nella rara eventualità che l'intera area di Azure risulti non disponibile, le richieste effettuate all'insieme di credenziali delle chiavi di tale area vengono indirizzate automaticamente a un'area secondaria ("failover"). Quando l'area primaria diventa di nuovo disponibile, le richieste vengono reindirizzate a tale area ("failback"). Anche in questo caso non è necessario eseguire alcuna azione, poiché questa operazione viene eseguita in modo automatico.

Esistono alcune limitazioni che è necessario tenere presenti:

* Nel caso del failover di un'area, il failover del servizio può richiedere alcuni minuti. È possibile che le richieste effettuate durante questo periodo abbiano esito negativo fino al completamento del failover.
* Dopo il completamento del failover, l'insieme di credenziali delle chiavi è in modalità di ___sola lettura___. Le richieste supportate in questa modalità sono le seguenti:
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
* Dopo il failback di un failover, tutti i tipi di richiesta, ad esempio le richieste di lettura ___e___ scrittura, risultano disponibili. 

<!---HONumber=AcomDC_0601_2016-->