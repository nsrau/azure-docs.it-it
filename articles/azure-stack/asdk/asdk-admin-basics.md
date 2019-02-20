---
title: Nozioni di base di Azure Stack Development Kit | Microsoft Docs
description: Viene descritto come eseguire attività di amministrazione di base per Azure Stack Development Kit (ASDK).
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
ms.date: 02/19/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 9a07a829aac9659ac7ab8d04b64a1ea1a9a2de78
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428058"
---
# <a name="asdk-administration-basics"></a>Nozioni fondamentali sull'amministrazione ASDK 
Esistono diversi aspetti da sapere se si ha familiarità con amministrazione di Azure Stack Development Kit (ASDK). Questa guida viene fornita una panoramica del ruolo come operatore di Azure Stack nell'ambiente di valutazione e come garantire agli utenti di test può diventare rapidamente produttivi.

In primo luogo, è necessario rivedere le [che cos'è Azure Stack Development Kit?](asdk-what-is.md) articolo per assicurarsi di comprendere lo scopo del ASDK e le relative limitazioni. È necessario utilizzare il kit di sviluppo come una "sandbox", in cui è possibile valutare Azure Stack per sviluppare e testare le App in un ambiente non di produzione. 

Come Azure, Azure Stack innova rapidamente in modo che saranno rilasciate nuove build del ASDK regolarmente. Tuttavia, non è possibile aggiornare il ASDK come è possibile le distribuzioni di sistemi integrati di Azure Stack. Pertanto, se si desidera spostare la build più recente, è necessario completamente [ridistribuire il ASDK](asdk-redeploy.md). Non è possibile applicare i pacchetti di aggiornamento. Questo processo richiede tempo, ma il vantaggio è che è possibile provare le funzionalità più recenti, non appena diventano disponibili. 

## <a name="what-account-should-i-use"></a>Quale account si deve usare?
Esistono alcune considerazioni sull'account che è necessario tenere presenti durante la gestione di Azure Stack. In particolare nelle distribuzioni usando Windows Server Active Directory Federation Services (ADFS) come provider di identità anziché Azure Active Directory (Azure AD). Le considerazioni sull'account seguente si applicano a entrambi i sistemi integrati di Azure Stack e ASDK distribuzioni:

|Account|Azure AD|AD FS|
|-----|-----|-----|
|Amministratore locale (. \Administrator)|Amministratore di host ASDK|Amministratore di host ASDK|
|AzureStack\AzureStackAdmin|Amministratore di host ASDK<br><br>Può essere utilizzato per accedere al portale di amministrazione di Azure Stack<br><br>Accesso per visualizzare e amministrare gli anelli di Service Fabric|Amministratore di host ASDK<br><br>Nessun accesso al portale di amministrazione di Azure Stack<br><br>Accesso per visualizzare e amministrare gli anelli di Service Fabric<br><br>Non è più proprietario del Provider di sottoscrizione predefinite (DPS)|
|AzureStack\CloudAdmin|Può accedere ed eseguire i comandi consentiti all'interno dell'Endpoint con privilegi|Può accedere ed eseguire i comandi consentiti all'interno dell'Endpoint con privilegi<br><br>Non è possibile accedere all'host ASDK<br><br>Proprietario della sottoscrizione del Provider predefinito (DPS)|
|Amministratore globale di Azure AD|Usato durante l'installazione<br><br>Proprietario della sottoscrizione del Provider predefinito (DPS)|Non applicabile|
|

## <a name="what-tools-do-i-use-to-manage"></a>Quali strumenti usare per la gestione?
È possibile usare la [portale di Azure Stack amministratore](https://adminportal.local.azurestack.external) o Azure PowerShell per gestire Azure Stack. Il modo più semplice per apprendere i concetti di base è tramite il portale. Se si desidera usare PowerShell, è necessario installare [PowerShell per Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) e [scaricare gli strumenti di Azure Stack da GitHub](asdk-post-deploy.md#download-the-azure-stack-tools).

Stack di Azure Usa Azure Resource Manager come relativo meccanismo di distribuzione, gestione e organizzazione sottostante. Se si intende gestire Azure Stack e consentire agli utenti del supporto, è consigliabile informazioni su Azure Resource Manager. Altre informazioni, vedere la [Guida introduttiva a white paper su Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Le responsabilità tipiche del cliente
Gli utenti vogliono usare i servizi. Dal loro punto di vista, il ruolo principale consiste nel rendere disponibili questi servizi. Usando il ASDK, è possibile ottenere informazioni quali servizi offerti e come rendere i servizi disponibili in base [creazione di piani, offerte e quote](asdk-offer-services.md). È anche necessario aggiungere elementi al marketplace, ad esempio le immagini di macchina virtuale. Il modo più semplice consiste [download di elementi di marketplace](asdk-marketplace-item.md) da Azure ad Azure Stack.

> [!NOTE]
> Se si desidera testare i piani, offerte e i servizi, è consigliabile usare la [portale per gli utenti](https://portal.local.azurestack.external); non il [portale di amministrazione](https://adminportal.local.azurestack.external).

Oltre a fornire servizi, è necessario eseguire tutte le normali attività di un operatore di Azure Stack per mantenere la ASDK attivo e in esecuzione. Questi compiti includono operazioni come la seguente:
- Aggiungere account utente per le distribuzioni di Active Directory Federation Services (ADFS) o Azure Active Directory (Azure AD).
- Assegnare l'accesso basato sui ruoli ruoli controllo degli accessi (ciò non è limitato solo agli amministratori)
- Monitorare l'integrità dell'infrastruttura
- Gestire le risorse di rete e archiviazione
- Sostituire l'hardware del computer host non riuscite development kit 

## <a name="where-to-get-support"></a>Come ottenere supporto
Per il kit di sviluppo, è l'unica opzione di supporto per porre domande relative al supporto nel [forum di Microsoft Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Se si sceglie l'icona della Guida e supporto (punto interrogativo) nell'angolo superiore destro del portale di amministratore e quindi fare clic su **nuova richiesta di supporto**, verrà aperto il sito dei forum direttamente. Questi forum vengono regolarmente monitorati. 

> [!IMPORTANT]
> Poiché il ASDK è un ambiente di valutazione, non è previsto alcun supporto ufficiale offerto tramite Microsoft dei clienti supporto tecnico clienti Microsoft.

## <a name="next-steps"></a>Passaggi successivi
[Distribuire il ASDK](asdk-install.md)

