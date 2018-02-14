---
title: Nozioni fondamentali di amministrazione Azure Stack | Documenti Microsoft
description: Informazioni su cosa occorre per amministrare Azure Stack.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: fa77faac195de3be7bf7b2785eb589b030a6e6ce
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-stack-administration-basics"></a>Nozioni fondamentali di amministrazione Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Ci sono vari aspetti, che è necessario sapere se si ha familiarità con amministrazione dello Stack di Azure. Questa guida fornisce una panoramica del ruolo operatore di Stack di Azure e cosa è necessario indicare agli utenti per loro di diventare rapidamente produttivi.

## <a name="understand-the-builds"></a>Comprendere le compilazioni

### <a name="integrated-systems"></a>Sistemi integrati

Se si utilizza un sistema integrato dello Stack di Azure, le versioni aggiornate dello Stack di Azure vengono distribuite tramite pacchetti di aggiornamento. È possibile importare i pacchetti e applicarli usando il riquadro di aggiornamenti nel portale di amministrazione.
 
### <a name="development-kit"></a>Kit di sviluppo

Se si utilizza il Kit di sviluppo dello Stack di Azure, esaminare il [che cos'è Azure Stack?](azure-stack-poc.md) articolo per assicurarsi comprendere lo scopo del kit di sviluppo e le relative limitazioni. È necessario utilizzare il kit di sviluppo come una "sandbox", in cui è possibile valutare Stack di Azure e sviluppare e testare le App in un ambiente non di produzione. (Per informazioni sulla distribuzione, vedere il [distribuzione Azure Stack Development Kit](azure-stack-deploy-overview.md) Guida introduttiva.)

Come Azure, è innovazione rapidamente. Si verrà regolarmente nuovi le build di rilascio. Se si esegue il kit di sviluppo e si desidera spostare la build più recente, è necessario [ridistribuire Azure Stack](azure-stack-redeploy.md). Non è possibile applicare i pacchetti di aggiornamento. Questo processo richiede tempo, ma il vantaggio è che è possibile provare le funzionalità più recenti. La documentazione del kit di sviluppo del sito riflette la build di versione più recente.

## <a name="learn-about-available-services"></a>Informazioni sui servizi disponibili

È necessario un riconoscimento di servizi che è possibile rendere disponibili agli utenti. Stack di Azure supporta un sottoinsieme di servizi di Azure. L'elenco di servizi supportati continua a evolvere.

**Servizi fondamentali**

Per impostazione predefinita, Azure Stack include "servizi di base" seguenti quando si distribuisce Azure Stack:

- Calcolo
- Archiviazione
- Rete
- Insieme di credenziali di chiave

Con questi servizi fondamentali, è possibile offrire Infrastructure-as-a-Service (IaaS) per gli utenti con la configurazione minima.

**Servizi aggiuntivi**

Attualmente, sono supportate i servizi Platform-as-a-Service (PaaS) aggiuntivi seguenti:

- Servizio app
- Funzioni di Azure
- Database MySQL e SQL Server

Questi servizi richiedono un'ulteriore configurazione prima di renderli disponibili agli utenti. Per ulteriori informazioni, vedere le esercitazioni"" e le sezioni "guides\Offer procedure servizi" della documentazione operatore dello Stack di Azure.

**Guida di orientamento per servizio**

Stack Azure continuerà aggiungere supporto per servizi di Azure. Per la Guida di orientamento previsto, vedere il [dello Stack di Azure: un'estensione di Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) white paper. È inoltre possibile monitorare il [post di blog di Azure Stack](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) per gli annunci di nuove.

## <a name="what-tools-do-i-use-to-manage"></a>Quali strumenti utilizzare per gestire?
 
È possibile utilizzare il [portale dell'amministratore](azure-stack-manage-portals.md) o PowerShell per gestire Azure Stack. Il modo più semplice per apprendere i concetti di base è tramite il portale. Se si desidera usare PowerShell, sono presenti operazioni di preparazione. È necessario [installare](azure-stack-powershell-install.md) PowerShell [scaricare](azure-stack-powershell-download.md) moduli aggiuntivi, e [configurare](azure-stack-powershell-configure-admin.md) PowerShell.

Stack di Azure Usa Gestione risorse di Azure come meccanismo di distribuzione, gestione e organizzazione sottostante. Se prevede di gestire Azure Stack e supportare gli utenti, è necessario informazioni su Gestione risorse. Vedere il [introduzione con Azure Resource Manager](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) white paper.

## <a name="your-typical-responsibilities"></a>Responsabilità dell'utente tipico

Gli utenti desiderano utilizzare i servizi. Prospettiva, il ruolo principale consiste nel rendere disponibili questi servizi. È necessario scegliere i servizi per offrire e rendere disponibili tali servizi mediante la creazione di piani, offerte e le quote. Per ulteriori informazioni, vedere [panoramica dell'offerta di servizi di Azure Stack](azure-stack-offer-services-overview.md). 

È necessario anche aggiungere elementi a [marketplace](azure-stack-marketplace.md), ad esempio le immagini di macchina virtuale. Il modo più semplice è [scaricare elementi del marketplace da Azure a Azure Stack](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Se si desidera testare i piani, offerte e servizi, è necessario utilizzare il [portale per gli utenti](azure-stack-manage-portals.md); non è stato il portale dell'amministratore.

Oltre a fornire servizi, è necessario eseguire tutte le normali attività di un operatore per mantenere Stack Azure attivo e in esecuzione. Queste funzioni includono:

- Aggiungere gli account utente (per [Azure Active Directory](azure-stack-add-new-user-aad.md) distribuzione o per [Active Directory Federation Services](azure-stack-add-users-adfs.md) distribuzione)
- [Assegnare l'accesso basato sui ruoli ruoli (RBAC) controllo](azure-stack-manage-permissions.md) (ciò non è limitato ai soli amministratori).
- [Integrità dell'infrastruttura di monitoraggio](azure-stack-monitor-health.md)
- Gestire [rete](azure-stack-viewing-public-ip-address-consumption.md) e [archiviazione](azure-stack-manage-storage-accounts.md) risorse
- Sostituire un componente hardware danneggiato, ad esempio [sostituire un disco guasto](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Informazioni per gli utenti

È necessario informare gli utenti su come lavorare con servizi nello Stack di Azure, come connettersi all'ambiente e come effettuare la sottoscrizione. Oltre a qualsiasi documentazione personalizzati che si desideri fornire agli utenti, è possibile indirizzare gli utenti per il sito della documentazione di utenti di Azure dello Stack.

**Comprensione del funzionamento con servizi nello Stack di Azure**

Le informazioni che degli utenti è necessario comprendere prima di utilizzare i servizi e compilare l'App nello Stack di Azure sono. Ad esempio, esistono requisiti di versione di PowerShell e API specifici. Inoltre, esistono alcuni delta di funzionalità tra un servizio in Azure e il servizio equivalente nello Stack di Azure. Assicurarsi che gli utenti, esaminare gli articoli seguenti:

- [Considerazioni sulla chiave: utilizzo di servizi o creazione di applicazioni per Azure Stack](user/azure-stack-considerations.md)
- [Considerazioni per le macchine virtuali in Azure Stack](user/azure-stack-vm-considerations.md)
- [Archiviazione: considerazioni e le differenze](user/azure-stack-acs-differences.md)

Le informazioni contenute in questi articoli sono riepilogate le differenze tra un servizio in Azure e Azure Stack. Integra le informazioni che sono disponibile per un servizio di Azure nella documentazione di Azure globale.

**Connettersi allo Stack di Azure come un utente**

In un ambiente di kit di sviluppo, se un utente non dispone dell'accesso Desktop remoto all'host del kit di sviluppo, è necessario configurare una connessione di rete privata virtuale (VPN) prima di poter accedere dello Stack di Azure. Vedere [connettersi allo Stack Azure](azure-stack-connect-azure-stack.md). 

Gli utenti dovranno sapere come [accedere al portale utenti ](user/azure-stack-use-portal.md) o come connettersi tramite PowerShell. In un ambiente integrato di sistemi, l'indirizzo di portale utente varia per ogni distribuzione. È necessario fornire agli utenti con l'URL corretto.

Se si usa PowerShell, gli utenti potrebbero essere necessario registrare i provider di risorse prima di poter utilizzare i servizi. (Un provider di risorse consente di gestire un servizio. Ad esempio, il provider di risorse di rete gestisce le risorse, ad esempio le reti virtuali, le interfacce di rete e servizi di bilanciamento del carico.) Devono essere [installare](user/azure-stack-powershell-install.md) PowerShell [scaricare](user/azure-stack-powershell-download.md) moduli aggiuntivi, e [configurare](user/azure-stack-powershell-configure-user.md) PowerShell (che include la registrazione dei provider di risorse).

**Sottoscrivere un'offerta**

Prima di un utente può accedere ai servizi, essi devono [sottoscrivere un'offerta](azure-stack-subscribe-plan-provision-vm.md) creati come operatore.

## <a name="where-to-get-support"></a>Come ottenere supporto

### <a name="integrated-systems"></a>Sistemi integrati

Per un sistema integrato, è disponibile un processo di risoluzione tra Microsoft e i partner hardware (OEM) original equipment manufacturer e di escalation coordinato.

Se si verifica un problema di servizi cloud, il supporto è disponibile tramite Microsoft cliente il supporto tecnico clienti Microsoft. Se fai clic sull'icona della Guida e supporto (punto interrogativo) nell'angolo superiore sinistro del portale di amministrazione e quindi fare clic su **nuova richiesta di assistenza**, si apre un sito in cui è possibile aprire direttamente una richiesta di supporto.

Se si verifica un problema con la distribuzione, patch e aggiornamento hardware (tra cui unità sostituibili sul campo) e qualsiasi software marchio hardware, ad esempio un software in esecuzione nell'host del ciclo di vita di hardware, contattare innanzitutto il fornitore dell'hardware OEM.

Per altri scopi, contattare Microsoft CSS.

### <a name="development-kit"></a>Kit di sviluppo

Per il kit di sviluppo, è possibile porre domande relative al supporto di [forum Microsoft](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Se fai clic sull'icona della Guida e supporto (punto interrogativo) nell'angolo superiore sinistro del portale di amministrazione e quindi fare clic su **nuova richiesta di assistenza**, si apre il sito di forum direttamente. Questi forum regolarmente vengono monitorati. Poiché il kit di sviluppo è un ambiente di valutazione, non è previsto alcun supporto ufficiale di Microsoft CSS.

## <a name="next-steps"></a>Passaggi successivi

- [Gestione area nello Stack di Azure](azure-stack-region-management.md)


