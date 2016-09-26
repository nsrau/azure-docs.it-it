<properties 
	pageTitle="Test di un runbook in Automazione di Azure | Microsoft Azure"
	description="Prima di pubblicare un runbook in automazione di Azure, è possibile eseguirne il test per assicurarsi che funzioni come previsto. In questo articolo viene descritto come verificare un runbook e visualizzarne l'output."
	services="automation"
	documentationCenter=""
	authors="mgoedtel"
	manager="jwhit"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/12/2016"
	ms.author="magoedte;bwren" />

# Test di un runbook in Automazione di Azure
Quando si testa un Runbook, viene eseguita la [Versione bozza](automation-creating-importing-runbook.md#publishing-a-runbook) e le azioni eseguite vengono completate. Non viene creata alcuna cronologia dei processi, ma i flussi di [Output](automation-runbook-output-and-messages.md#output-stream) e di [Avviso ed errore](automation-runbook-output-and-messages.md#message-streams) vengono visualizzati nel pannello output del Test. I messaggi per il [Flusso dettagliato](automation-runbook-output-and-messages.md#message-streams) vengono visualizzati solo se il pannello di Output [$VerbosePreference variabile](automation-runbook-output-and-messages.md#preference-variables) è impostato su Continue.

Anche se si esegue la versione bozza, il runbook ancora esegue normalmente il flusso di lavoro ed realizza qualsiasi azione su risorse nell'ambiente. Per questo motivo, è necessario testare solo runbook a risorse non di produzione.

La procedura per verificare ogni [tipo di runbook](automation-runbook-types.md) è la stessa e non vi è alcuna differenza nel test tra l'editor di testo e dell'editor grafico nel portale di Azure.


## Per testare un runbook nel portale di Azure

È possibile usare qualsiasi [tipo di runbook](automation-runbook-types.md) nel portale di Azure.

1. Aprire la versione bozza del runbook in [editor testuale](automation-editing-a-runbook.md#Portal) o [editor grafico](automation-graphical-authoring-intro.md).
2. Far clic sul pulsante **Test** per aprire il pannello del Test.
3. Se il runbook dispone di parametri, essi verranno elencati nel pannello sinistro in cui è possibile fornire valori da utilizzare per il test.
4. Se si desidera eseguire il test su un [Ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md), allora modificare le **Impostazioni di esecuzione** in **Ruolo di lavoro ibrido** e selezionare il nome del gruppo di destinazione. In caso contrario, mantenere l'impostazione predefinita **Azure** per eseguire il test nel cloud.
5. Fare clic sul pulsante **Start** per avviare il test.
6. Se il runbook è [Flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) o [Grafico](automation-runbook-types.md#graphical-runbooks), è possibile arrestarlo o sospenderlo, mentre viene testato utilizzando i pulsanti sotto il pannello di Output. Quando si sospende il runbook, esso completa l'attività corrente prima di essere sospeso. Una volta che il runbook viene sospeso, è possibile arrestarlo o riavviarlo.
7. Esaminare l'output dal runbook nel pannello di output.


## Passaggi successivi

- Per informazioni su come creare o importare un runbook, vedere [Creazione o importazione di un runbook in Automazione di Azure](automation-creating-importing-runbook.md)
- Per altre informazioni sulla creazione grafica, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).
- Per iniziare a usare i runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).
- Per altre informazioni sulla configurazione dei runbok per restituire messaggi di stato ed errori e per conoscere le operazioni consigliate, vedere [Output di runbook e messaggi in automazione di Azure](automation-runbook-output-and-messages.md)

<!---HONumber=AcomDC_0914_2016-->