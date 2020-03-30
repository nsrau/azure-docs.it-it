---
title: Azure VMware Solution by CloudSimple - Security for CloudSimple Services
description: Descrive i modelli di responsabilità condivisa per la sicurezza dei servizi CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1a33e20ec540a05885eb13a3828d28ffc9923fff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024994"
---
# <a name="cloudsimple-security-overview"></a>Panoramica della sicurezza di CloudSimple

Questo articolo offre una panoramica dell'implementazione della sicurezza nel servizio, nell'infrastruttura e nel data center di Azure VMware. Vengono in trar' informazioni sulla protezione e la sicurezza dei dati, sulla sicurezza della rete e sulla modalità di gestione delle vulnerabilità e delle patch.

## <a name="shared-responsibility"></a>Responsabilità condivisa

Azure VMware Solution di CloudSimple usa un modello di responsabilità condivisa per la sicurezza. La sicurezza affidabile nel cloud si ottiene attraverso le responsabilità condivise dei clienti e di Microsoft come provider di servizi. Questa matrice di responsabilità fornisce una maggiore sicurezza ed elimina singoli punti di errore.

## <a name="azure-infrastructure"></a>Infrastruttura di Azure

Le considerazioni sulla sicurezza dell'infrastruttura di Azure includono i data center e la posizione delle apparecchiature.

### <a name="datacenter-security"></a>Sicurezza dei data center

Microsoft has an entire division devoted to designing, building, and operating the physical facilities that support Azure. Questo team viene usato per mantenere la sicurezza fisica d'avanguardia. Per informazioni dettagliate sulla sicurezza fisica, vedere [Strutture, locali e sicurezza fisica](../security/azure-physical-security.md)di Azure.

### <a name="equipment-location"></a>Ubicazione dell'attrezzatura

L'apparecchiatura hardware bare metal che esegue i cloud privati è ospitata nelle posizioni dei data center di Azure.The bare metal hardware equipment that runs your Private Clouds is hosted in Azure datacenter locations.  Le gabbie in cui si trova tale apparecchiatura, richiedono l'autenticazione biometrica a due fattori per ottenere l'accesso.

## <a name="dedicated-hardware"></a>Hardware dedicato

Come parte del servizio CloudSimple, tutti i clienti CloudSimple ottengono host bare metal dedicati con dischi collegati locali fisicamente isolati da altri componenti hardware tenant. Un hypervisor ESXi con vSAN viene eseguito su ogni nodo. I nodi vengono gestiti tramite VMware vCenter e NSX dedicati al cliente. La condivisione dell'hardware tra tenant offre un ulteriore livello di isolamento e protezione.

## <a name="data-security"></a>Sicurezza dei dati

I clienti mantengono il controllo e la proprietà dei propri dati. La gestione dei dati dei dati dei clienti è responsabilità del cliente.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Protezione dei dati per i dati inattivi e i dati in movimento all'interno delle reti interne

Per i dati inattivi nell'ambiente Cloud privato, è possibile usare la crittografia vSAN. La crittografia vSAN funziona con i server di gestione delle chiavi esterne (KMS) certificati VMware nella propria rete virtuale o in locale.  È possibile controllare le chiavi di crittografia dei dati manualmente. Per i dati in movimento all'interno del cloud privato, vSphere supporta la crittografia dei dati in rete per tutto il traffico vmkernel (incluso il traffico vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Protezione dei dati per i dati necessari per spostarsi tra le reti pubbliche

Per proteggere i dati che si spostano attraverso reti pubbliche, è possibile creare tunnel IPsec e VPN SSL per i cloud privati. Sono supportati metodi di crittografia comuni, tra cui AES a 128 e 256 byte. I dati in transito (inclusi l'autenticazione, l'accesso amministrativo e i dati dei clienti) vengono crittografati con meccanismi di crittografia standard (SSH, TLS 1.2 e Secure RDP). La comunicazione che trasporta informazioni riservate utilizza i meccanismi di crittografia standard.

### <a name="secure-disposal"></a>Smaltimento sicuro

Se il servizio CloudSimple scade o viene terminato, l'utente è responsabile della rimozione o dell'eliminazione dei dati. CloudSimple collaborerà con voi per eliminare o restituire tutti i dati dei clienti forniti nel contratto con il cliente, ad eccezione della misura in cui CloudSimple è richiesto dalla legge applicabile per conservare alcuni o tutti i dati personali. Se necessario conservare i dati personali, CloudSimple archivierà i dati e implementerà misure ragionevoli per impedire ai dati dei clienti di qualsiasi ulteriore elaborazione.

### <a name="data-location"></a>Posizione dei dati

Quando si configurano i cloud privati, si sceglie l'area di Azure in cui verranno distribuiti. I dati delle macchine virtuali VMware non vengono spostati da tale data center fisico a meno che non si esegua la migrazione dei dati o il backup dei dati fuori sede. È anche possibile ospitare carichi di lavoro e archiviare i dati all'interno di più aree di Azure, se appropriato per le proprie esigenze.

I dati dei clienti residenti nei nodi convergenti private cloud non attraversano le posizioni senza l'azione esplicita dell'amministratore tenant. È responsabilità dell'utente implementare i carichi di lavoro in modo altamente disponibile.

### <a name="data-backups"></a>Backup di dati

CloudSimple non esegue il backup o non archivia i dati dei clienti. CloudSimple esegue il backup periodico dei dati vCenter e NSX per garantire un'elevata disponibilità dei server di gestione. Prima del backup, tutti i dati vengono crittografati nell'origine vCenter utilizzando le API VMware. I dati crittografati vengono trasportati e archiviati nel BLOB di Azure.The encrypted data is transported and stored in Azure blob. Le chiavi di crittografia per i backup vengono archiviate in un insieme di credenziali gestito CloudSimple altamente sicuro in esecuzione nella rete virtuale CloudSimple in Azure.Encryption keys for backups are stored in a highly secure CloudSimple managed vault running in the CloudSimple virtual network in Azure.

## <a name="network-security"></a>Sicurezza di rete

La soluzione CloudSimple si basa su livelli di sicurezza di rete.

### <a name="azure-edge-security"></a>Sicurezza perimetrale di AzureAzure edge security

I servizi CloudSimple si basano sulla sicurezza di rete di base fornita da Azure.The CloudSimple services are built on the base network security provided by Azure. Azure applica tecniche di difesa in profondità per il rilevamento e la risposta tempestiva agli attacchi basati sulla rete associati a modelli di traffico in ingresso o in uscita anomali e attacchi DDoS (Distributed Denial of Service). Questo controllo di sicurezza si applica agli ambienti Private Cloud e al software del piano di controllo sviluppato da CloudSimple.

### <a name="segmentation"></a>Segmentazione

Il servizio CloudSimple dispone di reti di livello 2 logicamente separate che limitano l'accesso alle proprie reti private nell'ambiente Private Cloud. È possibile proteggere ulteriormente le reti Private Cloud utilizzando un firewall. Il portale CloudSimple consente di definire le regole di controllo del traffico di rete EW e NS per tutto il traffico di rete, incluso il traffico intra Cloud privato, il traffico inter-private Cloud, il traffico generale verso Internet e il traffico di rete verso l'ambiente locale tramite IPsec VPN o Connessione ExpressRoute.ExpressRoute connection.

## <a name="vulnerability-and-patch-management"></a>Vulnerabilità e gestione delle patch

CloudSimple è responsabile dell'applicazione periodica dell'applicazione di patch di sicurezza del software VMware gestito (ESXi, vCenter e NSX).

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

I clienti possono eseguire l'autenticazione al proprio account Azure (in Azure AD) usando l'autenticazione a più fattori o SSO come preferito. Dal portale di Azure è possibile avviare il portale CloudSimple senza immettere nuovamente le credenziali.

CloudSimple supporta la configurazione facoltativa di un'origine di identità per il cloud privato vCenter. È possibile usare [un'origine identità locale,](set-vcenter-identity.md)una nuova origine identità per il cloud privato o [Azure AD.](azure-ad.md)

Per impostazione predefinita, ai clienti vengono concessi i privilegi necessari per le operazioni quotidiane di vCenter all'interno del cloud privato. Questo livello di autorizzazione non include l'accesso amministrativo a vCenter. Se l'accesso amministrativo è temporaneamente necessario, è possibile [riassegnare i privilegi](escalate-private-cloud-privileges.md) per un periodo limitato durante il completamento delle attività amministrative.
