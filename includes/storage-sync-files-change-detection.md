---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: beb08c29587e4ce522131142fd61925b5af45fa9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180217"
---
Le modifiche apportate alla condivisione file di Azure tramite il portale di Azure o SMB non vengono rilevate e replicate immediatamente come le modifiche all'endpoint server. File di Azure non ha ancora funzioni di notifica o di inserimento nel journal per le modifiche. Non è quindi possibile avviare automaticamente una sessione di sincronizzazione quando i file vengono modificati. In Windows Server Sincronizzazione file di Azure usa [l'inserimento nel journal del numero di sequenza di aggiornamento (USN) di Windows](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) per avviare automaticamente una sessione di sincronizzazione quando i file vengono modificati.<br /><br /> Per rilevare le modifiche apportate alla condivisione file di Azure, Sincronizzazione file di Azure ha un processo pianificato denominato *processo di rilevamento modifiche*. Il processo di rilevamento modifiche enumera tutti i file nella condivisione file e quindi confronta ogni file con la versione di sincronizzazione corrispondente. Se il processo di rilevamento modifiche determina che i file sono stati modificati, Sincronizzazione file di Azure avvia una sessione di sincronizzazione. Il processo di rilevamento modifiche viene avviato ogni 24 ore. Poiché il processo di rilevamento modifiche funziona tramite l'enumerazione di ogni file nella condivisione file di Azure, il rilevamento delle modifiche richiede più tempo negli spazi dei nomi di grandi dimensioni rispetto agli spazi dei nomi più piccoli. Per gli spazi dei nomi di grandi dimensioni il tempo necessario per determinare quali file sono stati modificati può risultare superiore a 24 ore.<br /><br />
Si noti che le modifiche apportate a una condivisione file di Azure tramite REST non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione. <br /><br />
Per i volumi in Windows Server è in fase di studio la possibilità di aggiungere una funzione di rilevamento delle modifiche per una condivisione file di Azure simile a USN. È possibile incrementare la priorità dello sviluppo futuro di questa funzionalità votandola alla pagina [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).
