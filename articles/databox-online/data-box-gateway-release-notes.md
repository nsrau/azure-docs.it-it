---
title: Note sulla versione della versione relativa alla disponibilità generale di Azure Data Box Gateway Documenti Microsoft
description: Descrive i problemi e le soluzioni di apertura critici per Azure Data Box Gateway che esegue la versione di disponibilità generale.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265402"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Note sulla versione di Azure Data Box Edge/Azure Data Box Gateway

## <a name="overview"></a>Panoramica

The following release notes identify the critical open issues and the resolved issues for General Availability (GA) release for Azure Data Box Edge and Azure Data Box Gateway.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire il gateway Data Box Edge/Data Box, esaminare attentamente le informazioni contenute nelle note sulla versione.

La versione GA corrisponde alle versioni del software:

- **Gateway data box 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Novità

- **Nuove immagini del disco virtuale:** le nuove immagini VHDX e VMDK sono ora disponibili nel portale di Azure.New virtual disk images - New VHDX and VMDK are now available in the Azure portal. Scaricare queste immagini per eseguire il provisioning, configurare e distribuire nuovi dispositivi GA di Data Box Gateway.Download these images to provision, configure, and deploy new Data Box Gateway GA devices. I dispositivi Data Box Gateway creati nelle versioni di anteprima precedenti non possono essere aggiornati a questa versione. Per ulteriori informazioni, andare all'articolo sulle [operazioni preliminari alla distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **Supporto NFS:** il supporto NFS è attualmente in anteprima ed è disponibile per i client v3.0 e v4.1 che accedono ai dispositivi Data Box Edge e Data Box Gateway.
- **Resilienza dello storage:** il dispositivo Data Box Edge è in grado di resistere all'errore di un disco dati con la funzionalità di resilienza dello storage. Questa funzionalità è attualmente in anteprima. È possibile abilitare la resilienza di archiviazione selezionando l'opzione **Resiliente** nelle **impostazioni di archiviazione** nell'interfaccia utente Web locale.


## <a name="known-issues-in-ga-release"></a>Problemi noti nella versione GA

Nella tabella seguente viene fornito un riepilogo dei problemi noti per la versione del gateway casella dati in esecuzione.

| No. | Funzionalità | Problema | Soluzione alternativa/commenti |
| --- | --- | --- | --- |
| **1.** |Tipi di file | I seguenti tipi di file non sono supportati: file di caratteri, file di blocco, socket, pipe, collegamenti simbolici.  |La copia di questi file comporta la creazione di file di lunghezza 0 nella condivisione NFS. Questi file rimangono in uno stato di errore e vengono anche segnalati in *error.xml*. <br> I collegamenti simbolici alle directory hanno come risultato che le directory non vengono mai contrassegnate come offline. Di conseguenza, è possibile che non venga visualizzata la croce grigia indicante che le directory sono offline e che tutto il contenuto associato è stato caricato completamente in Azure. |
| **2.** |Eliminazione | A causa di un bug in questa versione, potrebbe essere impossibile eliminare una condivisione NFS. Lo stato della condivisione rimarrà comunque *In fase di eliminazione*.  |Questo problema si verifica solo quando la condivisione usa un nome file non supportato. |
| **3.** |Copiare | La copia dei dati non riesce con errore: impossibile completare l'operazione richiesta a causa di una limitazione del file system.  |Il flusso di dati alternativo (ADS) associato a dimensioni del file superiori a 128 KB non è supportato.   |


## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- [Preparare la distribuzione di Azure Data Box Edge.](data-box-edge-deploy-prep.md)
