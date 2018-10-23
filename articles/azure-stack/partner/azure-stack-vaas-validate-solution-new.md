---
title: Convalidare una nuova soluzione di Azure Stack | Microsoft Docs
description: Informazioni su come convalidare una nuova soluzione di Azure Stack con convalida di un servizio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 777609b89bc08cd61489d2c3a3669ec07ccbc372
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647012"
---
# <a name="validate-a-new-azure-stack-solution"></a>Convalidare una nuova soluzione di Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Informazioni su come usare il **soluzione convalida** flusso di lavoro per certificare le nuove soluzioni di Azure Stack.

Una soluzione di Azure Stack è una hardware distinta base (BoM) che è stato congiuntamente concordata tra Microsoft e partner di una volta soddisfatti i requisiti di certificazione del logo di Windows Server. Una soluzione deve essere recertified quando è stata apportata una modifica all'hardware BoM. Per ulteriori domande sulle situazioni in cui ricertificare soluzioni, contattare il team all'indirizzo [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

Per certificare la soluzione, eseguire due volte il flusso di lavoro di convalida di soluzioni. Eseguirlo una sola volta per il *minima* configurazione supportata. Eseguirlo una seconda volta per il *quello ottenuto* configurazione supportata. Microsoft certifica le soluzioni se entrambe le configurazioni superano tutti i test.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Creare un flusso di lavoro di convalida di soluzioni

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]
2. Selezionare **avviare** nel **soluzione convalide** riquadro.

    ![Riquadro della soluzione convalide del flusso di lavoro](media/tile_validation-solution.png)

3. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
4. Selezionare il **configurazione della soluzione**.
    - **Minimo**: la soluzione è configurata con il numero di nodi minimo supportato.
    - **Massimo**: la soluzione è configurata con il numero massimo supportato di nodi.
5. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Informazioni sulla convalida di soluzioni](media/workflow_validation-solution_info.png)

6. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Parametri di ambiente non possono essere modificati dopo la creazione di un flusso di lavoro.

7. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
8. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Si verrà reindirizzati alla pagina di riepilogo di test.

## <a name="execute-solution-validation-tests"></a>Eseguire i test di convalida di soluzioni

Nel **riepilogo test di convalida soluzione** pagina, verrà visualizzato un elenco dei test necessari per completare la convalida.

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

![Test di convalida di soluzioni di pianificazione](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Passaggi successivi

- [Monitorare e gestire i test nel portale di VaaS](azure-stack-vaas-monitor-test.md)