---
title: Gestire Azure Data Lake Analytics tramite il portale di Azure
description: Questo articolo descrive come usare il portale di Azure per gestire utenti, processi, origini dati e account di Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8a6b7cb3fd45e17b84519efcaa826b569083e156
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839469"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Gestire Azure Data Lake Analytics tramite il portale di Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Questo articolo descrive come gestire utenti, processi, origini dati e account Azure Data Lake Analytics tramite il portale di Azure.


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
4. Fai clic su **Crea**. 

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
2. Fare clic su **Controllo di accesso (IAM)**  > **Aggiungi un'assegnazione di ruolo**.
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

3. Fare clic su **Submit Job**.

### <a name="monitor-jobs"></a>Monitorare i processi

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Visualizza tutti i processi**. Viene visualizzato un elenco di tutti i processi attivi e completati di recente nell'account.
3. Facoltativamente, fare clic su **Filtra** per trovare i processi in base ai valori di **Intervallo di tempo**, **Nome processo** e **Autore**. 

### <a name="monitoring-pipeline-jobs"></a>Monitoraggio dei processi della pipeline
I processi che fanno parte di una pipeline funzionano insieme, in genere in sequenza, per realizzare uno scenario specifico. È ad esempio possibile avere una pipeline che esegue la pulizia, estrae, trasforma, e aggrega l'utilizzo per Customer Insights. I processi della pipeline vengono identificati mediante la proprietà "Pipeline" al momento dell'invio del processo. Per i processi pianificati usando ADF V2 questa proprietà viene popolata automaticamente. 

Per visualizzare un elenco di processi U-SQL che fanno parte delle pipeline: 

1. Nel portale di Azure accedere agli account di Data Lake Analytics.
2. Fare clic su **Informazioni dettagliate sul processo**. Verrà aperta per impostazione predefinita la scheda "Tutti i processi", che mostra un elenco di processi in esecuzione, in coda e terminati.
3. Fare clic sulla scheda **processi pipeline** . Verrà visualizzato un elenco di processi della pipeline insieme a statistiche aggregate per ogni pipeline.

### <a name="monitoring-recurring-jobs"></a>Monitoraggio dei processi ricorrenti
Un processo ricorrente è un processo che ha la stessa logica di business, ma usa dati di input diversi ogni volta che viene eseguito. Idealmente, i processi ricorrenti devono avere sempre esito positivo e un tempo di esecuzione relativamente stabile. Il monitoraggio di questi comportamenti aiuta ad assicurarsi che il processo sia integro. I processi ricorrenti vengono identificati mediante la proprietà "Recurrence". Per i processi pianificati usando ADF V2 questa proprietà viene popolata automaticamente.

Per visualizzare un elenco di processi U-SQL ricorrenti: 

1. Nel portale di Azure accedere agli account di Data Lake Analytics.
2. Fare clic su **Informazioni dettagliate sul processo**. Verrà aperta per impostazione predefinita la scheda "Tutti i processi", che mostra un elenco di processi in esecuzione, in coda e terminati.
3. Fare clic sulla scheda **processi ricorrenti** . Verrà visualizzato un elenco di processi ricorrenti insieme a statistiche aggregate per ogni processo ricorrente.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Gestire Azure Data Lake Analytics tramite Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Manage Azure Data Lake Analytics using policies](data-lake-analytics-account-policies.md) (Gestire Azure Data Lake Analytics con criteri)
