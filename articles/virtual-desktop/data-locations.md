---
title: Posizioni dei dati per Windows Virtual Desktop - AzureData locations for Windows Virtual Desktop - Azure
description: Breve panoramica delle posizioni in cui sono archiviati i dati e i metadati di Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f48586f38c310c77c61f470d3e2fbc819c33f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128049"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Posizioni dei dati per Windows Virtual Desktop

Windows Virtual Desktop è attualmente disponibile per tutte le località geografiche. Inizialmente, i metadati del servizio possono essere archiviati solo nell'area geografica degli Stati Uniti (US). Gli amministratori possono scegliere il percorso in cui archiviare i dati utente quando creano le macchine virtuali del pool host e i servizi associati, ad esempio i file server. Per altre informazioni sulle aree geografiche di Azure, vedere la mappa del data center di Azure.Learn more about Azure geographies at the [Azure datacenter map](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft non controlla o limita le aree in cui l'utente o gli utenti possono accedere ai dati specifici dell'utente e dell'app.

>[!IMPORTANT]
>Windows Virtual Desktop archivia le informazioni sui metadati globali, ad esempio i nomi dei tenant, i nomi dei pool host, i nomi dei gruppi di app e i nomi delle entità utente in un data center situato negli Stati Uniti. I metadati archiviati vengono crittografati inattivi e i mirror con ridondanza geografica vengono mantenuti all'interno degli Stati Uniti. Tutti i dati dei clienti, ad esempio le impostazioni dell'app e i dati utente, risiedono nella posizione scelta dal cliente e non sono gestiti dal servizio.

I metadati del servizio vengono replicati negli Stati Uniti per scopi di ripristino di emergenza.