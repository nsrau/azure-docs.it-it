---
title: Avviare e arrestare Azure Stack | Microsoft Docs
description: Informazioni su come avviare e arrestare Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: b8d13377b2fb1545ecf88645a5e19bf5eab24c74
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238136"
---
# <a name="start-and-stop-azure-stack"></a>Avviare e arrestare Azure Stack
È necessario seguire le procedure descritte in questo articolo per arrestare e riavviare i servizi di Azure Stack in modo corretto. Arresto verrà fisicamente spegnere l'intero ambiente Azure Stack. Avvio consente il funzionamento in tutti i ruoli di infrastruttura e restituisce le risorse del tenant e lo stato di alimentazione che si trovavano prima dell'arresto.

## <a name="stop-azure-stack"></a>Arrestare Azure Stack 

Arrestare Azure Stack con i passaggi seguenti:

1. Preparare tutti i carichi di lavoro in esecuzione in risorse del tenant dell'ambiente Azure Stack per la chiusura imminente. 

2. Aprire una sessione di Endpoint con privilegi (PEP) da un computer con accesso alla rete alle VM di Azure Stack ERCS. Per istruzioni, vedere [usando l'endpoint con privilegi in Azure Stack](azure-stack-privileged-endpoint.md).

3. Da PEP, eseguire:

    ```powershell
      Stop-AzureStack
    ```

4. Attendere che tutti i nodi di Azure Stack fisici per power off.

> [!Note]  
> È possibile verificare lo stato di alimentazione di un nodo fisico, seguendo le istruzioni da Original Equipment Manufacturer (OEM) che ha fornito l'hardware di Azure Stack. 

## <a name="start-azure-stack"></a>Avviare Azure Stack 

Avviare Azure Stack con i passaggi seguenti. Seguire questi passaggi indipendentemente dal modo in cui Azure Stack arrestato.

1. Risparmio energia in ogni nodo fisico nell'ambiente Azure Stack. Verificare il risparmio di energia nelle istruzioni per i nodi fisici, seguendo le istruzioni da Original Equipment Manufacturer (OEM) che ha fornito l'hardware per Azure Stack.

2. Attendere fino all'avvio di servizi di infrastruttura di Azure Stack. Servizi di infrastruttura di Azure Stack possono richiedere due ore per terminare il processo di avvio. È possibile verificare lo stato iniziale di Azure Stack con il [ **Get-ActionStatus** cmdlet](#get-the-startup-status-for-azure-stack).

3. Assicurarsi che tutte le risorse del tenant hanno riportato allo stato in cui che si trovavano prima dell'arresto. I carichi di lavoro in esecuzione in risorse del tenant debba essere riconfigurato dopo l'avvio dal gestore del carico di lavoro.

## <a name="get-the-startup-status-for-azure-stack"></a>Ottenere lo stato di avvio per Azure Stack

Ottenere l'avvio per la routine di avvio di Azure Stack con i passaggi seguenti:

1. Aprire una sessione di Endpoint con privilegi da un computer con accesso alla rete alle VM di Azure Stack ERCS.

2. Da PEP, eseguire:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Risolvere i problemi di avvio e arresto di Azure Stack

Eseguire la procedura seguente se i servizi di infrastruttura e del tenant non è stato avviato 2 ore dopo si power nell'ambiente Azure Stack. 

1. Aprire una sessione di Endpoint con privilegi da un computer con accesso alla rete alle VM di Azure Stack ERCS.

2. Eseguire: 

    ```powershell
      Test-AzureStack
      ```

3. Esaminare l'output e risolvere eventuali errori di integrità. Per altre informazioni, vedere [esegue un test di convalida di Azure Stack](azure-stack-diagnostic-test.md).

4. Eseguire:

    ```powershell
      Start-AzureStack
    ```

5. Se in esecuzione **Start-AzureStack** genera un errore, contattare il supporto di Microsoft Customer Services. 

## <a name="next-steps"></a>Passaggi successivi 

Altre informazioni su [gli strumenti di diagnostica di Azure Stack](azure-stack-diagnostics.md)
