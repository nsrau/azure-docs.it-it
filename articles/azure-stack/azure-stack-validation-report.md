---
title: Report di convalida per lo Stack di Azure | Documenti Microsoft
description: Usare il report di controllo di conformità dello Stack di Azure per esaminare i risultati della convalida.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a0ca0ae3ed615f6bc2774364f7a443023b911b5d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="azure-stack-validation-report"></a>Report di convalida Azure Stack
Le convalide che supportano la distribuzione e manutenzione di un ambiente dello Stack di Azure viene eseguito lo strumento di controllo di conformità dello Stack di Azure. Lo strumento scrive i risultati della convalida in un file di report con estensione JSON. Il report visualizza dati modo riepilogati e dettagliati sullo stato dei prerequisiti per la distribuzione dello Stack di Azure e su segreti rotazione per le distribuzioni esistenti dello Stack di Azure.  

 ## <a name="where-to-find-the-report"></a>Dove trovare il report
Quando si esegue lo strumento, log risultati **AzsReadinessCheckerReport.json**. Lo strumento crea inoltre un registro denominato **AzsReadinessChecker.log**. La posizione di questi file viene visualizzato con i risultati della convalida in PowerShell.

![eseguire la convalida](./media/azure-stack-validation-report/validation.png)

Entrambi i file di rendere persistenti i risultati successivi controlli di convalida quando eseguirvi nello stesso computer.  Ad esempio, è possibile eseguire lo strumento per la convalida dei certificati, eseguire di nuovo per convalidare l'identità di Azure e quindi una terza volta per convalidare la registrazione. I risultati di tutte le tre convalide sono disponibili nel report con estensione JSON risultante.  

Per impostazione predefinita, entrambi i file vengono scritti *C:\Users\<nomeutente > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Usare la **- OutputPath** ***&lt;percorso&gt;*** parametro alla fine di Esegui riga di comando per specificare un percorso di report diverso.   
- Usare la **- CleanReport** parametro alla fine del comando di esecuzione per cancellare informazioni da *AzsReadinessCheckerReport.json*. sulle esecuzioni precedenti dello strumento.

## <a name="view-the-report"></a>Visualizzare il report
Per visualizzare il report in PowerShell, specificare il percorso del report come un valore per **- ReportPath**. Questo comando Visualizza il contenuto del report e identifica anche le convalide che non hanno ancora i risultati.

Ad esempio, per visualizzare il report da un prompt di PowerShell che sia aperto per la posizione in cui si trova il report, eseguire: 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

L'output è simile nella figura seguente:

![Visualizza report](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>Visualizzare il report riepilogo
Per visualizzare un riepilogo del report, è possibile aggiungere la **-riepilogo** passare alla fine della riga di comando PowerShell. Ad esempio:  
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

Il riepilogo Mostra le convalide che non dispongono di risultati e indica superato o non superato per le convalide che vengono completate. L'output è simile nella figura seguente:

![Riepilogo report](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>Visualizzare un report filtrato
Per visualizzare un report filtrato in un unico tipo di convalida, usare il **- ReportSections** parametro e specificare uno dei seguenti valori che corrispondono al tipo di convalida che si desidera visualizzare:
- Certificate
- AzureRegistration
- AzureIdentity
- Processi   
- Tutti  

Ad esempio, per visualizzare il report riepilogo per i certificati solo, utilizzare la riga di comando PowerShell seguente: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Vedere anche 
[Riferimento ai cmdlet Start-AzsReadinessChecker](azure-stack-azsreadiness-cmdlet.md)
