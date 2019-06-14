---
title: Migrare le app allo schema più recente- App per la logica di Azure | Microsoft Docs
description: Come eseguire la migrazione di app per la logica alla versione più recente dello schema
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: bf27739bd42106550c18e3bbc27a1ff8b3770747
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60447155"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrare le app per la logica alla versione più recente dello schema

Per spostare le app per la logica esistenti allo schema più recente, seguire questa procedura: 

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

2. Scegliere **Panoramica** dal menu dell'app per la logica. Sulla barra degli strumenti scegliere **Aggiorna schema**.

   > [!NOTE]
   > Quando si seleziona **Aggiorna schema**, le App per la logica di Azure eseguono automaticamente i passaggi della migrazione e generano l'output del codice. Per aggiornare la definizione dell'app per la logica, è possibile usare questo output. Tuttavia, assicurarsi di seguire le procedure consigliate, come descritto di seguito nella sezione delle **Procedure consigliate**.

   ![Aggiorna schema](./media/connectors-schema-migration/update-schema.png)

   La pagina Aggiorna schema verrà visualizzata e mostrerà un collegamento a un documento che descrive i miglioramenti apportati al nuovo schema.

## <a name="best-practices"></a>Procedure consigliate

Di seguito sono riportate alcune procedure consigliate durante la migrazione di app per la logica alla versione più recente dello schema:

* Copiare lo script migrato in una nuova app per la logica. Non sovrascrivere la versione precedente finché non si completa il test e verificare che l'app migrata funzioni come previsto.

* Testare l'app per la logica **prima** di passare alla fase di produzione.

* Al termine della migrazione, avviare l'aggiornamento delle app per la logica per usare le [API gestite](../connectors/apis-list.md), quando è possibile. Ad esempio, iniziare a usare Dropbox 2 dove si usa DropBox 1.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [eseguire manualmente la migrazione delle app per la logica](../logic-apps/logic-apps-schema-2015-08-01.md)
