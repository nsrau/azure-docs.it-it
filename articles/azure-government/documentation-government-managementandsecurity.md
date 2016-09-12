<properties
	pageTitle="Documentazione di Azure per enti pubblici | Microsoft Azure"
	description="Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="08/25/2016"
	ms.author="ryansoc"/>  


#  Sicurezza e gestione di Azure per enti pubblici

##  Insieme di credenziali di chiave

Le informazioni seguenti identificano il limite di Azure per enti pubblici per l'insieme di credenziali delle chiavi di Azure:

| Dati regolamentati o controllati consentiti | Dati regolamentati o controllati non consentiti |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tutti i dati crittografati con una chiave dell'insieme di credenziali delle chiavi di Azure possono contenere dati regolamentati o controllati. | I metadati dell'insieme di credenziali delle chiavi di Azure non possono contenere dati soggetti al controllo all'esportazione. Questi metadati includono tutti i dati di configurazione immessi durante la creazione e la gestione dell'insieme di credenziali delle chiavi. Non immettere dati regolamentati o controllati nei campi relativi a nomi di gruppi di risorse, nomi di insiemi di credenziali delle chiavi e nome della sottoscrizione |

L'insieme di credenziali delle chiavi è disponibile a livello generale in Azure per enti pubblici. Come per la versione pubblica, non esistono estensioni, quindi l'insieme di credenziali delle chiavi è disponibile solo tramite PowerShell e l'interfaccia della riga di comando.

Per altre informazioni, vedere la [documentazione pubblica sull'insieme di credenziali delle chiavi di Azure](/key-vault-get-started).

Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>

<!---HONumber=AcomDC_0831_2016-->