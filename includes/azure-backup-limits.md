---
title: File di inclusione
description: File di inclusione
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 9/10/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: 64101ea5a3bbaac4a6b2e349a04d06ea84a87081
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381086"
---
I limiti seguenti si applicano ai Backup di Azure.

| Identificatore limite | Limite predefinito |
| --- | --- |
| Numero di server/macchine che possono essere registrati con ogni insieme di credenziali |50 per Windows Server/Client/SCDPM  <br/> 1000 per le macchine virtuali IaaS |
| Dimensioni di un'origine dati per i dati archiviati nel servizio di archiviazione Azure insieme di credenziali |54400 GB max<sup>1</sup> |
| Numero di insiemi di credenziali di backup creati in ogni sottoscrizione di Azure |500 insiemi di credenziali di Servizi di ripristino per ogni area |
| Numero di volte in cui è possibile pianificare backup al giorno |3 al giorno per Windows Server/Client <br/> 2 al giorno per SCDPM <br/> Una volta al giorno per le macchine virtuali IaaS |
| Dischi dati collegati a una macchina virtuale di Azure per il backup |16 |
| Dimensione di un singolo disco dati collegato a una macchina virtuale di Azure per il backup| 4095 GB|

* <sup>1</sup>Il limite di 54400 GB non si applica ai backup delle macchine virtuali IaaS.
 

