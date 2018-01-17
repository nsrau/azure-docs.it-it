---
title: Le decisioni di distribuzione disconnesso di Azure per lo Stack di Azure integrati sistemi | Documenti Microsoft
description: "Determinare le decisioni per le distribuzioni di Azure Stack Azure connesse a più nodi di pianificazione della distribuzione."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 8708f13109767c7cb9e4a1bf0d2797d7c4fb9a23
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Distribuzione di Azure disconnessa pianificazione decisioni per lo Stack di Azure integrato sistemi
Dopo aver deciso [integrerà come Stack di Azure nell'ambiente cloud ibrido](azure-stack-deployment-decisions.md), quindi è possibile finalizzare le decisioni relative alla distribuzione di Azure Stack.

Con l'opzione di distribuzione di Azure, è possibile distribuire e utilizzare Azure Stack senza una connessione a Internet. Tuttavia, con una distribuzione disconnessa, sono limitati a un archivio di identità AD FS e il modello di fatturazione della capacità. 

Scegliere questa opzione se si:
- Disporre di sicurezza o altre restrizioni che è necessario distribuire Azure Stack in un ambiente che non è connesso a Internet.
- Desidera bloccare dati (inclusi i dati di utilizzo) vengano inviate a Azure.
- Da utilizzare Azure Stack semplicemente come una soluzione di cloud privato viene distribuita alla Intranet aziendale e non è interessati in scenari ibridi.

> [!TIP]
> Inoltre, questo tipo di ambiente è definiti talvolta detta "scenario sottomarino".

Una distribuzione disconnessa non significa necessariamente che è possibile connettersi in un secondo momento l'istanza dello Stack di Azure in Azure per scenari VM tenant ibridi. Ciò significa che non si dispone di connettività in Azure durante la distribuzione o non si desidera usare Azure Active Directory come archivio di identità. Tuttavia, se si desidera disporre di connettività a Azure dopo la distribuzione, indipendentemente dal fatto ciò che si desidera utilizzare come archivio di identità, è necessario scegliere l'opzione di distribuzione di Azure Connetti a. 

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Funzionalità che sono compromesse o non disponibili nelle distribuzioni disconnesse 
Stack di Azure è stato progettato per la scelta ottimale quando è connesso ad Azure, pertanto è importante notare che esistono alcune caratteristiche e funzionalità che sono compromesse o completamente non disponibile in modalità disconnessa. 

|Funzionalità|Impatto in modalità disconnessa|
|-----|-----|
|Distribuzione di macchine Virtuali con estensione DSC per configurazione post-distribuzione di macchina virtuale|Compromessa - estensione DSC a Internet di ricerca più recente WMF.|
|Distribuzione di macchine Virtuali con estensione Docker per eseguire i comandi di Docker|Compromessa – Docker controllerà Internet per la versione più recente e questo controllo ha esito negativo.|
|Collegamenti alla documentazione relativa nel portale Azure Stack|Non disponibile-collegamenti come guida introduttiva di inviare un Feedback, Guida, e così via, che utilizzano un URL Internet non funzionerà.|
|Avviso monitoraggio e aggiornamento/mitigazione che fa riferimento a una Guida di monitoraggio e aggiornamento in linea|Non disponibile: qualsiasi avviso correzione collegamenti che utilizzano che un URL Internet non funzionerà.|
|Diffusione Marketplace: la possibilità di selezionare e aggiungere i pacchetti di raccolta direttamente da Azure Marketplace|Non disponibile: questa funzionalità richiede la connettività ad Azure e un account Azure Active Directory.|
|Utilizza gli account di Azure Active Directory federation per gestire una distribuzione di Azure Stack|Non disponibile: questa funzionalità richiede la connettività in Azure. AD FS con un'istanza locale di Active Directory devono essere utilizzate.|
|Provider di risorse, ad esempio SQL e WebApp|Non disponibile - provider di risorse, ad esempio SQL e WebApp richiedono l'accesso a Internet per il contenuto.|
|Interfaccia della riga di comando|Compromessa-CLI con funzionalità ridotte in termini di autenticazione e il provisioning dei principi di servizio.|
|Visual Studio: Cloud discovery|Cloud Discovery compromessa – rileverà sia cloud diverso o non funzionerà affatto.|
|Visual Studio: ADFS|Possibile – solo Visual Studio Enterprise supporta AD FS.
Telemetria|Non disponibile: dati di telemetria per lo Stack di Azure, come pure qualsiasi pacchetti di raccolta di terze parti che dipendono dai dati di telemetria.|
|Certificati|Non disponibile-connettività Internet è necessaria per i servizi di elenco di revoche di certificati (CRL) e del protocollo di stato certificato Online (OSCP) nel contesto di HTTPS.|
|Insieme di credenziali chiave|Un caso di utilizzo comune per l'insieme di credenziali chiave compromessa – è configurare un'applicazione di leggere i segreti in fase di esecuzione. Per questo l'applicazione richiede un'entità servizio nella directory. In Azure Active Directory, gli utenti normali (non amministratori) valore predefinito sono consentito aggiungere le entità servizio. In Active Directory (tramite ad FS) non sono. In tal modo un ostacolo l'esperienza end-to-end perché uno deve sempre passa attraverso un amministratore di directory per aggiungere le applicazioni.| 

## <a name="learn-more"></a>Altre informazioni
- Per informazioni sui casi di utilizzo, acquisto, partner e fornitori di hardware OEM, vedere il [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) pagina del prodotto.
- Per informazioni sulla Guida di orientamento e geografica disponibilità per lo Stack di Azure sistemi integrati, vedere il white paper: [dello Stack di Azure: un'estensione di Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Per ulteriori informazioni su Microsoft Azure Stack assemblaggio e prezzi [scaricare il PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

