---
title: Benchmark di sicurezza di Azure V2-sicurezza di rete
description: Sicurezza di rete di benchmark di sicurezza di Azure V2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9833f63d999ab7c24174853bd37f4e7a76f6dfbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329432"
---
# <a name="security-control-v2-network-security"></a>Controllo di sicurezza V2: sicurezza di rete

La sicurezza di rete copre i controlli per proteggere e proteggere le reti di Azure. Ciò include la protezione delle reti virtuali, la creazione di connessioni private, la prevenzione e la mitigazione degli attacchi esterni e la protezione di DNS.

## <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementare la sicurezza per il traffico interno

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| NS-1 | 9,2, 9,4, 14,1, 14,2, 14,3 | AC-4, CA-3, SC-7 |

Assicurarsi che tutte le reti virtuali di Azure seguano un principio di segmentazione aziendale che sia allineato ai rischi aziendali. Tutti i sistemi che potrebbero comportare un rischio maggiore per l'organizzazione devono essere isolati all'interno della propria rete virtuale e sufficientemente protetti con un gruppo di sicurezza di rete (NSG) e/o un firewall di Azure. 

In base alle applicazioni e alla strategia di segmentazione aziendale, limitare o consentire il traffico tra le risorse interne basate sulle regole del gruppo di sicurezza di rete. Per applicazioni specifiche ben definite, ad esempio un'applicazione a 3 livelli, può trattarsi di un approccio altamente sicuro "Nega per impostazione predefinita, Consenti per eccezione". Questo potrebbe non essere scalabile se si dispone di molte applicazioni ed endpoint che interagiscono tra loro. È anche possibile usare il firewall di Azure in situazioni in cui la gestione centrale è necessaria per un numero elevato di segmenti Enterprise o spoke (in una topologia hub/spoke). 

Usare la protezione avanzata della rete adattiva del Centro sicurezza di Azure per consigliare configurazioni di gruppi di sicurezza di rete che limitano le porte e gli indirizzi IP di origine in base al riferimento alle regole del traffico di rete esterno

Usare Sentinel di Azure per individuare l'uso di protocolli legacy non protetti, ad esempio SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, binding LDAP non firmati e crittografie vulnerabili in Kerberos.

- [Come creare un gruppo di sicurezza di rete con le regole di sicurezza](../../virtual-network/tutorial-filter-network-traffic.md)

- [Come distribuire e configurare il firewall di Azure](../../firewall/tutorial-firewall-deploy-portal.md)

- [Protezione avanzata della rete adattiva nel centro sicurezza di Azure](../../security-center/security-center-adaptive-network-hardening.md)

- [Cartella di lavoro protocolli non protetti di Sentinel di Azure](../../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)     

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-2-connect-private-networks-together"></a>NS-2: connettere le reti private

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| NS-2 | N/D | CA-3, AC-17, MA-4 |

Usare Azure ExpressRoute o la rete privata virtuale (VPN) di Azure per creare connessioni private tra i Data Center di Azure e l'infrastruttura locale in un ambiente di condivisione percorso. Le connessioni ExpressRoute non passano attraverso la rete Internet pubblica e offrono maggiore affidabilità, velocità più elevate e latenze più basse rispetto alle connessioni Internet tipiche. Per la VPN da punto a sito e VPN da sito a sito, è possibile connettere i dispositivi o le reti locali a una rete virtuale usando qualsiasi combinazione di queste opzioni VPN e Azure ExpressRoute.

Per connettere due o più reti virtuali in Azure, usare il peering di rete virtuale o il collegamento privato. Il traffico di rete tra reti virtuali con peering è privato e viene mantenuto nella rete backbone di Azure. 

- [Quali sono i modelli di connettività ExpressRoute](../../expressroute/expressroute-connectivity-models.md) 

- [Panoramica della VPN di Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md)

- [Collegamento privato di Azure](../../private-link/private-link-service-overview.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: stabilire l'accesso alla rete privata per i servizi di Azure

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| NS-3 | 14,1 | AC-4, CA-3, SC-7 |

Usare il collegamento privato di Azure per abilitare l'accesso privato ai servizi di Azure dalle reti virtuali senza attraversare Internet. In situazioni in cui il collegamento privato di Azure non è ancora disponibile, usare gli endpoint del servizio rete virtuale di Azure.  Gli endpoint del servizio rete virtuale di Azure forniscono l'accesso sicuro ai servizi tramite una route ottimizzata sulla rete backbone di Azure.  

L'accesso privato è una misura di difesa aggiuntiva in profondità, oltre all'autenticazione e alla sicurezza del traffico offerti dai servizi di Azure. 

- [Informazioni sul collegamento privato di Azure](../../private-link/private-link-overview.md)

- [Informazioni sugli endpoint del servizio rete virtuale](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteggere le applicazioni e i servizi da attacchi di rete esterni

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| NS-4 | 9,5, 12,3, 12,9 | SC-5, SC-7 |

Proteggi le risorse di Azure da attacchi provenienti da reti esterne, tra cui attacchi DDoS (Distributed Denial of Service), attacchi specifici dell'applicazione e traffico Internet potenzialmente dannoso e indesiderato. Azure include funzionalità native per questo:
-   Usare il firewall di Azure per proteggere le applicazioni e i servizi da traffico potenzialmente dannoso da Internet e da altre posizioni esterne. 

-   Usare le funzionalità di Web Application Firewall (WAF) in applicazione Azure gateway, in front-end di Azure e nella rete per la distribuzione di contenuti (CDN) di Azure per proteggere le applicazioni, i servizi e le API dagli attacchi a livello di applicazione. 

-   Proteggi le tue risorse da attacchi DDoS abilitando la protezione standard DDoS nelle tue reti virtuali di Azure. 
-   Usare il Centro sicurezza di Azure per rilevare i rischi di configurazione errata correlati. 

- [Documentazione di Azure firewall](/azure/firewall/)

- [Come distribuire Azure WAF](../../web-application-firewall/overview.md)

- [Gestire Protezione DDoS di Azure Standard nel portale di Azure](../../virtual-network/manage-ddos-protection.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

Nessuno

## <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: distribuire sistemi di rilevamento intrusione/prevenzione intrusioni (IDS/IP)

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| NS-5 | 12,6, 12,7 | SI-4 |

Usare il filtro basato su Intelligence per le minacce di Azure firewall per inviare avvisi e/o bloccare il traffico da e verso indirizzi IP dannosi noti e domini. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft. Quando è richiesta l'ispezione del payload, è possibile distribuire un sistema di rilevamento delle intrusioni/intrusioni di terze parti (IDS/IPS) da Azure Marketplace con funzionalità di ispezione del payload. In alternativa, è possibile usare ID/IP basati su host o una soluzione di rilevamento e risposta dell'endpoint basato su host in combinazione con o invece di ID/IP basati su rete.  

Nota: in caso di requisiti normativi o di altro genere per l'utilizzo di ID/IP, verificare che sia sempre ottimizzato per fornire avvisi di alta qualità alla soluzione SIEM. 

- [Come distribuire il firewall di Azure](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace include funzionalità per gli ID di terze parti](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Funzionalità di Microsoft Defender ATP EDR](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-6-simplify-network-security-rules"></a>NS-6: semplificare le regole di sicurezza di rete

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| NS-6 | 1.5 | IA-4 |

Semplificare le regole di sicurezza di rete sfruttando i tag di servizio e i gruppi di sicurezza delle applicazioni (gruppi). 

Usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio nel campo di origine o di destinazione di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

È anche possibile usare i gruppi di sicurezza delle applicazioni per semplificare la configurazione della sicurezza complessa. Anziché definire i criteri in base agli indirizzi IP espliciti nei gruppi di sicurezza di rete, i gruppi di sicurezza delle applicazioni consentono di configurare la sicurezza di rete come un'estensione naturale della struttura di un'applicazione, consentendo di raggruppare le macchine virtuali e definire i criteri di sicurezza di rete in base a tali gruppi.

- [Comprendere e usare i tag di servizio](../../virtual-network/service-tags-overview.md)

- [Comprendere e usare i gruppi di sicurezza delle applicazioni](/azure/virtual-network/security-overview#application-security-groups)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Secure Domain Name Service (DNS)

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| NS-7 | N/D | SC-20, SC-21 |

Seguire le procedure consigliate per la sicurezza DNS per attenuare gli attacchi comuni, ad esempio il DNS penzolante, gli attacchi di amplificazione DNS, l'avvelenamento e lo spoofing DNS e così via. 

Quando DNS di Azure viene usato come servizio DNS autorevole, assicurarsi che le zone e i record DNS siano protetti da modifiche accidentali o dannose usando il controllo degli accessi in base al ruolo di Azure 

- [Panoramica di DNS di Azure](../../dns/dns-overview.md)

- [Guida alla distribuzione di Secure Domain Name System (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final)

- [Impedisci le voci DNS in sospeso ed evita l'acquisizione di sottodomini](../fundamentals/subdomain-takeover.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

