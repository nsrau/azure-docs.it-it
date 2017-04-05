---
title: Informazioni su Azure Analysis Services | Microsoft Docs
description: Ottenere un quadro generale di Analysis Services in Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 3539da8afe5781d74cbf723090050b767373f268
ms.lasthandoff: 03/25/2017


---
# <a name="what-is-azure-analysis-services"></a>Informazioni su Azure Analysis Services
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Basato sul collaudato motore analitico di Microsoft SQL Server Analysis Services, Azure Analysis Services offre funzionalità di modellazione dei dati di livello aziendale nel cloud. 

Guardare il video seguente per saperne di più su come Azure Analysis Services si integra con le funzionalità BI generali di Microsoft e su come è possibile trarre vantaggio dai propri modelli semantici nel cloud.

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesGettingStarted/player]
>
>

> [!IMPORTANT]
> Azure Analysis Services è in versione di **anteprima**. Alcune funzionalità non sono ancora operative. Assicurarsi di consultare la sezione [Aspettative anteprima](#preview-expectations) più avanti in questo articolo e di consultare il [blog di Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920) per le informazioni più recenti.
> 
> 

## <a name="built-on-sql-server-analysis-services"></a>Compilare in SQL Server Analysis Services
Azure Analysis Services è compatibile con SQL Server 2016 Analysis Services Enterprise Edition. Azure Analysis Services supporta i modelli tabulari a livello di compatibilità 1200. Sono supportati anche DirectQuery, partizioni, sicurezza a livello di riga, relazioni bidirezionali e traduzioni.

## <a name="use-the-tools-you-already-know"></a>Usare gli strumenti noti
![Strumenti di sviluppo di Business Intelligence](./media/analysis-services-overview/aas-overview-dev-tools.png)

Durante la creazione di modelli dati per Azure Analysis Services, usare gli stessi strumenti di SQL Server Analysis Services. Creare e distribuire i modelli tabulari usando le versioni più recenti di [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) o i modelli di [Azure Powershell](/powershell/azureps-cmdlets-docs) e [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) in [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connect-to-data-sources"></a>Connettersi alle origini dati
I modelli dati distribuiti ai server in Azure supportano la connessione alle origini dati locali nell'organizzazione o nel cloud. Combinare i dati provenienti dall'ambiente locale e dalle origini dati cloud per una soluzione di Business Intelligence ibrida.

![Origini dati](./media/analysis-services-overview/aas-overview-data-sources.png)

Poiché il server è nel cloud, la connessione alle origini dati cloud è semplice. Quando ci si connette alle origini dati locali, il [gateway dati locale](analysis-services-gateway.md) garantisce connessioni veloci e sicure con il server Analysis Services nel cloud.  

 \* Alcune origini dati non sono ancora supportate nella versione di anteprima. Per altre informazioni, vedere la sezione [Aspettative anteprima](#preview-expectations) più avanti in questo articolo.

## <a name="explore-your-data-from-anywhere"></a>Esplorare i dati da qualsiasi luogo
Connettersi e [ottenere dati](analysis-services-connect.md) dai server da qualsiasi piattaforma. Azure Analysis Services supporta la connessione da Power BI Desktop, Excel, app personalizzate e strumenti basati su browser.

![Visualizzazioni di dati](./media/analysis-services-overview/aas-overview-visualization.png)

 \* Power BI Embedded non è ancora supportato nella versione di anteprima.

## <a name="secure"></a>Garantire la sicurezza
#### <a name="user-authentication"></a>Autenticazione utente
L'autenticazione utente per Azure Analysis Services viene gestita da [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). Quando si tenta di accedere a un database di Azure Analysis Services, gli utenti usano un'identità dell'account dell'organizzazione con accesso al database a cui si sta tentando di accedere. Queste identità utente devono essere membri dell'istanza predefinita di Azure Active Directory per la sottoscrizione in cui risiede il server Azure Analysis Services. [Integrazione di directory](https://technet.microsoft.com/library/jj573653.aspx) tra AAD e Active Directory locale è un ottimo modo per concedere agli utenti locali l'accesso a un database di Azure Analysis Services, ma non è obbligatorio per tutti gli scenari.

Gli utenti accedono con il nome dell'entità utente del proprio account e la password. Dopo la sincronizzazione con un'istanza locale di Active Directory, il nome dell'entità utente corrisponde in genere all'indirizzo di posta elettronica aziendale.

Le autorizzazioni per la gestione delle risorse del server Azure Analysis Services sono gestite tramite l'assegnazione di utenti ai ruoli nell'ambito della sottoscrizione di Azure. Per impostazione predefinita, gli amministratori della sottoscrizione dispongono di autorizzazioni proprietario per l'accesso alla risorsa del server in Azure. È possibile aggiungere altri utenti tramite Azure Resource Manager.

#### <a name="data-security"></a>Sicurezza dei dati
Azure Analysis Services usa l'archiviazione BLOB di Azure per rendere persistenti i metadati e l'archiviazione per i database di Analysis Services. I file di dati nel BLOB vengono crittografati mediante crittografia sul lato server di BLOB di Azure. Quando si usa la modalità DirectQuery, vengono archiviati solo i metadati. Ai dati effettivi è possibile accedere dall'origine dati in fase di query.

#### <a name="on-premises-data-sources"></a>Origini dati locali
È possibile ottenere la sicurezza dell'accesso ai dati in locale all'interno dell'organizzazione installando e configurando un [gateway dati locale](analysis-services-gateway.md). I gateway forniscono accesso ai dati per le modalità DirectQuery e in memoria. Quando un modello di Azure Analysis Services si connette a un'origine dati locale, viene creata una query con le credenziali crittografate per l'origine dati locale. Il servizio cloud del gateway analizza la query e inserisce la richiesta in un'istanza del bus di servizio di Azure. Il gateway locale esegue il polling del bus di servizio per le richieste in sospeso. Il gateway riceve quindi la query, decrittografa le credenziali e si connette all'origine dati per l'esecuzione. I risultati vengono inviati dall'origine dati al gateway e quindi al database Azure Analysis Services.

Azure Analysis Services è disciplinato dalle [Condizioni di Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e dall'[Informativa sulla privacy di Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Per altre informazioni sulla sicurezza di Azure, vedere [Microsoft Trust Center](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="get-help"></a>Ottenere aiuto
La gestione e la configurazione di Azure Analysis Services sono semplici. In quest'area è possibile trovare tutte le informazioni necessarie per creare e gestire un server. La creazione di un modello dati per la distribuzione al server è analoga alla creazione di un modello dati distribuiti a un server locale. È disponibile una ricca libreria concettuale, procedurale e di esercitazioni e articoli di riferimento in [Analysis Services su MSDN](https://msdn.microsoft.com/library/bb522607.aspx).

Sono inoltre disponibili numerosi video utili in [Azure Analysis Services su Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

Per rimanere sempre aggiornati sulle continue modifiche, è possibile consultare il [blog di Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920).

## <a name="community"></a>Community
Analysis Services è costituito da una vivace community di utenti. È possibile partecipare alle discussioni sul [forum di Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Commenti e suggerimenti
In caso di suggerimenti o richieste di funzionalità, assicurarsi di lasciare i propri commenti in [Commenti e suggerimenti su Azure Analysis Services](https://aka.ms/azureanalysisservicesfeedback).

In caso di suggerimenti sulla documentazione, è possibile aggiungere i commenti usando Disqus nella parte inferiore di ogni articolo.

## <a name="preview-expectations"></a>Aspettative anteprima
Azure Analysis Services è attualmente in versione di anteprima. Esistono alcuni aspetti che è necessario tenere presenti.

##### <a name="server-modes"></a>Modalità del server
Azure Analysis Services supporta attualmente la modalità tabulare per i modelli tabulari a livello di compatibilità 1200. Le modalità multidimensionale, Data Mining e PowerPivot per SharePoint non sono supportate.

##### <a name="data-sources"></a>Origini dati
Per l'anteprima, le origini dati seguenti sono supportate nei modelli tabulari 1200 distribuiti a un server Azure Analysis Services.

| **Cloud** | **Locale (richiede gateway dati)** |
| --- | --- |
| Database SQL |SQL Server |
| SQL Data Warehouse |APS |
| Oracle | Oracle |
| Teradata | |

### <a name="data-source-providers"></a>Provider di origini dati
I modelli dati in Azure Analysis Services possono richiedere diversi provider di dati per connettersi alle origini dati rispetto ai modelli dati in SQL Server Analysis Services. I requisiti del provider di dati dipendono dal fatto che l'origine dati si trovi nel cloud o in locale e dal tipo di modello di dati: in memoria o DirectQuery. Per altre informazioni, vedere [Datasource connections](analysis-services-datasource.md) (Connessioni alle origini dati).

### <a name="client-connections"></a>Connessioni client

Le applicazioni client richiedono ai [provider di dati](analysis-services-data-providers.md) client più recenti di connettersi ad Azure Analysis Services.

Le cartelle di lavoro di Excel con connessioni attive a un server Azure Analysis Services e salvate in OneDrive o SharePoint Online non sono supportate.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver ottenuto alcune informazioni su Azure Analysis Services, è possibile iniziare a usarlo. Informazioni su come [creare un server](analysis-services-create-server.md) in Azure e [distribuire un modello tabulare](analysis-services-deploy.md) in tale server.


