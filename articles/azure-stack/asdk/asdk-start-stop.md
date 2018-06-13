---
title: Avviare e arrestare il Kit di sviluppo dello Stack di Azure (ASDK) | Documenti Microsoft
description: Informazioni su come avviare e arrestare verso il basso dello Stack Development Kit (ASDK) di Azure.
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
ms.date: 04/11/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: dfb565803746ecdda9b36a4e12a3c3f2b4d9e0d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31427334"
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>Avviare e arrestare il Kit di sviluppo dello Stack di Azure (ASDK)
Non è consigliabile sufficiente riavviare il computer host ASDK. Seguire invece le procedure descritte in questo articolo per arrestare e riavviare i servizi ASDK correttamente. 

## <a name="stop-azure-stack"></a>Arrestare Azure Stack 
Per arrestare in modo corretto dello Stack di Azure di servizi del computer host ASDK, usare i comandi di PowerShell seguenti:

1. Accedere come AzureStack\CloudAdmin nel computer host ASDK.
2. Aprire PowerShell come amministratore (non PowerShell ISE).
3. Eseguire i comandi seguenti per stabilire una sessione di endpoint con privilegi (PEP): 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Successivamente, nella sessione PEP, usare il **Stop-AzureStack** cmdlet per arrestare i servizi di Azure Stack e spegnere il computer host ASDK:

   ```powershell
   Stop-AzureStack
   ```
5. Esaminare l'output di PowerShell per verificare che tutti i servizi di Azure Stack sono è stati arrestati prima che il computer host ASDK viene chiuso. Il processo di arresto richiede alcuni minuti.

## <a name="start-azure-stack"></a>Avviare dello Stack di Azure 
Servizi ASDK deve essere avviato automaticamente all'avvio del computer host. Tuttavia, il tempo di avvio ASDK infrastruttura servizi variano in base alle prestazioni della configurazione hardware del computer di host ASDK. Può richiedere diverse ore per tutti i servizi riavviare correttamente in alcuni casi.

Indipendentemente dal modo in cui è stato arrestato il ASDK, si deve usare la procedura seguente per verificare che tutti i servizi di Azure Stack sono completamente operativa dopo che il computer host è attiva: 

1. Accendere il computer host ASDK. 
2. Accedere come AzureStack\CloudAdmin nel computer host ASDK.
3. Aprire PowerShell come amministratore (non PowerShell ISE).
4. Eseguire i comandi seguenti per stabilire una sessione di endpoint con privilegi (PEP):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. Quindi, nella sessione PEP, eseguire i comandi seguenti per controllare lo stato di avvio dei servizi Azure Stack:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Esaminare l'output per garantire che i servizi di Azure Stack siano stati riavviati correttamente.

Per ulteriori informazioni sulle procedure consigliate per arrestare e riavviare i servizi Azure Stack correttamente, vedere [Start e stop Azure Stack](.\.\azure-stack-start-and-stop.md). 

## <a name="troubleshoot-startup-and-shutdown"></a>Risolvere i problemi di avvio e arresto 
Se servizi di Azure Stack non è stato avviato entro due ore dopo lo spegnimento del computer host ASDK, eseguire questi passaggi:

1. Accedere come AzureStack\CloudAdmin nel computer host ASDK.
2. Aprire PowerShell come amministratore (non PowerShell ISE).
3. Eseguire i comandi seguenti per stabilire una sessione di endpoint con privilegi (PEP):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Quindi, nella sessione PEP, eseguire i comandi seguenti per controllare lo stato di avvio dei servizi Azure Stack:

   ```powershell
   Test-AzureStack
   ```
5. Esaminare l'output e risolvere gli eventuali errori. Per ulteriori informazioni, vedere [eseguire un test di convalida dello Stack di Azure](.\.\azure-stack-diagnostic-test.md).
6. Riavviare i servizi di Azure Stack all'interno della sessione PEP eseguendo il **inizio AzureStack** cmdlet:

   ```powershell
   Start-AzureStack
   ```

Se in esecuzione **inizio AzureStack** comporta un errore, visitare il [forum di supporto di Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) ottenere ASDK risoluzione dei problemi di supporto. 

## <a name="next-steps"></a>Passaggi successivi 
Altre informazioni sullo strumento di diagnostica Azure Stack e rilasciare la registrazione, vedere [strumenti di diagnostica Azure Stack](.\.\azure-stack-diagnostics.md).
