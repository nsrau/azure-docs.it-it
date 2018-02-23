---
title: Avviare e arrestare dello Stack di Azure | Documenti Microsoft
description: Informazioni su come avviare e arrestare dello Stack di Azure.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: mabrigg
ms.openlocfilehash: 98bf75f5883b734c785ed1a3ed924afca1737c56
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="start-and-stop-azure-stack"></a>Avviare e arrestare Azure Stack

*Si applica a: Azure Stack integrati sistemi (versione 1712 e versioni successive)*

## <a name="stop-azure-stack"></a>Arrestare Azure Stack 

Arresto dello Stack di Azure con i passaggi seguenti:

1. Aprire una sessione di Endpoint con privilegi (PEP) da un computer con accesso alla rete per le macchine virtuali di Azure Stack ERCS. Per istruzioni, vedere [utilizzando l'endpoint con privilegi in Azure Stack](azure-stack-privileged-endpoint.md).

2. Da PEP, eseguire:

    ```powershell
      Stop-AzureStack
    ```

3. Attendere che tutti i nodi fisici di Stack di Azure per power off.

> [!Note]  
> È possibile verificare lo stato di alimentazione di un nodo fisico seguendo le istruzioni da Original Equipment Manufacturer (OEM) che ha fornito l'hardware di Azure Stack. 

## <a name="start-azure-stack"></a>Avviare dello Stack di Azure 

Avviare dello Stack di Azure con i passaggi seguenti. Seguire questi passaggi indipendentemente dalla modalità di arresto dello Stack di Azure.

1. Risparmio energia in ogni nodo fisico Stack Azure nell'ambiente in uso. Verificare la potenza nelle istruzioni per i nodi fisici seguendo le istruzioni da Original Equipment Manufacturer (OEM) che ha fornito l'hardware per lo Stack di Azure.

2. Attendere fino all'avvio di servizi di infrastruttura di Azure Stack. Servizi di infrastruttura di Azure Stack possono richiedere due ore per terminare il processo di avvio. È possibile verificare lo stato di inizio dello Stack di Azure con il [ **Get ActionStatus** cmdlet](#get-the-startup-status-for-azure-stack).


## <a name="get-the-startup-status-for-azure-stack"></a>Ottenere lo stato di avvio per lo Stack di Azure

Ottenere l'avvio della routine di avvio dello Stack di Azure con i passaggi seguenti:

1. Aprire una sessione di Endpoint con privilegi da un computer con accesso alla rete per le macchine virtuali di Azure Stack ERCS.

2. Da PEP, eseguire:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Risoluzione dei problemi di avvio e arresto dello Stack di Azure

Se i servizi di infrastruttura e del tenant non avviare 2 ore dopo il risparmio di energia nell'ambiente dello Stack di Azure, eseguire la procedura seguente. 

1. Aprire una sessione di Endpoint con privilegi da un computer con accesso alla rete per le macchine virtuali di Azure Stack ERCS.

2. Eseguire: 

    ```powershell
      Test-AzureStack
      ```

3. Esaminare l'output e risolvere eventuali errori di integrità. Per ulteriori informazioni, vedere [eseguire un test di convalida dello Stack di Azure](azure-stack-diagnostic-test.md).

4. Eseguire:

    ```powershell
      Start-AzureStack
    ```

5. Se in esecuzione **inizio AzureStack** comporta un errore, contattare il supporto tecnico di servizi Microsoft. 

## <a name="next-steps"></a>Passaggi successivi 

Ulteriori informazioni sullo strumento di diagnostica Azure Stack e di eseguire la registrazione, vedere [strumenti di diagnostica di Azure Stack. Strumenti di diagnostica Stack Azure. Strumenti di diagnostica Stack Azure. Strumenti di diagnostica Stack Azure.
