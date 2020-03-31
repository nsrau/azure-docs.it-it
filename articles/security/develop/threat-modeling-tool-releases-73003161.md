---
title: Microsoft Threat Modeling Tool versione 22/03/2020 - Azure
description: Documentazione delle note sulla versione per lo strumento di modellazione delle minacce
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146862"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Aggiornamento di Threat Modeling Tool versione 7.3.00316.1 - 22/03/03/2020

La versione 7.3.00316.1 di Microsoft Threat Modeling Tool (TMT) è stata rilasciata il 22 marzo 2020 e contiene le seguenti modifiche:

- Miglioramenti all'accessibilità
- Correzioni di bug
- Nuova funzione DiagramReader

## <a name="notable-bug-fixes"></a>Correzioni di bug notevoli

### <a name="exporting-the-threat-list-to-csv"></a>Esportazione dell'elenco delle minacce in CSV

La funzione di esportazione in CSV stava selezionando in modo incoerente i campi dall'elenco delle minacce da esportare. Ora tutti i campi dall'elenco delle minacce verranno esportati nel file CSV. 

### <a name="ux-bugs"></a>Bug dell'esperienza utente

- I menu della Guida nel flusso di lavoro principale (creazione/apertura/analisi) e l'esperienza dell'editor di modelli ora hanno opzioni di menu coerenti.
- La barra di ricerca nel riquadro degli stencil ora ha un cursore standard e sono state aggiunte le etichette appropriate.

## <a name="new-features"></a>Nuove funzionalità

### <a name="diagramreader-feature-has-been-added"></a>La funzione DiagramReader è stata aggiunta

Una nuova funzionalità DiagramReader è stata aggiunta nel menu principale mentre è aperto un modello. Questa funzione convertirà la rappresentazione grafica del modello in una narrazione basata su testo. 

## <a name="system-requirements"></a>Requisiti di sistema

- Sistemi operativi supportati:
  - [Aggiornamento dell'anniversario di Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) o versione successiva
- È richiesta la versione .NET:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) o versione successiva
- Requisiti aggiuntivi:
  - Una connessione Internet per ricevere aggiornamenti allo strumento e modelli

## <a name="documentation-and-feedback"></a>Documentazione e feedback

- La documentazione relativa a Threat Modeling Tool si trova su [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) e include le informazioni [sull'uso dello strumento](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Passaggi successivi

Scaricare la versione più recente di [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
