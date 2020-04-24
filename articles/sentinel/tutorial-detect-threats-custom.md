---
title: Creare regole analitiche personalizzate per rilevare minacce sospette con Azure Sentinel | Microsoft Docs
description: Usare questa esercitazione per imparare a creare regole analitiche personalizzate per rilevare minacce sospette con Azure Sentinel.
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
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: cea7429ecea105355b0afe306bfa334e55d5d9c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585108"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Esercitazione: creare regole analitiche personalizzate per rilevare minacce sospette

Dopo aver [connesso le origini](quickstart-onboard.md) dati ad Azure Sentinel, è possibile creare regole personalizzate che consentono di cercare criteri specifici nell'ambiente in uso e generare eventi imprevisti quando i criteri vengono confrontati in modo da poterli analizzare. Questa esercitazione consente di creare regole personalizzate per rilevare le minacce con Azure Sentinel.

Questa esercitazione consente di rilevare le minacce con Azure Sentinel.
> [!div class="checklist"]
> * Creare regole analitiche
> * Automatizzare le risposte alle minacce

## <a name="create-custom-analytic-rules"></a>Creare regole analitiche personalizzate

È possibile creare regole analitiche personalizzate che consentono di cercare i tipi di minacce e le anomalie sospette nell'ambiente in uso. La regola assicura che la notifica venga effettuata immediatamente, in modo da poter valutare, analizzare e correggere le minacce.

1. Nella portale di Azure in Azure Sentinel selezionare**Analisi**.

1. Nella barra dei menu superiore selezionare **+ Crea** e selezionare **regola di query pianificata**. Verrà visualizzata la **creazione guidata regola di analisi**.

    ![Crea query pianificata](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Nella scheda **generale** specificare un **nome**univoco e una **Descrizione**. Nel campo **tattiche** è possibile scegliere tra le categorie di attacchi per la classificazione della regola. Imposta la **gravità** dell'avviso in caso di necessità. Quando si crea la regola, il relativo **stato** è **abilitato** per impostazione predefinita, il che significa che verrà eseguito immediatamente dopo aver completato la creazione. Se non si vuole che venga eseguita immediatamente, selezionare **disabilitato**e la regola verrà aggiunta alla scheda **regole attive** ed è possibile abilitarla da tale posizione quando necessario.

    ![Avviare la creazione di una regola analitica personalizzata](media/tutorial-detect-threats-custom/general-tab.png)

1. Nella scheda **impostare la logica della regola** è possibile scrivere una query direttamente nel campo della **query della regola** oppure creare la query in log Analytics, quindi copiarla e incollarla.
 
   ![Creare una query in Sentinel di Azure](media/tutorial-detect-threats-custom/settings-tab.png)

   - Vedere l'area di **anteprima dei risultati** a destra, in cui Azure Sentinel Mostra il numero di risultati (eventi del log) che la query genererà e cambierà rapidamente durante la scrittura e la configurazione della query. Il grafico mostra il numero di risultati nel periodo di tempo definito, determinato dalle impostazioni nella sezione **pianificazione query** .
    - Se si nota che la query attiverà troppi avvisi o troppo frequenti, è possibile impostare una baseline nella sezione **soglia avviso** .

      Ecco una query di esempio che avvisa l'utente quando viene creato un numero anomalo di risorse nell'attività di Azure.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > La lunghezza della query deve avere una lunghezza compresa tra 1 e 10.000 caratteri e \*non può contenere " \*Search" o "Union".

    1. Usare la sezione **entità map** per collegare i parametri dei risultati della query a entità riconosciute da Azure Sentinel. Queste entità costituiscono la base per un'ulteriore analisi, incluso il raggruppamento di avvisi in eventi imprevisti nella scheda **Impostazioni evento imprevisto** .
    1. Nella sezione **pianificazione query** impostare i parametri seguenti:

       1. Impostare **Esegui query ogni** per controllare la frequenza con cui viene eseguita la query, con una frequenza pari a ogni 5 minuti o raramente come una volta al giorno.

       1. Impostare i **dati di ricerca dagli ultimi** per determinare il periodo di tempo dei dati analizzati dalla query, ad esempio, può eseguire una query sugli ultimi 10 minuti di dati o le ultime 6 ore di dati.

       > [!NOTE]
       > Queste due impostazioni sono indipendenti l'una dall'altra, fino a un punto. È possibile eseguire una query a un breve intervallo che copre un periodo di tempo più lungo dell'intervallo (in effetti con query sovrapposte), ma non è possibile eseguire una query a un intervallo che supera il periodo di copertura. in caso contrario, si disporrà di gap nel code coverage complessivo delle query.

    1. Usare la sezione **soglia avvisi** per definire una linea di base. Ad esempio, impostare **Genera avviso quando il numero di risultati della query** su **è maggiore di** e immettere il numero 1000 se si desidera che la regola generi un avviso solo se la query genera più di 1000 risultati ogni volta che viene eseguito. Poiché si tratta di un campo obbligatorio, se non si vuole impostare una baseline, ovvero se si vuole che l'avviso registri ogni evento, immettere 0 nel campo numerico.

    1. Nella sezione **eliminazione** , è possibile attivare l'impostazione **Interrompi esecuzione query dopo la generazione** dell' **On** avviso se, dopo aver ricevuto un avviso, si desidera sospendere il funzionamento di questa regola per un periodo di tempo superiore all'intervallo di query. Se si attiva questa impostazione, è necessario impostare **Interrompi esecuzione query per** sull'intervallo di tempo durante il quale la query deve arrestarsi, fino a 24 ore.

1. Nella scheda **Impostazioni evento imprevisto** è possibile scegliere se e in che modo Azure Sentinel attiva gli avvisi in eventi imprevisti. Se questa scheda viene lasciata da sola, Azure Sentinel creerà un singolo evento imprevisto separato da ogni avviso. È possibile scegliere di non creare eventi imprevisti o di raggruppare più avvisi in un singolo evento imprevisto, modificando le impostazioni in questa scheda.

    1. Nella sezione **delle impostazioni degli eventi imprevisti** , la **creazione di eventi imprevisti da avvisi attivati da questa regola di analisi** viene impostata per impostazione predefinita su **abilitato**, ovvero Azure Sentinel creerà un singolo evento imprevisto separato da ogni avviso attivato dalla regola.<br></br>Se non si vuole che questa regola provochi la creazione di eventi imprevisti (ad esempio, se questa regola è solo per raccogliere informazioni per l'analisi successiva), impostare questa impostazione su **disabilitato**.

    1. Nella sezione relativa al **raggruppamento** degli avvisi, se si desidera che venga generato un singolo evento imprevisto da un gruppo di avvisi simili o ricorrenti, impostare gli **avvisi relativi ai gruppi, attivati da questa regola di analisi, in eventi imprevisti** in **abilitati**e impostare i parametri seguenti.

    1. **Limitare il gruppo agli avvisi creati nell'intervallo di tempo selezionato**:<br></br> Determinare l'intervallo di tempo entro il quale verranno raggruppati gli avvisi simili o ricorrenti. Tutti gli avvisi corrispondenti entro questo intervallo di tempo genereranno collettivamente un evento imprevisto o un set di eventi imprevisti (a seconda delle impostazioni di raggruppamento seguenti). Gli avvisi esterni a questo intervallo di tempo genereranno un evento imprevisto o un set di eventi imprevisti distinti.

    2. **Avvisi di gruppo generati da questa regola di analisi in un singolo evento imprevisto da**: scegliere la base in cui verranno raggruppati gli avvisi:

        - **Raggruppa gli avvisi in un singolo evento imprevisto se tutte le entità corrispondono**: <br></br>Gli avvisi vengono raggruppati se condividono valori identici per ognuna delle entità mappate (definite nella scheda Imposta logica della regola precedente). Questa è l'impostazione consigliata.

        - **Raggruppa tutti gli avvisi generati da questa regola in un singolo evento imprevisto**: <br></br>Tutti gli avvisi generati da questa regola vengono raggruppati anche se non condividono valori identici.

        - **Raggruppa gli avvisi in un singolo evento imprevisto se le entità selezionate corrispondono**: <br></br>Gli avvisi vengono raggruppati se condividono valori identici per alcune delle entità mappate, che è possibile selezionare dall'elenco a discesa. Questa impostazione può essere utile se, ad esempio, si desidera creare eventi imprevisti distinti in base agli indirizzi IP di origine o di destinazione.

    3. **Riapri eventi imprevisti corrispondenti chiusi**: se un evento imprevisto è stato chiuso (ovvero il problema sottostante è stato risolto) e successivamente viene generato un altro avviso che sarebbe stato raggruppato in tale evento, impostare questa impostazione su **abilitato** se si desidera che l'evento imprevisto chiuso venga riaperto e lasciare **disabilitato** se si desidera che l'avviso crei un nuovo evento imprevisto.

1. Nella scheda **risposte automatiche** selezionare gli schemi che si desidera eseguire automaticamente quando viene generato un avviso dalla regola personalizzata. Per altre informazioni sulla creazione e l'automazione dei PlayBook, vedere [rispondere alle minacce](tutorial-respond-threats-playbook.md).

1. Selezionare **Verifica e crea** per esaminare tutte le impostazioni per la nuova regola di avviso e quindi selezionare **Crea per inizializzare la regola di avviso**.
  
1. Dopo la creazione dell'avviso, una regola personalizzata viene aggiunta alla tabella in **regole attive**. Da questo elenco è possibile abilitare, disabilitare o eliminare ogni regola.

1. Per visualizzare i risultati delle regole di avviso create, passare alla pagina **eventi imprevisti** , in cui è possibile valutare, [esaminare gli eventi imprevisti](tutorial-investigate-cases.md)e correggere le minacce.


> [!NOTE]
> Gli avvisi generati in Sentinel di Azure sono disponibili tramite [Microsoft Graph sicurezza](https://aka.ms/securitygraphdocs). Per ulteriori informazioni, vedere la [documentazione relativa agli avvisi di sicurezza Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come iniziare a rilevare le minacce usando Azure Sentinel.

Per informazioni su come automatizzare le risposte alle minacce, [configurare le risposte automatiche alle minacce in Sentinel di Azure](tutorial-respond-threats-playbook.md).

