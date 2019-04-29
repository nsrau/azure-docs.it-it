---
title: Offerta per l'App di BI Power - Azure Marketplace | Microsoft Docs
description: Come pubblicare un'app Power BI per il marketplace Microsoft AppSource.
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
ms.date: 03/27/2019
ms.author: pbutlerm
ms.openlocfilehash: f18a1b05e5a38a79945d8df6706dd2147d6b43df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725824"
---
# <a name="power-bi-app-offer"></a>Offerta per app Power BI

|              |                                |
|--------------|--------------------------------|
| Questo articolo illustra come pubblicare un'App di Power BI in Microsoft [marketplace AppSource](https://appsource.microsoft.com/).  Un'app Power BI i pacchetti inclusi set di dati, report e dashboard personalizzabile contenuto di Power BI. È quindi possibile distribuire l'app ad altri tenant di Power BI tramite AppSource, eseguire le regolazioni e personalizzazioni consentite dallo sviluppatore e connetterlo ai propri dati. | ![Icona di Power BI](./media/powerbi-icon.png) |


Questo articolo è suddivisa in tre parti principali:

-   [Prerequisiti](./cpp-prerequisites.md). I requisiti tecnici e aziendali per la creazione e pubblicazione di un'offerta di App di Power BI.
-   [Creare un'offerta di App di Power BI](./cpp-create-offer.md). Come creare una voce di offerta di App di Power BI usando il [portale Cloud Partner](https://cloudpartner.azure.com).
-   [Pubblicare un'offerta di App di Power BI](./cpp-publish-offer.md). Come inviare una nuova offerta in AppSource per la pubblicazione e come aggiornare un'offerta esistente.


## <a name="publishing-steps"></a>Procedura per la pubblicazione

Ecco i passaggi generali per la pubblicazione di un'offerta di App di Power BI:

![Passaggi per la pubblicazione dell'offerta App Power BI](media/publishing-steps.png)

Ecco l'offerta di App di Power BI processo di pubblicazione:

1. Creare applicazione del modello in Power BI. Questa azione genera un URL di installazione del pacchetto, che rappresenta l'asset tecnico primario per l'offerta. Anche in questo momento, alzare di livello il pacchetto di test nell'ambiente di pre-produzione. Per altre informazioni, vedere [cosa sono le app di modello di Power BI?](https://docs.microsoft.com/power-bi/service-template-apps-overview). 
2. Raccogliere o creare materiali di marketing dell'offerta, tra cui: nome ufficiale, descrizione, i logo e così via. 
3. Raccogliere o creare note legali dell'offerta e supportare documenti: *le condizioni d'uso*, *informativa sulla privacy*, *criteri di supporto*, Guida dell'utente e così via.
4. Creare l'offerta: usare il portale Cloud Partner per configurare i dettagli dell'offerta, tra cui la descrizione dell'offerta, specifiche di asset, informazioni legali, informazioni di supporto e materiali di marketing.  Dopo l'offerta è completamente specificato, inviarlo per la pubblicazione.
5. Monitorare il processo di pubblicazione nel portale Cloud Partner.  Durante questo passaggio, il team di onboarding di AppSource testa, convalida e certifica che l'applicazione. 
6. Dopo aver completato la certificazione per l'app, esaminarlo nel proprio ambiente di test e lo rilascia. 
7. L'App Power BI è elencato in AppSource ("entra in funzione" it).
8. In Power BI, alzare di livello il pacchetto di pre-produzione nell'ambiente di produzione. Per altre informazioni, vedere [gestire il rilascio di app modello](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).


## <a name="next-steps"></a>Passaggi successivi

Prima di creare l'App Power BI offerta e pubblicarla in AppSource, è necessario soddisfare i [requisiti](./cpp-prerequisites.md) per la pubblicazione di un'App di Power BI in AppSource.
