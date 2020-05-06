---
title: Che cos'è Anteprima di Gestione firewall di Azure?
description: Informazioni su Anteprima di Gestione firewall di Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 03/13/2020
ms.author: victorh
ms.openlocfilehash: 149782f627d586e927c828506a7d4f1b5437b987
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79366275"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Che cos'è Anteprima di Gestione firewall di Azure?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Anteprima di Gestione firewall di Azure è un servizio di gestione della sicurezza che fornisce funzionalità di gestione dei criteri di sicurezza e delle route per i perimetri di sicurezza basati sul cloud. 

Gestione firewall può garantire la gestione della sicurezza per due tipi di architettura di rete:

- **Hub virtuale protetto**

   Un [hub di rete WAN virtuale di Azure](../virtual-wan/virtual-wan-about.md#resources) è una risorsa gestita da Microsoft che consente di creare facilmente architetture hub-spoke. Quando i criteri di sicurezza e routing vengono associati a un hub di questo tipo, quest'ultimo viene definito *[hub virtuale protetto](secured-virtual-hub.md)* . 
- **Rete virtuale hub**

   È una rete virtuale di Azure standard creata e gestita dall'utente. Quando a un hub di questo tipo sono associati criteri di sicurezza, l'hub è definito *rete virtuale hub*. Attualmente sono supportati solo criteri firewall di Azure. È possibile eseguire il peering di reti virtuali spoke contenenti i servizi e i server del carico di lavoro, nonché gestire firewall in reti virtuali autonome non connesse tramite peering a spoke.

Per un confronto dettagliato delle architetture di tipo *hub virtuale protetto* e *rete virtuale hub*, vedere [Informazioni sulle opzioni disponibili per l'architettura di Gestione firewall di Azure](vhubs-and-vnets.md).

![firewall-manager](media/overview/firewallmanagerv5.png)

## <a name="azure-firewall-manager-preview-features"></a>Funzionalità di Anteprima di Gestione firewall di Azure

Anteprima di Gestione firewall di Azure offre le funzionalità seguenti:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Distribuzione e configurazione centrali di Firewall di Azure

È possibile distribuire e configurare in modo centralizzato più istanze di Firewall di Azure che si estendono in diverse aree e sottoscrizioni di Azure. 

### <a name="hierarchical-policies-global-and-local"></a>Criteri gerarchici (globali e locali)

È possibile usare Anteprima di Gestione firewall di Azure per gestire in modo centralizzato i criteri di Firewall di Azure tra più hub virtuali protetti. I team IT centrali possono creare criteri firewall globali per applicare i criteri firewall dell'organizzazione a tutti i team. I criteri firewall creati localmente consentono un modello self-service DevOps per una maggiore agilità.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrato con una soluzione di security come servizio di terze parti per una sicurezza avanzata

Oltre a Firewall di Azure, è possibile integrare i provider di terze parti per la security come servizio (SECaas) per fornire una protezione di rete aggiuntiva alle connessioni di rete virtuale e branch per Internet.

Questa funzionalità è disponibile solo con distribuzioni di tipo hub virtuale protetto.

- Filtro del traffico da VNet a Internet (V2I)

   - Filtrare il traffico di rete virtuale in uscita con provider di sicurezza di terze parti preferito.
   - Sfruttare la protezione Internet avanzata in grado di riconoscere l'utente per i carichi di lavoro su cloud in esecuzione in Azure.

- Filtro del traffico da branch a Internet (B2I)

   Sfruttare la connettività di Azure e la distribuzione globale per aggiungere con facilità il filtro di terze parti per gli scenari da branch a Internet.

Per altre informazioni su provider di sicurezza attendibili, vedere [Che cosa sono i partner di sicurezza attendibili di Gestione firewall di Azure (anteprima)?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Gestione centralizzata della route

È possibile instradare facilmente il traffico all'hub protetto per filtrare e accedere senza il bisogno di impostare route definite dall'utente alle reti virtuali spoke. 

Questa funzionalità è disponibile solo con distribuzioni di tipo hub virtuale protetto.

È possibile usare provider di terze parti per il filtro del traffico da branch a Internet (B2I), affiancato con Firewall di Azure per il filtro da branch a rete virtuale (B2V), da rete virtuale a rete virtuale (V2V) e da rete virtuale a Internet (V2I). È anche possibile usare provider di terze parti per filtrare il traffico V2I, purché Firewall di Azure non sia necessario per B2V o V2V. 

## <a name="region-availability"></a>Aree di disponibilità

I criteri firewall di Azure possono essere usati in tutte le aree. Ad esempio, è possibile creare un criterio all'interno degli Stati Uniti occidentali e usarlo negli Stati Uniti orientali. 

## <a name="known-issues"></a>Problemi noti

Anteprima di Gestione firewall di Azure presenta i problemi noti seguenti:

|Problema  |Descrizione  |Strategia di riduzione del rischio  |
|---------|---------|---------|
|Limitazioni relative al filtro di terze parti.|Il filtro del traffico V2I con provider di terze parti non è supportato con Firewall di Azure B2V e V2V.|Analisi in corso|
|La suddivisione del traffico non è attualmente supportata.|Office 365 e la suddivisione del traffico PaaS pubblico di Azure non sono supportati al momento. Di conseguenza, se si seleziona un provider di terze parti per V2I o B2I, vengono inviati tramite il servizio partner anche tutti i PaaS pubblici di Azure e il traffico di Office 365.|È attualmente in corso l'analisi della suddivisione del traffico nell'hub.
|Un unico hub virtuale protetto per area.|Non è possibile avere più hub virtuali protetti per area.|Creare più reti WAN virtuali in un'area.|
|I criteri di base devono trovarsi nella stessa area dei criteri locali.|Creare tutti i criteri locali nella stessa area dei criteri di base. È comunque possibile applicare un criterio creato in un'area in un hub protetto di un'altra area.|Analisi in corso|
|Comunicazione tra hub non funzionante con l'hub virtuale protetto|La comunicazione tra hub virtuali protetti non è ancora supportata.|Analisi in corso|
|Tutti gli hub virtuali protetti che condividono la stessa rete WAN virtuale devono risiedere nello stesso gruppo di risorse.|Questo comportamento è attualmente allineato agli hub di rete WAN virtuale.|Creare più reti WAN virtuali per consentire la creazione di hub virtuali protetti in gruppi di risorse diversi.|
|I gruppi IP non sono supportati nei criteri firewall.|I gruppi IP sono in anteprima pubblica e non sono attualmente supportati con regole del firewall tradizionali.|Correzione in corso.
|Le sottoscrizioni Cloud Solution Provider (CSP) non sono supportate.|Le [sottoscrizioni CSP](https://azure.microsoft.com/offers/ms-azr-0145p/) non sono attualmente supportate.|Analisi in corso

## <a name="next-steps"></a>Passaggi successivi

- Esaminare [Panoramica della distribuzione di Anteprima di Gestione firewall di Azure](deployment-overview.md)
- Informazioni sugli [hub virtuali protetti](secured-virtual-hub.md).