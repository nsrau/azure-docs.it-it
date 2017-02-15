---
title: Guida per sviluppatori di Azure Government
description: "Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government"
services: 
cloud: gov
documentationcenter: 
author: Joharve2
manager: Chrisnie
editor: 
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/29/2015
ms.author: jharve
translationtype: Human Translation
ms.sourcegitcommit: 08de3cb0d62e800db88238764e4f9f221d2add06
ms.openlocfilehash: 42c1b022a22cefae15d32690856bdb80b0eaffa9


---
# <a name="microsoft-azure-government-developer-guide"></a>Guida per gli sviluppatori di Microsoft Azure Government
<p> L'ambiente di Azure per enti pubblici è un'istanza separata dal resto della rete Microsoft.  Questa guida per sviluppatori fornisce i dettagli sulle differenze che gli sviluppatori e gli amministratori di applicazioni devono conoscere per interagire e usare queste aree separate di Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## <a name="in-this-topic"></a>Contenuto dell'argomento
* [Panoramica](#Overview)
* [Linee guida per gli sviluppatori](#Guidance)
* [Funzionalità attualmente disponibili in Microsoft Azure Government](#Features)
* [Mapping degli endpoint](#Endpoint)
* [Passaggi successivi](#next)

## <a name="a-nameoverviewaoverview"></a><a name="Overview"></a>Panoramica
Microsoft Azure Government è un'istanza separata del servizio Microsoft Azure creata per soddisfare i requisiti di sicurezza e conformità delle agenzie federali statunitensi, degli enti pubblici statali e locali e dei relativi provider di soluzioni. Azure Government offre isolamento fisico e di rete da distribuzioni non governative ed è gestita da cittadini statunitensi selezionati.

Microsoft fornisce una serie di strumenti per creare e distribuire le applicazioni cloud nel servizio globale di Microsoft Azure (“Servizio globale”) e nei servizi di Microsoft Azure Government.

Al momento della creazione e della distribuzione delle applicazioni nei servizi di Azure Government, è necessario che gli sviluppatori conoscano le differenze principali tra questi servizi e il Servizio globale,  con particolare riferimento all'installazione e alla configurazione dell'ambiente di programmazione, alla configurazione degli endpoint, alla scrittura delle applicazioni e alla distribuzione delle stesse come servizi di Azure Government.

Questo documento presenta un riepilogo di tali differenze e integra le informazioni disponibili nel sito [Azure per enti pubblici](http://www.azure.com/gov "Azure per enti pubblici") e nella [raccolta di documentazione tecnica di Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") su MSDN. Potrebbero essere disponibili informazioni ufficiali anche in molti altri percorsi, come il [Centro protezione di Microsoft Azure](https://azure.microsoft.com/support/trust-center/ "Centro protezione di Microsoft Azure"/), il [Centro documentazione di Azure](https://azure.microsoft.com/documentation/) e i [blog su Azure](https://azure.microsoft.com/blog/ "blog su Azure"/).

Questo contenuto è destinato ai partner e agli sviluppatori che distribuiscono applicazioni in Microsoft Azure Government.

## <a name="a-nameguidanceaguidance-for-developers"></a><a name="Guidance"></a>Linee guida per gli sviluppatori
La maggior parte dei contenuti tecnici attualmente disponibili presuppone che le applicazioni siano sviluppate per il Servizio globale e non per Microsoft Azure Government, pertanto è importante assicurarsi che gli sviluppatori conoscano le differenze principali per le applicazioni sviluppate per essere ospitate in Azure Government.

* In primo luogo, vi sono differenze in termini di servizi e funzionalità. Questo significa che alcune funzionalità presenti in aree specifiche del Servizio globale potrebbero non essere disponibili in Azure Government.
* In secondo luogo, le funzionalità offerte in Azure Government presentano differenze di configurazione rispetto al Servizio globale.  È pertanto necessario esaminare il codice di esempio, le configurazioni e i passaggi per assicurarsi che la creazione e l'esecuzione avvengano all'interno dell'ambiente dei servizi cloud di Azure Government.

## <a name="a-namefeaturesa-features-currently-available-in-microsoft-azure-government"></a><a name="Features"></a> Funzionalità attualmente disponibili in Microsoft Azure Government
Attualmente Azure Government offre i seguenti servizi disponibili nelle agenzie governative statunitensi dell'Iowa e della Virginia:

* Macchine virtuali
* Set di scalabilità di macchine virtuali
* Servizi contenitore
* Account Batch
* Raccolte di RemoteApp
* SET DI DISPONIBILITÀ
* Reti virtuali
* Servizi di bilanciamento del carico
* Gateway di applicazione
* Gateway di rete virtuale
* Gateway di rete locali
* Tabelle di route
* Profili di Gestione traffico
* Circuiti ExpressRoute
* Gruppo di sicurezza di rete
* Interfacce di rete
* Indirizzi IP pubblici
* Connessioni
* Account di archiviazione
* StorSimple Manager
* Servizi app
* Servizi multimediali
* DATABASE SQL
* SQL Data Warehouse
* Estensioni database di SQL Server
* Cache Redis
* Pool elastici SQL
* Server di SQL
* Log Analytics
* Hub eventi
* Spazi dei nomi del bus di servizio
* Azure Active Directory
* Autenticazione a più fattori
* Rights Management
* Account di Automazione
* Marketplace

Altri servizi sono già disponibili e altri ancora verranno aggiunti regolarmente.  Per l'elenco più aggiornato dei servizi, vedere la [pagina aree](https://azure.microsoft.com/regions/#services) in cui sono evidenziate tutte le aree disponibili e i relativi servizi.

Attualmente, le agenzie governative statunitensi dell'Iowa e della Virginia sono i data center di supporto di Azure Government.  Fare riferimento alla pagina aree riportata sopra per l'elenco dei centri dati e dei servizi disponibili.

## <a name="a-nameendpointaendpoint-mapping"></a><a name="Endpoint"></a>Mapping degli endpoint
Usare la seguente tabella come guida quando si esegue il mapping di endpoint pubblici di Microsoft Azure e del database SQL a endpoint specifici di Azure Government.

| Nome | Endpoint di Azure per enti pubblici |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId  | https://management.core.usgovcloudapi.net/ |
| GalleryUrl | https://gallery.usgovcloudapi.net/ |
| ManagementPortalUrl | https://manage.windowsazure.us |
| ServiceManagementUrl | https://management.core.usgovcloudapi.net/ |
| PublishSettingsFileUrl | https://manage.windowsazure.us/publishsettings/index |
| ResourceManagerUrl | https://management.usgovcloudapi.net/ |
| SqlDatabaseDnsSuffix | .database.usgovcloudapi.net |
| StorageEndpointSuffix | core.usgovcloudapi.net |
| ActiveDirectoryAuthority | https://login-us.microsoftonline.com/ |
| GraphUrl | https://graph.windows.net/ |
| GraphEndpointResourceId | https://graph.windows.net/ |
| TrafficManagerDnsSuffix | usgovtrafficmanager.net |
| AzureKeyVaultDnsSuffix | vault.usgovcloudapi.net |
| AzureKeyVaultServiceEndpointResourceId | https://vault.usgovcloudapi.net |

* Per l'autenticazione Azure Resource Manager tramite Azure AD, vedere [Authenticating Azure Resource Manager Requests](https://msdn.microsoft.com/library/azure/dn790557.aspx) (Autenticazione di richieste di gestione risorse di Azure)

## <a name="a-namenextanext-steps"></a><a name="next"></a>Passaggi successivi
Per altre informazioni dettagliate su Azure Government, servirsi dei collegamenti riportati di seguito.

* **[Iscriversi per una versione di valutazione gratuita](https://azuregov.microsoft.com/trial/azuregovtrial)**
* **[Acquisizione e accesso in Azure per enti pubblici](http://azure.com/gov)**
* **[Panoramica di Azure per enti pubblici](/azure-government-overview)**
* **[Blog su Azure per enti pubblici](http://blogs.msdn.com/b/azuregov/)**
* **[Conformità di Azure](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md



<!--HONumber=Dec16_HO2-->


