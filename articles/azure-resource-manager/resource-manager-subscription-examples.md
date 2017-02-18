---
title: Scenari ed esempi per la governance delle sottoscrizioni | Microsoft Docs
description: Vengono forniti esempi di come implementare la governance delle sottoscrizioni di Azure per scenari comuni.
services: azure-resource-manager
documentationcenter: na
author: rdendtler
manager: timlt
editor: tysonn
ms.assetid: e8fbeeb8-d7a1-48af-804f-6fe1a6024bcb
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: rodend;karlku;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: c75d95ed554a78a02e5469915c21491e65edd8c2
ms.openlocfilehash: 14ec59087b0aede76a18034f5aa93cb6ecd67a7e


---
# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Esempi di implementazione di scaffold enterprise di Azure
In questo argomento vengono forniti esempi di come un'azienda può implementare gli elementi consigliati per uno [scaffold  enterprise di Azure](resource-manager-subscription-governance.md). Usa una società fittizia denominata Contoso per illustrare le procedure consigliate per scenari comuni.

## <a name="background"></a>Background
Contoso è una società che opera in tutto il mondo per fornire soluzioni della supply chain per i clienti in ogni ambito, dal modello "Software come servizio" a un modello in pacchetto distribuito in locale.  La società sviluppa software in tutto il mondo e dispone di importanti centri per lo sviluppo in India, Stati Uniti e Canada.

La parte ISV della società è suddivisa in diverse business unit indipendenti che gestiscono i prodotti di un importante settore. Ogni business unit ha i suoi sviluppatori, responsabili di prodotto e architetti.

La business unit Enterprise Technology Services (ETS) fornisce funzionalità IT centralizzata e gestisce più data center in cui le business unit ospitano le loro applicazioni. Oltre a gestire i data center, l'organizzazione ETS fornisce e gestisce una collaborazione centralizzata (ad esempio e-mail e siti Web) e i servizi di rete/telefonia. Gestisce inoltre i carichi di lavoro rivolti ai clienti per business unit più piccole che non dispongono di personale operativo.

In questa esercitazione vengono usati gli utenti tipo seguenti.

* Dave è l'amministratore di Azure ETS.
* Alice è Director of Development di Contoso della business unit della supply chain.

Contoso deve creare un'app line-of-business e un'app rivolta ai clienti. Ha deciso di eseguire le app in Azure. Dave legge l'argomento relativo alla [governance normativa delle sottoscrizioni](resource-manager-subscription-governance.md) e ora è pronto a mettere in atto i consigli.

## <a name="scenario-1-line-of-business-application"></a>Scenario 1: applicazione line-of-business
Contoso sta creando un sistema per la gestione dei codici sorgente (BitBucket) che possa essere usato dagli sviluppatori in tutto il mondo.  L'applicazione usa l'infrastruttura distribuita come servizio (IaaS) per l'hosting ed è costituita da server Web e server di database. Gli sviluppatori accedono ai server nei loro ambienti di sviluppo, tuttavia non hanno bisogno di accedervi in Azure. Contoso ETS vuole consentire ai proprietari delle applicazioni e ai team di gestire l'applicazione. L'applicazione è disponibile solo mentre si trova nella rete aziendale di Contoso. Dave deve configurare la sottoscrizione per l'applicazione. In futuro, la sottoscrizione ospiterà anche altri software destinati agli sviluppatori.  

### <a name="naming-standards--resource-groups"></a>Standard di denominazione e gruppi di risorse
Dave crea una sottoscrizione per supportare gli strumenti di sviluppo più diffusi nelle business unit. Deve creare nomi significativi per ila sottoscrizione e i gruppi di risorse (per l'applicazione e le reti). Crea le sottoscrizioni e i gruppi di risorse seguenti:

| Item | Nome | Descrizione |
| --- | --- | --- |
| Subscription |Produzione di strumenti per gli sviluppatori Contoso ETS |Supporta gli strumenti di sviluppo più diffusi |
| Gruppo di risorse |rgBitBucket |Contiene il server Web dell'applicazione e il server di database |
| Gruppo di risorse |rgCoreNetworks |Contiene le reti virtuali e la connessione gateway da sito a sito |

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo
Dopo aver creato la sottoscrizione, Dave desidera assicurarsi che le risorse siano accessibili dal team appropriato e dai proprietari delle applicazioni. Dave riconosce che ogni team ha requisiti diversi. Usa i gruppi che sono stati sincronizzati da Active Directory locale di Contoso ad Azure Active Directory e fornisce il corretto livello di accesso ai team.

Dave assegna i ruoli seguenti per la sottoscrizione:

| Ruolo | Assegnato a | Descrizione |
| --- | --- | --- |
| [Proprietario](../active-directory/role-based-access-built-in-roles.md#owner) |ID gestito da Active Directory di Contoso |Questo ID è controllato con accesso Just in Time (JIT) tramite lo strumento di gestione delle identità di Contoso e assicura il totale controllo degli accessi dei proprietari delle sottoscrizioni. |
| [Gestore della sicurezza SQL](../active-directory/role-based-access-built-in-roles.md#security-manager) |Reparto di gestione dei rischi e della sicurezza |Questo ruolo consente agli utenti di esaminare il Centro sicurezza di Azure e lo stato delle risorse. |
| [Collaboratore di rete](../active-directory/role-based-access-built-in-roles.md#network-contributor) |Team di rete |Questo ruolo consente team di rete di Contoso gestire la VPN da sito a sito e le reti virtuali. |
| *Ruolo personalizzato* |Proprietario dell'applicazione |Dave crea un ruolo che concede la possibilità di modificare le risorse all'interno del gruppo di risorse. Per altre informazioni, vedere [Ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-custom-roles.md) |

### <a name="policies"></a>Criteri
Dave dispone dei requisiti seguenti per gestire le risorse nella sottoscrizione:

* Poiché gli strumenti di sviluppo aiutano gli sviluppatori di tutto il mondo, non desidera impedire agli utenti di creare risorse in qualsiasi area. Tuttavia, ha bisogno di sapere dove vengono create le risorse.
* È preoccupato per i costi. Di conseguenza, desidera impedire ai proprietari delle applicazioni di creare macchine virtuali inutilmente costose.  
* Poiché questa applicazione è destinata agli sviluppatori di diverse business unit. desidera aggiungere a ogni risorsa un tag con il proprietario dell'applicazione e la business unit. L'uso di questi tag aiuta ETS nella fatturazione ai team appropriati.

Dave crea i [criteri di Resource Manager](resource-manager-policy.md) seguenti:

| Campo | Effetto | Descrizione |
| --- | --- | --- |
| location |audit |Controlla la creazione delle risorse in qualsiasi area |
| type |deny |Nega la creazione di macchine virtuali serie G |
| tags |deny |Richiede il tag del proprietario dell'applicazione |
| tags |deny |Richiede il tag del centro di costo |
| tags |append |Aggiunge il nome del tag **BusinessUnit** e il valore del tag **ETS** a tutte le risorse |

### <a name="resource-tags"></a>Tag delle risorse
Dave è consapevole che è necessario disporre di informazioni specifiche sulla fattura per identificare il centro di costo per l'implementazione di BitBucket. Inoltre, Dave desidera conoscere tutte le risorse ETS che possiede.

Aggiunge i [tag](resource-group-using-tags.md) seguenti ai gruppi di risorse e alle risorse.

| Nome del tag | Valore del tag |
| --- | --- |
| ApplicationOwner |Il nome della persona che gestisce l'applicazione. |
| CostCenter |Il centro di costo del gruppo che paga per il consumo di Azure. |
| BusinessUnit |**ETS** (la business unit associata alla sottoscrizione) |

### <a name="core-network"></a>Rete core
Il team di gestione dei rischi e della sicurezza delle informazioni di Contoso ETS esamina il piano proposto da Dave per spostare l'applicazione in Azure. Desidera assicurarsi che l'applicazione non sia esposto su Internet.  Dave dispone anche di app per gli sviluppatori che in futuro verranno spostate in Azure. Queste app richiedono interfacce pubbliche.  Per soddisfare questi requisiti, fornisce reti virtuali interne ed esterne e un gruppo di sicurezza di rete per limitare l'accesso.

Crea le risorse seguenti:

| Tipo di risorsa | Nome | Descrizione |
| --- | --- | --- |
| Rete virtuale |vnInternal |Viene usata con l'applicazione BitBucket ed è connessa tramite ExpressRoute alla rete aziendale di Contoso.  Una subnet (sbBitBucket) fornisce all'applicazione uno spazio di indirizzi IP specifico. |
| Rete virtuale |vnExternal |È disponibile per le applicazioni future che richiedono endpoint pubblici. |
| Gruppo di sicurezza di rete |nsgBitBucket |Garantisce che la superficie di attacco di questo carico di lavoro sia ridotta al minimo consentendo le connessioni solo sulla porta 443 per la subnet in cui risiede l'applicazione (sbBitBucket). |

### <a name="resource-locks"></a>Blocchi risorse
Dave riconosce che la connettività dalla rete aziendale di Contoso alla rete virtuale interna deve essere protetta da qualsiasi script imprevisto o eliminazione accidentale.

Crea il [blocco risorsa](resource-group-lock-resources.md) seguente:

| Tipo di blocco | Risorsa | Descrizione |
| --- | --- | --- |
| **CanNotDelete** |vnInternal |Impedisce agli utenti di eliminare la rete virtuale o le subnet, ma non impedisce l'aggiunta di nuove subnet. |

### <a name="azure-automation"></a>Automazione di Azure
Dave non ha nulla da automatizzare per questa applicazione. Anche se ha creato un account di Automazione di Azure, inizialmente non lo userà.

### <a name="azure-security-center"></a>Centro sicurezza di Azure
La gestione dei servizi IT di Contoso deve identificare e gestire rapidamente i rischi. Inoltre è necessario capire quali problemi potrebbero esistere.  

Per soddisfare questi requisiti, Dave abilita il [Centro sicurezza di Azure](../security-center/security-center-intro.md) e fornisce l'accesso al responsabile della sicurezza.

## <a name="scenario-2-customer-facing-app"></a>Scenario 2: app destinata ai clienti
I responsabili aziendali della business unit della supply chain hanno identificato diverse opportunità per aumentare il coinvolgimento dei clienti Contoso attraverso l'uso di una carta fedeltà. Il team di Alice deve creare l'applicazione e decide che Azure aumenta la capacità di soddisfare le esigenze aziendali. Alice collabora con Dave di ETS per configurare due sottoscrizioni per sviluppare e far funzionare l'applicazione.

### <a name="azure-subscriptions"></a>Sottoscrizioni Azure
Dave accede al portale aziendale di Azure e vede che il reparto della supply chain esiste già.  Tuttavia, poiché questo progetto è il primo progetto di sviluppo per il team della supply chain in Azure, Dave riconosce la necessità di un nuovo account per il team di sviluppo di Alice.  Crea l'account "R & D" per il team e assegna l'accesso ad Alice. Alice accede tramite il portale di Azure e crea due sottoscrizioni: una per i server di sviluppo e l'altra per i server di produzione.  Segue gli standard di denominazione stabiliti in precedenza durante la creazione di sottoscrizioni seguenti:

| Uso della sottoscrizione | Nome |
| --- | --- |
| Sviluppo. |SupplyChain ResearchDevelopment LoyaltyCard Development |
| Produzione |SupplyChain Operations LoyaltyCard Production |

### <a name="policies"></a>Criteri
Dave e Alice discutono dell'applicazione e concludono che è destinata solo ai clienti nell'area dell'America del Nord.  Alice e il suo team pianificano di usare l'ambiente del servizio dell'applicazione di Azure e SQL di Azure per creare l'applicazione. Potrebbero aver bisogno di creare macchine virtuali durante lo sviluppo.  Alice vuole assicurarsi che gli sviluppatori dispongano delle risorse che necessarie per esplorare ed esaminare i problemi senza doversi rivolgere a ETS.

Per la **sottoscrizione di sviluppo**, creano i criteri seguenti:

| Campo | Effetto | Descrizione |
| --- | --- | --- |
| location |audit |Controlla la creazione delle risorse in qualsiasi area. |

Non stabiliscono un limite per il tipo di SKU che un utente può creare in fase di sviluppo e non richiedono tag per i gruppi di risorse o le risorse.

Per la **sottoscrizione di produzione**, creano i criteri seguenti:

| Campo | Effetto | Descrizione |
| --- | --- | --- |
| location |deny |Impedisce la creazione di risorse all'esterno dei data center degli Stati Uniti. |
| tags |deny |Richiede il tag del proprietario dell'applicazione |
| tags |deny |Richiede il tag del reparto. |
| tags |append |Aggiunge un tag a ogni gruppo di risorse per indicare l'ambiente di produzione. |

Non stabiliscono un limite per il tipo di SKU che un utente può creare nell'ambiente di produzione.

### <a name="resource-tags"></a>Tag delle risorse
Dave capisce che è necessario disporre di informazioni specifiche per identificare i gruppi aziendali corretti per la fatturazione e la proprietà. Definisce i tag delle risorse per i gruppi di risorse e le risorse.

| Nome del tag | Valore del tag |
| --- | --- |
| ApplicationOwner |Il nome della persona che gestisce l'applicazione. |
| Department |Il centro di costo del gruppo che paga per il consumo di Azure. |
| EnvironmentType |**Produzione** (anche se la sottoscrizione include **produzione** nel nome, l'inclusione di questo tag consente di identificarla facilmente quando si esaminano le risorse nel portale o nella fattura.) |

### <a name="core-networks"></a>Reti core
Il team di gestione dei rischi e della sicurezza delle informazioni di Contoso ETS esamina il piano proposto da Dave per spostare l'applicazione in Azure. Desidera assicurarsi che l'applicazione Carta fedeltà sia correttamente isolata e protetta in una rete perimetrale.  Per soddisfare questo requisito, Dave e Alice creano una rete virtuale esterna e un gruppo di sicurezza di rete per isolare l'applicazione Carta fedeltà dalla rete aziendale Contoso.  

Per la **sottoscrizione di sviluppo**, creano:

| Tipo di risorsa | Nome | Descrizione |
| --- | --- | --- |
| Rete virtuale |vnInternal |È destinata all'ambiente di sviluppo della carta fedeltà di Contoso ed è connessa tramite ExpressRoute alla rete aziendale di Contoso. |

Per la **sottoscrizione di produzione**, creano:

| Tipo di risorsa | Nome | Descrizione |
| --- | --- | --- |
| Rete virtuale |vnExternal |Ospita l'applicazione Carta fedeltà e non è connessa direttamente a ExpressRoute di Contoso. Il codice viene trasmesso tramite il sistema di codice sorgente direttamente ai servizi PaaS. |
| Gruppo di sicurezza di rete |nsgBitBucket |Garantisce che la superficie di attacco di questo carico di lavoro sia ridotta al minimo consentendo la comunicazione in entrata solo su TCP 443.  Contoso sta indagando anche attraverso un firewall applicazione Web per protezione aggiuntiva. |

### <a name="resource-locks"></a>Blocchi risorse
Dave e Alice si consultano e decidono di aggiungere blocchi di risorse in alcune delle risorse chiave nell'ambiente per impedire l'eliminazione accidentale durante un push del codice fuori controllo.

Creano il blocco seguente:

| Tipo di blocco | Risorsa | Descrizione |
| --- | --- | --- |
| **CanNotDelete** |vnExternal |Per impedire agli utenti di eliminare la rete virtuale o le subnet. Il blocco non impedisce l'aggiunta di nuove subnet. |

### <a name="azure-automation"></a>Automazione di Azure
Alice e il suo team di sviluppo hanno runbook estesi per gestire l'ambiente per l'applicazione. I runbook consentono l'aggiunta o l'eliminazione di nodi per l'applicazione e altre attività DevOps.

Per usare questi runbook, abilitano [Automazione](../automation/automation-intro.md).

### <a name="azure-security-center"></a>Centro sicurezza di Azure
La gestione dei servizi IT di Contoso deve identificare e gestire rapidamente i rischi. Inoltre è necessario capire quali problemi potrebbero esistere.  

Per soddisfare questi requisiti, Dave abilita il Centro sicurezza di Azure. Verifica che il Centro di sicurezza di Azure esegua il monitoraggio delle risorse e fornisce l'accesso ai team di sicurezza e DevOps.

## <a name="next-steps"></a>Passaggi successivi
* Per altri suggerimenti, vedere [Best practices for creating Azure Resource Manager templates](resource-manager-template-best-practices.md) (Procedure consigliate per la creazione di modelli di Azure Resource Manager).




<!--HONumber=Jan17_HO4-->


