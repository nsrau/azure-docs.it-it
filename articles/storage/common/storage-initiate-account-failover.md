---
title: Avviare un failover dell'account di archiviazione
titleSuffix: Azure Storage
description: Informazioni su come avviare un failover dell'account nel caso in cui l'endpoint primario dell'account di archiviazione non sia più disponibile. Il failover aggiorna l'area secondaria, che in questo modo diventa l'area primaria dell'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/11/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e39548a923e76fc118dec4158398d02577ec20c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91610059"
---
# <a name="initiate-a-storage-account-failover"></a>Avviare un failover dell'account di archiviazione

Se l'endpoint primario per l'account di archiviazione con ridondanza geografica diventa non disponibile per qualsiasi motivo, è possibile avviare un failover dell'account. Un failover dell'account aggiorna l'endpoint secondario, che in questo modo diventa l'endpoint primario dell'account di archiviazione. Una volta completato il failover, i clienti possono iniziare a scrivere nella nuova area primaria. Il failover forzato consente di mantenere la disponibilità elevata per le applicazioni.

Questo articolo illustra come avviare un failover per l'account di archiviazione usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni sul failover degli account, vedere [ripristino di emergenza e failover dell'account di archiviazione](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Un failover dell'account in genere comporta una perdita di dati. Per conoscere le implicazioni di un failover dell'account e prepararsi alla perdita di dati, vedere [Informazioni sul processo di failover dell'account](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di poter eseguire un failover dell'account nell'account di archiviazione, verificare che l'account di archiviazione sia configurato per la replica geografica. L'account di archiviazione può usare una delle opzioni di ridondanza seguenti:

- Archiviazione con ridondanza geografica (GRS) o archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)
- Archiviazione con ridondanza della zona geografica (GZRS) o archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS)

Per altre informazioni sulla ridondanza di Archiviazione di Azure, vedere [Ridondanza dell'archiviazione](storage-redundancy.md).

## <a name="initiate-the-failover"></a>Avviare il failover

## <a name="portal"></a>[Portale](#tab/azure-portal)

Per avviare un failover dell'account dal portale di Azure, seguire questa procedura:

1. Passare all'account di archiviazione.
1. In **Impostazioni** selezionare **Replica geografica**. L'immagine seguente mostra la replica geografica e lo stato del failover di un account di archiviazione.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-prepare.png" alt-text="Screenshot che mostra la replica geografica e lo stato del failover":::

1. Verificare che l'account di archiviazione sia configurato per l'archiviazione con ridondanza geografica o l'archiviazione con ridondanza geografica e accesso in lettura. In caso contrario, selezionare **Configurazione** in **Impostazioni** per aggiornare l'account in modo che sia abilitata la ridondanza geografica.
1. La proprietà **Ora ultima sincronizzazione** indica quanto tempo è passato tra la replica primaria e quella secondaria. **Ora ultima sincronizzazione** offre una stima della portata della perdita di dati che si verificherà al termine del failover. Per altre informazioni sul controllo della proprietà dell' **ora dell'ultima sincronizzazione** , vedere [controllare la proprietà dell'ora dell'ultima sincronizzazione per un account di archiviazione](last-sync-time-get.md).
1. Selezionare **prepara per il failover**.
1. Rivedere la finestra di conferma. Quando si è pronti, immettere **Sì** per confermare e avviare il failover.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-confirm.png" alt-text="Screenshot che mostra la replica geografica e lo stato del failover":::

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

La funzionalità di failover degli account è disponibile a livello generale, ma si basa comunque su un modulo di anteprima per PowerShell. Per usare PowerShell per avviare un failover di account, è necessario prima di tutto installare il modulo AZ. storage [1.1.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview) . Per installare il modulo, seguire questa procedura:

1. Disinstallare eventuali installazioni precedenti di Azure PowerShell:

    - Rimuovere da Windows eventuali installazioni precedenti di Azure PowerShell usando l'impostazione **App e funzionalità** in **Impostazioni**.
    - Rimuovere tutti i moduli di **Azure** da `%Program Files%\WindowsPowerShell\Modules` .

1. Assicurarsi di avere la versione più recente di PowerShellGet installata. Aprire una finestra di Windows PowerShell ed eseguire i seguenti comandi per installare la versione più recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Chiudere e riaprire la finestra di PowerShell dopo l'installazione di PowerShellGet.

1. Installare la versione più recente di Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installare un modulo di anteprima di archiviazione di Azure che supporta il failover degli account:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
    ```

Per avviare un failover dell'account da PowerShell, usare il comando seguente:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>
```

## <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare l'interfaccia della riga di comando di Azure per avviare un failover dell'account, usare i comandi seguenti:

```azurecli-interactive
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="important-implications-of-account-failover"></a>Implicazioni importanti del failover dell'account

Quando si avvia un failover per l'account di archiviazione, vengono aggiornati i record DNS per l'endpoint secondario in modo che l'endpoint secondario diventi l'endpoint primario. Assicurarsi di aver compreso l'impatto potenziale sull'account di archiviazione prima di avviare un failover.

Per stimare l'entità della probabile perdita di dati prima di avviare un failover, controllare la proprietà **ora ultima sincronizzazione** . Per altre informazioni sul controllo della proprietà dell' **ora dell'ultima sincronizzazione** , vedere [controllare la proprietà dell'ora dell'ultima sincronizzazione per un account di archiviazione](last-sync-time-get.md).

Dopo il failover, il tipo di account di archiviazione viene convertito automaticamente in archiviazione con ridondanza locale nella nuova area primaria. È possibile abilitare nuovamente l'archiviazione con ridondanza geografica o l'archiviazione con ridondanza geografica e accesso in lettura per l'account. Tenere presente che la conversione da archiviazione con ridondanza locale ad archiviazione con ridondanza geografica o archiviazione con ridondanza geografica e accesso in lettura comporta un costo aggiuntivo. Per altre informazioni, vedere [Dettagli sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).

Dopo avere nuovamente abilitato l'archiviazione con ridondanza geografica per l'account di archiviazione, Microsoft inizia la replica dei dati dell'account nella nuova area secondaria. La durata della replica dipende dalla quantità di dati da replicare.  

## <a name="next-steps"></a>Passaggi successivi

- [Ripristino di emergenza e failover dell'account di archiviazione](storage-disaster-recovery-guidance.md)
- [Controllare la proprietà Last Sync Time per un account di archiviazione](last-sync-time-get.md)
- [Usare la ridondanza geografica per progettare applicazioni a disponibilità elevata](geo-redundant-design.md)
- [Esercitazione: Compilare un'applicazione a disponibilità elevata con l'archivio BLOB](../blobs/storage-create-geo-redundant-storage.md)
