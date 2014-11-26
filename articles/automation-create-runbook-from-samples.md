<properties linkid="automation-create-runbook-from-samples" urlDisplayName="Get Started with Azure Automation" pageTitle="Get Started with Azure Automation" metaKeywords="" description="Learn how to import and run an automation job in Azure." metaCanonical="" services="automation" documentationCenter="" title="Get Started with Azure Automation" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bwren" />

# Introduzione all'automazione di Azure

L'automazione di Microsoft Azure offre agli sviluppatori la possibilità di automatizzare le attività manuali, a esecuzione prolungata, soggette a errori e ripetute di frequente comunemente eseguite negli ambienti cloud. È possibile creare, monitorare, gestire e distribuire risorse nell'ambiente Azure tramite Runbook, che in realtà non sono altro che flussi di lavoro di Windows PowerShell. Per ulteriori informazioni sull'automazione, vedere la [guida introduttiva all'automazione][guida introduttiva all'automazione].

Questa esercitazione illustra i passaggi per importare un Runbook "Hello World" di esempio in Automazione di Azure, eseguirlo e quindi visualizzarne l'output.

> [WACOM.NOTE] Per informazioni su come automatizzare le operazioni di Azure usando i [cmdlet di Azure PowerShell][cmdlet di Azure PowerShell], vedere il post di blog [Automazione di Azure: autenticazione in Azure mediante Azure Active Directory][Automazione di Azure: autenticazione in Azure mediante Azure Active Directory].

## Esempi e Runbook di utilità

Il team di automazione di Azure ha creato diversi esempi di Runbook per semplificare le attività iniziali con l'automazione. In questi esempi vengono trattati i concetti di base dell'automazione, allo scopo di fornire informazioni su come scrivere Runbook personalizzati.

Il team di automazione ha inoltre creato dei Runbook di utilità, che è possibile utilizzare come elementi di base per attività di automazione di più ampio respiro.

> [WACOM.NOTE] È consigliabile scrivere Runbook riutilizzabili, modulari e di piccole dimensioni. È inoltre consigliabile creare Runbook di utilità personalizzati per scenari di utilizzo comuni dopo aver acquisito familiarità con Automazione.

È possibile visualizzare e scaricare Runbook di utilità e di esempio del team Automazione nell'area [Script Center][Script Center].

## Community, commenti e suggerimenti sull'automazione

Anche i Runbook della community e di altri team Microsoft sono pubblicati nell'area [Script Center][1].

**Gli utenti sono invitati a fornire commenti e suggerimenti.** Se si è in cerca di un modulo di integrazione o di una soluzione Runbook di Automazione, inviare una richiesta di script in Script Center. Se si desidera condividere idee per nuove funzionalità di automazione, inviarle nella sezione [User Voice][User Voice].

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Procedure generali per questa esercitazione

1.  [Iscrizione per l'anteprima di Automazione][Iscrizione per l'anteprima di Automazione]
2.  [Download di un Runbook di esempio][Download di un Runbook di esempio]
3.  [Importazione, esecuzione e visualizzazione dell'output del Runbook di esempio][Importazione, esecuzione e visualizzazione dell'output del Runbook di esempio]

## <a name="preview"></a>Iscrizione per l'anteprima di Automazione di Azure

Per iniziare a usare Automazione, è necessario disporre di una sottoscrizione Azure attiva con la funzionalità di anteprima di Automazione di Azure abilitata.

-   Nella pagina **Anteprima funzionalità** fare clic su **Prova**.

    ![Abilitazione dell'anteprima][Abilitazione dell'anteprima]

## <a name="download-sample"></a>Download di un Runbook di esempio da Script Center

1.  Passare a [Script Center][Script Center] e quindi fare clic su **Hello World for Azure Automation**.

2.  Fare clic sul nome del file **Write-HelloWorld.ps1** accanto a **Download** e quindi salvare il file nel computer.

## <a name="import-sample"></a>Importazione, esecuzione e visualizzazione del Runbook di esempio in Automazione di Azure

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Nel portale di gestione fare clic su **Create an Automation Account**.

    > [WACOM.NOTE] Se è già stato creato un account di Automazione, andare al passaggio 4.

    ![Creazione di un account][Creazione di un account]

3.  Nella pagina **Add a New Automation Account** immettere un nome per l'account e quindi fare clic sul segno di spunta.

    ![Aggiunta di un nuovo account][Aggiunta di un nuovo account]

4.  Nella pagina **Automation** fare clic sul nuovo account appena creato.

    ![Nuovo account][Nuovo account]

5.  Fare clic su **RUNBOOKS**.

    ![Scheda Runbooks][Scheda Runbooks]

6.  Fare clic su **IMPORT**.

    ![Importazione][Importazione]

7.  Passare allo script **Write-HelloWorld.ps1** scaricato e quindi fare clic sul segno di spunta.

    ![Esplorazione][Esplorazione]

8.  Fare clic su **Write-HelloWorld**.

    ![Runbook importato][Runbook importato]

9.  Fare clic su **AUTHOR** e quindi su **DRAFT**. Per questo Runbook non è necessario apportare alcuna modifica.

    A questo punto è possibile visualizzare il contenuto di **Write-HelloWorld.ps1**. È possibile modificare il contenuto di un Runbook in modalità Bozza.

    ![Bozza di autore][Bozza di autore]

10. Fare clic su **PUBLISH** per alzare di livello il Runbook in modo che sia pronto per l'utilizzo in produzione.

    ![Publish][Publish]

11. Quando viene richiesto se salvare e pubblicare il Runbook, fare clic su **Yes**.

    ![Richiesta di salvataggio e pubblicazione][Richiesta di salvataggio e pubblicazione]

12. Fare clic su **PUBLISHED** e quindi su **START**.

    ![Opzione Published][Opzione Published]

13. Nella pagina **Specify the runbook parameter values** immettere un valore per **Name**, che verrà utilizzato come parametro di input per lo script Write-HelloWorld.ps1, quindi fare clic sul segno di spunta.

    ![Parametri del Runbook][Parametri del Runbook]

14. Fare clic su **JOBS** per controllare lo stato del Runbook appena avviato e quindi sul timestamp nella colonna **JOB START** per visualizzare il riepilogo del processo.

    ![Stato del Runbook][Stato del Runbook]

15. Nella pagina **SUMMARY** è possibile visualizzare il riepilogo, i parametri di input e l'output del processo.

    ![Riepilogo del Runbook][Riepilogo del Runbook]

# Gestione dei servizi di Azure da un Runbook

L'esempio precedente illustra un Runbook semplice che non gestisce i servizi di Azure. I [cmdlet di Azure][cmdlet di Azure PowerShell] richiedono l'autenticazione in Azure. È possibile seguire le istruzioni riportate nel post di blog [Automazione di Azure: autenticazione in Azure mediante Azure Active Directory][Automazione di Azure: autenticazione in Azure mediante Azure Active Directory] per configurare la sottoscrizione Azure per la gestione tramite Automazione di Azure.

# Vedere anche

-   [Panoramica dell'automazione][Panoramica dell'automazione]
-   [Guida alla creazione di Runbook][Guida alla creazione di Runbook]
-   [Forum sull'automazione][Forum sull'automazione]
-   [Automazione di Azure: autenticazione in Azure mediante Azure Active Directory][Automazione di Azure: autenticazione in Azure mediante Azure Active Directory]

  [guida introduttiva all'automazione]: http://go.microsoft.com/fwlink/p/?LinkId=392861
  [cmdlet di Azure PowerShell]: http://msdn.microsoft.com/it-it/library/jj156055.aspx
  [Automazione di Azure: autenticazione in Azure mediante Azure Active Directory]: http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/
  [Script Center]: http://go.microsoft.com/fwlink/p/?LinkId=393029
  [1]: http://go.microsoft.com/fwlink/?LinkID=391681
  [User Voice]: http://feedback.windowsazure.com/forums/34192--general-feedback
  [Iscrizione per l'anteprima di Automazione]: #preview
  [Download di un Runbook di esempio]: #download-sample
  [Importazione, esecuzione e visualizzazione dell'output del Runbook di esempio]: #import-sample
  [Abilitazione dell'anteprima]: ./media/automation/automation_00_EnablePreview.png
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Creazione di un account]: ./media/automation/automation_01_CreateAccount.png
  [Aggiunta di un nuovo account]: ./media/automation/automation_02_addnewautoacct.png
  [Nuovo account]: ./media/automation/automation_03_NewAutoAcct.png
  [Scheda Runbooks]: ./media/automation/automation_04_RunbooksTab.png
  [Importazione]: ./media/automation/automation_05_Import.png
  [Esplorazione]: ./media/automation/automation_06_Browse.png
  [Runbook importato]: ./media/automation/automation_07_ImportedRunbook.png
  [Bozza di autore]: ./media/automation/automation_08_AuthorDraft.png
  [Publish]: ./media/automation/automation_085_Publish.png
  [Richiesta di salvataggio e pubblicazione]: ./media/automation/automation_09_SavePubPrompt.png
  [Opzione Published]: ./media/automation/automation_10_PublishStart.png
  [Parametri del Runbook]: ./media/automation/automation_11_RunbookParams.png
  [Stato del Runbook]: ./media/automation/automation_12_RunbookStatus.png
  [Riepilogo del Runbook]: ./media/automation/automation_13_RunbookSummary_callouts.png
  [Panoramica dell'automazione]: http://go.microsoft.com/fwlink/p/?LinkId=392860
  [Guida alla creazione di Runbook]: http://go.microsoft.com/fwlink/p/?LinkID=301740
  [Forum sull'automazione]: http://go.microsoft.com/fwlink/p/?LinkId=390561
