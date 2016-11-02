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
    ms.date="09/29/2016"
    ms.author="ryansoc"/>



#  <a name="azure-government-compute"></a>Calcolo di Azure per enti pubblici

##  <a name="virtual-machines"></a>Macchine virtuali

Per informazioni dettagliate su questo servizio e come usarlo, vedere [Dimensioni delle macchine virtuali in Azure](../virtual-machines/virtual-machines-windows-sizes.md).

### <a name="variations"></a>Varianti

Le SKU delle VM seguenti sono disponibili a livello generale (GA) in Microsoft Azure per enti pubblici:

SKU di VM|US Gov VA|US Gov IA|Note
---|---|---|---
A|GA|GA|None
Dv1|GA|-|None
DSv1|GA|-|None
Dv2|GA|GA|15 - presto disponibile
F|GA|GA|None
G|Pianificata|-|None

###  <a name="data-considerations"></a>Considerazioni sui dati

Le informazioni seguenti identificano il limite di Azure per enti pubblici per Macchine virtuali di Azure:

| Dati regolamentati o controllati consentiti | Dati regolamentati o controllati non consentiti |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| I dati immessi, archiviati ed elaborati in una VM possono contenere dati soggetti al controllo all'esportazione. File binari in esecuzione in Macchine virtuali di Azure. Autenticatori statici, ad esempio le password e i PIN delle smart card per l'accesso ai componenti della piattaforma Azure. Chiavi private o i certificati usati per gestire i componenti della piattaforma Azure. Stringhe di connessione SQL.  Altri segreti di sicurezza o informazioni, ad esempio chiavi di crittografia, chiavi master, chiavi di archiviazione e certificati archiviati nei servizi di Azure.  | I metadati non possono contenere dati soggetti al controllo all'esportazione. Questi metadati includono tutti i dati di configurazione immessi durante la creazione e la gestione della macchina virtuale di Azure.  Non immettere dati regolamentati o controllati nei campi relativi a nomi di ruoli tenant, gruppi di risorse, nomi delle distribuzioni, nomi delle risorse e tag delle risorse  

## <a name="next-steps"></a>Passaggi successivi

Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>



<!--HONumber=Oct16_HO2-->


