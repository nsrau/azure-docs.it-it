---
title: Informazioni tecniche per un'offerta di app Power BI | Azure Marketplace
description: Configurare i campi delle informazioni tecniche per un'offerta di app Power BI per Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: b012ac012c7de3c6428ecbe2d2b922a2a9aa0523
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141784"
---
# <a name="power-bi-apps-technical-info-tab"></a>Scheda Technical Info (Informazioni tecniche) delle app Power BI

>[!Important]
>A partire dal 13 aprile 2020, si inizierà a trasferire la gestione delle offerte di Power BI App al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Per gestire le offerte migrate, seguire le istruzioni riportate in [Power bi panoramica sulla creazione di app](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) .

Nella pagina **nuova offerta** usare la scheda **informazioni tecniche** per fornire l'URL del pacchetto del programma di installazione di Power bi e altre informazioni necessarie per convalidare la nuova offerta.  Per la versione iniziale, tutte le app Power BI sono gratuite e sono disponibili per il download da AppSource. Per questo motivo, non è possibile definire le unità di mantenimento delle scorte (SKU) per questo tipo di offerta.

![Scheda informazioni tecniche](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Campi delle informazioni tecniche 

Nella scheda **Technical info (informazioni tecniche** ) completare i campi descritti nella tabella seguente. Un asterisco (*) alla fine di un'etichetta di campo significa che il campo è obbligatorio.

|        Campo          |  Descrizione                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL del programma di installazione\***     | Power BI genera questo URL quando si pubblica l'app e la si innalza di livello alla produzione.  Per altre informazioni, vedere [pubblicare app con dashboard e report in Power bi](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Validation instructions** (Istruzioni di convalida)  |  Se lo si desidera, aggiungere istruzioni (fino a 3.000 caratteri) per consentire al team di convalida Microsoft di configurare, connettere e testare l'app. Includere le impostazioni di configurazione, gli account, i parametri o altre informazioni tipiche che è possibile utilizzare per testare l'opzione Connetti dati. Queste informazioni sono visibili solo al team di convalida e vengono usate solo a scopo di convalida.  |
| **Is this app created as a Power BI content pack?** (L'app viene creata come pacchetto di contenuto Power BI?) | Attualmente, questo campo viene usato solo internamente. Lasciare l'impostazione predefinita **No**. Se si modifica l'impostazione impostandola su **Sì**, è possibile arrestare il processo di pubblicazione.  |  
|  |  |


## <a name="next-steps"></a>Passaggi successivi

Nella scheda [Dettagli storefront](./cpp-storefront-details-tab.md) fornire informazioni di marketing e legali per l'app.

