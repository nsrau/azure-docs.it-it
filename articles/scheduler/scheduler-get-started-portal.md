<properties
 pageTitle="Introduzione all'uso dell'Utilità di pianificazione di Azure nel portale di Azure | Microsoft Azure"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/18/2015"
 ms.author="krisragh"/>

# Introduzione all'uso dell'Utilità di pianificazione di Azure nel portale di Azure

## Configurare l'Utilità di pianificazione tramite il portale di Azure per creare processi in modo facile e veloce

Per completare questa esercitazione, è necessario disporre di un account Azure in cui sia abilitata l’Utilità di pianificazione di Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, fare riferimento alla pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## Introduzione

Creare processi e raccolte di processi nell'Utilità di pianificazione di Azure tramite il portale di Azure è un'operazione semplice. Questa esercitazione illustra la procedura dettagliata per la creazione di una raccolta di processi da usare per archiviare i progetti, per la creazione di un processo in una raccolta di processi e una panoramica delle attività di monitoraggio e gestione del processo disponibili tramite il portale. Per svolgere l'esercitazione, non è necessario avere esperienze precedenti nell'utilizzo di Azure.

Quando si apre il portale di Azure per la prima volta viene visualizzata automaticamente la scheda **TUTTI GLI ELEMENTI**, al cui interno sono presenti delle colonne che è possibile ordinare. Per visualizzare i processi e le raccolte di processi dell'utilità di pianificazione, cliccare sulla scheda **UTILITÀ DI PIANIFICAZIONE**.

![][1]

## Creare una raccolta di processi e un processo

1.  Accedere al [portale di Azure](https://manage.windowsazure.com/).  

2.  Fare clic su **Servizi app**> **Crea nuovo** > **Utilità di pianificazione** e quindi fare clic su **Creazione personalizzata**. <br /><br /> ![][2]

3.  In **Raccolta processi**, selezionare il nome della raccolta di processi esistente nell'elenco a discesa **Raccolta processi**. Se non si dispone di una raccolta processi esistente a cui aggiungere il processo, selezionare **Crea nuovo** e immettere un nome per identificare la nuova raccolta processi.<br /><br /> ![][3]

4.  In **Area** selezionare l'area geografica per la raccolta di processi.

5.  Fare clic sul tasto di direzione per creare la raccolta di processi e passare alla fase successiva – creare un processo.

6.  Creiamo un processo che parte semplicemente http://www.microsoft.com/ con una richiesta GET. Nella schermata **Azione processo** definire i valori seguenti per i campi modulo necessari:

    1.  **Nome:** ` getmicrosoft`  

    2.  **Tipo di azione:** ` HTTP`

    3.  **Metodo:** ` GET`

    4.  **URI:** ` http://www.microsoft.com`

   	![][4]

7.  Dopo aver creato un processo, definire una pianificazione. Il processo può essere impostato come processo one-time, ma in questo caso selezioniamo una pianificazione di ricorrenza. Alcune schermate in questa esercitazione mostrano una ricorrenza di 1 minuto a scopo puramente illustrativo, scegliere invece una ricorrenza di 12 ore.

    1.  **Ricorre ogni:** ` 12 Hours`  

    2.  **Avvio:** ` Now`

    3.  **Data di fine:** ` Select date 2 days after current day and any time`

   	![][5]

8.  Fare clic su **OK**. La creazione del processo e della raccolta di processi può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare le notifiche nella parte inferiore del portale.

   	![][6]

   	Dopo aver completato la procedura, un messaggio indicherà che il processo o la raccolta di processi è stata creata. Il processo viene visualizzato nella sezione Processi dell'Utilità di pianificazione e la raccolta di processi viene visualizzata nella sezione Raccolte processi. Per configurare ulteriori impostazioni avanzate del processo, vedere la sezione "Configurare un processo" riportata di seguito.

   	![][7]

## Gestire e monitorare le raccolte di processi e i processi

Dopo la creazione, la raccolta di processi viene visualizzata nella schermata principale di gestione dell'Utilità di pianificazione.

![][8]

Fare clic su una raccolta di processi per visualizzare una nuova finestra con le opzioni seguenti:

1.  Dashboard  

2.  Scalabilità

3.  History

4.  Processi

Gli argomenti seguenti descrivono in dettaglio queste schede.

### Dashboard

Quando si fa clic sul nome della propria raccolta di processi, viene visualizzata la scheda **Dashboard**. Nella scheda Dashboard sono visualizzate le informazioni seguenti:

![][9]

#### Panoramica sull'utilizzo del processo e Panoramica sull'utilizzo di esecuzione

Una tabella e una serie di grafici che mostrano un elenco fisso di metriche. Queste metriche forniscono valori in tempo reale riguardo lo stato corrente della propria raccolta di processi, inclusi:

1.  Processi correnti  

2.  Processi completati

3.  Processi con errori

4.  Processi abilitati

5.  Processi disabilitati

6.  Esecuzioni del processo

#### Riepilogo rapido

Una tabella che mostra un elenco fisso di metriche su stati e impostazioni. Queste metriche forniscono valori in tempo reale riguardo lo stato e le impostazioni associati alla propria raccolta di processi, inclusi:

1.  Stato  

2.  Region

3.  Numero di errori

4.  Numero di occorrenze con errori

5.  URI

### Scalabilità

Nella scheda **Ridimensiona**, è possibile modificare le impostazioni e il livello di servizio usato dall'Utilità di pianificazione.

![][10]

#### Generale

Indica se è in uso il piano **Free** o **Standard**.

#### Quote

L’Utilità di pianificazione di Azure implementa le quote in base a diversi parametri. In questa sezione vengono elencate le soglie di quota ed è possibile modificarle. Per impostazione predefinita è configurato un solo set di quote. I limiti di queste impostazioni di quote sono vincolati dal proprio piano, e modificare il proprio Piano può influire sui costi.. Le quote possono essere modificate per scalare l'Utilità di pianificazione. Le opzioni includono:

1.  Processi massimi  

2.  Frequenza massima

3.  Intervallo massimo

### History

La scheda **Cronologia** visualizza le informazioni seguenti per il processo selezionato:

![][11]

#### Tabella cronologia

Una tabella che visualizza le metriche selezionate per ogni esecuzione del processo nel sistema per il processo selezionato. Queste metriche forniscono valori in tempo reale riguardo lo stato corrente dell’Utilità di pianificazione.

#### Metriche disponibili

Sono disponibili i contatori/le metriche seguenti:

1.  Stato  

2.  Dettagli

3.  Tentativo di ripetizione

4.  Numero di esecuzioni (prima, seconda, terza, ecc.)

5.  Timestamp dell’esecuzione

Per esaminare l'intera risposta per ogni esecuzione, è possibile fare clic su **Visualizza dettagli cronologia**. La finestra di dialogo consente anche di copiare la risposta negli appunti.

![][12]

### Processi

La scheda processi visualizza le informazioni seguenti per monitorare la cronologia di esecuzione dei processi:

![][13]

#### Tabella processi

Una tabella che visualizza le metriche selezionate per ogni processo nel sistema. Queste metriche forniscono valori in tempo reale riguardo lo stato corrente dell’Utilità di pianificazione.

#### Disabilitazione, abilitazione o eliminazione di un processo

Per abilitare, disabilitare o eliminare un processo, fare clic sul nome di un processo. I processi eliminati potrebbero non essere ripristinabili.

#### Metriche disponibili

Sono disponibili i seguenti contatori e metriche:

1.  Nome  

2.  Ultima esecuzione

3.  Prossima esecuzione

4.  Stato

5.  Frequenza

6.  Errori

7.  Errori

8.  Esecuzioni

9.  Tipo di azione

### Configurare un processo

Per configurare un processo, fare clic su un processo nella schermata **Processi**. Ciò consente di configurare le impostazioni avanzate aggiuntive non disponibili nella procedura guidata di creazione rapida. Per configurare un processo, fare clic sulla freccia destra accanto al nome del processo nella schermata **Processi**.

La pagina di configurazione del processo consente di aggiornare le impostazioni del processo. La pagina di configurazione del processo è illustrata di seguito per i processi di tipo HTTP e HTTPS. Per le azioni di processo di tipo HTTP e HTTPS, è possibile modificare il metodo a qualsiasi verbo HTTP consentito. È inoltre possibile aggiungere, eliminare o modificare le intestazioni e le informazioni di autenticazione di base.

![][14]

La pagina di configurazione del processo appare come mostrata di seguito per i processi su coda di archiviazione. Per le azioni di tipo coda di archiviazione, è possibile modificare l'account di archiviazione, il nome della coda, token SAS e corpo. La sezione "Pianificazione" (non illustrata di seguito) è identica alla sezione "Pianificazione" per le azione dei processi di tipo HTTP/HTTPS.

![][15]

Infine, per tutti i tipi di azione, è possibile modificare la pianificazione stessa e il relativo comportamento di ricorrenza. È possibile modificare data e ora di inizio, la pianificazione di ricorrenza, e data e ora di fine (se il processo è ricorrente). Dopo aver apportato le modifiche, è possibile salvarle facendo clic su **Salva** o annullarle facendo clic su **Annulla**.

## Vedere anche

 [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)

 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione](scheduler-concepts-terms.md)

 [Piani e fatturazione nell'utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure](scheduler-advanced-complexity.md)

 [Riferimento API REST dell'utilità di pianificazione](https://msdn.microsoft.com/library/dn528946)

 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione](scheduler-powershell-reference.md)

 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione](scheduler-high-availability-reliability.md)

 [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione](scheduler-limits-defaults-errors.md)

 [Autenticazione in uscita dell'Utilità di pianificazione](scheduler-outbound-authentication.md)



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

<!---HONumber=AcomDC_1203_2015-->