---
title: Informazioni tecniche per un'offerta di App di Power BI | Azure Marketplace
description: Configurare i campi delle informazioni tecniche per un'offerta di app Power BI per Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 15f4e2a76724a70c15411dea767cc9bc433e4d4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943226"
---
# <a name="power-bi-apps-technical-info-tab"></a>Scheda Technical Info (Informazioni tecniche) delle app Power BI

Nel **offrono nuove** pagina, utilizzare il **informazioni tecniche** pressione di tab per specificare il programma di installazione di Power BI, l'URL del pacchetto e altre informazioni che è necessario convalidare la nuova offerta.  Per il rilascio iniziale, tutte le app di Power BI sono gratuite e sono disponibili per il download da AppSource. Per questo motivo, è possibile definire le unità SKU (SKU) per questo tipo di offerta.

![La scheda di informazioni tecniche](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Campi delle informazioni tecniche 

Nel **informazioni tecniche** scheda, completare i campi descritti nella tabella seguente. Un asterisco (*) alla fine di un'etichetta di campo indica che il campo è obbligatorio.

|        Campo          |  Descrizione                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL del programma di installazione\***     | Power BI genera questo URL quando si pubblica l'app e promuoverlo alla produzione.  Per altre informazioni, vedere [pubblicare le app con dashboard e report in Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Validation instructions** (Istruzioni di convalida)  |  Se si desidera, aggiungere le istruzioni (fino a 3000 caratteri) per consentire al team di convalida di Microsoft di configurare, connettersi e testare l'app. Includono impostazioni di configurazione comuni, gli account, parametri o altre informazioni che possono essere usati per verificare l'opzione di connessione dati. Queste informazioni sono visibili solo al team di convalida, e viene usato solo per scopi di convalida.  |
| **Is this app created as a Power BI content pack?** (L'app viene creata come pacchetto di contenuto Power BI?) | Attualmente, questo campo viene usato solo internamente. Lasciare l'impostazione predefinita **No**. Se si modifica l'impostazione **Sì**, è possibile arrestare il processo di pubblicazione.  |  
|  |  |


## <a name="next-steps"></a>Passaggi successivi

Nel [dettagli vetrina](./cpp-storefront-details-tab.md) scheda, fornire le informazioni di marketing e legali per l'app.

