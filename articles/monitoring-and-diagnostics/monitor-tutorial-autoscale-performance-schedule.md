---
title: "Scalabilità automatica delle risorse di Azure in base ai dati sulle prestazioni o a una pianificazione| Microsoft Docs"
description: "Creare un'impostazione di scalabilità automatica per un piano di servizio app usando i dati di metrica e una pianificazione"
author: anirudhcavale
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 12/11/2017
ms.author: ancav
ms.custom: mvc
ms.openlocfilehash: f956a9436126c2a46d80b20770d7d86309e5b3af
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Creare un'impostazione di scalabilità automatica per le risorse di Azure in base ai dati sulle prestazioni o a una pianificazione

Le impostazioni di scalabilità automatica consentono di aggiungere o rimuovere le istanze del servizio in base alle condizioni predefinite. Queste impostazioni possono essere create nel portale. Questo metodo offre un'interfaccia utente basata sul browser per la creazione e la configurazione delle impostazioni di scalabilità automatica. 

In questa esercitazione si apprenderà come 
> [!div class="checklist"]
> * Creare un'app Web e un piano di servizio app
> * Configurare le regole di scalabilità automatica per aumentare e ridurre le istanze in base al numero di richieste ricevute da un'app Web
> * Attivare un'azione di aumento delle istanze e osservare l'incremento del numero di istanze
> * Attivare un'azione di riduzione delle istanze e osservare la riduzione del numero di istanze
> * Pulire le risorse

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="create-a-web-app-and-app-service-plan"></a>Creare un'app Web e un piano di servizio app
1. Fare clic sull'opzione **Crea una risorsa** nel riquadro di spostamento a sinistra.
2. Cercare e selezionare l'elemento *App Web* e fare clic su **Crea**.
3. Selezionare un nome per l'app, ad esempio *MyTestScaleWebApp*. Creare un nuovo gruppo di risorse *myResourceGroup' e posizionarlo in un gruppo di risorse di propria scelta.

Entro pochi minuti, verrà effettuato il provisioning delle risorse. Usare l'app Web e il piano di servizio app corrispondente nella restante parte di questa esercitazione.

   ![Creare un nuovo servizio app nel portale](./media/monitor-tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Passare alle impostazioni di scalabilità automatica
1. Selezionare l'opzione **Monitoraggio** nel riquadro di navigazione a sinistra. Quando la pagina si è caricata, selezionare la scheda **Scalabilità automatica**.
2. Qui viene mostrato un elenco delle risorse nella sottoscrizione che supporta la scalabilità automatica. Identificare il piano di servizio app che è stato creato in precedenza nell'esercitazione, fare clic su di esso.

    ![Passare alle impostazioni di scalabilità automatica](./media/monitor-tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. Tra le impostazioni di scalabilità automatica fare clic sul pulsante **Abilita scalabilità automatica**.

I passaggi successivi consentono di compilare la schermata di scalabilità automatica, in modo da ottenere un risultato simile a quanto illustrato nella figura seguente:

   ![Salvare le impostazioni di scalabilità automatica](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

 ## <a name="configure-default-profile"></a>Configurare il profilo predefinito
1. Specificare il valore di **Nome** per l'impostazione di scalabilità automatica.
2. Nel profilo predefinito assicurarsi che l'opzione **Modalità di ridimensionamento** sia impostata su "Ridimensiona in base a un numero di istanze specifico".
3. Impostare il numero di istanze su **1**. Questa impostazione fa in modo che quando non sono attivi oppure operativi altri profili, il profilo predefinito restituisce 1 come numero di istanze.

  ![Passare alle impostazioni di scalabilità automatica](./media/monitor-tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrance-profile"></a>Creare un profilo di ricorrenza

1. Fare clic sul collegamento **Add a scale condition** (Aggiungi una condizione di scalabilità) nel profilo predefinito.

2. Modificare il valore di **Nome** del profilo impostandolo su "Monday to Friday profile" (Profilo da lunedì a venerdì).

3. Verificare che l'opzione **Modalità di ridimensionamento** sia impostata su "Ridimensiona in base a una metrica".

4. Per **Limiti per le istanze** impostare **Minimo** su "1", **Massimo** su "2" e **Predefinito** su "1". Questa impostazione garantisce che il profilo non applichi la scalabilità automatica al piano di servizio per avere meno di 1 istanza o più di 2 istanze. Se il profilo non dispone di dati sufficienti per prendere una decisione, usa il numero predefinito di istanze, in questo caso 1.

5. Per **Pianificazione** selezionare "Ripeti in giorni specifici".

6. Impostare il profilo affinché si ripeta dal lunedì al venerdì, dalle 09:00 PST alle 18:00 PST. Questa impostazione garantisce che il profilo è attivo e applicabile solo dalle 09:00 alle 18:00, dal lunedì al venerdì. Al di fuori di questo orario, il profilo "Predefinito" è il profilo usato dall'impostazione di scalabilità automatica.

## <a name="create-a-scale-out-rule"></a>Creare un regola per aumentare il numero di istanze

1. In "Monday to Friday profile" (Profilo da lunedì a venerdì).

2. Fare clic sul collegamento **Aggiungi una regola**.

3. Impostare **Origine metrica** su "other resource" (altra risorsa). Impostare **Tipo di risorsa** su "Servizi app" e **Risorsa** sull'app Web creata precedentemente in questa esercitazione.

4. Impostare **Aggregazione temporale** su "Totale", **Nome metrica** su "Richieste" e **Statistica intervallo di tempo** su "Somma".

5. Impostare **Operatore** su "Greater than" (Maggiore di), **Soglia** su "10" e **Durata** su "5" minuti.

6. Impostare **Operazione** su "Aumenta numero di", **Numero di istanze** su "1" e **Disattiva regole dopo** su "5" minuti.

7. Fare clic su **Add** .

Questa regola fa in modo che se l'app Web riceve più di 10 richieste in un periodo massimo 5 minuti, viene aggiunta un'altra istanza al piano del servizio app per la gestione del carico.

   ![Creare un regola per aumentare il numero di istanze](./media/monitor-tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>Creare un regola per ridurre il numero di istanze
Si consiglia sempre di avere sempre una regola per ridurre il numero di istanze e una per aumentarle. La presenza di entrambe le regole fa in modo che non si esegua un provisioning eccessivo sulle risorse. Provisioning eccessivo significa che l'utente dispone di un numero maggiore istanze in esecuzione di quelle necessarie per gestire il carico corrente. 

1. In "Monday to Friday profile" (Profilo da lunedì a venerdì).

2. Fare clic sul collegamento **Aggiungi una regola**.

3. Impostare **Origine metrica** su "other resource" (altra risorsa). Impostare **Tipo di risorsa** su "Servizi app" e **Risorsa** sull'app Web creata precedentemente in questa esercitazione.

4. Impostare **Aggregazione temporale** su "Totale", **Nome metrica** su "Richieste" e **Statistica intervallo di tempo** su "Media".

5. Impostare **Operatore** su "Less than" (Minore di), **Soglia** su "5" e **Durata** su "5" minuti.

6. Impostare **Operazione** su "Riduci numero di", **Numero di istanze** su "1" e **Disattiva regole dopo** su "5" minuti.

7. Fare clic su **Add** .

    ![Creare un regola per ridurre il numero di istanze](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. Fare clic su **Salva** per salvare l'impostazione di scalabilità automatica.

    ![Salvare le impostazioni di scalabilità automatica](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Attivare un'azione per aumentare le istanze
Per attivare una condizione per aumentare le istanze nella scalabilità automatica appena creata, l'app Web deve avere di più di 10 richieste in meno di 5 minuti.

1. Aprire una finestra del browser e passare all'app Web creata precedentemente in questa esercitazione. L'URL per l'app Web è disponibile nel portale di Azure passando alla risorsa dell'app Web e facendo clic sul pulsante **Sfoglia** nella scheda "Panoramica".

2. In rapida successione ricaricare la pagina più di 10 volte.

3. Selezionare l'opzione **Monitoraggio** nel riquadro di navigazione a sinistra. Quando la pagina si è caricata selezionare la scheda **Scalabilità automatica**.

4. Nell'elenco selezionare il piano di servizio app usato in questa esercitazione.

5. Nell'impostazione di scalabilità automatica fare clic sulla scheda **Cronologia**.

6. Viene visualizzato un grafico che riflette il numero di istanze del piano di servizio app nel tempo.

7. In pochi minuti, il numero di istanze aumenterà da 1 a 2.

8. Nel grafico, vengono visualizzate le voci di log attività per ogni azione di scalabilità adottata da questa impostazione di scalabilità automatica.

## <a name="trigger-scale-in-action"></a>Attivare un'azione per ridurre le istanze
Condizione per ridurre le istanze nei trigger di scalabilità automatica se ci sono meno di 5 richieste per l'app Web in un periodo di 10 minuti. 

1. Assicurarsi che non vengano inviate richieste all'app Web.

2. Caricare il portale di Azure.

3. Selezionare l'opzione **Monitoraggio** nel riquadro di navigazione a sinistra. Quando la pagina si è caricata selezionare la scheda **Scalabilità automatica**.

4. Nell'elenco selezionare il piano di servizio app usato in questa esercitazione.

5. Nell'impostazione di scalabilità automatica fare clic sulla scheda **Cronologia**.

6. Viene visualizzato un grafico che riflette il numero di istanze del piano di servizio app nel tempo.

7. In pochi minuti, il numero di istanze scende da 2 a 1. Il processo richiede almeno 100 minuti.  

8. Sotto il grafico vengono visualizzate le voci del set del log attività corrispondenti per ogni azione di ridimensionamento eseguita dall'impostazione di scalabilità automatica.

    ![Visualizzare le azioni per ridurre le istanze](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Pulire le risorse

1. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare l'app Web creata in questa esercitazione.

2. Nella pagina della risorsa fare clic su **Elimina**, confermare l'eliminazione digitando **sì** nella casella di testo e quindi fare clic su **Elimina**.

3. Selezionare la risorsa del piano di servizio app e fare clic su **Elimina**.

4. Confermare l'eliminazione digitando **sì** nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come  
> [!div class="checklist"]
> * creare un'app Web e un piano di servizio app
> * configurare le regole di scalabilità automatica per aumentare e ridurre le istanze in base al numero di richieste ricevute dall'app Web
> * attivare un'azione di aumento delle istanze e osservare l'incremento del numero di istanze
> * attivare un'azione di riduzione delle istanze e osservare la riduzione del numero di istanze
> * eliminare le risorse


Per altre informazioni sulle impostazioni di scalabilità automatica procedere con la [panoramica sulla scalabilità automatica](monitoring-overview-autoscale.md).

> [!div class="nextstepaction"]
> [Archiviare i dati di monitoraggio](monitor-tutorial-archive-monitoring-data.md)
