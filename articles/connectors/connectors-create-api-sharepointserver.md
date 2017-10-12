---
title: Usare il connettore SharePoint Server nelle app per la logica | Microsoft Docs
description: Introduzione all'uso del connettore SharePoint Server nelle app per la logica
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 0f3274816e279a1aa57febaa2f8294914900799a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-sharepoint-connector"></a>Introduzione al connettore SharePoint
Il connettore SharePoint consente di utilizzare gli elenchi in SharePoint.

Creare prima di tutto un'app per la logica. Vedere [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-sharepoint"></a>Creare una connessione a SharePoint
Per usare il connettore SharePoint, creare prima una **connessione** , quindi indicare i dettagli di queste proprietà: 

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| Token |Sì |Fornire le credenziali di SharePoint |

Per connettersi a **SharePoint**, immettere la propria identità (nome utente e password, credenziali di smart card e così via) in SharePoint. Dopo l'autenticazione, è possibile usare il connettore SharePoint nella propria app per la logica. 

Dall'applicazione di progettazione delle app per la logica, seguire questi passaggi per accedere a SharePoint e creare la connessione **connessione** da usare con l'app per la logica:

1. Immettere SharePoint nella casella di ricerca e attendere che la ricerca restituisca tutte le voci con SharePoint nel nome:    
   ![Configurare SharePoint][1]  
2. Selezionare **SharePoint - Quando un file viene creato**   
3. Selezionare **Sign in to SharePoint** (Accedi a SharePoint):   
   ![Configurare SharePoint][2]    
4. Fornire le credenziali di SharePoint per accedere ed eseguire l'autenticazione con SharePoint    
   ![Configurare SharePoint][3]     
5. Al termine dell'autenticazione si verrà reindirizzati all'app per la logica per completarla tramite la configurazione della finestra di dialogo **Quando un file viene creato** di SharePoint.          
   ![Configurare SharePoint][4]  
6. È quindi possibile aggiungere altri trigger e azioni necessari per completare l'app per la logica.   
7. Salvare il lavoro selezionando **Salva** nella barra dei menu visualizzata in alto.  

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/sharepoint/).

## <a name="more-connectors"></a>Altri connettori
Tornare all' [elenco di API](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
