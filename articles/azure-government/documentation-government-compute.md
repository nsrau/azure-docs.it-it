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


#  Calcolo di Azure per enti pubblici

##  Macchine virtuali


Le informazioni seguenti identificano il limite di Azure per enti pubblici per Macchine virtuali di Azure:

| Dati regolamentati o controllati consentiti | Dati regolamentati o controllati non consentiti |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| I dati immessi, archiviati ed elaborati in una macchina virtuale possono contenere dati soggetti al controllo all'esportazione. File binari in esecuzione in Macchine virtuali di Azure. Autenticatori statici, ad esempio le password e i PIN delle smart card per l'accesso ai componenti della piattaforma Azure. Chiavi private o i certificati usati per gestire i componenti della piattaforma Azure. Stringhe di connessione SQL. Altri segreti di sicurezza o informazioni, ad esempio chiavi di crittografia, chiavi master, chiavi di archiviazione e certificati archiviati nei servizi di Azure. | I metadati non possono contenere dati soggetti al controllo all'esportazione. Questi metadati includono tutti i dati di configurazione immessi durante la creazione e la gestione della macchina virtuale di Azure. Non immettere dati regolamentati o controllati nei campi relativi a nomi di ruoli tenant, gruppi di risorse, nomi delle distribuzioni, nomi delle risorse e tag delle risorse.  

Per altre informazioni, vedere la <a href=https://azure.microsoft.com/it-it/documentation/services/virtual-machines/windows/>documentazione pubblica su Macchine virtuali di Azure</a>.

Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>

<!---HONumber=AcomDC_0831_2016-->