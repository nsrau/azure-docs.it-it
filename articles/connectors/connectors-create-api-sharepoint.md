---
title: Connettersi a SharePoint da App per la logica di Azure | Microsoft Docs
description: Automatizzare le attività e i flussi di lavoro per il monitoraggio e la gestione delle risorse in SharePoint Online o SharePoint Server in locale usando App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: e636b2bb08477e6c56c6ae41f08983fc5bfa2a9b
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885492"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Monitorare e gestire le risorse di SharePoint con App per la logica di Azure

Con App per la logica di Azure e il connettore SharePoint, è possibile creare attività e flussi di lavoro automatizzati per monitorare e gestire le risorse, ad esempio file, cartelle, elenchi, elementi, persone e così via, in SharePoint Online o SharePoint Server in locale, ad esempio:

* Monitorare quando file o elementi vengono creati, modificati o eliminati.
* Creare, ottenere, aggiornare o eliminare elementi.
* Aggiungere, ottenere o eliminare allegati. Ottenere il contenuto dagli allegati.
* Creare, copiare, aggiornare o eliminare file. 
* Aggiornare le proprietà dei file. Ottenere il contenuto, i metadati o le proprietà di un file.
* Elencare o estrarre cartelle.
* Ottenere elenchi o visualizzazioni elenco.
* Impostare lo stato di approvazione del contenuto.
* Risolvere persone.
* Inviare richieste HTTP a SharePoint.
* Ottenere valori di entità.

È possibile usare i trigger per ottenere risposte da SharePoint e rendere l'output disponibile per altre azioni. È possibile usare azioni nelle app per la logica per eseguire attività in SharePoint. È anche possibile fare in modo che altre azioni usino l'output delle azioni di SharePoint. Se ad esempio si recuperano periodicamente file da SharePoint, è possibile inviare messaggi al proprio team tramite il connettore Slack.
Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* Le credenziali utente e l'indirizzo del sito SharePoint

  Le credenziali autorizzano l'app per la logica alla creazione di una connessione e all'accesso all'account SharePoint. 

* Prima di poter connettere le app per la logica a sistemi locali, ad esempio SharePoint Server, è necessario [installare e configurare un gateway dati locale](../logic-apps/logic-apps-gateway-install.md). In questo modo, è possibile specificare di usare l'installazione del gateway quando si crea la connessione a SharePoint Server per l'app per la logica.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere all'account SharePoint. Per iniziare con un trigger di SharePoint, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione di SharePoint, avviare l'app per la logica con un trigger, ad esempio un trigger Salesforce, se si ha un account Salesforce.

  È ad esempio possibile avviare l'app per la logica con il trigger Salesforce **Quando un record viene creato**. 
  Questo trigger viene attivato ogni volta che in Salesforce viene creato un nuovo record, ad esempio un lead. 
  È quindi possibile seguire questo trigger con l'azione **Crea file** di SharePoint. In questo modo, quando viene creato il nuovo record, l'app per la logica crea un file in SharePoint con informazioni sul nuovo record.

## <a name="connect-to-sharepoint"></a>Connettersi a SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Per le app per la logica vuote, nella casella di ricerca immettere "sharepoint" come filtro. Nell'elenco dei trigger selezionare il trigger desiderato. 

   -oppure-

   Per le app per la logica esistenti, nell'ultimo passaggio in cui si vuole aggiungere un'azione di SharePoint, scegliere **Nuovo passaggio**. 
   Nella casella di ricerca immettere "sharepoint" come filtro. 
   Nell'elenco delle azioni selezionare l'azione desiderata.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. 
   Scegliere il segno più (**+**) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Quando viene chiesto di eseguire l'accesso, specificare le informazioni di connessione necessarie. Se si usa SharePoint Server, assicurarsi di selezionare **Connetti tramite gateway dati locale**. Al termine dell'operazione, scegliere **Crea**.

1. Specificare i dettagli necessari per l'azione o il trigger selezionato e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/sharepoint/) del connettore.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
