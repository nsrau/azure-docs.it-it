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
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: cc85ae1c6642e763e57327c151ec68845f7673f7
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---
# <a name="what-is-azure-analysis-services"></a>Informazioni su Azure Analysis Services
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Basato sul collaudato motore analitico di Microsoft SQL Server Analysis Services, Azure Analysis Services offre funzionalità di modellazione dei dati di livello aziendale nel cloud. 

Guardare il video seguente per informazioni su come Azure Analysis Services si integra con le funzionalità BI generali di Microsoft e su come è possibile trarre vantaggio dai propri modelli di dati nel cloud.


>
>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/Azure-Analysis-Services-overview/player]
>
>


## <a name="built-on-sql-server-analysis-services"></a>Compilare in SQL Server Analysis Services
Azure Analysis Services è compatibile con SQL Server Analysis Services Enterprise Edition, già noto agli utenti. Azure Analysis Services supporta modelli tabulari con livello di compatibilità 1200 o successivo. Sono supportati anche DirectQuery, partizioni, sicurezza a livello di riga, relazioni bidirezionali e traduzioni.

## <a name="use-the-tools-you-already-know"></a>Usare gli strumenti noti
![Strumenti di sviluppo di Business Intelligence](./media/analysis-services-overview/aas-overview-dev-tools.png)

Durante la creazione di modelli dati per Azure Analysis Services, usare gli stessi strumenti di SQL Server Analysis Services. Creare e distribuire i modelli con [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) oppure usando [Azure Powershell](/powershell/azureps-cmdlets-docs) e [modelli di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) in [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="supports-the-latest-features"></a>Supporto delle funzionalità più recenti
Azure Analysis Services supporta modelli tabulari con i livelli di compatibilità 1200 e 1400 (anteprima).

**Tabulare 1200**: incluso per la prima volta in SQL Server 2016 Analysis Services, il livello 1200 ha introdotto il modello a oggetti tabulare per descrivere oggetti dei modelli come tabelle, colonne e relazioni. Il modello a oggetti tabulare viene esposto in JSON tramite [TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) e nel linguaggio di definizione dati AMO tramite lo spazio dei nomi [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx).

**Tabulare 1400 (anteprima)**: introduce il supporto per righe di dettaglio e gerarchie incomplete, la sicurezza a livello di oggetto, un'esperienza di recupero dati moderna per la connettività dei dati e molti altri miglioramenti. Per sfruttare tutte le funzionalità più recenti, è necessario usare l'ultima versione di [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx). Dato che Tabulare 1400 è ancora in anteprima, vengono apportate frequenti modifiche. Per informazioni aggiornate, vedere questo [post di blog](https://azure.microsoft.com/blog/1400-models-in-azure-as/).

## <a name="data-sources"></a>Origini dati
I modelli dati distribuiti ai server in Azure supportano la connessione alle origini dati locali nell'organizzazione o nel cloud. Combinare i dati provenienti dall'ambiente locale e dalle origini dati cloud per una soluzione di Business Intelligence ibrida.

Poiché il server è nel cloud, la connessione alle origini dati cloud è semplice. Quando ci si connette a origini dati locali, il [gateway dati locale](analysis-services-gateway.md) garantisce connessioni veloci e sicure con il server nel cloud.

Per altre informazioni sulle origini dati locali supportate, vedere l'articolo relativo alle [origini dati supportate in Azure Analysis Services](analysis-services-datasource.md).


## <a name="explore-your-data-from-anywhere"></a>Esplorare i dati da qualsiasi luogo
È possibile connettersi e ottenere dati dai server da qualsiasi piattaforma. Azure Analysis Services supporta la connessione da Power BI Desktop, Excel, app personalizzate e strumenti basati su browser.

![Visualizzazioni di dati](./media/analysis-services-overview/aas-overview-visualization.png)


## <a name="secure"></a>Proteggere
#### <a name="user-authentication"></a>Autenticazione utente
L'autenticazione utente per Azure Analysis Services viene gestita da [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). Quando si tenta di accedere a un database di Azure Analysis Services, gli utenti usano un'identità dell'account dell'organizzazione con accesso al database a cui si sta tentando di accedere. Queste identità utente devono essere membri dell'istanza predefinita di Azure Active Directory per la sottoscrizione in cui risiede il server Azure Analysis Services. L'[integrazione di directory](https://technet.microsoft.com/library/jj573653.aspx) tra AAD e l'istanza locale di Active Directory è un ottimo modo per concedere agli utenti l'accesso a un database di Azure Analysis Services, ma non è necessaria per tutti gli scenari.

Gli utenti accedono con il nome dell'entità utente del proprio account e la password. Dopo la sincronizzazione con un'istanza locale di Active Directory, il nome dell'entità utente corrisponde in genere all'indirizzo di posta elettronica aziendale.

Le autorizzazioni per la gestione delle risorse del server Azure Analysis Services sono gestite tramite l'assegnazione di utenti ai ruoli nell'ambito della sottoscrizione di Azure. Per impostazione predefinita, gli amministratori della sottoscrizione dispongono di autorizzazioni proprietario per l'accesso alla risorsa del server in Azure. È possibile aggiungere altri utenti tramite Azure Resource Manager.

#### <a name="data-security"></a>Sicurezza dei dati
Azure Analysis Services usa l'archiviazione BLOB di Azure per rendere persistenti i metadati e l'archiviazione per i database di Analysis Services. I file di dati nel BLOB vengono crittografati mediante crittografia sul lato server di BLOB di Azure. Quando si usa la modalità di query diretta, vengono archiviati solo i metadati. L'accesso ai dati effettivi viene eseguito dall'origine dati in fase di query.

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

In caso di suggerimenti sulla documentazione, è possibile aggiungere commenti usando Livefyre nella parte inferiore di ogni articolo.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver ottenuto alcune informazioni su Azure Analysis Services, è possibile iniziare a usarlo. Informazioni su come [creare un server](analysis-services-create-server.md) in Azure e [distribuire un modello tabulare](analysis-services-deploy.md) in tale server.


