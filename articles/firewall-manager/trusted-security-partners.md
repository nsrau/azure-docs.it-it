---
title: Che cosa sono i partner di sicurezza attendibili di Azure Firewall Manager (anteprima)What are Azure Firewall Manager trusted security partners (preview)
description: Informazioni sui partner di sicurezza attendibili di Azure Firewall ManagerLearn about Azure Firewall Manager trusted security partners
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436781"
---
# <a name="what-are-trusted-security-partners-preview"></a>Che cosa sono i partner di sicurezza affidabili (anteprima)?

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I partner di *sicurezza affidabili (anteprima)* in Azure Firewall Manager consentono di usare le offerte di sicurezza di terze parti di terze parti per proteggere l'accesso a Internet per gli utenti.

Con una configurazione rapida, è possibile proteggere un hub con un partner di sicurezza supportato e instradare e filtrare il traffico Internet dalle reti virtuali (VNet) o dalle succursali all'interno di un'area. Questa operazione viene eseguita utilizzando la gestione automatizzata dei percorsi, senza impostare e gestire le route definite dall'utente (UDR).

È possibile distribuire hub protetti configurati con il partner di sicurezza di propria scelta in più aree di Azure per ottenere connettività e sicurezza per gli utenti in tutto il mondo in tali aree. Con la possibilità di usare l'offerta del partner di sicurezza per il traffico delle applicazioni Internet/SaaS e Firewall di Azure per il traffico privato negli hub protetti, è ora possibile iniziare a creare il perimetro perimetrale della sicurezza in Azure vicino agli utenti e alle applicazioni distribuite a livello globale.

Per questa anteprima, i partner di sicurezza supportati sono i due sono **iboss** e **iboss**. Le aree supportate sono WestCentralUS, NorthCentralUS, WestUS, WestUS2 e EastUS.

![Partner di sicurezza affidabili](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Scenari chiave

È possibile utilizzare i partner di sicurezza per filtrare il traffico Internet nei seguenti scenari:

- Rete virtuale (VNet) a Internet

   Sfruttare la protezione Internet avanzata in grado di riconoscere l'utente per i carichi di lavoro su cloud in esecuzione in Azure.

- Diramazione a Internet

   Sfrutta la connettività di Azure e la distribuzione globale per aggiungere facilmente filtri NSaaS di terze parti per scenari da diramazione a Internet.Leverage your Azure connectivity and global distribution to easily add third-party NSaaS filtering for branch to Internet scenarios. È possibile creare la rete di transito globale e il perimetro di sicurezza usando la rete WAN virtuale di Azure.You can build your global transit network and security edge using Azure Virtual WAN.

Sono supportati gli scenari indicati di seguito:
-   da rete virtuale a Internet tramite un'offerta di partner di terze parti.
-   Succursale a Internet tramite un'offerta di partner di terze parti.
-   Succursale a Internet tramite un'offerta di partner di terze parti, il resto del traffico privato (Spoke-to-Spoke, Spoke-to-Branches, Branch-to-Spokes) tramite Firewall di Azure.

Lo scenario seguente non è supportato:The following scenario isn't supported:

- La rete virtuale a Internet tramite un'offerta di partner non può essere combinata con Firewall di Azure per il traffico privato. Vedere le seguenti limitazioni.

## <a name="current-limitations"></a>Limitazioni correnti

- Per la rete virtuale in Internet, non è possibile combinare l'aggiunta di Firewall di Azure per il traffico privato e un'offerta di partner per il traffico Internet.For VNet to Internet, you can't mix adding Azure Firewall for private traffic and a partner offering for Internet traffic. È possibile inviare traffico Internet a Firewall di Azure o a un partner di sicurezza di terze parti nell'hub virtuale protetto, ma non a entrambi. 
- È possibile distribuire al massimo un partner di sicurezza per ogni hub virtuale. Se è necessario modificare il provider, è necessario rimuovere il partner esistente e aggiungerne uno nuovo.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Procedure consigliate per il filtraggio del traffico Internet negli hub virtuali protettiBest practices for Internet traffic filtering in secured virtual hubs

Il traffico Internet include in genere il traffico Web. Ma include anche il traffico destinato alle applicazioni SaaS come Office 365 (O365) e i servizi Pubblici DiaS di Azure come Archiviazione di Azure, Azure Sql e così via. Di seguito sono riportati i consigli sulle procedure consigliate per la gestione del traffico verso questi servizi:The following are best practice recommendations for handling traffic to these services:

### <a name="handling-azure-paas-traffic"></a>Gestione del traffico di Azure PaaSHandling Azure PaaS traffic
 
- Usare Firewall di Azure per la protezione se il traffico è costituito principalmente da Azure PaaS e l'accesso alle risorse per le applicazioni può essere filtrato usando indirizzi IP, FQDN, tag di servizio o tag FQDN.

- Usare una soluzione partner di terze parti negli hub se il traffico è costituito dall'accesso alle applicazioni SaaS o se sono necessari filtri in grado di riconoscere l'utente (ad esempio, per i carichi di lavoro VDI (Virtual Desktop Infrastructure) oppure sono necessarie funzionalità avanzate di filtro Internet.

![Tutti gli scenari per Gestione firewall di AzureAll scenarios for Azure Firewall Manager](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Gestione del traffico di Office 365 (O365)

Negli scenari di sedi di succursale distribuite a livello globale, è necessario reindirizzare il traffico di Office 365 direttamente nella succursale prima di inviare il traffico Internet rimanente protetto dall'hub di Azure.In globally distributed branch location scenarios, you should redirect Office 365 traffic directly at the branch before sending the remaining Internet traffic your Azure secured hub.

Per Office 365, la latenza di rete e le prestazioni sono fondamentali per un'esperienza utente di successo. Per raggiungere questi obiettivi relativi alle prestazioni ottimali e all'esperienza utente, i clienti devono implementare l'escape diretto e locale di Office 365 prima di considerare il routing del resto del traffico Internet tramite Azure.To achieve these goals around optimal performance and user experience, customers must implement Office 365 direct and local escape before consider routing the rest of Internet traffic through Azure.

I principi di connettività di rete di [Office 365](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) richiedono che le connessioni di rete chiave di Office 365 vengano instradate localmente dalla filiale utente o dal dispositivo mobile e direttamente tramite Internet nel punto di presenza Microsoft più vicino.

Inoltre, le connessioni di Office 365 sono fortemente crittografate per la privacy e utilizzano protocolli proprietari efficienti per motivi di prestazioni. Ciò rende poco pratico e incisivo sottoporre tali connessioni alle tradizionali soluzioni di sicurezza a livello di rete. Per questi motivi è consigliabile che i clienti inviino il traffico di Office 365 direttamente dalle filiali, prima di inviare il resto del traffico tramite Azure.For these reasons we strongly recommend that customers send Office 365 traffic directly from branches, before sending rest of the traffic through Azure. Microsoft ha collaborato con diversi provider di soluzioni SD-WAN, che si integrano con Azure e Office 365 e semplificano l'abilitazione dei gruppi di lavoro Internet diretti e locali di Office 365. Per informazioni dettagliate, vedere [Come si impostano i criteri di O365 tramite la rete WAN virtuale?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>Passaggi successivi

[Distribuire un'offerta di sicurezza attendibile in un hub protetto usando Gestione firewall di Azure.Deploy a trusted security offering in a secured hub, using Azure Firewall Manager](deploy-trusted-security-partner.md).
