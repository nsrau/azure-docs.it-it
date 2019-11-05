---
title: Aggiornare il controller di ingresso con Helm
description: Questo articolo fornisce informazioni su come aggiornare il traffico in ingresso di un gateway applicazione usando Helm.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: 6ed73a2172e09e7255447b4467698670c95b63af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513237"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Come aggiornare il controller di ingresso del gateway applicazione con Helm 

È possibile aggiornare il controller di ingresso applicazione Azure gateway per Kubernetes (AGIC) usando un repository Helm ospitato in archiviazione di Azure.

Prima di iniziare la procedura di aggiornamento, verificare di aver aggiunto il repository necessario:

- Visualizzare i repository Helm attualmente aggiunti con:

    ```bash
    helm repo list
    ```

- Aggiungere il repository AGIC con:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Aggiornamento

1. Aggiornare il repository Helm di AGIC per ottenere la versione più recente:

    ```bash
    helm repo update
    ```

1. Visualizza le versioni disponibili del grafico `application-gateway-kubernetes-ingress`:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Risposta di esempio:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    La versione più recente disponibile nell'elenco precedente è: `0.7.0-rc1`

1. Visualizza i grafici Helm attualmente installati:

    ```bash
    helm list
    ```

    Risposta di esempio:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    L'installazione del grafico Helm dalla risposta di esempio precedente è denominata `odd-billygoat`. Questo nome verrà usato per il resto dei comandi. Il nome effettivo della distribuzione è probabilmente diverso.

1. Aggiornare la distribuzione Helm a una nuova versione:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Rollback

Se la distribuzione Helm ha esito negativo, è possibile eseguire il rollback a una versione precedente.

1. Ottenere l'ultimo numero di versione integro noto:

    ```bash
    helm history odd-billygoat
    ```

    Output di esempio:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    Dall'output di esempio del comando di `helm history` si presenta come l'ultima distribuzione riuscita della `odd-billygoat` was Revision `1`

1. Eseguire il rollback all'ultima revisione riuscita:

    ```bash
    helm rollback odd-billygoat 1
    ```
