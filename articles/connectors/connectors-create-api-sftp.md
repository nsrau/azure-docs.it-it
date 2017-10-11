---
title: Informazioni su come usare il connettore SFTP nelle app per la logica | Documentazione Microsoft
description: "Creare app per la logica con Servizio app di Azure. Connettersi all'API SFTP per inviare e ricevere file. È possibile eseguire varie operazioni come creare, aggiornare, recuperare o eliminare file."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 31253d8daee1581167a96a20ba8ad529a04b3e92
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-sftp-connector"></a>Introduzione al connettore SFTP
Usare il connettore SFTP per accedere a un account SFTP al fine di inviare e ricevere file. È possibile eseguire varie operazioni come creare, aggiornare, recuperare o eliminare file.  

Per usare [qualsiasi connettore](apis-list.md), è necessario innanzitutto creare un'app per la logica. Come prima operazione [creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-sftp"></a>Connettersi a SFTP
Perché l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una [connessione](connectors-overview.md) fornisce la connettività tra un'app per la logica e un altro servizio.  

### <a name="create-a-connection-to-sftp"></a>Creare una connessione a SFTP
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>Usare un trigger SFTP
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

In questo esempio il trigger **SFTP - Quando viene aggiunto o modificato un file** viene usato per avviare un flusso di lavoro dell'app per la logica quando viene aggiunto o modificato un file in un server SFTP. Viene aggiunta anche una condizione che controlla il contenuto del file nuovo o modificato e decide di estrarre il file se il relativo contenuto indica che il file deve essere estratto prima di usare il contenuto. Viene infine aggiunta un'azione per estrarre il contenuto di un file e inserire il contenuto estratto in una cartella sul server SFTP. 

In un esempio riguardante un'organizzazione si potrebbe usare questo trigger per monitorare una cartella SFTP per nuovi file di ordini dei clienti.  È quindi possibile usare un'azione connettore SFTP come **Recuperare i contenuti del file** per recuperare il contenuto dell'ordine per elaborarlo ulteriormente e archiviarlo nel database degli ordini.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Add a condition
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>Usare un'azione SFTP
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/sftpconnector/).

## <a name="more-connectors"></a>Altri connettori
Tornare all' [elenco di API](apis-list.md).