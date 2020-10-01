---
title: Raccogliere i log delle risorse da una risorsa di Azure e analizzarli con Monitoraggio di Azure
description: Questa esercitazione mostra come configurare le impostazioni di diagnostica per raccogliere i log delle risorse da una risorsa di Azure in un'area di lavoro Log Analytics in cui possono essere analizzati con una query su log.
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: b68d4779d33d2f8a1f1c2bae35f438ad2ccccabb
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090075"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Esercitazione: Raccogliere e analizzare i log delle risorse da una risorsa di Azure

I log delle risorse forniscono informazioni dettagliate sul funzionamento di una risorsa di Azure e sono utili per monitorarne l'integrità e la disponibilità. Le risorse di Azure generano automaticamente i log delle risorse, ma è necessario configurare la posizione in cui devono essere raccolti. Questa esercitazione illustra il processo di creazione di un'impostazione di diagnostica per raccogliere i log delle risorse per una risorsa nella sottoscrizione di Azure e analizzarli con una query su log.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'area di lavoro Log Analytics in Monitoraggio di Azure
> * Creare un'impostazione di diagnostica per raccogliere i log delle risorse 
> * Creare una query su log semplice per analizzare i log


## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessaria una risorsa di Azure da monitorare. È possibile usare tutte le risorse della sottoscrizione di Azure che supportano le impostazioni di diagnostica. Per determinare se una risorsa supporta le impostazioni di diagnostica, passare al relativo menu nel portale di Azure e verificare se è presente un'opzione **Impostazioni di diagnostica** nella sezione **Monitoraggio** del menu.


## <a name="log-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-workspace"></a>Creare un'area di lavoro
Un'area di lavoro Log Analytics in Monitoraggio di Azure raccoglie e indicizza i dati di log da un'ampia varietà di origini e consente l'analisi avanzata tramite un potente linguaggio di query. È necessario che l'area di lavoro Log Analytics sia già presente prima di creare un'impostazione di diagnostica per l'invio dei dati. È possibile usare un'area di lavoro esistente nella sottoscrizione di Azure o crearne una con la procedura seguente. 

> [!NOTE]
> Le aree di lavoro Log Analytics nel menu **Monitoraggio di Azure** consentono di usare i dati, ma per creare e gestire le aree di lavoro è necessario usare il menu **Aree di lavoro Log Analytics**.

1. In **Tutti i servizi** selezionare **Aree di lavoro Log Analytics**.
2. Fare clic su **Aggiungi** nella parte superiore della schermata e specificare i dettagli seguenti per l'area di lavoro:
   - **area di lavoro Log Analytics**: immettere un nome per la nuova area di lavoro. Questo nome deve essere univoco a livello globale in tutte le sottoscrizioni di Monitoraggio di Azure.
   - **Sottoscrizione** selezionare la sottoscrizione in cui archiviare l'area di lavoro. Non è necessario che corrisponda alla sottoscrizione della risorsa monitorata.
   - **Gruppo di risorse**: selezionare un gruppo di risorse esistente oppure fare clic su **Crea nuovo** per crearne uno nuovo. Non è necessario che corrisponda al gruppo di risorse della risorsa monitorata.
   - **Località**: selezionare un'area di Azure o crearne una nuova. Non è necessario che corrisponda alla località della risorsa monitorata.
   - **Piano tariffario**: Selezionare *Con pagamento in base al consumo* come piano tariffario. Sarà possibile modificare il piano tariffario in un secondo momento. Per altre informazioni sui vari piani tariffari disponibili, fare clic sul collegamento **Prezzi di Log Analytics**.

    ![Nuova area di lavoro](media/tutorial-resource-logs/new-workspace.png)

3. Fare clic su **OK** per creare l'area di lavoro.

## <a name="create-a-diagnostic-setting"></a>Creare un'impostazione di diagnostica
Le [impostazioni di diagnostica](../platform/diagnostic-settings.md) definiscono la destinazione per l'invio dei log delle risorse per una risorsa specifica. Possono essere presenti più [destinazioni](../platform/diagnostic-settings.md#destinations) per una singola impostazione di diagnostica, ma in questa esercitazione verrà usata solo un'area di lavoro Log Analytics.

1. Nella sezione **Monitoraggio** del menu della risorsa selezionare **Impostazioni di diagnostica**.
2. Verrà visualizzato il messaggio "Non sono state definite impostazioni di diagnostica". Fare clic su **Aggiungi impostazione di diagnostica**.

    ![Impostazioni di diagnostica](media/tutorial-resource-logs/diagnostic-settings.png)

3. Ogni impostazione di diagnostica è costituita da tre parti di base:
 
   - **Name**: deve essere descrittivo e non prevede effetti significativi.
   - **Destinazioni**: una o più destinazioni a cui inviare i log. Tutti i servizi di Azure condividono lo stesso set di tre destinazioni possibili. Ogni impostazione di diagnostica può definire una o più destinazioni, ma non più di una destinazione di un tipo specifico. 
   - **Categorie**: categorie dei log da inviare a ognuna delle destinazioni. Il set di categorie può variare per ogni servizio di Azure.

4. Selezionare **Send to Log Analytics workspace** (Invia all'area di lavoro Log Analytics) e quindi selezionare l'area di lavoro creata.
5. Selezionare le categorie da raccogliere. Per una definizione delle categorie disponibili, vedere la documentazione relativa a ogni servizio.

    ![Impostazione di diagnostica](media/tutorial-resource-logs/diagnostic-setting.png)

6. Fare clic su **Salva** per salvare le impostazioni di diagnostica.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Usare una query su log per recuperare i log
I dati vengono recuperati da un'area di lavoro Log Analytics tramite una query su log scritta nel linguaggio di query Kusto (KQL). Le informazioni dettagliate e le soluzioni in Monitoraggio di Azure forniranno le query su log per recuperare i dati per un servizio specifico, ma è possibile usare direttamente le query su log e i relativi risultati nel portale di Azure con Log Analytics. 

1. Nella sezione **Monitoraggio** del menu della risorsa selezionare **Log**.
2. Log Analytics verrà aperto con una finestra di query vuota con l'ambito impostato sulla risorsa. Le eventuali query includeranno solo i record di tale risorsa.

    > [!NOTE]
    > Se i log sono stati aperti dal menu Monitoraggio di Azure, l'ambito sarà impostato sull'area di lavoro Log Analytics. In questo caso, le query includeranno tutti i record nell'area di lavoro.
   
    ![Screenshot che mostra i log per un'app per la logica che visualizza una nuova query con il nome dell'app per la logica evidenziato.](media/tutorial-resource-logs/logs.png)

4. Il servizio illustrato nell'esempio esegue la scrittura dei log delle risorse nella tabella **AzureDiagnostics**, ma le tabelle in cui scrivono altri servizi potrebbero essere diverse. Per informazioni sulle tabelle usate dai vari servizi di Azure, vedere [Servizi, schemi e categorie supportati per i log delle risorse di Azure](../platform/resource-logs-schema.md).

    > [!NOTE]
    > Più servizi scrivono i log delle risorse nella tabella AzureDiagnostics. Se si avvia Log Analytics dal menu Monitoraggio di Azure, è necessario aggiungere un'istruzione `where` con la colonna `ResourceProvider` per indicare il servizio specifico. Quando si avvia Log Analytics dal menu di una risorsa, l'ambito è già limitato ai record di questa risorsa, quindi questa colonna non è necessaria. Per le query di esempio, vedere la documentazione del servizio.


5. Digitare una query e fare clic su **Esegui** per esaminare i risultati. 
6. Per un'esercitazione sulla scrittura di query su log, vedere [Introduzione alle query su log in Monitoraggio di Azure](../log-query/get-started-queries.md).

    ![Query di log](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso come raccogliere i log delle risorse in un'area di lavoro Log Analytics, completare un'esercitazione sulla scrittura di query su log per analizzare i dati.

> [!div class="nextstepaction"]
> [Introduzione alle query su log in Monitoraggio di Azure](../log-query/get-started-queries.md)
