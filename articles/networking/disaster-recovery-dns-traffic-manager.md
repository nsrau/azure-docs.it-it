---
title: Ripristino di emergenza con Gestione traffico e DNS di Azure | Microsoft Docs
description: Panoramica delle soluzioni di ripristino di emergenza con Gestione traffico e DNS di Azure.
services: dns
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2018
ms.author: kumud
ms.openlocfilehash: a560cc526e73f3ce7e851f2a545f9b16fa53b423
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65501725"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Ripristino di emergenza con DNS di Azure e Gestione traffico

Il ripristino di emergenza consiste nel recupero da una grave perdita di funzionalità delle applicazioni. Per scegliere una soluzione di ripristino di emergenza, i proprietari di aziende e tecnologia devono prima di tutto determinare il livello di funzionalità richiesto durante un'emergenza, ad esempio mancanza totale di disponibilità, disponibilità parziale con funzionalità ridotta, disponibilità ritardata o disponibilità completa.
La maggior parte dei clienti aziendali sceglie un'architettura distribuita su più aree per garantire resilienza in caso di failover a livello di applicazione o infrastruttura. I clienti possono adottare diversi approcci per ottenere failover e disponibilità elevata tramite un'architettura ridondante. Ecco alcuni degli approcci più diffusi:

- **Attivo-passivo con cold standby**: In questa soluzione di failover, le macchine virtuali e altre Appliance in esecuzione nell'area in standby non sono attivi fino a quando non è necessario per il failover. Tuttavia, l'ambiente di produzione è replicato sotto forma di backup, immagini di VM o modelli di Resource Manager, in un'altra area. Questo meccanismo di failover è economico, ma richiede più tempo nel caso di un failover completo.
 
    ![Approccio Attivo-Passivo con cold standby](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Figura: Configurazione di ripristino di emergenza di tipo Attivo-Passivo con cold standby*

- **Attiva/passiva con light pilota**: In questa soluzione di failover, l'ambiente standby è configurato con una configurazione minima. che prevede solo l'esecuzione dei servizi necessari per supportare un set minimo critico di applicazioni. Nel formato nativo questo scenario può solo eseguire funzionalità minime, ma può essere ridimensionato con l'aggiunta di altri servizi in modo da gestire gran parte del carico di produzione in caso di failover.
    
    ![Approccio Attivo-Passivo con luce pilota](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Figura: Attiva/passiva con configurazione di ripristino di emergenza pilota light*

- **Attiva/passiva con standby a caldo**: In questa soluzione di failover, l'area in standby viene pre-warmed e pronto accettare il caricamento di base, la scalabilità automatica è attivata e tutte le istanze sono in esecuzione. Questo scenario non può essere ridimensionato in modo da gestire il carico di produzione completo, ma offre una soluzione funzionale, in cui tutti i servizi sono in esecuzione. SI tratta di una versione più estesa dell'approccio con luce pilota.
    
    ![Approccio Attivo-Passivo con warm standby](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Figura: Attiva/passiva con la configurazione di ripristino di emergenza di standby a caldo*
    
Per altre informazioni sul failover e sulla disponibilità elevata, vedere [Ripristino di emergenza per le applicazioni basate su Microsoft Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Pianificazione di un'architettura di ripristino di emergenza

Per configurare un'architettura di ripristino di emergenza è possibile adottare due diverse strategie:
-  Uso di un meccanismo di distribuzione per replicare le istanze, i dati e le configurazioni tra l'ambiente primario e quello di standby. Questo tipo di ripristino di emergenza può essere eseguito in modalità nativa tramite Azure Site Recovery con appliance o servizi di partner Microsoft Azure come Veritas o NetApp. 
- Sviluppo di una soluzione per deviare il traffico Web o di rete dal sito primario a quello di standby. Questo tipo di ripristino di emergenza può essere ottenuto tramite DNS di Azure, Gestione traffico di Azure (DNS) o software di bilanciamento del carico globale di terze parti.

Questo articolo si limita a illustrare gli approcci basati sul reindirizzamento del traffico Web e di rete. Per istruzioni su come configurare Azure Site Recovery, vedere la [documentazione di Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
DNS è uno dei meccanismi più efficienti per deviare il traffico di rete poiché è spesso un servizio globale ed esterno al data center ed è quindi isolato da eventuali errori a livello di area o di zona di disponibilità. Se si sceglie di adottare un meccanismo di failover basato su DNS, in Azure sono disponibili due servizi DNS che consentono di raggiungere lo stesso risultato: DNS di Azure (DNS autorevole) e Gestione traffico di Azure (routing intelligente del traffico basato su DNS). 

È importante comprendere alcuni concetti relativi a DNS che vengono ampiamente usati in questo articolo per illustrare le soluzioni disponibili:
- **Record A DNS** – record sono disponibili informazioni utili che fanno riferimento a un dominio a un indirizzo IPv4. 
- **CNAME o nome canonico**: questo tipo di record viene usato per puntare a un altro record DNS. CNAME non risponde con un indirizzo IP ma con il puntatore al record che contiene l'indirizzo IP. 
- **Routing di tipo Ponderato**: è possibile scegliere di associare un peso agli endpoint del servizio e quindi distribuire il traffico in base ai pesi assegnati. Questo metodo di routing è uno dei quattro meccanismi di routing del traffico disponibili in Gestione traffico. Per altre informazioni, vedere [Metodo di routing del traffico Ponderato](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Routing di tipo Priorità**: questo metodo è basato sui controlli di integrità degli endpoint. Per impostazione predefinita, Gestione traffico di Azure invia tutto il traffico all'endpoint con priorità più elevata e, in caso di errore o di emergenza, indirizza il traffico all'endpoint secondario. Per altre informazioni, vedere [Metodo di routing del traffico Priorità](../traffic-manager/traffic-manager-routing-methods.md#priority).

## <a name="manual-failover-using-azure-dns"></a>Failover manuale con DNS di Azure
La soluzione di failover manuale con DNS di Azure per il ripristino di emergenza adotta il meccanismo standard di DNS per eseguire il failover nel sito di backup. L'opzione manuale tramite DNS di Azure risulta più efficace quando è usata in combinazione con l'approccio con luce pilota o cold standby. 

![Failover manuale con DNS di Azure](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Figura: Failover manuale con DNS di Azure*

La soluzione si basa sui presupposti seguenti:
- L'endpoint primario e quello secondario hanno indirizzi IP statici che non cambiano di frequente. Ad esempio, per il sito primario l'indirizzo IP è 100.168.124.44 e per quello secondario l'indirizzo IP è 100.168.124.43.
- È presente una zona DNS di Azure per entrambi i siti, primario e secondario. Ad esempio, per il sito primario l'endpoint è prod.contoso.com e per il sito di backup l'endpoint è dr.contoso.com. Un record DNS per l'applicazione principale, noto come www\.esiste anche contoso.com.   
- Il valore TTL è pari o inferiore al valore RTO (Recovery Time Objective, obiettivo del tempo di ripristino) del contratto di servizio impostato nell'organizzazione. Se, ad esempio, un'azienda imposta 60 minuti come RTO della risposta di emergenza dell'applicazione, la durata TTL deve essere inferiore a 60 minuti, preferibilmente il più possibile inferiore a questo valore. 
  È possibile configurare il servizio DNS di Azure per il failover manuale nel modo seguente:
- Creare una zona DNS
- Creare record di zona DNS
- Aggiornare il record CNAME

### <a name="step-1-create-a-dns"></a>Passaggio 1: Creare un DNS
Creare una zona DNS (ad esempio, www\.contoso.com) come illustrato di seguito:

![Creare una zona DNS in Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Figura: Creare una zona DNS in Azure*

### <a name="step-2-create-dns-zone-records"></a>Passaggio 2: Creare record di zona DNS

Creare tre record all'interno di quest'area (ad esempio: www\.contoso.com, prod.contoso.com e dr.consoto.com) come illustrato di seguito.

![Creare record di zona DNS](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Figura: Creare record di zona DNS in Azure*

In questo scenario, sito, www\.contoso.com ha una durata (TTL) di 30 minuti, ovvero ben di sotto l'obiettivo RTO dichiarato e fa riferimento a prod.contoso.com di sito di produzione. Questa è la configurazione durante le normali attività aziendali. La durata TTL di prod.contoso.com e dr.contoso.com è stata impostata su 300 secondi, ovvero 5 minuti. È possibile usare un servizio di monitoraggio di Azure, come Monitoraggio di Azure o Azure App Insights, oppure qualsiasi soluzione di monitoraggio di partner, come Dynatrace, o anche soluzioni proprietarie in grado di monitorare o rilevare gli errori a livello di applicazione o di infrastruttura virtuale.

### <a name="step-3-update-the-cname-record"></a>Passaggio 3: Aggiornare il record CNAME

Dopo aver rilevato un errore, modificare il valore del record in modo che punti a dr.contoso.com, come illustrato di seguito:
       
![Aggiornare il record CNAME](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Figura: Aggiornare il record CNAME in Azure*

Entro 30 minuti, durante il quale la maggior parte dei sistemi di risoluzione aggiornerà il file di zona memorizzato nella cache, una query a www\.verrà reindirizzato a dr.contoso.com contoso.com.
Per modificare il valore di CNAME è anche possibile usare il seguente comando dell'interfaccia della riga di comando di Azure:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Questo passaggio può essere eseguito manualmente o tramite automazione. Per la modifica manuale è possibile usare la console o l'interfaccia della riga di comando di Azure. Per l'automazione dell'aggiornamento di CNAME, in modo da evitare l'intervento manuale, è possibile usare l'API e l'SDK di Azure. L'automazione può essere configurata tramite l'uso di funzioni di Azure o di un'applicazione di monitoraggio di terze parti o persino dal sistema locale.

### <a name="how-manual-failover-works-using-azure-dns"></a>Modalità di funzionamento del failover con DNS di Azure
Poiché il server DNS si trova al di fuori della zona di failover o di emergenza, non corre il rischio di tempi di inattività. Ciò consente di progettare uno scenario semplice di failover, economicamente conveniente e in grado di funzionare senza interruzioni, purché l'operatore disponga di connettività di rete in caso di emergenza e possa quindi effettuare il passaggio rapidamente. Se la soluzione viene inserita in uno script, è necessario assicurarsi che il server o il servizio che esegue lo script sia isolato dal problema che interessa l'ambiente di produzione. È inoltre necessario tenere presente il valore basso della durata TTL impostato per la zona, in modo che nessun resolver in tutto il mondo mantenga l'endpoint memorizzato nella cache per molto tempo e i clienti possano accedere al sito entro il tempo RTO. Per un approccio con cold standby o luce pilota, può essere necessario eseguire una configurazione preliminare di tipo warm e altre attività amministrative. È inoltre opportuno considerare un tempo sufficiente prima di effettuare il passaggio.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Failover automatico con Gestione traffico di Azure
Se si hanno architetture complesse e più set di risorse in grado di eseguire la stessa funzione, è possibile configurare Gestione traffico di Azure (basato su DNS) per controllare l'integrità delle risorse e indirizzare il traffico dalla risorsa non integra a quella integra. Nell'esempio seguente l'area primaria e quella secondaria presentano una distribuzione completa. Questa distribuzione include i servizi cloud e un database sincronizzato. 

![Failover automatico con Gestione traffico di Azure](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Figura: Failover automatico con Gestione traffico di Azure*

Tuttavia, solo l'area primaria gestisce attivamente le richieste di rete degli utenti. Quella secondaria si attiva solo quando nell'area primaria si verifica un'interruzione del servizio. In questo caso tutte le richieste di rete vengono reindirizzate all'area secondaria. Poiché il backup del database è quasi istantaneo, entrambi i servizi di bilanciamento del carico hanno indirizzi IP di cui può essere controllata l'integrità e le istanze sono sempre in esecuzione, questa topologia offre una buona opzione per una configurazione con basso RTO e failover senza intervento manuale. L'area di failover secondaria deve essere pronta ad attivarsi immediatamente in caso di problemi con quella primaria.
Questo scenario è ideale per l'uso di Gestione traffico di Azure con probe incorporati per vari tipi di controlli di integrità, inclusi HTTP/HTTPS e TCP. Gestione traffico di Azure ha anche un motore regole che può essere configurato per il failover in caso di errore, come descritto di seguito. Si consideri la soluzione seguente che prevede l'uso di Gestione traffico:
- Il cliente ha un endpoint di Area 1, denominato prod.contoso.com, con indirizzo IP statico 100.168.124.44 e un endpoint di Area 2, denominato dr.contoso.com, con indirizzo IP statico 100.168.124.43. 
-   Ognuno di questi ambienti è gestito tramite una proprietà pubblica come servizio di bilanciamento del carico. Quest'ultimo può essere configurato con un endpoint basato su DNS o un nome di dominio completo (FQDN), come illustrato in precedenza.
-   Tutte le istanze in Area 2 vengono replicate quasi in tempo reale con Area 1. Inoltre, le immagini di computer sono aggiornate e tutti i dati di software/configurazione hanno patch applicate e sono in linea con Area 1.  
-   La scalabilità automatica è preconfigurata in anticipo. 

I passaggi da eseguire per configurare il failover con Gestione traffico di Azure sono i seguenti:
1. Creare un nuovo profilo di Gestione traffico di Azure
2. Creare endpoint all'interno del profilo di Gestione traffico
3. Definire la configurazione per il controllo di integrità e il failover

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Passaggio 1: Creare un nuovo profilo di Gestione traffico di Azure
Creare un nuovo profilo di Gestione traffico di Azure denominato contoso123 e selezionare Priorità come Metodo di routing. Se si ha già un gruppo di risorse da associare al profilo, è possibile selezionarlo, altrimenti crearne uno nuovo.

![Crea profilo di Gestione traffico](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Figura - creare un profilo di Traffic Manager*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Passaggio 2: Creare endpoint all'interno del profilo di Gestione traffico

In questo passaggio si creano endpoint che puntano ai siti di produzione e di ripristino di emergenza. In questo caso, scegliere un endpoint esterno come **Tipo**, ma se la risorsa è ospitata in Azure, è possibile scegliere anche **Endpoint Azure**. Se si sceglie **Endpoint Azure**, selezionare come **Risorsa di destinazione** un **servizio App** o un **IP pubblico** allocato da Azure. Per la priorità è impostato il valore **1** perché si tratta del servizio primario per Area 1.
Con una procedura analoga, creare l'endpoint di ripristino di emergenza anche all'interno di Gestione traffico.

![Creare endpoint di ripristino di emergenza](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Figura: Creare endpoint di ripristino di emergenza*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Passaggio 3: Definire la configurazione per il controllo di integrità e il failover

In questo passaggio si imposta la durata TTL di DNS su 10 secondi, un valore che viene rispettato dalla maggior parte dei resolver ricorsivi con connessione Internet. Questa configurazione indica che nessun resolver DNS memorizzerà le informazioni nella cache per più di 10 secondi. Per le impostazioni di monitoraggio degli endpoint, il percorso è impostato sulla radice (o /), ma è possibile personalizzare le impostazioni degli endpoint in modo da valutare un percorso, ad esempio prod.contoso.com/index. Come protocollo di sondaggio nell'esempio seguente è impostato **HTTPS**, ma è possibile scegliere anche **HTTP** o **TCP**. La scelta del protocollo dipende dall'applicazione finale. L'intervallo di sondaggio è impostato su 10 secondi, in modo da consentire un'individuazione rapida tramite probe, e come valore di retry è impostato 3. Di conseguenza, Gestione traffico eseguirà il failover nel secondo endpoint se verrà registrato un errore in tre intervalli consecutivi. La formula seguente definisce il tempo totale per un failover automatico: Tempo per il failover di durata (TTL) = + ripetere * Probing intervallo e in questo caso, il valore è 10 + 3 * 10 = 40 secondi (Max).
Se il valore di retry è impostato su 1 e la durata TTL su 10 secondi, il tempo per il failover sarà 10 + 1 * 10 = 20 secondi. Impostare il valore di retry su un valore maggiore di **1** per evitare l'esecuzione del failover a causa di falsi positivi o problemi di rete irrilevanti. 


![Configurare il controllo di integrità](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Figura: Definire la configurazione per il controllo di integrità e il failover*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Modalità di funzionamento del failover con Gestione traffico

Durante un'emergenza, l'endpoint primario viene sottoposto a sondaggio tramite probe, lo stato diventa **danneggiato** e il sito di ripristino di emergenza rimane **Online**. Per impostazione predefinita, tutto il traffico viene inviato all'endpoint primario (o con priorità più elevata). Se l'endpoint primario risulta danneggiato, Gestione traffico indirizza il traffico verso il secondo endpoint purché rimanga integro. In Gestione traffico si ha la possibilità di configurare più endpoint da usare come endpoint di failover aggiuntivi o come servizi di bilanciamento del carico per la condivisione del carico tra endpoint.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md).
- Altre informazioni su [DNS di Azure](../dns/dns-overview.md).









