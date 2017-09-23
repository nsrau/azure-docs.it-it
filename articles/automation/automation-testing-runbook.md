---
title: Test di un runbook in Automazione di Azure | Documentazione Microsoft
description: "Prima di pubblicare un runbook in automazione di Azure, è possibile eseguirne il test per assicurarsi che funzioni come previsto.  In questo articolo viene descritto come verificare un runbook e visualizzarne l'output."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 7f7db785-52c0-4613-aa12-b02fd32a5182
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte;bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5186eb8f1732d533cbceb397b4d8b5224ad773cd
ms.contentlocale: it-it
ms.lasthandoff: 11/17/2016

---
# <a name="testing-a-runbook-in-azure-automation"></a>Test di un runbook in Automazione di Azure
Quando si testa un Runbook, viene eseguita la [Versione bozza](automation-creating-importing-runbook.md#publishing-a-runbook) e le azioni eseguite vengono completate. Non viene creata alcuna cronologia dei processi, ma nel pannello di output del test vengono visualizzati i flussi di [output](automation-runbook-output-and-messages.md#output-stream) e di [avviso ed errore](automation-runbook-output-and-messages.md#message-streams). I messaggi per il [flusso dettagliato](automation-runbook-output-and-messages.md#message-streams) vengono visualizzati solo se nel pannello di output la [variabile $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) è impostata su Continua.

Anche se si esegue la versione bozza, il runbook ancora esegue normalmente il flusso di lavoro ed realizza qualsiasi azione su risorse nell'ambiente. Per questo motivo, è necessario testare solo runbook a risorse non di produzione.

La procedura per verificare ogni [tipo di runbook](automation-runbook-types.md) è la stessa e non vi è alcuna differenza nel test tra l'editor di testo e dell'editor grafico nel portale di Azure.  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>Per testare un runbook nel portale di Azure
È possibile usare qualsiasi [tipo di runbook](automation-runbook-types.md) nel portale di Azure.

1. Aprire la versione bozza del runbook in un [editor di testo](automation-edit-textual-runbook.md) o in un [editor grafico](automation-graphical-authoring-intro.md).
2. Far clic sul pulsante **Test** per aprire il pannello del Test.
3. Se il runbook dispone di parametri, essi verranno elencati nel pannello sinistro in cui è possibile fornire valori da utilizzare per il test.
4. Se si desidera eseguire il test su un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md), specificare per l'opzione **Impostazioni di esecuzione** il valore **Ruolo di lavoro ibrido** e selezionare il nome del gruppo di destinazione.  In caso contrario, mantenere l'impostazione predefinita **Azure** per eseguire il test nel cloud.
5. Fare clic sul pulsante **Start** per avviare il test.
6. Se il runbook è un [flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) o è [grafico](automation-runbook-types.md#graphical-runbooks), è possibile arrestarlo o sospenderlo, mentre viene testato usando i pulsanti presenti sotto il pannello di output. Quando si sospende il runbook, esso completa l'attività corrente prima di essere sospeso. Una volta che il runbook viene sospeso, è possibile arrestarlo o riavviarlo.
7. Esaminare l'output dal runbook nel pannello di output.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni su come creare o importare un runbook, vedere [Creazione o importazione di un runbook in Automazione di Azure](automation-creating-importing-runbook.md)
* Per altre informazioni sulla creazione grafica, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md)
* Per iniziare a usare i runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md)
* Per altre informazioni sulla configurazione dei runbok per restituire messaggi di stato ed errori e per conoscere le operazioni consigliate, vedere [Output di runbook e messaggi in automazione di Azure](automation-runbook-output-and-messages.md)


