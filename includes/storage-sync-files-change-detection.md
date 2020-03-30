---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 55456a6be938411d3c08a0eaa8fdbfb0844e7129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391683"
---
Le modifiche apportate alla condivisione file di Azure tramite il portale di Azure o SMB non vengono rilevate e replicate immediatamente come le modifiche all'endpoint server. File di Azure non ha ancora funzioni di notifica o di inserimento nel journal per le modifiche. Non è quindi possibile avviare automaticamente una sessione di sincronizzazione quando i file vengono modificati. In Windows Server Sincronizzazione file di Azure usa [l'inserimento nel journal del numero di sequenza di aggiornamento (USN) di Windows](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) per avviare automaticamente una sessione di sincronizzazione quando i file vengono modificati.

Per rilevare le modifiche apportate alla condivisione file di Azure, Sincronizzazione file di Azure ha un processo pianificato denominato *processo di rilevamento modifiche*. Il processo di rilevamento modifiche enumera tutti i file nella condivisione file e quindi confronta ogni file con la versione di sincronizzazione corrispondente. Se il processo di rilevamento modifiche determina che i file sono stati modificati, Sincronizzazione file di Azure avvia una sessione di sincronizzazione. Il processo di rilevamento modifiche viene avviato ogni 24 ore. Poiché il processo di rilevamento modifiche funziona tramite l'enumerazione di ogni file nella condivisione file di Azure, il rilevamento delle modifiche richiede più tempo negli spazi dei nomi di grandi dimensioni rispetto agli spazi dei nomi più piccoli. Per gli spazi dei nomi di grandi dimensioni il tempo necessario per determinare quali file sono stati modificati può risultare superiore a 24 ore.

Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet PowerShell Invoke-AzStorageSyncChangeDetection per avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure.To immediately sync files that are changed in the Azure file share, the **Invoke-AzStorageSyncChangeDetection** PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. Questo cmdlet è destinato a scenari in cui un certo tipo di processo automatizzato sta apportando modifiche nella condivisione file di Azure o le modifiche vengono apportate da un amministratore (ad esempio lo spostamento di file e directory nella condivisione). Per le modifiche apportate all'utente finale, è consigliabile installare l'agente di Sincronizzazione file di Azure in una macchina virtuale IaaS e fare in modo che gli utenti finali accedano alla condivisione file tramite la macchina virtuale IaaS.For end user changes, the recommendation is to install the Azure File Sync agent in an IaaS VM and have end users access the file share through the IaaS VM. In questo modo tutte le modifiche verranno sincronizzate rapidamente con altri agenti senza la necessità di utilizzare il cmdlet Invoke-AzStorageSyncChangeDetection. Per altre informazioni, vedere la documentazione di [Invoke-AzStorageSyncChangeDetection.To](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) learn more, see the Invoke-AzStorageSyncChangeDetection documentation.

>[!NOTE]
>Le modifiche apportate a una condivisione file di Azure tramite REST non aggiornano l'ora dell'ultima modifica di SMB e non verranno considerate come una modifica dalla sincronizzazione.

Per i volumi in Windows Server è in fase di studio la possibilità di aggiungere una funzione di rilevamento delle modifiche per una condivisione file di Azure simile a USN. È possibile incrementare la priorità dello sviluppo futuro di questa funzionalità votandola alla pagina [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).
