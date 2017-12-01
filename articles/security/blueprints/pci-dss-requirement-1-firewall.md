---
title: Progetto per l'elaborazione dei pagamenti di Azure - Requisiti per i firewall
description: Requisito 1 di PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: b1935d88-acae-42f9-bc25-bb0766f876ab
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 995ecd5ef876695145fc6313aba2a46d2cc085cc
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="firewall-requirements-for-pci-dss-compliant-environments"></a>Requisiti per i firewall per gli ambienti conformi a PCI DSS 
## <a name="pci-dss-requirement-1"></a>Requisito 1 di PCI DSS

**Installare e gestire una configurazione del firewall per proteggere i dati di titolari di carte**

> [!NOTE]
> Questi requisiti sono definiti dal [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) nell'ambito di [PCI Data Security Standard (DSS) versione 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Per informazioni sulle procedure di test e indicazioni per ogni requisito, vedere lo standard PCI DSS.

I firewall sono dispositivi che controllano il traffico dei computer consentito tra le reti di un'entità (interne) e le reti non attendibili (esterne), nonché il traffico in e da aree più sensibili all'interno delle reti attendibili interne di un'entità. L'ambiente dei dati di titolari di carte è un esempio di un'area più sensibile all'interno della rete attendibile di un'entità.
Un firewall esamina tutto il traffico di rete e blocca le trasmissioni che non soddisfano i criteri di sicurezza specificati.
Tutti i sistemi devono essere protetti da accessi non autorizzati da reti non attendibili, sia in ingresso nel sistema tramite Internet come transazioni di e-commerce, accesso a Internet dei dipendenti tramite browser desktop, accesso dei dipendenti alla posta elettronica, connessioni dedicate come le connessioni B2B (business-to-business), tramite reti wireless o altre origini. Spesso, percorsi apparentemente non significativi da e verso reti non attendibili possono rappresentare percorsi non protetti per l'accesso a sistemi chiave. I firewall rappresentano un meccanismo di protezione cruciale per qualsiasi rete di computer.
Altri componenti di sistema possono offrire funzionalità di firewall, a condizione che soddisfino i requisiti minimi per i firewall definiti nel requisito 1. Nei casi in cui si usano altri componenti di sistema nell'ambiente dei dati di titolari di carte per fornire funzionalità di firewall, questi dispositivi devono essere inclusi nell'ambito e nelle valutazioni del requisito 1.

## <a name="pci-dss-requirement-11"></a>Requisito 1.1 di PCI DSS

**1.1** Definire e implementare standard per la configurazione di firewall e router che includano i seguenti (vedere i requisiti da 1.1.1 a 1.1.7).


**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore implementa la protezione tramite firewall dell'ambiente dei dati di titolari di carte tramite l'isolamento PaaS e un'implementazione dell'ambiente del servizio app garantisce la protezione dei dati in entrata e in uscita dall'ambiente dei dati di titolari di carte.<br /><br />Un [ambiente del servizio app](/azure/app-service-web/app-service-app-service-environment-intro) è un piano di servizio Premium usato per motivi di conformità. Per altre informazioni sui controlli e la configurazione dell'ambiente del servizio app, vedere [PCI Guidance - App Service Environment](payment-processing-blueprint.md#app-service-environment) (Linee guida per PCI - Ambiente del servizio app).|



### <a name="pci-dss-requirement-111"></a>Requisito 1.1.1 di PCI DSS

**1.1.1** Processo formale per l'approvazione e il testing di tutte le connessioni di rete e delle modifiche alle configurazioni di firewall e router


**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Un'istanza di Contoso Webstore stabilisce un modello DevOps di integrazione continua/recapito continuo (CI/CD) per garantire che tutte le modifiche vengano gestite correttamente. [Operations Management Suite (OMS)](/azure/operations-management-suite/) fornisce una registrazione completa delle modifiche. Le modifiche possono essere controllate e verificate per stabilirne l'accuratezza. Per istruzioni più dettagliate, vedere [PCI Guidance - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing) (Linee guida per PCI - Operations Management Suite).<br /><br />[Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) offre una vista centralizzata dello stato di sicurezza di tutte le risorse di Azure. È possibile verificare subito che i controlli di sicurezza appropriati siano implementati e configurati correttamente, così come identificare rapidamente le risorse che richiedono attenzione.|



### <a name="pci-dss-requirement-112"></a>Requisito 1.1.2 di PCI DSS

**1.1.2** Diagramma di rete aggiornato che identifica tutte le connessioni tra l'ambiente dei titolari di carte e altre reti, tra cui eventuali reti wireless

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Fare riferimento all'architettura e alla documentazione di progettazione di riferimento di Contoso Webstore fornite come parte del modello di installazione della soluzione.|



### <a name="pci-dss-requirement-113"></a>Requisito 1.1.3 di PCI DSS

**1.1.3** Diagramma aggiornato che mostra tutti i flussi dei dati di titolari di carte all'interno di sistemi e reti

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Fare riferimento al diagramma di flusso dei dati e al modello di rischio di Contoso Webstore.|



### <a name="pci-dss-requirement-114"></a>Requisito 1.1.4 di PCI DSS

**1.1.4** Presenza di un firewall per ogni connessione a Internet e tra qualsiasi rete perimetrale (DMZ) e la zona della rete interna

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure usa dispositivi di protezione dei limiti, come gateway, ACL di rete e firewall applicazione per controllare le comunicazioni sui limiti esterni e interni al livello della piattaforma. Il cliente configura quindi questi dispositivi in base alle specifiche e ai requisiti. Microsoft Azure filtra le comunicazioni in ingresso nella piattaforma. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore offre una rete perimetrale che usa l'isolamento PaaS e un'implementazione dell'ambiente del servizio app garantisce la protezione dei dati in entrata e in uscita nell'ambiente dei dati di titolari di carte.<br /><br />Un [ambiente del servizio app](/azure/app-service-web/app-service-app-service-environment-intro) è un piano di servizio Premium usato per motivi di conformità. Per altre informazioni sui controlli e la configurazione dell'ambiente del servizio app, vedere [PCI Guidance - App Service Environment](payment-processing-blueprint.md#app-service-environment) (Linee guida per PCI - Ambiente del servizio app).|



### <a name="pci-dss-requirement-115"></a>Requisito 1.1.5 di PCI DSS

**1.1.5** Descrizione di gruppi, ruoli e responsabilità per la gestione dei componenti di rete

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa il [controllo degli accessi in base al ruolo](/azure/active-directory/role-based-access-control-configure) per isolare i ruoli utente. Questo meccanismo consente una gestione degli accessi di precisione per Azure. Esistono configurazioni specifiche per l'accesso con sottoscrizione e l'accesso con Azure Key Vault.|



### <a name="pci-dss-requirement-116"></a>Requisito 1.1.6 di PCI DSS

**1.1.6** Documentazione delle motivazioni e approvazioni aziendali per l'uso di tutti i servizi, i protocolli e le porte consentiti, inclusa la documentazione delle funzionalità di sicurezza implementate per i protocolli considerati non sicuri.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore apre solo le porte e i protocolli necessari nell'ambito della progettazione dell'autorità di registrazione (RA). Informazioni dettagliate su flusso di dati sono disponibili nel diagramma di flusso dei dati e nel modello di rischio.|



### <a name="pci-dss-requirement-117"></a>Requisito 1.1.7 di PCI DSS

**1.1.7** Requisito di riesame dei set di regole per firewall e router almeno ogni sei mesi

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | In Contoso Webstore, i set di regole del firewall vengono riesaminati per assicurarsi che non siano incluse regole superflue o inutilizzate. Come da progettazione, la demo viene distribuita con un footprint basato su privilegi minimi e percorsi più brevi.|



## <a name="pci-dss-requirement-12"></a>Requisito 1.2 di PCI DSS

**1.2** Creazione di configurazioni di firewall e router che limitano le connessioni tra reti non attendibili e i componenti di sistema nell'ambiente dei dati di titolari di carte. 

> [!NOTE]
> Con "rete non attendibile" si intende qualsiasi rete esterna alle reti che appartengono all'entità in fase di esame e/o che non rientra nelle capacità di controllo o gestione dell'entità.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | L'ambiente dei dati di titolari di carte di Contoso Webstore è definito nella documentazione relativa all'autorità di registrazione (RA) e alla distribuzione. La progettazione prevede la negazione delle reti non attendibili.|



### <a name="pci-dss-requirement-121"></a>Requisito 1.2.1 di PCI DSS

**1.2.1** Limitare il traffico in ingresso e in uscita a quello necessario per l'ambiente dei dati di titolari di carte e negare in modo specifico tutto il restante traffico.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | L'ambiente dei dati di titolari di carte di Contoso Webstore è definito nella documentazione relativa all'autorità di registrazione (RA) e alla distribuzione. La progettazione prevede la negazione delle reti non attendibili. La demo Contoso Webstore consente di configurare il firewall applicazione di Microsoft Azure per consentire solo agli intervalli di indirizzi IP specificati di accedere ai servizi di Microsoft Azure. Contoso Webstore offre un firewall per negare tutto il traffico su tutti i limiti dell'ambiente dei dati di titolari di carte. Tutte le configurazioni sono eseguite durante l'installazione iniziale della distribuzione.

> [!NOTE]
> In questa soluzione, per isolare l'ambiente dei dati di titolari di carte viene usato l'ambiente del servizio app. Tuttavia, è fondamentale che la soluzione venga valutata dall'ispettore QSA (Qualified Security Assessor) di riferimento, perché l'ambiente del servizio app implementa un isolamento della rete perimetrale che consente connessioni in uscita dall'ambiente del servizio app. Lo standard PCI-DSS richiede il blocco di tutte le connessioni in ingresso e in uscita non necessarie. Per il corretto funzionamento, l'ambiente del servizio app stabilirà le connessioni in uscita necessarie, come definito in ["Considerazioni sulla rete per un ambiente del servizio app"](/azure/app-service/app-service-environment/network-info). I clienti devono valutare le connessioni in uscita insieme all'ispettore QSA prima di distribuire la soluzione in ambiente di produzione, per assicurarsi che soddisfi i requisiti. |



### <a name="pci-dss-requirement-122"></a>Requisito 1.2.2 di PCI DSS

**1.2.2** Proteggere e sincronizzare i file di configurazione dei router.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore offre configurazioni sincronizzate per i controlli di rete nativi di Microsoft Azure.|



### <a name="pci-dss-requirement-123"></a>Requisito 1.2.3 di PCI DSS

**1.2.3** Installare firewall perimetrali tra tutte le reti wireless e l'ambiente dei dati di titolari di carte, quindi configurare questi firewall per negare il traffico tra l'ambiente wireless e l'ambiente dei dati di titolari di carte oppure, nel caso il traffico sia necessario per scopi aziendali, consentire solo il traffico autorizzato.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore non include soluzioni o funzionalità wireless abilitate.|



## <a name="pci-dss-requirement-13"></a>Requisito 1.3 di PCI DSS

**1.3** Proibire l'accesso pubblico diretto tra Internet e qualsiasi componente di sistema nell'ambiente dei dati di titolari di carte.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure usa dispositivi di protezione dei limiti basati su rete e su host, come firewall, servizi di bilanciamento del carico ed elenchi di controllo di accesso. Questi dispositivi usano meccanismi come l'isolamento VLAN, NAT e il filtraggio dei pacchetti, per separare il traffico dei clienti dal traffico Internet e di gestione. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore offre, al momento della distribuzione, le configurazioni del firewall applicazione di Azure per consentire l'accesso al sito solo agli intervalli di indirizzi IP specificati, incluse le macchine virtuali di Azure bastion nell'ambiente dei dati di titolari di carte.|



### <a name="pci-dss-requirement-131"></a>Requisito 1.3.1 di PCI DSS

**1.3.1** Implementare una rete perimetrale per limitare il traffico in ingresso ai soli componenti di sistema che forniscono servizi, protocolli e porte autorizzati e accessibili pubblicamente.


**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | L'implementazione della rete perimetrale di Contoso Webstore garantisce che solo i servizi autorizzati possano connettersi all'ambiente dei dati di titolari di carte.|



### <a name="pci-dss-requirement-132"></a>Requisito 1.3.2 di PCI DSS

**1.3.2** Limitare il traffico Internet in ingresso agli indirizzi IP all'interno della rete perimetrale.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | L'implementazione della rete perimetrale di Contoso Webstore garantisce che solo i servizi autorizzati possano connettersi all'ambiente dei dati di titolari di carte.|



### <a name="pci-dss-requirement-133"></a>Requisito 1.3.3 di PCI DSS

**1.3.3** Implementare misure anti-spoofing per rilevare indirizzi IP di origine contraffatti e impedirne l'ingresso nella rete. (Ad esempio, bloccare il traffico proveniente da Internet con un indirizzo di origine interno.)

**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure implementa l'applicazione di filtri alla rete per impedire il traffico falsificato e limitare il traffico in ingresso e in uscita ai componenti attendibili della piattaforma. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



### <a name="pci-dss-requirement-134"></a>Requisito 1.3.4 di PCI DSS

**1.3.4** Non consentire il traffico in uscita non autorizzato dall'ambiente dei dati di titolari di carte a Internet.


**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | L'architettura di Contoso Webstore impedisce il traffico in uscita non autorizzato dall'ambiente nell'ambito a Internet. Questa protezione viene ottenuta configurando elenchi di controllo di accesso per il traffico in uscita per le porte e i protocolli approvati in Microsoft Azure. Questi controlli includono l'accesso all'ambiente dei dati di titolari di carte nel database di SQL Server. <br /><br />Un'istanza di database SQL PaaS viene usata per presentare le misure di sicurezza del database. Per altre informazioni, vedere [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Linee guida per PCI - Database SQL di Azure).|



### <a name="pci-dss-requirement-135"></a>Requisito 1.3.5 di PCI DSS

**1.3.5** Consentire solo connessioni comprovate nella rete.


**Responsabilità:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure implementa l'applicazione di filtri alla rete per impedire il traffico falsificato e limitare il traffico in ingresso e in uscita ai componenti attendibili della piattaforma. La rete di Microsoft Azure è segregata in modo da separare il traffico dei clienti dal traffico di gestione. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Non applicabile|



### <a name="pci-dss-requirement-136"></a>Requisito 1.3.6 di PCI DSS

**1.3.6** Posizionare i componenti di sistema che archiviano i dati di titolari di carte (ad esempio un database) in una zona della rete interna, separata dalla rete perimetrale e da altre reti non attendibili.


**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure usa la segregazione della rete e NAT per separare il traffico dei clienti dal traffico di gestione. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | L'architettura di Contoso Webstore impedisce il traffico in uscita non autorizzato dall'ambiente nell'ambito a Internet. Questa protezione viene ottenuta configurando elenchi di controllo di accesso per il traffico in uscita per le porte e i protocolli approvati in Microsoft Azure. Questi controlli includono l'accesso all'ambiente dei dati di titolari di carte nel database di SQL Server. <br /><br />Un'istanza di database SQL PaaS viene usata per presentare le misure di sicurezza del database. Per altre informazioni, vedere [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Linee guida per PCI - Database SQL di Azure).|



### <a name="pci-dss-requirement-137"></a>Requisito 1.3.7 di PCI DSS

**1.3.7** Non rivelare indirizzi IP privati e informazioni di routing a soggetti non autorizzati. 

> [!NOTE]
> I metodi per nascondere gli indirizzi IP includono, a titolo di esempio:
> - NAT (Network Address Translation).
> - Protezione dei server che contengono i dati di titolari di carte con server proxy o firewall.
> - Rimozione o filtraggio degli annunci di route per le reti private che usano l'indirizzamento registrato.
> - Uso interno dello spazio indirizzi RFC 1918 anziché degli indirizzi registrati.


**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure usa NAT (Network Address Translation) e la segregazione della rete per separare il traffico dei clienti dal traffico di gestione. I dispositivi di Azure vengono identificati in modo univoco in base al rispettivo UUID e vengono autenticati tramite Kerberos. I dispositivi di rete gestiti da Azure sono identificati in base a indirizzi IP RFC 1918. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore posiziona tutti i dati di titolari di carte dietro server proxy/firewall e usa internamente lo spazio indirizzi RFC1918.|



## <a name="pci-dss-requirement-14"></a>Requisito 1.4 di PCI DSS

**1.4** Installare software firewall personale o funzionalità equivalenti in qualsiasi dispositivo portatile (sia di proprietà dell'azienda che dei dipendenti) che si connette a Internet all'esterno della rete (ad esempio, i computer portatili usati dai dipendenti), e che viene usato anche per accedere all'ambiente dei dati di titolari di carte. Le configurazioni del firewall (o equivalente) includono: -Sono definite impostazioni di configurazione specifiche.
- Esecuzione attiva del firewall personale (o di una funzionalità equivalente).
- Il firewall personale (o una funzionalità equivalente) non può essere modificato dagli utenti dei dispositivi portatili.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore non offre protezione per i dispositivi degli utenti finali. È possibile usare [Microsoft Intune](https://www.microsoft.com/cloud-platform/microsoft-intune) per gestire i dispositivi mobili usati dalla forza lavoro per accedere ai dati aziendali.|



## <a name="pci-dss-requirement-15"></a>Requisito 1.5 di PCI DSS

**1.5** Assicurarsi che i criteri di sicurezza e le procedure operative per la gestione dei firewall siano documentati, applicati e noti a tutte le parti interessate.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore offre, al momento della distribuzione, le configurazioni del firewall applicazione di Azure per consentire l'accesso al sito solo agli intervalli di indirizzi IP specificati, incluse le macchine virtuali di Azure bastion nell'ambiente dei dati di titolari di carte.|




