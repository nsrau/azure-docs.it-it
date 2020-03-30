---
title: Creare regole analitiche personalizzate per rilevare le minacce sospette con Azure Sentinel Documenti Microsoft
description: Usare questa esercitazione per informazioni su come creare regole analitiche personalizzate per rilevare le minacce sospette con Azure Sentinel.Use this tutorial to learn how to Create custom analytic rules to detect suspicious threats with Azure Sentinel.
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
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Esercitazione: Creare regole analitiche personalizzate per rilevare le minacce sospetteTutorial: Create custom analytic rules to detect suspicious threats

Dopo aver [connesso le origini](quickstart-onboard.md) dati ad Azure Sentinel, è possibile creare regole personalizzate in grado di cercare criteri specifici nell'ambiente e generare eventi imprevisti quando i criteri vengono associati in modo da poterli analizzare. Questa esercitazione consente di creare regole personalizzate per rilevare le minacce con Azure Sentinel.This tutorial helps you create custom rules to detect threats with Azure Sentinel.

Questa esercitazione consente di rilevare le minacce con Azure Sentinel.This tutorial helps you detect threats with Azure Sentinel.
> [!div class="checklist"]
> * Creare regole analitiche
> * Automatizzare le risposte alle minacce

## <a name="create-custom-analytic-rules"></a>Creare regole analitiche personalizzate

È possibile creare regole analitiche personalizzate per cercare i tipi di minacce e anomalie che sono sospetti nell'ambiente. La regola garantisce una notifica immediatamente teppure le notifiche, in modo da poter valutare, analizzare e correggere le minacce.

1. Nella portale di Azure in Azure Sentinel selezionare**Analisi**.

1. Nella barra dei menu in alto, selezionare **Crea** e selezionare **Regola di query pianificata.** Verrà **visualizzata**la Creazione guidata regola di analisi .

    ![Crea query pianificata](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Nella scheda **Generale** specificare un **Nome**univoco e una **Descrizione**. Nel campo **Tattiche** è possibile scegliere tra le categorie di attacchi in base alle quali classificare la regola. Impostare l'avviso **Gravità** in base alle esigenze. Quando si crea la regola, il relativo **stato** è **Abilitato** per impostazione predefinita, il che significa che verrà eseguito immediatamente dopo aver completato la creazione. Se non si desidera che venga eseguito immediatamente, selezionare **Disabilitato**e la regola verrà aggiunta alla scheda **Regole attive** ed è possibile abilitarla da tale posizione quando necessario.

    ![Iniziare a creare una regola analitica personalizzata](media/tutorial-detect-threats-custom/general-tab.png)

1. Nella scheda **Imposta logica regola** è possibile scrivere una query direttamente nel campo Query **regola** oppure creare la query in Log Analytics e quindi copiarla e incollarla.
 
   ![Creare una query in Azure SentinelCreate query in Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Vedere l'area di **anteprima Risultati** a destra, in cui Azure Sentinel mostra il numero di risultati (eventi di log) che la query genererà, modificando in tempo reale durante la scrittura e la configurazione della query. Il grafico mostra il numero di risultati nel periodo di tempo definito, determinato dalle impostazioni nella sezione **Programmazione query.**
    - Se la query genera troppi o troppo frequenti avvisi, è possibile impostare una linea di base nella sezione Soglia di **avviso.**

      Ecco una query di esempio che avvisa l'utente quando viene creato un numero anomalo di risorse in Attività di Azure.Here's a sample query that would alert you when an anomalous number of resources is created in Azure Activity.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > La lunghezza della query deve essere compresa tra 1 e \*10.000 caratteri e non può contenere "search" o "union". \*

    1. Usare la sezione Mappa entità per collegare i parametri dei risultati della query alle entità riconosciute di Azure Sentinel.Use the **Map entities** section to link parameters from your query results to Azure Sentinel-recognized entities. Queste entità costituiscono la base per un'ulteriore analisi, incluso il raggruppamento di avvisi in eventi imprevisti nella scheda **Impostazioni eventi imprevisti.**
    1. Nella sezione **Pianificazione query** impostare i parametri seguenti:

       1. Impostare **Esegui query ogni** per controllare la frequenza di esecuzione della query, con la frequenza ogni 5 minuti o raramente una volta al giorno.

       1. Impostare **i dati di ricerca dell'ultimo** per determinare il periodo di tempo dei dati coperti dalla query, ad esempio può eseguire query sugli ultimi 10 minuti di dati o sulle ultime 6 ore di dati.

       > [!NOTE]
       > Queste due impostazioni sono indipendenti l'una dall'altra, fino a un certo punto. È possibile eseguire una query a intervalli brevi che coprano un periodo di tempo più lungo dell'intervallo (in effetti con query sovrapposte), ma non è possibile eseguire una query a un intervallo che supera il periodo di copertura, altrimenti si presenteranno lacune nella copertura complessiva della query.

    1. Utilizzare la sezione **Soglia di avviso** per definire una linea di base. Ad esempio, impostare **Genera avviso quando il numero di risultati della query** è maggiore **di** e immettere il numero 1000 se si desidera che la regola generi un avviso solo se la query genera più di 1000 risultati ogni volta che viene eseguita. Poiché si tratta di un campo obbligatorio, se non si desidera impostare una previsione, ovvero se si desidera che l'avviso registri ogni evento, immettere 0 nel campo del numero.

    1. Nella sezione **Eliminazione** è possibile attivare l'impostazione Interrompi **On** query in esecuzione dopo la generazione **dell'avviso** se, una volta visualizzato un avviso, si desidera sospendere l'operazione di questa regola per un periodo di tempo superiore all'intervallo di query. Se si attiva questa impostazione, è necessario impostare **Interrompi l'esecuzione** della query per la quantità di tempo in cui la query deve interrompere l'esecuzione, fino a 24 ore.

1. Nella scheda **Impostazioni evento imprevisto** è possibile scegliere se e come Azure Sentinel trasforma gli avvisi in eventi imprevisti utilizzabili. Se questa scheda viene lasciata da sola, Azure Sentinel creerà un singolo evento imprevisto separato da ogni avviso. È possibile scegliere di non creare eventi imprevisti o di raggruppare più avvisi in un singolo evento imprevisto modificando le impostazioni in questa scheda.

    1. Nella sezione **Impostazioni evento imprevisto,** **Creare eventi imprevisti da avvisi attivati da questa regola** di analisi è impostato per impostazione predefinita su **Abilitato**, pertanto Azure Sentinel creerà un singolo evento imprevisto separato da ogni avviso attivato dalla regola.<br></br>Se non si desidera che questa regola comportifichi la creazione di eventi imprevisti (ad esempio, se questa regola consiste solo nel raccogliere informazioni per l'analisi successiva), impostarla su **Disabilitato**.

    1. Nella sezione **Raggruppamento avvisi,** se si desidera che un singolo evento imprevisto venga generato da un gruppo di avvisi simili o ricorrenti, impostare **Avvisi correlati al gruppo, attivati da questa regola** di analisi, in Eventi imprevisti su **Abilitato**e impostare i parametri seguenti.

    1. **Limitare il gruppo agli avvisi creati entro l'intervallo**di tempo selezionato:<br></br> Determinare l'intervallo di tempo entro il quale gli avvisi simili o ricorrenti verranno raggruppati. Tutti gli avvisi corrispondenti entro questo intervallo di tempo genereranno collettivamente un evento imprevisto o un set di eventi imprevisti (a seconda delle impostazioni di raggruppamento riportate di seguito). Gli avvisi al di fuori di questo intervallo di tempo genereranno un evento imprevisto separato o un set di eventi imprevisti.

    2. **Raggruppare gli avvisi attivati da questa regola di analisi in un singolo evento imprevisto:** scegliere la base in base alla quale gli avvisi verranno raggruppati:Group alerts triggered by this analytics rule into a single incident by : Choose the basis on which alerts will be groupd together:

        - **Raggruppare gli avvisi in un singolo evento imprevisto se tutte le entità corrispondono:** <br></br>Gli avvisi vengono raggruppati se condividono valori identici per ognuna delle entità mappate (definite nella scheda Imposta logica regola sopra). Questa è l'impostazione consigliata.

        - **Raggruppa tutti gli avvisi attivati da questa regola in un singolo evento:** <br></br>Tutti gli avvisi generati da questa regola vengono raggruppati anche se non condividono valori identici.

        - **Raggruppare gli avvisi in un singolo evento imprevisto se le entità selezionate corrispondono:** <br></br>Gli avvisi vengono raggruppati se condividono valori identici per alcune delle entità mappate (che è possibile selezionare dall'elenco a discesa). È possibile utilizzare questa impostazione se, ad esempio, si desidera creare eventi imprevisti separati in base agli indirizzi IP di origine o di destinazione.

    3. **Riaprire gli eventi imprevisti di corrispondenza chiusi**: se un evento imprevisto è stato chiuso (ovvero il problema sottostante è stato risolto) e successivamente viene generato un altro avviso che sarebbe stato raggruppato in tale evento imprevisto, impostare questa impostazione **su Abilitato** se si desidera che l'evento imprevisto chiuso venga riaperto e lasciare **disattivato** se si desidera che l'avviso crei un nuovo evento imprevisto.

1. Nella scheda **Risposte automatiche** selezionare i playbook che si desidera eseguire automaticamente quando viene generato un avviso dalla regola personalizzata. Per ulteriori informazioni sulla creazione e l'automazione dei playbook, consultate [Rispondere alle minacce.](tutorial-respond-threats-playbook.md)

1. Selezionare **Rivedi e crea** per esaminare tutte le impostazioni per la nuova regola di avviso e quindi selezionare **Crea per inizializzare la regola**di avviso.
  
1. Dopo aver creato l'avviso, una regola personalizzata viene aggiunta alla tabella in **Regole attive**. Da questo elenco è possibile abilitare, disabilitare o eliminare ogni regola.

1. Per visualizzare i risultati delle regole di avviso create, passare alla pagina **Operazioni non consentite,** in cui è possibile valutare, [analizzare gli eventi imprevisti](tutorial-investigate-cases.md)e correggere le minacce.


> [!NOTE]
> Gli avvisi generati in Azure Sentinel sono disponibili tramite [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Per ulteriori informazioni, vedere la [documentazione](https://aka.ms/graphsecurityreferencebetadocs)relativa agli avvisi di protezione di Microsoft Graph .

## <a name="next-steps"></a>Passaggi successivi

In this tutorial, you learned how to get started detecting threats using Azure Sentinel.

Per informazioni su come automatizzare le risposte alle minacce, [configurare le risposte alle minacce automatizzate in Azure Sentinel.](tutorial-respond-threats-playbook.md)

