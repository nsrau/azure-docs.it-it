---
title: Connettersi agli utenti di Office 365
description: Automatizzare le attività e i flussi di lavoro per ottenere e gestire i profili nei profili degli utenti di Office 365 usando le app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75666857"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Ottenere e gestire i profili negli utenti di Office 365 usando app per la logica di Azure

Connettersi a Office 365 Users per ottenere profili, cercare utenti e altro ancora. Con Office 365 Users è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Office 365 Users. 
* Usare azioni per ottenere i dipendenti diretti, il profilo utente di un manager e altro ancora. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, ottenere i dipendenti diretti di una persona e quindi sfruttare queste informazioni per aggiornare un database SQL Azure. 

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Creare una connessione a Office 365 Users

Quando si aggiunge questo connettore alle app per la logica, è necessario accedere all'account degli utenti di Office 365 in modo che le app per la logica di Azure possano connettersi all'account.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Dopo aver creato la connessione, immettere le proprietà di Office 365 Users, ad esempio l'ID utente. Il **riferimento all'API REST** in questo articolo descrive tali proprietà.

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per informazioni tecniche su trigger, azioni e limiti, descritti dalla descrizione di spavalderia del connettore, vedere la [pagina di riferimento del connettore](/connectors/officeusers/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](apis-list.md)