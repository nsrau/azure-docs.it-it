---
title: Impostazioni per un'offerta di app Power BI - Azure Marketplace | Microsoft Docs
description: Configurare le impostazioni per un'offerta di app Power BI per Microsoft AppSource Marketplace.
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
ms.openlocfilehash: fef2455129d94913c5b51a08c04403834861bb48
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665812"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Scheda Offer Settings (Impostazioni dell'offerta) per app Power BI

La pagina **New Offer** (Nuova offerta) per le app di servizio viene visualizzata nella prima scheda denominata **Offer Settings** (Impostazioni dell'offerta).  In questa scheda si specificano gli identificatori principali e il nome dell'offerta.  Un asterisco (*) accanto al nome del campo indica che si tratta di un campo obbligatorio.

![Scheda Offer Settings (Impostazioni dell'offerta)](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Campi delle impostazioni dell'offerta 

Nella scheda **Offer Settings** (Impostazioni dell'offerta) è necessario specificare i campi obbligatori seguenti.

|  Campo        |  Descrizione                                                               |
|---------------|----------------------------------------------------------------------------|
| **Offer ID** (ID offerta)  | Identificatore univoco dell'offerta (in un profilo di pubblicazione). Questo identificatore è visibile negli URL dei prodotti, nei modelli di Resource Manager e nei report di fatturazione. Ha una lunghezza massima di 50 caratteri, può essere composto solo da caratteri alfanumerici minuscoli e trattini (-), ma non può terminare con un trattino. Questo campo non può essere modificato dopo che l'offerta è diventata disponibile in Azure Marketplace. Se ad esempio Contoso pubblica un'offerta con ID `sample-SvcApp`, come URL di AppSource gli viene assegnato `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Publisher ID** (ID editore) | Identificatore univoco dell'organizzazione in [AppSource](https://appsource.microsoft.com). È consigliabile associare a tutte le proprie offerte il proprio ID editore. Questo valore non può essere modificato dopo che l'offerta è stata salvata.                         |
| **Name** (Nome)      | Nome visualizzato dell'offerta. Questo nome viene visualizzato in AppSource e nel portale Cloud Partner. Può contenere massimo 50 caratteri. Le linee guida prevedono qui l'inclusione di un nome di marchio riconoscibile per il prodotto. Non includere qui il nome dell'organizzazione a meno che non corrisponda al nome con cui viene commercializzata l'app. Se si pubblicizza questa offerta in altri siti Web e altre pubblicazioni, assicurarsi che il nome sia uguale in tutte le pubblicazioni.    <br/>Se si rilascia un'offerta durante il periodo di anteprima di App Power BI, in modalità di anteprima, accodare la stringa `(Preview)` al nome dell'applicazione, ad esempio `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Passaggi successivi

Nella scheda successiva si specificheranno le [informazioni tecniche](./cpp-technical-info-tab.md) relative all'offerta.
