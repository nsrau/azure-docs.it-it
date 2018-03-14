---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 47ef014f31c628fed9d7b2b005e67e8138d3e7e9
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
## <a name="terminology"></a>Terminologia

Un'immagine del Marketplace in Azure presenta gli attributi seguenti:

* **Server di pubblicazione**: un'organizzazione che ha creato l'immagine. Esempi: Canonical, MicrosoftWindowsServer
* **Offerta**: nome di un gruppo di immagini correlate create da un server di pubblicazione. Esempi: Ubuntu Server, WindowsServer
* **SKU**: un'istanza di un'offerta, ad esempio una versione principale di una distribuzione. Esempi: 16.04-LTS, 2016-Datacenter
* **Versione**: il numero di versione di un'immagine SKU. 

Per identificare un'immagine del Marketplace quando si distribuisce una macchina virtuale a livello di codice, specificare questi valori singolarmente come parametri; in alternativa, alcuni strumenti accettano l'*URN* dell'immagine. L'URN combina tali valori, separati dal carattere due punti (:): *Server di pubblicazione*:*Offerta*:*SKU*:*Versione*. In un URN è possibile sostituire il numero di versione con "latest", che seleziona la versione più recente dell'immagine. 

Se il server di pubblicazione di immagini fornisce condizioni di licenza e di acquisto aggiuntive, è necessario accettare tali condizioni e abilitare la distribuzione a livello di codice. È inoltre necessario fornire i parametri del *piano di acquisto* quando si distribuisce una macchina virtuale a livello di codice. Vedere [Distribuire un'immagine con le condizioni del Marketplace](#deploy-an-image-with-marketplace-terms).