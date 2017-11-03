---
title: Obiettivi di ridimensionamento e prestazioni di File di Azure | Microsoft Docs
description: "Informazioni sugli obiettivi di scalabilità e prestazioni di File di Azure, incluse la capacità, la velocità di richiesta e la larghezza di banda in entrata e in uscita."
services: storage
documentationcenter: na
author: wmgries
manager: klaasl
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/17/2017
ms.author: wgries
ms.openlocfilehash: 35d430b683224d1035cccdfb58b9db415d47ea3b
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2017
---
# <a name="azure-files-scalability-and-performance-targets"></a>Obiettivi di scalabilità e prestazioni per File di Azure
[File di Azure](storage-files-introduction.md) offre condivisioni file completamente gestite nel cloud, accessibili tramite il protocollo SMB standard di settore. Questo articolo descrive gli obiettivi di scalabilità e prestazioni per File di Azure e Sincronizzazione file di Azure (anteprima).

Gli obiettivi di scalabilità e prestazioni elencati di seguito sono di fascia alta, ma possono dipendere da altre variabili nella distribuzione. Ad esempio, la velocità effettiva per un file potrebbe essere limitata anche dalla larghezza di banda di rete disponibile, non solo dai server che ospitano il servizio File di Azure. È consigliabile eseguire il test del criterio di utilizzo per determinare se la scalabilità e le prestazioni di File di Azure soddisfano i requisiti. Microsoft è impegnata ad aumentare i limiti gradualmente. Fornire commenti e suggerimenti sui limiti che si desidera vengano incrementati nella sezione dedicata di seguito o in [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

## <a name="azure-storage-account-scale-targets"></a>Obiettivi di scalabilità per gli account di archiviazione di Azure
La risorsa padre per una condivisione di File di Azure è un account di archiviazione di Azure. Un account di archiviazione rappresenta un pool di archiviazione in Azure che può essere usato da più servizi di archiviazione, incluso File di Azure, per archiviare i dati. Altri servizi che archiviano i dati negli account di archiviazione sono archiviazione BLOB di Azure, archiviazione code di Azure e archiviazione tabelle di Azure. Le destinazioni seguenti si applicano a tutti i servizi di archiviazione che archiviano i dati in un account di archiviazione:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> L'utilizzo di account di archiviazione da altri servizi di archiviazione incide sulle condivisioni file di Azure nell'account di archiviazione. Ad esempio, se si raggiunge la capacità massima dell'account di archiviazione con archiviazione BLOB di Azure, l'utente non sarà in grado di creare nuovi file nella condivisione file di Azure, anche se la condivisione file di Azure è inferiore alla dimensione massima di condivisione.

## <a name="azure-files-scale-targets"></a>Obiettivi di scalabilità di File di Azure
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Obiettivi di scalabilità di Sincronizzazione file di Azure
Con Sincronizzazione file di Azure si è tentato di progettare nella misura massima senza limiti di utilizzo, ma non sempre è possibile. La tabella seguente indica i limiti dei test e le destinazioni con limiti rigidi:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="see-also"></a>Vedere anche
- [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
- [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- [Obiettivi di scalabilità e prestazioni per altri servizi di archiviazione](../common/storage-scalability-targets.md)