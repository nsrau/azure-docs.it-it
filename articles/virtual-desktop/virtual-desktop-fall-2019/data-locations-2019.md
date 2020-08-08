---
title: Percorsi dati per desktop virtuale di Windows (versione classica)-Azure
description: Breve panoramica dei percorsi in cui vengono archiviati i dati e i metadati del desktop virtuale Windows (classico).
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 869defde657c9cb4c8bea6bbacebb9458e5a2b96
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008815"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Percorsi dati per desktop virtuale di Windows (versione classica)

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows (versione classica), che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se occorre gestire gli oggetti Azure Resource Manager di Desktop virtuale Windows, vedere [questo articolo](../data-locations.md).

Desktop virtuale di Windows è attualmente disponibile per tutte le posizioni geografiche. Inizialmente, i metadati del servizio possono essere archiviati solo nel Stati Uniti geografia (US). Gli amministratori possono scegliere il percorso in cui archiviare i dati utente quando creano le macchine virtuali del pool host e i servizi associati, ad esempio i file server. Scopri di più sulle aree geografiche di Azure nella [mappa del Data Center di Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft non controlla né limita le aree in cui l'utente o gli utenti possono accedere ai dati specifici dell'utente e dell'app.

>[!IMPORTANT]
>Desktop virtuale di Windows archivia le informazioni sui metadati globali, ad esempio i nomi dei tenant, i nomi dei pool di host, i nomi dei gruppi di app e i nomi delle entità utente in un centro dati situato nel Stati Uniti. I metadati archiviati vengono crittografati a riposo e i mirror con ridondanza geografica vengono mantenuti all'interno del Stati Uniti. Tutti i dati dei clienti, ad esempio le impostazioni dell'app e i dati utente, si trovano nella posizione scelta dal cliente e non sono gestiti dal servizio.

I metadati del servizio vengono replicati nel Stati Uniti a scopo di ripristino di emergenza.