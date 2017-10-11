---
title: "Scalabilità automatica delle risorse di Azure in base ai dati sulle prestazioni o a una pianificazione| Microsoft Docs"
description: "Creare un'impostazione di scalabilità automatica per un piano di servizio app usando i dati di metrica e una pianificazione"
author: anirudhcavale
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: ancav
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7e8d97657e03b0eaff76365d3988f51c773e3b55
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Creare un'impostazione di scalabilità automatica per le risorse di Azure in base ai dati sulle prestazioni o a una pianificazione

Le impostazioni di scalabilità automatica consentono di aggiungere o rimuovere le istanze del servizio in base alle condizioni predefinite. Queste impostazioni possono essere create nel portale. Questo metodo offre un'interfaccia utente basata sul browser per la creazione e la configurazione delle impostazioni di scalabilità automatica. Questa esercitazione consente di:

1. creare un servizio app
2. configurare un'impostazione di scalabilità automatica
3. attivare un'azione per aumentare le istanze
4. attivare un'azione per ridurre le istanze

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="create-a-web-app-and-app-service-plan"></a>Creare un'app Web e un piano di servizio app
1. Fare clic sull'opzione **Nuovo** nel riquadro di navigazione a sinistra
2. Cercare e selezionare l'elemento *App Web* e fare clic su **Crea**
3. Selezionare un nome per l'app, ad esempio *MyTestScaleWebApp*. Creare un nuovo gruppo di risorse *myResourceGroup' e posizionarlo in un gruppo di risorse di propria scelta.
4. Entro pochi minuti, verrà effettuato il provisioning delle risorse. Si fa riferimento all'app Web e al piano di servizio app corrispondente appena creati nella restante parte di questa esercitazione.

    ![Creare un nuovo servizio app nel portale](./media/monitor-tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Passare alle impostazioni di scalabilità automatica
1. Selezionare l'opzione **Monitoraggio** nel riquadro di navigazione a sinistra. Quando la pagina si è caricata selezionare la scheda **Scalabilità automatica**.
2. Qui viene mostrato un elenco delle risorse nella sottoscrizione che supporta la scalabilità automatica. Identificare il piano di servizio app che è stato creato in precedenza nell'esercitazione, fare clic su di esso.

    ![Passare alle impostazioni di scalabilità automatica](./media/monitor-tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. Tra le impostazioni di scalabilità automatica fare clic sul pulsante **Abilita scalabilità automatica**

I passaggi successivi consentono di compilare la schermata di scalabilità automatica, che apparirà come illustrato nella figura seguente:

   ![Salvare le impostazioni di scalabilità automatica](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

 ## <a name="configure-default-profile"></a>Configurare il profilo predefinito
1. Inserire un **Nome** per l'impostazione di scalabilità automatica
2. Nel profilo predefinito assicurarsi che **Modalità di ridimensionamento** sia impostato su "Ridimensiona in base a un numero di istanze specifico"
3. Impostare il numero di istanze su 1. Questa impostazione fa in modo che quando non sono attivi oppure operativi altri profili, il profilo predefinito restituisce 1 come numero di istanze.

  ![Passare alle impostazioni di scalabilità automatica](./media/monitor-tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrence-profile"></a>Creare un profilo Ricorrenza

1. Fare clic sul collegamento **Add a scale condition** (Aggiungi una condizione di scalabilità) nel profilo predefinito

2. Modificare il **Nome** del profilo impostandolo su "Monday to Friday profile" (Profilo da lunedì a venerdì)

3. Verificare che **Modalità di ridimensionamento** sia impostato su "Ridimensiona in base a una metrica"

4. Per **Limiti per le istanze** impostare **Minimo** su "1", **Massimo** su "2" e **Predefinito** su "1". In questo modo si garantisce che il profilo non applichi la scalabilità automatica al piano di servizio per avere meno di 1 istanza o più di 2 istanze. Se il profilo non dispone di dati sufficienti per prendere una decisione, usa il numero predefinito di istanze, in questo caso 1.

5. Selezionare "Ripeti in giorni specifici" per **Pianificazione**

6. Impostare il profilo affinché si ripeta dal lunedì al venerdì, dalle 09:00 PST alle 18:00 PST. In questo modo il profilo è attivo e applicabile dalle 09: 00 alle 18: 00, dal lunedì al venerdì. Al di fuori di questo orario, il profilo "Predefinito" è il profilo usato dall'impostazione di scalabilità automatica.

## <a name="create-a-scale-out-rule"></a>Creare un regola per aumentare il numero di istanze

1. In "Monday to Friday profile" (Profilo da lunedì a venerdì)

2. Fare clic sul collegamento **Aggiungi una regola**

3. Impostare **Origine metrica** su "other resource" (altra risorsa). Impostare **Tipo di risorsa** su "Servizi app" e **Risorsa** sull'app Web creata precedentemente in questa esercitazione.

4. Impostare **Aggregazione temporale** su "Totale", **Nome metrica** su "Richieste" e **Statistica intervallo di tempo** su "Somma"

5. Impostare **Operatore** su "Greater than" (Maggiore di), **Soglia** su "10" e **Durata** su "5" minuti.

6. Selezionare l'**operazione** "Aumenta numero di", impostare le opzioni **Numero di istanze** su "1" e **Tempo di raffreddamento** su "5" minuti

7. Fare clic sul pulsante **Aggiungi**

Questa regola fa in modo che se l'app Web riceve più di 10 richieste in un periodo massimo 5 minuti, viene aggiunta un'altra istanza al piano del servizio app per la gestione del carico.

   ![Creare un regola per aumentare il numero di istanze](./media/monitor-tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>Creare un regola per ridurre il numero di istanze
Si consiglia sempre di avere sempre una regola per ridurre il numero di istanze e una per aumentarle. La presenza di entrambe le regole fa in modo che non si esegua un provisioning eccessivo sulle risorse. Provisioning eccessivo significa che l'utente dispone di un numero maggiore istanze in esecuzione di quelle necessarie per gestire il carico corrente. 

1. In "Monday to Friday profile" (Profilo da lunedì a venerdì)

2. Fare clic sul collegamento **Aggiungi una regola**

3. Impostare **Origine metrica** su "other resource" (altra risorsa). Impostare **Tipo di risorsa** su "Servizi app" e **Risorsa** sull'app Web creata precedentemente in questa esercitazione.

4. Impostare **Aggregazione temporale** su "Totale", **Nome metrica** su "Richieste" e **Statistica intervallo di tempo** su "Media"

5. Impostare **Operatore** su "Less than" (Minore di), **Soglia** su "5" e **Durata** su "5" minuti.

6. Selezionare l'**operazione** "Riduci numero di", impostare le opzioni **Numero di istanze** su "1" e **Tempo di raffreddamento** su "5" minuti

7. Fare clic sul pulsante **Aggiungi**

    ![Creare un regola per ridurre il numero di istanze](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. **Salvare** le impostazione di scalabilità automatica

    ![Salvare le impostazioni di scalabilità automatica](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Attivare un'azione per aumentare le istanze
Per attivare una condizione per aumentare le istanze nella scalabilità automatica appena creata, l'app Web deve avere di più di 10 richieste in meno di 5 minuti.

1. Aprire una finestra del browser e passare all'app Web creata precedentemente in questa esercitazione. L'URL per l'app Web è disponibile nel portale di Azure passando alla risorsa dell'app Web e facendo clic sul pulsante **Sfoglia** nella scheda "Panoramica".

2. In rapida successione ricaricare la pagina almeno 10 volte

3. Selezionare l'opzione **Monitoraggio** nel riquadro di navigazione a sinistra. Quando la pagina si è caricata selezionare la scheda **Scalabilità automatica**.

4. Nell'elenco selezionare il piano di servizio app usato in questa esercitazione

5. Nell'impostazione di scalabilità automatica, fare clic sulla scheda **Cronologia di esecuzione**

6. Viene visualizzato un grafico che riflette il numero di istanze del piano di servizio app nel tempo

7. In pochi minuti, il numero di istanze aumenterà da 1 a 2.

8. Nel grafico, vengono visualizzate le voci di log attività per ogni azione di scalabilità adottata da questa impostazione di scalabilità automatica.

## <a name="trigger-scale-in-action"></a>Attivare un'azione per ridurre le istanze
Condizione per ridurre le istanze nei trigger di scalabilità automatica se ci sono meno di 5 richieste per l'app Web in un periodo di 10 minuti. 

1. Assicura che non vengano inviate richieste all'app Web

2. Caricare il portale di Azure

3. Selezionare l'opzione **Monitoraggio** nel riquadro di navigazione a sinistra. Quando la pagina si è caricata selezionare la scheda **Scalabilità automatica**.

4. Nell'elenco selezionare il piano di servizio app usato in questa esercitazione

5. Nell'impostazione di scalabilità automatica, fare clic sulla scheda **Cronologia di esecuzione**

6. Viene visualizzato un grafico che riflette il numero di istanze del piano di servizio app nel tempo.

7. In pochi minuti, il numero di istanze scende da 2 a 1. Il processo richiede almeno dieci minuti.  

8. Nel grafico, vengono visualizzate le voci del set del log attività corrispondenti per ogni azione di scalabilità adottata da questa impostazione di scalabilità automatica

    ![Visualizzare le azioni per ridurre le istanze](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Pulire le risorse

1. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare l'app Web creata in questa esercitazione.

2. Nella pagina della risorsa fare clic su **Elimina**, confermare l'eliminazione digitando **sì** nella casella di testo e quindi fare clic su **Elimina**.

3. Quindi selezionare la risorsa del piano di servizio app e fare clic su **Elimina**

4. Confermare l'eliminazione digitando **sì** nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una semplice app Web e il piano di servizio app. Quindi è stata creata un'impostazione di scalabilità automatica che ridimensiona il piano di servizio app in base al numero di richieste ricevute dall'app Web. Per altre informazioni sulle impostazioni di scalabilità automatica procedere con la panoramica sulla scalabilità automatica.

> [!div class="nextstepaction"]
> [Archiviare i dati di monitoraggio](./monitor-tutorial-archive-monitoring-data.md)

