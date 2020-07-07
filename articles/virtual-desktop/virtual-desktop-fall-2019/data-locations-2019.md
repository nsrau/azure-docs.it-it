---
title: Percorsi dati per desktop virtuale Windows-Azure
description: Breve panoramica delle posizioni in cui vengono archiviati i metadati e i dati del desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b8cbee74a8f9639fc2c3838f38dabf2a62cf339e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82614447"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Percorsi dati per desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica alla versione Autunno 2019 che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si sta tentando di gestire gli oggetti Azure Resource Manager di Desktop virtuale Windows introdotti nell'aggiornamento di Primavera 2020, vedere [questo articolo](../data-locations.md).

Desktop virtuale di Windows è attualmente disponibile per tutte le posizioni geografiche. Inizialmente, i metadati del servizio possono essere archiviati solo nel Stati Uniti geografia (US). Gli amministratori possono scegliere il percorso in cui archiviare i dati utente quando creano le macchine virtuali del pool host e i servizi associati, ad esempio i file server. Scopri di più sulle aree geografiche di Azure nella [mappa del Data Center di Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft non controlla né limita le aree in cui l'utente o gli utenti possono accedere ai dati specifici dell'utente e dell'app.

>[!IMPORTANT]
>Desktop virtuale di Windows archivia le informazioni sui metadati globali, ad esempio i nomi dei tenant, i nomi dei pool di host, i nomi dei gruppi di app e i nomi delle entità utente in un centro dati situato nel Stati Uniti. I metadati archiviati vengono crittografati a riposo e i mirror con ridondanza geografica vengono mantenuti all'interno del Stati Uniti. Tutti i dati dei clienti, ad esempio le impostazioni dell'app e i dati utente, si trovano nella posizione scelta dal cliente e non sono gestiti dal servizio.

I metadati del servizio vengono replicati nel Stati Uniti a scopo di ripristino di emergenza.