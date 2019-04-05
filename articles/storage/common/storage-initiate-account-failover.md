---
title: Avviare un failover dell'account di archiviazione (anteprima) - Archiviazione di Azure
description: Informazioni su come avviare un failover dell'account nel caso in cui l'endpoint primario dell'account di archiviazione non sia più disponibile. Il failover aggiorna l'area secondaria, che in questo modo diventa l'area primaria dell'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: fd8eecbd20446bfde8d3a7467e2982398c3b8c19
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044964"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Avviare un failover dell'account di archiviazione (anteprima)

Se l'endpoint primario dell'account di archiviazione con ridondanza geografica non è più disponibile per qualsiasi motivo, è possibile avviare un failover dell'account (anteprima). Un failover dell'account aggiorna l'endpoint secondario, che in questo modo diventa l'endpoint primario dell'account di archiviazione. Una volta completato il failover, i clienti possono iniziare a scrivere nella nuova area primaria. Il failover forzato consente di mantenere la disponibilità elevata per le applicazioni.

Questo articolo illustra come avviare un failover per l'account di archiviazione usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni sul failover dell'account, vedere [Ripristino di emergenza e failover dell'account (anteprima) in Archiviazione di Azure](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Un failover dell'account in genere comporta una perdita di dati. Per conoscere le implicazioni di un failover dell'account e prepararsi alla perdita di dati, vedere [Informazioni sul processo di failover dell'account](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di poter eseguire un failover sull'account di archiviazione, assicurarsi di avere eseguito i passaggi seguenti:

- Registrarsi all'anteprima del failover dell'account. Per informazioni sulla registrazione, vedere [Informazioni sull'anteprima](storage-disaster-recovery-guidance.md#about-the-preview).
- Verificare che l'account di archiviazione sia configurato per usare l'archiviazione con ridondanza geografica o l'archiviazione con ridondanza geografica e accesso in lettura. Per altre informazioni sull'archiviazione con ridondanza geografica, vedere [Archiviazione con ridondanza geografica: replica tra più aree per Archiviazione di Azure](storage-redundancy-grs.md). 

## <a name="important-implications-of-account-failover"></a>Implicazioni importanti del failover dell'account

Quando si avvia un failover per l'account di archiviazione, vengono aggiornati i record DNS per l'endpoint secondario in modo che l'endpoint secondario diventi l'endpoint primario. Assicurarsi di aver compreso l'impatto potenziale sull'account di archiviazione prima di avviare un failover.

Per valutare la portata della possibile perdita di dati prima di avviare un failover, controllare la proprietà **Ora ultima sincronizzazione** usando il cmdlet di PowerShell `Get-AzStorageAccount` e includere il parametro `-IncludeGeoReplicationStats`. Controllare quindi la proprietà `GeoReplicationStats` dell'account. 

Dopo il failover, il tipo di account di archiviazione viene convertito automaticamente in archiviazione con ridondanza locale nella nuova area primaria. È possibile abilitare nuovamente l'archiviazione con ridondanza geografica o l'archiviazione con ridondanza geografica e accesso in lettura per l'account. Tenere presente che la conversione da archiviazione con ridondanza locale ad archiviazione con ridondanza geografica o archiviazione con ridondanza geografica e accesso in lettura comporta un costo aggiuntivo. Per altre informazioni, vedere [Dettagli sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

Dopo avere nuovamente abilitato l'archiviazione con ridondanza geografica per l'account di archiviazione, Microsoft inizia la replica dei dati dell'account nella nuova area secondaria. La durata della replica dipende dalla quantità di dati da replicare.  

## <a name="azure-portal"></a>Portale di Azure

Per avviare un failover dell'account dal portale di Azure, seguire questa procedura:

1. Passare all'account di archiviazione.
2. In **Impostazioni** selezionare **Replica geografica**. L'immagine seguente mostra la replica geografica e lo stato del failover di un account di archiviazione.

    ![Screenshot che mostra la replica geografica e lo stato del failover](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Verificare che l'account di archiviazione sia configurato per l'archiviazione con ridondanza geografica o l'archiviazione con ridondanza geografica e accesso in lettura. In caso contrario, selezionare **Configurazione** in **Impostazioni** per aggiornare l'account in modo che sia abilitata la ridondanza geografica. 
4. La proprietà **Ora ultima sincronizzazione** indica quanto tempo è passato tra la replica primaria e quella secondaria. **Ora ultima sincronizzazione** offre una stima della portata della perdita di dati che si verificherà al termine del failover.
5. Selezionare **Prepare for failover (preview)** (Prepara il failover - anteprima). 
6. Rivedere la finestra di conferma. Quando si è pronti, immettere **Sì** per confermare e avviare il failover.

    ![Screenshot che mostra la finestra di dialogo di conferma per un failover dell'account](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>PowerShell

Per usare PowerShell per avviare un failover dell'account, è necessario installare prima di tutto il modulo di anteprima 6.0.1. Per installare il modulo, seguire questa procedura:

1. Disinstallare eventuali installazioni precedenti di Azure PowerShell:

    - Rimuovere da Windows eventuali installazioni precedenti di Azure PowerShell usando l'impostazione **App e funzionalità** in **Impostazioni**.
    - Rimuovere tutti i moduli di **Azure** da `%Program Files%\WindowsPowerShell\Modules`.
    
1. Assicurarsi di avere la versione più recente di PowerShellGet installata. Aprire una finestra di Windows PowerShell ed eseguire i seguenti comandi per installare la versione più recente:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```
1. Chiudere e riaprire la finestra di PowerShell dopo l'installazione di PowerShellGet. 

1. Installare la versione più recente di Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installare un modulo in anteprima di Archiviazione di Azure che supporta Azure AD:
   
    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```
1. Chiudere e riaprire la finestra di PowerShell.
 

Per avviare un failover dell'account da PowerShell, usare il comando seguente:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare l'interfaccia della riga di comando di Azure per avviare un failover dell'account, usare i comandi seguenti:

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

## <a name="next-steps"></a>Passaggi successivi

- [Disaster recovery e l'account di failover (anteprima) in archiviazione di Azure](storage-disaster-recovery-guidance.md)
- [Progettazione di applicazioni a disponibilità elevata con RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Esercitazione: Compilare un'applicazione a disponibilità elevata con l'archivio BLOB](../blobs/storage-create-geo-redundant-storage.md) 
