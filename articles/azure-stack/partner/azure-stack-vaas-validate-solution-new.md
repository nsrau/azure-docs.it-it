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
ms.date: 12/20/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 12/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 57aa0146f29d45dbcb5b1a0ac2f4fbdf31cb045b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238755"
---
# <a name="validate-a-new-azure-stack-solution"></a>Convalidare una nuova soluzione di Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Informazioni su come usare il **convalida soluzione** flusso di lavoro per certificare le nuove soluzioni di Azure Stack.

Una soluzione di Azure Stack è una hardware distinta base (BoM) che è stato congiuntamente concordata tra Microsoft e partner di una volta soddisfatti i requisiti di certificazione del logo di Windows Server. Una soluzione deve essere recertified quando è stata apportata una modifica all'hardware BoM. Per ulteriori domande sulle situazioni in cui ricertificare soluzioni, contattare il team all'indirizzo [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

Per certificare la soluzione, eseguire due volte il flusso di lavoro di convalida di soluzioni. Eseguirlo una sola volta per il *minima* configurazione supportata. Eseguirlo una seconda volta per il *quello ottenuto* configurazione supportata. Microsoft certifica le soluzioni se entrambe le configurazioni superano tutti i test.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Creare un flusso di lavoro di convalida di soluzioni

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Selezionare **avviare** nel **soluzione convalide** riquadro.

    ![Riquadro della soluzione convalide del flusso di lavoro](media/tile_validation-solution.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Selezionare il **configurazione della soluzione**.
    - **Minimo**: la soluzione è configurata con il numero di nodi minimo supportato.
    - **Massimo**: la soluzione è configurata con il numero massimo supportato di nodi.
6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Informazioni sulla convalida di soluzioni](media/workflow_validation-solution_info.png)

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Parametri di ambiente non possono essere modificati dopo la creazione di un flusso di lavoro.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Si verrà reindirizzati alla pagina di riepilogo di test.

## <a name="run-solution-validation-tests"></a>Eseguire test di convalida di soluzioni

Nel **riepilogo test di convalida dei soluzione** pagina, verrà visualizzato un elenco dei test necessari per completare la convalida.

Nei flussi di lavoro di convalida **pianificazione** un test Usa i parametri comuni del flusso di lavoro a livello specificato durante la creazione del flusso di lavoro (vedere [parametri comuni del flusso di lavoro per la convalida dello Stack di Azure come servizio](azure-stack-vaas-parameters.md)). Se uno qualsiasi dei valori dei parametri di test diventano non valido, è necessario resupply li come indicato nelle [modificare i parametri del flusso di lavoro](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Pianificazione di un test di convalida su un'istanza esistente creerà una nuova istanza al posto di istanza precedente nel portale. I log per l'istanza precedente verranno mantenuti ma non sono accessibili dal portale.  
Una volta che un test è stata completata, il **pianificazione** azione viene disabilitata.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. Selezionare i test seguenti:
    - Motore di simulazione di cloud
    - Suite Operational SDK di calcolo
    - Test di identificazione del disco
    - KeyVault Extension SDK Operational Suite
    - KeyVault SDK Operational Suite
    - Network SDK Operational Suite
    - Storage Account SDK Operational Suite

3. Selezionare **pianificazione** dal menu di scelta rapida per aprire un prompt dei comandi per la pianificazione dell'istanza di test.

4. Esaminare i parametri di test e quindi selezionare **Submit** per il test per l'esecuzione.

![Test di convalida di soluzioni di pianificazione](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Passaggi successivi

- [Monitorare e gestire i test nel portale di VaaS](azure-stack-vaas-monitor-test.md)