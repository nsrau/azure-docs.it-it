---
title: File di inclusione
description: File di inclusione
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 63922eb623576379058c9a8a367d6e52249115f2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109094"
---
I limiti seguenti si applicano ai Backup di Azure.

| **Limite** | **Valore predefinito** |
| --- | --- |
| Server/macchine virtuali che è possibile registrare in un insieme di credenziali | Windows Server/Windows Client/System Center DPM: 50 <br/><br/> Macchine virtuali IaaS: 1000  |
| Dimensione di un'origine dati nella risorsa di archiviazione dell'insieme di credenziali |54400 GB al massimo. Il limite non si applica al backup delle macchine virtuali IaaS. |
| Insiemi di credenziali di backup in una sottoscrizione di Azure |500 insiemi di credenziali per ogni area |
| Pianificazione dei backup giornalieri |Windows Server/Client: Tre volte al giorno<br/> System Center DPM: Due volte al giorno <br/> Macchine virtuali IaaS: Una volta al giorno  |
| Dischi dati collegati a una macchina virtuale di Azure per il backup | 32 |
| Singolo disco dati collegato a una macchina virtuale di Azure per il backup| 4095 GB|



