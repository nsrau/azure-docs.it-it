---
title: Microsoft Threat Modeling Tool versione 03/22/2020-Azure
description: Documentazione delle note sulla versione per lo strumento di modellazione delle minacce
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80146862"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Versione di aggiornamento Threat Modeling Tool 7.3.00316.1-03/22/2020

La versione 7.3.00316.1 del Microsoft Threat Modeling Tool (TMT) è stata rilasciata il 22 2020 marzo e contiene le modifiche seguenti:

- Miglioramenti all'accessibilità
- Correzioni di bug
- Nuova funzionalità DiagramReader

## <a name="notable-bug-fixes"></a>Correzioni di bug rilevanti

### <a name="exporting-the-threat-list-to-csv"></a>Esportazione dell'elenco di minacce in CSV

La funzione Export to CSV non consente di selezionare in modo coerente i campi dall'elenco delle minacce che verranno esportati. Ora tutti i campi dell'elenco di minacce verranno esportati nel file CSV. 

### <a name="ux-bugs"></a>Bug di UX

- I menu della guida nel flusso di lavoro principale (Crea/Apri/analizza) e l'esperienza dell'editor dei modelli dispongono ora di opzioni di menu coerenti.
- La barra di ricerca nel riquadro stencil dispone ora di un cursore standard ed è stata aggiunta un'etichetta appropriata.

## <a name="new-features"></a>Nuove funzionalità

### <a name="diagramreader-feature-has-been-added"></a>Aggiunta la funzionalità DiagramReader

È stata aggiunta una nuova funzionalità DiagramReader nel menu principale mentre è aperto un modello. Questa funzionalità consente di convertire la rappresentazione grafica del modello in una descrizione basata su testo. 

## <a name="system-requirements"></a>Requisiti di sistema

- Sistemi operativi supportati:
  - [Aggiornamento dell'anniversario di Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) o versione successiva
- Versione di .NET obbligatoria:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) o versione successiva
- Requisiti aggiuntivi:
  - Una connessione Internet per ricevere aggiornamenti allo strumento e ai modelli

## <a name="documentation-and-feedback"></a>Documentazione e feedback

- La documentazione relativa a Threat Modeling Tool si trova su [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) e include le informazioni [sull'uso dello strumento](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Passaggi successivi

Scaricare la versione più recente di [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
