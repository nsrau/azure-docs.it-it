---
title: Backup e ripristino del database di Azure Analysis Services | Microsoft Docs
description: Viene descritto come eseguire backup e ripristino di un database di Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: fb660384f2f9f569bcfbe7fa7d5c1f7ce772cacd
ms.lasthandoff: 04/20/2017


---

# <a name="backup-and-restore"></a>Backup e ripristino

Il backup dei database modello tabulare in Azure Analysis Services è molto simile a quello di Analysis Services in locale. La differenza principale è dove vengono archiviati i file di backup. I file di backup devono essere salvati in un contenitore in un [account di archiviazione di Azure](../storage/storage-create-storage-account.md). È possibile usare un account di archiviazione e un contenitore già esistenti, o è possibile crearne di nuovi durante la configurazione delle impostazioni di archiviazione per il server.

> [!NOTE]
> La creazione di un account di archiviazione può dare luogo a un nuovo servizio fatturabile. Per altre informazioni, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

I backup vengono salvati con estensione abf. Per i modelli tabulari in memoria, vengono archiviati sia i dati del modello che i metadati. Per i modelli tabulari DirectQuery, vengono archiviati solo i metadati del modello. I backup possono essere compressi e crittografati, a seconda delle opzioni scelte. 



## <a name="configure-storage-settings"></a>Configurare le impostazioni di archiviazione
Prima di eseguire il backup, è necessario configurare le impostazioni di archiviazione per il server.


### <a name="to-configure-storage-settings"></a>Per configurare le impostazioni di archiviazione
1.  Nel portale di Azure > **Impostazioni**, fare clic su **Backup**.

    ![Backup in Impostazioni](./media/analysis-services-backup/aas-backup-backups.png)

2.  Fare clic su **Abilitata** e quindi su **Impostazioni di archiviazione**.

    ![Abilita](./media/analysis-services-backup/aas-backup-enable.png)

3. Selezionare l'account di archiviazione o crearne uno nuovo.

4. Selezionare un contenitore o crearne uno nuovo.

    ![Selezionare un contenitore](./media/analysis-services-backup/aas-backup-container.png)

5. Salvare le impostazioni di backup. Ogni volta che si modificano le impostazioni di archiviazione o che si abilita o disabilita il backup, è necessario salvare le modifiche.

    ![Salvare le impostazioni di backup](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Per eseguire il backup tramite SSMS

1. In SSMS fare clic con il pulsante destro del mouse su un database > **Backup**.

2. In **Backup database** > **File di backup** fare clic su **Sfoglia**.

3. Nella finestra di dialogo **Salva file con nome** verificare il percorso della cartella e quindi digitare un nome per il file di backup. Per impostazione predefinita, il nome del file ha l'estensione abf. 

4. Nella finestra di dialogo **Backup database** selezionare le opzioni.

    **Consenti sostituzione file**: selezionare questa opzione per sovrascrivere i file di backup con lo stesso nome. Se questa opzione non è selezionata, il file da salvare non può avere lo stesso nome di un file già esistente nello stesso percorso.

    **Applica compressione**: selezionare questa opzione per comprimere il file di backup. I file di backup compressi consentono di risparmiare spazio su disco, ma richiedono un utilizzo della CPU leggermente più elevato. 

    **Crittografa file di backup**: selezionare questa opzione per crittografare il file di backup. Questa opzione richiede una password specificata dall'utente per proteggere il file di backup. La password impedisce la lettura dei dati di backup con qualsiasi mezzo che non sia un'operazione di ripristino. Se si sceglie di crittografare i backup, archiviare la password in un luogo sicuro.

5. Fare clic su **OK** per creare e salvare il file di backup.


### <a name="powershell"></a>PowerShell
Usare il cmdlet [Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet).

## <a name="restore"></a>Ripristino
Durante il ripristino, il file di backup deve essere nell'account di archiviazione configurato per il server. Se è necessario spostare un file di backup da un percorso locale all'account di archiviazione, usare [Archiviazione di Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) o l'utilità della riga di comando [AzCopy](../storage/storage-use-azcopy.md). 

Se si sta ripristinando un database di modello tabulare con livello di compatibilità 1200 da un server SQL Server Analysis Services locale, è innanzitutto necessario rimuovere tutti gli utenti di dominio dai ruoli del modello e aggiungerli nuovamente ai ruoli come utenti di Azure Active Directory. I ruoli saranno gli stessi.

### <a name="to-restore-by-using-ssms"></a>Per eseguire il ripristino usando SSMS

1. In SSMS fare clic con il pulsante destro del mouse su un database > **Ripristina**.

2. Nella finestra di dialogo **Backup database** in **File di backup** fare clic su **Sfoglia**.

3. Nella finestra di dialogo **Trova file di database** selezionare il file da ripristinare.

4. In **Ripristina database** selezionare il database.

5. Specificare le opzioni. Le opzioni di sicurezza devono corrispondere alle opzioni di backup usate per eseguire il backup.


### <a name="powershell"></a>PowerShell

Usare il cmdlet [Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet).


## <a name="related-information"></a>Informazioni correlate

[Account di archiviazione di Azure](../storage/storage-create-storage-account.md)  
[Disponibilità elevata](analysis-services-bcdr.md)     
[Gestire Azure Analysis Services](analysis-services-manage.md)

