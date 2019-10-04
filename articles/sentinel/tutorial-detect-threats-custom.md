---
title: Creare regole analitiche personalizzate per rilevare minacce sospette con Azure Sentinel | Microsoft Docs
description: Usare questa esercitazione per imparare a creare regole analitiche personalizzate per rilevare minacce sospette con Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 611d3b3fdf2f76a9d5e79b8f3728dedae59074cd
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240447"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Esercitazione: Creare regole analitiche personalizzate per rilevare minacce sospette

Dopo aver [connesso le origini](quickstart-onboard.md) dati ad Azure Sentinel, è possibile creare regole personalizzate che consentono di cercare criteri specifici nell'ambiente in uso e generare eventi imprevisti quando i criteri vengono confrontati in modo da poterli analizzare. Questa esercitazione consente di creare regole personalizzate per rilevare le minacce con Azure Sentinel.

Questa esercitazione consente di rilevare le minacce con Azure Sentinel.
> [!div class="checklist"]
> * Creare regole analitiche
> * Automatizzare le risposte alle minacce

## <a name="create-custom-analytic-rules"></a>Creare regole analitiche personalizzate

È possibile creare regole analitiche personalizzate che consentono di cercare i tipi di minacce e le anomalie sospette nell'ambiente in uso. La regola assicura che la notifica venga effettuata immediatamente, in modo da poter valutare, analizzare e correggere le minacce.

1. Nel portale di Azure in Sentinel di Azure selezionare **Analytics**.

1. Nella barra dei menu superiore selezionare **+ Crea** e selezionare **regola di query pianificata**. Verrà visualizzata la **creazione guidata regola personalizzata**.

    ![Crea query pianificata](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Nella scheda **generale** specificare un nome descrittivo e una descrizione. Imposta la **gravità** dell'avviso in caso di necessità. Quando si crea la regola, è possibile abilitarla, in modo che venga eseguita immediatamente dopo aver completato la creazione. Se viene creato come disabilitato, la regola verrà aggiunta alla scheda **regole** attive ed è possibile abilitarla da tale posizione quando necessario.

    ![Avviare la creazione di una regola analitica personalizzata](media/tutorial-detect-threats-custom/general-tab.png)

1. Nella scheda **Impostazioni** è possibile scrivere direttamente una query o creare la query in log Analytics e quindi incollarla nel campo della **query** di ricerca. Quando si modifica e si configura la query, Azure Sentinel simula i risultati della query nella finestra di  **anteprima dei risultati**, a destra. In questo modo è possibile ottenere informazioni sulla quantità di dati generati in un intervallo di tempo specifico per l'avviso che si sta creando. La quantità dipende da ciò che è stato impostato per l' **esecuzione della query ogni** e i **dati di ricerca dell'ultima**. Se si nota che, in media, l'avviso attiverà troppo spesso gli avvisi, è possibile impostare il numero di risultati in modo più elevato, in modo che si trovi al di sopra della linea di base media.

   ![Creare una query in Sentinel di Azure](media/tutorial-detect-threats-custom/settings-tab.png)

   Ecco una query di esempio che avvisa l'utente quando viene creato un numero anomalo di risorse nell'attività di Azure.

    `AzureActivity
    \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    \| where ActivityStatus == "Succeeded"
    \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

   > [!NOTE]
   > La lunghezza della query deve avere una lunghezza compresa tra 1 e 1, 0000 caratteri e \*non può contenere " \*Search" o "Union".

    1. In **pianificazione query**impostare i parametri seguenti:

        1.  Impostare **Esegui query ogni** per impostare la **frequenza** con cui viene eseguita la query, con una frequenza pari a ogni 5 minuti o raramente come una volta al giorno.

        1.  Impostare i **dati di ricerca dagli ultimi** per controllare l'intervallo di tempo per la quantità di dati su cui viene eseguita la query, ad esempio, può essere eseguito ogni ora, in 60 minuti di dati.

        1. È possibile impostare Azure Sentinel per **arrestare l'esecuzione della query dopo la generazione dell'avviso** se si desidera ricevere un avviso solo dopo che si è verificato. È necessario impostare la finestra durante la quale la query smette di funzionare, fino a 24 ore.

    1. Definire le condizioni del trigger di avviso in **trigger di avviso**. In **mapping entità**è possibile eseguire il mapping delle colonne nella query ai campi di entità riconosciuti da Azure Sentinel. Per ogni campo, eseguire il mapping della colonna pertinente nella query creata in Log Analytics al campo dell'entità appropriato. Ogni entità include più campi, ad esempio SID e GUID. È possibile eseguire il mapping dell'entità in base a qualsiasi campo, non solo all'entità di livello superiore.

1.  Nella scheda **automatizzare le risposte** selezionare gli schemi che si desidera eseguire automaticamente quando viene generato un avviso dalla regola personalizzata. Per altre informazioni sulla creazione e l'automazione dei PlayBook, vedere [rispondere alle minacce](tutorial-respond-threats-playbook.md).

    ![Automatizzare la risposta alle minacce in Sentinel di Azure](media/tutorial-detect-threats-custom/response-automation-custom.png)

1. Selezionare **Revisione** per esaminare tutte le impostazioni per la nuova regola di avviso e quindi selezionare **Crea per inizializzare la regola di avviso**.

   ![Esaminare la query personalizzata](media/tutorial-detect-threats-custom/review-tab.png)

1.  Dopo la creazione dell'avviso, una regola personalizzata viene aggiunta alla tabella in **regole attive**. Da questo elenco è possibile abilitare, disabilitare o eliminare ogni regola.

1.  Per visualizzare i risultati delle regole di avviso create, passare alla pagina **eventi imprevisti**, in cui è possibile valutare, [esaminare gli eventi imprevisti](tutorial-investigate-cases.md)e correggere le minacce.


> [!NOTE]
> Gli avvisi generati in Sentinel di Azure sono disponibili tramite [Microsoft Graph sicurezza](https://aka.ms/securitygraphdocs). Per ulteriori informazioni, vedere la [documentazione relativa agli avvisi di sicurezza Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come iniziare a rilevare le minacce usando Azure Sentinel.

Per informazioni su come automatizzare le risposte alle minacce, [configurare le risposte automatiche alle minacce in Sentinel di Azure](tutorial-respond-threats-playbook.md).

