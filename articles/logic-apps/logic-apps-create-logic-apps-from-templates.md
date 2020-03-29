---
title: Creare flussi di lavoro delle app per la logica più rapidamente usando modelli predefinitiCreate logic app workflows faster by using prebuilt templates
description: Crea rapidamente flussi di lavoro delle app per la logica usando modelli predefiniti forniti dalle app per la logica di Azure
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: aac0060527af7b7d880f971e7608be3fa44a2d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905105"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Creare flussi di lavoro di app per la logica da modelli predefiniti

Per creare più rapidamente i flussi di lavoro, è possibile usare i modelli di App per la logica, ovvero app per la logica predefinite che seguono criteri usati comunemente. Usare i modelli così come sono oppure modificarli per adattarli al proprio scenario.

Ecco alcune categorie di modelli:

| Tipo di modello | Descrizione | 
| ------------- | ----------- | 
| Modelli cloud dell'organizzazione | Per l'integrazione di BLOB di Azure, Dynamics CRM, Salesforce, Box e include altri connettori per le esigenze cloud dell'organizzazione. Ad esempio, è possibile usare questi modelli per organizzare i clienti potenziali o creare un backup dei dati dei file aziendali. | 
| Modelli per la produttività personale | Migliorare la produttività personale impostando promemoria giornalieri, trasformando importanti elementi di lavoro in elenchi di attività e automatizzando le attività di lunga durata fino ad avere un singolo passaggio di approvazione dell'utente. | 
| Modelli cloud di livello consumer | Per l'integrazione di servizi di social media, ad esempio Twitter, Slack e posta elettronica. Utile per il potenziamento delle iniziative di marketing sui social media. Questi modelli includono anche attività, ad esempio la copia su cloud, che consentono di aumentare la produttività risparmiando il tempo dedicato alle attività tradizionalmente ripetitive. | 
| Modelli di Enterprise Integration Pack | Per la configurazione di pipeline VETER (convalida, estrazione, trasformazione, arricchimento, routing), la ricezione di un documento X12 EDI via AS2 e la conversione in formato XML, nonché gestione di messaggi X12, EDIFACT e AS2. | 
| Modelli di schema di protocollo | Per l'implementazione di schemi di protocollo, ad esempio richiesta-risposta via HTTP e integrazioni tra FTP e SFTP. Usare questi modelli così come sono o come base per creare schemi di protocollo complessi. | 
||| 

Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account di Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare. Per altre informazioni sulla compilazione di un'app per la logica, vedere [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Creare app per la logica dai modelli

1. Se non è già stato fatto, accedere al portale di [Azure.](https://portal.azure.com "Portale di Azure")

2. Scegliere **Crea una risorsa** > app per**la logica**di**integrazione** > aziendale dal menu principale di Azure .

   ![Portale di Azure, Nuovo, Enterprise Integration, App per la logica](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Creare l'app per la logica con le impostazioni specificate nella tabella riportata dopo l'immagine:

   ![Specificare i dettagli dell'app per la logica](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Impostazione | valore | Descrizione | 
   | ------- | ----- | ----------- | 
   | **Nome** | *your-logic-app-name* | Specificare un nome univoco per l'app per la logica. | 
   | **Sottoscrizione** | *nome-sottoscrizione-Azure* | Selezionare la sottoscrizione di Azure da usare. | 
   | **Gruppo di risorse** | *nome-gruppo-di-risorse-di-Azure* | Creare o selezionare un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) per questa app per la logica e per organizzare tutte le risorse associate a questa app. | 
   | **Percorso** | *area-data-center-di-Azure* | Selezionare l'area del data center per la distribuzione dell'app per la logica, ad esempio Stati Uniti occidentali. | 
   | **Analisi dei log** | **Off** (impostazione predefinita) o **On** | Configurare [la registrazione diagnostica](../logic-apps/monitor-logic-apps-log-analytics.md) per l'app per la logica usando i log di Monitoraggio di [Azure.](../log-analytics/log-analytics-overview.md) È necessario disporre già di un'area di lavoro Log Analytics. | 
   |||| 

4. Al termine, selezionare **Aggiungi al dashboard**. L'app per la logica verrà così visualizzata nel dashboard di Azure e aperta dopo la distribuzione. Scegliere **Crea**.

   > [!NOTE]
   > Se non si vuole aggiungere l'app per la logica, per continuare è necessario trovare e aprire manualmente l'app per la logica dopo la distribuzione.

   Quando Azure ha distribuito l'app per la logica, Progettazione app per la logica si apre e visualizza una pagina con un video introduttivo. 
   Sotto il video è possibile trovare i modelli di app per la logica comuni. 

5. Scorrere oltre il video introduttivo e i trigger comuni fino a **Modelli**. Scegliere un modello predefinito. Ad esempio:

   ![Scegliere un modello di app per la logica](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Per creare l'app per la logica da zero, scegliere **App per la logica vuota**.

   Quando si seleziona un modello predefinito, è possibile visualizzare altre informazioni sul modello. 
   Ad esempio:

   ![Scegliere un modello predefinito](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Per continuare con il modello selezionato, scegliere **Usa questo modello**. 

7. In base ai connettori presenti nel modello, viene chiesto di eseguire una di queste operazioni:

   * Accedere con le proprie credenziali ai sistemi o servizi a cui fa riferimento il modello.

   * Creare connessioni per tutti i servizi o sistemi a cui fa riferimento il modello. Per creare una connessione, specificare un nome per la connessione e, se necessario, selezionare la risorsa che si vuole usare. 

   * Se queste connessioni sono già state configurate, scegliere **Continua**.

   Ad esempio:

   ![Creare le connessioni](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Al termine, l'app per la logica si apre e viene visualizzata nella finestra Progettazione app per la logica.

   > [!TIP]
   > Per tornare alla visualizzazione del modello, scegliere **Modelli** sulla barra degli strumenti della finestra di progettazione. Questa azione elimina eventuali modifiche non salvate, quindi viene visualizzato un messaggio di avviso per confermare la richiesta.

8. Continuare a compilare l'app per la logica.

   > [!NOTE] 
   > Molti modelli includono connettori che potrebbero avere già compilato le proprietà obbligatorie. Alcuni modelli tuttavia possono comunque richiedere l'inserimento di valori per poter distribuire correttamente l'app per la logica. Se si tenta di eseguire la distribuzione senza completare i campi di proprietà mancanti, viene visualizzato un messaggio di errore. 

## <a name="update-logic-apps-with-templates"></a>Aggiornare le app per la logica con i modelli

1. Nel [portale di Azure](https://portal.azure.com "Portale di Azure")individuare e aprire l'app per la logica in progettazione app per la logica.

2. Nella barra degli strumenti della finestra di progettazione scegliere **Modelli**. Questa azione elimina eventuali modifiche non salvate, quindi viene visualizzato un messaggio di avviso che richiede di confermare che si vuole continuare. Per confermare, scegliere **OK**. Ad esempio:

   ![Scegliere "Modelli"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Scorrere oltre il video introduttivo e i trigger comuni fino a **Modelli**. Scegliere un modello predefinito. Ad esempio:

   ![Scegliere un modello di app per la logica](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Quando si seleziona un modello predefinito, è possibile visualizzare altre informazioni sul modello. 
   Ad esempio:

   ![Scegliere un modello predefinito](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Per continuare con il modello selezionato, scegliere **Usa questo modello**. 

5. In base ai connettori presenti nel modello, viene chiesto di eseguire una di queste operazioni:

   * Accedere con le proprie credenziali ai sistemi o servizi a cui fa riferimento il modello.

   * Creare connessioni per tutti i servizi o sistemi a cui fa riferimento il modello. Per creare una connessione, specificare un nome per la connessione e, se necessario, selezionare la risorsa che si vuole usare. 

   * Se queste connessioni sono già state configurate, scegliere **Continua**.

   ![Creare le connessioni](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   L'app per la logica si apre e viene visualizzata nella finestra Progettazione app per la logica.

8. Continuare a compilare l'app per la logica. 

   > [!TIP]
   > Se le modifiche non sono state salvate, è possibile eliminare il lavoro fatto e tornare all'app per la logica precedente. Nella barra degli strumenti della finestra di progettazione scegliere **Rimuovi**.

> [!NOTE] 
> Molti modelli includono connettori che potrebbero avere già compilato le proprietà obbligatorie. Alcuni modelli tuttavia possono comunque richiedere l'inserimento di valori per poter distribuire correttamente l'app per la logica. Se si tenta di eseguire la distribuzione senza completare i campi di proprietà mancanti, viene visualizzato un messaggio di errore.

## <a name="deploy-logic-apps-built-from-templates"></a>Distribuire le app per la logica create da modelli

Dopo aver apportato le modifiche al modello, è possibile salvarle. Questa azione pubblica anche automaticamente l'app per la logica.

Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

![Salvare e pubblicare l'app per la logica](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla compilazione di app per la logica con esempi, scenari, casi di successo dei clienti e procedure dettagliate.

> [!div class="nextstepaction"]
> [Scenari comuni, esempi, esercitazioni e procedure dettagliate per le app per la logica di Azure](../logic-apps/logic-apps-examples-and-scenarios.md)