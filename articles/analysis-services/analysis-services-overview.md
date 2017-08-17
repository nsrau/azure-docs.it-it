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
ms.date: 08/01/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 94d228f38a760b40f1acb4685702e6244f03bb5d
ms.contentlocale: it-it
ms.lasthandoff: 08/05/2017

---
# <a name="what-is-azure-analysis-services"></a>Informazioni su Azure Analysis Services
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services fornisce funzionalità di modellazione di dati di livello aziendale sul cloud. È una piattaforma distribuita come servizio (PaaS) completamente gestita, integrata con i servizi della piattaforma dati di Azure. 

Analysis Services consente di eseguire il mashup e di combinare dati da più origini, definire metriche e proteggere i dati in un singolo modello semantico di dati attendibile. Il modello di dati consente agli utenti di esplorare modo più semplice e rapido quantità elevate di dati con applicazioni client come Power BI, Excel, Reporting Services, app di terze parti e personalizzate.

![Origini dati](./media/analysis-services-overview/aas-overview-data-sources.png)

Guardare [questo video](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4) per informazioni su come Azure Analysis Services si integra con le funzionalità BI generali di Microsoft e su come è possibile trarre vantaggio dai propri modelli di dati nel cloud.

## <a name="built-on-sql-server-analysis-services"></a>Compilare in SQL Server Analysis Services
Azure Analysis Services è compatibile con molte funzionalità avanzate già disponibili in SQL Server Analysis Services Enterprise Edition. Azure Analysis Services supporta i modelli tabulari ai [livelli di compatibilità](https://docs.microsoft.com/sql/analysis-services/tabular-models/compatibility-level-for-tabular-models-in-analysis-services) 1200 e 1400. Sono supportate anche partizioni, sicurezza a livello di riga, relazioni bidirezionali e traduzioni. Le modalità In-Memory e DirectQuery consentono query velocissime su set di dati complessi e di grandi dimensioni.

I modelli tabulari offrono uno sviluppo rapido e sono altamente personalizzabili. Per gli sviluppatori, i modelli tabulari includono il Modello a oggetti tabulare (TOM, Tabular Object Model) per descrivere i modelli a oggetti. Il modello a oggetti tabulare viene esposto in JSON tramite [TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) e nel linguaggio DDL (Data Definition Language) AMO tramite lo spazio dei nomi [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx).

## <a name="better-with-azure"></a>Ottimizzato per Azure
Azure Analysis Services si integra con molti servizi di Azure e permette di creare soluzioni di analisi avanzate. L'integrazione con [Azure Active Directory](../active-directory/active-directory-whatis.md) offe l'accesso ai dati essenziali sicuro e in base al ruolo. È possibile ottenere l'integrazione con le pipeline di [Azure Data Factory](../data-factory/data-factory-introduction.md) includendo un'attività che carica i dati nel modello. È possibile usare [Automazione di Azure](../automation/automation-intro.md) e [Funzioni di Azure](../azure-functions/functions-overview.md) per un'orchestrazione semplice di modelli tramite codice personalizzato.

## <a name="get-up-and-running-quickly"></a>Operazioni iniziali rapide
Nel portale di Azure è possibile [creare un server](analysis-services-create-server.md) in pochi minuti. Con i [modelli](../azure-resource-manager/resource-manager-create-first-template.md) di Azure Resource Manager e PowerShell è possibile effettuare il provisioning dei server tramite un modello dichiarativo. Con un singolo modello si possono distribuire più servizi e altri componenti di Azure, ad esempio gli account di archiviazione e Funzioni di Azure. 

Dopo avere creato un server, è possibile creare un modello tabulare direttamente nel portale di Azure. La nuova funzionalità [Finestra di progettazione Web (anteprima)](analysis-services-create-model-portal.md) consente di connettersi a un database SQL di Azure o a un'origine dati Azure SQL Data Warehouse oppure di importare un file con estensione pbix di Power BI Desktop. Le relazioni tra le tabelle vengono create automaticamente ed è possibile creare misure o modificare il file model.bim in formato JSON direttamente dal browser.

## <a name="scale-to-your-needs"></a>Ridimensionare in base alle esigenze specifiche
Azure Analysis Services è disponibile nei livelli Developer, Basic e Standard. Entro ogni livello, i costi del piano dipendono da potenza di elaborazione, QPU e dimensioni della memoria. Quando si crea un server, si seleziona un piano entro un livello. È possibile passare a un piano inferiore o superiore entro lo stesso livello oppure passare a un livello superiore, ma non è possibile passare da un livello superiore a un livello inferiore.

È possibile aumentare o ridurre le prestazioni o sospendere il server. Usare il portale di Azure oppure usare PowerShell per ottenere subito il controllo totale. Si paga solo per le risorse utilizzate. Per altre informazioni sui diversi piani e livelli e per usare il calcolatore dei prezzi per determinare il piano ottimale, vedere [Prezzi di Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="keep-your-data-close"></a>Mantenere i dati nelle vicinanze
I server di Azure Analysis Services possono essere creati nelle [aree di Azure](https://azure.microsoft.com/regions/) seguenti:

| Americhe | Europa | Asia/Pacifico |
|----------|--------|--------------|
|  Brasile meridionale<br> Canada centrale<br> Stati Uniti orientali 2<br> Stati Uniti centro-settentrionali<br> Stati Uniti centro-meridionali<br> Stati Uniti centro-occidentali<br> Stati Uniti occidentali | Europa settentrionale<br> Regno Unito meridionale<br> Europa occidentale |   Australia sudorientale<br> Giappone orientale<br> Asia sudorientale<br> India occidentale  |

Vengono continuamente aggiunte nuove aree, quindi è possibile che questo elenco non sia completo. Scegliere una località in cui creare il server nel portale di Azure oppure usando i modelli di Azure Resource Manager. Per ottenere prestazioni ottimali, selezionare una località vicina alla base di utenti più estesa. È possibile assicurare la [disponibilità elevata](analysis-services-bcdr.md) distribuendo i modelli in server ridondanti in più aree.

## <a name="migrate-your-existing-tabular-models"></a>Eseguire la migrazione dei modelli tabulari esistenti
Se sono già presenti soluzioni di modelli locali di SQL Server Analysis Services, è possibile eseguire la migrazione in Azure Analysis Services senza modifiche significative. Per eseguire la migrazione, è possibile usare SSDT per distribuire il modello nel server. In alternativa, in SSMS è possibile usare il backup e ripristino o TMSL.

Se sono presenti origini dati locali, è necessario installare e configurare un [gateway dati locale](analysis-services-gateway.md). Se sono presenti ruoli e membri del ruolo già configurati, viene eseguita la migrazione dei ruoli ma sarà necessario aggiungere di nuovo i membri del ruolo tramite SSMS o PowerShell.

## <a name="connect-to-popular-data-sources"></a>Connettersi alle origini dati più diffuse
Azure Analysis Services supporta la [connessione a origini dati](analysis-services-datasource.md) locali nell'organizzazione e sul cloud. È possibile combinare i dati provenienti dall'ambiente locale e dalle origini dati cloud per una soluzione ibrida. 

I nuovi modelli tabulari 1400 usano la funzionalità Recupera dati moderna in SSDT, basata sul linguaggio di query con formula M. Recupera dati offre un numero superiore di funzionalità di trasformazione dei dati e di mashup e consente di creare e modificare query personalizzate del linguaggio con formula M. Ad esempio, con i modelli tabulari 1400 è possibile eseguire la modellazione sui file di dati nell'archivio BLOB di Azure.

## <a name="use-the-tools-you-already-know"></a>Usare gli strumenti noti

![Strumenti di sviluppo di Business Intelligence](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>SQL Server Data Tools (SSDT) per Visual Studio
È possibile sviluppare e distribuire i modelli usando gratuitamente [SQL Server Data Tools (SSDT) per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). SSDT include modelli di progetto di Analysis Services che consentono di iniziare subito a lavorare. SSDT include ora Recupera dati, una query dell'origine dati moderna, e funzionalità di mashup per modelli tabulari 1400. Gli utenti che hanno familiarità con Recupera dati in Power BI Desktop ed Excel 2016 sanno già quanto sia facile creare query dell'origine dati a personalizzazione elevata.

#### <a name="sql-server-management-studio"></a>SQL Server Management Studio
Gestire i server e i database modello usando [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) È possibile connettersi ai server sul cloud, eseguire gli script TMSL direttamente dalla finestra di query XMLA e automatizzare le attività usando gli script TMSL. Le nuove funzionalità sono disponibili rapidamente, perché SSMS viene aggiornato ogni mese.

#### <a name="powershell"></a>PowerShell
Le attività di gestione delle risorse del server, ad esempio la creazione di server, la sospensione o la ripresa delle operazioni di un server oppure la modifica del livello di servizio, usano i cmdlet di Azure Resource Manager (AzureRM). Le altre attività per la gestione dei database, ad esempio l'aggiunta o la rimozione dei membri di un ruolo, l'elaborazione o l'esecuzione di script TMSL, usano cmdlet del modulo SqlServer. I moduli AzureRM e SQLServer sono disponibili in [PowerShell Gallery](https://www.powershellgallery.com/).


## <a name="your-data-is-secure"></a>I dati sono protetti
![Visualizzazioni di dati](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>Autenticazione
L'autenticazione utente per Azure Analysis Services viene gestita da [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). Quando si tenta di accedere a un database di Azure Analysis Services, gli utenti usano un'identità dell'account dell'organizzazione con accesso al database a cui si sta tentando di accedere. Queste identità utente devono essere membri dell'istanza predefinita di Azure Active Directory per la sottoscrizione in cui risiede il server Azure Analysis Services. Per altre informazioni, vedere [Autenticazione e autorizzazioni utente](analysis-services-manage-users.md).

#### <a name="data-security"></a>Sicurezza dei dati
Azure Analysis Services usa l'archiviazione BLOB di Azure per rendere persistenti i metadati e l'archiviazione per i database di Analysis Services. I file di dati nel BLOB vengono crittografati mediante crittografia sul lato server di BLOB di Azure. Quando si usa la modalità di query diretta, vengono archiviati solo i metadati. L'accesso ai dati effettivi viene eseguito dall'origine dati in fase di query.

#### <a name="on-premises-data-sources"></a>Origini dati locali
È possibile ottenere la sicurezza dell'accesso ai dati in locale all'interno dell'organizzazione installando e configurando un [gateway dati locale](analysis-services-gateway.md). I gateway forniscono accesso ai dati per le modalità DirectQuery e in memoria. Quando un modello di Azure Analysis Services si connette a un'origine dati locale, viene creata una query con le credenziali crittografate per l'origine dati locale. Il servizio cloud del gateway analizza la query e inserisce la richiesta in un'istanza del bus di servizio di Azure. Il gateway locale esegue il polling del bus di servizio per le richieste in sospeso. Il gateway riceve quindi la query, decrittografa le credenziali e si connette all'origine dati per l'esecuzione. I risultati vengono inviati dall'origine dati al gateway e quindi al database Azure Analysis Services.

Azure Analysis Services è disciplinato dalle [Condizioni di Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e dall'[Informativa sulla privacy di Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Per altre informazioni sulla sicurezza di Azure, vedere [Microsoft Trust Center](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="supports-the-latest-client-tools"></a>Supporto degli strumenti client più recenti
![Visualizzazioni di dati](./media/analysis-services-overview/aas-overview-clients.png)

Gli strumenti moderni per l'esplorazione e la visualizzazione dei dati, come Power BI, Excel e strumenti di terze parti, forniscono agli utenti informazioni dettagliate a interattività elevata e visivamente accattivanti nei dati del modello.

I client usano le [librerie client](analysis-services-data-providers.md) MSOLAP, AMO o ADOMD per la connessione ai server di Analysis Services. Le applicazioni client di Microsoft, come ad esempio Power BI Desktop ed Excel, installano tutte e tre le librerie client. A seconda della versione o della frequenza degli aggiornamenti, le librerie client potrebbero tuttavia non essere le versioni più recenti richieste da Azure Analysis Services. Lo stesso vale per le applicazioni personalizzate o per le altre interfacce come AsCmd, TOM e ADOMD.NET. Queste applicazioni richiedono in genere l'installazione manuale delle librerie come parte di un pacchetto.


## <a name="get-help"></a>Ottenere aiuto

#### <a name="documentation"></a>Documentazione
La gestione e la configurazione di Azure Analysis Services sono semplici. In quest'area è possibile trovare tutte le informazioni necessarie per creare e gestire i servizi del server. La creazione di un modello dati per la distribuzione al server è analoga alla creazione di un modello dati distribuiti a un server locale. È disponibile una ricca libreria concettuale, procedurale e di esercitazioni e articoli di riferimento in [Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services).

#### <a name="videos"></a>Video
In [Azure Analysis Services su Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services) sono disponibili alcuni video utili.

#### <a name="blogs"></a>Blog
Per rimanere sempre aggiornati sulle continue modifiche, è possibile consultare il [blog di Azure Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/) e il [blog di Azure](https://azure.microsoft.com/blog/).

#### <a name="community"></a>Community
Analysis Services è costituito da una vivace community di utenti. È possibile partecipare alle discussioni sul [forum di Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Commenti e suggerimenti
In caso di suggerimenti o richieste di funzionalità, assicurarsi di lasciare i propri commenti in [Commenti e suggerimenti su Azure Analysis Services](https://aka.ms/azureanalysisservicesfeedback).

In caso di suggerimenti sulla documentazione, è possibile aggiungere commenti usando Livefyre nella parte inferiore di ogni articolo.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver ottenuto alcune informazioni su Azure Analysis Services, è possibile iniziare a usarlo. Altre informazioni su come [creare un server](analysis-services-create-server.md) in Azure. Quando il server è pronto, vedere l'[esercitazione di Adventure Works](tutorials/aas-adventure-works-tutorial.md) per informazioni su come creare un modello tabulare completamente funzionale e distribuirlo nel server.

