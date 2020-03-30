---
title: Distribuzione dell'agente di Sincronizzazione file di AzureDeploying the Azure File Sync agent
description: Distribuzione dell'agente di Sincronizzazione file di Azure.Deploying the Azure File Sync agent. Blocco di testo comune, condiviso tra documenti di migrazione.
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
In this section, you install the Azure File Sync agent on your Windows Server.
La guida alla [distribuzione](../articles/storage/files/storage-sync-files-deployment-guide.md) illustra che è necessario disattivare la protezione avanzata di **Internet IE.** La sicurezza avanzata di Internet IE è una misura di sicurezza che non è applicabile con Sincronizzazione file di Azure e la sua disattivazione consente di eseguire l'autenticazione in Azure senza problemi.

Aprire PowerShell e installare i moduli di PowerShell necessari con i comandi seguenti. Assicurarsi di installare il modulo completo e il provider NuGet quando richiesto:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Se avete problemi a raggiungere Internet dal server, ora è il momento di risolverli. Sincronizzazione file di Azure usa qualsiasi connessione di rete disponibile a Internet.Azure File Sync uses any available network connection to the internet.
È inoltre supportata la richiesta di un server proxy per raggiungere Internet. È possibile configurare subito un proxy a livello di computer oppure specificare un proxy che verrà utilizzato dalla sincronizzazione dei file durante l'installazione dell'agente.

Se questo significa che è necessario aprire i firewall per questo server, allora che potrebbe essere un approccio accettabile per voi. Al termine dell'installazione del server, dopo aver completato la registrazione del server, verrà visualizzato un rapporto sulla connettività di rete che mostra gli URL esatti dell'endpoint in Azure con cui deve essere necessario comunicare per l'area selezionata. Il rapporto indica anche il motivo per cui è necessaria la comunicazione. È possibile utilizzare il report per quindi bloccare i firewall intorno a questo server, a URL specifici.

È anche possibile seguire un approccio più conservativo, in cui non si aprono i firewall a livello largo, ma limitare il server per comunicare con spazi dei nomi DNS di livello superiore: sono disponibili più documentazione e dettagli nell'articolo Impostazioni del firewall e del proxy di [Sincronizzazione file](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) di Azure.You can also follow a more conservative approach, in which you don't open the firewalls wide, but instead limit the server to communicate to higher-level DNS name spaces - there is more documentation and details available in the Azure File Sync proxy and firewall settings article. Seguire le procedure consigliate di rete.

Al termine *dell'installazione* guidata del server, verrà visualizzata una procedura guidata di *registrazione* del server.
Registrare il server nella risorsa di Azure del servizio di sincronizzazione archiviazione da in precedenza.

Questi passaggi sono descritti in modo più dettagliato nella guida alla distribuzione, inclusi i moduli di PowerShell precedenti che è necessario installare per primi: [Installazione dell'agente di Sincronizzazione file](../articles/storage/files/storage-sync-files-deployment-guide.md)di Azure.

L'agente più recente deve essere usato e può essere scaricato dall'Area download Microsoft: [Sincronizzazione file di Azure - Agente](https://aka.ms/AFS/agent "Download dell'agente di Sincronizzazione file di AzureAzure File Sync agent download").

Dopo aver completato correttamente l'installazione e la registrazione del server, è possibile verificare di aver completato correttamente questo passaggio: passare alla risorsa del servizio di sincronizzazione archiviazione nel portale di Azure, quindi seguire il menu a sinistra a "Server registrati". Vedrai il tuo server elencato lì subito.
