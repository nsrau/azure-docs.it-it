---
title: File di inclusione
description: File di inclusione
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/04/2019
ms.openlocfilehash: c1f4b3d60c0b8af96d851ce54c2aac57c76335a7
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73633688"
---
1. Accedere al [portale di Azure](https://portal.azure.com/) usando le credenziali della sottoscrizione di Azure. 

1. Nell'angolo in alto a sinistra del portale di Azure selezionare **+ Crea una risorsa**.

      ![Creare una nuova risorsa](media/aml-create-in-portal/create-workspace.gif)

1. Usare la barra di ricerca per trovare **Machine Learning**.

1. Selezionare **Machine Learning**.

1. Nel riquadro **Machine Learning** selezionare **Crea** per iniziare.

1. Specificare le informazioni seguenti per configurare la nuova area di lavoro:

   Campo|DESCRIZIONE 
   ---|---
   Nome dell'area di lavoro |Immettere un nome univoco che identifichi l'area di lavoro. In questo esempio si usa **docs-ws**. I nomi devono essere univoci all'interno del gruppo di risorse. Usare un nome facile da ricordare e da distinguere dai nomi delle aree di lavoro create da altri utenti.  
   Subscription |Selezionare la sottoscrizione di Azure da usare.
   Resource group | Usare un gruppo di risorse esistente nella sottoscrizione oppure immettere un nome per creare un nuovo gruppo di risorse. Un gruppo di risorse include risorse correlate per una soluzione Azure. In questo esempio si usa **docs-aml**. 
   Location | Selezionare la località più vicina agli utenti e alle risorse di dati per creare l'area di lavoro.
   Edizione dell'area di lavoro | Selezionare **Enterprise**.  Per questa esercitazione è necessario usare l'edizione Enterprise.  L'edizione Enterprise è in anteprima e attualmente non prevede costi aggiuntivi.  

1. Dopo aver completato la configurazione dell'area di lavoro, selezionare **Crea**. 

   > [!Warning] 
   > La creazione dell'area di lavoro nel cloud può richiedere diversi minuti.

   Al termine del processo verrà visualizzato un messaggio di conferma del completamento della distribuzione, 
 
 1. Per visualizzare la nuova area di lavoro, selezionare **Vai alla risorsa**.

