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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/14/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4e6f5a17544c1419eb6101acdd6590f034ea4aa3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241459"
---
# <a name="validate-software-updates-from-microsoft"></a>Convalidare gli aggiornamenti software da Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft rilascerà periodicamente aggiornamenti per il software Azure Stack. Questi aggiornamenti sono disponibili per Azure Stack coengineering partner. Gli aggiornamenti vengono forniti in anticipo di disponibili pubblicamente. È possibile controllare gli aggiornamenti con la soluzione e fornire commenti e suggerimenti a Microsoft.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>Applicare l'aggiornamento mensile

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>Creare un flusso di lavoro

Le convalide di aggiornamento utilizzano lo stesso flusso di lavoro **soluzione convalida**.

## <a name="run-tests"></a>Esecuzione dei test

1. Le convalide di aggiornamento utilizzano lo stesso flusso di lavoro **soluzione convalida**. 

2. Seguire le istruzioni in [eseguire la convalida di Esplora test](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). In alternativa, selezionare i test seguenti:
    - Verificare l'aggiornamento mensile Azure Stack
    - Motore di simulazione di cloud

Non è necessario richiedere di firma per le convalide di aggiornamento del pacchetto.

## <a name="next-steps"></a>Passaggi successivi

- [Monitorare e gestire i test nel portale di VaaS](azure-stack-vaas-monitor-test.md)