---
title: Sicurezza dei servizi CloudSimple
description: Vengono descritti i modelli di responsabilità condivisa per la sicurezza dei servizi CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/27/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f5f3fe32e03a9a2bb0186854a83917f8918c6647
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358124"
---
# <a name="cloudsimple-security-overview"></a>Panoramica della sicurezza CloudSimple

Questo articolo fornisce una panoramica del modo in cui viene implementata la sicurezza CloudSimple service, infrastruttura e Data Center.  Informazioni sulla protezione dei dati e sicurezza, sicurezza di rete e gestione delle vulnerabilità e le patch.

## <a name="shared-responsibility"></a>Responsabilità condivisa

Soluzione Azure di VMware da CloudSimple Usa un modello di responsabilità condivisa per la sicurezza. Sicurezza con attendibilità nel cloud avviene attraverso le responsabilità condivise di clienti e Microsoft come provider di servizi. Questa matrice di responsabilità offre una maggiore sicurezza ed elimina i singoli punti di errore. 

## <a name="azure-infrastructure"></a>Infrastruttura di Azure 

Considerazioni sulla sicurezza dell'infrastruttura di Azure includono la posizione di apparecchiature e Data Center. 

### <a name="datacenter-security"></a>Sicurezza dei Data Center 

Microsoft vanta un'intera divisione dedicati alla progettazione, compilazione e gestione le installazioni fisiche che supportano Azure. Questo team viene usato per mantenere la sicurezza fisica d'avanguardia. Per informazioni dettagliate sulla sicurezza fisica, vedere [funzionalità di Azure, ai locali e la sicurezza fisica](https://docs.microsoft.com/azure/security/azure-physical-security).

### <a name="equipment-location"></a>Posizione delle apparecchiature

Le apparecchiature hardware bare metal che esegue il cloud privato sono ospitata in posizioni dei Data Center di Azure.  I cabine in cui tale apparecchiature, richiede l'autenticazione a due fattori basata su biometrica per ottenere l'accesso.

## <a name="dedicated-hardware"></a>Hardware dedicato

Come parte del servizio CloudSimple, tutti i clienti CloudSimple ha dedicato host bare metal bare con dischi locali collegati sono fisicamente isolati da altri componenti hardware di tenant. Un hypervisor ESXi con vSAN viene eseguito in ogni nodo. I nodi vengono gestiti tramite NSX e cliente dedicato VMware vCenter. Non viene condivisa hardware tra i tenant fornisce un ulteriore livello di isolamento e protezione.

## <a name="data-security"></a>Sicurezza dei dati

I clienti di mantenere il controllo e la proprietà dei propri dati. Amministrazione dei dati dei dati dei clienti è responsabilità del cliente.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Protezione dei dati per dati inattivi e i dati in transito all'interno delle reti interne

Per i dati inattivi nell'ambiente di Cloud privato, è possibile usare la crittografia di rete vSAN. la crittografia di rete vSAN funziona con VMware certificata i server di gestione delle chiavi esterne (KMS) nella propria rete virtuale o in locale.  È possibile controllare le chiavi di crittografia dei dati manualmente. Per i dati in transito all'interno del Cloud privato, vSphere supporta la crittografia dei dati trasmessi in rete per tutto il traffico vmkernel (incluso il traffico vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Protezione dei dati per i dati necessari per spostarsi tra le reti pubbliche

Per proteggere i dati che si spostano attraverso reti pubbliche, è possibile creare IPsec e SSL VPN tunnel per i cloud privati. Sono supportati i metodi di crittografia più comuni, tra cui AES 128 e 256 byte. I dati in transito (inclusi autenticazione, l'accesso amministrativo e i dati dei clienti) vengono crittografati con meccanismi di crittografia standard (Secure RDP, SSH e TLS 1.2). La comunicazione che trasporta informazioni riservate vengono utilizzati i meccanismi di crittografia standard.

### <a name="secure-disposal"></a>Eliminazione sicura 

Se il servizio CloudSimple scade o viene terminato, sei responsabile della rimozione o l'eliminazione dei dati. CloudSimple cooperano con l'utente di eliminare o restituire tutti i dati del cliente come specificati nel contratto dei clienti, tranne nella misura massima CloudSimple è richiesto dalla legge applicabile per mantenere alcuni o tutti i dati personali. Se è necessario conservare i dati personali, CloudSimple verrà archiviare i dati e implementare misure ragionevoli per impedire che i dati del cliente un'ulteriore elaborazione.

### <a name="data-location"></a>Posizione dei dati

Quando si configurano i cloud privati, è scegliere l'area di Azure in cui verranno distribuite. Dati della macchina virtuale VMware non vengono spostati dal Data Center fisici a meno che non si esegue la migrazione dei dati o il backup dei dati fuori sede. È anche possibile ospitare i carichi di lavoro e archiviare i dati in più aree di Azure se è adatta alle proprie esigenze.

I dati dei clienti che risiedono in nodi iperconvergenti Cloud privato non attraversa posizioni senza l'azione esplicita dell'amministratore del tenant. È responsabilità dell'utente per implementare i carichi di lavoro in modalità a disponibilità elevata.

### <a name="data-backups"></a>Backup dei dati
CloudSimple non eseguire il backup o archiviare i dati dei clienti. CloudSimple comporta l'esecuzione di backup periodico dei dati NSX e vCenter per garantire un'elevata disponibilità dei server di gestione. Prima del backup, tutti i dati vengono crittografati quando l'origine di vCenter usando APIs VMware. I dati crittografati sono trasportati e archiviati in blob di Azure. Le chiavi di crittografia per i backup vengono archiviate in un archivio gestito a CloudSimple altamente sicuro in esecuzione nella rete virtuale CloudSimple in Azure.

## <a name="network-security"></a>Sicurezza di rete

La soluzione CloudSimple si basa sui livelli di sicurezza di rete.

### <a name="azure-edge-security"></a>Sicurezza di Azure edge

I servizi CloudSimple sono basati sulla protezione di rete di base fornita da Azure. Azure applica tecniche di difesa in profondità per il rilevamento e risposta tempestiva da attacchi basati sulla rete associati con traffico in ingresso anomala o i modelli di traffico in uscita e gli attacchi di distributed denial of service (DDoS). Questo controllo di sicurezza sono valide per gli ambienti Cloud privati e il software del piano di controllo sviluppato da CloudSimple.

### <a name="segmentation"></a>Segmentazione

Il servizio CloudSimple presenta reti logicamente separate di livello 2 che limitano l'accesso alle reti private nel proprio ambiente Cloud privato. È possibile proteggere ulteriormente le reti Private Cloud Usa un firewall. Il portale CloudSimple consente di definire uova e NS rete controlli le regole del traffico per tutto il traffico di rete, tra cui tra il traffico del Cloud privato, il traffico del Cloud privato tra, generale traffico a Internet e di rete del traffico ai servizi locali tramite VPN IPsec o Connessione ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Una vulnerabilità e la gestione delle Patch 

CloudSimple è responsabile per l'applicazione delle patch di sicurezza periodico del software gestito VMware (ESXi vCenter e NSX).

## <a name="identity-and-access-management"></a>Gestione delle identità e degli accessi

I clienti possono eseguire l'autenticazione al proprio account Azure (in Azure AD) usando l'autenticazione a più fattori o SSO come preferito. Dal portale di Azure, è possibile avviare il portale CloudSimple senza immettere di nuovo le credenziali.

CloudSimple supporta la configurazione facoltativa di un'origine di identità per il server vCenter sul Cloud privato. È possibile usare un [origine delle identità in locale](https://docs.azure.cloudsimple.com/set-vcenter-identity), una nuova origine di identità per il Cloud privato, oppure [Azure AD](https://docs.azure.cloudsimple.com/azure-ad).

Per impostazione predefinita, i clienti ha i privilegi necessari per le operazioni quotidiane di vCenter nell'ambito del Cloud privato. Questo livello di autorizzazione non include l'accesso amministrativo a vCenter. Se l'accesso amministrativo sia temporaneamente necessario, è possibile [l'escalation dei privilegi](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) per un periodo limitato fino al completamento di attività amministrative.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un servizio CloudSimple in Azure](quickstart-create-cloudsimple-service.md)
* Informazioni su come [creare un cloud privato](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md)
