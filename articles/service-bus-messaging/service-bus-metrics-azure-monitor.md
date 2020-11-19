---
title: Metriche del bus di sevizio di Azure in Monitoraggio di Azure| Microsoft Docs
description: Questo articolo illustra come usare Monitoraggio di Azure per monitorare le entità del bus di servizio (code, argomenti e sottoscrizioni).
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: 1f8bd9484bf2a2106818da1d6e4ef21e937d2ac3
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916883"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Metriche del bus di servizio di Azure in Monitoraggio di Azure

Le metriche del bus di servizio indicano lo stato delle risorse nella sottoscrizione di Azure. Grazie a un set completo di dati delle metriche è possibile valutare l'integrità generale delle risorse del bus di servizio non solo a livello di spazio dei nomi, ma anche a livello di entità. Queste statistiche possono rivelarsi importanti perché consentono di monitorare lo stato del bus di servizio. Le metriche consentono anche di risolvere i problemi senza dover contattare il supporto di Azure.

Monitoraggio di Azure offre interfacce utente unificate per il monitoraggio di diversi servizi di Azure. Per altre informazioni, vedere [Panoramica sul monitoraggio in Microsoft Azure](../azure-monitor/overview.md) e l'esempio che descrive come [recuperare le metriche di Monitoraggio di Azure con .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) in GitHub.

> [!IMPORTANT]
> Nel caso in cui non si verifichi alcuna interazione con un'entità per 2 ore, fino a quando l'entità resterà inattiva le metriche mostreranno il valore "0".

## <a name="access-metrics"></a>Accedere alle metriche

Monitoraggio di Azure offre molti modi per accedere alle metriche. È possibile accedere alle metriche dal [portale di Azure](https://portal.azure.com) o usare le API di Monitoraggio di Azure (REST e .NET) e le soluzioni di analisi, ad esempio i log di Monitoraggio di Azure e Hub eventi. Per altre informazioni, vedere [Metriche in Monitoraggio di Azure](../azure-monitor/platform/data-platform-metrics.md).

Le metriche sono abilitate per impostazione predefinita ed è possibile accedere ai 30 giorni di dati più recenti. Se è necessario conservare i dati per un periodo di tempo più lungo, è possibile archiviare i dati delle metriche in un account di archiviazione di Azure. Questo valore viene configurato nelle [impostazioni di diagnostica ](../azure-monitor/platform/diagnostic-settings.md) in Monitoraggio di Azure.

## <a name="access-metrics-in-the-portal"></a>Accedere alle metriche nel portale

È possibile monitorare le metriche nel tempo nel [portale di Azure](https://portal.azure.com). L'esempio seguente illustra come visualizzare le richieste completate e le richieste in ingresso a livello di account:

![Screenshot della pagina Monitoraggio-metriche (anteprima) nella portale di Azure.][1]

È anche possibile accedere alle metriche direttamente tramite lo spazio dei nomi. A tale scopo, selezionare lo spazio dei nomi e quindi selezionare **metrica**. Per visualizzare le metriche filtrate nell'ambito dell'entità, selezionare l'entità e quindi selezionare **metrica**.

![Screenshot della pagina Monitoraggio-metriche (anteprima) filtrata per l'ambito dell'entità.][2]

Per le metriche che supportano le dimensioni, è necessario filtrare specificando il valore di dimensione da usare.

## <a name="billing"></a>Fatturazione

Le metriche e gli avvisi in Monitoraggio di Azure vengono addebitati in base all'avviso. Questi addebiti dovrebbero essere disponibili nel portale quando l'avviso viene configurato e prima che venga salvato. 

Le soluzioni aggiuntive che inseriscono i dati delle metriche vengono fatturate direttamente da tali soluzioni. Ad esempio, se si archiviano i dati delle metriche in un account di archiviazione di Azure, viene addebitato l'archiviazione di Azure. viene addebitato anche il Log Analytics se si esegue lo streaming dei dati di metrica Log Analytics per l'analisi avanzata.

Le metriche seguenti offrono una panoramica dell'integrità del servizio. 

> [!NOTE]
> Numerose metriche risultano attualmente deprecate perché hanno assunto un nome diverso. Ciò potrebbe rendere necessario l'aggiornamento dei riferimenti. Le metriche contrassegnate con la parola "deprecata" non saranno supportate in futuro.

Tutti i valori delle metriche vengono inviati a Monitoraggio di Azure ogni minuto. La granularità temporale definisce l'intervallo di tempo per il quale vengono presentati i valori delle metriche. L'intervallo di tempo supportato per tutte le metriche del bus di servizio è 1 minuto.

## <a name="request-metrics"></a>Metriche per le richieste

Conta il numero di richieste di operazioni di dati e gestione.

| Nome misurazione | Descrizione |
| ------------------- | ----------------- |
| Richieste in ingresso| Numero di richieste inviate al servizio del bus di servizio in un periodo specificato. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensione: nome entità|
|Richieste riuscite|Numero di richieste completate inviate al servizio del bus di servizio in un periodo specificato.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensione: nome entità|
|Errori server|Numero di richieste non elaborate a causa di un errore nel servizio del bus di servizio in un periodo di tempo specificato.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensione: nome entità|
|Errori utente (vedere la sottosezione seguente)|Numero di richieste non elaborate a causa di errori dell'utente in un periodo specificato.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensione: nome entità|
|Richieste limitate|Numero di richieste che sono state limitate perché è stata superata la soglia di utilizzo.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensione: nome entità|

### <a name="user-errors"></a>Errori utente

I due tipi di errori seguenti sono classificati come errori utente:

1. Errori sul lato client (in HTTP sono errori 400).
2. Gli errori che si verificano durante l'elaborazione di messaggi, ad esempio [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Metriche per i messaggi

| Nome misurazione | Descrizione |
| ------------------- | ----------------- |
|Messaggi in ingresso|Numero di eventi o messaggi inviati al bus di servizio in un periodo specificato.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensione: nome entità|
|Messaggi in uscita|Numero di eventi o messaggi inviati dal bus di servizio in un periodo specificato.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensione: nome entità|
| Messaggi| Numero di messaggi contenuti in una coda o in un argomento. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Dimensione: nome entità |
| Messaggi attivi| Numero di messaggi attivi in una coda o in un argomento. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Dimensione: nome entità |
| Messaggi non recapitabili| Numero di messaggi non recapitabili in una coda o in un argomento. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/>Dimensione: nome entità |
| Messaggi pianificati| Numero di messaggi pianificati in una coda o in un argomento. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media  <br/> Dimensione: nome entità |
| Messaggi completati| Numero di messaggi completati in una coda o in un argomento. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Dimensione: nome entità |
| Messaggi abbandonati| Numero di messaggi abbandonati in una coda o in un argomento. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Dimensione: nome entità |
| Dimensione | Dimensioni in byte di un'entità (coda o argomento). <br/><br/>Unità: Conteggio <br/>Tipo di aggregazione: Media <br/>Dimensione: nome entità | 

> [!NOTE]
> I valori per i messaggi, i messaggi attivi, i messaggi non recapitabili, quelli pianificati, completati e abbandonati sono valori temporizzati. I messaggi in arrivo usati immediatamente dopo tale temporizzazione potrebbero non essere riportati in queste metriche. 

## <a name="connection-metrics"></a>Metriche di connessione

| Nome misurazione | Descrizione |
| ------------------- | ----------------- |
|Connessioni attive|Numero di connessioni attive in uno spazio dei nomi e in un'entità nello spazio dei nomi. Il valore di questa metrica è un valore temporizzato. Le connessioni attive immediatamente dopo tale temporizzazione potrebbero non essere riflesse nella metrica.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensione: nome entità|
|Connessioni aperte |Numero di connessioni aperte.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensione: nome entità|
|Connessioni chiuse |Numero di connessioni chiuse.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensione: nome entità|

## <a name="resource-usage-metrics"></a>Metriche di utilizzo delle risorse

> [!NOTE] 
> Le metriche seguenti sono disponibili solo con il livello **Premium**. 
> 
> Le metriche importanti da monitorare per eventuali interruzioni per uno spazio dei nomi di livello Premium sono: **Utilizzo CPU per spazio dei nomi** e **Utilizzo dimensioni memoria per spazio dei nomi**. [Configurare gli avvisi](../azure-monitor/platform/alerts-metric.md) per queste metriche usando Monitoraggio di Azure.
> 
> L'altra metrica che è possibile monitorare è **Richieste limitate**. Non dovrebbe costituire un problema, purché lo spazio dei nomi rimanga entro i limiti della memoria, della CPU e delle connessioni negoziate. Per altre informazioni, vedere [Limitazione delle richieste nel livello Premium del bus di servizio di Azure](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)

| Nome misurazione | Descrizione |
| ------------------- | ----------------- |
|Uso della CPU per spazio dei nomi|Utilizzo percentuale della CPU dello spazio dei nomi.<br/><br/> Unità: Percentuale <br/> Tipo di aggregazione: Massimo <br/> Dimensione: nome entità|
|Uso delle dimensioni della memoria per spazio dei nomi|Utilizzo percentuale della memoria dello spazio dei nomi.<br/><br/> Unità: Percentuale <br/> Tipo di aggregazione: Massimo <br/> Dimensione: nome entità|

## <a name="metrics-dimensions"></a>Dimensioni delle metriche

Il bus di servizio di Azure supporta le dimensioni seguenti per le metriche in Monitoraggio di Azure. L'aggiunta di dimensioni alle metriche è facoltativa. Se non si aggiungono dimensioni, le metriche vengono specificate a livello di spazio dei nomi. 

|Nome della dimensione|Descrizione|
| ------------------- | ----------------- |
|Entity Name| Il bus di servizio supporta le entità di messaggistica nello spazio dei nomi.|

## <a name="set-up-alerts-on-metrics"></a>Configurazione di avvisi relativi alle metriche

1. Nella scheda **Metriche** della pagina **Spazio dei nomi del bus di servizio** selezionare **Configura avvisi**. 

    ![Pagina Metriche - menu Configura avvisi](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Selezionare l'opzione **Seleziona la destinazione** ed eseguire le azioni seguenti nella pagina **Seleziona una risorsa**: 
    1. Selezionare **Spazio dei nomi del bus di servizio** per il campo **Filtra per tipo di risorsa**. 
    2. Selezionare la sottoscrizione di Azure per il campo **Filtra per sottoscrizione**.
    3. Selezionare lo **spazio dei nomi del bus di servizio** nell'elenco. 
    4. Selezionare **Operazione completata**. 
    
        ![Selezionare lo spazio dei nomi](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Selezionare **Aggiungi criteri** ed eseguire le azioni seguenti nella pagina **Configura logica dei segnali**:
    1. Selezionare **Metriche** per **Tipo segnale**. 
    2. Selezionare un segnale, Ad esempio: **Errori del servizio**. 

        ![Selezionare gli errori del server](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Selezionare **Greater than** (Maggiore di) per **Condizione**.
    2. Selezionare **Totale** per **Aggregazione temporale**. 
    3. Immettere **5** per **Soglia**. 
    4. Selezionare **Operazione completata**.    

        ![Specificare una condizione](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Nella pagina **Crea regola** espandere **Definire i dettagli dell'avviso** ed eseguire queste azioni:
    1. Immettere un **nome** per l'avviso. 
    2. Immettere una **descrizione** per l'avviso.
    3. Selezionare una **gravità** per l'avviso. 

        ![Screenshot della pagina Crea regola. Definisci dettagli avviso è espanso e i campi relativi a nome, descrizione e gravità della regola di avviso sono evidenziati.](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Nella pagina **Crea regola** espandere **Definire il gruppo di azioni**, selezionare **Nuovo gruppo di azioni** e nella pagina **Aggiungi gruppo di azioni** eseguire le azioni seguenti. 
    1. Immettere un nome per il gruppo di azioni.
    2. Immettere un nome breve per il gruppo di azioni. 
    3. Selezionare la propria sottoscrizione. 
    4. Selezionare un gruppo di risorse. 
    5. Per questa procedura dettagliata immettere **Invia messaggio di posta elettronica** per **NOME AZIONE**.
    6. Selezionare **Email/SMS/Push/Voice** (E-mail/SMS/Push/Voce) per **TIPO DI AZIONE**. 
    7. Selezionare **Modifica dettagli**. 
    8. Nella pagina **Email/SMS/Push/Voice** (E-mail/SMS/Push/Voce) eseguire le azioni seguenti:
        1. Selezionare **E-mail**. 
        2. Digitare l'**indirizzo e-mail**. 
        3. Selezionare **OK**.

            ![Screenshot della pagina Aggiungi gruppo di azioni. Al gruppo verrà aggiunta un'azione denominata "Invia messaggio di posta elettronica" con il tipo di azione email/SMS/push/Voice.](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Nella pagina **Aggiungi gruppo di azioni** selezionare **OK**. 
1. Nella pagina **Crea regola** selezionare **Crea regola di avviso**. 

    ![Pulsante Crea regola di avviso](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere [Panoramica di Monitoraggio di Azure](../azure-monitor/overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png