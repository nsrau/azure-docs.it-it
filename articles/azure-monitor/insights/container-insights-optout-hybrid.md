---
title: Come arrestare il monitoraggio del cluster Kubernetes ibrido | Microsoft Docs
description: Questo articolo descrive come è possibile arrestare il monitoraggio del cluster Kubernetes ibrido con monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: f2f3a8671c1f2baf60d399cc87f2f843dfee4f70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196218"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Come arrestare il monitoraggio del cluster ibrido

Dopo aver abilitato il monitoraggio del cluster Kubernetes in esecuzione in Azure Stack o in locale, è possibile arrestare il monitoraggio del cluster con monitoraggio di Azure per i contenitori se si decide che non si vuole più monitorarlo. Questo articolo illustra come eseguire questa operazione.  

## <a name="how-to-stop-monitoring-using-helm"></a>Come arrestare il monitoraggio con Helm

1. Per identificare prima di tutto il monitoraggio di Azure per i contenitori Helm Chart versione installata nel cluster, eseguire il comando Helm seguente.

    ```
    helm list
    ```

    L'output sarà simile al seguente:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *Azmon-Containers-Release-1* rappresenta la versione del grafico Helm per il monitoraggio di Azure per i contenitori.

2. Per eliminare la versione del grafico, eseguire il comando Helm seguente.

    `helm delete <releaseName>`

    Esempio:

    `helm delete azmon-containers-release-1`

    La versione verrà rimossa dal cluster. È possibile verificare eseguendo il `helm list` comando:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Il completamento della modifica della configurazione può richiedere alcuni minuti. Poiché Helm tiene traccia dei rilasci anche dopo averli eliminati, è possibile controllare la cronologia di un cluster e persino annullare l'eliminazione di `helm rollback`una versione con.

## <a name="next-steps"></a>Passaggi successivi

Se l'area di lavoro Log Analytics è stata creata solo per supportare il monitoraggio del cluster e non è più necessaria, è necessario eliminarla manualmente. Se non si ha familiarità con la modalità di eliminazione di un'area di lavoro, vedere [eliminare un'area di lavoro di Azure log Analytics](../../log-analytics/log-analytics-manage-del-workspace.md).
