---
title: Novità di Desktop virtuale Windows - Azure
description: Nuove funzionalità e aggiornamenti del prodotto per Desktop virtuale Windows.
author: Heidilohr
ms.topic: overview
ms.date: 11/11/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.custom: references_regions
ms.openlocfilehash: c82312b40955b392243bf616ee991c0108a13fb5
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94537391"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Novità di Desktop virtuale Windows

Desktop virtuale Windows viene aggiornato regolarmente. Questo articolo include le informazioni seguenti:

- Aggiornamenti più recenti
- Nuove funzionalità
- Miglioramenti alle funzionalità esistenti
- Correzioni di bug

Questo articolo viene aggiornato ogni mese. Controllare spesso l'articolo per ottenere informazioni sui nuovi aggiornamenti.

## <a name="october-2020"></a>Ottobre 2020

Ecco cosa è cambiato in ottobre 2020:

### <a name="improved-performance"></a>prestazioni migliorate

- Sono state ottimizzate le prestazioni riducendo la latenza di connessione nelle aree geografiche di Azure seguenti:
    - Svizzera
    - Canada

È ora possibile usare lo [strumento di valutazione dell'esperienza](https://azure.microsoft.com/services/virtual-desktop/assessment/) per stimare la qualità dell'esperienza utente in queste aree.

### <a name="azure-government-cloud-availability"></a>Disponibilità del Cloud Azure per enti pubblici

Il Cloud Azure per enti pubblici è ora disponibile a livello generale. Per altre informazioni, vedere il [post di blog](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/).

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Aggiornamenti a Desktop virtuale Windows nel portale di Azure

Sono stati apportati alcuni aggiornamenti a Desktop virtuale Windows nel portale di Azure:

- È stato corretto un errore resourceID che impediva agli utenti di aprire la scheda "Sessioni".
- L'interfaccia utente della scheda "Host di sessione" è stata semplificata.
- Sono state corrette le impostazioni "Valori predefiniti," "Usabilità"e "Ripristina impostazioni predefinite" nelle proprietà RDP.
- Le funzioni "Rimuovi" ed "Elimina" sono state rese coerenti in tutte le schede.
- Il portale ora convalida i nomi delle app nel flusso di lavoro "Aggiungi un'app".
- È stato risolto un problema di disallineamento dei dati esportati dagli host di sessione nelle colonne.
- È stato risolto un problema a causa del quale non era possibile recuperare le sessioni utente nel portale.
- È stato risolto un problema di recupero dell'host di sessione che si verificava quando la macchina virtuale veniva creata in un gruppo di risorse diverso.
- È stata aggiornata la scheda "Host di sessione" in modo da elencare sia le sessioni attive che quelle disconnesse.
- La scheda "Applicazioni" ora ha più pagine.
- È stato risolto un problema a causa del quale il testo "richiede la riga di comando" non veniva visualizzato correttamente nella scheda "Elenco applicazioni".
- È stato risolto un problema a causa del quale non era possibile distribuire pool di host o macchine virtuali tramite il portale usando la versione in lingua tedesca della Raccolta immagini condivise.

### <a name="client-updates-for-october-2020"></a>Aggiornamenti client di ottobre 2020

Sono state rilasciate nuove versioni dei client. Per altre informazioni, vedere questi articoli:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

Per altre informazioni sugli altri client, vedere [Aggiornamenti client](#client-updates).

## <a name="september-2020"></a>Settembre 2020

Ecco cosa è cambiato a settembre 2020:

- Sono state ottimizzate le prestazioni riducendo la latenza di connessione nelle aree geografiche di Azure seguenti:
    - Germania
    - Sudafrica (solo per gli ambienti di convalida)

È ora possibile usare lo [strumento di valutazione dell'esperienza](https://azure.microsoft.com/services/virtual-desktop/assessment/) per stimare la qualità dell'esperienza utente in queste aree.

- È stata rilasciata la versione 1.2.1364 del client Windows Desktop per Desktop virtuale Windows. In questo aggiornamento sono state apportate le modifiche seguenti:
    - Risoluzione di un problema a causa del quale il processo Single Sign-On (SSO) non funzionava in Windows 7.
    - È stato risolto un problema che causava la disconnessione del client quando un utente che aveva abilitato l'ottimizzazione del contenuto multimediale per Teams tentava di chiamare o di partecipare a una riunione di Teams mentre un'altra app aveva un flusso audio aperto in modalità esclusiva.
    - È stato risolto un problema a causa del quale Teams non enumerava i dispositivi audio o video quando era abilitata l'ottimizzazione del contenuto multimediale per Teams.
    - Aggiunta di un collegamento per ottenere assistenza con le impostazioni nella pagina delle impostazioni desktop.
    - Risoluzione di un problema relativo al pulsante "Sottoscrivi" che si verificava quando si usavano temi scuri a contrasto elevato.
    
- Grazie all'enorme aiuto degli utenti, sono stati risolti due problemi critici per il client Desktop remoto di Microsoft Store. Microsoft continuerà a esaminare il feedback e a risolvere i problemi man mano che il rilascio graduale del client viene esteso a un numero sempre maggiore di utenti in tutto il mondo.
    
- È stata aggiunta una nuova funzionalità che consente di modificare la posizione, l'immagine, il gruppo di risorse, il nome del prefisso e la configurazione di rete di una macchina virtuale nell'ambito del flusso di lavoro per l'aggiunta di una VM alla distribuzione nel portale di Azure.

- I professionisti IT possono ora gestire le VM Windows 10 Enterprise aggiunte ad Azure Active Directory ibrido tramite Microsoft Endpoint Manager. Per altre informazioni, vedere il [post di blog](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048).

## <a name="august-2020"></a>Agosto 2020

Ecco cosa è cambiato in agosto 2020:

- Sono state migliorate le prestazioni per ridurre la latenza di connessione nelle aree di Azure seguenti: 

    - Regno Unito
    - Francia
    - Norvegia
    - Corea del Sud

   È possibile usare lo [strumento di valutazione dell'esperienza](https://azure.microsoft.com/services/virtual-desktop/assessment/) per farsi un'idea generale dell'impatto di questi cambiamenti sugli utenti.

- Il Client Desktop remoto di Microsoft Store (v10.2.1522+) è ora disponibile a livello generale. Questa versione del client è compatibile con Desktop virtuale Windows. Sono stati introdotti anche flussi di interfaccia utente aggiornati per migliorare l'esperienza utente. Questo aggiornamento include Fluent Design, le modalità chiara e scura e molti altri cambiamenti interessanti. Il client è stato inoltre riscritto in modo da usare lo stesso motore RDP (Remote Desktop Protocol) sottostante dei client iOS, macOS e Android. Questo velocizza la distribuzione delle nuove funzionalità su tutte le piattaforme. [Scaricare il client](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab) per provarlo.

- È stato risolto un problema nel client di Teams Desktop (versione 1.3.00.21759) a causa del quale il client mostrava solo il fuso orario UTC nella chat, nei canali e nel calendario. Il client aggiornato ora mostra il fuso orario della sessione remota.

- Azure Advisor fa ora parte di Desktop virtuale Windows. Quando si accede a Desktop virtuale Windows tramite il portale di Azure, è possibile visualizzare i suggerimenti per l'ottimizzazione dell'ambiente di Desktop virtuale Windows. Altre informazioni su [Azure Advisor](azure-advisor.md).

- L'interfaccia della riga di comando di Azure ora supporta Desktop virtuale Windows (`az desktopvirtualization`) per facilitare l'automazione delle distribuzioni di Desktop virtuale Windows. Per un elenco dei comandi dell'estensione, vedere [desktopvirtualization](/cli/azure/ext/desktopvirtualization/?view=azure-cli-latest&preserve-view=true).

- I modelli di distribuzione sono stati aggiornati per renderli completamente compatibili con le interfacce di Azure Resource Manager e Desktop virtuale Windows. I modelli sono disponibili su [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates).

- Desktop virtuale Windows è ora disponibile in anteprima pubblica nelle aree US Gov insieme all'integrazione con il portale di Azure. Per altre informazioni, vedere questo [annuncio](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/).

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

È anche possibile visualizzare un elenco dei comandi disponibili nelle [informazioni di riferimento su AzWvd PowerShell](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization&preserve-view=true).

Per altre informazioni sulle nuove funzionalità, vedere il [post di blog](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>Gateway aggiuntivi

È stato aggiunto un nuovo cluster di gateway in Sudafrica per ridurre la latenza della connessione.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams in Desktop virtuale Windows (anteprima)

Sono stati apportati alcuni miglioramenti a Microsoft Teams per Desktop virtuale Windows. Desktop virtuale Windows supporta ora soprattutto il reindirizzamento audio e visivo per le chiamate. Il reindirizzamento migliora la latenza mediante la creazione di percorsi diretti tra gli utenti quando effettuano una chiamata con audio o video. Una distanza minore comporta meno hop, quindi la chiamata ha un aspetto e un audio migliori.

Per altre informazioni, vedere il [post di blog](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="client-updates"></a>Aggiornamenti client

Per informazioni sugli aggiornamenti per i client per Desktop virtuale Windows e 
Servizi Desktop remoto, vedere questi articoli:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui piani futuri, vedere la [roadmap di Desktop virtuale Windows in Microsoft 365](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).
