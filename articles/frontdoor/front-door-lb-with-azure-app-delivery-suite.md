---
title: 'Sportello anteriore di Azure: bilanciamento del carico con la suite di distribuzione delle applicazioni di Azure | Microsoft Docs'
description: Questo articolo illustra come Azure consiglia di bilanciare il carico con la relativa suite di distribuzione delle applicazioni
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 44af14a01e7b045b7abb6a84db89a67f3dd22445
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875283"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Bilanciamento del carico con la suite per il recapito di applicazioni di Azure

## <a name="introduction"></a>Introduzione
Microsoft Azure offre più servizi globali e a livello di area per la gestione della distribuzione del traffico di rete e del bilanciamento del carico: gestione traffico, sportello anteriore, gateway applicazione e Load Balancer.  In combinazione con l'architettura di zona e le numerose aree di Azure, l'uso di questi servizi permette di creare applicazioni affidabili, scalabili e con prestazioni elevate.

![Suite per il recapito di applicazioni ][1]
 
Questi servizi sono suddivisi in due categorie:
1. I **servizi globali di bilanciamento del carico** , ad esempio gestione traffico e sportello anteriore, distribuiscono il traffico dagli utenti finali nei backend internazionali, in cloud o anche nei servizi locali ibridi. Un servizio di bilanciamento del carico a livello globale instrada il traffico al back-end del servizio più vicino e reagisce ai cambiamenti di affidabilità o di prestazioni del servizio per mantenere prestazioni ottimali sempre disponibili per gli utenti. 
2. I **servizi di bilanciamento del carico a livello** di area, ad esempio Load Balancer standard o il gateway applicazione, offrono la possibilità di distribuire il traffico nelle reti virtuali (reti virtuali) tra le macchine virtuali (VM) o gli endpoint di servizio di zona all'interno di un'area.

La combinazione di servizi globali e internazionali nell'applicazione fornisce un modo affidabile, efficiente e sicuro end-to-end per instradare il traffico da e verso gli utenti ai servizi IaaS, PaaS o locali. Nella sezione successiva vengono descritti questi servizi.

## <a name="global-load-balancing"></a>Bilanciamento del carico globale
**Gestione traffico** offre il bilanciamento del carico DNS globale. Esamina le richieste DNS in ingresso e risponde con un back-end integro, in base ai criteri di routing selezionati dal cliente. Le opzioni per i metodi di routing sono le seguenti:
- Routing in base alle prestazioni, per reindirizzare il richiedente al back-end più vicino in termini di latenza.
- Routing prioritario per indirizzare tutto il traffico a un back-end, con altri back-end come backup.
- Routing round robin ponderato, che distribuisce il traffico in base alla ponderazione assegnata a ciascun back-end.
- Routing geografico, per garantire che i richiedenti ubicati in aree geografiche specifiche vengano indirizzati ai back-end mappati a tali aree (ad esempio, tutte le richieste dalla Spagna devono essere indirizzate all'area di Azure Francia centrale).
- Routing della subnet, che consente di mappare intervalli di indirizzi IP ai back-end, in modo che le richieste provenienti da tali indirizzi vengano inviate al back-end specificato (ad esempio, tutti gli utenti che si connettono dall'intervallo di indirizzi IP della sede centrale dell'azienda devono ottenere contenuti Web diversi rispetto a agli utenti generici).

Il client si connette direttamente al back-end. Gestione traffico di Azure rileva i back-end non integri e reindirizza i client a un'altra istanza integra. Per ulteriori informazioni sul servizio, vedere la documentazione di [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) .

Il front-end di **Azure** fornisce accelerazione sito Web dinamica (DSA), incluso il bilanciamento del carico http globale.  Analizza le richieste HTTP in ingresso e le instrada all'area/back-end del servizio più vicino per il nome host specificato, il percorso URL e le regole configurate.  
Frontdoor termina le richieste HTTP nella parte perimetrale della rete Microsoft ed esegue attivamente il probe per rilevare i cambiamenti di integrità o latenza delle applicazioni o dell'infrastruttura.  Frontdoor instrada quindi sempre il traffico al back-end (integro) più veloce e disponibile. Per altre informazioni sul servizio, vere le informazioni sull'[architettura di routing](front-door-routing-architecture.md) e sui [metodi di routing del traffico](front-door-routing-methods.md) di Frontdoor.

## <a name="regional-load-balancing"></a>Bilanciamento del carico a livello di area
Il gateway applicazione offre un controller di recapito delle applicazioni come servizio, con numerose funzionalità di bilanciamento del carico di livello 7 per l'applicazione. Consente ai clienti di ottimizzare la produttività Web farm eseguendo l'offload della terminazione TLS a elevato utilizzo di CPU al gateway applicazione. Altre funzionalità di routing di livello 7 includono la distribuzione round robin del traffico in ingresso, l'affinità di sessione basata su cookie, il routing basato su percorso URL e la possibilità di ospitare più siti Web dietro un unico gateway applicazione. Il gateway applicazione può essere configurato come gateway con connessione Internet, come gateway solo interno o come una combinazione di queste due opzioni. È completamente gestito in Azure e offre scalabilità e disponibilità elevata, oltre a un set completo di funzionalità di registrazione e diagnostica che ne migliorano la gestibilità.
Load Balancer è parte integrante dello stack di Azure SDN e offre servizi di bilanciamento del carico di livello 4 a elevate prestazioni e bassa latenza per tutti i protocolli UDP e TCP. Gestisce le connessioni in ingresso e in uscita. È possibile configurare endpoint pubblici e interni con carico bilanciato e definire regole per mappare le connessioni in ingresso a destinazioni pool back-end con opzioni di probe dell'integrità TCP e HTTP per gestire la disponibilità del servizio.


## <a name="choosing-a-global-load-balancer"></a>Scelta di un servizio di bilanciamento del carico a livello globale
Quando si sceglie un servizio di bilanciamento del carico a livello globale tra Gestione traffico e Frontdoor di Azure per il routing globale, è necessario considerare le analogie e le differenze tra i due servizi.   Entrambi i servizi offrono
- **Ridondanza multi-geografica**: se un'area risulta non disponibile, il traffico viene instradato senza problemi all'area più vicina, senza alcun intervento da parte del proprietario dell'applicazione.
- **Routing all'area più vicina**: il traffico viene automaticamente instradato all'area più vicina.

</br>La tabella seguente descrive le differenze tra gestione traffico e la porta anteriore di Azure:</br>

| Gestione traffico | Frontdoor di Azure |
| --------------- | ------------------------ |
|**Qualsiasi protocollo**: poiché Gestione traffico funziona a livello DNS, è possibile instradare qualsiasi tipo di traffico di rete: HTTP, TCP, UDP e così via. | **Accelerazione http:** Il traffico della porta anteriore viene inoltrato tramite proxy al perimetro della rete Microsoft.  Per questo motivo, le richieste HTTP (S) vedono i miglioramenti della latenza e della velocità effettiva che consentono di ridurre la latenza per la negoziazione TLS e le connessioni a caldo da AFD all'applicazione.|
|**Routing locale:** Con il routing a un livello DNS, il traffico va sempre da punto a punto.  Il routing dalla succursale al Data Center locale può assumere un percorso diretto; anche sulla propria rete mediante gestione traffico. | **Scalabilità indipendente**: poiché Frontdoor funziona con la richiesta HTTP, le richieste a percorsi URL diversi possono essere instradate a back-end/pool di servizi di area (microservizi) diversi, in base alle regole e all'integrità di ogni microservizio dell'applicazione.|
|**Formato di fatturazione**: la fatturazione basata su DNS consente la scalabilità in base agli utenti e per i servizi con più utenti, con la possibilità di ridurre i costi per i casi di maggiore utilizzo. |**Sicurezza inline**: Frontdoor consente l'uso di regole, ad esempio per la limitazione della frequenza, e di elenchi di controllo di accesso in base agli IP per proteggere i back-end prima che il traffico raggiunga l'applicazione. 

</br>Considerando i vantaggi in termini di prestazioni, funzionalità e sicurezza per i carichi di lavoro HTTP offerti da Frontdoor, è consigliabile usare questo servizio per i carichi di lavoro HTTP.    È possibile usare Gestione traffico e Frontdoor in parallelo per soddisfare le esigenze di tutto il traffico per l'applicazione. 

## <a name="building-with-azures-application-delivery-suite"></a>Compilazione con la suite per il recapito di applicazioni di Azure 
È consigliabile che tutti i siti Web, le API e i servizi abbiano ridondanza geografica e recapitino il traffico agli utenti dalla località più vicina (latenza più bassa), quando possibile.  Combinare i servizi di gestione traffico, sportello anteriore, gateway applicazione e Load Balancer ti permette di compilare con ridondanza geografica e zonally per ottimizzare l'affidabilità, la scalabilità e le prestazioni.

Nel diagramma seguente viene descritto un servizio di esempio che usa una combinazione di tutti questi servizi per creare un servizio Web globale.   In questo caso, il progettista ha usato Gestione traffico per il routing ai back-end globali per il recapito di file e oggetti e Frontdoor per il routing a livello globale dei percorsi URL che corrispondono al modello /store/* verso il servizio di cui è stata eseguita la migrazione al servizio app, mentre per tutte le altre richieste viene eseguito il routing ai gateway applicazione a livello di area.

Nell'area, per il servizio IaaS, lo sviluppatore dell'applicazione ha deciso che qualsiasi URL corrispondente al modello /images/* deve essere servito da un pool dedicato di macchine virtuali separato dal resto della Web farm.

Inoltre, il pool predefinito di macchine virtuali che rende disponibile il contenuto dinamico deve comunicare con un database back-end ospitato in un cluster a disponibilità elevata. La configurazione dell'intera distribuzione viene eseguita tramite Azure Resource Manager.

Il diagramma seguente illustra l'architettura di questi scenario:

![Architettura dettagliata della suite per il recapito di applicazioni][2] 

> [!NOTE]
> Questo esempio è solo una delle numerose configurazioni possibili dei servizi di bilanciamento del carico offerti da Azure. È possibile combinare Gestione traffico, Frontdoor, il gateway applicazione e Load Balancer per soddisfare al meglio le specifiche esigenze di bilanciamento del carico. Se, ad esempio, l'offload TLS/SSL o l'elaborazione di livello 7 non è necessaria, è possibile usare Load Balancer al posto del gateway applicazione.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
