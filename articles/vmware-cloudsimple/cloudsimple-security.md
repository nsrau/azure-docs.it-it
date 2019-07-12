---
title: Sicurezza dei servizi CloudSimple
description: Vengono descritti i modelli di responsabilità condivisa per la sicurezza dei servizi CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/27/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 325915aae43c905236910382f650730a6daa127a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595331"
---
# <a name="cloudsimple-security-overview"></a>Panoramica della sicurezza CloudSimple

Questo articolo fornisce una panoramica del modo in cui viene implementata la sicurezza la soluzione VMware Azure CloudSimple service, infrastruttura e Data Center. Informazioni sulla protezione dei dati e sicurezza, sicurezza di rete e gestione delle vulnerabilità e le patch.

## <a name="shared-responsibility"></a>Responsabilità condivisa

Soluzione Azure di VMware da CloudSimple Usa un modello di responsabilità condivisa per la sicurezza. Sicurezza con attendibilità nel cloud avviene attraverso le responsabilità condivise di clienti e Microsoft come provider di servizi. Questa matrice di responsabilità offre una maggiore sicurezza ed elimina i singoli punti di errore.

## <a name="azure-infrastructure"></a>Infrastruttura di Azure 

Considerazioni sulla sicurezza dell'infrastruttura di Azure includono la posizione di apparecchiature e Data Center.

### <a name="datacenter-security"></a>Sicurezza dei Data Center 

Microsoft vanta un'intera divisione dedicati alla progettazione, compilazione e gestione le strutture fisiche che supportano Azure. Questo team viene usato per mantenere la sicurezza fisica d'avanguardia. Per altre informazioni sulla sicurezza fisica, vedere [funzionalità di Azure, ai locali e la sicurezza fisica](https://docs.microsoft.com/azure/security/azure-physical-security).

### <a name="equipment-location"></a>Posizione delle apparecchiature

Le apparecchiature hardware bare metal che esegue dei cloud privati sono ospitata in posizioni dei Data Center di Azure. L'autenticazione a due fattori basata su biometria è necessario effettuare l'accesso per i cabine in cui si trovano tale apparecchiature.

## <a name="dedicated-hardware"></a>Hardware dedicato

Come parte del servizio CloudSimple, tutti i clienti CloudSimple ha host bare metal dedicato con dischi locali collegati sono fisicamente isolati da altri componenti hardware di tenant. Un hypervisor ESXi con vSAN viene eseguito in ogni nodo. I nodi vengono gestiti tramite dedicato al cliente VMware vCenter e NSX. Non viene condivisa hardware tra i tenant fornisce un ulteriore livello di isolamento e protezione.

## <a name="data-security"></a>Sicurezza dei dati

I clienti di mantenere il controllo e la proprietà dei propri dati. Amministrazione dei dati dei dati dei clienti è responsabilità del cliente.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Protezione dei dati per dati inattivi e i dati in transito all'interno delle reti interne

Per i dati inattivi nell'ambiente di cloud privato, è possibile usare la crittografia di rete vSAN. la crittografia di rete vSAN funziona con i server di gestione delle chiavi esterne certified VMware (KMS) nella propria rete virtuale o in locale. È possibile controllare le chiavi di crittografia dei dati manualmente. Per i dati in movimento nell'ambito del cloud privato, vSphere supporta la crittografia dei dati trasmessi in rete per tutto il traffico VMkernel, che include il traffico di vMotion.

### <a name="data-protection-for-data-thats-required-to-move-through-public-networks"></a>Protezione dei dati per i dati necessari per spostarsi tra le reti pubbliche

Per proteggere i dati che si spostano attraverso reti pubbliche, è possibile creare IPsec e SSL VPN tunnel per i cloud privati. Sono supportati i metodi di crittografia più comuni, tra cui AES 128 e 256 byte. I dati in transito, che include l'autenticazione, l'accesso amministrativo e i dati del cliente, viene crittografati con meccanismi di crittografia standard, ad esempio SSH e TLS 1.2 RDP sicuro. La comunicazione che trasporta informazioni riservate vengono utilizzati i meccanismi di crittografia standard.

### <a name="secure-disposal"></a>Eliminazione sicura 

Se il servizio CloudSimple scade o viene terminato, ha la responsabilità per la rimozione o l'eliminazione dei dati. CloudSimple coopera pertanto con è possibile eliminare o restituire tutti i dati del cliente come specificati nel contratto dei clienti, tranne nella misura massima CloudSimple è richiesto dalla legge applicabile per mantenere alcuni o tutti i dati personali. Se è necessario conservare i dati personali, CloudSimple archivia i dati e implementa misure ragionevoli per impedire che i dati del cliente un'ulteriore elaborazione.

### <a name="data-location"></a>Posizione dei dati

Quando si configura dei cloud privati, si sceglie l'area di Azure in cui vengono implementate. Dati della macchina virtuale VMware non vero spostati dal Data Center fisici solo se si esegue la migrazione dei dati o il backup dei dati fuori sede. È anche possibile ospitare i carichi di lavoro e archiviare i dati in più aree di Azure se è adatta alle proprie esigenze.

I dati dei clienti che risiedono in nodi di cloud privato iperconvergente non attraversa posizioni senza l'azione esplicita dell'amministratore del tenant. È responsabilità dell'utente per implementare i carichi di lavoro in modalità a disponibilità elevata.

### <a name="data-backups"></a>Backup dei dati
CloudSimple non eseguire il backup o archiviare i dati dei clienti. CloudSimple comporta l'esecuzione di backup periodico dei dati NSX e vCenter per garantire un'elevata disponibilità dei server di gestione. Prima del backup, tutti i dati vengono crittografati quando l'origine di vCenter usando APIs VMware. I dati crittografati sono trasportati e archiviati in un blob di Azure. Le chiavi di crittografia per i backup vengono archiviate in un archivio gestito altamente sicuro CloudSimple che viene eseguito nella rete virtuale CloudSimple in Azure.

## <a name="network-security"></a>Sicurezza di rete

La soluzione CloudSimple si basa sui livelli di sicurezza di rete.

### <a name="azure-edge-security"></a>Sicurezza di Azure edge

I servizi CloudSimple sono basati sulla protezione di rete di base fornita da Azure. Azure applica tecniche di difesa in profondità per il rilevamento e risposta tempestiva da attacchi basati sulla rete associati con traffico in ingresso anomala o i modelli di traffico in uscita e gli attacchi di distributed denial of service (DDoS). Questo controllo di sicurezza sono valide per ambienti cloud privati e il software del piano di controllo sviluppato da CloudSimple.

### <a name="segmentation"></a>Segmentazione

Il servizio CloudSimple presenta reti logicamente separate di livello 2 che limitano l'accesso alle reti private nel proprio ambiente cloud privato. È possibile proteggere ulteriormente le reti cloud privato con un firewall. Nel portale di CloudSimple, si definiscono le regole di controllo del traffico di rete est-ovest e Nord-Sud per tutto il traffico di rete, che include il traffico del cloud privato all'interno della stessa, il traffico del cloud privato tra, generale del traffico a Internet e il traffico di rete in locale tramite connessione VPN IPsec o ExpressRoute di Azure.

## <a name="vulnerability-and-patch-management"></a>Una vulnerabilità e gestione delle patch 

CloudSimple è responsabile per l'applicazione delle patch di sicurezza periodico del software di VMware gestito, ad esempio NSX ESXi e vCenter.

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

I clienti possono eseguire l'autenticazione al proprio account Azure (in Azure Active Directory) mediante l'autenticazione a più fattori o SSO come preferito. Dal portale di Azure, è possibile avviare il portale CloudSimple senza immettere di nuovo le credenziali.

CloudSimple supporta la configurazione facoltativa di un'origine di identità per il cloud privato di vCenter. È possibile usare un [origine delle identità in locale](https://docs.azure.cloudsimple.com/set-vcenter-identity), una nuova origine di identità per il cloud privato, oppure [Azure Active Directory](https://docs.azure.cloudsimple.com/azure-ad).

Per impostazione predefinita, i clienti ha i privilegi necessari per le operazioni quotidiane di vCenter nel cloud privato. Questo livello di autorizzazione non include l'accesso amministrativo a vCenter. Se l'accesso amministrativo sia temporaneamente necessario, è possibile [l'escalation dei privilegi](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) per un periodo limitato fino al completamento di attività amministrative.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un servizio CloudSimple in Azure](quickstart-create-cloudsimple-service.md).
* Informazioni su come [creare un cloud privato](https://docs.azure.cloudsimple.com/create-private-cloud/).
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md).
