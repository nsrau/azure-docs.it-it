---
title: Informazioni tecniche per un'offerta di Power BI App Azure Marketplace
description: Configurare i campi delle informazioni tecniche per un'offerta di app Power BI per Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d814ebc34193f5d7c0c3828d32aa3d2af29f3679
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981359"
---
# <a name="power-bi-apps-technical-info-tab"></a>Scheda Technical Info (Informazioni tecniche) delle app Power BI

>[!Important]
>A partire dal 13 aprile 2020, inizieremo a spostare la gestione delle offerte dell'app Power BI nel Centro per i partner. Dopo la migrazione, potrai creare e gestire le tue offerte nel Centro per i partner. Seguire le istruzioni in [Panoramica](https://aka.ms/AzureCreatePBIServiceApp) sulla creazione di app Power BI per gestire le offerte migrate.

Nella pagina **Nuova offerta** usare la scheda **Informazioni tecniche** per fornire l'URL del pacchetto di installazione di Power BI e altre informazioni necessarie per convalidare la nuova offerta.  For the initial release, all Power BI apps are free and are available for download from AppSource. Per questo motivo, non è possibile definire unità di conservazione delle scorte (SKU) per questo tipo di offerta.

![La scheda Informazioni tecniche](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Campi delle informazioni tecniche 

Nella scheda **Informazioni tecniche** completare i campi descritti nella tabella seguente. Alla fine dell'etichetta di un campo, l'asterisco alla fine dell'etichetta di un campo indica che il campo è obbligatorio.

|        Campo          |  Descrizione                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL del programma di installazione\***     | Power BI genera questo URL quando si pubblica l'app e la promuove all'ambiente di produzione.  Per altre informazioni, vedere [Pubblicare app con dashboard e report in Power BI.](https://docs.microsoft.com/power-bi/service-create-distribute-apps)  |
|  **Validation instructions** (Istruzioni di convalida)  |  Se lo desideri, aggiungi istruzioni (fino a 3.000 caratteri) per consentire al team di convalida Microsoft di configurare, connettere e testare l'app. Includere impostazioni di configurazione, account, parametri o altre informazioni tipiche che possono essere utilizzate per testare l'opzione Connetti dati. Queste informazioni sono visibili solo al team di convalida e vengono utilizzate solo a scopo di convalida.  |
| **Is this app created as a Power BI content pack?** (L'app viene creata come pacchetto di contenuto Power BI?) | Attualmente, questo campo viene utilizzato solo internamente. Lasciare l'impostazione predefinita **No**. Se si modifica l'impostazione su **Sì**, è possibile interrompere il processo di pubblicazione.  |  
|  |  |


## <a name="next-steps"></a>Passaggi successivi

Nella scheda [Dettagli Vetrina](./cpp-storefront-details-tab.md) fornire informazioni legali e di marketing per l'app.

