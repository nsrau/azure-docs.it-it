---
title: Che cosa sono i partner di sicurezza attendibili di Azure Firewall Manager (anteprima)
description: Informazioni sui partner di sicurezza attendibili di Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: cb8a2fdd14cfa7d361e3d78a64f3aaf60ea7676d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468567"
---
# <a name="what-are-trusted-security-partners-preview"></a>Che cosa sono i partner di sicurezza attendibili (anteprima)?

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Partner di sicurezza attendibili (anteprima)* in Azure Firewall Manager ti permette di usare le tue offerte di sicurezza come servizio (secar) di terze parti per proteggere l'accesso a Internet per gli utenti.

Con una configurazione rapida, è possibile proteggere un hub con un partner di sicurezza supportato e indirizzare e filtrare il traffico Internet dalle reti virtuali (reti virtuali) o dalle sedi delle succursali all'interno di un'area. Questa operazione viene eseguita tramite la gestione automatica delle route senza configurare e gestire route definite dall'utente (UDR).

Puoi distribuire Hub protetti configurati con il partner di sicurezza che preferisci in più aree di Azure per ottenere connettività e sicurezza per gli utenti in qualsiasi punto del mondo in tali aree. Grazie alla possibilità di usare l'offerta del partner di sicurezza per il traffico delle applicazioni Internet/SaaS e il firewall di Azure per il traffico privato negli hub protetti, è ora possibile iniziare a creare il proprio perimetro di sicurezza in Azure vicino agli utenti distribuiti a livello globale e applicazioni.

Per questa anteprima, i partner di sicurezza supportati sono **ZScaler** e **iboss**. Le aree supportate sono WestCentralUS, NorthCentralUS, Westus, WestUS2 e Eastus.

![Partner di sicurezza attendibili](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Scenari chiave

È possibile usare i partner di sicurezza per filtrare il traffico Internet negli scenari seguenti:

- Da rete virtuale (VNet) a Internet

   Sfrutta la protezione Internet avanzata in grado di riconoscere l'utente per i tuoi carichi di lavoro cloud in esecuzione in Azure.

- Da ramo a Internet

   Sfrutta la tua connettività di Azure e la tua distribuzione globale per aggiungere con facilità il filtro NSaaS di terze parti per gli scenari da ramo a Internet. È possibile creare la rete di transito globale e il perimetro di sicurezza usando la rete WAN virtuale di Azure.

Sono supportati gli scenari seguenti:
-   VNet a Internet tramite un'offerta partner di terze parti.
-   Diramazione a Internet tramite un'offerta partner di terze parti.
-   Diramazione a Internet tramite un'offerta di partner di terze parti, il resto del traffico privato (spoke-to-spoke, spoke-to-Branch, da ramo a spoke) tramite il firewall di Azure.

Lo scenario seguente non è supportato:

- VNet a Internet tramite un'offerta partner non può essere combinato con il firewall di Azure per il traffico privato. Vedere le limitazioni seguenti.

## <a name="current-limitations"></a>Limitazioni correnti

- Per VNet su Internet, non è possibile combinare l'aggiunta del firewall di Azure per il traffico privato e un'offerta partner per il traffico Internet. È possibile inviare traffico Internet al firewall di Azure o a un'offerta di partner di sicurezza di terze parti nell'hub virtuale protetto, ma non entrambi. 
- È possibile distribuire al massimo un partner di sicurezza per ogni hub virtuale. Se è necessario modificare il provider, è necessario rimuovere il partner esistente e aggiungerne uno nuovo.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Procedure consigliate per il filtraggio del traffico Internet in hub virtuali protetti

Il traffico Internet include in genere il traffico Web. Include anche il traffico destinato ad applicazioni SaaS come Office 365 (O365) e servizi PaaS pubblici di Azure, ad esempio archiviazione di Azure, Azure SQL e così via. Di seguito sono illustrate le procedure consigliate per la gestione del traffico a questi servizi:

### <a name="handling-azure-paas-traffic"></a>Gestione del traffico PaaS di Azure
 
- Usare il firewall di Azure per la protezione se il traffico è costituito principalmente da Azure PaaS e l'accesso alle risorse per le applicazioni può essere filtrato usando indirizzi IP, nomi di dominio completi, tag di servizio o tag FQDN.

- Usare una soluzione di partner di terze parti negli hub se il traffico è costituito da accesso alle applicazioni SaaS o se è necessario un filtro compatibile con l'utente (ad esempio per i carichi di lavoro dell'infrastruttura VDI) oppure sono necessarie funzionalità di filtro Internet avanzate.

![Tutti gli scenari per gestione firewall di Azure](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Gestione del traffico di Office 365 (O365)

Negli scenari di distribuzione di Branch distribuiti a livello globale, è consigliabile reindirizzare il traffico di Office 365 direttamente al ramo prima di inviare il traffico Internet rimanente nell'hub protetto di Azure.

Per Office 365, la latenza di rete e le prestazioni sono fondamentali per un'esperienza utente corretta. Per raggiungere questi obiettivi in termini di prestazioni ottimali e di esperienza utente, i clienti devono implementare Office 365 Direct e local escape prima di prendere in considerazione il routing del resto del traffico Internet tramite Azure.

I [principi di connettività di rete di office 365](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) chiamano per le connessioni di rete key Office 365 da indirizzare localmente dal ramo utente o dal dispositivo mobile e direttamente tramite Internet al punto di presenza più vicino di rete Microsoft.

Inoltre, le connessioni di Office 365 sono fortemente crittografate per la privacy e usano protocolli efficienti e proprietari per motivi di prestazioni. In questo modo è impraticabile che influisca negativamente sulle connessioni alle tradizionali soluzioni di sicurezza a livello di rete. Per questi motivi si consiglia vivamente ai clienti di inviare il traffico di Office 365 direttamente dai rami, prima di inviare il resto del traffico tramite Azure. Microsoft ha collaborato con diversi provider di soluzioni SD-WAN, che si integrano con Azure e Office 365 e semplificano la possibilità per i clienti di abilitare Office 365 Direct e Internet breakout locale. Per informazioni dettagliate, vedere [ricerca per categorie impostare i criteri O365 tramite la rete WAN virtuale?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview#how-do-i-set-my-o365-policies-via-virtual-wan)


## <a name="next-steps"></a>Passaggi successivi

[Distribuire un'offerta di sicurezza attendibile in un hub protetto usando gestione firewall di Azure](deploy-trusted-security-partner.md).
