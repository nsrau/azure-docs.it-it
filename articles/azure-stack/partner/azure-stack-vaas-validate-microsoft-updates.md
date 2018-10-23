---
title: Convalidare gli aggiornamenti software da Microsoft in Azure Stack di convalida come servizio | Microsoft Docs
description: Informazioni su come convalidare gli aggiornamenti software da Microsoft con la convalida come servizio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 7fcc7d5a1d87fe93d32772dbbb84f1d3c91d5631
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648786"
---
# <a name="validate-software-updates-from-microsoft"></a>Convalidare gli aggiornamenti software da Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft rilascerà periodicamente aggiornamenti per il software Azure Stack. Questi aggiornamenti vengono forniti per i partner di CO-ingegneria Azure Stack prima viene reso disponibile pubblicamente in modo da poter convalidare gli aggiornamenti con le proprie soluzioni e fornire commenti e suggerimenti a Microsoft.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>Applicare l'aggiornamento mensile

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>Creare un flusso di lavoro

Le convalide di aggiornamento utilizzano lo stesso flusso di lavoro **convalida dei pacchetti**. Seguire le istruzioni in [creare un flusso di lavoro di convalida del pacchetto](azure-stack-vaas-validate-oem-package.md#create-a-package-validation-workflow).

## <a name="run-tests"></a>Esegui i test

Le convalide di aggiornamento utilizzano lo stesso flusso di lavoro **convalida dei pacchetti**. Seguire le istruzioni in [eseguire la convalida del pacchetto verifica](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests).

Non devi richiesta di firma per le convalide di aggiornamento del pacchetto.

## <a name="next-steps"></a>Passaggi successivi

- [Monitorare e gestire i test nel portale di VaaS](azure-stack-vaas-monitor-test.md)
