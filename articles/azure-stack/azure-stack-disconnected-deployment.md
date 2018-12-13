---
title: I sistemi integrati di decisioni di distribuzione Azure disconnesso per Azure Stack | Microsoft Docs
description: Determinare le decisioni per le distribuzioni basate su Azure Stack di Azure a più nodi di pianificazione della distribuzione.
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
ms.date: 12/11/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 33512b47eff75421ce07b02f9c17ae3028152568
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276251"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>I sistemi integrati di Azure distribuzione disconnessa pianificazione decisioni per Azure Stack
Dopo aver deciso [modo in cui si integrerà Azure Stack nell'ambiente cloud ibrido](azure-stack-connection-models.md), è quindi possibile finalizzare le decisioni relative alla distribuzione di Azure Stack.

È possibile distribuire e usare Azure Stack senza una connessione a internet. Tuttavia, con una distribuzione disconnessa, si è limitati a un archivio identità di AD FS e il modello di fatturazione basato sulla capacità. Poiché la multi-tenancy richiede l'uso di Azure Active Directory, multi-tenancy non è supportato per le distribuzioni disconnesse. 

Scegliere questa opzione se si:
- Disporre di sicurezza o altre restrizioni che richiedono di distribuire Azure Stack in un ambiente non connesso a Internet.
- Vuole bloccare dati, inclusi i dati di utilizzo, che vengano inviati ad Azure.
- Da usare Azure Stack esclusivamente come una soluzione di cloud privato che viene distribuita per la rete Intranet aziendale e non è interessati in scenari ibridi.

> [!TIP]
> In alcuni casi, questo tipo di ambiente viene anche considerato uno scenario"sottomarino".

Una distribuzione disconnessa non significa necessariamente che è in un secondo momento non è possibile connettere l'istanza di Azure Stack in Azure per scenari di macchine Virtuali tenant ibrido. Ciò significa che non è disponibile connettività ad Azure durante la distribuzione o non si desidera usare Azure Active Directory come archivio di identità.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Funzionalità che sono compromesse o non disponibile nelle distribuzioni disconnesse 
Azure Stack è stato progettato per funzionare meglio quando si è connessi ad Azure, pertanto è importante notare che esistono alcune caratteristiche e funzionalità che sono compromesse o completamente non disponibile in modalità disconnessa. 

|Funzionalità|Impatto in modalità disconnessa|
|-----|-----|
|Distribuzione di macchine Virtuali con l'estensione DSC per configurare dopo la distribuzione della macchina virtuale|Con difficoltà - estensione DSC è simile a Internet per WMF più recente.|
|Distribuzione di macchine Virtuali con l'estensione Docker per eseguire i comandi di Docker|Compromessa – Docker controllerà Internet per la versione più recente e la verifica avrà esito negativo.|
|Collegamenti alla documentazione nel portale di Azure Stack|Non disponibile: collegamenti, ad esempio fornire commenti e suggerimenti, informazioni della Guida, Guida introduttiva, e così via, che usano un URL Internet non funzionerà.|
|Correzione o mitigazione degli avvisi che fa riferimento a una Guida di monitoraggio e aggiornamento in linea|Non disponibile: qualsiasi correzione dell'avviso collega che usano che un URL Internet non funzionerà.|
|Marketplace: la possibilità di selezionare e aggiungere i pacchetti di raccolta direttamente da Azure Marketplace|Con difficoltà: quando si distribuisce Azure Stack in modalità disconnessa (senza connettività di Internet), non è possibile scaricare elementi del marketplace tramite il portale di Azure Stack. Tuttavia, è possibile usare la [dello strumento di diffusione di marketplace](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) per scaricare gli elementi del marketplace in un computer con connettività internet e quindi di trasferirle all'ambiente Azure Stack.|
|Uso di account di Azure Active Directory federation per gestire una distribuzione di Azure Stack|Non disponibile: questa funzionalità richiede la connettività ad Azure. Invece è necessario utilizzare AD FS con un'istanza di Active Directory locale.|
|Servizi app|Vista - App Web può richiedere l'accesso a Internet per contenuto aggiornato.|
|Interfaccia della riga di comando|Con difficoltà – CLI offre funzionalità ridotte in termini di autenticazione e il provisioning delle entità servizio.|
|Visual Studio: Cloud discovery|Cloud Discovery con difficoltà – individueranno sia cloud diversi o non funzionerà affatto.|
|Visual Studio: AD FS|Con difficoltà – solo Visual Studio Enterprise supporta AD FS.
Telemetria|Non disponibile a causa dei dati di telemetria per Azure Stack come pacchetti oltre a una raccolta di terze parti che dipendono da dati di telemetria.|
|Certificati|Non disponibile a causa della connettività Internet è necessaria per i servizi di elenco di revoche di certificati (CRL) e del protocollo di stato di certificato Online (OSCP) nel contesto del protocollo HTTPS.|
|Key Vault|Compromessa – un caso d'uso comuni per Key Vault è per un'applicazione di leggere i segreti in fase di esecuzione. Per questa applicazione deve un'entità servizio nella directory. In Azure Active Directory, gli utenti normali (non amministratore) sono per impostazione predefinita è consentito aggiungere le entità servizio. In Active Directory (tramite ad FS) non sono. Ciò causa un ostacolo nell'esperienza end-to-end perché uno deve sempre passare attraverso un amministratore di directory per aggiungere tutte le applicazioni.| 

## <a name="learn-more"></a>Altre informazioni
- Per informazioni sui casi d'uso, acquisto, partner e fornitori di hardware OEM, vedere la [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) pagina del prodotto.
- Per informazioni sulla Guida di orientamento e la disponibilità geografica per Azure Stack i sistemi integrati, vedere il white paper: [Azure Stack: Un'estensione di Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Per altre informazioni sulla creazione di pacchetti e prezzi di Microsoft Azure Stack [Scarica il PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Passaggi successivi
[Integrazione della rete datacenter](azure-stack-network.md)