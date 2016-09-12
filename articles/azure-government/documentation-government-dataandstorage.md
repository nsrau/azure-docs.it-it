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


#  Dati e archiviazione di Azure per enti pubblici

##  Archiviazione

Le informazioni seguenti identificano il limite di Azure per enti pubblici per Archiviazione di Azure:

| Dati regolamentati o controllati consentiti | Dati regolamentati o controllati non consentiti |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| I dati immessi, archiviati ed elaborati in un prodotto di archiviazione di Azure possono contenere dati soggetti al controllo all'esportazione. Autenticatori statici, ad esempio le password e i PIN delle smart card per l'accesso ai componenti della piattaforma Azure. Chiavi private o i certificati usati per gestire i componenti della piattaforma Azure. Altri segreti di sicurezza o informazioni, ad esempio chiavi di crittografia, chiavi master, chiavi di archiviazione e certificati archiviati nei servizi di Azure. | I metadati di Archiviazione di Azure non possono contenere dati soggetti al controllo all'esportazione. Questi metadati includono tutti i dati di configurazione immessi durante la creazione e la gestione del prodotto di archiviazione. Non immettere dati regolamentati o controllati nei campi relativi a gruppi di risorse, nomi delle distribuzioni, nomi delle risorse e tag delle risorse  

Per altre informazioni, vedere la <a href=https://azure.microsoft.com/documentation/services/storage/> documentazione pubblica di Archiviazione di Azure </a>.

Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>

##  Database SQL

Le informazioni seguenti identificano il limite di Azure per enti pubblici per Archiviazione di Azure:

| Dati regolamentati o controllati consentiti | Dati regolamentati o controllati non consentiti |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tutti i dati archiviati ed elaborati in database SQL di Microsoft Azure possono contenere dati regolamentati di Azure per enti pubblici. È necessario usare gli strumenti di database per il trasferimento dei dati regolamentati di Azure per enti pubblici. | I metadati del database SQL di Azure non possono contenere dati soggetti al controllo all'esportazione. Questi metadati includono tutti i dati di configurazione immessi durante la creazione e la gestione del prodotto di archiviazione. Non immettere dati regolamentati o controllati nei campi relativi a nome del database, nome della sottoscrizione, gruppi di risorse, nome del server, account di accesso amministratore del server, nomi delle distribuzioni, nomi delle risorse e tag delle risorse

Il database SQL versione 11 è disponibile a livello generale in Azure per enti pubblici.

Per informazioni aggiuntive sulla configurazione della visibilità dei metadati e per le procedure consigliate sulla protezione, vedere <a href="https://msdn.microsoft.com/it-IT/library/bb510589.aspx"> Centro sicurezza per il motore di database di SQL Server </a> e la <a href="https://azure.microsoft.com/documentation/services/sql-database/"> documentazione pubblica sul database SQL di Azure </a>.

Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>

<!---HONumber=AcomDC_0831_2016-->