---
title: Informazioni tecniche per un'offerta di app Power BI | Azure Marketplace
description: Configurare i campi delle informazioni tecniche per un'offerta di app Power BI per Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 02c98e38f26fff73d02dc4c2b838ad3428cd2996
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819038"
---
# <a name="power-bi-apps-technical-info-tab"></a>Scheda Technical Info (Informazioni tecniche) delle app Power BI

Nella pagina **nuova offerta** usare la scheda **informazioni tecniche** per fornire l'URL del pacchetto del programma di installazione di Power bi e altre informazioni necessarie per convalidare la nuova offerta.  Per la versione iniziale, tutte le app Power BI sono gratuite e sono disponibili per il download da AppSource. Per questo motivo, non è possibile definire le unità di mantenimento delle scorte (SKU) per questo tipo di offerta.

![Scheda informazioni tecniche](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Campi delle informazioni tecniche 

Nella scheda **Technical info (informazioni tecniche** ) completare i campi descritti nella tabella seguente. Un asterisco (*) alla fine di un'etichetta di campo significa che il campo è obbligatorio.

|        Campo          |  Descrizione                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **\* URL del programma di installazione**     | Power BI genera questo URL quando si pubblica l'app e la si innalza di livello alla produzione.  Per altre informazioni, vedere [pubblicare app con dashboard e report in Power bi](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Validation instructions** (Istruzioni di convalida)  |  Se lo si desidera, aggiungere istruzioni (fino a 3.000 caratteri) per consentire al team di convalida Microsoft di configurare, connettere e testare l'app. Includere le impostazioni di configurazione, gli account, i parametri o altre informazioni tipiche che è possibile utilizzare per testare l'opzione Connetti dati. Queste informazioni sono visibili solo al team di convalida e vengono usate solo a scopo di convalida.  |
| **Is this app created as a Power BI content pack?** (L'app viene creata come pacchetto di contenuto Power BI?) | Attualmente, questo campo viene usato solo internamente. Lasciare l'impostazione predefinita **No**. Se si modifica l'impostazione impostandola su **Sì**, è possibile arrestare il processo di pubblicazione.  |  
|  |  |


## <a name="next-steps"></a>Passaggi successivi

Nella scheda [Dettagli storefront](./cpp-storefront-details-tab.md) fornire informazioni di marketing e legali per l'app.

