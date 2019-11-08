---
title: Impostazioni dell'offerta per un'offerta di app Power BI | Azure Marketplace
description: Configurare le impostazioni dell'offerta per un'offerta di app Power BI per il Marketplace di Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: ca326b10a0707d5e4b1a5f05dccc303c9ec28269
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822670"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Scheda Offer Settings (Impostazioni dell'offerta) per app Power BI

Quando si apre la pagina **nuova offerta** per le app di servizio, viene visualizzata per prima cosa la scheda **impostazioni offerta** . Specificare gli identificatori primari e il nome dell'offerta in questa scheda. Un asterisco (*) indica un campo obbligatorio.

![Scheda Impostazioni dell'offerta](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Campi di impostazioni dell'offerta 

Nella scheda **impostazioni offerta** è necessario immettere le informazioni nei campi obbligatori seguenti. I campi obbligatori sono indicati da un asterisco (*).

|  Campo        |  Descrizione                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID offerta\***  | Identificatore univoco dell'offerta (in un profilo di pubblicazione). Questo ID è visibile negli URL dei prodotti, nei modelli di Azure Resource Manager e nei report di fatturazione. La lunghezza massima consentita è di 50 caratteri. Può contenere solo caratteri alfanumerici minuscoli e trattini (-). Non può terminare con un trattino. Questo identificatore non può essere modificato dopo il completamento di un'offerta. Se contoso pubblica un'offerta con ID offerta `sample-SvcApp`, all'offerta viene assegnato l'URL AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **\* editore** | Identificatore univoco dell'organizzazione in [AppSource](https://appsource.microsoft.com). È consigliabile associare a tutte le proprie offerte il proprio ID editore. Questo valore non può essere modificato dopo che l'offerta è stata salvata.                         |
| **Nome\***      | Nome visualizzato per l'offerta. Questo nome verrà visualizzato in AppSource e nella portale Cloud Partner. La lunghezza massima consentita è di 50 caratteri. Usare un nome di marchio riconoscibile per il prodotto. Non includere il nome dell'organizzazione, a meno che l'app non venga commercializzata con tale nome. Se si fornisce questa offerta ad altri siti Web e pubblicazioni, utilizzare lo stesso nome in tutte le pubblicazioni.    <br/>Se si rilascia un'offerta durante il periodo di anteprima per Power BI app, aggiungere la stringa `(Preview)` alla fine del nome dell'applicazione, come segue: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Passaggi successivi

Nella scheda successiva si specificheranno le [informazioni tecniche](./cpp-technical-info-tab.md) per l'offerta.
