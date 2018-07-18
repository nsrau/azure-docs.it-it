---
title: Gestire i criteri di Azure Data Lake Analytics
description: Informazioni sull'uso dei criteri per controllare l'utilizzo di un account Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 2c074b1a75c5bfef07ffb90e719bd3247a474e63
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623945"
---
# <a name="manage-azure-data-lake-analytics-using-policies"></a>Gestire Azure Data Lake Analytics con criteri

Usando i criteri account, è possibile controllare le modalità di utilizzo delle risorse di un account Data Lake Analytics. Questi criteri consentono di controllare il costo dell'utilizzo di Azure Data Lake Analytics. Con questi criteri, ad esempio, è possibile impedire picchi di costo imprevisti limitando il numero di AU utilizzabili contemporaneamente dall'account.

## <a name="account-level-policies"></a>Criteri a livello di account

Questi criteri si applicano a tutti i processi in un account di Data Lake Analytics.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Numero massimo di unità di analisi in un account di Data Lake Analytics
Un criterio controlla il numero totale di unità di analisi (AU, Analytics Unit) che possono essere usate dall'account di Data Lake Analytics. Per impostazione predefinita, il valore è impostato su 250. Se ad esempio sono impostate 250 AU, è possibile eseguire un processo a cui sono assegnate 250 AU oppure 10 processi con 25 AU ciascuno. Gli altri processi inviati vengono inseriti in coda finché i processi in esecuzione non vengono completati. Al termine di questi processi, le AU vengono rese disponibili per consentire l'esecuzione dei processi in coda.

Per modificare il numero di AU per l'account di Data Lake Analytics:

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Proprietà**.
3. In **Numero massimo di AU** spostare il dispositivo di scorrimento per selezionare un valore o immettere il valore nella casella di testo. 
4. Fare clic su **Save**.

> [!NOTE]
> Se è necessario impostare un numero di AU superiore al valore predefinito (250), nel portale fare clic su **Guida e supporto** per inviare una richiesta di supporto. Il numero di AU disponibili nell'account di Data Lake Analytics può essere incrementato.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Numero massimo di processi che possono essere eseguiti contemporaneamente
Un criterio controlla il numero di processi che possono essere eseguiti contemporaneamente. Per impostazione predefinita, questo valore è impostato su 20. Se in Data Lake Analytics sono presenti AU disponibili, viene pianificata l'esecuzione di nuovi processi finché il numero totale dei processi in esecuzione non raggiunge il valore di questo criterio. Quando si raggiunge il numero massimo di processi che possono essere eseguiti contemporaneamente, i processi successivi vengono inseriti in coda, in ordine di priorità, finché uno o più processi non vengono completati (a seconda della disponibilità di AU).

Per modificare il numero di processi che possono essere eseguiti contemporaneamente:

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Proprietà**.
3. In **Numero massimo di processi in esecuzione** spostare il dispositivo di scorrimento per selezionare un valore o immettere il valore nella casella di testo. 
4. Fare clic su **Save**.

> [!NOTE]
> Se è necessario eseguire un numero di processi superiore al valore predefinito (20), nel portale fare clic su **Guida e supporto** per inviare una richiesta di supporto. Il numero di processi che possono essere eseguiti contemporaneamente nell'account di Data Lake Analytics può essere incrementato.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Per quanto tempo mantenere le risorse e i metadati dei processi 
Quando gli utenti eseguono processi U-SQL, il servizio Data Lake Analytics mantiene tutti i file correlati, ovvero lo script U-SQL, i file DLL a cui viene fatto riferimento nello script U-SQL, le risorse compilate e le statistiche. I file si trovano nella cartella /system/ dell'account di Azure Data Lake Store. Questo criterio controlla per quanto tempo le risorse rimangono archiviate prima di essere eliminate automaticamente. Il valore predefinito è 30 giorni. È possibile usare questi file per il debug e per ottimizzare le prestazioni dei processi che si eseguiranno nuovamente in futuro.

Per modificare il tempo di archiviazione delle risorse e dei metadati dei processi:

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Proprietà**.
3. In **Giorni di conservazione delle query del processo** spostare il dispositivo di scorrimento per selezionare un valore o immettere il valore nella casella di testo.  
4. Fare clic su **Save**.

## <a name="job-level-policies"></a>Criteri a livello di processo

Con i criteri a livello di processo è possibile controllare il numero massimo di AU e la massima priorità che i singoli utenti (o i membri di specifici gruppi di sicurezza) possono impostare per i processi che inviano. Questi criteri consentono di controllare i costi sostenuti dagli utenti. e l'effetto che i processi pianificati possono avere sui processi di produzione ad alta priorità in esecuzione nello stesso account di Data Lake Analytics.

In Data Lake Analytics è possibile impostare due criteri a livello di processo:

* **AU limit per job** (Limite AU per processo): gli utenti possono inviare solo processi che hanno al massimo il numero di AU specificato. Per impostazione predefinita, questo limite corrisponde a quello definito per l'account.
* **Priorità**: gli utenti possono inviare solo processi che hanno una priorità inferiore o uguale a questo valore. Un numero più alto indica una priorità più bassa. Per impostazione predefinita, questo limite è impostato su 1, che corrisponde alla massima priorità possibile.

Per ogni account è impostato un criterio predefinito che si applica a tutti gli utenti dell'account. È possibile impostare altri criteri per utenti e gruppi specifici. 

> [!NOTE]
> I criteri a livello di account e quelli a livello di processo vengono applicati contemporaneamente.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Aggiungere un criterio per un utente o un gruppo specifico

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Proprietà**.
3. In **Limiti di invio del processo** fare clic sul pulsante **Aggiungi criterio**. Selezionare o immettere le impostazioni seguenti:
    1. **Nome criteri di calcolo**: immettere un nome di criterio in modo da identificarne la finalità.
    2. **Selezionare l'utente o il gruppo**: selezionare l'utente o il gruppo a cui si applica questo criterio.
    3. **Impostare il limite AU del processo**: impostare il limite di AU che si applica all'utente o al gruppo selezionato.
    4. **Impostare il limite di priorità del processo**: impostare il limite di priorità che si applica all'utente o al gruppo selezionato.

4. Fare clic su **OK**.

5. Il nuovo criterio viene elencato nella tabella dei criteri **Impostazioni predefinite** in **Limiti di invio del processo**. 

### <a name="delete-or-edit-an-existing-policy"></a>Eliminare o modificare un criterio esistente

1. Nel portale di Azure accedere all'account di Data Lake Analytics.
2. Fare clic su **Proprietà**.
3. In **Limiti di invio del processo** trovare il criterio da modificare.
4.  Per visualizzare le opzioni **Elimina** e **Modifica**, nella colonna all'estremità destra della tabella fare clic su `...`.

## <a name="additional-resources-for-job-policies"></a>Risorse aggiuntive per i criteri dei processi
* [Post di blog con una panoramica dei criteri](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Post di blog sui criteri a livello di account](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Post di blog sui criteri a livello di processo](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introduzione a Data Lake Analytics con il portale di Azure](data-lake-analytics-get-started-portal.md)
* [Gestire Azure Data Lake Analytics tramite Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

