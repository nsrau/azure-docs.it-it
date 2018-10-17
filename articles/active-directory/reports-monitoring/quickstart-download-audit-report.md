---
title: 'Guida introduttiva: Scaricare il report di controllo con il portale di Azure | Microsoft Docs'
description: Informazioni su come scaricare il report di controllo con il portale di Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 09/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d6ecf62b46c25ec7cf4ab80219d1bc220e4eb4cc
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367407"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Guida introduttiva: scaricare il report di controllo con il portale di Azure

In questa guida introduttiva si apprenderà come scaricare i log di controllo per il tenant per le ultime 24 ore.

## <a name="prerequisites"></a>Prerequisiti

È necessario:

* Un tenant di Azure Active Directory. 
* Un utente che dispone del ruolo di Amministratore della sicurezza o di Amministratore globale o che dispone del ruolo con autorizzazioni di lettura per la sicurezza per il tenant. Inoltre, tutti gli utenti nel tenant possono accedere ai propri log di controllo.

## <a name="quickstart-download-an-audit-report"></a>Guida introduttiva: Scaricare un report di controllo

1. Passare al [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** nel riquadro di spostamento sinistro e usare il pulsante **Cambia directory** per selezionare la directory attiva.
3. Nel dashboard selezionare **Azure Active Directory** e quindi selezionare **Log di controllo**. 
4. Scegliere **Ultime 24 ore** nell'elenco a discesa del filtro **Intervallo di date** e selezionare **Applica** per visualizzare i log di controllo delle ultime 24 ore. 
5. Selezionare il pulsante **Scarica** per scaricare un file CSV contenente i record filtrati. 

![Creazione di report](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Passaggi successivi

* [Report delle attività di accesso nel portale di Azure Active Directory](concept-sign-ins.md)
* [Criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md)
* [Latenze dei report di Azure Active Directory](reference-reports-latencies.md)
