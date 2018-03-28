---
title: Usare il connettore SharePoint Server nelle app per la logica | Microsoft Docs
description: Introduzione all'uso del connettore SharePoint Server nelle app per la logica
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: d342b3c4f84c5dab212b9327d6a72759934d0ae5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-sharepoint-connector"></a>Introduzione al connettore SharePoint
Il connettore SharePoint consente di usare gli elenchi in SharePoint.

Creare prima di tutto un'app per la logica. Vedere [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sharepoint"></a>Creare una connessione a SharePoint
Per usare il connettore SharePoint, creare prima una **connessione**, quindi indicare i dettagli di queste proprietà: 

| Proprietà | Obbligatoria | DESCRIZIONE |
| --- | --- | --- |
| token |Sì |Fornire le credenziali di SharePoint |

Per connettersi a **SharePoint**, immettere la propria identità (nome utente e password, credenziali di smart card e così via). Dopo l'autenticazione, è possibile usare il connettore SharePoint nella propria app per la logica. 

Nella finestra di progettazione dell'app per la logica usare la procedura seguente per accedere e creare la **connessione** da usare nell'app per la logica:

1. Immettere SharePoint nella casella di ricerca e attendere che la ricerca restituisca tutte le voci con SharePoint nel nome:    
   ![Configurare SharePoint][1]  
2. Selezionare **SharePoint - Quando un file viene creato**   
3. Selezionare **Sign in to SharePoint** (Accedi a SharePoint):   
   ![Configurare SharePoint][2]    
4. Fornire le credenziali di SharePoint per accedere ed eseguire l'autenticazione con SharePoint    
   ![Configurare SharePoint][3]     
5. Al termine dell'autenticazione, si viene reindirizzati all'app per la logica per completarla configurando la finestra di dialogo **Quando un file viene creato** di SharePoint.          
   ![Configurare SharePoint][4]  
6. È quindi possibile aggiungere altri trigger e azioni necessari per completare l'app per la logica.   
7. Salvare il lavoro scegliendo **Salva** dal menu (in alto).

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/sharepoint/).

## <a name="more-connectors"></a>Altri connettori
Tornare all' [elenco di API](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
