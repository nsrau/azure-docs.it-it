---
title: Impostazioni per un'offerta di app Power BI - Azure Marketplace | Microsoft Docs
description: Configurare le impostazioni di offerta per un'offerta di App di Power BI per il marketplace Microsoft AppSource.
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
ms.openlocfilehash: 49bbe5dcf17a9aa20cb47f477c59e7115d29dacc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60727940"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Scheda Offer Settings (Impostazioni dell'offerta) per app Power BI

Quando si apre la **offrono nuove** pagina del servizio App, controllare prima i **impostazioni dell'offerta** scheda. In questa scheda è fornire gli identificatori primari e il nome dell'offerta. Un asterisco (*) indica un campo obbligatorio.

![Scheda Impostazioni dell'offerta](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Campi di impostazioni dell'offerta 

Nel **impostazioni dell'offerta** scheda, è necessario immettere le informazioni nei campi obbligatori seguenti:

|  Campo        |  DESCRIZIONE                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID offerta**  | Identificatore univoco dell'offerta (in un profilo di pubblicazione). Questo ID è visibile negli URL dei prodotti, nei modelli di Azure Resource Manager e nei report di fatturazione. La lunghezza massima consentita è di 50 caratteri. Può contenere solo caratteri alfanumerici minuscoli e trattini (-). Non può terminare con un trattino. Questo identificatore non può essere modificato dopo la pubblicazione dell'offerta. Se Contoso pubblica un'offerta con ID offerta `sample-SvcApp`, l'offerta viene assegnato l'URL di AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Autore** | Identificatore univoco dell'organizzazione in [AppSource](https://appsource.microsoft.com). È consigliabile associare a tutte le proprie offerte il proprio ID editore. Questo valore non può essere modificato dopo che l'offerta è stata salvata.                         |
| **Nome**      | Nome visualizzato dell'offerta. Questo nome verrà visualizzato in AppSource e nel portale Cloud Partner. La lunghezza massima consentita è di 50 caratteri. Usare un nome di marchio riconoscibile per il prodotto. Non includere il nome dell'organizzazione in questo caso, a meno che l'app viene distribuito con lo stesso nome. Se si esegue questa offerta su altri siti Web e le pubblicazioni, utilizzare lo stesso nome in tutte le pubblicazioni.    <br/>Se si rilascia un'offerta durante il periodo di anteprima per le app Power BI, aggiungere la stringa `(Preview)` alla fine del nome dell'applicazione, simile al seguente: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Passaggi successivi

Nella scheda successiva, si specificherà [informazioni tecniche](./cpp-technical-info-tab.md) dell'offerta.
