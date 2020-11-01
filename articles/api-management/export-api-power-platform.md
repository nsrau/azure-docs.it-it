---
title: Esportare API da Gestione API di Azure a Power Platform | Microsoft Docs
description: Informazioni su come esportare API da Gestione API a Power Platform.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 7c4d32dd63120a52fd7351977943574455e5cfad
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146655"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Esportare API da Gestione API di Azure a Power Platform 

Gli sviluppatori non professionisti che usano Microsoft [Power Platform](https://powerplatform.microsoft.com) spesso devono accedere a funzionalità aziendali sviluppate da sviluppatori professionisti e distribuite in Azure. [Gestione API di Azure](https://aka.ms/apimrocks) consente agli sviluppatori professionisti di pubblicare servizi back-end come API e di esportare facilmente tali API in Power Platform (Power Apps e Power Automate) come connettori personalizzati utilizzabili da sviluppatori non professionisti. 

Questo articolo illustra i passaggi per esportare le API da Gestione API a Power Platform. 

## <a name="prerequisites"></a>Prerequisiti

+ Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)
+ Assicurarsi che nell'istanza di Gestione API sia presente un'API che si vuole esportare in Power Platform
+ Assicurarsi di avere un [ambiente](/powerapps/powerapps-overview#power-apps-for-admins) Power Apps o Power Automate 

## <a name="export-an-api"></a>Esportare un'API

1. Passare al servizio Gestione API nel portale di Azure e selezionare **API** dal menu.
2. Fare clic sui tre puntini di sospensione accanto all'API che si vuole esportare. 
3. Selezionare **Export** (Esporta).
4. Selezionare **Power Apps e Power Automate** .
5. Scegliere un ambiente in cui esportare l'API. 
6. Specificare un nome visualizzato, che verrà usato come nome del connettore personalizzato.  
7. Facoltativamente, se l'API è protetta da un server OAuth 2.0, sarà necessario fornire anche dettagli aggiuntivi, tra cui `Client ID`, `Client secret`, `Authorization URL`, `Token URL` e `Refresh URL`.  
8. Selezionare **Export** (Esporta). 

Al termine dell'esportazione, passare all'ambiente Power App o Power Automate. L'API sarà visualizzata come connettore personalizzato.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su Power Platform](https://powerplatform.microsoft.com/)
* [Informazioni sulle attività comuni eseguibili in Gestione API seguendo le esercitazioni](./import-and-publish.md)
