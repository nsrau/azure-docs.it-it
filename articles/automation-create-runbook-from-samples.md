<properties linkid="automation-create-runbook-from-samples" urlDisplayName="Get Started with Azure Automation" pageTitle="Get Started with Azure Automation" metaKeywords="" description="Learn how to import and run an automation job in Azure." metaCanonical="" services="automation" documentationCenter="" title="Get Started with Azure Automation" authors="" solutions="" manager="" editor="" />

Introduzione all'automazione di Azure
=====================================

In questa esercitazione vengono illustrati i passaggi per importare ed eseguire un Runbook di automazione in Microsoft Azure.

L'automazione di Microsoft Azure offre agli sviluppatori la possibilità di automatizzare le attività manuali, a esecuzione prolungata, soggette a errori e ripetute di frequente comunemente eseguite negli ambienti cloud. È possibile creare, monitorare, gestire e distribuire risorse nell'ambiente Azure tramite Runbook, che in realtà non sono altro che flussi di lavoro di Windows PowerShell. Per ulteriori informazioni sull'automazione, vedere la [guida introduttiva all'automazione](http://go.microsoft.com/fwlink/p/?LinkId=392861).

In questa esercitazione vengono illustrati i passaggi per importare un Runbook di esempio in automazione di Azure, eseguirlo e quindi visualizzarne l'output.

Esempi e Runbook di utilità
---------------------------

Il team di automazione di Azure ha creato diversi esempi di Runbook per semplificare le attività iniziali con l'automazione. In questi esempi vengono trattati i concetti di base dell'automazione, allo scopo di fornire informazioni su come scrivere Runbook personalizzati.

Il team di automazione ha inoltre creato dei Runbook di utilità, che è possibile utilizzare come elementi di base per attività di automazione di più ampio respiro.

> [WACOM.NOTE] È consigliabile scrivere Runbook riutilizzabili, modulari e di piccole dimensioni. È inoltre consigliabile creare Runbook di utilità personalizzati per scenari di utilizzo comuni, dopo aver acquisito familiarità con l'automazione.

È possibile visualizzare e scaricare gli esempi e i Runbook di utilità del team di automazione nella sezione [Script Center](http://go.microsoft.com/fwlink/p/?LinkId=393029).

Community, commenti e suggerimenti sull'automazione
---------------------------------------------------

Anche i Runbook della community e di altri team Microsoft sono pubblicati nella sezione [Script Center](http://go.microsoft.com/fwlink/?LinkID=391681).

**Gli utenti sono invitati a fornire commenti e suggerimenti.** Se si è in cerca di una soluzione Runbook o di un modulo di integrazione di PowerShell, inviare una richiesta di script in Script Center. Se si desidera condividere idee per nuove funzionalità di automazione, inviarle nella sezione [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback).

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

Download di un Runbook di esempio da Script Center
--------------------------------------------------

1.  Passare a [Script Center](http://go.microsoft.com/fwlink/p/?LinkId=393029) e quindi fare clic su **Hello World for Azure Automation**.

2.  Fare clic sul nome del file **Write-HelloWorld.ps1** accanto a **Download** e quindi salvare il file nel computer.

Importazione del Runbook di esempio in Azure
--------------------------------------------

1.  Accedere al [portale di gestione di Azure](http://manage.windowsazure.com).

2.  Nel portale di gestione fare clic su **Create an Automation Account**.

    > [WACOM.NOTE] Se è già stato creato un account di automazione, andare al passaggio 4.

    ![Creazione di un account](./media/automation/automation_01_CreateAccount.png)

3.  Nella pagina **Add a New Automation Account** immettere un nome per l'account e quindi fare clic sul segno di spunta.

    ![Aggiunta di un nuovo account](./media/automation/automation_02_addnewautoacct.png)

4.  Nella pagina **Automation** fare clic sul nuovo account appena creato.

    ![Nuovo account](./media/automation/automation_03_NewAutoAcct.png)

5.  Fare clic su **RUNBOOKS**.

    ![Scheda Runbooks](./media/automation/automation_04_RunbooksTab.png)

6.  Fare clic su **IMPORT**.

    ![Importazione](./media/automation/automation_05_Import.png)

7.  Passare allo script **Write-HelloWorld.ps1** scaricato e quindi fare clic sul segno di spunta.

    ![Esplorazione](./media/automation/automation_06_Browse.png)

8.  Fare clic su **Write-HelloWorld**.

    ![Runbook importato](./media/automation/automation_07_ImportedRunbook.png)

9.  Fare clic su **AUTHOR** e quindi su **DRAFT**. Per questo Runbook, non è necessario apportare alcuna modifica.

    A questo punto è possibile visualizzare il contenuto di **Write-HelloWorld.ps1**. È possibile modificare il contenuto di un Runbook in modalità Bozza.

    ![Bozza di autore](./media/automation/automation_08_AuthorDraft.png)

10. Fare clic su **PUBLISH** per alzare di livello il Runbook in modo che sia pronto per l'utilizzo in produzione.

    ![Opzione Publish](./media/automation/automation_085_Publish.png)

11. Quando viene richiesto se salvare e pubblicare il Runbook, fare clic su **Yes**.

    ![Richiesta di salvataggio e pubblicazione](./media/automation/automation_09_SavePubPrompt.png)

12. Fare clic su **PUBLISHED** e quindi su **START**.

    ![Opzione Published](./media/automation/automation_10_PublishStart.png)

13. Nella pagina **Specify the runbook parameter values** immettere un valore per **Name**, che verrà utilizzato come parametro di input per lo script Write-HelloWorld.ps1, quindi fare clic sul segno di spunta.

    ![Parametri del Runbook](./media/automation/automation_11_RunbookParams.png)

14. Fare clic su **JOBS** per controllare lo stato del Runbook appena avviato e quindi sul timestamp nella colonna **JOB START** per visualizzare il riepilogo del processo.

    ![Stato del Runbook](./media/automation/automation_12_RunbookStatus.png)

15. Nella pagina **SUMMARY** è possibile visualizzare il riepilogo, i parametri di input e l'output del processo.

    ![Riepilogo del Runbook](./media/automation/automation_13_RunbookSummary_callouts.png)

Vedere anche
------------

-   [Panoramica dell'automazione](http://go.microsoft.com/fwlink/p/?LinkId=392860)
-   [Guida alla creazione di Runbook](http://go.microsoft.com/fwlink/p/?LinkID=301740)
-   [Forum sull'automazione](http://go.microsoft.com/fwlink/p/?LinkId=390561)

