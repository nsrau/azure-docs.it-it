---
title: Distribuire l'agente di Sincronizzazione file di Azure
description: Distribuzione dell'agente di Sincronizzazione file di Azure. Un blocco di testo comune, condiviso tra documenti di migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 66388f14949b4f398de18c9162ca453e7fb3cbe1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143576"
---
In questa sezione si installa l'agente di Sincronizzazione file di Azure nell'istanza di Windows Server.

Nella [Guida alla distribuzione](../articles/storage/files/storage-sync-files-deployment-guide.md) viene illustrato che è necessario disattivare la **configurazione della sicurezza avanzata di Internet Explorer**. Questa misura di sicurezza non è applicabile con Sincronizzazione file di Azure. La disattivazione consente di eseguire l'autenticazione in Azure senza problemi.

Aprire PowerShell e installare i moduli di PowerShell necessari usando i comandi seguenti. Quando viene richiesto, assicurarsi di installare il modulo completo e il provider NuGet.

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Se si verificano problemi durante il raggiungimento di Internet dal server, è ora possibile risolverli. Sincronizzazione file di Azure usa qualsiasi connessione di rete disponibile a Internet. È supportata anche la richiesta di un server proxy per raggiungere Internet. È ora possibile configurare un proxy a livello di computer oppure specificare un proxy che verrà usato da Sincronizzazione file di Azure, durante l'installazione dell'agente.

Se la configurazione di un proxy significa che è necessario aprire i firewall per questo server, potrebbe trattarsi di un approccio accettabile. Al termine dell'installazione del server, dopo aver completato la registrazione del server, un report sulla connettività di rete visualizzerà gli URL dell'endpoint esatti in Azure per cui Sincronizzazione file di Azure necessario comunicare per l'area selezionata. Il report indica anche il motivo per cui è necessaria la comunicazione. È possibile utilizzare il report per bloccare i firewall attorno al server a URL specifici.

È anche possibile seguire un approccio più conservativo in cui non si aprono i firewall, ma si limita il server a comunicare con spazi dei nomi DNS di livello superiore. Per ulteriori informazioni, vedere [sincronizzazione file di Azure impostazioni del proxy e del firewall](../articles/storage/files/storage-sync-files-firewall-and-proxy.md). Seguire le procedure consigliate per la rete.

Al termine dell' *installazione* guidata del server verrà aperta una *registrazione* guidata del server. Registrare il server nella risorsa di Azure del servizio di sincronizzazione archiviazione precedente.

Questi passaggi sono descritti in modo più dettagliato nella Guida alla distribuzione, inclusi i moduli di PowerShell da installare per primi: [installazione di sincronizzazione file di Azure Agent](../articles/storage/files/storage-sync-files-deployment-guide.md).

Usare l'agente più recente. È possibile scaricarlo dall'area download Microsoft: [sincronizzazione file di Azure Agent](https://aka.ms/AFS/agent "Download agente Sincronizzazione file di Azure").

Dopo aver completato correttamente l'installazione e la registrazione del server, è possibile verificare di aver completato questo passaggio. Passare alla risorsa del servizio di sincronizzazione archiviazione nel portale di Azure, quindi scegliere **Server registrati**dal menu a sinistra. Il server verrà elencato qui.
