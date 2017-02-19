---
title: Guida per sviluppatori di Azure per enti pubblici | Microsoft Docs
description: "Questo articolo fornisce un confronto delle funzionalità e alcune linee guida sullo sviluppo di applicazioni per Azure per enti pubblici."
services: azure-government
cloud: gov
documentationcenter: 
author: sachamicrosoft
manager: zakramer
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/29/2015
ms.author: jharve
translationtype: Human Translation
ms.sourcegitcommit: e0e313f2f3a077f45ba5935363c251bba89bc524
ms.openlocfilehash: c20736554425204f47fed96f8618f66002623259


---
# <a name="azure-government-developer-guide"></a>Guida per sviluppatori di Azure per enti pubblici
L'ambiente di Azure per enti pubblici è un'istanza separata dal resto della rete Microsoft. Questa guida illustra le differenze che gli sviluppatori e gli amministratori di applicazioni devono conoscere per interagire e usare aree separate di Azure.

## <a name="overview"></a>Panoramica
Azure per enti pubblici è un'istanza separata del servizio Microsoft Azure. Soddisfa le esigenze di sicurezza e conformità delle agenzie federali degli Stati Uniti, dei governi statali e locali e dei relativi provider di soluzioni. Azure per enti pubblici offre isolamento fisico e di rete da distribuzioni governative non statunitensi ed è gestita da cittadini statunitensi selezionati.

Microsoft fornisce una serie di strumenti per aiutare gli sviluppatori a creare e distribuire le applicazioni cloud nel servizio globale di Microsoft Azure ("Servizio globale") e nei servizi di Microsoft Azure per enti pubblici.

Al momento della creazione e della distribuzione delle applicazioni nei servizi di Azure per enti pubblici, è necessario che gli sviluppatori conoscano le differenze principali tra questi servizi e il Servizio globale, con particolare riferimento all'installazione e alla configurazione dell'ambiente di programmazione, alla configurazione degli endpoint, alla scrittura delle applicazioni e alla distribuzione delle stesse come servizi di Azure per enti pubblici.

Le informazioni contenute in questo documento riepilogano le differenze tra i due servizi. Integrano le informazioni disponibili nel sito [Azure per enti pubblici](http://www.azure.com/gov "Azure per enti pubblici") e nella [raccolta di documentazione tecnica di Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") su MSDN. Potrebbero essere disponibili informazioni ufficiali anche in altri percorsi, come il [Centro protezione di Microsoft Azure](https://azure.microsoft.com/support/trust-center/ "Centro protezione di Microsoft Azure"), il [Centro documentazione di Azure](https://azure.microsoft.com/documentation/) e i [blog su Azure](https://azure.microsoft.com/blog/ "blog su Azure").

Questo contenuto è destinato ai partner e agli sviluppatori che distribuiscono applicazioni in Microsoft Azure Government.

## <a name="guidance-for-developers"></a>Linee guida per gli sviluppatori
La maggior parte dei contenuti tecnici attualmente disponibili presuppone che le applicazioni vengano sviluppate per il servizio globale e non per Azure per enti pubblici. Per questo motivo è importante conoscere due differenze fondamentali nelle applicazioni sviluppate per l'hosting in Azure per enti pubblici.

* Vi sono differenze in termini di servizi e funzionalità. Questo significa che alcune funzionalità e servizi presenti in aree specifiche del servizio globale potrebbero non essere disponibili in Azure per enti pubblici.
* Le configurazioni delle funzionalità in Azure per enti pubblici potrebbero essere diverse da quelli nel servizio globale. È quindi importante esaminare il codice di esempio, le configurazioni e i passaggi per assicurarsi che la creazione e l'esecuzione avvengano all'interno dell'ambiente dei servizi cloud di Azure per enti pubblici.

## <a name="available-features-and-services-in-azure-government"></a>Funzionalità e servizi disponibili in Azure per enti pubblici
Attualmente Azure per enti pubblici offre i seguenti servizi e funzionalità nelle agenzie governative statunitensi dell'Iowa e della Virginia:

* Macchine virtuali
* Set di scalabilità di macchine virtuali
* Servizio contenitore
* Account Batch
* Raccolte di RemoteApp
* Set di disponibilità
* Rete virtuale
* Bilanciamento del carico
* Gateway applicazione
* Gateway di rete virtuale
* Gateway di rete locali
* Tabelle di route
* Profili di Gestione traffico
* Circuiti ExpressRoute
* Gruppi di sicurezza di rete
* Interfacce di rete
* Indirizzi IP pubblici
* Connessioni
* Account di archiviazione
* StorSimple Manager
* Servizio app
* Servizi multimediali
* Database SQL
* SQL Data Warehouse
* Estensione database di SQL Server
* Cache Redis
* Pool elastici di Database SQL
* SQL Server
* Log Analytics
* Hub eventi
* Spazi dei nomi del bus di servizio
* Azure Active Directory
* Autenticazione a più fattori
* Rights Management
* Account di Automazione
* Marketplace

Altri servizi sono già disponibili e altri ancora verranno aggiunti regolarmente. Per l'elenco di servizi più recente, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/#services), pagina che visualizza i servizi disponibili in ogni area.

Attualmente, le agenzie governative statunitensi dell'Iowa e della Virginia sono i data center di supporto di Azure per enti pubblici. Per i data center e i servizi disponibili correnti, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/#services).

## <a name="endpoint-mapping"></a>Mapping degli endpoint
Consultare la seguente tabella per saperne di più sul mapping di endpoint pubblici di Azure e del database SQL a endpoint specifici di Azure per enti pubblici:

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

Per l'autenticazione Azure Resource Manager tramite Azure Active Directory, vedere [Authenticating Azure Resource Manager Requests](https://msdn.microsoft.com/library/azure/dn790557.aspx) (Autenticazione delle richieste di Azure Resource Manager).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Azure per enti pubblici, vedere le risorse seguenti:

* [Iscriversi per una versione di valutazione gratuita](https://azuregov.microsoft.com/trial/azuregovtrial)
* [Acquisizione e accesso in Azure per enti pubblici](http://azure.com/gov)
* [Panoramica di Azure per enti pubblici](/azure-government-overview)
* [Blog su Azure per enti pubblici](http://blogs.msdn.com/b/azuregov/)
* [Conformità di Azure](https://azure.microsoft.com/support/trust-center/compliance/)



<!--HONumber=Jan17_HO1-->


