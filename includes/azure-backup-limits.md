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
ms.openlocfilehash: b345283f87c446ff3b583b0c5dd8a4be222303ae
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
I limiti seguenti si applicano ai Backup di Azure.

| Identificatore limite | Limite predefinito |
| --- | --- |
| Numero di server/macchine che possono essere registrati con ogni insieme di credenziali |50 per Windows Server/Client/SCDPM  <br/> 200 per le macchine virtuali IaaS |
| Dimensioni di un'origine dati per i dati archiviati nel servizio di archiviazione Azure insieme di credenziali |54400 GB max<sup>1</sup> |
| Numero di insiemi di credenziali di backup creati in ogni sottoscrizione di Azure |25 insiemi di credenziali dei servizi di ripristino per ogni area |
| Numero di volte in cui è possibile pianificare backup al giorno |3 al giorno per Windows Server/Client <br/> 2 al giorno per SCDPM <br/> Una volta al giorno per le macchine virtuali IaaS |
| Dischi dati collegati a una macchina virtuale di Azure per il backup |16 |
| Dimensione di un singolo disco dati collegato a una macchina virtuale di Azure per il backup| 4095 GB <sup>2</sup>|

* <sup>1</sup>Il limite di 54400 GB non si applica ai backup delle macchine virtuali IaaS.
 

