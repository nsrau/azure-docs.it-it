---
title: Kit nozioni fondamentali sullo sviluppo di Azure Stack | Documenti Microsoft
description: Viene descritto come eseguire le attività di amministrazione di base per Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 05dd42b049c75b9ea592ffe341f44e3b02b9757f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="asdk-administration-basics"></a>Nozioni fondamentali di amministrazione ASDK 
Vi sono vari fattori, che è necessario sapere se si ha familiarità con amministrazione Azure Stack Development Kit (ASDK). Questa guida viene fornita una panoramica del ruolo come operatore dello Stack di Azure nell'ambiente di valutazione, e come assicurarsi che gli utenti di prova può diventare rapidamente produttivi.

In primo luogo, è necessario rivedere le [che cos'è Azure Stack Development Kit?](asdk-what-is.md) articolo per assicurarsi comprendere lo scopo del ASDK e le relative limitazioni. È necessario utilizzare il kit di sviluppo come una "sandbox", che consente di valutare Azure Stack per sviluppare e testare le App in un ambiente non di produzione. 

Come Azure, Azure Stack innova rapidamente in modo vengono regolarmente verranno rilasciate nuove build del ASDK. Tuttavia, non è possibile aggiornare il ASDK come le distribuzioni di sistemi Azure Stack integrato. In tal caso, se si desidera spostare la build più recente, è necessario completamente [ridistribuire il ASDK](asdk-redeploy.md). Non è possibile applicare i pacchetti di aggiornamento. Questo processo richiede tempo, ma il vantaggio è che è possibile provare le funzionalità più recenti non appena diventano disponibili. 

## <a name="what-account-should-i-use"></a>Quale account si deve usare?
Esistono alcune considerazioni sull'account che è necessario essere consapevoli di durante la gestione dello Stack di Azure. In particolare nelle distribuzioni con Windows Server Active Directory Federation Services (ADFS) come provider di identità invece di Azure Active Directory (Azure AD). Per i sistemi Azure Stack integrata e le distribuzioni ASDK si applicano le considerazioni sull'account seguente:

|Account|Azure AD|AD FS|
|-----|-----|-----|
|Amministratore locale (. \Administrator)|Amministratore di host ASDK|Amministratore di host ASDK|
|AzureStack\AzureStackAdmin|Amministratore di host ASDK<br><br>Può essere usato per accedere al portale di amministrazione di Azure Stack<br><br>Accesso per visualizzare e amministrare anelli di Service Fabric|Amministratore di host ASDK<br><br>Nessun accesso al portale di amministrazione di Azure Stack<br><br>Accesso per visualizzare e amministrare anelli di Service Fabric<br><br>Non è più proprietario del Provider di sottoscrizione predefinito (dp)|
|AzureStack\CloudAdmin|Può accedere ed eseguire i comandi consentiti all'interno dell'Endpoint con privilegi|Può accedere ed eseguire i comandi consentiti all'interno dell'Endpoint con privilegi<br><br>Non può accedere all'host ASDK<br><br>Proprietario della sottoscrizione del Provider predefinito (dp)|
|Amministratore globale di Azure AD|Utilizzato durante l'installazione<br><br>Proprietario della sottoscrizione del Provider predefinito (dp)|Non applicabile|
|

## <a name="what-tools-do-i-use-to-manage"></a>Quali strumenti utilizzare per gestire?
È possibile usare il [portale dell'amministratore di Stack Azure](https://adminportal.local.azurestack.external) o PowerShell per gestire Azure Stack. Il modo più semplice per apprendere i concetti di base è tramite il portale. Se si desidera usare PowerShell, è necessario installare [PowerShell per Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) e [scaricare gli strumenti di Azure Stack da GitHub](asdk-post-deploy.md#download-the-azure-stack-tools).

Stack di Azure Usa Gestione risorse di Azure come meccanismo di distribuzione, gestione e organizzazione sottostante. Se si intende gestire dello Stack di Azure e consentire agli utenti del supporto, è necessario informazioni su Gestione risorse di Azure. Per altre informazioni, leggere il [Getting Started with white paper Azure Resource Manager](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Responsabilità dell'utente tipico
Gli utenti desiderano utilizzare i servizi. Prospettiva, il ruolo principale consiste nel rendere disponibili questi servizi. Utilizza il ASDK, sarà possibile apprendere i servizi per offrire e come rendere i servizi disponibili in base [creazione di piani, offerte e le quote](asdk-offer-services.md). È inoltre necessario aggiungere gli elementi nel Marketplace, ad esempio le immagini di macchina virtuale. Il modo più semplice consiste [scaricare elementi del marketplace](asdk-marketplace-item.md) da Azure allo Stack di Azure.

> [!NOTE]
> Se si desidera testare i piani, offerte e servizi, è necessario utilizzare il [portale per gli utenti](https://portal.local.azurestack.external); non il [portale dell'amministratore](https://adminportal.local.azurestack.external).

Oltre a fornire servizi, è necessario eseguire tutte le normali attività di un operatore di Stack di Azure per mantenere il ASDK attivo e in esecuzione. Queste funzioni includono simile al seguente:
- Aggiungere gli account utente per Azure Active Directory (Azure AD) o le distribuzioni di Active Directory Federation Services (ADFS).
- Assegnare l'accesso basato su ruolo ruoli di controllo degli accessi (ciò non è consentito solo agli amministratori)
- Integrità dell'infrastruttura di monitoraggio
- Gestire le risorse di rete e archiviazione
- Sostituire l'hardware del computer host kit sviluppo non riuscita 

## <a name="where-to-get-support"></a>Come ottenere supporto
Per il kit di sviluppo, l'unica possibilità di supporto consiste nel porre domande nel supporto di [forum di Microsoft Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Se fai clic sull'icona della Guida e supporto (punto interrogativo) nell'angolo superiore sinistro del portale di amministrazione e quindi fare clic su **nuova richiesta di assistenza**, si apre il sito di forum direttamente. Questi forum regolarmente vengono monitorati. 

> [!IMPORTANT]
> Poiché il ASDK è un ambiente di valutazione, non sono supportate ufficiale offerti tramite Microsoft cliente il supporto tecnico clienti Microsoft.

## <a name="next-steps"></a>Passaggi successivi
[Distribuire il ASDK](asdk-deploy.md)

