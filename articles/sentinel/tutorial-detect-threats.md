---
title: Esaminare gli avvisi con l'anteprima di Azure Sentinel | Microsoft Docs
description: Usare questa esercitazione per informazioni su come analizzare gli avvisi con Azure Sentinel.
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
ms.date: 07/20/2019
ms.author: rkarlin
ms.openlocfilehash: 2cc33a9ac55ae9e906d88b72476d4b5ee244d2c8
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780420"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Esercitazione: Rilevare le minacce con l'anteprima di Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dopo aver [connesso le origini dati](quickstart-onboard.md) ad Azure Sentinel, è necessario ricevere una notifica quando si verifica un evento sospetto. Per consentire l'esecuzione di questa operazione, Azure Sentinel consente di creare regole di avviso avanzate che generano eventi imprevisti che è possibile assegnare e usare per analizzare in modo approfondito le anomalie e le minacce nell'ambiente. 

Questa esercitazione consente di rilevare le minacce con Azure Sentinel.
> [!div class="checklist"]
> * Creare regole di rilevamento
> * Automatizzare le risposte alle minacce

## <a name="create-detection-rules"></a>Creare regole di rilevamento

Per esaminare gli eventi imprevisti, è necessario innanzitutto creare regole di rilevamento. 

> [!NOTE]
> Gli avvisi generati in Sentinel di Azure sono disponibili tramite [Microsoft Graph sicurezza](https://aka.ms/securitygraphdocs). Per altri dettagli e partner di integrazione, fare riferimento alla [documentazione relativa agli avvisi di sicurezza Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs) .

Le regole di rilevamento sono basate sui tipi di minacce e le anomalie che potrebbero essere sospette nell'ambiente che si desidera conoscere immediatamente, garantendo che siano rilevate, analizzate e corrette. 

1. Nel portale di Azure in Sentinel di Azure selezionare **Analytics**.

   ![Analisi](./media/tutorial-detect-threats/alert-rules.png)

2. Nella barra dei menu superiore fare clic su **+ Aggiungi**.  

   ![Crea regola di avviso](./media/tutorial-detect-threats/create-alert-rule.png)

3. In **Crea regola di avviso**, fornire un nome descrittivo e impostare la **gravità** secondo le esigenze. 

4. Creare la query in Log Analytics e quindi incollarla nel campo **impostazione regola di avviso** . Ecco una query di esempio che avvisa l'utente quando viene creato un numero anomalo di risorse nell'attività di Azure.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > La lunghezza della query deve avere una lunghezza compresa tra 1 e 10000 caratteri e non può contenere "Search *" e "Union *".


5. Nella sezione **mapping di entità** usare i campi in **tipo di entità** per eseguire il mapping delle colonne nella query ai campi di entità riconosciuti da Azure Sentinel. Per ogni campo, eseguire il mapping della colonna pertinente nella query creata in Log Analytics al campo dell'entità appropriato. Selezionare il nome della colonna pertinente nella **Proprietà**. Ogni entità include più campi, ad esempio SID, GUID e così via. È possibile eseguire il mapping dell'entità in base a uno dei campi, non solo all'entità di livello superiore.

6. Definire le condizioni del trigger di avviso in **trigger di avviso**. Definisce le condizioni che attivano l'avviso. 

7. Impostare la **frequenza** con cui viene eseguita la query, con una frequenza pari a ogni 5 minuti o raramente come una volta al giorno. 

8. Impostare il **periodo** per controllare l'intervallo di tempo per la quantità di dati su cui viene eseguita la query, ad esempio, può essere eseguito ogni ora in 60 minuti di dati.

9. È anche possibile impostare l' **eliminazione**. L'eliminazione è utile quando si desidera arrestare l'attivazione di avvisi duplicati per lo stesso evento imprevisto. In questo modo, è possibile arrestare l'attivazione degli avvisi durante un periodo specifico. Ciò consente di evitare gli avvisi duplicati per lo stesso evento imprevisto e di eliminare gli avvisi consecutivi per un determinato periodo di tempo. Se, ad esempio, la **frequenza** di **pianificazione degli avvisi** è impostata su 60 minuti e il periodo di **pianificazione dell'avviso** è impostato su due ore e i risultati della query hanno superato la soglia specificata, verrà generato un avviso due volte, una volta quando viene rilevata per la prima volta. negli ultimi 60 minuti e nuovamente quando si trova nei primi 60 minuti delle 2 ore di campionamento dei dati. Se viene attivato un avviso, è consigliabile che l'eliminazione sia per la quantità di tempo impostata nel periodo di avviso. In questo esempio, potrebbe essere necessario impostare l'eliminazione per 60 minuti, in modo che gli avvisi vengano attivati solo per gli eventi che si sono verificati durante l'ora più recente.

8. Dopo aver incollato la query nel campo **Imposta regola di avviso** , è possibile visualizzare immediatamente una simulazione dell'avviso nella **simulazione dell'avviso di logica** , in modo da poter ottenere informazioni sulla quantità di dati che verranno generati in un intervallo di tempo specifico per l'avviso. è stato creato. Ciò dipende da quanto impostato per **frequenza** e **soglia**. Se si nota che in media, l'avviso viene attivato troppo spesso, è consigliabile impostare il numero di risultati in modo che sia superiore alla baseline media.

9. Fare clic su **Crea** per inizializzare la regola di avviso. Dopo la creazione dell'avviso, viene creato un evento imprevisto che contiene l'avviso. È possibile visualizzare le regole di rilevamento definite come righe nella scheda **analisi della sicurezza** . È anche possibile visualizzare il numero di corrispondenze per ogni regola, ovvero gli avvisi attivati. Da questo elenco è possibile abilitare, disabilitare o eliminare ogni regola. È anche possibile fare clic con il pulsante destro del mouse sui puntini di sospensione (...) alla fine della riga per ogni avviso per modificare, disabilitare, clonare, visualizzare corrispondenze o eliminare una regola. La pagina **Analytics** è una raccolta di tutte le regole di avviso attive, inclusi i modelli abilitati e le regole di avviso create in base ai modelli.

1. I risultati delle regole di avviso possono essere visualizzati nella pagina **eventi imprevisti** , in cui è possibile [valutare, esaminare gli eventi imprevisti](tutorial-investigate-cases.md)e correggere le minacce.



## <a name="automate-threat-responses"></a>Automatizzare le risposte alle minacce

I team SIEM/SOC possono essere inondati periodicamente da avvisi di sicurezza. Il numero degli avvisi generati è talmente elevato che gli amministratori della sicurezza disponibili sono sovraccaricati. La conseguenza è che troppo spesso molti avvisi non possono essere analizzati, lasciando l'organizzazione vulnerabile ad attacchi che non vengono rilevati. 

Molti di questi avvisi, se non la maggior parte, seguono modelli ricorrenti che possono essere risolti con azioni correttive specifiche e definite. Azure Sentinel consente già di definire le misure correttive nei playbook. È anche possibile impostare l'automazione in tempo reale come parte della definizione dei playbook, consentendo di automatizzare completamente una risposta definita a determinati avvisi di sicurezza. Usando l'automazione in tempo reale, i team di sicurezza possono ridurre sensibilmente il loro carico di lavoro automatizzando completamente le risposte di routine ai tipi di avviso ricorrenti, con la possibilità di concentrarsi maggiormente su avvisi univoci, analisi dei modelli, ricerca di minacce e altro ancora.

Per automatizzare le risposte:

1. Scegliere l'avviso per cui si vuole automatizzare la risposta.
1. Nel menu di spostamento dell'area di lavoro di Azure Sentinel selezionare **Analisi**.
1. Selezionare l'avviso da automatizzare. 
1. Nella pagina **Modifica la regola di avviso**, in **Playbook attivati** della sezione **Automazione in tempo reale**, scegliere il playbook che si vuole eseguire quando viene trovata una corrispondenza con la regola di avviso.
1. Selezionare **Salva**.

   ![automazione in tempo reale](./media/tutorial-detect-threats/rt-configuration.png)


Inoltre, è possibile correggere manualmente un avviso eseguendo un PlayBook dall'interno dell'avviso, facendo clic su **Visualizza PlayBook** e selezionando un PlayBook da eseguire. Per informazioni su come creare un nuovo PlayBook o modificarne uno esistente, vedere [uso dei PlayBook in Sentinel di Azure](tutorial-respond-threats-playbook.md).



## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come iniziare a rilevare le minacce usando Azure Sentinel. 

Per informazioni su come automatizzare le risposte alle minacce, su [come rispondere alle minacce usando i PlayBook automatici](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Rispondere alle minacce](tutorial-respond-threats-playbook.md) per automatizzare le risposte alle minacce.

