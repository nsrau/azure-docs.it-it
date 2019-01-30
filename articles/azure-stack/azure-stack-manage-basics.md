---
title: Nozioni fondamentali sull'amministrazione di Azure Stack | Microsoft Docs
description: Informazioni su quello che devi sapere per amministrare Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: a9bf2240e6e60a3d2d80bf4829d74f1043024d7e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239673"
---
# <a name="azure-stack-administration-basics"></a>Nozioni fondamentali sull'amministrazione di Azure Stack
Esistono diversi aspetti da sapere se si ha familiarità con amministrazione di Azure Stack. Questa guida fornisce una panoramica del ruolo operatore di Azure Stack e cosa occorre per informare gli utenti per loro diventare rapidamente produttivi.

## <a name="understand-the-builds"></a>Comprendere le compilazioni

### <a name="integrated-systems"></a>Sistemi integrati

Se si usa un sistema integrato Azure Stack, le versioni aggiornate dello Stack di Azure vengono distribuite attraverso i pacchetti di aggiornamento. È possibile importare i pacchetti e applicarle usando il riquadro di aggiornamenti nel portale di amministrazione.
 
### <a name="development-kit"></a>Kit di sviluppo

Se si usa Azure Stack Development Kit, consultare il [cos'è Azure Stack?](./asdk/asdk-what-is.md) articolo per assicurarsi di comprendere lo scopo del kit di sviluppo e le relative limitazioni. È necessario utilizzare il kit di sviluppo come una "sandbox" in cui è possibile valutare Azure Stack e sviluppare e testare le App in un ambiente non di produzione. (Per informazioni sulla distribuzione, vedere la [distribuzione di Azure Stack Development Kit](./asdk/asdk-install.md) articolo.)

Come Azure, abbiamo rapidità di innovazione. Saranno rilasciate regolarmente nuove compilazioni. Se si esegue il kit di sviluppo e si vuole spostare la build più recente, è necessario [ridistribuire Azure Stack](./asdk/asdk-redeploy.md). Non è possibile applicare i pacchetti di aggiornamento. Questo processo richiede tempo, ma il vantaggio è che è possibile provare le funzionalità più recenti. La documentazione development kit nel nostro sito Web riflette la build di versione più recente.

## <a name="learn-about-available-services"></a>Informazioni su servizi disponibili

È necessario consapevolezza dei servizi che è possibile rendere disponibili agli utenti. Azure Stack supporta un subset dei servizi di Azure. L'elenco di servizi supportati continuerà a evolversi.

**Servizi fondamentali**

Per impostazione predefinita, Azure Stack include i servizi seguenti: "base" quando si distribuisce Azure Stack:

- Calcolo
- Archiviazione
- Rete
- Key Vault

Con questi servizi fondamentali, è possibile offrire agli utenti con la configurazione minima Infrastructure-as-a-Service (IaaS).

**Servizi aggiuntivi**

Attualmente, sono supportati i servizi Platform-as-a-Service (PaaS) aggiuntivi seguenti:

- Servizio app
- Funzioni di Azure
- Database SQL e MySQL

Questi servizi richiedono un'ulteriore configurazione prima di renderli disponibili agli utenti. Per altre informazioni, vedere le esercitazioni "su" e le sezioni "guides\Offer procedure servizi" della nostra documentazione di operatore di Azure Stack.

**Guida di orientamento per servizio**

Azure Stack continuerà aggiungere il supporto per servizi di Azure. Per la Guida di orientamento previsto, vedere il [Azure Stack: Un'estensione di Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) white paper. È anche possibile monitorare il [post di blog di Azure Stack](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) per gli annunci di nuove.

## <a name="what-account-should-i-use"></a>Quale account si deve usare?
Esistono alcune considerazioni sull'account che è necessario tenere presenti durante la gestione di Azure Stack. In particolare nelle distribuzioni usando Windows Server Active Directory Federation Services (ADFS) come provider di identità anziché Azure Active Directory (Azure AD). Le considerazioni sull'account seguente si applicano a entrambi i sistemi integrati di Azure Stack e ASDK distribuzioni:


|Account|Azure AD|AD FS|
|-----|-----|-----|
|Amministratore locale (. \Administrator)|Amministratore di host ASDK|Amministratore di host ASDK|
|AzureStack\AzureStackAdmin|Amministratore di host ASDK<br><br>Può essere utilizzato per accedere al portale di amministrazione di Azure Stack<br><br>Accesso per visualizzare e amministrare gli anelli di Service Fabric|Amministratore di host ASDK<br><br>Nessun accesso al portale di amministrazione di Azure Stack<br><br>Accesso per visualizzare e amministrare gli anelli di Service Fabric<br><br>Non è più proprietario del Provider di sottoscrizione predefinite (DPS)|
|AzureStack\CloudAdmin|Può accedere ed eseguire i comandi consentiti all'interno dell'Endpoint con privilegi|Può accedere ed eseguire i comandi consentiti all'interno dell'Endpoint con privilegi<br><br>Non può accedere all'host ASDK<br><br>Proprietario della sottoscrizione del Provider predefinito (DPS)|
|Amministratore globale di Azure AD|Usato durante l'installazione<br><br>Proprietario della sottoscrizione del Provider predefinito (DPS)|Non applicabile|
|

## <a name="what-tools-do-i-use-to-manage"></a>Quali strumenti usare per la gestione?
 
È possibile usare la [portale di amministrazione](azure-stack-manage-portals.md) o Azure PowerShell per gestire Azure Stack. Il modo più semplice per apprendere i concetti di base è tramite il portale. Se si vuole usare PowerShell, sono disponibili passaggi di preparazione. È necessario [installare](azure-stack-powershell-install.md) PowerShell [scaricare](azure-stack-powershell-download.md) altri moduli, e [configurare](azure-stack-powershell-configure-admin.md) PowerShell.

Stack di Azure Usa Azure Resource Manager come relativo meccanismo di distribuzione, gestione e organizzazione sottostante. Se si intende gestire Azure Stack e consentire agli utenti del supporto, è consigliabile informazioni su Resource Manager. Vedere le [Introduzione ad Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) white paper.

## <a name="your-typical-responsibilities"></a>Le responsabilità tipiche del cliente

Gli utenti vogliono usare i servizi. Dal loro punto di vista, il ruolo principale consiste nel rendere disponibili questi servizi. È necessario decidere quali servizi offerti e rendere disponibili tali servizi mediante la creazione di piani, offerte e quote. Per altre informazioni, vedere [panoramica dell'offerta di servizi in Azure Stack](azure-stack-offer-services-overview.md). 

È necessario anche aggiungere elementi al [marketplace](azure-stack-marketplace.md), ad esempio le immagini di macchina virtuale. Il modo più semplice consiste [download di elementi di marketplace di Azure ad Azure Stack](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Se si desidera testare i piani, offerte e i servizi, è consigliabile usare la [portale per gli utenti](azure-stack-manage-portals.md); non il portale dell'amministratore.

Oltre a fornire servizi, è necessario eseguire tutte le normali attività di un operatore per mantenere dello Stack di Azure in esecuzione. Questi compiti includono quanto segue:

- Aggiungere gli account utente (per [Azure Active Directory](azure-stack-add-new-user-aad.md) distribuzione o per [Active Directory Federation Services](azure-stack-add-users-adfs.md) distribuzione)
- [Assegnare i ruoli controllo degli accessi in base al ruolo di accesso](azure-stack-manage-permissions.md) (ciò non è limitato ai soli amministratori.)
- [Monitorare l'integrità dell'infrastruttura](azure-stack-monitor-health.md)
- Gestire [network](azure-stack-viewing-public-ip-address-consumption.md) e [archiviazione](azure-stack-manage-storage-accounts.md) risorse
- Sostituire l'hardware non valido, ad esempio [sostituire un disco guasto](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Ciò che gli utenti

È necessario informare gli utenti come usare i servizi in Azure Stack, come connettersi all'ambiente e come sottoscrivere le offerte. Oltre a qualsiasi documentazione personalizzata che è possibile assegnare agli utenti, è possibile indirizzare gli utenti per il sito della documentazione di utenti di Azure Stack.

**Informazioni su come lavorare con i servizi in Azure Stack**

Sono presenti informazioni che agli utenti è necessario comprendere prima di usare i servizi e crea App in Azure Stack. Ad esempio, esistono requisiti specifici della versione API e PowerShell. Inoltre, esistono alcuni valori differenziali funzionalità tra un servizio di Azure e il servizio equivalente in Azure Stack. Assicurarsi che gli utenti esaminare gli articoli seguenti:

- [Considerazioni principali: Uso dei servizi o creazione di App per Azure Stack](user/azure-stack-considerations.md)
- [Considerazioni per le macchine virtuali in Azure Stack](user/azure-stack-vm-considerations.md)
- [Archiviazione: le differenze e considerazioni](user/azure-stack-acs-differences.md)

Le informazioni contenute in questi articoli sono riepilogate le differenze tra un servizio di Azure e Azure Stack. Integrano le informazioni che sono disponibile per un servizio di Azure nella documentazione di Azure globale.

**Connettersi ad Azure Stack come utente**

In un ambiente di kit di sviluppo, se un utente non ha accesso al Desktop remoto all'host del kit di sviluppo, è necessario configurare una connessione di rete privata virtuale (VPN) prima di accedere a Azure Stack. Visualizzare [connettersi ad Azure Stack](azure-stack-connect-azure-stack.md). 

Gli utenti vorranno sapere come [accedere al portale utenti ](user/azure-stack-use-portal.md) o la modalità di connessione tramite PowerShell. In un ambiente di sistemi integrati, l'indirizzo del portale utente varia per ogni distribuzione. È necessario assegnare agli utenti con l'URL corretto.

Se si usa PowerShell, gli utenti potrebbero essere necessario registrare i provider di risorse prima di poter usare i servizi. (Un provider di risorse consente di gestire un servizio. Ad esempio, il provider di risorse di rete gestisce le risorse, ad esempio le reti virtuali, interfacce di rete e servizi di bilanciamento del carico.) Essi devono [installare](user/azure-stack-powershell-install.md) PowerShell [scaricare](user/azure-stack-powershell-download.md) altri moduli, e [configurare](user/azure-stack-powershell-configure-user.md) PowerShell (che include la registrazione dei provider di risorse).

**Sottoscrivere un'offerta**

Prima di un utente può accedere ai servizi, essi dovranno [sottoscrivere un'offerta](azure-stack-subscribe-plan-provision-vm.md) creato come un operatore.

## <a name="where-to-get-support"></a>Come ottenere supporto

### <a name="integrated-systems"></a>Sistemi integrati

Per un sistema integrato, sussiste un escalation coordinato e il processo di risoluzione tra Microsoft e partner hardware OEM (OEM).

Se si verifica un problema di servizi cloud, il supporto è disponibile tramite Microsoft dei clienti supporto tecnico clienti Microsoft. Se si sceglie l'icona della Guida e supporto (punto interrogativo) nell'angolo superiore destro del portale di amministratore e quindi fare clic su **nuova richiesta di supporto**, verrà visualizzato un sito in cui è possibile aprire direttamente una richiesta di supporto.

Se si verifica un problema con la distribuzione, patch e aggiornamento hardware (incluse unità sostituibili sul campo) e qualsiasi software e con marchio di hardware, ad esempio il software in esecuzione nell'host del ciclo di vita dell'hardware, contattare prima il fornitore dell'hardware OEM.

Per qualsiasi altra esigenza, contattare Microsoft CSS.

### <a name="development-kit"></a>Kit di sviluppo

Per il kit di sviluppo, è possibile porre domande relative al supporto nel [forum Microsoft](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Se si sceglie l'icona della Guida e supporto (punto interrogativo) nell'angolo superiore destro del portale di amministratore e quindi fare clic su **nuova richiesta di supporto**, verrà aperto il sito dei forum direttamente. Questi forum vengono regolarmente monitorati. Poiché il kit di sviluppo è un ambiente di valutazione, non è previsto alcun supporto ufficiale offerto tramite Microsoft CSS.

## <a name="next-steps"></a>Passaggi successivi

[Gestione area nello Stack di Azure](azure-stack-region-management.md)


