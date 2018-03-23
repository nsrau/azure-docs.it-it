---
title: Gestire Azure Data Lake Analytics tramite il portale di Azure | Microsoft Docs
description: Informazioni su come gestire account, origini dati, utenti e processi di Data Lake Analytics.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: kfile
editor: cgronlun
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 93815904e7e21e1ba8283d7a522297c7e3466702
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="manage-azure-data-lake-analytics-by-using-the-azure-portal"></a>Gestire Azure Data Lake Analytics tramite il portale di Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Informazioni su come gestire gli account, le origini dati degli account, gli utenti e i processi di Azure Data Lake Analytics usando il portale di Azure. Per visualizzare gli argomenti di gestione sull'uso di altri strumenti, fare clic su una scheda nella parte superiore della pagina.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Gestire gli account di Data Lake Analytics

### <a name="create-an-account"></a>Creare un account

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Crea una risorsa** > **Intelligence e analisi** > **Data Lake Analytics**.
3. Selezionare i valori per gli elementi seguenti: 
   1. **Nome**: nome dell'account di Data Lake Analytics.
   2. **Sottoscrizione**: sottoscrizione di Azure usata per l'account.
   3. **Gruppo di risorse**: gruppo di risorse di Azure in cui creare l'account. 
   4. **Posizione**: data center di Azure per l'account di Data Lake Analytics. 
   5. **Data Lake Store**: archivio predefinito da usare per l'account di Data Lake Analytics. L'account di Azure Data Lake Store e quello di Data Lake Analytics devono trovarsi nella stessa posizione.
4. Fare clic su **Crea**. 

### <a name="delete-a-data-lake-analytics-account"></a>Eliminare un account di Data Lake Analytics

Prima di eliminare un account di Data Lake Analytics, eliminare il relativo account di Data Lake Store predefinito.

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Elimina**.
3. Digitare il nome dell'account.
4. Fare clic su **Elimina**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Gestire le origini dati

Data Lake Analytics supporta le origini dati seguenti:

* Archivio Data Lake
* Archiviazione di Azure

Per esplorare le origini dati ed eseguire operazioni di gestione dei file di base è possibile usare Esplora dati. 

### <a name="add-a-data-source"></a>Aggiungere un'origine dati

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Origini dati**.
3. Fare clic su **Aggiungi origine dati**.
    
   * Per aggiungere un account di Data Lake Store, sono necessari il nome dell'account e i diritti di accesso all'account per poter eseguire una query.
   * Per aggiungere una risorsa di archiviazione BLOB di Azure, sono necessari l'account di archiviazione e la chiave dell'account. Per trovarli, passare all'account di archiviazione nel portale.

## <a name="set-up-firewall-rules"></a>Configurare le regole del firewall

È possibile usare Data Lake Analytics per bloccare ulteriormente l'accesso all'account di Data Lake Analytics a livello di rete. È possibile abilitare un firewall, specificare un indirizzo IP o definire un intervallo di indirizzi IP per i client attendibili. Dopo aver configurato queste regole, solo i client con indirizzo IP compreso nell'intervallo definito potranno connettersi all'archivio.

Se altri servizi di Azure, come Azure Data Factory o Macchine virtuali, si connettono all'account di Data Lake Analytics, verificare che l'opzione **Consenti ai servizi di Azure di accedere al server** sia **attivata**. 

### <a name="set-up-a-firewall-rule"></a>Configurare una regola del firewall

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Nel menu a sinistra fare clic su **Firewall**.

## <a name="add-a-new-user"></a>Aggiungere un nuovo utente

È possibile usare l'**Aggiunta guidata utente** per effettuare facilmente il provisioning di nuovi utenti di Data Lake.

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. A sinistra, sotto **Attività iniziali**, fare clic su **Aggiunta guidata utente**.
3. Selezionare un utente e quindi fare clic su **Seleziona**.
4. Selezionare un ruolo e quindi fare clic su **Seleziona**. Per configurare un nuovo sviluppatore per Azure Data Lake, selezionare il ruolo **Sviluppatore di Data Lake Analytics**.
5. Selezionare gli elenchi di controllo di accesso (ACL) per i database U-SQL. Dopo aver completato le selezioni, fare clic su **Seleziona**.
6. Selezionare gli elenchi ACL per i file. Per l'archivio predefinito, non modificare gli elenchi ACL per la cartella radice "/" e per la cartella /system. Fare clic su **Seleziona**.
7. Esaminare tutte le modifiche selezionate e quindi fare clic su **Esegui**.
8. Al termine della procedura guidata, fare clic su **Fine**.

## <a name="manage-role-based-access-control"></a>Gestire il controllo degli accessi in base al ruolo

Come per altri servizi di Azure, è possibile usare il controllo degli accessi in base al ruolo (RBAC, Role-Based Access Control) per controllare il modo con cui gli utenti interagiscono con il servizio.

I ruoli RBAC standard offrono le funzionalità seguenti:
* **Proprietario**: può inviare e monitorare i processi, annullarli da qualsiasi utente e configurare l'account.
* **Collaboratore**: può inviare e monitorare i processi, annullarli da qualsiasi utente e configurare l'account.
* **Lettore**: può monitorare i processi.

Usare il ruolo Sviluppatore di Data Lake Analytics per consentire agli sviluppatori di U-SQL di usare il servizio Data Lake Analytics. È possibile usare il ruolo Sviluppatore di Data Lake Analytics per eseguire queste operazioni:
* Inviare processi.
* Monitorare lo stato e l'avanzamento dei processi inviati da qualsiasi utente.
* Vedere gli script U-SQL relativi ai processi inviati da qualsiasi utente.
* Annullare solo i processi creati personalmente.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Aggiungere utenti o gruppi di sicurezza a un account di Data Lake Analytics

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Controllo di accesso (IAM)** > **Aggiungi**.
3. Selezionare un ruolo.
4. Aggiungere un utente.
5. Fare clic su **OK**.

>[!NOTE]
>Se un utente o un gruppo di sicurezza vuole inviare processi, deve disporre anche dell'autorizzazione sull'account di archiviazione. Per altre informazioni, vedere [Protezione dei dati presenti in Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Gestire i processi

### <a name="submit-a-job"></a>Inviare un processo

1. Nel portale di Azure accedere all'account di Data Lake Analytics.

2. Fare clic su **Nuovo processo**. Per ogni processo, configurare quanto segue:

    1. **Nome processo**: nome del processo.
    2. **Priorità**: i numeri più bassi hanno maggiore priorità. Se due processi sono in coda, verrà eseguito per primo quello con la priorità più bassa.
    3. **Parallelismo**: numero massimo di processi di calcolo da riservare per il processo.

3. Fare clic su **Invia processo**.

### <a name="monitor-jobs"></a>Monitorare i processi

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Visualizza tutti i processi**. Viene visualizzato un elenco di tutti i processi attivi e completati di recente nell'account.
3. Facoltativamente, fare clic su **Filtra** per trovare i processi in base ai valori di **Intervallo di tempo**, **Nome processo** e **Autore**. 

### <a name="monitoring-pipeline-jobs"></a>Monitoraggio dei processi della pipeline
I processi che fanno parte di una pipeline funzionano insieme, in genere in sequenza, per realizzare uno scenario specifico. È ad esempio possibile avere una pipeline che esegue la pulizia, estrae, trasforma, e aggrega l'utilizzo per Customer Insights. I processi della pipeline vengono identificati mediante la proprietà "Pipeline" al momento dell'invio del processo. Per i processi pianificati usando ADF V2 questa proprietà viene popolata automaticamente. 

Per visualizzare un elenco di processi U-SQL che fanno parte delle pipeline: 

1. Nel portale di Azure accedere agli account di Data Lake Analytics.
2. Fare clic su **Informazioni dettagliate sul processo**. Verrà aperta per impostazione predefinita la scheda "Tutti i processi", che mostra un elenco di processi in esecuzione, in coda e terminati.
3. Fare clic sulla scheda **Processi della pipeline**. Verrà visualizzato un elenco di processi della pipeline, con statistiche aggregate per ogni pipeline.

### <a name="monitoring-recurring-jobs"></a>Monitoraggio dei processi ricorrenti
Un processo ricorrente è un processo che ha la stessa logica di business, ma usa dati di input diversi ogni volta che viene eseguito. Idealmente, i processi ricorrenti devono avere sempre esito positivo e un tempo di esecuzione relativamente stabile. Il monitoraggio di questi comportamenti aiuta ad assicurarsi che il processo sia integro. I processi ricorrenti vengono identificati mediante la proprietà "Recurrence". Per i processi pianificati usando ADF V2 questa proprietà viene popolata automaticamente.

Per visualizzare un elenco di processi U-SQL ricorrenti: 

1. Nel portale di Azure accedere agli account di Data Lake Analytics.
2. Fare clic su **Informazioni dettagliate sul processo**. Verrà aperta per impostazione predefinita la scheda "Tutti i processi", che mostra un elenco di processi in esecuzione, in coda e terminati.
3. Fare clic sulla scheda **Processi ricorrenti**. Verrà visualizzato un elenco di processi ricorrenti, con statistiche aggregate per ogni processo.

## <a name="manage-policies"></a>Gestire i criteri

### <a name="account-level-policies"></a>Criteri a livello di account

Questi criteri si applicano a tutti i processi in un account di Data Lake Analytics.

#### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Numero massimo di unità di analisi in un account di Data Lake Analytics
Un criterio controlla il numero totale di unità di analisi (AU, Analytics Unit) che possono essere usate dall'account di Data Lake Analytics. Per impostazione predefinita, il valore è impostato su 250. Se ad esempio sono impostate 250 AU, è possibile eseguire un processo a cui sono assegnate 250 AU oppure 10 processi con 25 AU ciascuno. Gli altri processi inviati vengono inseriti in coda finché i processi in esecuzione non vengono completati. Al termine di questi processi, le AU vengono rese disponibili per consentire l'esecuzione dei processi in coda.

Per modificare il numero di AU per l'account di Data Lake Analytics:

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Proprietà**.
3. In **Numero massimo di AU** spostare il dispositivo di scorrimento per selezionare un valore o immettere il valore nella casella di testo. 
4. Fare clic su **Save**.

> [!NOTE]
> Se è necessario impostare un numero di AU superiore al valore predefinito (250), nel portale fare clic su **Guida e supporto** per inviare una richiesta di supporto. Il numero di AU disponibili nell'account di Data Lake Analytics può essere incrementato.
>

#### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Numero massimo di processi che possono essere eseguiti contemporaneamente
Un criterio controlla il numero di processi che possono essere eseguiti contemporaneamente. Per impostazione predefinita, questo valore è impostato su 20. Se in Data Lake Analytics sono presenti AU disponibili, viene pianificata l'esecuzione di nuovi processi finché il numero totale dei processi in esecuzione non raggiunge il valore di questo criterio. Quando si raggiunge il numero massimo di processi che possono essere eseguiti contemporaneamente, i processi successivi vengono inseriti in coda, in ordine di priorità, finché uno o più processi non vengono completati (a seconda della disponibilità di AU).

Per modificare il numero di processi che possono essere eseguiti contemporaneamente:

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Proprietà**.
3. In **Numero massimo di processi in esecuzione** spostare il dispositivo di scorrimento per selezionare un valore o immettere il valore nella casella di testo. 
4. Fare clic su **Save**.

> [!NOTE]
> Se è necessario eseguire un numero di processi superiore al valore predefinito (20), nel portale fare clic su **Guida e supporto** per inviare una richiesta di supporto. Il numero di processi che possono essere eseguiti contemporaneamente nell'account di Data Lake Analytics può essere incrementato.
>

#### <a name="how-long-to-keep-job-metadata-and-resources"></a>Per quanto tempo mantenere le risorse e i metadati dei processi 
Quando gli utenti eseguono processi U-SQL, il servizio Data Lake Analytics mantiene tutti i file correlati, ovvero lo script U-SQL, i file DLL a cui viene fatto riferimento nello script U-SQL, le risorse compilate e le statistiche. I file si trovano nella cartella /system/ dell'account di Azure Data Lake Store. Questo criterio controlla per quanto tempo le risorse rimangono archiviate prima di essere eliminate automaticamente. Il valore predefinito è 30 giorni. È possibile usare questi file per il debug e per ottimizzare le prestazioni dei processi che si eseguiranno nuovamente in futuro.

Per modificare il tempo di archiviazione delle risorse e dei metadati dei processi:

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Proprietà**.
3. In **Giorni di conservazione delle query del processo** spostare il dispositivo di scorrimento per selezionare un valore o immettere il valore nella casella di testo.  
4. Fare clic su **Save**.

### <a name="job-level-policies"></a>Criteri a livello di processo
Con i criteri a livello di processo è possibile controllare il numero massimo di AU e la massima priorità che i singoli utenti (o i membri di specifici gruppi di sicurezza) possono impostare per i processi che inviano. Ciò consente di controllare i costi sostenuti dagli utenti e l'effetto che i processi pianificati possono avere sui processi di produzione ad alta priorità in esecuzione nello stesso account di Data Lake Analytics.

In Data Lake Analytics è possibile impostare due criteri a livello di processo:

* **AU limit per job** (Limite AU per processo): gli utenti possono inviare solo processi che hanno al massimo il numero di AU specificato. Per impostazione predefinita, questo limite corrisponde a quello definito per l'account.
* **Priorità**: gli utenti possono inviare solo processi che hanno una priorità inferiore o uguale a questo valore. Si noti che un numero più alto indica una priorità più bassa. Per impostazione predefinita, questo valore è impostato su 1, che corrisponde alla massima priorità possibile.

Per ogni account è impostato un criterio predefinito che si applica a tutti gli utenti dell'account. È possibile impostare altri criteri per utenti e gruppi specifici. 

> [!NOTE]
> I criteri a livello di account e quelli a livello di processo vengono applicati contemporaneamente.
>

#### <a name="add-a-policy-for-a-specific-user-or-group"></a>Aggiungere un criterio per un utente o un gruppo specifico

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Proprietà**.
3. In **Limiti di invio del processo** fare clic sul pulsante **Aggiungi criterio**. Selezionare o immettere le impostazioni seguenti:
    1. **Nome criteri di calcolo**: immettere un nome di criterio in modo da identificarne la finalità.
    2. **Selezionare l'utente o il gruppo**: selezionare l'utente o il gruppo a cui si applica questo criterio.
    3. **Impostare il limite AU del processo**: impostare il limite di AU che si applica all'utente o al gruppo selezionato.
    4. **Impostare il limite di priorità del processo**: impostare il limite di priorità che si applica all'utente o al gruppo selezionato.

4. Fare clic su **OK**.

5. Il nuovo criterio viene elencato nella tabella dei criteri **Impostazioni predefinite** in **Limiti di invio del processo**. 

#### <a name="delete-or-edit-an-existing-policy"></a>Eliminare o modificare un criterio esistente

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Proprietà**.
3. In **Limiti di invio del processo** trovare il criterio da modificare.
4.  Per visualizzare le opzioni **Elimina** e **Modifica**, nella colonna all'estremità destra della tabella fare clic su **...**.

### <a name="additional-resources-for-job-policies"></a>Risorse aggiuntive per i criteri dei processi
* [Post di blog con una panoramica dei criteri](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Post di blog sui criteri a livello di account](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Post di blog sui criteri a livello di processo](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introduzione a Data Lake Analytics con il portale di Azure](data-lake-analytics-get-started-portal.md)
* [Gestire Azure Data Lake Analytics tramite Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

