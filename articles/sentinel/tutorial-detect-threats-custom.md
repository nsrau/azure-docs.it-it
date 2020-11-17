---
title: Creare regole di analisi personalizzate per rilevare le minacce con Azure Sentinel | Microsoft Docs
description: Usare questa esercitazione per imparare a creare regole di analisi personalizzate per rilevare le minacce alla sicurezza con Azure Sentinel. Sfruttare i vantaggi del raggruppamento di eventi e dei gruppi di avvisi e comprendere la DISABILITAzione automatica.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: b685f716688cfbe732fa7d3566e1af97cc81272a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94652111"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Esercitazione: creare regole di analisi personalizzate per rilevare le minacce

Dopo aver [connesso le origini dati](quickstart-onboard.md) ad Azure Sentinel, è possibile creare regole personalizzate che consentono di cercare criteri specifici nell'ambiente e generare eventi imprevisti quando i criteri vengono confrontati in modo da poterli analizzare. Questa esercitazione consente di creare regole personalizzate per rilevare le minacce con Azure Sentinel.

Questa esercitazione consente di rilevare le minacce con Azure Sentinel.
> [!div class="checklist"]
> * Creare regole di analisi
> * Automatizzare le risposte alle minacce

## <a name="create-custom-analytics-rules"></a>Creare regole di analisi personalizzate

È possibile creare regole di analisi personalizzate che consentono di cercare i tipi di minacce e le anomalie sospette nell'ambiente in uso. La regola assicura che la notifica venga effettuata immediatamente, in modo da poter valutare, analizzare e correggere le minacce.

1. Nella portale di Azure in Azure Sentinel selezionare **Analisi**.

1. Nella barra dei menu superiore selezionare **+ Crea** e selezionare **regola di query pianificata**. Verrà visualizzata la **creazione guidata regola di analisi**.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query.png" alt-text="Crea query pianificata":::

1. Nella scheda **generale** specificare un **nome** univoco e una **Descrizione**. Nel campo **tattiche** è possibile scegliere tra le categorie di attacchi per la classificazione della regola. Imposta la **gravità** dell'avviso in caso di necessità. Quando si crea la regola, il relativo **stato** è **abilitato** per impostazione predefinita, il che significa che verrà eseguito immediatamente dopo aver completato la creazione. Se non si vuole che venga eseguita immediatamente, selezionare **disabilitato** e la regola verrà aggiunta alla scheda **regole attive** ed è possibile abilitarla da tale posizione quando necessario.

    ![Avviare la creazione di una regola di analisi personalizzata](media/tutorial-detect-threats-custom/general-tab.png)

1. Nella scheda **impostare la logica della regola** è possibile scrivere una query direttamente nel campo della **query della regola** oppure creare la query in log Analytics, quindi copiarla e incollarla.
 
   ![Creare una query in Sentinel di Azure](media/tutorial-detect-threats-custom/settings-tab.png)

   - Vedere l'area di **anteprima dei risultati** a destra, in cui Azure Sentinel Mostra il numero di risultati (eventi del log) che la query genererà e cambierà rapidamente durante la scrittura e la configurazione della query. Il grafico mostra il numero di risultati nel periodo di tempo definito, determinato dalle impostazioni nella sezione **pianificazione query** .
    - Se si nota che la query attiverà troppi avvisi o troppo frequenti, è possibile impostare una baseline nella sezione **soglia avviso** .

      Ecco una query di esempio che avvisa l'utente quando viene creato un numero anomalo di risorse nell'attività di Azure.

      ```kusto
      AzureActivity
      | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
      | where ActivityStatus == "Succeeded"
      | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
      ```

        > [!NOTE]
        > - La lunghezza della query deve avere una lunghezza compresa tra 1 e 10.000 caratteri e non può contenere "Search \* " o "Union \* ".
        >
        > - L'uso di funzioni ADX per creare query di Esplora dati di Azure nella finestra di query Log Analytics **non è supportato**.

    1. Usare la sezione **entità map** per collegare i parametri dei risultati della query a entità riconosciute da Azure Sentinel. Queste entità costituiscono la base per un'ulteriore analisi, incluso il raggruppamento di avvisi in eventi imprevisti nella scheda **Impostazioni evento imprevisto** .
  
    1. Nella sezione **pianificazione query** impostare i parametri seguenti:

       1. Impostare **Esegui query ogni** per controllare la frequenza con cui viene eseguita la query, con una frequenza pari a ogni 5 minuti o raramente come una volta al giorno.

       1. Impostare i **dati di ricerca dagli ultimi** per determinare il periodo di tempo dei dati analizzati dalla query, ad esempio, può eseguire una query sugli ultimi 10 minuti di dati o le ultime 6 ore di dati.

          > [!NOTE]
          > **Intervalli di query e periodo lookback**
          > - Queste due impostazioni sono indipendenti l'una dall'altra, fino a un punto. È possibile eseguire una query a un breve intervallo che copre un periodo di tempo più lungo dell'intervallo (in effetti con query sovrapposte), ma non è possibile eseguire una query a un intervallo che supera il periodo di copertura. in caso contrario, si disporrà di gap nel code coverage complessivo delle query.
          >
          > **Ritardo di inserimento**
          > - Per tenere conto della **latenza** che può verificarsi tra la generazione di un evento nell'origine e l'inserimento in Sentinel di Azure e per garantire la copertura completa senza duplicazione dei dati, Azure Sentinel esegue le regole di analisi pianificate in un **ritardo di cinque minuti** dall'orario pianificato.

    1. Usare la sezione **soglia avvisi** per definire una linea di base. Ad esempio, impostare **Genera avviso quando il numero di risultati della query** su **è maggiore di** e immettere il numero 1000 se si desidera che la regola generi un avviso solo se la query restituisce più di 1000 risultati ogni volta che viene eseguito. Si tratta di un campo obbligatorio, quindi se non si vuole impostare una baseline, ovvero se si vuole che l'avviso registri ogni evento, immettere 0 nel campo numerico.
    
    1. In **raggruppamento eventi** scegliere uno dei due modi per gestire il raggruppamento degli **eventi** negli **avvisi**: 

       - **Raggruppare tutti gli eventi in un singolo avviso** (impostazione predefinita). La regola genera un singolo avviso ogni volta che viene eseguito, purché la query restituisca più risultati rispetto alla **soglia di avviso** specificata sopra. L'avviso include un riepilogo di tutti gli eventi restituiti nei risultati. 

       - **Attivare un avviso per ogni evento**. La regola genera un avviso univoco per ogni evento restituito dalla query. Questa opzione è utile se si desidera che gli eventi vengano visualizzati singolarmente o se si desidera raggrupparli in base a determinati parametri, in base all'utente, al nome host o a un altro elemento. È possibile definire questi parametri nella query.
    
       Attualmente, il numero di avvisi che una regola può generare è limitato a 20. Se in una determinata regola il **raggruppamento di eventi** è impostato in modo da **attivare un avviso per ogni evento** e la query della regola restituisce più di 20 eventi, ognuno dei primi 19 eventi genererà un avviso univoco e il ventesimo avviso riepilogarà l'intero set di eventi restituiti. In altre parole, il ventesimo avviso è quello che verrebbe generato sotto l'opzione **raggruppa tutti gli eventi in un singolo avviso** .

       > [!NOTE]
       > Qual è la differenza tra **gli eventi** e gli **avvisi**?
       >
       > - Un **evento** è una descrizione di una singola occorrenza. Ad esempio, una singola voce in un file di log può contare come un evento. In questo contesto un evento fa riferimento a un singolo risultato restituito da una query in una regola di analisi.
       >
       > - Un **avviso** è una raccolta di eventi che, presi insieme, sono significativi dal punto di vista della sicurezza. Un avviso può contenere un solo evento se l'evento presenta implicazioni di sicurezza significative, ad esempio un account di accesso amministrativo da un paese esterno all'orario di ufficio.
       >
       > - Che cosa sono gli **eventi imprevisti**? La logica interna di Sentinel di Azure consente di creare **eventi imprevisti** da **avvisi** o gruppi di avvisi. La coda degli eventi imprevisti è il punto focale degli analisti per il lavoro di valutazione, indagine e correzione.
       > 
       > Azure Sentinel inserisce gli eventi non elaborati da alcune origini dati e gli avvisi già elaborati da altri. È importante tenere presente quale è la gestione in qualsiasi momento.

       > [!IMPORTANT]
       > Il raggruppamento di eventi è attualmente disponibile in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. Nella sezione **eliminazione** , è possibile attivare l'impostazione **Interrompi esecuzione query dopo la generazione** dell' **On** avviso se, dopo aver ricevuto un avviso, si desidera sospendere il funzionamento di questa regola per un periodo di tempo superiore all'intervallo di query. Se si attiva questa impostazione, è necessario impostare **Interrompi esecuzione query per** sull'intervallo di tempo durante il quale la query deve arrestarsi, fino a 24 ore.

1. Nella scheda **Impostazioni evento imprevisto** è possibile scegliere se e in che modo Azure Sentinel attiva gli avvisi in eventi imprevisti. Se questa scheda viene lasciata da sola, Azure Sentinel creerà un singolo evento imprevisto separato da ogni avviso. È possibile scegliere di non creare eventi imprevisti o di raggruppare più avvisi in un singolo evento imprevisto, modificando le impostazioni in questa scheda.

   > [!IMPORTANT]
   > La scheda Impostazioni evento imprevisto è attualmente disponibile in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. Nella sezione **delle impostazioni degli eventi imprevisti** , la **creazione di eventi imprevisti da avvisi attivati da questa regola di analisi** viene impostata per impostazione predefinita su **abilitato**, ovvero Azure Sentinel creerà un singolo evento imprevisto separato da ogni avviso attivato dalla regola.
       - Se non si vuole che questa regola provochi la creazione di eventi imprevisti (ad esempio, se questa regola è solo per raccogliere informazioni per l'analisi successiva), impostare questa impostazione su **disabilitato**.

    1. Nella sezione **raggruppamento avvisi** , se si desidera che venga generato un singolo evento imprevisto da un gruppo di un massimo di 150 avvisi simili o ricorrenti (vedere la nota), impostare gli **avvisi relativi ai gruppi, attivati da questa regola di analisi, in eventi imprevisti** per **abilitati** e impostare i parametri seguenti.

    - **Limitare il gruppo agli avvisi creati nell'intervallo di tempo selezionato**: determinare l'intervallo di tempo entro il quale verranno raggruppati gli avvisi simili o ricorrenti. Tutti gli avvisi corrispondenti entro questo intervallo di tempo genereranno collettivamente un evento imprevisto o un set di eventi imprevisti (a seconda delle impostazioni di raggruppamento seguenti). Gli avvisi esterni a questo intervallo di tempo genereranno un evento imprevisto o un set di eventi imprevisti distinti.

    - **Avvisi di gruppo generati da questa regola di analisi in un singolo evento imprevisto da**: scegliere la base in cui verranno raggruppati gli avvisi:

        - **Raggruppare gli avvisi in un singolo evento imprevisto se tutte le entità corrispondono: gli** avvisi vengono raggruppati se condividono valori identici per ognuna delle entità mappate (definite nella scheda logica della regola di impostazione precedente). Questa è l'impostazione consigliata.

        - **Raggruppa tutti gli avvisi attivati da questa regola in un singolo evento imprevisto**: tutti gli avvisi generati da questa regola vengono raggruppati anche se non condividono valori identici.

        - **Raggruppa gli avvisi in un singolo evento imprevisto se le entità selezionate corrispondono: gli** avvisi vengono raggruppati se condividono valori identici per alcune delle entità mappate, che è possibile selezionare dall'elenco a discesa. Questa impostazione può essere utile se, ad esempio, si desidera creare eventi imprevisti distinti in base agli indirizzi IP di origine o di destinazione.

    - **Riapri eventi imprevisti corrispondenti chiusi**: se un evento imprevisto è stato risolto e chiuso e successivamente viene generato un altro avviso che deve appartenere a tale evento imprevisto, impostare questa impostazione su **abilitato** se si desidera che l'evento imprevisto chiuso venga riaperto e lasciare **disabilitato** se si desidera che l'avviso crei un nuovo evento imprevisto.
    
        > [!NOTE]
        > È possibile raggruppare fino a 150 avvisi in un singolo evento imprevisto. Se vengono generati più di 150 avvisi da una regola che li raggruppa in un singolo evento imprevisto, verrà generato un nuovo evento imprevisto con gli stessi dettagli dell'evento imprevisto e gli avvisi in eccesso verranno raggruppati nel nuovo evento imprevisto.

1. Nella scheda **risposte automatiche** selezionare gli schemi che si desidera eseguire automaticamente quando viene generato un avviso dalla regola personalizzata. Per altre informazioni sulla creazione e l'automazione dei PlayBook, vedere [rispondere alle minacce](tutorial-respond-threats-playbook.md).

1. Selezionare **Verifica e crea** per esaminare tutte le impostazioni per la nuova regola di avviso e quindi selezionare **Crea per inizializzare la regola di avviso**.
  
1. Dopo la creazione dell'avviso, una regola personalizzata viene aggiunta alla tabella in **regole attive**. Da questo elenco è possibile abilitare, disabilitare o eliminare ogni regola.

1. Per visualizzare i risultati delle regole di avviso create, passare alla pagina **eventi imprevisti** , in cui è possibile valutare, [esaminare gli eventi imprevisti](tutorial-investigate-cases.md)e correggere le minacce.


> [!NOTE]
> Gli avvisi generati in Sentinel di Azure sono disponibili tramite [Microsoft Graph sicurezza](/graph/security-concept-overview). Per ulteriori informazioni, vedere la [documentazione relativa agli avvisi di sicurezza Microsoft Graph](/graph/api/resources/security-api-overview).

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Non è stato possibile eseguire una regola pianificata o viene visualizzato con la DISABILITAzione automatica aggiunta al nome

Si tratta di un caso raro in cui non è possibile eseguire una regola di query pianificata, ma ciò può verificarsi. Azure Sentinel classifica gli errori in primo piano come temporanei o permanenti in base al tipo specifico di errore e alle circostanze che lo hanno generato.

#### <a name="transient-failure"></a>Errore temporaneo

Un errore temporaneo si verifica a causa di una circostanza che è temporanea e tornerà presto alla normalità, a quel punto l'esecuzione della regola avrà esito positivo. Di seguito sono riportati alcuni esempi di errori classificati da Azure Sentinel come temporanei:

- Una query di regola richiede troppo tempo per l'esecuzione e il timeout.
- Problemi di connettività tra origini dati e Log Analytics o tra Log Analytics e Sentinel di Azure.
- Eventuali altri errori nuovi e sconosciuti sono considerati temporanei.

In caso di errore temporaneo, Azure Sentinel continua a provare a eseguire nuovamente la regola dopo gli intervalli predeterminati e in continua crescita, fino a un punto. Successivamente, la regola verrà eseguita di nuovo solo al successivo orario pianificato. Una regola non verrà mai disabilitata automaticamente a causa di un errore temporaneo.

#### <a name="permanent-failure---rule-auto-disabled"></a>Errore permanente: la regola è disabilitata automaticamente

Si verifica un errore permanente a causa di una modifica nelle condizioni che consentono l'esecuzione della regola, che senza intervento umano non tornerà allo stato precedente. Di seguito sono riportati alcuni esempi di errori classificati come permanenti:

- L'area di lavoro di destinazione (su cui è stata eseguita la query della regola) è stata eliminata.
- La tabella di destinazione in cui è stata eseguita la query della regola è stata eliminata.
- Azure Sentinel è stato rimosso dall'area di lavoro di destinazione.
- Una funzione utilizzata dalla query della regola non è più valida. è stato modificato o rimosso.
- Le autorizzazioni per una delle origini dati della query della regola sono state modificate.
- Una delle origini dati della query della regola è stata eliminata o disconnessa.

**Nel caso di un numero predeterminato di errori permanenti consecutivi, dello stesso tipo e della stessa regola,** Azure Sentinel smette di provare a eseguire la regola e, inoltre, esegue i passaggi seguenti:

- Disabilita la regola.
- Aggiunge le parole **"auto disabled"** all'inizio del nome della regola.
- Aggiunge la causa dell'errore (e la disabilitazione) alla descrizione della regola.

È possibile determinare facilmente la presenza di eventuali regole disabilitate automaticamente, ordinando l'elenco di regole in base al nome. Le regole disabilitate automaticamente si troveranno nella parte superiore dell'elenco.

I responsabili SOC devono assicurarsi di controllare regolarmente l'elenco di regole per la presenza di regole disabilitate automaticamente.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come iniziare a rilevare le minacce usando Azure Sentinel.

Per informazioni su come automatizzare le risposte alle minacce, [configurare le risposte automatiche alle minacce in Sentinel di Azure](tutorial-respond-threats-playbook.md).