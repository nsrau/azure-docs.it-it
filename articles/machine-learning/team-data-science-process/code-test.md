---
title: Testare il codice di data science con Azure DevOps Services - Processo di data science per i team
description: Test di codice di data science su Azure con il set di dati di stima del reddito degli adulti UCI con il processo di data science per i team e Azure DevOps Services
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: 9612114bb368898ccf31b2c8692869b84544b652
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722003"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Test di codice di data science su Azure con il processo di data science per i team e Azure DevOps Services
Questo articolo include le linee guida preliminari per il test del codice in un flusso di lavoro di data science. Il test offre ai data scientist un metodo sistematico ed efficiente per il controllo della qualità e del risultato previsto del codice. Viene usato un [progetto TDSP (Team Data Science Process) che usa il set di dati sul reddito degli adulti UCI](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) pubblicato in precedenza per illustrare la modalità di esecuzione del test del codice. 

## <a name="introduction-on-code-testing"></a>Introduzione sul test del codice
Il "testing unità" è una prassi consolidata dello sviluppo di software. Tuttavia, per data science, spesso non è chiaro cosa significa "unit test" e come è consigliabile testare il codice per diverse fasi di un ciclo di vita data science, ad esempio:

* Preparazione dei dati
* Analisi della qualità dei dati
* Creazione di modelli
* Distribuzione di modelli 

In questo articolo il termine "testing unità" è sostituito con "test del codice". Il termine si riferisce alle funzioni che consentono di valutare se il codice di una fase specifica di un ciclo di vita di data science produce i risultati previsti. La persona che scrive il test definisce i risultati previsti, a seconda del risultato della funzione, ad esempio del controllo della qualità o della modellazione dei dati.

Questo articolo include riferimenti che costituiscono risorse utili.

## <a name="azure-devops-for-the-testing-framework"></a>Azure DevOps per il framework di test
Questo articolo descrive come eseguire e automatizzare i test usando Azure DevOps. È possibile decidere di usare strumenti diversi. Viene inoltre illustrato come configurare una compilazione automatica con Azure DevOps e agenti di compilazione. Per gli agenti di compilazione, vengono usate macchine virtuali di data science (DSVM) di Azure.

## <a name="flow-of-code-testing"></a>Flusso del test del codice
Il flusso di lavoro del test del codice in un progetto di data science è simile al seguente: 

![Diagramma di flusso del test del codice](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Procedura dettagliata

Usare la procedura seguente per configurare ed eseguire il test di codice e una compilazione automatizzata tramite un agente di compilazione e Azure DevOps:

1. Creare un progetto nell'applicazione desktop di Visual Studio:

    ![Schermata "Crea nuovo progetto" in Visual Studio](./media/code-test/create_project.PNG)

   Dopo averlo creato, il progetto è visibile in Esplora soluzioni nel riquadro di destra:
    
    ![Passaggi per la creazione di un progetto](./media/code-test/create_python_project_in_vs.PNG)

    ![Esplora soluzioni](./media/code-test/solution_explorer_in_vs.PNG)

1. Inserire il codice del progetto nel repository di codice del progetto di Azure DevOps: 

    ![Repository di codice del progetto](./media/code-test/create_repo.PNG)

1. Si supponga di aver eseguito alcune operazioni di preparazione dei dati, ad esempio l'inserimento dei dati, la progettazione delle funzionalità e la creazione di colonne etichetta. Si vuole verificare che il codice produca i risultati previsti. Di seguito è riportato un codice che è possibile usare per verificare il corretto funzionamento del codice di elaborazione dei dati:

    * Verificare che i nomi di colonna siano corretti:
    
      ![Codice per la corrispondenza dei nomi di colonna](./media/code-test/check_column_names.PNG)

    * Verificare che i livelli di risposta siano corretti:

      ![Codice per la corrispondenza dei livelli](./media/code-test/check_response_levels.PNG)

    * Verificare che la percentuale di risposta sia adeguata:

      ![Codice per la percentuale di risposta](./media/code-test/check_response_percentage.PNG)

    * Controllare la percentuale non rilevata di ogni colonna nei dati:
    
      ![Codice per la percentuale non rilevata](./media/code-test/check_missing_rate.PNG)


1. Dopo aver eseguito l'elaborazione dei dati e la progettazione delle funzionalità e aver eseguito il training di un buon modello, assicurarsi che il modello sia in grado di valutare correttamente i nuovi set di dati. Per verificare i livelli di stima e la distribuzione dei valori di etichetta è possibile usare i due test seguenti:

    * Verificare i livelli di stima:
    
      ![Codice per la verifica dei livelli di stima](./media/code-test/check_prediction_levels.PNG)

    * Controllare la distribuzione dei valori di stima:

      ![Codice per la verifica dei valori di stima](./media/code-test/check_prediction_values.PNG)

1. Inserire tutte le funzioni di test in uno script Python denominato **test_funcs.py**:

    ![Script Python per le funzioni di test](./media/code-test/create_file_test_func.PNG)


1. Dopo aver preparato i codici di test, è possibile configurare l'ambiente di test in Visual Studio.

   Creare un file Python denominato **test1.py**. Nel file creare una classe che include tutti i test da eseguire. L'esempio seguente mostra sei test preparati:
    
    ![File Python con un elenco di test in una classe](./media/code-test/create_file_test1_class.PNG)

1. I test possono essere individuati automaticamente se si inserisce **codetest.testCase** dopo il nome della classe. Aprire Esplora test nel riquadro di destra e selezionare **Esegui tutto**. Tutti i test verranno eseguite in sequenza e verrà indicato se il test ha esito positivo o negativo.

    ![Esecuzione dei test](./media/code-test/run_tests.PNG)

1. Archiviare il codice nel repository del progetto usando i comandi GIT. Le ultime operazioni eseguite verranno visualizzate in Azure DevOps.

    ![Comandi GIT per l'archiviazione del codice](./media/code-test/git_check_in.PNG)

    ![Ultime operazioni in Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Configurare la compilazione automatica e di test in Azure DevOps:

    a. Nel repository del progetto selezionare **Compilazione e versione** e quindi **+Nuovo** per creare un nuovo processo di compilazione.

    ![Selezioni per l'avvio di un nuovo processo di compilazione](./media/code-test/create_new_build.PNG)

    b. Seguire le istruzioni per selezionare il percorso del codice sorgente, il nome del progetto, il repository e le informazioni sul ramo.
    
    ![Origine, nome, repository e informazioni sul ramo](./media/code-test/fill_in_build_info.PNG)

    c. Selezionare un modello. Poiché non è disponibile alcun modello di progetto Python, iniziare selezionando **Processo vuoto**. 

    ![Elenco di modelli e pulsante "Processo vuoto"](./media/code-test/start_empty_process_template.PNG)

    d. Assegnare un nome alla compilazione e selezionare l'agente. È possibile scegliere il valore predefinito qui se si vuole usare un DSVM per completare il processo di compilazione. Per altre informazioni sull'impostazione degli agenti, vedere [Build and release agents](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=vsts) (Agenti di compilazione e versione).
    
    ![Compilazione e selezioni degli agenti](./media/code-test/select_agent.PNG)

    e. Selezionare **+** nel riquadro di sinistra per aggiungere un'attività per la fase di compilazione. Poiché verrà eseguito lo script Python **test1.py** per completare tutti i controlli, questa attività sta usando un comando di PowerShell per eseguire il codice Python.
    
    ![Riquadro "Aggiungi attività" con PowerShell selezionato](./media/code-test/add_task_powershell.PNG)

    f. Nei dettagli di PowerShell inserire le informazioni necessarie, ad esempio il nome e la versione di PowerShell. Scegliere il tipo **Script inline**. 
    
    Nella casella **Script inline** è possibile digitare **python test1.py**. Assicurarsi che la variabile di ambiente sia configurata correttamente per Python. Se occorre una versione o un kernel di Python diverso, è possibile specificare in modo esplicito il percorso come illustrato nella figura: 
    
    ![Dettagli di PowerShell](./media/code-test/powershell_scripts.PNG)

    g. Selezionare **salva & coda** per completare il processo della pipeline di compilazione.

    ![Pulsante "Salva e accoda"](./media/code-test/save_and_queue_build_definition.PNG)

Ogni volta che verrà eseguito il push di un nuovo commit nel repository di codice, il processo di compilazione verrà avviato automaticamente. Qui viene usato master come repository, ma è possibile definire qualsiasi ramo. Il processo esegue il file **test1.py** nel computer agente per assicurarsi che tutto ciò che viene definito nel codice venga eseguito correttamente. 

Se gli avvisi sono impostati correttamente, viene inviata una notifica tramite posta elettronica al termine della compilazione. È anche possibile controllare lo stato della compilazione in Azure DevOps. In caso di esito negativo, è possibile controllare i dettagli della compilazione e individuare l'elemento errato.

![Notifica tramite posta elettronica per la compilazione completata](./media/code-test/email_build_succeed.PNG)

![Notifica di Azure DevOps per la compilazione completata](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Passaggi successivi
* Vedere il [repository di stima del reddito UCI](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) per esempi concreti di unit test per gli scenari di data science.
* Seguire la struttura e gli esempi precedenti dello scenario di stima del reddito UCI nei progetti di data science.

## <a name="references"></a>Riferimenti
* [Team Data Science Process](https://aka.ms/tdsp)
* [Strumenti di test di Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [Risorse di test di Azure DevOps](https://www.visualstudio.com/team-services/)
* [Macchine virtuali di data science](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
