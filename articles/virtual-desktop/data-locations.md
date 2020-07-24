---
title: Percorsi dati per desktop virtuale Windows Spring 2020-Azure
description: Breve panoramica delle posizioni in cui vengono archiviati i metadati e i dati del desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 96072b8008f42f58ec6a3aff32728c087b8e3290
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080574"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Percorsi di dati e metadati per desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Primavera 2020 con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa la versione Autunno 2019 di Desktop virtuale Windows senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/data-locations-2019.md).
>
> L'aggiornamento di Primavera 2020 di Desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Desktop virtuale di Windows è attualmente disponibile per tutte le posizioni geografiche. Gli amministratori possono scegliere il percorso in cui archiviare i dati utente quando creano le macchine virtuali del pool host e i servizi associati, ad esempio i file server. Scopri di più sulle aree geografiche di Azure nella [mappa del Data Center di Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft non controlla né limita le aree in cui l'utente o gli utenti possono accedere ai dati specifici dell'utente e dell'app.

>[!IMPORTANT]
>Desktop virtuale di Windows archivia informazioni sui metadati globali, ad esempio nomi di tenant, nomi dei pool di host, nomi dei gruppi di app e nomi dell'entità utente in un Data Center. Ogni volta che un cliente crea un oggetto servizio, deve immettere un percorso per l'oggetto servizio. Il percorso immesso determina la posizione in cui verranno archiviati i metadati per l'oggetto. Il cliente sceglie un'area di Azure e i metadati verranno archiviati nell'area geografica correlata. Per un elenco di tutte le aree di Azure e le aree geografiche correlate, vedere [geografie di Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

Al momento, è supportata solo l'archiviazione dei metadati nella Stati Uniti geografia (US) di Azure. I metadati archiviati sono crittografati e i mirror con ridondanza geografica vengono mantenuti all'interno dell'area geografica. Tutti i dati dei clienti, ad esempio le impostazioni dell'app e i dati utente, si trovano nella posizione scelta dal cliente e non sono gestiti dal servizio. Più aree geografiche diventeranno disponibili man mano che il servizio cresce.

I metadati del servizio vengono replicati all'interno dell'area geografica di Azure a scopo di ripristino di emergenza.