---
title: Note sulla versione di Azure Data Box Gateway disponibilità generale | Microsoft Docs
description: Vengono descritti i problemi critici aperti e le risoluzioni per la Azure Data Box Gateway esecuzione della versione di disponibilità generale.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: 1a8a9840cc6e1f3627c5fbd30e0b7432db0f16e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82561044"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Note sulla versione di disponibilità generale Azure Data Box Edge/Azure Data Box Gateway

## <a name="overview"></a>Panoramica

Le seguenti note sulla versione identificano i problemi critici aperti e i problemi risolti per la versione di disponibilità generale (GA) per Azure Data Box Edge e Azure Data Box Gateway. 

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire il Data Box Edge/Data Box Gateway, esaminare attentamente le informazioni contenute nelle note sulla versione.

La versione GA corrisponde alle versioni del software:

- **Data Box Gateway 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Novità

- **Nuove immagini del disco virtuale** : i nuovi VHDX e VMDK sono ora disponibili nella portale di Azure. Scaricare queste immagini per eseguire il provisioning, configurare e distribuire nuovi dispositivi Data Box Gateway GA. I dispositivi Data Box Gateway creati nelle versioni di anteprima precedenti non possono essere aggiornati a questa versione. Per ulteriori informazioni, andare all'articolo sulle [operazioni preliminari alla distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **Supporto per NFS** : il supporto per NFS è attualmente disponibile in anteprima e disponibile per i client v 3.0 e v 4.1 che accedono ai dispositivi Data Box Edge e data box gateway.
- **Resilienza dell'archiviazione** : il dispositivo data box Edge può sopportare l'errore di un disco dati con la funzionalità di resilienza di archiviazione. Questa funzionalità è attualmente in anteprima. È possibile abilitare la resilienza dell'archiviazione selezionando l'opzione **resiliente** nelle **impostazioni di archiviazione** nell'interfaccia utente Web locale.


## <a name="known-issues-in-ga-release"></a>Problemi noti nella versione GA

Nella tabella seguente viene fornito un riepilogo dei problemi noti per il Data Box Gateway versione in esecuzione.

| No. | Funzionalità | Problema | Soluzione alternativa/commenti |
| --- | --- | --- | --- |
| **1.** |Tipi di file | I tipi di file seguenti non sono supportati: file di caratteri, file di blocco, socket, pipe, collegamenti simbolici.  |La copia di questi file comporta la creazione di file di lunghezza 0 nella condivisione NFS. Questi file rimangono in uno stato di errore e vengono anche segnalati in *error.xml*. <br> I collegamenti simbolici alle directory hanno come risultato che le directory non vengono mai contrassegnate come offline. Di conseguenza, è possibile che non venga visualizzata la croce grigia indicante che le directory sono offline e che tutto il contenuto associato è stato caricato completamente in Azure. |
| **2.** |Eliminazione | A causa di un bug in questa versione, potrebbe essere impossibile eliminare una condivisione NFS. Lo stato della condivisione rimarrà comunque *In fase di eliminazione*.  |Questo problema si verifica solo quando la condivisione usa un nome file non supportato. |
| **3.** |Copia | La copia dei dati ha esito negativo con errore: Impossibile completare l'operazione richiesta a causa di una limitazione file system.  |Il flusso di dati alternativo (ADS) associato a dimensioni del file maggiori di 128 KB non è supportato.   |


## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Data Box gateway](data-box-gateway-deploy-prep.md).
- [Preparare la distribuzione di Azure Data Box Edge](azure-stack-edge-deploy-prep.md).
