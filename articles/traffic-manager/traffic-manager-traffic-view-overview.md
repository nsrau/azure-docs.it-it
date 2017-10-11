---
title: Visualizzazione traffico di Gestione traffico di Azure | Microsoft Docs
description: Introduzione a Visualizzazione di traffico di Gestione traffico
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f1ab98ab173edc794bfabdf55d950ac689a37c0a
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="traffic-manager-traffic-view"></a>Visualizzazione traffico di Gestione traffico

>[!NOTE]
>La funzionalità Visualizzazione traffico di Gestione traffico è attualmente in versione di anteprima pubblica e potrebbe non offrire lo stesso livello di disponibilità e affidabilità delle funzionalità presenti nella versione con disponibilità generale. La funzionalità non è supportata, potrebbe avere funzioni vincolate e potrebbe non essere disponibile in tutte le località di Azure. Per ricevere le notifiche più aggiornate su disponibilità e stato di questa funzionalità, vedere la pagina [Aggiornamenti di Gestione traffico di Azure](https://azure.microsoft.com/updates/?product=traffic-manager).

Gestione traffico fornisce il routing del livello DNS in modo che gli utenti finali vengano indirizzati a endpoint integri in base al metodo di routing impostato quando viene creato il profilo. Questo fornisce a Gestione traffico una visualizzazione delle basi utente (a livello di granularità del resolver DNS) e il modello di traffico. Quando si abilita Visualizzazione traffico, tali informazioni vengono elaborate per offrire approfondimenti attuabili. 

Con Visualizzazione traffico è possibile:
- comprendere dove si trovano le basi utente (fino a una granularità di livello del resolver DNS locale).
- conoscere il volume di traffico proveniente da tali aree.
- sapere qual è la latenza rappresentativa riscontrata dagli utenti.
- immergersi in modelli di traffico specifici da ognuna di queste basi utente alle aree di Azure in cui sono presenti endpoint. 

Ad esempio, è possibile usare Visualizzazione traffico per comprendere quali aree hanno un numero elevato di traffico ma sono soggette a latenze più elevate. Successivamente è possibile usare queste informazioni per pianificare l'espansione footprint a nuove aree di Azure in modo che questi utenti possano avere un'esperienza di latenza più bassa.

## <a name="how-traffic-view-works"></a>Funzionamento di Visualizzazione traffico

Visualizzazione traffico fa in modo che Gestione traffico esamini le query in ingresso ricevute negli ultimi sette giorni da un profilo che ha questa funzionalità abilitata. Da tali informazioni, estrae l'IP di origine del resolver DNS che viene quindi usato come una rappresentazione della posizione degli utenti. Questi vengono quindi raggruppati in una granularità a livello del resolver DNS per creare aree di basi utente usando le informazioni geografiche degli indirizzi IP gestiti da Gestione traffico. Gestione traffico analizza quindi le aree di Azure a cui la query è stata indirizzata e costruisce una mappa del flusso di traffico per gli utenti da tali aree.
Nel passaggio successivo, Gestione traffico mette in correlazione l'area di base utente al mapping dell'area di Azure con le tabelle di latenza intelligence di rete che mantiene per reti di utenti finali diverse per comprendere la latenza media riscontrata dagli utenti da tali aree quando si connettono ad aree di Azure. Tutti questi calcoli vengono quindi elaborati per livello di IP del resolver DNS locale prima di essere presentati all'utente. È possibile elaborare tali informazioni in un flusso di lavoro analitico a scelta; è anche possibile scaricare tali informazioni sotto forma di file CSV.
Quando si usa Visualizzazione traffico, la fatturazione viene eseguita in base al numero di punti dati usati per creare le informazioni presentate. L'unico tipo di punto dati attualmente usato corrisponde alle query ricevute dal profilo di Gestione traffico. Per altri dettagli sui prezzi, visitare la [pagina dei prezzi di Gestione Traffico](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [funzionamento di Gestione traffico](traffic-manager-overview.md)
- Ulteriori informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico
- Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-create-profile.md)


