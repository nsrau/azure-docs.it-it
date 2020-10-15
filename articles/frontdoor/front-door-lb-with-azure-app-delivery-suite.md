---
title: 'Sportello anteriore di Azure: bilanciamento del carico con la suite di distribuzione delle applicazioni di Azure | Microsoft Docs'
description: Questo articolo illustra come Azure consiglia di bilanciare il carico con la relativa suite di distribuzione delle applicazioni
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 50e047325ad17710794b1640715ab1938373fe85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542173"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Bilanciamento del carico con la suite per il recapito di applicazioni di Azure

## <a name="introduction"></a>Introduzione
Microsoft Azure offre diversi servizi globali e regionali per la gestione della distribuzione del traffico di rete e del bilanciamento del carico: 

* Gateway applicazione
* Frontdoor 
* Load Balancer  
* Gestione traffico

Insieme alle numerose aree e all'architettura di zona di Azure, l'uso combinato di questi servizi può consentire di creare applicazioni solide, scalabili e a elevate prestazioni.

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="Suite per il recapito di applicazioni":::
 
Questi servizi sono suddivisi in due categorie:
1. I **servizi globali di bilanciamento del carico** , ad esempio gestione traffico e sportello anteriore, distribuiscono il traffico dagli utenti finali attraverso i backend internazionali, tra cloud e persino i servizi locali ibridi. Il bilanciamento del carico globale instrada il traffico al back-end del servizio più vicino e reagisce alle modifiche nell'affidabilità del servizio per mantenere la disponibilità sempre attiva e le prestazioni elevate per gli utenti. 
1. I **servizi di bilanciamento del carico a livello** di area, ad esempio i servizi di bilanciamento del carico e i gateway applicazione, offrono la possibilità di distribuire il traffico alle macchine virtuali (VM) in una rete virtuale (reti virtuali) o agli endpoint di servizio all'interno di un'area.

Quando si combinano i servizi globali e internazionali, l'applicazione trarrà vantaggio dal traffico end-to-end affidabile e sicuro che viene inviato dagli utenti finali ai servizi IaaS, PaaS o locali. Nella sezione successiva vengono descritti questi servizi.

## <a name="global-load-balancing"></a>Bilanciamento del carico globale
**Gestione traffico** offre il bilanciamento del carico DNS globale. Esamina le richieste DNS in ingresso e risponde con un back-end integro, seguendo i criteri di routing selezionati dal cliente. Le opzioni per i metodi di routing sono le seguenti:
- * * Il routing delle prestazioni Invia le richieste al back-end più vicino con la latenza minima.
- Il **routing prioritario** indirizza tutto il traffico a un back-end, con altri back-end come backup.
- Il **routing round robin ponderato** distribuisce il traffico in base alla ponderazione assegnata a ogni back-end.
- Il **routing geografico** garantisce che le richieste che vengono originate da aree geografiche specifiche vengano gestite dai backend mappati per tali aree. (Ad esempio, tutte le richieste provenienti dalla Spagna devono essere indirizzate all'area di Azure della Francia centrale)
- Il **routing della subnet** consente di eseguire il mapping degli intervalli di indirizzi IP ai back-end, in modo che le richieste in ingresso per tali indirizzi IP vengano inviate al back-end specifico. (Ad esempio, tutti gli utenti che si connettono dall'intervallo di indirizzi IP dell'HQ aziendale devono ottenere contenuti Web diversi rispetto agli utenti generali)

Il client si connette direttamente al back-end. Gestione traffico di Azure rileva i back-end non integri e reindirizza i client a un'altra istanza integra. Per ulteriori informazioni sul servizio, vedere la documentazione di [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) .

Il front-end di **Azure** fornisce accelerazione sito Web dinamica (DSA), incluso il bilanciamento del carico http globale.  Analizza le richieste HTTP in ingresso e le instrada all'area/back-end del servizio più vicino per il nome host specificato, il percorso URL e le regole configurate.  
Frontdoor termina le richieste HTTP nella parte perimetrale della rete Microsoft ed esegue attivamente il probe per rilevare i cambiamenti di integrità o latenza delle applicazioni o dell'infrastruttura.  Frontdoor instrada quindi sempre il traffico al back-end (integro) più veloce e disponibile. Per altre informazioni sul servizio, vere le informazioni sull'[architettura di routing](front-door-routing-architecture.md) e sui [metodi di routing del traffico](front-door-routing-methods.md) di Frontdoor.

## <a name="regional-load-balancing"></a>Bilanciamento del carico a livello di area
Il gateway applicazione offre un controller di recapito delle applicazioni come servizio, con numerose funzionalità di bilanciamento del carico di livello 7 per l'applicazione. Consente ai clienti di ottimizzare la produttività Web farm eseguendo l'offload della terminazione TLS a elevato utilizzo di CPU al gateway applicazione. Altre funzionalità di routing di livello 7 aggiuntive includono anche la distribuzione Round Robin del traffico in ingresso, l'affinità di sessione basata su cookie, il routing basato su percorso URL e la possibilità di ospitare più siti Web dietro un unico gateway applicazione. Il gateway applicazione può essere configurato come un endpoint con connessione Internet, un endpoint solo interno o una combinazione di entrambi. Il gateway applicazione è completamente gestito da Azure, garantendo scalabilità e disponibilità elevata. oltre a un set completo di funzionalità di registrazione e diagnostica che ne migliorano la gestibilità.

I servizi di bilanciamento del carico sono parte integrante dello stack SDN di Azure, che offre servizi di bilanciamento del carico di livello 4 a prestazioni elevate e a bassa latenza per tutti i protocolli UDP e TCP. È possibile configurare endpoint pubblici o interni con bilanciamento del carico definendo regole che eseguono il mapping delle connessioni in ingresso ai pool back-end. Con il monitoraggio del probe di integrità con TCP o HTTPS, può essere utile per gestire la disponibilità dei servizi.

## <a name="choosing-a-global-load-balancer"></a>Scelta di un servizio di bilanciamento del carico a livello globale
Quando si sceglie un servizio di bilanciamento del carico a livello globale tra Gestione traffico e Frontdoor di Azure per il routing globale, è necessario considerare le analogie e le differenze tra i due servizi.   Entrambi i servizi offrono
- **Ridondanza geografica:** Se un'area è fuori servizio, il traffico viene indirizzato direttamente all'area più vicina senza alcun intervento da parte del proprietario dell'applicazione.
- **Routing all'area più vicina**: il traffico viene automaticamente instradato all'area più vicina.

</br>La tabella seguente descrive le differenze tra gestione traffico e la porta anteriore di Azure:</br>

| Gestione traffico | Frontdoor di Azure |
| --------------- | ------------------------ |
|**Qualsiasi protocollo:** Poiché Gestione traffico funziona a livello di DNS, è possibile indirizzare qualsiasi tipo di traffico di rete. HTTP, TCP, UDP e così via. | **Accelerazione http:** Con la porta anteriore, il traffico viene inoltrato al perimetro della rete Microsoft. Le richieste HTTP/S vedranno i miglioramenti della latenza e della velocità effettiva, riducendo la latenza per la negoziazione TLS.|
|**Routing locale:** Con il routing a un livello DNS, il traffico va sempre da punto a punto.  Il routing dalla succursale al Data Center locale può assumere un percorso diretto; anche sulla propria rete mediante gestione traffico. | **Scalabilità indipendente:** Poiché la porta anteriore funziona con la richiesta HTTP, le richieste a percorsi URL diversi possono essere indirizzate a diversi pool di servizi back-end/regionali (microservizi) in base alle regole e all'integrità di ogni microservizio dell'applicazione.|
|**Formato di fatturazione**: la fatturazione basata su DNS consente la scalabilità in base agli utenti e per i servizi con più utenti, con la possibilità di ridurre i costi per i casi di maggiore utilizzo. |**Sicurezza inline**: Frontdoor consente l'uso di regole, ad esempio per la limitazione della frequenza, e di elenchi di controllo di accesso in base agli IP per proteggere i back-end prima che il traffico raggiunga l'applicazione. 

</br>Si consiglia ai clienti di usare la porta anteriore per il proprio carico di lavoro HTTP a causa delle prestazioni, dell'operabilità e dei vantaggi della sicurezza che il protocollo HTTP funziona con la porta anteriore. È possibile usare Gestione traffico e Frontdoor in parallelo per soddisfare le esigenze di tutto il traffico per l'applicazione. 

## <a name="building-with-azures-application-delivery-suite"></a>Compilazione con la suite per il recapito di applicazioni di Azure 
È consigliabile che tutti i siti Web, le API e i servizi siano con ridondanza geografica, in modo da poter recapitare il traffico agli utenti dalla località più vicina laddove possibile.  La combinazione di più servizi di bilanciamento del carico consente di creare ridondanza geografica e di zona per ottimizzare l'affidabilità e le prestazioni.

Nel diagramma seguente viene descritta un'architettura di esempio che usa una combinazione di tutti questi servizi per creare un servizio Web globale. Il progettista ha usato gestione traffico per instradare il traffico ai backend globali per il recapito di file e oggetti. Quando si usa la porta anteriore, per indirizzare globalmente i percorsi URL che corrispondono al modello/Store/* al servizio di cui è stata eseguita la migrazione al servizio app. Infine, il routing di tutte le altre richieste ai gateway applicazione internazionali.

In ogni area del servizio IaaS, lo sviluppatore dell'applicazione ha deciso che qualsiasi URL corrispondente al modello/images/* viene servito da un pool dedicato di macchine virtuali. Questo pool di macchine virtuali è diverso dal resto del Web farm.

Inoltre, il pool predefinito di macchine virtuali che rende disponibile il contenuto dinamico deve comunicare con un database back-end ospitato in un cluster a disponibilità elevata. L'intera distribuzione viene configurata tramite Azure Resource Manager.

Il diagramma seguente illustra l'architettura di questi scenario:

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="Suite per il recapito di applicazioni":::

> [!NOTE]
> Questo esempio è solo una delle numerose configurazioni possibili dei servizi di bilanciamento del carico offerti da Azure. È possibile combinare Gestione traffico, Frontdoor, il gateway applicazione e Load Balancer per soddisfare al meglio le specifiche esigenze di bilanciamento del carico. Se, ad esempio, l'offload TLS/SSL o l'elaborazione di livello 7 non è necessaria, è possibile usare Load Balancer al posto del gateway applicazione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
