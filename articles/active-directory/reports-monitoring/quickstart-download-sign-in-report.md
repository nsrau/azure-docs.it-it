---
title: 'Guida introduttiva: Scaricare un report relativo agli accessi con il portale di Azure | Microsoft Docs'
description: Informazioni su come scaricare un report relativo agli accessi con il portale di Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 06/21/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 334649dbe315cb54ba95a29f4c69441fcd2c3417
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46362970"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Guida introduttiva: Scaricare un report relativo agli accessi con il portale di Azure

In questa guida introduttiva si apprenderà come scaricare i dati di accesso per il tenant relativi alle ultime 24 ore.

## <a name="prerequisites"></a>Prerequisiti

È necessario:

* Un tenant di Azure Active Directory con una licenza Premium per visualizzare il report delle attività di accesso. 
* Un utente con il ruolo Amministratore della sicurezza, Amministratore globale, Lettore report per il tenant o con un ruolo con autorizzazioni di lettura per la sicurezza. Qualsiasi utente nel tenant, inoltre, può visualizzare i propri accessi.

## <a name="quickstart-download-a-sign-in-report"></a>Guida introduttiva: Scaricare un report relativo agli accessi

1. Passare al [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** nel riquadro di spostamento sinistro e usare il pulsante **Cambia directory** per selezionare la directory attiva.
3. Nel dashboard selezionare **Azure Active Directory** e quindi selezionare **Accessi**. 
4. Scegliere **Ultime 24 ore** nell'elenco a discesa del filtro **Data** e selezionare **Applica** per visualizzare i dati di accesso delle ultime 24 ore. 
5. Selezionare il pulsante **Scarica** per scaricare un file CSV contenente i record filtrati. 

![Creazione di report](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Passaggi successivi

* [Report delle attività di accesso nel portale di Azure Active Directory](concept-sign-ins.md)
* [Criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md)
* [Latenze dei report di Azure Active Directory](reference-reports-latencies.md)
