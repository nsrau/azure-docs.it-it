---
title: Note sulla versione di disponibilità generale della finestra di Gateway di dati di Azure | Microsoft Docs
description: Descrive i problemi critici aperti e soluzioni per il Gateway di finestra di dati di Azure che eseguono la versione di disponibilità generale.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 3f3e07d745d83041afc9dafd64678a3ac4a65012
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418026"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Note sulla versione di disponibilità di dati Edge o Azure Data finestra Gateway generale della finestra di Azure

## <a name="overview"></a>Panoramica

Le note sulla versione seguenti identificano i problemi critici aperti e i problemi risolti per GA (General Availability) di rilascio per bordo casella dei dati di Azure e il Gateway di finestra di dati di Azure.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire il Gateway di finestra di dati finestra Edge/Data, esaminare attentamente le informazioni contenute nelle note sulla versione.

La versione GA corrisponde per le versioni del software:

- **Gateway di dati finestra 1903 (1.5.810.441)**
- **Bordo casella dati 1903 (1.5.810.441)**


## <a name="whats-new"></a>Novità

- **Nuove immagini di dischi virtuali** -nuovo VHDX e VMDK sono ora disponibili nel portale di Azure. Scaricare queste immagini per eseguire il provisioning, configurare e distribuire i nuovi dispositivi Gateway finestra dati a livello generale. I dispositivi Data Box Gateway creati nelle versioni di anteprima precedenti non possono essere aggiornati a questa versione. Per ulteriori informazioni, andare all'articolo sulle [operazioni preliminari alla distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **Supporto NFS** -supporto NFS è attualmente in anteprima e disponibile per v3.0 e v4.1 client che accedono a dispositivi Edge di finestra di dati e Gateway di finestra di dati.
- **Resilienza di archiviazione** : dispositivo periferico di casella Your Data può resistere il guasto di un disco dati con la funzionalità di resilienza di archiviazione. Questa funzionalità è attualmente in anteprima. È possibile abilitare la resilienza di archiviazione, selezionando il **resilienti** opzione il **impostazioni di archiviazione** in locale un'interfaccia utente web.


## <a name="known-issues-in-ga-release"></a>Problemi noti nella versione disponibile a livello generale

Nella tabella seguente fornisce un riepilogo dei problemi noti per il Gateway di finestra di dati esegue una versione.

|  No. | Funzionalità | Problema | Soluzione alternativa/commenti |
| --- | --- | --- | --- |
| **1.** |Tipi di file | Non sono supportati i seguenti tipi di file: file, i file di blocco, socket, le pipe, i collegamenti simbolici di caratteri.  |La copia di questi file comporta la creazione di file di lunghezza 0 nella condivisione NFS. Questi file rimangono in uno stato di errore e vengono anche segnalati in *error.xml*. <br> I collegamenti simbolici alle directory hanno come risultato che le directory non vengono mai contrassegnate come offline. Di conseguenza, è possibile che non venga visualizzata la croce grigia indicante che le directory sono offline e che tutto il contenuto associato è stato caricato completamente in Azure. |
| **2.** |Eliminazione | A causa di un bug in questa versione, potrebbe essere impossibile eliminare una condivisione NFS. Lo stato della condivisione rimarrà comunque *In fase di eliminazione*.  |Questo problema si verifica solo quando la condivisione usa un nome file non supportato. |
| **3.** |Copia | L'operazione di copia dei dati ha esito negativo con errore:  Impossibile completare l'operazione a causa di un limite del file system.  |Non è supportato l'alternativo Stream dei dati (ADS) associati a dimensioni del file maggiore di 128 KB.   |


## <a name="next-steps"></a>Passaggi successivi

- [Operazioni preliminari alla distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- [Preparare la distribuzione Edge finestra di dati Azure](data-box-edge-deploy-prep.md).
