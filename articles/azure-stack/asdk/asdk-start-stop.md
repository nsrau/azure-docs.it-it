---
title: Avviare e arrestare Azure Stack Development Kit (ASDK) | Microsoft Docs
description: Informazioni su come avviare e arrestare verso il basso Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: b821c5cfa0dd9ea605d0d0a73e0b98a64a7502b7
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268652"
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>Avviare e arrestare Azure Stack Development Kit (ASDK)
È consigliabile non è sufficiente riavviare il computer host ASDK. In alternativa, è necessario seguire le procedure descritte in questo articolo per arrestare e riavviare i servizi ASDK correttamente. 

## <a name="stop-azure-stack"></a>Arrestare Azure Stack 
Per arrestare correttamente servizi di Azure Stack e il computer host ASDK, usare i comandi di PowerShell seguenti:

1. Accedere come AzureStack\AzureStackAdmin nel computer host ASDK.
2. Aprire PowerShell come amministratore (non PowerShell ISE).
3. Eseguire i comandi seguenti per stabilire una sessione di endpoint con privilegi (PEP): 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Successivamente, nella sessione PEP, usare il **Stop-AzureStack** cmdlet per arrestare i servizi di Azure Stack e arrestare il computer host ASDK:

   ```powershell
   Stop-AzureStack
   ```
5. Esaminare l'output di PowerShell per verificare che tutti i servizi di Azure Stack vengono arrestati correttamente prima che il computer host ASDK viene arrestato. Il processo di arresto richiede alcuni minuti.

## <a name="start-azure-stack"></a>Avviare Azure Stack 
Servizi ASDK devono avviarsi automaticamente all'avvio del computer host. Tuttavia, tempo di avvio di servizi di infrastruttura ASDK varia in base alle prestazioni della configurazione hardware ASDK computer host. Può richiedere diverse ore per tutti i servizi riavviare correttamente in alcuni casi.

Indipendentemente dal modo in cui è stato arrestato il ASDK, si deve usare la procedura seguente per verificare che tutti i servizi di Azure Stack sono stati avviati e completamente operativa dopo che il computer host viene acceso: 

1. Accendere il computer host ASDK. 
2. Accedere come AzureStack\AzureStackAdmin nel computer host ASDK.
3. Aprire PowerShell come amministratore (non PowerShell ISE).
4. Eseguire i comandi seguenti per stabilire una sessione di endpoint con privilegi (PEP):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. Successivamente, nella sessione PEP, eseguire i comandi seguenti per controllare lo stato di avvio dei servizi di Azure Stack:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Esaminare l'output per verificare che servizi di Azure Stack siano stati riavviati correttamente.

Per altre informazioni sulle procedure consigliate per arrestare e riavviare i servizi di Azure Stack in modo corretto, vedere [Start e stop Azure Stack](../azure-stack-start-and-stop.md). 

## <a name="troubleshoot-startup-and-shutdown"></a>Risolvere i problemi di avvio e arresto 
Se servizi di Azure Stack non è stato avviato entro due ore dopo avere energia nel computer host ASDK, seguire questi passaggi:

1. Accedere come AzureStack\AzureStackAdmin nel computer host ASDK.
2. Aprire PowerShell come amministratore (non PowerShell ISE).
3. Eseguire i comandi seguenti per stabilire una sessione di endpoint con privilegi (PEP):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Successivamente, nella sessione PEP, eseguire i comandi seguenti per controllare lo stato di avvio dei servizi di Azure Stack:

   ```powershell
   Test-AzureStack
   ```
5. Esaminare l'output e risolvere gli eventuali errori. Per altre informazioni, vedere [esegue un test di convalida di Azure Stack](../azure-stack-diagnostic-test.md).
6. Riavviare i servizi di Azure Stack all'interno della sessione PEP eseguendo il **Start-AzureStack** cmdlet:

   ```powershell
   Start-AzureStack
   ```

Se in esecuzione **Start-AzureStack** genera un errore, visitare il [forum del supporto di Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) ottenere ASDK risoluzione dei problemi di supporto. 

## <a name="next-steps"></a>Passaggi successivi 
Altre informazioni sullo strumento di diagnostica di Azure Stack e rilasciare la registrazione, vedere [strumenti di diagnostica di Azure Stack](../azure-stack-diagnostics.md).
