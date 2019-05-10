---
title: Applicazione di monitoraggio di Azure modificare analysis - individuare le modifiche che potrebbero influire sulla problemi/interruzioni di siti live con applicazione di monitoraggio di Azure modificare analysis | Microsoft Docs
description: Risolvere i problemi di siti attivi dell'applicazione in servizi App di Azure con analisi di modifica dell'applicazione di monitoraggio di Azure
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: b04bd57c66b52e9a2c14d43c9e89d7c54fc48ae2
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415810"
---
# <a name="azure-monitor-application-change-analysis-public-preview"></a>Azure Monitor analisi applicazione di modifiche (anteprima pubblica)

Quando si verifica una problema o interruzione del sito live, è essenziale determinare rapidamente la causa radice. Soluzioni di monitoraggio standard può consentire di identificare rapidamente che si è verificato un problema e spesso anche quale componente ha esito negativo. Ma ciò non sempre causare una spiegazione immediata per il motivo per cui si sta verificando l'errore. Il sito ha lavorato cinque minuti fa, a questo punto è rotto. Le modifiche apportate negli ultimi cinque minuti? Questa è la domanda che la nuova funzionalità di analisi applicazione di modifiche sono progettata per rispondere a monitoraggio di Azure. Creando la potenza delle [Graph di Azure Resource](https://docs.microsoft.com/azure/governance/resource-graph/overview) analisi applicazione di modifiche offre informazioni approfondite le modifiche dell'applicazione di servizio App di Azure per aumentare l'osservabilità e ridurre il MTTR (tempo medio di ripristino).

> [!IMPORTANT]
> Modifica analisi applicazione di Azure Monitor sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-do-i-use-application-change-analysis"></a>Come usare analisi applicazione di modifiche?

Modifica analisi applicazione di Azure Monitor sono attualmente incorporato in Self-Service **diagnosi e risoluzione dei problemi** esperienza, che è possibile accedere dalle **Panoramica** sezione del servizio App di Azure applicazione:

![Cenni preliminari sulla schermata di Azure App Service con caselle rosse intorno pulsante Cenni preliminari sulla pagina e diagnosticare e risolvere i pulsante problemi](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis"></a>Abilitare l'analisi di modifica

1. Selezionare **disponibilità e prestazioni**

    ![schermata di disponibilità e le opzioni di risoluzione dei problemi di prestazioni](./media/change-analysis/availability-and-performance.png)

2. Scegliere il **blocchi applicazione** riquadro.

   ![Screenshot con riquadro di arresti anomali dell'applicazione](./media/change-analysis/application-crashes-tile.png)

3. Per abilitare **modifica Analysis** selezionate **abilitare ora**.

   ![schermata di disponibilità e le opzioni di risoluzione dei problemi di prestazioni](./media/change-analysis/application-crashes.png)

4. Per sfruttare i vantaggi della versione completa modificare set di funzionalità di analisi **modificare Analysis**, **rileva modifiche di codice**, e **Always on** al **su** e selezionare **salvare**.

    ![Screenshot dell'interfaccia utente del servizio App di Azure Abilita modifica analysis](./media/change-analysis/change-analysis-on.png)

    Se **analisi modifica** è abilitato, sarà in grado di rilevare le modifiche a livello di risorsa. Se **rileva modifiche di codice** è abilitata, verrà anche vedere i file di distribuzione e le modifiche di configurazione del sito. Abilitazione **Always on** ottimizzerà la modifica l'analisi delle prestazioni, ma può comportare costi aggiuntivi da una prospettiva di fatturazione.

5.  Dopo che tutto ciò che è abilitato, selezionando **diagnosi e risoluzione dei problemi** > **disponibilità e prestazioni** > **blocchi applicazione** sarà possibile accedere l'esperienza di analisi di modifica. Il grafico verrà summerize il tipo di modifiche che si sono verificati nel corso del tempo con i dettagli su queste modifiche:

     ![Screenshot della visualizzazione di modifica delle differenze](./media/change-analysis/change-view.png)

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="unable-to-fetch-change-analysis-information"></a>Impossibile recuperare le informazioni di analisi della modifica.

Si tratta di un problema temporaneo durante l'esperienza di anteprima corrente. La soluzione alternativa prevede l'impostazione di un tag nascosto nell'app web e quindi ad aggiornare la pagina:

   ![Schermata di modifica nascosta tag](./media/change-analysis/hidden-tag.png)

Per impostare i tag nascosti tramite PowerShell:

1. Aprire Azure Cloud Shell:

    ![Screenshot della modifica Azure Cloud Shell](./media/change-analysis/cloud-shell.png)

2. Modificare il tipo di shell per PowerShell:

   ![Screenshot della modifica Azure Cloud Shell](./media/change-analysis/choose-powershell.png)

3. Eseguire il comando seguente:

```powershell
$webapp=Get-AzWebApp -Name <name_of_your_webapp>
$tags = $webapp.Tags
$tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
```

> [!NOTE]
> Dopo aver aggiunto il tag nascosto, è comunque necessario inizialmente attendere fino a 4 ore per essere in grado di visualizzare prima di tutto le modifiche. Ciò è dovuto il freqeuncy entro 4 ore che usa il servizio di analisi di modifica per analizzare l'app web pur limitando l'impatto sulle prestazioni dell'analisi.

## <a name="next-steps"></a>Passaggi successivi

- Migliorare il monitoraggio dei servizi App di Azure [abilitando le funzionalità di Application Insights](azure-web-apps.md) di monitoraggio di Azure.
- Migliorare la comprensione del [Graph di Azure Resource](https://docs.microsoft.com/azure/governance/resource-graph/overview) in modo da modificare analisi applicazione di monitoraggio di Azure power. 
