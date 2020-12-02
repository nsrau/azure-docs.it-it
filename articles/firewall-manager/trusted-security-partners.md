---
title: Che cosa sono i provider del partner di sicurezza di Azure Firewall Manager?
description: Informazioni sui provider di partner di sicurezza di Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: victorh
ms.openlocfilehash: 923c6b685d20ff68788e7d9cfcb45ebaecb535e3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490109"
---
# <a name="what-are-security-partner-providers"></a>Che cosa sono i provider del partner di sicurezza?

I *provider di partner di sicurezza* in gestione firewall di Azure consentono di usare le proprie offerte di sicurezza come servizio (secar) di terze parti per proteggere l'accesso a Internet per gli utenti.

Con una configurazione rapida, è possibile proteggere un hub con un partner di sicurezza supportato e indirizzare e filtrare il traffico Internet dalle reti virtuali (reti virtuali) o dalle sedi delle succursali all'interno di un'area. Questa operazione può essere eseguita con la gestione automatica delle route senza configurare e gestire route definite dall'utente (UDR).

Puoi distribuire Hub protetti configurati con il partner di sicurezza che preferisci in più aree di Azure per ottenere connettività e sicurezza per gli utenti in qualsiasi punto del mondo in tali aree. Grazie alla possibilità di usare l'offerta del partner di sicurezza per il traffico delle applicazioni Internet/SaaS e il firewall di Azure per il traffico privato negli hub protetti, ora puoi iniziare a creare il tuo perimetro di sicurezza in Azure, che è vicino a utenti e applicazioni distribuiti a livello globale.

I partner di sicurezza supportati sono **zScaler**, **[Check Point](check-point-overview.md)** e **iboss**.

![Provider del partner di sicurezza](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Scenari principali

È possibile usare i partner di sicurezza per filtrare il traffico Internet negli scenari seguenti:

- Da rete virtuale (VNet) a Internet

   Sfruttare la protezione Internet avanzata in grado di riconoscere l'utente per i carichi di lavoro su cloud in esecuzione in Azure.

- Da ramo a Internet

   Sfrutta la tua connettività di Azure e la tua distribuzione globale per aggiungere con facilità il filtro NSaaS di terze parti per gli scenari da ramo a Internet. È possibile creare la rete di transito globale e il perimetro di sicurezza usando la rete WAN virtuale di Azure.

Sono supportati gli scenari indicati di seguito:
- VNet/ramo a Internet tramite un provider del partner di sicurezza e l'altro traffico (spoke to spoke, spoke to Branch, branch to spoke) tramite il firewall di Azure.
- VNet/ramo a Internet tramite il provider del partner di sicurezza

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Procedure consigliate per il filtraggio del traffico Internet in hub virtuali protetti

Il traffico Internet include in genere il traffico Web. Include anche il traffico destinato ad applicazioni SaaS come Microsoft 365 e servizi PaaS pubblici di Azure, ad esempio archiviazione di Azure, Azure SQL e così via. Di seguito sono illustrate le procedure consigliate per la gestione del traffico a questi servizi:

### <a name="handling-azure-paas-traffic"></a>Gestione del traffico PaaS di Azure
 
- Usare il firewall di Azure per la protezione se il traffico è costituito principalmente da Azure PaaS e l'accesso alle risorse per le applicazioni può essere filtrato usando indirizzi IP, nomi di dominio completi, tag di servizio o tag FQDN.

- Usare una soluzione di partner di terze parti negli hub se il traffico è costituito da accesso alle applicazioni SaaS o se è necessario un filtro compatibile con l'utente (ad esempio per i carichi di lavoro dell'infrastruttura VDI) oppure sono necessarie funzionalità di filtro Internet avanzate.

![Tutti gli scenari per gestione firewall di Azure](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-microsoft-365-traffic"></a>Gestione del traffico Microsoft 365

Negli scenari di distribuzione di Branch distribuiti a livello globale, è consigliabile reindirizzare il traffico Microsoft 365 direttamente al ramo prima di inviare il traffico Internet rimanente nell'hub protetto di Azure.

Per Microsoft 365, la latenza di rete e le prestazioni sono fondamentali per un'esperienza utente corretta. Per raggiungere questi obiettivi in termini di prestazioni ottimali e di esperienza utente, i clienti devono implementare Microsoft 365 Escape diretto e locale prima di prendere in considerazione il routing del resto del traffico Internet tramite Azure.

[Microsoft 365 principi di connettività di rete](/microsoft-365/enterprise/microsoft-365-network-connectivity-principles) chiamano per la chiave Microsoft 365 le connessioni di rete devono essere instradate localmente dal ramo utente o dal dispositivo mobile e direttamente tramite Internet nel più vicino punto di presenza di rete Microsoft.

Inoltre, le connessioni Microsoft 365 vengono crittografate per la privacy e utilizzano protocolli proprietari efficienti per motivi di prestazioni. In questo modo è impraticabile che influisca negativamente sulle connessioni alle tradizionali soluzioni di sicurezza a livello di rete. Per questi motivi, è consigliabile che i clienti invii Microsoft 365 il traffico direttamente dai rami, prima di inviare il resto del traffico tramite Azure. Microsoft ha collaborato con diversi provider di soluzioni SD-WAN, che si integrano con Azure e Microsoft 365 e rendono più semplice per i clienti l'abilitazione di Microsoft 365 breakout Internet diretto e locale. Per informazioni dettagliate, vedere [che cos'è la rete WAN virtuale di Azure?](../virtual-wan/virtual-wan-about.md)

## <a name="next-steps"></a>Passaggi successivi

[Distribuire un'offerta partner di sicurezza in un hub protetto usando gestione firewall di Azure](deploy-trusted-security-partner.md).
