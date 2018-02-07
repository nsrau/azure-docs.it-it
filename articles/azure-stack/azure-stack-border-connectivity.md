---
title: "Considerazioni relative all'integrazione di rete di connettività per i sistemi Azure Stack integrato il bordo | Documenti Microsoft"
description: "Informazioni sulle operazioni per la pianificazione dei Data Center bordo la connettività di rete con lo Stack di Azure a più nodi."
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
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 93dd609df90adac2c84ba8c62cf0d18f55a317bb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="border-connectivity"></a>Connettività di bordo 
Pianificazione dell'integrazione di rete è un importante requisito per la distribuzione di sistemi Azure Stack integrato, gestione e operazione ha esito positivo. Pianificazione della connettività bordo inizia scegliendo o meno di utilizzare il routing dinamico con il protocollo border gateway protocol (BGP). Questa operazione richiede l'assegnazione di un numero sistema autonomo BGP 16 bit (pubblico o privato) o con routing statico, in cui una route statica predefinita viene assegnata ai dispositivi di bordo.

> [!IMPORTANT]
> Nella parte superiore di commutatori rack (TOR) richiedono uplink Layer 3 con gli indirizzi IP Point to Point (/ 30 reti) configurato nelle interfacce fisiche. Non è supportata per l'utilizzo di porte uplink di livello 2 con commutatori TOR il supporto di operazioni di Stack di Azure. 

## <a name="bgp-routing"></a>Routing BGP
Utilizzando un protocollo di routing dinamico, ad esempio BGP garantisce che il sistema è sempre consapevole delle modifiche di rete e facilita l'amministrazione. 

Come illustrato nel diagramma seguente, pubblicità dell'indirizzo IP privato spazio nel commutatore TOR è limitato con un elenco di prefissi. Gli elenchi di prefisso Nega le subnet IP private e applicarlo come una mappa di route per la connessione tra TOR e il bordo.

Il servizio di bilanciamento di carico Software (SLB) in esecuzione all'interno della soluzione Azure Stack peer per i dispositivi TOR, pertanto è possibile pubblicizzare dinamicamente gli indirizzi VIP.

Per garantire che il traffico utente immediatamente e in modo trasparente ripristino in caso di errore, di Virtual PC o MLAG configurata tra i dispositivi TOR consente l'utilizzo di più chassis collegamento aggregazione per gli host e HSRP o VRRP che fornisce la ridondanza per le reti IP di rete.

![Routing BGP](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>routing statico
Utilizzo delle route statiche aggiunge la configurazione più predefinita per il bordo e i dispositivi TOR. Richiede analisi approfondita prima di qualsiasi modifica. I problemi causati da un errore di configurazione potrebbero richiedere più tempo per eseguire il rollback a seconda delle modifiche apportate. Non è il metodo di routing consigliato, ma è supportato.

Per integrare Azure Stack in ambiente di rete utilizzando questo metodo, il dispositivo di border deve essere configurato con route statiche che puntano ai dispositivi TOR per il traffico destinato alla rete esterna, VIP pubblici.

I dispositivi TOR devono essere configurati con una route statica predefinita, l'invio di tutto il traffico per i dispositivi di bordo. L'unica eccezione di traffico a questa regola è privato per il spazio verrà bloccato tramite un elenco di controllo di accesso applicate in TOR alla connessione di bordo.

Tutto il resto deve essere lo stesso come il primo metodo. Il routing dinamico del BGP verrà comunque utilizzato all'interno del rack perché non è uno strumento essenziale per la SLB e altri componenti e può essere disabilitato o rimosso.

![routing statico](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>Proxy trasparenti
Se il Data Center richiede tutto il traffico di usare un proxy, è necessario configurare un *proxy trasparente* elaborare tutto il traffico dal rack da gestire in base ai criteri, separare il traffico tra le aree della rete.

> [!IMPORTANT]
> La soluzione di Stack di Azure non supporta proxy web normale.  

Un proxy trasparente (noto anche come un'intercettazione inline o proxy forzato) intercetta le normali comunicazioni nel livello rete senza richiedere alcuna configurazione speciale client. I client non devono essere consapevoli dell'esistenza del proxy.

![Proxy trasparenti](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Passaggi successivi
[Integrazione di DNS](azure-stack-integrate-dns.md)