---
title: Esaminare gli avvisi con l'anteprima di Azure Sentinel | Microsoft Docs
description: Usare questa esercitazione per imparare a esaminare gli avvisi con Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: rkarlin
ms.openlocfilehash: e20f6fc0dc8dbe02b09490f62ce84af12aa31b87
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621242"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Esercitazione: Rilevare le minacce con Sentinel anteprima di Azure

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dopo aver [le origini dati connesse](quickstart-onboard.md) Sentinel di Azure, si desidera ricevere una notifica quando succede qualcosa di sospetto. Per consentire a tale scopo, Azure Sentinel consente di che creare avanzato e le regole di avviso, che generano i casi in cui è possibile assegnare per analizzare completamente le anomalie e le minacce nell'ambiente in uso. 

Questa esercitazione consente di rilevare le minacce con Sentinel di Azure.
> [!div class="checklist"]
> * Creare regole di rilevamento
> * Rispondere alle minacce

## <a name="create-detection-rules"></a>Creare regole di rilevamento

Per analizzare i casi, è necessario innanzitutto creare le regole di rilevamento. 

> [!NOTE]
> Sono disponibili tramite gli avvisi generati in Azure Sentinel [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Vedere le [documentazione degli avvisi di sicurezza di Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs) per ulteriori dettagli e i partner di integrazione.

Regole di rilevamento sono basate sui tipi di minacce e anomalie che potrebbero essere sospette nell'ambiente che si desidera conoscere sin da subito, assicurando sono visibili, esaminati e risolti. 

1. Nel portale di Azure in Azure Sentinel, selezionare **Analitica**.

   ![Analytics](./media/tutorial-detect-threats/alert-rules.png)

2. Nella barra dei menu superiore, fare clic su **+ Aggiungi**.  

   ![Creare la regola di avviso](./media/tutorial-detect-threats/create-alert-rule.png)

3. Sotto **Crea regola di avviso**, specificare un nome descrittivo e impostare le **gravità** in base alle esigenze. 

4. Creare la query in Log Analitica e quindi incollarlo nella **regola di avviso Set** campo. Ecco un esempio di query che potrebbe ricevere un avviso quando un numero anomalo delle risorse viene creato nell'attività di Azure.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > La lunghezza di query deve essere compreso tra 1 e 10000 caratteri e non può contenere "search *" e "union *".


5. Nel **mapping dell'entità** sezione, i campi nell'area **tipo di entità** per eseguire il mapping ai campi dell'entità riconosciuti da Azure Sentinel le colonne nella query. Per ogni campo, eseguire il mapping di colonna corrispondente nella query che è stato creato in Log Analitica, per il campo dell'entità appropriata. Selezionare il nome della colonna pertinente sotto il **proprietà**. Ogni entità include più campi, ad esempio SID, GUID, e così via. È possibile eseguire il mapping dell'entità in base a uno dei campi, non solo le entità di livello superiore.

6. Definire le condizioni di attivazione dell'avviso sotto **attivazione dell'avviso**. Definisce le condizioni che attivano l'avviso. 

7. Impostare il **frequenza** per la frequenza di esecuzione query - come frequentemente ogni 5 minuti o di frequenza minima di una volta al giorno. 

8. Impostare il **periodo** per controllare l'intervallo di tempo per la quantità di dati viene eseguita la query in - ad esempio, è possibile eseguire ogni ora tra 60 minuti di dati.

9. È anche possibile impostare il **eliminazione**. L'eliminazione è utile quando si vuole arrestare gli avvisi duplicati attivazione per lo stesso evento imprevisto. In questo modo, è possibile arrestare gli avvisi da attivati durante un periodo specifico. Ciò consente di evitare avvisi duplicati per lo stesso evento imprevisto e consentono di eliminare gli avvisi consecutivi per un periodo di tempo. Ad esempio, se il **avviso pianificazione** **frequenza** è impostato su 60 minuti e il **stagione di avviso** è impostato su due ore, e i risultati della query superata definito soglia, attiverà un avviso due volte, una volta quando viene prima rilevato negli ultimi 60 minuti, e anche in questo caso quando è nei primi 60 minuti 2 ore di dati verranno campionati. È consigliabile che, se viene attivato un avviso, verificare che l'eliminazione deve essere la quantità di tempo impostato nel periodo di avviso. Nel nostro esempio, è possibile impostare la soppressione per 60 minuti, in modo che gli avvisi vengono attivati solo per gli eventi che si sono verificati durante l'ora più recente.

8. Dopo che incollare la query nella **regola di avviso Set** campo, è possibile visualizzare immediatamente una simulazione dell'avviso in **simulazione degli avvisi per la logica** in modo che è possibile ottenere la comprensione del modo in cui la quantità di dati sarà generato in un intervallo di tempo specifico per l'avviso che è stato creato. Procedura varia a seconda di ciò che è impostato per **Frequency** e **soglia**. Se viene visualizzato che in Media, l'avviso verrà attivato una frequenza eccessiva, è opportuno impostare il numero di risultati superiore in modo che sia sopra la linea di base medio.

9. Fare clic su **Create** per inizializzare la regola di avviso. Dopo aver creato l'avviso, viene creato un caso che contiene l'avviso. È possibile visualizzare le regole di rilevamento definiti come righe di **Analitica sicurezza** scheda. È anche possibile visualizzare il numero di corrispondenze per ogni regola - gli avvisi attivati. In questo elenco che è possibile abilitare, disabilitare o eliminare ogni regola. È possibile anche a destra selezionare i puntini di sospensione (...) alla fine della riga per ogni modifica, disabilitare, clonare, mostrare le corrispondenze o eliminare una regola dell'avviso. Il **Analitica** pagina è una raccolta di tutte le regole di avviso attive, inclusi i modelli si abilita e regole di avviso creata in base a modelli.

1. I risultati le regole di avviso possono essere visualizzati nel **casi** pagina, in cui è possibile valutare [analizzare casi](tutorial-investigate-cases.md), e risolvere le minacce.



## <a name="respond-to-threats"></a>Rispondere alle minacce

Sentinel Azure offre due opzioni principali per rispondere alle minacce tramite Playbook. È possibile impostare un playbook da eseguire automaticamente quando viene attivato un avviso oppure è possibile eseguire manualmente un playbook in risposta a un avviso.

- Impostare un playbook da eseguire automaticamente quando viene attivato un avviso quando si configura il playbook. 

- Eseguire manualmente un playbook all'interno dell'avviso, facendo clic **Visualizza i Playbook** e quindi selezionando un playbook per l'esecuzione.




## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come iniziare a rilevare le minacce tramite Azure Sentinel. 

Per informazioni su come automatizzare le risposte alle minacce, [come rispondere alle minacce tramite Playbook automatizzati](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Rispondere alle minacce](tutorial-respond-threats-playbook.md) per automatizzare le risposte alle minacce.

