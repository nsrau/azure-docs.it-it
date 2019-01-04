---
title: Bordo considerazioni relative all'integrazione di rete di connettività per i sistemi integrati di Azure Stack | Microsoft Docs
description: Informazioni su cosa è possibile fare per pianificare la connettività di rete del bordo Data Center con Azure Stack con più nodi.
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
ms.date: 08/30/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 12219e2df875d317aece73cabebdfb55115f7b41
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021085"
---
# <a name="border-connectivity"></a>Connettività del bordo 
Pianificazione dell'integrazione di rete è un prerequisito fondamentale per la corretta distribuzione di sistemi integrati di Azure Stack, l'operazione e gestione. Pianificazione della connettività di bordo inizia con la scelta di usare il routing dinamico con bordo BGP gateway protocol () o meno. Questo richiede l'assegnazione di un numero sistema autonomo BGP 16 bit (pubblico o privato) o Usa il routing statico, in cui una route predefinita statico viene assegnata ai dispositivi del bordo.

> [!IMPORTANT]
> Parte superiore di opzioni di rack (TOR) richiedono uplink Layer 3 con gli indirizzi IP da punto a punto (/ 30 reti) configurato nelle interfacce fisiche. Non è supportata per l'uso di livello 2 uplink con commutatori TOR che supportano le operazioni di Azure Stack. 

## <a name="bgp-routing"></a>Routing BGP
Usando un protocollo di routing dinamico, ad esempio BGP garantisce che il sistema è sempre a conoscenza delle modifiche alla rete e facilita l'amministrazione. Per la sicurezza avanzate sono contraddistinte dal, una password può essere impostata su BGP peering tra il bordo e il commutatore TOR. 

Come illustrato nel diagramma seguente, annuncio dell'indirizzo IP privato il spazio nel commutatore TOR è bloccato tramite un elenco di prefissi. L'elenco di prefissi Nega l'annuncio della rete privata e viene applicata come una mappa di route per la connessione tra il bordo e il commutatore TOR.

Il servizio di bilanciamento di carico Software (SLB) in esecuzione all'interno della soluzione di Azure Stack peer per i dispositivi TOR in modo che è possibile segnalare in modo dinamico gli indirizzi VIP.

Per assicurarsi che il traffico utente in modo immediato e in modo trasparente viene ripristinato da un errore, il VPC o MLAG configurata tra i dispositivi TOR consente l'uso del collegamento di più chassis aggregazione di indirizzi e HSRP e VRRP che fornisce la ridondanza per le reti IP di rete.

![Routing BGP](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>Routing statico
Routing statico richiede configurazioni aggiuntive per i dispositivi del bordo. Richiede ulteriori interventi manuali e gestione, nonché analisi approfondita prima di eventuali modifiche e i problemi causati da un errore di configurazione possono richiedere più tempo per eseguire il rollback a seconda delle modifiche apportate. Non è il metodo di routing consigliato, ma è supportato.

Per integrare Azure Stack nell'ambiente di rete con routing statico, devono essere connesso tutti i quattro collegamenti fisici tra il bordo e il dispositivo TOR e la disponibilità elevata non può essere garantita a causa di funzionamento del routing come statico.

Il dispositivo del bordo deve essere configurato con route statiche che puntano ai dispositivi TOR P2P per il traffico destinato al *esterne* rete o gli indirizzi VIP pubblici e il *infrastruttura* rete. Sarà necessario specificare le route statiche per la *BMC* e il *esterno* reti per la distribuzione. Gli operatori è possono scegliere di lasciare route statiche nel bordo per accedere alle risorse di gestione che si trovano sul *BMC* rete. Aggiunta delle route statiche *commutatore infrastruttura* e *passare gestione* reti è facoltativo.

I dispositivi TOR dotati configurati una route statica predefinita l'invio di tutto il traffico per i dispositivi del bordo. L'unica eccezione il traffico per la regola predefinita è per lo spazio privato, questa viene bloccato per usare un elenco di controllo di accesso applicato il commutatore TOR alla connessione del bordo.

Routing statico valido solo per le porte uplink tra i commutatori TOR e bordo. Routing dinamico BGP viene utilizzato all'interno del rack perché non è uno strumento essenziale per il bilanciamento del carico software e altri componenti e può essere disabilitato o rimosso.

![Routing statico](media/azure-stack-border-connectivity/static-routing.png)

<sup>\*</sup> La rete BMC è facoltativa dopo la distribuzione.

<sup>\*\*</sup> La rete dell'infrastruttura di Switch è facoltativa, come l'intera rete può essere incluso nella rete di gestione del commutatore.

<sup>\*\*\*</sup> La rete di gestione del commutatore è necessaria e può essere aggiunte separatamente dalla rete dell'infrastruttura di Switch.

## <a name="transparent-proxy"></a>Proxy trasparente
Se il tuo Data Center richiede tutto il traffico usi un proxy, è necessario configurare un *proxy trasparente* per elaborare tutto il traffico da rack a gestirla in base ai criteri, che separa il traffico tra le zone nella rete.

> [!IMPORTANT]
> La soluzione di Azure Stack non supporta i proxy web normale.  

Un proxy trasparente (noto anche come un'intercettazione, inline o proxy forzato) intercetta le normali comunicazioni a livello di rete senza richiedere alcuna configurazione speciale client. I client non devono essere consapevoli dell'esistenza del proxy.

![Proxy trasparente](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Passaggi successivi
[Integrazione DNS](azure-stack-integrate-dns.md)
