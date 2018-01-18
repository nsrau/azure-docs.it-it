---
title: Connettersi ad Azure Analysis Services con Power BI | Microsoft Docs
description: Informazioni su come connettersi a un server di Azure Analysis Services usando Power BI.
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
ms.date: 01/10/2018
ms.author: owend
ms.openlocfilehash: ea1094d0ce858cd7df9c49f18fb81b07e31fca53
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="connect-with-power-bi"></a>Connettersi con Power BI

Dopo aver creato un server in Azure e aver distribuito un modello tabulare nel server, gli utenti dell'organizzazione sono pronti per connettersi e iniziare l'esplorazione dei dati. 

> [!TIP]
> Assicurarsi di usare la versione più recente di [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Connettersi in Power BI Desktop

1. In Power BI Desktop fare clic su **Recupera dati** > **Azure** > **Database di Azure Analysis Services**.

2. In **Server** immettere il nome del server. Includere l'URL completo, ad esempio asazure://westcentralus.asazure.windows.net/advworks.

3. In **Database**, se si conosce il nome della prospettiva o del database del modello tabulare a cui connettersi, incollarlo qui. In caso contrario, è possibile lasciare vuoto questo campo e selezionare un database o una prospettiva in un secondo momento.

4. Lasciare l'impostazione predefinita dell'opzione **Connessione dinamica** e quindi scegliere **Connetti**. Le connessioni di importazione non sono al momento supportate.

5. Immettere le credenziali di accesso, se richieste. 

6. In **Strumento di navigazione** espandere il server, selezionare il modello o la prospettiva a cui connettersi e quindi fare clic su **Connetti**. Fare clic su un modello o su una prospettiva per visualizzare tutti gli oggetti per la vista selezionata.

    Il modello viene aperto in Power BI Desktop con un report vuoto nella vista Report. L'elenco Campi consente di visualizzare tutti gli oggetti modello non nascosti. Lo stato della connessione viene visualizzato nell'angolo in basso a destra.

## <a name="connect-in-power-bi-service"></a>Connettersi in Power BI (servizio)

1. Creare un file di Power BI Desktop che dispone di una connessione dinamica al modello sul server.
2. In [Power BI](https://powerbi.microsoft.com) fare clic su **Recupera dati** > **File** e quindi individuare e selezionare il file con estensione pbix.



## <a name="see-also"></a>Vedere anche 
[Connettersi ad Azure Analysis Services](analysis-services-connect.md)   
[Librerie client](analysis-services-data-providers.md)

