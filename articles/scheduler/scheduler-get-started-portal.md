<properties
 pageTitle="Introduzione all'Utilità di pianificazione di Azure nel portale di Azure | Microsoft Azure"
 description="Introduzione all'Utilità di pianificazione di Azure nel portale di Azure"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/10/2016"
 ms.author="deli"/>

# Introduzione all'Utilità di pianificazione di Azure nel portale di Azure

L'Utilità di pianificazione di Azure permette di creare facilmente processi pianificati. In questa esercitazione si apprenderà come creare un processo. Si apprenderanno anche le funzionalità di monitoraggio e gestione dell'Utilità di pianificazione.

## Creare un processo

1.  Accedere al [portale di Azure](https://portal.azure.com/).

2.  Fare clic su **+Nuovo**, digitare _Utilità di pianificazione_ nella casella di ricerca, selezionare **Utilità di pianificazione** tra i risultati e quindi fare clic su **Crea**.

     ![][marketplace-create]

3.  Creiamo un processo che parte semplicemente http://www.microsoft.com/ con una richiesta GET. Nella schermata **Processo dell'Utilità di pianificazione** immettere le informazioni seguenti:

    1.  **Nome:** `getmicrosoft`

    2.  **Sottoscrizione:** sottoscrizione di Azure

    3.  **Raccolta processi:** selezionare una raccolta di processi esistente oppure fare clic su **Crea nuovo** e immettere un nome.

4.  Successivamente, in **Impostazioni azione** definire i valori seguenti:

    1.  **Tipo di azione:** ` HTTP`

    2.  **Metodo:** `GET`

    3.  **URL:** ` http://www.microsoft.com`

      ![][action-settings]

5.  Infine, definire una pianificazione. È possibile impostare il processo come unico, ma in questo esempio viene selezionata una pianificazione ricorrenza.

    1. **Ricorrenza**: `Recurring`

    2. **Inizia**: data odierna

    3. **Ricorre ogni:** `12 Hours`

    4. **Termina entro**: due giorni dalla data odierna

      ![][recurrence-schedule]

6.  Fare clic su **Crea**

## Gestire e monitorare i processi

Un processo appena creato viene visualizzato nel dashboard principale di Azure. Fare clic sul processo per visualizzare una nuova finestra con le schede seguenti:

1.  Proprietà

2.  Impostazioni azione

3.  Pianificazione

4.  Cronologia

5.  Utenti

    ![][job-overview]

### Proprietà

Queste proprietà di sola lettura descrivono i metadati di gestione per il processo dell'Utilità di pianificazione.

   ![][job-properties]


### Impostazioni di azione

Per configurare un processo, fare clic sul processo corrispondente nella schermata **Processi**. Questo permette di configurare le impostazioni avanzate, se non sono state configurate nella procedura guidata di creazione rapida.

Per tutti i tipi di azione è possibile modificare i criteri per i tentativi e l'azione di errore.

Per le azioni di processo di tipo HTTP e HTTPS, è possibile modificare il metodo a qualsiasi verbo HTTP consentito. È inoltre possibile aggiungere, eliminare o modificare le intestazioni e le informazioni di autenticazione di base.

Per le azioni di tipo coda di archiviazione, è possibile modificare l'account di archiviazione, il nome della coda, token SAS e corpo.

Per i tipi di azione del bus di servizio è possibile modificare lo spazio dei nomi, il percorso della coda o dell'argomento, le impostazioni di autenticazione, il tipo di trasporto, le proprietà del messaggio e il corpo del messaggio.

   ![][job-action-settings]

### Pianificazione

Permette di riconfigurare la pianificazione creata nella procedura guidata di creazione rapida.

Offre la possibilità di creare [pianificazioni complesse e operazioni ricorrenti avanzate nel processo](scheduler-advanced-complexity.md)

È possibile modificare data e ora di inizio, la pianificazione di ricorrenza, e data e ora di fine (se il processo è ricorrente).

   ![][job-schedule]


### Cronologia

La scheda **Cronologia** mostra le metriche selezionate per ogni esecuzione di processo nel sistema per il processo selezionato. Le metriche forniscono valori in tempo reale relativi all'integrità dell'Utilità di pianificazione:

1.  Status

2.  Dettagli

3.  Tentativi

4.  Occorrenza: 1, 2, 3 e così via

5.  Ora di inizio dell'esecuzione

6.  Ora di fine dell'esecuzione

   ![][job-history]

È possibile fare clic su un'esecuzione per visualizzarne i **Dettagli cronologia**, inclusa l'intera risposta per ogni esecuzione. La finestra di dialogo consente anche di copiare la risposta negli appunti.

   ![][job-history-details]

### Utenti

Il controllo degli accessi in base al ruolo di Azure consente una gestione degli accessi specifica per l'Utilità di pianificazione di Azure. Per informazioni sull'uso della scheda Utenti, vedere [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md)


## Vedere anche

 [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)

 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione](scheduler-concepts-terms.md)

 [Piani e fatturazione nell'utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure](scheduler-advanced-complexity.md)

 [Riferimento API REST dell'utilità di pianificazione](https://msdn.microsoft.com/library/mt629143)

 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione](scheduler-powershell-reference.md)

 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione](scheduler-high-availability-reliability.md)

 [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione](scheduler-limits-defaults-errors.md)

 [Autenticazione in uscita dell'Utilità di pianificazione](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png

<!---HONumber=AcomDC_1005_2016-->