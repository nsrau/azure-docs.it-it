---
title: Ruolo di lavoro ibrido per runbook Linux di Automazione di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni sull'installazione di un ruolo di lavoro ibrido per runbook di Automazione di Azure che consente di eseguire i runbook sui computer Linux nel data center locale o nell'ambiente cloud.
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/07/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: d01d9b8723e321ca0f2f9073d5b99b984e30d971
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

---

# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Come distribuire un ruolo di lavoro ibrido per runbook di Linux
I runbook in Automazione di Azure non possono accedere alle risorse in altri cloud o negli ambienti locali perché vengono eseguiti nel cloud di Azure.  La funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure consente di eseguire runbook direttamente sul computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer designati.  

Questa funzionalità è illustrata nell'immagine seguente:<br>  

![Panoramica di Hybrid Runbook Workers](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Per una panoramica tecnica sul ruolo di lavoro ibrido per runbook, vedere [Panoramica dell'architettura di Automazione](automation-offering-get-started.md#automation-architecture-overview). Esaminare le informazioni seguenti per quanto riguarda i [requisiti hardware e software](automation-offering-get-started.md#hybrid-runbook-worker) e le [informazioni per preparare la rete](automation-offering-get-started.md#network-planning) prima di iniziare la distribuzione di un ruolo di lavoro ibrido per runbook.  Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione dei runbook per Hybrid Runbook Workers](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud.     

## <a name="hybrid-runbook-worker-groups"></a>Gruppi di computer di lavoro runbook ibridi
Ogni computer di lavoro runbook ibrido è un membro di un gruppo di computer di lavoro runbook ibridi che è possibile specificare quando si installa l'agente.  Un gruppo può includere un solo agente, ma è possibile installarvi più agenti per garantire una disponibilità elevata.

Quando si avvia un runbook in un ruolo di lavoro ibrido per runbook, è necessario specificare il gruppo in cui verrà eseguito.  I membri del gruppo determinano il ruolo di lavoro che gestirà la richiesta.  Non è possibile scegliere un computer di lavoro specifico.

## <a name="installing-linux-hybrid-runbook-worker"></a>Installazione del ruolo di lavoro ibrido per runbook di Linux
La procedura per installare e configurare manualmente un ruolo di lavoro ibrido per runbook in un computer Linux è molto semplice.   È necessario abilitare la soluzione **Ruolo di lavoro ibrido per runbook di Automazione** nell'area di lavoro di OMS e quindi eseguire un set di comandi per registrare il computer come ruolo di lavoro e aggiungerlo a un gruppo nuovo o esistente. 

Prima di procedere, è necessario annotare l'area di lavoro di Log Analytics a cui è collegato l'account di Automazione e anche la chiave primaria per l'account di Automazione.  È possibile trovare entrambi gli elementi nel portale, selezionando l'account di Automazione, **Area di lavoro** per l'ID dell'area di lavoro, quindi scegliendo **Chiavi** per la chiave primaria.  

1.  Abilitare la soluzione "Ruolo di lavoro ibrido per runbook di Automazione" in OMS. A tale scopo, è possibile:

   1. Dalla Raccolta soluzioni nel [portale di OMS](https://mms.microsoft.com) abilitare la soluzione **Ruolo di lavoro ibrido per runbook di Automazione**
   2. Eseguire il cmdlet seguente:

        ```powershell
         $null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

2.  Eseguire il comando seguente modificano i valori per i parametri *-w* e *-k* sul computer Linux.
    
    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <AutomationSharedKey> --groupname <hybridgroupname> -e <automationendpoint>
    ```
3. Dopo il completamento del comando, il pannello Gruppi di ruoli di lavoro ibridi nel portale di Azure mostrerà il nuovo gruppo e il numero di membri oppure, nel caso di un gruppo esistente, il numero di membri verrà incrementato.  È possibile selezionare il gruppo nell'elenco nel pannello **Gruppi di ruoli di lavoro ibridi** e selezionare il riquadro **Ruoli di lavoro per runbook**.  Nel pannello **Ruoli di lavoro per runbook** sono elencati i membri del gruppo.  

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [Esecuzione dei runbook per Hybrid Runbook Workers](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud.
* Per istruzioni su come rimuovere i ruoli di lavoro ibridi per runbook, vedere [Remove Azure Automation Hybrid Runbook Workers](automation-remove-hrw.md) (Rimuovere i ruoli di lavoro ibridi per runbook di Automazione di Azure)
