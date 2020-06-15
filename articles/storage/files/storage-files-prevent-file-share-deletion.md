---
title: Evitare l'eliminazione accidentale - Condivisioni file di Azure
description: Informazioni sulla funzionalità di eliminazione temporanea per le condivisioni file di Azure e su come è possibile usarla per il ripristino dei dati e la prevenzione dell'eliminazione accidentale.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 96e3d5001d11455337ae092776a1a4c5c3738012
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83882934"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Evitare l'eliminazione accidentale di condivisioni file di Azure

Archiviazione di Azure mette ora a disposizione la funzionalità di eliminazione temporanea per le condivisioni file. L'eliminazione temporanea consente di recuperare i dati quando vengono eliminati per errore da un'applicazione o da un altro utente dell'account di archiviazione.

## <a name="how-soft-delete-works"></a>Funzionamento dell'eliminazione temporanea

Se abilitata, la funzionalità di eliminazione temporanea consente di salvare e recuperare le condivisioni file quando vengono eliminate. Quando vengono eliminati, i dati passano in uno stato di eliminazione temporanea anziché essere cancellati in modo definitivo. È possibile configurare il tempo entro il quale i dati eliminati temporaneamente possono essere recuperati prima dell'eliminazione definitiva.

L'eliminazione temporanea può essere abilitata per condivisioni file nuove o esistenti ed è anche compatibile con le versioni precedenti. Non è quindi necessario apportare modifiche alle applicazioni per usufruire della protezione offerta da questa funzionalità. 

Per le condivisioni file Premium eliminate temporaneamente, la quota di condivisione file (la dimensione di cui è stato effettuato il provisioning per la condivisione file) è compresa nel calcolo della quota totale dell'account di archiviazione fino alla scadenza della condivisione eliminata temporaneamente, quando la condivisione viene eliminata completamente.

### <a name="availability"></a>Disponibilità

L'eliminazione temporanea per le condivisioni file di Azure è disponibile in tutti i livelli di archiviazione, in tutti i tipi di account di archiviazione e in ogni area in cui è disponibile File di Azure.

## <a name="configuration-settings"></a>Impostazioni di configurazione

L'eliminazione temporanea per le condivisioni file è abilitata a livello di account di archiviazione. Le impostazioni di eliminazione temporanea si applicano a tutte le condivisioni file all'interno di un account di archiviazione. Quando si crea un nuovo account di archiviazione, l'eliminazione temporanea è disabilitata per impostazione predefinita. L'eliminazione temporanea è disabilitata per impostazione predefinita anche per gli account di archiviazione esistenti. È possibile attivare e disattivare l'eliminazione temporanea in qualsiasi momento.

Se si abilita l'eliminazione temporanea per le condivisioni file, si eliminano alcune condivisioni file e quindi si disabilita l'eliminazione temporanea, è comunque possibile accedere a tali condivisioni file e recuperarle, a condizione che siano state salvate quando l'eliminazione temporanea era abilitata. Quando si abilita l'eliminazione temporanea, è anche necessario configurare il periodo di conservazione.

Il periodo di conservazione indica per quanto tempo le condivisioni file eliminate temporaneamente restano memorizzate e disponibili per il ripristino. Per le condivisioni file eliminate in modo esplicito, il calcolo del periodo di conservazione inizia nel momento in cui i dati vengono eliminati. È attualmente possibile conservare le condivisioni eliminate temporaneamente per un periodo compreso tra 1 e 365 giorni.

È possibile modificare il periodo di conservazione dell'eliminazione temporanea in qualsiasi momento. Un periodo di conservazione aggiornato si applica solo alle condivisioni eliminate dopo l'aggiornamento del periodo di conservazione. Le condivisioni eliminate prima dell'aggiornamento del periodo di conservazione scadono in base al periodo di conservazione configurato al momento dell'eliminazione dei dati.

Per eliminare definitivamente una condivisione file in stato di eliminazione temporanea prima della scadenza, è necessario annullare l'eliminazione della condivisione, disabilitare l'eliminazione temporanea e quindi eliminare nuovamente la condivisione. Sarà quindi necessario riabilitare l'eliminazione temporanea, dal momento che mentre l'eliminazione temporanea è disattivata tutte le altre condivisioni file nell'account di archiviazione sono vulnerabili all'eliminazione accidentale.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Quando vengono eliminate temporaneamente, sia le condivisioni file Standard che le condivisioni file Premium vengono fatturate in base alla capacità usata, anziché in base alla capacità di cui è stato effettuato il provisioning. Le condivisioni file Premium nello stato di eliminazione temporanea, poi, vengono fatturate in base alla frequenza di acquisizione snapshot. Le condivisioni file Standard nello stato di eliminazione temporanea vengono fatturate alla tariffa normale. Non si incorre in alcun addebito per i dati che vengono eliminati definitivamente al termine del periodo di conservazione configurato.

Per altre informazioni sui prezzi di Archiviazione file di Azure in generale, vedere la [pagina Prezzi per File di Azure](https://azure.microsoft.com/pricing/details/storage/files/).

Quando si abilita l'eliminazione temporanea per la prima volta, è consigliabile usare un periodo di conservazione ridotto per comprendere meglio in che modo la funzionalità influisca sui costi.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come abilitare e usare l'eliminazione temporanea, passare ad [Abilitare l'eliminazione temporanea](storage-files-enable-soft-delete.md)