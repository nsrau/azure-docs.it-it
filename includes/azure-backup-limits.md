---
title: File di inclusione
description: File di inclusione
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: 1f41567f7d5dcc9b6536b47ef639ba53abff8ec4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755740"
---
I limiti seguenti si applicano ai Backup di Azure.

| Identificatore limite | Limite predefinito |
| --- | --- |
| Numero di server/macchine che possono essere registrati con ogni insieme di credenziali |50 per Windows Server/Client/SCDPM  <br/> 1000 per le macchine virtuali IaaS |
| Dimensioni di un'origine dati per i dati archiviati nel servizio di archiviazione Azure insieme di credenziali |54400 GB max<sup>1</sup> |
| Numero di insiemi di credenziali di backup creati in ogni sottoscrizione di Azure |500 insiemi di credenziali di Servizi di ripristino per ogni area |
| Numero di volte in cui è possibile pianificare backup al giorno |3 al giorno per Windows Server/Client <br/> 2 al giorno per SCDPM <br/> Una volta al giorno per le macchine virtuali IaaS |
| Dischi dati collegati a una macchina virtuale di Azure per il backup |16 |
| Dimensione di un singolo disco dati collegato a una macchina virtuale di Azure per il backup| 4095 GB <sup>2</sup>|

* <sup>1</sup>Il limite di 54400 GB non si applica ai backup delle macchine virtuali IaaS.
 

