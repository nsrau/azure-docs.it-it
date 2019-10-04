---
title: Percorsi dati per desktop virtuale Windows-Azure
description: Breve panoramica delle posizioni in cui vengono archiviati i metadati e i dati del desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
ms.openlocfilehash: 5a634f3449d88e2c1885f4a32ae2662c93811c63
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349936"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Percorsi dati per desktop virtuale Windows

Desktop virtuale di Windows è attualmente disponibile per tutte le posizioni geografiche. Inizialmente, i metadati del servizio possono essere archiviati solo nel Stati Uniti geografia (US). Gli amministratori possono scegliere il percorso in cui archiviare i dati utente quando creano le macchine virtuali del pool host e i servizi associati, ad esempio i file server. Scopri di più sulle aree geografiche di Azure nella [mappa del Data Center di Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft non controlla né limita le aree in cui l'utente o gli utenti possono accedere ai dati specifici dell'utente e dell'app.

>[!IMPORTANT]
>Desktop virtuale di Windows archivia le informazioni sui metadati globali, ad esempio i nomi dei tenant, i nomi dei pool di host, i nomi dei gruppi di app e i nomi delle entità utente in un centro dati situato nel Stati Uniti. I metadati archiviati vengono crittografati a riposo e i mirror con ridondanza geografica vengono mantenuti all'interno del Stati Uniti. Tutti i dati dei clienti, ad esempio le impostazioni dell'app e i dati utente, si trovano nella posizione scelta dal cliente e non sono gestiti dal servizio.

I metadati del servizio vengono replicati nel Stati Uniti a scopo di ripristino di emergenza.