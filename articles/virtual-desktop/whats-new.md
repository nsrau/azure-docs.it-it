---
title: Novità di Desktop virtuale Windows - Azure
description: Nuove funzionalità e aggiornamenti del prodotto per Desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 06/15/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 383b59d10fa1de1b2ee0ea8b505c164577487255
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84974382"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Novità di Desktop virtuale Windows

Desktop virtuale Windows viene aggiornato regolarmente. Questo articolo include le informazioni seguenti:

- Aggiornamenti più recenti
- Nuove funzionalità
- Miglioramenti alle funzionalità esistenti
- Correzioni di bug

Questo articolo viene aggiornato ogni mese. Controllare spesso l'articolo per ottenere informazioni sui nuovi aggiornamenti.

## <a name="june-2020"></a>Giugno 2020

Il mese scorso è stato presentato in anteprima l'aggiornamento della primavera 2020 per Desktop virtuale Windows. Questo aggiornamento offre molte nuove funzionalità straordinarie. Di seguito sono illustrate le novità dell'aggiornamento della primavera 2020.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager-preview"></a>Desktop virtuale Windows è ora integrato con Azure Resource Manager (anteprima)

Desktop virtuale Windows è ora integrato in Azure Resource Manager. Nell'aggiornamento più recente tutti gli oggetti di Desktop virtuale Windows sono ora risorse di Azure Resource Manager. Questo aggiornamento è integrato anche con il controllo degli accessi in base al ruolo di Azure. Per altre informazioni, vedere [Informazioni su Azure Resource Manager](../azure-resource-manager/management/overview.md).

Questa modifica ha gli effetti seguenti:

- Desktop virtuale Windows è ora integrato con il portale di Azure. È quindi possibile gestire tutto direttamente nel portale, senza che sia necessario usare PowerShell, app Web o strumenti di terze parti. Per iniziare, vedere l'esercitazione [Creare un pool di host con il portale di Azure](create-host-pools-azure-marketplace.md).

- Prima dell'aggiornamento della primavera 2020 era possibile pubblicare RemoteApps e desktop solo per singoli utenti. Grazie ad Azure Resource Manager, è ora possibile pubblicare risorse nei gruppi di Azure Active Directory.

- La versione precedente di Desktop virtuale Windows include quattro ruoli di amministratore predefiniti che è possibile assegnare a un tenant o a un pool di host. Questi ruoli sono ora disponibili nel [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) do Azure. È possibile applicare questi ruoli a ogni oggetto di Azure Resource Manager di Desktop virtuale Windows, in modo da ottenere un modello di delega completo e avanzato.

- Nell'aggiornamento della primavera 2020 non è più necessario eseguire Azure Marketplace o il modello di GitHub ripetutamente per espandere un pool di host. Per espandere un pool di host è sufficiente passare al pool di host nel portale di Azure e selezionare **+ Aggiungi** per distribuire host di sessione aggiuntivi.

- La distribuzione di pool di host è ora completamente integrata con la [Raccolta immagini condivise di Azure](../virtual-machines/windows/shared-image-galleries.md). La Raccolta immagini condivise è un servizio separato di Azure che consente di archiviare definizioni delle immagini di macchine virtuali, incluso il controllo delle versioni delle immagini. È anche possibile usare la replica globale per copiare e inviare immagini ad altre aree di Azure per la distribuzione locale.

- Le funzioni di monitoraggio eseguite in precedenza tramite PowerShell o l'app Web Servizio diagnostica sono ora state spostate in Log Analytics nel portale di Azure. Sono inoltre ora disponibili due opzioni per visualizzare i report. È possibile eseguire query di Kusto e usare Cartelle di lavoro per creare report visivi.

- Non è più necessario completare il consenso di Azure Active Directory (Azure AD) per usare Desktop virtuale Windows. Nell'aggiornamento della primavera 2020 il tenant di Azure AD nella sottoscrizione di Azure esegue l'autenticazione degli utenti e fornisce controlli del Controllo degli accessi in base al ruolo per gli amministratori.


### <a name="powershell-support"></a>Supporto PowerShell

Sono stati aggiunti nuovi cmdlet di AzWvd al modulo Az di Azure PowerShell con l'aggiornamento della primavera 2020. Questo nuovo modulo è supportato in PowerShell Core, in esecuzione in .NET Core.

Per installare il modulo, seguire le istruzioni disponibili in [Configurare il modulo PowerShell per Desktop virtuale Windows](powershell-module.md).

È anche possibile visualizzare un elenco dei comandi disponibili nelle [informazioni di riferimento su AzWvd PowerShell](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization).

Per altre informazioni sulle nuove funzionalità, vedere il [post di blog](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="additional-gateways"></a>Gateway aggiuntivi

È stato aggiunto un nuovo cluster di gateway in Sudafrica per ridurre la latenza della connessione.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams in Desktop virtuale Windows (anteprima)

Sono stati apportati alcuni miglioramenti a Microsoft Teams per Desktop virtuale Windows. Desktop virtuale Windows supporta ora soprattutto il reindirizzamento audio e visivo per le chiamate. Il reindirizzamento migliora la latenza mediante la creazione di percorsi diretti tra gli utenti quando effettuano una chiamata con audio o video. Una distanza minore comporta meno hop, quindi la chiamata ha un aspetto e un audio migliori.

Per altre informazioni, vedere il [post di blog](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sugli aggiornamenti per i client per Desktop virtuale Windows e 
Servizi Desktop remoto, vedere questi articoli:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
