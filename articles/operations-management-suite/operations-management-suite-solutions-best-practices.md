---
title: Procedure consigliate per soluzioni OMSManagement | Documentazione Microsoft
description: 
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: b3d07ad3164609a5628c0d9805de55a32870ab94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-creating-management-solutions-in-operations-management-suite-oms-preview"></a>Procedure consigliate per la creazione di soluzioni di gestione in Operations Management Suite (OMS) (anteprima)
> [!NOTE]
> Questa è una documentazione preliminare per la creazione di soluzioni di gestione in OMS attualmente disponibili in versione di anteprima. Qualsiasi schema descritto di seguito è soggetto a modifiche.  

Questo articolo riporta le procedure consigliate per la [creazione di una soluzione di gestione](operations-management-suite-solutions-solution-file.md) in Operations Management Suite (OMS).  Queste informazioni verranno aggiornate quando saranno identificate procedure consigliate aggiuntive.

## <a name="data-sources"></a>Origini dati
- È possibile [configurare le origini dati con un modello di Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), ma non devono essere incluse nel file della soluzione.  Il motivo è che la configurazione delle origini dati non è attualmente idempotente, vale a dire che la soluzione potrebbe sovrascrivere la configurazione esistente nell'area di lavoro dell'utente.<br><br>Ad esempio la soluzione potrebbe richiedere eventi di avviso e di errore dal registro eventi dell'applicazione.  Se si specifica questo come origine dati nella soluzione, si rischia di rimuovere eventi informativi, se l'utente ha configurato questa impostazione nella sua area di lavoro.  Se sono stati inclusi tutti gli eventi, potrebbero essere raccolti troppi eventi informativi nell'area di lavoro dell'utente.

- Se la soluzione richiede dati da una delle origini dati standard, è necessario definire questo come prerequisito.  Dichiarare nella documentazione che il cliente deve configurare l'origine dati da solo.  
- Aggiungere un messaggio di [verifica del flusso di dati](../log-analytics/log-analytics-view-designer-tiles.md) alle viste della soluzione per indicare all'utente le origini dati che devono essere configurate per la raccolta dei dati richiesti.  Questo messaggio viene mostrato nel riquadro della vista quando i dati necessari non vengono trovati.


## <a name="runbooks"></a>Runbook
- Aggiungere una [pianificazione di automazione](../automation/automation-schedules.md) per ogni runbook della soluzione che deve essere eseguito in modo pianificato.
- Includere nella soluzione il [modulo IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) che dovrà essere usato da runbook per la scrittura dei dati nel repository di Log Analytics.  Configurare la soluzione in modo che [faccia riferimento](operations-management-suite-solutions-solution-file.md#solution-resource) a questa risorsa affinché rimanga se la soluzione viene rimossa.  In questo modo più soluzioni possono condividere il modulo.
- Utilizzare le [variabili di automazione](../automation/automation-schedules.md) per fornire valori alla soluzione che gli utenti potranno modificare in seguito.  Anche se la soluzione è configurata per contenere la variabile, il suo valore può comunque essere modificato.

## <a name="views"></a>Visualizzazioni
- Tutte le soluzioni devono includere una singola vista che viene mostrata nel portale dell'utente.  Questa vista può contenere più [parti della visualizzazione](../log-analytics/log-analytics-view-designer-parts.md) per illustrare i diversi set di dati.
- Aggiungere un messaggio di [verifica del flusso di dati](../log-analytics/log-analytics-view-designer-tiles.md) alle viste della soluzione per indicare all'utente le origini dati che devono essere configurate per la raccolta dei dati richiesti.
- Configurare la soluzione in modo che [contenga](operations-management-suite-solutions-solution-file.md#solution-resource) la vista, affinché venga rimossa se la soluzione viene rimossa.

## <a name="alerts"></a>Avvisi
- Definire l'elenco dei destinatari come parametro nel file della soluzione, in modo che l'utente possa definirli quando installa la soluzione.
- Configurare la soluzione in modo che [faccia riferimento](operations-management-suite-solutions-solution-file.md#solution-resource) alle regole di avviso, affinché gli utenti possano modificare la loro configurazione.  Potrebbe voler apportare modifiche come cambiare l'elenco dei destinatari, cambiare la soglia di avviso o disabilitare la regola di avviso. 


## <a name="next-steps"></a>Passaggi successivi
* Illustra il processo di base di [progettazione e creazione di una soluzione di gestione](operations-management-suite-solutions-creating.md).
* Informazioni su come [creare un file di soluzione](operations-management-suite-solutions-solution-file.md).
* [Aggiungere ricerche salvate e avvisi](operations-management-suite-solutions-resources-searches-alerts.md) alla soluzione di gestione.
* [Aggiungere viste](operations-management-suite-solutions-resources-views.md) alla soluzione di gestione.
* [Aggiungere runbook di automazione e altre risorse](operations-management-suite-solutions-resources-automation.md) alla soluzione di gestione.

