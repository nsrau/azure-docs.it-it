---
title: Report di convalida per Azure Stack | Microsoft Docs
description: Usare il report di controllo di conformità di Azure Stack per esaminare i risultati della convalida.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/23/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 44530a1c4eec0f2efca7357438366dd7b6abe42a
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093343"
---
# <a name="azure-stack-validation-report"></a>Report di convalida Azure Stack
Usare lo strumento di controllo di conformità di Azure Stack per eseguire le convalide che supportano la distribuzione e manutenzione di un ambiente Azure Stack. Lo strumento scrive i risultati in un file di rapporto con estensione JSON. Il report visualizza i dati di riepilogati e dettagliati sullo stato dei prerequisiti per la distribuzione di Azure Stack. Il report visualizza anche informazioni sulla rotazione dei segreti per le distribuzioni esistenti dello Stack di Azure.  

 ## <a name="where-to-find-the-report"></a>Dove trovare il report
Quando si esegue lo strumento, registra i risultati per **AzsReadinessCheckerReport.json**. Lo strumento crea anche un registro denominato **AzsReadinessChecker.log**. Consente di visualizzare la posizione di questi file con i risultati della convalida in PowerShell.

![eseguire la convalida](./media/azure-stack-validation-report/validation.png)

Entrambi i file di rendere persistenti i risultati successivi di controlli di convalida quando eseguirvi nello stesso computer.  Ad esempio, lo strumento può essere eseguito per convalidare i certificati, eseguire di nuovo per convalidare l'identità di Azure e quindi una terza volta per convalidare la registrazione. I risultati di tutte e tre le convalide sono disponibili nel report con estensione JSON risultante.  

Per impostazione predefinita, entrambi i file vengono scritti *C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Usare la **- OutputPath** ***&lt;path&gt;*** parametro alla fine di Esegui riga di comando per specificare un percorso di report diversa.   
- Usare la **- CleanReport** parametro alla fine del comando di esecuzione per cancellare informazioni da *AzsReadinessCheckerReport.json*. sulle esecuzioni precedenti dello strumento.

## <a name="view-the-report"></a>Visualizzare il report
Per visualizzare il report in PowerShell, specificare il percorso del report come un valore per **- ReportPath**. Questo comando Visualizza il contenuto del report e identifica le convalide che non hanno ancora i risultati.

Ad esempio, per visualizzare il report da un prompt di PowerShell che è aperto nel percorso in cui si trova il report, eseguire: 
   > `Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json` 

L'output sarà simile al seguente:

```PowerShell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

    Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters : 
```

## <a name="view-the-report-summary"></a>Visualizzare il report riepilogativo
Per visualizzare un riepilogo del report, è possibile aggiungere il **-riepilogo** passare alla fine della riga di comando di PowerShell. Ad esempio:  
 > `Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary`  

Il riepilogo Mostra le convalide che non hanno risultati e indica positivo o negativo per le convalide che vengono completate. L'output sarà simile al seguente:

```PowerShell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

    Certificate Validation found no errors or warnings.
    
############### Registration Validation Summary ###############

    Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

    Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.
```


## <a name="view-a-filtered-report"></a>Visualizzare un report filtrato
Per visualizzare un report che viene filtrato in un unico tipo di convalida, usare il **- ReportSections** parametro con uno dei valori seguenti:
- Certificate
- AzureRegistration
- AzureIdentity
- Grafico
- AD FS
- Processi   
- Tutti  

Ad esempio, per visualizzare il report riepilogo per i certificati solo, utilizzare la riga di comando PowerShell seguente: 
 > `Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Vedere anche 
