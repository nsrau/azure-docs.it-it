---
title: Librerie client di Azure Analysis Services - Documenti Microsoft
description: Vengono illustrate le librerie client necessarie per la connessione di applicazioni e strumenti client ad Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: be6ee594896384bce86d892c9a7811e09a0f4efd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676572"
---
# <a name="client-libraries-for-connecting-to-analysis-services"></a>Client libraries for connecting to Analysis Services

Le librerie client sono necessarie per la connessione di applicazioni e strumenti client ai server di Analysis Services. Le applicazioni client Microsoft come Power BI Desktop, Excel, SQL Server Management Studio (SSMS) e Analysis Services Analysis Services sono estensioni per i progetti di Visual Studio, installare tutte e tre le librerie client e aggiornarle insieme ai normali aggiornamenti delle applicazioni. In alcuni casi, potrebbe essere necessario installare le versioni più recenti delle librerie client. Le applicazioni client personalizzate richiedono inoltre l'installazione di librerie client.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Scaricare le librerie client più recenti (Windows Installer)  

|Download  |Versione del prodotto  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.36.38    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.36.38       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.6.0.1    |
|[Adomd](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.6.0.1     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO e ADOMD (pacchetti NuGet)

Le librerie client AMO (Analysis Services Management Objects) e ADOMD sono disponibili come pacchetti installabili da [NuGet.org](https://www.nuget.org/). Si consiglia di eseguire la migrazione ai riferimenti NuGet anziché utilizzare Windows Installer. 

|Pacchetto  | Versione del prodotto  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.6.0.1     |
|[Adomd](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.6.0.1      |

Gli assembly AssemblyVersion del pacchetto NuGet sono conformi al controllo delle versioni semantici: PRINCIPALE.SECONDARIA.PATCH. I riferimenti NuGet caricano la versione prevista anche se nella Global Assembly Cache è presente una versione diversa (risultante dall'installazione con file MSI). Il valore di PATCH viene incrementato a ogni versione. Le versioni di AMO e ADOMD vengono mantenute sincronizzate.

## <a name="understanding-client-libraries"></a>Informazioni sulle librerie client

Analysis Services usa tre librerie client, definite anche provider di dati. ADOMD.NET e Analysis Services Management Objects (AMO) sono librerie client gestite. Il provider OLE DB Analysis Services (MSOLAP DLL) è una libreria client nativa. In genere, vengono installate tutte e tre nello stesso momento. **Azure Analysis Services richiede le versioni più recenti di tutte e tre le librerie**. 

Le applicazioni client di Microsoft, come ad esempio Power BI Desktop ed Excel, installano tutte e tre le librerie client e le aggiornano quando sono disponibili nuove versioni. A seconda della versione o della frequenza degli aggiornamenti, alcune librerie client potrebbero non essere le versioni più recenti richieste da Azure Analysis Services. Lo stesso vale per le applicazioni personalizzate o per le altre interfacce come AsCmd, TOM e ADOMD.NET. Queste applicazioni richiedono l'installazione manuale o a livello di codice delle librerie. Le librerie client per l'installazione manuale sono incluse nei Feature Pack di SQL Server come pacchetti distribuibili. Queste librerie client sono tuttavia collegate alla versione di SQL Server e potrebbero non essere le più recenti.  

Le librerie client per le connessioni client sono diverse dai provider di dati necessari per connettersi da un server di Azure Analysis Services a un'origine dati. Per ulteriori informazioni sulle connessioni alle origini dati, vedere [Connessioni alle origini dati](analysis-services-datasource.md).

## <a name="client-library-types"></a>Tipi di librerie client

### <a name="analysis-services-ole-db-provider-msolap"></a>Provider OLE DB per Analysis Services (MSOLAP) 

 Il provider OLE DB Analysis Services (MSOLAP) è la libreria client nativa per le connessioni di database di Analysis Services. Viene usato in modo indiretto sia da ADOMD.NET che da AMO, delegando le richieste di connessione al provider di dati. È anche possibile chiamare il provider OLE DB direttamente dal codice dell'applicazione.  
  
 Il provider OLE DB Analysis Services viene installato automaticamente dalla maggior parte degli strumenti e delle applicazioni client usati per accedere ai database di Analysis Services. Deve essere installato nei computer usati per accedere ai dati di Analysis Services.  
  
 I provider OLE DB vengono spesso specificati nelle stringhe di connessione. Una stringa di connessione di Analysis Services usa una nomenclatura diversa per fare riferimento al provider OLE DB: MSOLAP.\<versione>.dll.

### <a name="amo"></a>AMO  

 AMO è una libreria client gestita usata per l'amministrazione di server e la definizione di dati. Viene installata e usata da strumenti e applicazioni client. SQL Server Management Studio (SSMS), ad esempio, usa AMO per connettersi ad Analysis Services. Una connessione che usa AMO è in genere minima, costituita da `"data source=\<servername>"`. Dopo che è stata stabilita una connessione, l'API consente di usare le raccolte di database e gli oggetti principali. Sia Visual Studio che SSMS utilizzano AMO per connettersi a un'istanza di Analysis ServicesAnalysis Services .  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET è una libreria client di dati gestiti usata per eseguire query sui dati di Analysis Services. Viene installata e usata da strumenti e applicazioni client. 
  
 Durante la connessione a un database, le proprietà della stringa di connessione per tutte e tre le librerie sono simili. Quasi tutte le stringhe di connessione definite per ADOMD.NET usando [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) funzionano anche per AMO e il provider OLE DB Analysis Services (MSOLAP). Per altre informazioni, vedere [Proprietà delle stringhe di connessione &#40;Analysis Services&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="how-to-determine-client-library-version"></a><a name="bkmk_LibUpdate"></a> Come determinare la versione della libreria client   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Passare a `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Se si ha più di una cartella, scegliere il numero più elevato.
  
2.  Fare clic con il pulsante destro**del** > mouse su **Msolap.dll** > **Properties Details**. Se il nome del file è msolap140.dll, si tratta di una versione precedente che deve essere aggiornata.
    
    ![Dettagli della libreria client](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Passare a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Se si ha più di una cartella, scegliere il numero più elevato.
2. Fare clic**con il** > pulsante destro del mouse su Dettagli proprietà **Microsoft.AnalysisServices** > **.**  

### <a name="adomd"></a>ADOMD

1. Passare a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Se si ha più di una cartella, scegliere il numero più elevato.
2. Fare clic**con** > il pulsante destro del mouse su **Microsoft.AnalysisServices.AdomdClient** > Properties**Details**.  


## <a name="next-steps"></a>Passaggi successivi
[Connettersi con Excel](analysis-services-connect-excel.md)    
[Connettersi con Power BI](analysis-services-connect-pbi.md)
