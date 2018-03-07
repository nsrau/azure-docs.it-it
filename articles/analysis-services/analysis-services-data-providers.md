---
title: Librerie client necessarie per la connessione ad Azure Analysis Services | Microsoft Docs
description: Vengono illustrate le librerie client necessarie per la connessione di applicazioni e strumenti client ad Azure Analysis Services
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2018
ms.author: owend
ms.openlocfilehash: 5c847f5cd02503b708db8a0a0211b5d403df0943
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Librerie client per la connessione ad Azure Analysis Services

Le librerie client sono necessarie per la connessione di applicazioni e strumenti client ai server di Analysis Services. 

## <a name="download-the-latest-client-libraries-windows-installer"></a>Scaricare le librerie client più recenti (Windows Installer)  

|Download  |Versione prodotto  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.0.1.208      |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    15.0.1.208      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   15.0.2     |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    15.0.2     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO e ADOMD (pacchetti NuGet)

Le librerie client Analysis Services Management Objects (AMO) e ADOMD sono disponibili come pacchetti installabili in [NuGet.org](https://www.nuget.org/). È consigliabile eseguire la migrazione ai riferimenti NuGet anziché usare Windows Installer. 

|Pacchetto  | Versione prodotto  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    15.0.2.0      |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   15.0.2.0      |

Gli assembly AssemblyVersion del pacchetto NuGet sono conformi al controllo delle versioni semantici: PRINCIPALE.SECONDARIA.PATCH. I riferimenti NuGet caricano la versione prevista anche se nella Global Assembly Cache è presente una versione diversa (risultante dall'installazione con file MSI). Il valore di PATCH viene incrementato a ogni versione. Le versioni di AMO e ADOMD vengono mantenute sincronizzate.

## <a name="understanding-client-libraries"></a>Informazioni sulle librerie client

Analysis Services usa tre librerie client, definite anche provider di dati. ADOMD.NET e Analysis Services Management Objects (AMO) sono librerie client gestite. Il provider OLE DB Analysis Services (MSOLAP DLL) è una libreria client nativa. In genere, vengono installate tutte e tre nello stesso momento. **Azure Analysis Services richiede le versioni più recenti di tutte e tre le librerie**. 

Le applicazioni client di Microsoft, come ad esempio Power BI Desktop ed Excel, installano tutte e tre le librerie client e le aggiornano quando sono disponibili nuove versioni. A seconda della versione o della frequenza degli aggiornamenti, alcune librerie client potrebbero non essere le versioni più recenti richieste da Azure Analysis Services. Lo stesso vale per le applicazioni personalizzate o per le altre interfacce come AsCmd, TOM e ADOMD.NET. Queste applicazioni richiedono l'installazione manuale o a livello di codice delle librerie. Le librerie client per l'installazione manuale sono incluse nei Feature Pack di SQL Server come pacchetti distribuibili. Queste librerie client sono tuttavia collegate alla versione di SQL Server e potrebbero non essere le più recenti.  

Le librerie client per le connessioni client sono diverse dai provider di dati necessari per connettersi da un server di Azure Analysis Services a un'origine dati. Per ulteriori informazioni sulle connessioni alle origini dati, vedere [Connessioni alle origini dati](analysis-services-datasource.md).

## <a name="client-library-types"></a>Tipi di librerie client

### <a name="analysis-services-ole-db-provider-msolap"></a>Provider OLE DB Analysis Services (MSOLAP) 

 Il provider OLE DB Analysis Services (MSOLAP) è la libreria client nativa per le connessioni di database di Analysis Services. Viene usato in modo indiretto sia da ADOMD.NET che da AMO, delegando le richieste di connessione al provider di dati. È anche possibile chiamare il provider OLE DB direttamente dal codice dell'applicazione.  
  
 Il provider OLE DB Analysis Services viene installato automaticamente dalla maggior parte degli strumenti e delle applicazioni client usati per accedere ai database di Analysis Services. Deve essere installato nei computer usati per accedere ai dati di Analysis Services.  
  
 I provider OLE DB vengono spesso specificati nelle stringhe di connessione. Una stringa di connessione di Analysis Services usa una nomenclatura diversa per fare riferimento al provider OLE DB: MSOLAP.\<versione>.dll.

### <a name="amo"></a>AMO  

 AMO è una libreria client gestita usata per l'amministrazione di server e la definizione di dati. Viene installata e usata da strumenti e applicazioni client. SQL Server Management Studio (SSMS), ad esempio, usa AMO per connettersi ad Analysis Services. Una connessione che usa AMO è in genere minima, costituita da `“data source=\<servername>”`. Dopo che è stata stabilita una connessione, l'API consente di usare le raccolte di database e gli oggetti principali. Sia SSDT che SSMS usano AMO per connettersi a un'istanza di Analysis Services.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET è una libreria client di dati gestiti usata per eseguire query sui dati di Analysis Services. Viene installata e usata da strumenti e applicazioni client. 
  
 Durante la connessione a un database, le proprietà della stringa di connessione per tutte e tre le librerie sono simili. Quasi tutte le stringhe di connessione definite per ADOMD.NET usando [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](https://msdn.microsoft.com/library/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring.aspx) funzionano anche per AMO e il provider OLE DB Analysis Services (MSOLAP). Per altre informazioni, vedere [Proprietà delle stringhe di connessione &#40;Analysis Services&#41;](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a> Come determinare la versione della libreria client   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Passare a C:\Programmi\Microsoft Analysis Services\AS OLEDB\. Se si ha più di una cartella, scegliere il numero più elevato.
  
2.  Fare clic con il pulsante destro del mouse su **msolap.dll** > **Proprietà** > **Dettagli**. Se il nome del file è msolap140.dll, si tratta di una versione precedente che deve essere aggiornata.
    
    ![Dettagli della libreria client](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Passare a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Se si ha più di una cartella, scegliere il numero più elevato.
2. Fare clic con il pulsante destro del mouse **Microsoft.AnalysisServices** > **Proprietà** > **Dettagli**.  

### <a name="adomd"></a>ADOMD

1. Passare a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Se si ha più di una cartella, scegliere il numero più elevato.
2. Fare clic con il pulsante destro del mouse **Microsoft.AnalysisServices.AdomdClient** > **Proprietà** > **Dettagli**.  


## <a name="next-steps"></a>Passaggi successivi
[Connettersi con Excel](analysis-services-connect-excel.md)    
[Stabilire la connessione con Power BI](analysis-services-connect-pbi.md)
