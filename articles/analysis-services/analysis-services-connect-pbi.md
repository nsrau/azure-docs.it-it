---
title: Connettersi ad Azure Analysis Services con Power BI | Microsoft Docs
description: Informazioni su come connettersi a un server di Azure Analysis Services usando Power BI. Una volta connessi, gli utenti possono esplorare i dati del modello.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ed0f3d379ca961c610958c073c27c6fc9583252b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500921"
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

4. Selezionare un'opzione di connessione e quindi premere **Connetti**. 

    Sono supportate entrambe le opzioni **Connessione dinamica** e **Importa**. È tuttavia consigliabile usare connessioni dinamiche, perché la modalità Importa presenta alcune limitazioni; in particolare, l'importazione potrebbe influire negativamente sulle prestazioni del server. Se il modello deve essere aggiornato nel servizio Power BI, poi, l'impostazione **Consenti l'accesso da Power BI** si applica solo se si sceglie **Connessione dinamica**.

5. Immettere le credenziali di accesso, se richieste. 

   > [!NOTE]
   > Gli account di accesso monouso (OTP) non sono supportati. 

6. In **Strumento di navigazione** espandere il server, selezionare il modello o la prospettiva a cui connettersi e quindi fare clic su **Connetti**. Fare clic su un modello o su una prospettiva per visualizzare tutti gli oggetti per la vista selezionata.

    Il modello viene aperto in Power BI Desktop con un report vuoto nella vista Report. L'elenco Campi consente di visualizzare tutti gli oggetti modello non nascosti. Lo stato della connessione viene visualizzato nell'angolo in basso a destra.

## <a name="connect-in-power-bi-service"></a>Connettersi in Power BI (servizio)

1. Creare un file di Power BI Desktop che dispone di una connessione dinamica al modello sul server.
2. In [Power BI](https://powerbi.microsoft.com) fare clic su **Recupera dati** > **File** e quindi individuare e selezionare il file con estensione pbix.

## <a name="see-also"></a>Vedi anche
[Connetti a Azure Analysis Services](analysis-services-connect.md)   
[Librerie client](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)