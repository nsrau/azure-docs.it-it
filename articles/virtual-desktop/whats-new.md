---
title: Novità di Desktop virtuale Windows - Azure
description: Nuove funzionalità e aggiornamenti del prodotto per Desktop virtuale Windows.
author: Heidilohr
ms.topic: overview
ms.date: 08/07/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 8663fc3f016fadcd4f4c99acd800cd0ccf8844f8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003527"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Novità di Desktop virtuale Windows

Desktop virtuale Windows viene aggiornato regolarmente. Questo articolo include le informazioni seguenti:

- Aggiornamenti più recenti
- Nuove funzionalità
- Miglioramenti alle funzionalità esistenti
- Correzioni di bug

Questo articolo viene aggiornato ogni mese. Controllare spesso l'articolo per ottenere informazioni sui nuovi aggiornamenti.

## <a name="july-2020"></a>Luglio 2020  

Nel mese di luglio è diventato disponibile a livello generale Desktop virtuale Windows con l'integrazione con Azure Resource Manager.

Ecco le modifiche apportate in questa nuova versione: 

- La "versione Autunno 2019" è ora denominata "Desktop virtuale Windows (versione classica)", mentre la "versione Primavera 2020" è ora semplicemente "Desktop virtuale Windows". Per altre informazioni, vedere [questo post di blog](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/). 

Per altre informazioni sulle nuove funzionalità, vedere [questo post di blog](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="autoscaling-tool-update"></a>Aggiornamento dello strumento di scalabilità automatica

L'ultima versione dello strumento di scalabilità automatica, precedentemente in anteprima, è ora disponibile a livello generale. Questo strumento usa un account di automazione di Azure e un'app per la logica di Azure per arrestare e riavviare automaticamente le macchine virtuali (VM) host della sessione all'interno di un pool di host, riducendo i costi dell'infrastruttura. Per altre informazioni, vedere [Ridimensionare gli host di sessione con Automazione di Azure](set-up-scaling-script.md).

### <a name="azure-portal"></a>Portale di Azure

È ora possibile eseguire le operazioni seguenti con il portale di Azure in Desktop virtuale Windows: 

- Assegnare direttamente gli utenti agli host di sessione del desktop personale  
- Modificare l'impostazione dell'ambiente di convalida per i pool di host 

### <a name="diagnostics"></a>Diagnostica

Sono state rilasciate alcune nuove query predefinite per l'area di lavoro Log Analytics. Per accedere alle query, passare a **Log** e selezionare **Desktop virtuale Windows** in **Categoria**. Per altre informazioni, vedere [Usare Log Analytics per la funzionalità di diagnostica](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Aggiornamento per il client Desktop remoto per Android

Il [client Desktop remoto per Android](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) supporta ora le connessioni a Desktop virtuale Windows. A partire dalla versione 10.0.7, il client Android offre una nuova interfaccia utente per una migliore esperienza utente. Il client si integra anche con Microsoft Authenticator nei dispositivi Android per abilitare l'accesso condizionale per la sottoscrizione di aree di lavoro Desktop virtuale Windows.  

La versione precedente del client Desktop remoto è ora denominata "Desktop remoto 8". Tutte le connessioni esistenti nella versione precedente del client verranno trasferite senza problemi al nuovo client. Il nuovo client è stato riscritto per lo stesso motore di base RDP sottostante dei client iOS e macOS, per velocizzare il rilascio delle nuove funzionalità in tutte le piattaforme. 

### <a name="teams-update"></a>Aggiornamento di Teams

Sono stati apportati alcuni miglioramenti a Microsoft Teams per Desktop virtuale Windows. Desktop virtuale Windows supporta ora soprattutto l'ottimizzazione audio e video per il client desktop Windows. Il reindirizzamento migliora la latenza creando percorsi diretti tra gli utenti quando effettuano chiamate e riunioni con audio o video. Una distanza minore comporta meno hop, quindi la chiamata ha un aspetto e un audio migliori. Per altre informazioni, vedere [Usare Teams in Desktop virtuale Windows](teams-on-wvd.md).

## <a name="june-2020"></a>Giugno 2020

Il mese scorso è stato presentato Desktop virtuale Windows con l'integrazione con Azure Resource Manager in anteprima. Questo aggiornamento offre molte nuove funzionalità straordinarie. Ecco le novità per questa versione di Desktop virtuale Windows.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Integrazione di Desktop virtuale Windows con Azure Resource Manager

Desktop virtuale Windows è ora integrato in Azure Resource Manager. Nell'aggiornamento più recente tutti gli oggetti di Desktop virtuale Windows sono ora risorse di Azure Resource Manager. Questo aggiornamento è integrato anche con il controllo degli accessi in base al ruolo di Azure. Per altre informazioni, vedere [Informazioni su Azure Resource Manager](../azure-resource-manager/management/overview.md).

Questa modifica ha gli effetti seguenti:

- Desktop virtuale Windows è ora integrato con il portale di Azure. È quindi possibile gestire tutto direttamente nel portale, senza che sia necessario usare PowerShell, app Web o strumenti di terze parti. Per iniziare, vedere l'esercitazione [Creare un pool di host con il portale di Azure](create-host-pools-azure-marketplace.md).

- Prima dell'aggiornamento era possibile pubblicare RemoteApps e desktop solo per singoli utenti. Grazie ad Azure Resource Manager, è ora possibile pubblicare risorse nei gruppi di Azure Active Directory.

- La versione precedente di Desktop virtuale Windows include quattro ruoli di amministratore predefiniti che è possibile assegnare a un tenant o a un pool di host. Questi ruoli sono ora disponibili nel [controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md). È possibile applicare questi ruoli a ogni oggetto di Azure Resource Manager di Desktop virtuale Windows, in modo da ottenere un modello di delega completo e avanzato.

- In questo aggiornamento non è più necessario eseguire Azure Marketplace o il modello di GitHub ripetutamente per espandere un pool di host. Per espandere un pool di host è sufficiente passare al pool di host nel portale di Azure e selezionare **+ Aggiungi** per distribuire host di sessione aggiuntivi.

- La distribuzione di pool di host è ora completamente integrata con la [Raccolta immagini condivise di Azure](../virtual-machines/windows/shared-image-galleries.md). La Raccolta immagini condivise è un servizio separato di Azure che consente di archiviare definizioni delle immagini di macchine virtuali, incluso il controllo delle versioni delle immagini. È anche possibile usare la replica globale per copiare e inviare immagini ad altre aree di Azure per la distribuzione locale.

- Le funzioni di monitoraggio eseguite in precedenza tramite PowerShell o l'app Web Servizio diagnostica sono ora state spostate in Log Analytics nel portale di Azure. Sono inoltre ora disponibili due opzioni per visualizzare i report. È possibile eseguire query di Kusto e usare Cartelle di lavoro per creare report visivi.

- Non è più necessario completare il consenso di Azure Active Directory (Azure AD) per usare Desktop virtuale Windows. In questo aggiornamento, il tenant di Azure AD nella sottoscrizione di Azure esegue l'autenticazione degli utenti e fornisce i controlli degli accessi in base al ruolo di Azure per gli amministratori.


### <a name="powershell-support"></a>Supporto PowerShell

Sono stati aggiunti nuovi cmdlet di AzWvd al modulo Az di Azure PowerShell con questo aggiornamento. Questo nuovo modulo è supportato in PowerShell Core, in esecuzione in .NET Core.

Per installare il modulo, seguire le istruzioni disponibili in [Configurare il modulo PowerShell per Desktop virtuale Windows](powershell-module.md).

È anche possibile visualizzare un elenco dei comandi disponibili nelle [informazioni di riferimento su AzWvd PowerShell](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization).

Per altre informazioni sulle nuove funzionalità, vedere il [post di blog](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>Gateway aggiuntivi

È stato aggiunto un nuovo cluster di gateway in Sudafrica per ridurre la latenza della connessione.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams in Desktop virtuale Windows (anteprima)

Sono stati apportati alcuni miglioramenti a Microsoft Teams per Desktop virtuale Windows. Desktop virtuale Windows supporta ora soprattutto il reindirizzamento audio e visivo per le chiamate. Il reindirizzamento migliora la latenza mediante la creazione di percorsi diretti tra gli utenti quando effettuano una chiamata con audio o video. Una distanza minore comporta meno hop, quindi la chiamata ha un aspetto e un audio migliori.

Per altre informazioni, vedere il [post di blog](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui piani futuri, vedere la [roadmap di Desktop virtuale Windows in Microsoft 365](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).

Per informazioni sugli aggiornamenti per i client per Desktop virtuale Windows e 
Servizi Desktop remoto, vedere questi articoli:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
