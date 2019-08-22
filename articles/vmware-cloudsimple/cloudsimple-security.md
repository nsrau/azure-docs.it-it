---
title: Soluzione VMware di Azure di CloudSimple-sicurezza per i servizi CloudSimple
description: Descrive i modelli di responsabilità condivisa per la sicurezza dei servizi CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f7afd3d4b03c55c6cd9878b7d22106eee371ba99
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877914"
---
# <a name="cloudsimple-security-overview"></a>Panoramica della sicurezza CloudSimple

Questo articolo fornisce una panoramica del modo in cui la sicurezza è implementata nella soluzione VMware di Azure dal servizio CloudSimple, dall'infrastruttura e dal Data Center. Vengono fornite informazioni sulla protezione dei dati e sulla sicurezza, sulla sicurezza della rete e sulle modalità di gestione delle vulnerabilità e delle patch.

## <a name="shared-responsibility"></a>Responsabilità condivisa

La soluzione VMware di Azure di CloudSimple usa un modello di responsabilità condivisa per la sicurezza. Una sicurezza attendibile nel cloud viene realizzata attraverso le responsabilità condivise dei clienti e di Microsoft come provider di servizi. Questa matrice di responsabilità offre una maggiore sicurezza ed Elimina singoli punti di errore.

## <a name="azure-infrastructure"></a>Infrastruttura di Azure

Le considerazioni sulla sicurezza dell'infrastruttura di Azure includono i Data Center e il percorso delle apparecchiature.

### <a name="datacenter-security"></a>Sicurezza del Data Center

Microsoft dispone di un'intera divisione dedicata alla progettazione, compilazione e gestione delle funzionalità fisiche che supportano Azure. Questo team viene usato per mantenere la sicurezza fisica d'avanguardia. Per informazioni dettagliate sulla sicurezza fisica, vedere [funzionalità di Azure, locale e sicurezza fisica](../security/azure-physical-security.md).

### <a name="equipment-location"></a>Località attrezzature

Le attrezzature hardware bare metal che eseguono i cloud privati sono ospitate in località del Data Center di Azure.  Le gabbie in cui si trovino le attrezzature, richiedono l'autenticazione a due fattori basata su biometrica per ottenere l'accesso.

## <a name="dedicated-hardware"></a>Hardware dedicato

Come parte del servizio CloudSimple, tutti i clienti di CloudSimple ottengono host bare metal dedicati con dischi collegati locali che sono fisicamente isolati dall'hardware di altri tenant. Un hypervisor ESXi con rete VSAN viene eseguito in ogni nodo. I nodi vengono gestiti tramite VMware vCenter e NSX dedicati dal cliente. La mancata condivisione dell'hardware tra i tenant offre un ulteriore livello di isolamento e protezione della sicurezza.

## <a name="data-security"></a>Sicurezza dei dati

I clienti mantengono il controllo e la proprietà dei dati. L'amministrazione dei dati dei clienti è responsabilità del cliente.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Protezione dei dati inattivi e dati in movimento nelle reti interne

Per i dati inattivi nell'ambiente cloud privato, è possibile usare la crittografia rete VSAN. la crittografia rete VSAN funziona con i server di gestione delle chiavi esterne (KMS) certificati VMware nella propria rete virtuale o in locale.  Si controllano le chiavi di crittografia dei dati. Per i dati in movimento nel cloud privato, vSphere supporta la crittografia dei dati in transito per tutto il traffico VMkernel (incluso il traffico vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Protezione dei dati per i dati necessari per spostarsi attraverso le reti pubbliche

Per proteggere i dati che si spostano attraverso le reti pubbliche, è possibile creare tunnel VPN IPsec e SSL per i cloud privati. Sono supportati i metodi di crittografia comuni, inclusi AES a 128 byte e 256 byte. I dati in transito (inclusi l'autenticazione, l'accesso amministrativo e i dati dei clienti) vengono crittografati con i meccanismi di crittografia standard (SSH, TLS 1,2 e RDP protetto). Comunicazione che trasporta informazioni riservate utilizza i meccanismi di crittografia standard.

### <a name="secure-disposal"></a>Eliminazione sicura

Se il servizio CloudSimple scade o è terminato, l'utente è responsabile della rimozione o dell'eliminazione dei dati. CloudSimple collaborerà con l'utente per eliminare o restituire tutti i dati dei clienti come indicato nel contratto del cliente, ad eccezione del fatto che CloudSimple è richiesto dalla legge applicabile per conservare alcuni o tutti i dati personali. Se necessario, CloudSimple archivia i dati e implementa misure ragionevoli per impedire ai dati dei clienti di eseguire ulteriori elaborazioni.

### <a name="data-location"></a>Posizione dei dati

Quando si configurano i cloud privati, è possibile scegliere l'area di Azure in cui verranno distribuiti. I dati della macchina virtuale VMware non vengono spostati da tale data center fisico a meno che non si esegua la migrazione dei dati o il backup dei dati fuori sede. È anche possibile ospitare i carichi di lavoro e archiviare i dati in più aree di Azure, se appropriato per le proprie esigenze.

I dati dei clienti residenti nei nodi iperconvergenti del cloud privato non attraversano i percorsi senza l'azione esplicita dell'amministratore tenant. È responsabilità dell'utente implementare i carichi di lavoro in modo a disponibilità elevata.

### <a name="data-backups"></a>Backup dei dati

CloudSimple non esegue il backup o l'archiviazione dei dati dei clienti. CloudSimple esegue un backup periodico dei dati vCenter e NSX per garantire la disponibilità elevata dei server di gestione. Prima del backup, tutti i dati vengono crittografati nell'origine vCenter usando le API VMware. I dati crittografati vengono trasportati e archiviati nel BLOB di Azure. Le chiavi di crittografia per i backup sono archiviate in un insieme di credenziali gestito CloudSimple a sicurezza elevata in esecuzione nella rete virtuale CloudSimple in Azure.

## <a name="network-security"></a>Sicurezza di rete

La soluzione CloudSimple si basa sui livelli di sicurezza della rete.

### <a name="azure-edge-security"></a>Sicurezza di Azure Edge

I servizi CloudSimple sono basati sulla sicurezza di rete di base fornita da Azure. Azure applica tecniche di difesa approfondita per il rilevamento e la risposta tempestiva agli attacchi basati sulla rete associati a modelli di traffico in ingresso o in uscita anomali e attacchi di tipo Denial of Service (DDoS) distribuiti. Questo controllo di sicurezza si applica agli ambienti cloud privati e al software del piano di controllo sviluppato da CloudSimple.

### <a name="segmentation"></a>Segmentazione

Il servizio CloudSimple ha separato logicamente le reti di livello 2 che limitano l'accesso alle proprie reti private nell'ambiente cloud privato. È possibile proteggere ulteriormente le reti cloud private usando un firewall. Il portale di CloudSimple consente di definire le regole per il traffico di rete EW e NS per tutto il traffico di rete, incluso il traffico del cloud privato interno, il traffico di cloud tra più privati, il traffico generale verso Internet e il traffico di rete verso l'ambiente locale tramite VPN IPsec o Connessione ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Vulnerabilità e gestione delle patch

CloudSimple è responsabile della patch di sicurezza periodica del software VMware gestito (ESXi, vCenter e NSX).

## <a name="identity-and-access-management"></a>Gestione delle identità e degli accessi

I clienti possono eseguire l'autenticazione al proprio account Azure (in Azure AD) usando l'autenticazione a più fattori o SSO come preferito. Dal portale di Azure è possibile avviare il portale di CloudSimple senza immettere nuovamente le credenziali.

CloudSimple supporta la configurazione facoltativa di un'origine di identità per il cloud privato vCenter. È possibile usare un' [origine di identità locale](set-vcenter-identity.md), una nuova origine di identità per il cloud privato o [Azure ad](azure-ad.md).

Per impostazione predefinita, ai clienti vengono assegnati i privilegi necessari per le operazioni quotidiane di vCenter all'interno del cloud privato. Questo livello di autorizzazione non include l'accesso amministrativo a vCenter. Se l'accesso amministrativo è temporaneamente necessario, è possibile eseguire l'escalation dei [privilegi](escalate-private-cloud-privileges.md) per un periodo di tempo limitato durante le attività amministrative.
