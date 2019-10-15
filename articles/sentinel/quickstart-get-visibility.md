---
title: Avvio rapido di Azure Sentinel - Introduzione ad Azure Sentinel | Microsoft Docs
description: Avvio rapido di Azure Sentinel - Introduzione all'anteprima di Azure Sentinel
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 676a41b64b79be6b11c3ef22b34d3de0e37e2a92
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023801"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Guida introduttiva: Introduzione ad Azure Sentinel




Questa guida di avvio rapido illustra come usare Azure Sentinel per visualizzare e monitorare rapidamente gli eventi che si verificano nell'ambiente. Dopo aver connesso le origini dati ad Azure Sentinel, si ottengono visualizzazione e analisi immediate dei dati, grazie alle quali è possibile sapere ciò che avviene in tutte le origini dati connesse. Azure Sentinel include cartelle di lavoro che consentono di sfruttare i potenti strumenti già disponibili in Azure, oltre a tabelle e grafici integrati per l'analisi di log e query. È possibile usare le cartelle di lavoro predefinite o creare facilmente una nuova cartella di lavoro, da zero o in base a una cartella di lavoro esistente. 

## <a name="get-visualization"></a>Ottenere visualizzazioni

Per visualizzare e ottenere analisi di ciò che accade nell'ambiente, in primo luogo esaminare il dashboard di panoramica per avere un'idea delle condizioni di sicurezza dell'organizzazione. È possibile fare clic su ogni elemento di questi riquadri per eseguire il drill-down ai dati non elaborati da cui vengono creati. Per ridurre i risultati non significativi e minimizzare il numero di avvisi da esaminare e di cui ricercare le cause, Azure Sentinel usa una tecnica di fusione per correlare gli avvisi in eventi imprevisti. Gli **eventi imprevisti** sono gruppi di avvisi correlati che insieme creano un evento imprevisto di utilità pratica ai fini delle indagini e della risoluzione.

- Nel portale di Azure selezionare Azure Sentinel e quindi selezionare l'area di lavoro che si vuole monitorare.

  ![Panoramica di Azure Sentinel](./media/qs-get-visibility/overview.png)

- La barra degli strumenti nella parte superiore indica il numero di eventi che si sono verificati nel periodo di tempo selezionato e lo confronta con le 24 ore precedenti. La barra degli strumenti indica gli avvisi che sono stati attivati da questi eventi (il piccolo numero rappresenta i cambiamenti nelle ultime 24 ore) e quanti eventi sono aperti, in corso e chiusi. Qui si può controllare se ci sia un forte aumento o calo nel numero degli eventi. Se si verifica un calo, è possibile che una connessione abbia smesso di inviare segnalazioni ad Azure Sentinel. Se si verifica un aumento, può essere accaduto qualcosa di sospetto. Controllare se sono presenti nuovi avvisi.

   ![Imbuto di Azure Sentinel](./media/qs-get-visibility/funnel.png)

Il corpo principale della pagina di panoramica offre informazioni dettagliate a colpo d'occhio sullo stato di sicurezza dell'area di lavoro:

- **Events and alerts over time** (Eventi e avvisi nel tempo): elenca il numero di eventi e il numero di avvisi creato da tali eventi. Se è presente un picco insolito, dovrebbero comparire avvisi in proposito. La presenza di un picco di eventi insolito senza avvisi associati potrebbe rappresentare un motivo di preoccupazione.

- **Potential malicious events** (Potenziali eventi dannosi): quando viene rilevato traffico da origini note per essere dannose, Azure Sentinel lo segnala sulla mappa. Se il segnale è arancione, si tratta di traffico in ingresso: qualcuno sta provando ad accedere all'organizzazione da un indirizzo IP dannoso noto. Se è rosso indica attività in uscita, ovvero che dati della rete interna vengono trasmessi all'esterno dell'organizzazione verso un indirizzo IP dannoso noto.

   ![Mappa di Azure Sentinel](./media/qs-get-visibility/map.png)


- **Eventi imprevisti recenti**: per visualizzare gli eventi imprevisti recenti, la relativa gravità e il numero di avvisi associati all'evento imprevisto. La comparsa di un picco improvviso di un tipo specifico di avviso potrebbe indicare un attacco attivo in corso. Ad esempio, se è visibile un picco improvviso di 20 eventi Pass-the-Hash da Azure ATP, l'organizzazione potrebbe essere sotto attacco.

- **Data source anomalies** (Anomalie dell'origine dati): gli analisti di dati di Microsoft hanno creato modelli che cercano continuamente anomalie nei dati provenienti dalle origini dati. Se non sono presenti anomalie, non compare nulla. Se vengono rilevate anomalie, è necessario approfondire per capire cosa è successo. Ad esempio, fare clic sul picco nelle attività di Azure. È possibile fare clic su **Chart** (Grafico) per vedere quando si è verificato il picco e quindi filtrare le attività che si sono verificate in quel periodo di tempo per vedere cosa ha causato il picco.

   ![Mappa di Azure Sentinel](./media/qs-get-visibility/anomolies.png)

## Usare cartelle di lavoro predefinite<a name="dashboards"></a>

Le cartelle di lavoro predefinite offrono dati integrati provenienti dalle origini dati connesse e consentono quindi di esaminare in modo approfondito gli eventi generati in tali servizi. Sono disponibili cartelle di lavoro predefinite per Azure AD, eventi relativi ad attività di Azure ed eventi locali, che possono essere dati di eventi di Windows provenienti da server, avvisi proprietari, eventuali terze parti, inclusi i log del traffico firewall, Office 365 e protocolli non sicuri basati su eventi di Windows. Le cartelle di lavoro sono basate sulle cartelle di lavoro di Monitoraggio di Azure per offrire una maggiore personalizzazione e flessibilità durante la progettazione di una cartella di lavoro personalizzata. Per altre informazioni, vedere l'articolo sulle [cartelle di lavoro](../azure-monitor/app/usage-workbooks.md).

1. In **Impostazioni** selezionare **Cartelle di lavoro**. In **Installate** è possibile visualizzare tutte le cartelle di lavoro installate. In **Tutte** è possibile visualizzare l'intera raccolta di cartelle di lavoro predefinite disponibili per l'installazione. 
2. Cercare una cartella di lavoro specifica per visualizzare l'elenco completo e una descrizione delle caratteristiche di ognuna. 
3. Supponendo che si usi Azure AD, per iniziare a usare Azure Sentinel è consigliabile installare almeno le cartelle di lavoro seguenti:
   - **Azure AD**: usare uno dei seguenti o entrambi:
       - **Azure AD sign-ins** (Accessi ad Azure AD) analizza gli accessi nel tempo per verificare se esistono anomalie. Questa cartella di lavoro segnala i tentativi di accesso non riusciti da parte di applicazioni, dispositivi e posizioni in modo, permettendo di notare immediatamente eventuali attività insolite. Prestare attenzione alla presenza di più accessi non riusciti. 
       - **Azure AD audit logs** (Log di controllo di Azure AD) analizza le attività di amministrazione, ad esempio modifiche agli utenti, come aggiunte rimozioni e così via, creazione di gruppi e modifiche.  

   - Aggiungere una cartella di lavoro per il firewall, ad esempio la cartella di lavoro Palo Alto. La cartella di lavoro analizza il traffico del firewall, offrendo correlazioni tra i dati del firewall e gli eventi di minaccia ed evidenziando gli eventi sospetti tra le entità. Le cartelle di lavoro forniscono informazioni sulle tendenze del traffico e consentono di eseguire il drill-down e filtrare i risultati. 

      ![Dashboard Palo Alto](./media/qs-get-visibility/palo-alto-week-query.png)


È possibile personalizzare le cartelle di lavoro modificando la query principale ![pulsante](./media/qs-get-visibility/edit-query-button.png). È possibile fare clic sul pulsante ![pulsante](./media/qs-get-visibility/go-to-la-button.png) per passare a[Log Analytics e modificare la query](../azure-monitor/log-query/get-started-portal.md) ed è possibile selezionare i puntini di sospensione (...) e quindi **Personalizza dati riquadro**, che consente di modificare il filtro temporale principale o rimuovere specifici riquadri dalla cartella di lavoro.

Per altre informazioni sull'uso delle query, vedere [Esercitazione: Visualizzare i dati in Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Aggiungere un nuovo riquadro

Se si vuole aggiungere un nuovo riquadro, è possibile aggiungerlo a una cartella di lavoro esistente, a una creata autonomamente oppure a una cartella di lavoro predefinita di Azure Sentinel. 
1. In Log Analytics creare un riquadro seguendo le istruzioni disponibili in [Esercitazione: Visualizzare i dati in Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Dopo aver creato il riquadro, in **Aggiungi** selezionare la cartella di lavoro in cui si vuole che compaia il riquadro.

## <a name="create-new-workbooks"></a>Creare nuove cartelle di lavoro
È possibile creare una nuova cartella di lavoro da zero o usarne una predefinita come base per la nuova cartella di lavoro.

1. Per creare una nuova cartella di lavoro da zero, selezionare **Cartelle di lavoro** e quindi **+Nuova cartella di lavoro**.
2. Selezionare la sottoscrizione in cui creare la cartella di lavoro e assegnargli un nome descrittivo. Ogni cartella di lavoro è una normale risorsa di Azure, ed è possibile assegnargli ruoli (controllo degli accessi in base al ruolo) per definire e limitare gli accessi. 
3. Per fare in modo che sia visualizzata tra le cartelle di lavoro disponibili e potervi aggiungere visualizzazioni, è necessario condividerla. Fare clic su **Condividi** e quindi su **Gestisci utenti**. 
 
1. Usare **Verifica l'accesso** e le **Assegnazioni di ruolo** come si farebbe per qualsiasi altra risorsa di Azure. Per altre informazioni, vedere [Condividere le cartelle di lavoro di Azure tramite il Controllo degli accessi in base al ruolo](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-workbook-examples"></a>Esempi di nuove cartelle di lavoro

La query di esempio seguente consente di confrontare le tendenze del traffico tra le varie settimane. È possibile cambiare facilmente il fornitore di dispositivi e l'origine dati su cui eseguire la query. Questo esempio usa SecurityEvent di Windows, è possibile modificarlo per eseguirlo su AzureActivity o CommonSecurityLog su qualsiasi altro firewall.

     |where DeviceVendor = = "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Si potrebbe voler creare una query che incorpora i dati da più origini. È possibile creare una query che esamina i log di controllo di Azure Active Directory cercando i nuovi utenti che sono stati creati, quindi controlla i log di Azure per verificare se l'utente ha iniziato ad apportare modifiche alle assegnazioni di ruolo entro 24 ore dalla creazione. Questa attività sospetta verrebbe mostrata in questo dashboard:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

È possibile creare cartelle di lavoro diverse in base al ruolo della persona che sta esaminando i dati e a ciò che sta cercando. Ad esempio, si può creare per l'amministratore di rete una cartella di lavoro che includa i dati del firewall. È anche possibile creare cartelle di lavoro in base alla frequenza di consultazione, se ci sono elementi da controllare ogni giorno e altri da controllare ogni ora. Ad esempio, si possono esaminare gli accessi ad Azure AD ogni ora per cercare eventuali anomalie. 

## <a name="create-new-detections"></a>Creare nuovi rilevamenti

È possibile generare rilevamenti sulle [origini dati connesse ad Azure Sentinel](connect-data-sources.md) per analizzare le minacce nell'organizzazione.

Quando si crea una nuova funzionalità di rilevamento, sfruttare i rilevamenti incorporati predisposti dai ricercatori di sicurezza Microsoft più adatti alle origini dati connesse.

Per visualizzare tutti i rilevamenti predefiniti, passare ad **Analisi** e quindi scegliere **Rule templates** (Modelli di regole). Questa scheda contiene tutte le regole predefinite di Azure Sentinel.

   ![Usare i rilevamenti predefiniti per individuare le minacce con Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Per altre informazioni su come ottenere rilevamenti predefiniti, vedere [Esercitazione: Rilevare le minacce predefinite](tutorial-detect-threats-built-in.md).
 
## <a name="next-steps"></a>Passaggi successivi
In questo argomento di avvio rapido si è appreso come iniziare a usare Azure Sentinel. Continuare con l'esercitazione su [come rilevare le minacce](tutorial-detect-threats-built-in.md).
> [!div class="nextstepaction"]
> [Creare regole personalizzate di rilevamento delle minacce](tutorial-detect-threats-custom.md) per automatizzare le risposte alle minacce.

