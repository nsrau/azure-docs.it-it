---
title: Impostazioni dell'offerta per un'offerta di Power BI App - Azure Marketplace
description: Configurare le impostazioni dell'offerta per un'offerta power BI App per il marketplace Di Microsoft AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: f3a8d740d391edc09a290d3dba4307af7eec00b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286354"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Scheda Offer Settings (Impostazioni dell'offerta) per app Power BI

Quando apri la pagina **Nuova offerta** per le app di servizio, viene visualizzata la scheda **Impostazioni offerta.** Specificare gli identificatori primari e il nome dell'offerta in questa scheda. L'asterisco (-) indica un campo obbligatorio.

![Scheda Impostazioni dell'offerta](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Campi di impostazioni dell'offerta 

Nella scheda **Impostazioni offerta** è necessario immettere le informazioni nei seguenti campi obbligatori. I campi obbligatori sono indicati da un asterisco (*).

|  Campo        |  Descrizione                                                               |
|---------------|----------------------------------------------------------------------------|
| **Offer ID (ID offerta)\***  | Identificatore univoco dell'offerta (in un profilo di pubblicazione). Questo ID è visibile negli URL dei prodotti, nei modelli di Azure Resource Manager e nei report di fatturazione. La lunghezza massima consentita è di 50 caratteri. Può contenere solo caratteri alfanumerici minuscoli e trattini (-). Non può finire con un trattino. Questo identificatore non può essere modificato dopo la gestione di un'offerta. Se Contoso pubblica un'offerta `sample-SvcApp`con ID offerta, all'offerta viene assegnato l'URL `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`AppSource.      |
| **Editore\*** | Identificatore univoco dell'organizzazione in [AppSource](https://appsource.microsoft.com). È consigliabile associare a tutte le proprie offerte il proprio ID editore. Questo valore non può essere modificato dopo che l'offerta è stata salvata.                         |
| **Nome\***      | Un nome visualizzato per l'offerta. Questo nome verrà visualizzato in AppSource e nel portale Cloud Partner. La lunghezza massima consentita è di 50 caratteri. Usa un marchio riconoscibile per il tuo prodotto. Non includere il nome della tua organizzazione qui a meno che l'app non sia commercializzata con tale nome. Se stai fornendo questa offerta su altri siti web e pubblicazioni, usa lo stesso nome in tutte le pubblicazioni.    <br/>Se si rilascia un'offerta durante il periodo di `(Preview)` anteprima per le app Power BI, `Sample Scv App (Preview)`aggiungere la stringa alla fine del nome dell'applicazione, in questo modo: . |
|     |     |


## <a name="next-steps"></a>Passaggi successivi

Nella scheda successiva, specificherai le [informazioni tecniche](./cpp-technical-info-tab.md) per la tua offerta.
