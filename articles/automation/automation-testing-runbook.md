<properties 
	pageTitle="Test di un runbook in Automazione di Azure"
	description="Prima di pubblicare un runbook in automazione di Azure, è possibile eseguirne il test per assicurarsi che funzioni come previsto. In questo articolo viene descritto come verificare un runbook e visualizzarne l'output."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/23/2015"
	ms.author="bwren" />

# Test di un runbook in Automazione di Azure
Quando si testa un Runbook, viene eseguita la [Versione bozza](automation-creating-importing-runbook.md#publishing-a-runbook) e le azioni eseguite vengono completate. Non viene creata alcuna cronologia dei processi, ma i flussi di [Output](automation-runbook-output-and-messages.md#output-stream) e di [Avviso ed errore](automation-runbook-output-and-messages.md#message-streams) vengono visualizzati nel pannello output del Test. I messaggi per il [Flusso dettagliato](automation-runbook-output-and-messages.md#message-streams) vengono visualizzati solo se il pannello di Output [$VerbosePreference variabile](automation-runbook-output-and-messages.md#preference-variables) è impostato su Continue.

Anche se si esegue la versione bozza, il runbook ancora esegue normalmente il flusso di lavoro ed realizza qualsiasi azione su risorse nell'ambiente. Per questo motivo, è necessario testare solo runbook a risorse non di produzione.

La procedura per verificare ogni [tipo di runbook](automation-runbook-types.md) è la stessa e non vi è alcuna differenza nel test tra l'editor di testo e dell'editor grafico nel portale di anteprima di Azure.


## Per testare un runbook nel portale di anteprima di Azure

È possibile utilizzare qualsiasi [tipo di runbook](automation-runbook-types.md) nel portale di anteprima di Azure.

1. Aprire la versione bozza del runbook in [editor testuale](automation-editing-a-runbook#Portal) o [editor grafico](automation-graphical-authoring-intro.md).
2. Far clic sul pulsante **Test** per aprire il pannello del Test.
3. Se il runbook dispone di parametri, essi verranno elencati nel pannello sinistro in cui è possibile fornire valori da utilizzare per il test.
4. Se si desidera eseguire il test su un [Ruolo di lavoro ibrido per runbook](automation-hybrid), allora modificare le **Impostazioni di esecuzione** in **Ruolo di lavoro ibrido** e selezionare il nome del gruppo di destinazione. In caso contrario, mantenere l'impostazione predefinita **Azure** per eseguire il test nel cloud.
5. Fare clic sul pulsante **Start** per avviare il test.
6. Se il runbook è [Flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) o [Grafico](automation-runbook-types.md#graphical-runbooks), è possibile arrestarlo o sospenderlo, mentre viene testato utilizzando i pulsanti sotto il pannello di Output. Quando si sospende il runbook, esso completa l'attività corrente prima di essere sospeso. Una volta che il runbook viene sospeso, è possibile arrestarlo o riavviarlo.
7. Esaminare l'output dal runbook nel pannello di output.



## Per testare un runbook nel portale di Azure

È possibile utilizzare solo i [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) nel portale di Azure.


1. [Aprire la versione bozza del runbook](automation-edit-textual-runbook.md#to-edit-a-runbook-with-the-azure-portal).
2. Scegliere il pulsante **Test** per avviare il test. Se il runbook dispone di parametri, si riceverà una finestra di dialogo per fornire i valori da utilizzare per il test.
6. È possibile arrestare o sospendere il runbook mentre viene testato utilizzando i pulsanti presenti sotto il pannello di Output. Quando si sospende il runbook, esso completa l'attività corrente prima di essere sospeso. Una volta che il runbook viene sospeso, è possibile arrestarlo o riavviarlo.
7. Esaminare l'output dal runbook nel pannello di output.


## Articoli correlati

- [Creazione o importazione di un runbook in automazione di Azure](automation-creating-importing-runbook.md)
- [Runbook grafici in Automazione di Azure](automation-graphical-authoring-intro.md)
- [Modifica dei runbook testuali di automazione di Azure](automation-edit-textual-runbook.md)
- [Output di runbook e messaggi in automazione di Azure](automation-runbook-output-and-messages.md)

<!---HONumber=Oct15_HO3-->