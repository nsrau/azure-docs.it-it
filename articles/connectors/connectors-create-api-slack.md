---
title: Usare il connettore Slack nelle app per la logica di Azure | Microsoft Docs
description: Connettersi a Slack nelle app per la logica
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 73c512c70f1c135bd791d93cecc42bd6f4c06b3d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-slack-connector"></a>Introduzione al connettore Slack
Slack è uno strumento di comunicazione del team, che riunisce tutte le comunicazioni del team in un'unica posizione immediatamente disponibile e individuabile in qualsiasi luogo. 

Creare prima di tutto un'app per la logica. Vedere [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-slack"></a>Creare una connessione a Slack
Per usare il connettore Slack, creare prima una **connessione** , quindi specificare i dettagli di queste proprietà: 

| Proprietà | Obbligatoria | DESCRIZIONE |
| --- | --- | --- |
| token |Sì |Fornire le credenziali di Slack |

Accedere a Slack usando i passaggi seguenti e completare la configurazione della **connessione** Slack nell'app per la logica:

1. Selezionare **Ricorrenza**
2. Selezionare una **frequenza** e immettere un **intervallo**
3. Selezionare **Aggiungi un'azione**  
   ![Configurare Slack][1]  
4. Immettere Slack nella casella di ricerca e attendere che la ricerca restituisca tutte le voci con Slack nel nome
5. Selezionare **Slack - Invia messaggio**
6. Selezionare **Sign in to Slack** (Accedi a Slack):  
   ![Configurare Slack][2]
7. Specificare le credenziali di Slack per accedere e autorizzare l'applicazione    
   ![Configurare Slack][3]  
8. Si verrà reindirizzati alla pagina di accesso dell'organizzazione. **Autorizzare** l'interazione di Slack con l'app per la logica:      
   ![Configurare Slack][5] 
9. Al termine dell'autorizzazione si viene reindirizzati all'app per la logica per completarla tramite la configurazione della sezione **Slack - Recupera tutti i messaggi**. Aggiungere altri trigger e azioni necessari.  
   ![Configurare Slack][6]
10. Salvare il lavoro scegliendo **Salva** dal menu (in alto).

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/slack/).

## <a name="more-connectors"></a>Altri connettori
Tornare all' [elenco di API](apis-list.md).

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
