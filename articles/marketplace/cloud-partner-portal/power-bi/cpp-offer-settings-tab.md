---
title: Impostazioni per un'offerta di App di Power BI dell'offerta | Azure Marketplace
description: Configurare le impostazioni di offerta per un'offerta di App di Power BI per il marketplace Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: be2c2b4f5d9461aa0fdc6dde89931ed4b6418ced
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943431"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Scheda Offer Settings (Impostazioni dell'offerta) per app Power BI

Quando si apre la **offrono nuove** pagina del servizio App, controllare prima i **impostazioni dell'offerta** scheda. In questa scheda è fornire gli identificatori primari e il nome dell'offerta. Un asterisco (*) indica un campo obbligatorio.

![Scheda Impostazioni dell'offerta](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Campi di impostazioni dell'offerta 

Nel **impostazioni dell'offerta** scheda, è necessario immettere le informazioni nei campi obbligatori seguenti. I campi obbligatori sono indicati da un asterisco (*).

|  Campo        |  DESCRIZIONE                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID offerta\***  | Identificatore univoco dell'offerta (in un profilo di pubblicazione). Questo ID è visibile negli URL dei prodotti, nei modelli di Azure Resource Manager e nei report di fatturazione. La lunghezza massima consentita è di 50 caratteri. Può contenere solo caratteri alfanumerici minuscoli e trattini (-). Non può terminare con un trattino. Questo identificatore non può essere modificato dopo la pubblicazione dell'offerta. Se Contoso pubblica un'offerta con ID offerta `sample-SvcApp`, l'offerta viene assegnato l'URL di AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Publisher\*** | Identificatore univoco dell'organizzazione in [AppSource](https://appsource.microsoft.com). È consigliabile associare a tutte le proprie offerte il proprio ID editore. Questo valore non può essere modificato dopo che l'offerta è stata salvata.                         |
| **Nome\***      | Nome visualizzato dell'offerta. Questo nome verrà visualizzato in AppSource e nel portale Cloud Partner. La lunghezza massima consentita è di 50 caratteri. Usare un nome di marchio riconoscibile per il prodotto. Non includere il nome dell'organizzazione in questo caso, a meno che l'app viene distribuito con lo stesso nome. Se si esegue questa offerta su altri siti Web e le pubblicazioni, utilizzare lo stesso nome in tutte le pubblicazioni.    <br/>Se si rilascia un'offerta durante il periodo di anteprima per le app Power BI, aggiungere la stringa `(Preview)` alla fine del nome dell'applicazione, simile al seguente: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Passaggi successivi

Nella scheda successiva, si specificherà [informazioni tecniche](./cpp-technical-info-tab.md) dell'offerta.
