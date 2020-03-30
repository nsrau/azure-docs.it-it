---
title: Connettersi agli utenti di Office 365
description: Automatizzare le attività e i flussi di lavoro che ottengono e gestiscono profili nei profili degli utenti di Office 365 usando le app per la logica di AzureAutomate tasks and workflows that get and manage profiles in Office 365 Users profiles by using Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666857"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Ottenere e gestire profili in Utenti di Office 365 usando le app per la logica di AzureGet and manage profiles in Office 365 Users by using Azure Logic Apps

Connettersi a Office 365 Users per ottenere profili, cercare utenti e altro ancora. Con Office 365 Users è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Office 365 Users. 
* Usare azioni per ottenere i dipendenti diretti, il profilo utente di un manager e altro ancora. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, ottenere i dipendenti diretti di una persona e quindi sfruttare queste informazioni per aggiornare un database SQL Azure. 

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Creare una connessione a Office 365 Users

Quando si aggiunge questo connettore alle app per la logica, è necessario accedere all'account Utenti di Office 365 in modo che le app per la logica di Azure possano connettersi all'account.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Dopo aver creato la connessione, immettere le proprietà di Office 365 Users, ad esempio l'ID utente. Il **riferimento all'API REST** in questo articolo descrive tali proprietà.

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per informazioni tecniche sui trigger, le azioni e i limiti descritti dalla descrizione swagger del connettore, consultare la pagina di [riferimento del connettore.](/connectors/officeusers/)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](apis-list.md)