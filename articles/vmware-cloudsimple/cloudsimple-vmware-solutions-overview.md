---
title: Azure VMware Solutions (AVS)-Panoramica
description: Informazioni su funzionalità, scenari e vantaggi della soluzione VMware in Azure tramite AVS Service.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5de5a11f520a6882bb474e9926ad370bf330be1e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024875"
---
# <a name="what-is-azure-vmware-solutions-avs"></a>Informazioni sulle soluzioni VMware di Azure (AVS)

La **soluzione VMware di Azure (AVS)** è un servizio completamente gestito che consente di eseguire la piattaforma VMware in Azure. Questa soluzione include vSphere, vCenter, rete VSAN, NSX-T e gli strumenti corrispondenti. L'ambiente VMware viene eseguito in modalità nativa nell'infrastruttura bare metal di Azure in posizioni cloud di Azure. Il servizio include tutte le funzionalità necessarie per utilizzare le piattaforme VMware in modo efficiente e sicuro.

![Panoramica della soluzione VMware in Azure by AVS](media/azure-vmware-solution-by-cloudsimple.png)

## <a name="features"></a>database elastico

* Provisioning self-service su richiesta di ambienti cloud VMware. Possibilità di aggiungere e rimuovere capacità su richiesta.
* Distribuzione della piattaforma VMware, aggiornamento, backup del piano di gestione, monitoraggio dello stato e della capacità, avvisi, risoluzione dei problemi e correzione.
* Servizi di rete di sottolineatura necessari per abilitare VMware, inclusi i servizi L2/L3 e la gestione delle regole del firewall.
* Servizi di rete di tipo Edge, tra cui VPN, IP pubblico e gateway Internet. Questi servizi vengono eseguiti in Azure e includono la protezione e la protezione DDoS di Azure.
* Prenotazione della capacità per ridurre i costi.
* Connettività ad alta velocità e bassa latenza ad Azure e in locale.
* Architetture di soluzioni per i clienti che utilizzano i servizi di Azure in modo integrato e sfruttano l'architettura "cloud VMware in un cloud pubblico". I servizi di Azure includono Azure AD, archiviazione, gateway applicazione e altri.
* Infrastruttura completamente dedicata all'utente ed è fisicamente isolata dall'infrastruttura di altri clienti.
* Funzionalità di gestione quali gestione delle attività, utilizzo, fatturazione/misurazione e gestione degli utenti.
* supporto tecnico 24x7.

## <a name="benefits"></a>Vantaggi

* **Continuità operativa**. AVS offre accesso nativo alle piattaforme VMware. L'architettura AVS è compatibile con la tua esistente:
    * Applicazioni
    * Operazioni
    * Sicurezza
    * Eseguire il backup
    * Ripristino di emergenza
    * Audit
    * Strumenti di conformità
    * Procedure
* **Nessuna**ripetizione del training. La compatibilità della piattaforma VMware consente di utilizzare competenze e conoscenze esistenti.
* **Agilità dell'infrastruttura**. Non è più necessario prevedere tutte le esigenze in termini di capacità e quindi si verifica una perdita di capacità o di infrastruttura. AVS viene fornito come servizio cloud ed è possibile aggiungere o ridurre la capacità in qualsiasi momento
* **Sicurezza**. L'accesso all'ambiente AVS tramite Azure fornisce protezione DDoS incorporata e monitoraggio della sicurezza.
* **Costo inferiore**. La piattaforma AVS è altamente progettata e fornisce livelli elevati di automazione, efficienza operativa e economie di scalabilità. AVS pubblica inoltre le architetture delle soluzioni che sfruttano la presenza di VMware in un cloud pubblico per ridurre i costi. Gli esempi includono Azure AD, backup in archiviazione di Azure, gateway applicazione, bilanciamento del carico e altro ancora.
* **Una nuova piattaforma ibrida**. Il servizio consente un accesso ad alta velocità e a bassa latenza al resto di Azure. AVS Management consente inoltre la gestione unificata di macchine virtuali VMware e il resto di Azure usando la stessa interfaccia utente e la stessa API. I team di sviluppo possono sfruttare le piattaforme pubbliche e private in modo integrato e coerente.
* **Monitoraggio dell'infrastruttura, risoluzione dei problemi e supporto**. AVS gestisce l'infrastruttura sottostante come servizio. Errore hardware viene sostituito automaticamente. È possibile concentrarsi sul consumo mentre AVS garantisce che l'ambiente venga eseguito senza problemi.
* **Compatibilità dei criteri**. Mantieni gli strumenti, le procedure di sicurezza, le procedure di controllo e le certificazioni di conformità basati su VMware.

## <a name="scenarios"></a>Scenari

* **Ritiro o migrazione del Data Center**. Ottenere capacità aggiuntiva quando si raggiungono i limiti del data center esistente o si aggiorna l'hardware. È facile aggiungere la capacità necessaria nel cloud ed eliminare i problemi di gestione degli aggiornamenti dell'hardware. Ridurre i rischi e i costi delle migrazioni cloud rispetto alle conversioni o alla riarchitettura dispendiose in termini di tempo. Usa strumenti e competenze VMware comuni per accelerare le migrazioni cloud. Nel cloud usare i servizi di Azure per modernizzare le applicazioni in modo più veloce.
* **Espandi su richiesta**. Espandersi nel cloud per soddisfare le esigenze impreviste, ad esempio nuovi ambienti di sviluppo o picchi di capacità stagionali. Puoi creare con facilità una nuova capacità su richiesta e mantenerla solo finché ti serve. Riduci gli investimenti iniziali, accelera la velocità di provisioning e Riduci la complessità con la stessa architettura e criteri sia in locale che nel cloud.
* **Ripristino di emergenza e desktop virtuali nel cloud di Azure**. Consente di stabilire l'accesso remoto ai dati, alle app e ai desktop nel cloud di Azure. Con connessioni a larghezza di banda elevata, è possibile caricare e scaricare i dati velocemente per il ripristino dagli eventi imprevisti. Le reti a bassa latenza offrono tempi di risposta rapidi che gli utenti si aspettano da un'app desktop. Con AVS è facile replicare tutti i criteri e le funzionalità di rete nel cloud usando il portale AVS e gli strumenti di VMware noti. La facilità di ripristino e di replica riduce notevolmente l'impegno e il rischio di creare e gestire le implementazioni di DR e VDI.
* **Applicazioni e database ad alte prestazioni**. AVS fornisce un'architettura iperconvergente progettata per eseguire i carichi di lavoro VMware più complessi. Eseguire Oracle, Microsoft SQL Server, i sistemi middleware e i database no-SQL ad alte prestazioni. Scopri il cloud come data center con connessioni di rete a 25 Gbps ad alta velocità che ti permettono di eseguire app ibride che si estendono in locale, VMware in Azure e carichi di lavoro privati di Azure senza compromettere le prestazioni.
* **Vero ibrido**. Unificare DevOps tra VMware e Azure. Ottimizza l'amministrazione VMware per i servizi e le soluzioni di Azure che possono essere applicati a tutti i tuoi carichi di lavoro. Accedi ai servizi cloud pubblici senza dover espandere il data center o riprogettare le tue applicazioni. Centralizzare le identità, i criteri di controllo degli accessi, la registrazione e il monitoraggio per le applicazioni VMware in Azure.

![Scenari](media/cloudsimple-scenarios.png)

## <a name="next-steps"></a>Passaggi successivi

* [Crea servizio AVS](quickstart-create-cloudsimple-service.md)
* [Creare un cloud privato AVS](quickstart-create-private-cloud.md)
