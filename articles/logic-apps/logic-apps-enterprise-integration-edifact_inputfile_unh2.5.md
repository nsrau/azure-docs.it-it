---
title: UNH 2.5 segmenti nei messaggi EDIFACT
description: Risolvere i messaggi EDIFACT con segmenti UNH2.5 nelle app per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792531"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Gestire documenti EDIFACT con segmenti UNH2.5 nelle App per la logica di Azure

Se in un documento EDIFACT è presente un segmento UNH2.5, il segmento viene utilizzato per la ricerca dello schema. Ad esempio, in questo messaggio EDIFACT di `EAN008`esempio, il campo UNH è :

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Per gestire questo messaggio, attenersi alla seguente procedura descritta di seguito:

1. Aggiornare lo schema.

1. Controllare le impostazioni dell'accordo.

## <a name="update-the-schema"></a>Aggiornare lo schema

Per elaborare il messaggio, è necessario distribuire uno schema con il nome del nodo radice UNH2.5.To process the message, you need to deploy a schema that has the UNH2.5 root node name. Ad esempio, il nome radice dello schema `EFACT_D03B_ORDERS_EAN008`per il campo UNH di esempio è . Per `D03B_ORDERS` ognuno che ha un segmento UNH2.5 diverso, è necessario distribuire un singolo schema.

## <a name="add-schema-to-edifact-agreement"></a>Aggiungere lo schema all'accordo EDIFACTAdd schema to EDIFACT agreement

### <a name="edifact-decode"></a>Decodifica EDIFACT

Per decodificare il messaggio in arrivo, impostare lo schema nelle impostazioni di ricezione dell'accordo EDIFACT:

1. Nel [portale di Azure](https://portal.azure.com)aprire l'account di integrazione.

1. Aggiungere lo schema all'account di integrazione.

1. Configurare lo schema nelle impostazioni di ricezione dell'accordo EDIFACT.

1. Selezionare l'accordo EDIFACT e selezionare **Modifica come JSON**. Aggiungere il valore UNH2.5 alla `schemaReferences` sezione del contratto di ricezione:

   ![Aggiungere UNH2.5 per ricevere l'accordo](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Codifica EDIFACT

Per codificare il messaggio in arrivo, configurare lo schema nelle impostazioni di invio dell'accordo EDIFACT

1. Nel [portale di Azure](https://portal.azure.com)aprire l'account di integrazione.

1. Aggiungere lo schema all'account di integrazione.

1. Configurare lo schema nelle impostazioni di invio dell'accordo EDIFACT.

1. Selezionare il contratto EDIFACT e fare clic su **Modifica come JSON**.  Aggiungere il valore UNH2.5 nel contratto di invio **schemaReferences**

1. Selezionare l'accordo EDIFACT e selezionare **Modifica come JSON**. Aggiungere il valore UNH2.5 alla `schemaReferences` sezione Invia accordo:

   ![Aggiungere UNH2.5 per inviare il contratto](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sui contratti per gli account di [integrazione](../logic-apps/logic-apps-enterprise-integration-agreements.md)