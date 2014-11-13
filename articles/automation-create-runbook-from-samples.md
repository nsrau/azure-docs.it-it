<properties urlDisplayName="Get Started with Azure Automation" pageTitle="Introduzione all'automazione di Azure" metaKeywords="" description="Informazioni sull'importazione e sull'esecuzione di un processo di automazione in Azure." metaCanonical="" services="automation" documentationCenter="" title="Introduzione all'automazione di Azure" authors="bwren" solutions="" manager="stevenka" editor="" />

<tags ms.service="automation" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bwren" />

# Introduzione all'automazione di Azure

L'automazione di Microsoft Azure offre agli sviluppatori la possibilità di automatizzare le attività manuali, a esecuzione prolungata, soggette a errori e ripetute di frequente comunemente eseguite negli ambienti cloud. È possibile creare, monitorare, gestire e distribuire risorse nell'ambiente Azure tramite Runbook, che in realtà non sono altro che flussi di lavoro di Windows PowerShell. Per altre informazioni sull'automazione, vedere la [guida introduttiva all'automazione][guida introduttiva all'automazione].

In questa esercitazione vengono illustrati i passaggi per importare un Runbook "Hello World" di esempio nell'automazione di Azure, eseguirlo e quindi visualizzarne l'output.

> [WACOM.NOTE] Per informazioni su come automatizzare operazioni di Azure usando i [Cmdlet PowerShell per Azure][Cmdlet PowerShell per Azure], vedere il post di blog sull'argomento [Automazione di Azure: autenticazione in Azure usando Azure Active Directory][Automazione di Azure: autenticazione in Azure usando Azure Active Directory].

## Esempi e Runbook di utilità

Il team di automazione di Azure ha creato diversi esempi di Runbook per semplificare le attività iniziali con l'automazione. In questi esempi vengono trattati i concetti di base dell'automazione, allo scopo di fornire informazioni su come scrivere Runbook personalizzati.

Il team di automazione ha inoltre creato dei Runbook di utilità, che è possibile usare come elementi di base per attività di automazione di più ampio respiro.

> [WACOM.NOTE] È consigliabile scrivere Runbook riutilizzabili, modulari e di piccole dimensioni. È inoltre consigliabile creare Runbook di utilità personalizzati per scenari di utilizzo comuni, dopo aver acquisito familiarità con l'automazione.

È possibile visualizzare e scaricare gli esempi e i Runbook di utilità del team di automazione dall'area [Script Center][Script Center] oppure è possibile importarli direttamente dalla [Raccolta di Runbook][Raccolta di Runbook].

## Community, commenti e suggerimenti sull'automazione

Anche i Runbook della community e di altri team Microsoft sono pubblicati nell'area [Script Center][1] e nella [Raccolta di Runbook][Raccolta di Runbook].

**Gli utenti sono invitati a fornire commenti e suggerimenti.** Se si è in cerca di una soluzione Runbook di automazione o di un modulo di integrazione, inviare una richiesta di script in Script Center. Se si vogliono condividere idee per nuove funzionalità di automazione, inviarle nella sezione [User Voice][User Voice].

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Passaggi di alto livello per la presente esercitazione

1.  [Iscriversi all'anteprima dell'automazione][Iscriversi all'anteprima dell'automazione]
2.  [Importare il Runbook dalla Raccolta di Runbook][Importare il Runbook dalla Raccolta di Runbook]
3.  [Pubblicare il Runbook][Pubblicare il Runbook]
4.  [Avviare il Runbook][Avviare il Runbook]

## <a name="preview"></a>Iscriversi per l'anteprima di Automazione di Azure

Per iniziare a usare l'automazione è necessaria una sottoscrizione di Azure attiva con la funzionalità per l'anteprima di Automazione di Microsoft Azure abilitata.

-   Nella pagina **Anteprima funzionalità** fare clic sul pulsante per iniziare la prova**.**

    ![Abilitare l'anteprima][Abilitare l'anteprima]

## <a name="automationaccount"></a>Creare un account di automazione

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Nel portale di gestione fare clic su **Crea un account di automazione**.

    > [WACOM.NOTE] Se è già stato creato un account di automazione, andare al passaggio 4.

    ![Creazione di un account][Creazione di un account]

3.  Nella pagina **Aggiungi un nuovo account di automazione** immettere un nome per l'account e quindi fare clic sul segno di spunta.

    ![Aggiunta di un nuovo account][Aggiunta di un nuovo account]

## <a name="importrunbook"></a>Importare il Runbook dalla Raccolta di Runbook

1.  Nella pagina **Automazione** fare clic sul nuovo account appena creato.

    ![Nuovo account][Nuovo account]

2.  Fare clic su **RUNBOOK**.

    ![Scheda Runbook][Scheda Runbook]

3.  Fare clic su **Nuovo** \> **Runbook** \> **Da raccolta**.

    ![Raccolta di Runbook][2]

4.  Selezionare la categoria **Esercitazione** e quindi **Hello World per Automazione di Azure**. Fare clic sul pulsante con la freccia destra.

    ![Importare Runbook][Importare Runbook]

5.  Verificare i contenuti del Runbook, quindi fare clic sul pulsante con la freccia destra.

    ![Definizione del Runbook][Definizione del Runbook]

6.  Verificare i dettagli del Runbook, quindi fare clic sul pulsante con il segno di spunta.

    ![Dettagli del Runbook][Dettagli del Runbook]

## <a name="publishrunbook"></a>Pubblicare il Runbook

1.  Al termine dell'importazione del Runbook fare clic su **Write-HelloWorld**.

    ![Runbook importato][Runbook importato]

2.  Fare clic su **CREA** e quindi su **BOZZA**.

    È possibile modificare il contenuto di un Runbook in modalità Bozza. Per questo Runbook, non è necessario apportare alcuna modifica.

    ![Bozza di autore][Bozza di autore]

3.  Fare clic su **PUBBLICA** per alzare di livello il Runbook in modo che sia pronto per l'uso in produzione.

    ![Publish][Publish]

4.  Quando viene richiesto se salvare e pubblicare il Runbook, fare clic su **Sì**.

    ![Richiesta di salvataggio e pubblicazione][Richiesta di salvataggio e pubblicazione]

## <a name="startrunbook"></a>Avviare il Runbook

1.  Con il Runbook **Write-HelloWorld** aperto, fare clic su **AVVIO**.

    ![Opzione Pubblicato][Opzione Pubblicato]

2.  Nella pagina **Specificare i valori di parametro del Runbook** immettere un valore per **Nome**, che verrà usato come parametro di input per lo script Write-HelloWorld.ps1, quindi fare clic sul segno di spunta.

    ![Parametri del Runbook][Parametri del Runbook]

3.  Fare clic su **PROCESSI** per controllare lo stato del Runbook appena avviato e quindi sul timestamp nella colonna **AVVIO PROCESSO** per visualizzare il riepilogo del processo.

    ![Stato del Runbook][Stato del Runbook]

4.  Nella pagina **RIEPILOGO** è possibile visualizzare il riepilogo, i parametri di input e l'output del processo.

    ![Riepilogo del Runbook][Riepilogo del Runbook]

# Gestione dei servizi di Azure da un Runbook

Nell'esempio sopra viene illustrato un Runbook di esempio che non gestisce i servizi di Azure. I [cmdlet di Azure][Cmdlet PowerShell per Azure] richiedono l'autenticazione in Azure. È possibile seguire le istruzioni nel post di blog sull'argomento [Automazione di Azure: autenticazione in Azure usando Azure Active Directory.][Automazione di Azure: autenticazione in Azure usando Azure Active Directory] per configurare la sottoscrizione di Azure per la gestione tramite Automazione di Azure.

# Vedere anche

-   [Panoramica dell'automazione][Panoramica dell'automazione]
-   [Guida alla creazione di Runbook][Guida alla creazione di Runbook]
-   [Forum sull'automazione][Forum sull'automazione]
-   [Automazione di Azure: autenticazione in Azure usando Azure Active Directory][Automazione di Azure: autenticazione in Azure usando Azure Active Directory]

  [guida introduttiva all'automazione]: http://go.microsoft.com/fwlink/p/?LinkId=392861
  [Cmdlet PowerShell per Azure]: http://msdn.microsoft.com/it-it/library/jj156055.aspx
  [Automazione di Azure: autenticazione in Azure usando Azure Active Directory]: http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/
  [Script Center]: http://go.microsoft.com/fwlink/p/?LinkId=393029
  [Raccolta di Runbook]: http://aka.ms/runbookgallery
  [1]: http://go.microsoft.com/fwlink/?LinkID=391681
  [User Voice]: http://feedback.windowsazure.com/forums/34192--general-feedback
  [Iscriversi all'anteprima dell'automazione]: #automationaccount
  [Importare il Runbook dalla Raccolta di Runbook]: #importrunbook
  [Pubblicare il Runbook]: #publishrunbook
  [Avviare il Runbook]: #startrunbook
  [Abilitare l'anteprima]: ./media/automation/automation_00_EnablePreview.png
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Creazione di un account]: ./media/automation/automation_01_CreateAccount.png
  [Aggiunta di un nuovo account]: ./media/automation/automation_02_addnewautoacct.png
  [Nuovo account]: ./media/automation/automation_03_NewAutoAcct.png
  [Scheda Runbook]: ./media/automation/automation_04_RunbooksTab.png
  [2]: ./media/automation/automation_05_ImportGallery.png
  [Importare Runbook]: ./media/automation/automation_06_ImportRunbook.png
  [Definizione del Runbook]: ./media/automation/automation_07_RunbookDefinition.png
  [Dettagli del Runbook]: ./media/automation/automation_08_RunbookDetails.png
  [Runbook importato]: ./media/automation/automation_07_ImportedRunbook.png
  [Bozza di autore]: ./media/automation/automation_08_AuthorDraft.png
  [Publish]: ./media/automation/automation_085_Publish.png
  [Richiesta di salvataggio e pubblicazione]: ./media/automation/automation_09_SavePubPrompt.png
  [Opzione Pubblicato]: ./media/automation/automation_10_PublishStart.png
  [Parametri del Runbook]: ./media/automation/automation_11_RunbookParams.png
  [Stato del Runbook]: ./media/automation/automation_12_RunbookStatus.png
  [Riepilogo del Runbook]: ./media/automation/automation_13_RunbookSummary_callouts.png
  [Panoramica dell'automazione]: http://go.microsoft.com/fwlink/p/?LinkId=392860
  [Guida alla creazione di Runbook]: http://go.microsoft.com/fwlink/p/?LinkID=301740
  [Forum sull'automazione]: http://go.microsoft.com/fwlink/p/?LinkId=390561
