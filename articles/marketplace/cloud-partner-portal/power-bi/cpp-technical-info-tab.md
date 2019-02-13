---
title: Informazioni tecniche per un'offerta di app Power BI - Azure Marketplace | Microsoft Docs
description: Configurare i campi delle informazioni tecniche per un'offerta di app Power BI per Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: d96ef2fd318d6164e1b7dfc5c4b72d6957af0f3e
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744435"
---
# <a name="power-bi-apps-technical-info-tab"></a>Scheda Technical Info (Informazioni tecniche) delle app Power BI

Nella scheda **Technical Info** (Informazioni tecniche) della pagina **New Offer** (Nuova offerta) è possibile specificare l'URL del pacchetto del programma di installazione Power BI e le eventuali informazioni aggiuntive necessarie per la convalida della nuova offerta.  Per la versione iniziale, tutte le app Power BI sono gratuite e quindi disponibili per il download da AppSource senza costi aggiuntivi. Non sarà pertanto possibile definire codici di riferimento del prodotto (SKU) per questo tipo di offerta.

![Scheda Technical Info (Informazioni tecniche)](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Campi delle informazioni tecniche 

Nella scheda **Technical Info** (Informazioni tecniche) è necessario completare i campi seguenti.  Un asterisco (*) accanto all'etichetta del campo indica che si tratta di un campo obbligatorio.

|        Campo          |  Descrizione                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **Installer URL** (URL programma di installazione)     | Indirizzo generato da Power BI quando l'app viene pubblicata e passa alla fase di produzione.  Per altre informazioni su come generare l'URL, vedere [Pubblicare app di servizio in Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Validation instructions** (Istruzioni di convalida)  |  Istruzioni facoltative in formato testo (fino a un massimo di 3000 caratteri) che possono facilitare il team di convalida Microsoft nelle operazioni di configurazione, connessione e testing dell'app, ovvero impostazioni di configurazione tipiche, account di test o parametri utilizzabili per testare l'opzione "Connetti dati" e così via. Queste informazioni saranno visibili solo al team di convalida e vengono usate solo a scopo di convalida.  |
| **Is this app created as a Power BI content pack?** (L'app viene creata come pacchetto di contenuto Power BI?) | Per il momento questo è un campo usato internamente. Lasciarlo impostato sul valore predefinito, ovvero `No`. Impostandolo su `Yes` si potrebbe impedire la pubblicazione.  |  
|  |  |


## <a name="next-steps"></a>Passaggi successivi

Nella scheda successiva [Storefront Details](./cpp-storefront-details-tab.md) (Dettagli vetrina) si specificheranno le informazioni di marketing e di carattere legale per l'app.

