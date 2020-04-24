---
title: Distribuzione dell'agente di Sincronizzazione file di Azure
description: Distribuzione dell'agente di Sincronizzazione file di Azure. Un blocco di testo comune, condiviso tra i documenti di migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209415"
---
In questa sezione si installa l'agente di Sincronizzazione file di Azure in Windows Server.
Nella [Guida alla distribuzione](../articles/storage/files/storage-sync-files-deployment-guide.md) viene illustrato che è necessario disattivare la **sicurezza avanzata di IE**. Sicurezza avanzata di Internet Explorer è una misura di sicurezza non applicabile con Sincronizzazione file di Azure e la sua disattivazione consente di eseguire l'autenticazione in Azure senza problemi.

Aprire PowerShell e installare i moduli di PowerShell necessari con i comandi seguenti. Quando richiesto, assicurarsi di installare il modulo completo e il provider NuGet:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Se si verificano problemi durante il raggiungimento di Internet dal server, è ora possibile risolverli. Sincronizzazione file di Azure usa qualsiasi connessione di rete disponibile a Internet.
È supportata anche la richiesta di un server proxy per raggiungere Internet. È ora possibile configurare un proxy a livello di computer oppure specificare un proxy che verrà usato solo da sincronizzazione file durante l'installazione dell'agente.

Se questo significa che è necessario aprire i firewall per questo server, è possibile che si tratta di un approccio accettabile. Al termine dell'installazione del server, dopo aver completato la registrazione del server, sarà presente un report sulla connettività di rete che Mostra gli URL dell'endpoint esatti in Azure, per la quale è necessario comunicare la sincronizzazione dei file per l'area selezionata. Il report indica anche il motivo per cui è necessaria la comunicazione. È possibile utilizzare il report per bloccare i firewall presenti in questo server a URL specifici.

È anche possibile seguire un approccio più conservativo, in cui non si aprono i firewall, ma si limita il server a comunicare con spazi dei nomi DNS di livello superiore. sono disponibili più documentazione e dettagli nell'articolo sulle [impostazioni del proxy sincronizzazione file di Azure e del firewall](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) . Seguire le procedure consigliate per la rete.

Al termine dell' *installazione* guidata del server verrà visualizzata la *registrazione* guidata server.
Registrare il server nella risorsa di Azure del servizio di sincronizzazione archiviazione da una versione precedente.

Questi passaggi sono descritti in modo più dettagliato nella Guida alla distribuzione, inclusi i moduli di PowerShell sopra indicati, che è necessario installare per primo: [sincronizzazione file di Azure l'installazione dell'agente](../articles/storage/files/storage-sync-files-deployment-guide.md).

È necessario usare l'agente più recente e può essere scaricato dall'area download Microsoft: [sincronizzazione file di Azure-Agent](https://aka.ms/AFS/agent "Download agente Sincronizzazione file di Azure").

Al termine dell'installazione e della registrazione del server, è possibile verificare di aver completato correttamente questo passaggio: passare alla risorsa del servizio di sincronizzazione archiviazione nel portale di Azure, quindi seguire il menu a sinistra per "Server registrati". Il server sarà visualizzato immediatamente.
