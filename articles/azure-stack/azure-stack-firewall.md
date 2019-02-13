---
title: I sistemi integrati di firewall di Azure Stack pianificazione per Azure Stack | Microsoft Docs
description: Descrive le considerazioni firewall di Azure Stack per le distribuzioni basate su Azure Stack di Azure a più nodi.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 023201d221ee5d7ec884c6a760407e8da8340d3f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207123"
---
# <a name="azure-stack-firewall-integration"></a>Integrazione di Azure Stack firewall
È consigliabile utilizzare un dispositivo firewall per consentire sicuro Azure Stack. I firewall possono difesa contro le cose come gli attacchi distributed denial of service (DDOS), il rilevamento delle intrusioni e l'ispezione del contenuto. Tuttavia, possono anche diventare un collo di bottiglia della velocità effettiva per i servizi di archiviazione di Azure, ad esempio BLOB, tabelle e code.

 Se viene utilizzata una modalità di distribuzione disconnesso, è necessario pubblicare l'endpoint di AD FS. Per altre informazioni, vedere la [articolo di identità di integrazione di Data Center](azure-stack-integrate-identity.md).

Il Azure Resource Manager (amministratore), portale per gli amministratori e gli endpoint di Key Vault (amministratore) non richiedono necessariamente pubblicazione esterna. Come provider di servizi, ad esempio, è possibile limitare la superficie di attacco dall'amministrazione di Azure Stack da solo all'interno della rete e non da internet.

Per le organizzazioni aziendali, la rete esterna può essere la rete azienda esistente. In questo scenario, è necessario pubblicare gli endpoint per il funzionamento di Azure Stack dalla rete aziendale.

### <a name="network-address-translation"></a>Network Address Translation
Network Address Translation (NAT) è il metodo consigliato per consentire la macchina virtuale di distribuzione (DVM) per accedere alle risorse esterne e internet durante la distribuzione, nonché le macchine virtuali alla Console di ripristino di emergenza (ERCS) o Privileged punto finale (PEP) durante registrazione e risoluzione dei problemi.

NAT può essere anche un'alternativa per gli indirizzi IP pubblici nella rete esterna o degli indirizzi VIP pubblici. Tuttavia, è consigliabile non eseguire questa operazione, perché consente di limitare l'esperienza utente di tenant e aumenta la complessità. Un'opzione potrebbe essere un dispositivo NAT da uno a uno che richiede comunque un indirizzo IP pubblico per ogni indirizzo IP utente nel pool. Un'altra opzione è molti-a NAT che richiede una regola NAT per ogni utente VIP per tutte le porte utilizzabili dall'utente.

Alcuni degli svantaggi dell'uso di NAT per indirizzo VIP pubblico sono:
- NAT aggiunge un overhead quando si gestiscono le regole del firewall perché gli utenti di controllare i propri endpoint e le proprie regole di pubblicazione definita dal software (SDN) dello stack di rete. Gli utenti devono contattare l'operatore di Azure Stack per ottenere i VIP nei servizi pubblicate e per aggiornare l'elenco di porte.
- Durante l'utilizzo NAT consente di limitare l'esperienza utente, offre controllo completo all'operatore tramite le richieste di pubblicazione.
- Per scenari di cloud ibrido con Azure, prendere in considerazione che Azure non supporta l'impostazione di un tunnel VPN a un endpoint tramite NAT.

### <a name="ssl-decryption"></a>Decrittografia SSL
È attualmente consigliabile disabilitare la decrittografia SSL in tutto il traffico di Azure Stack. Se è supportata negli aggiornamenti futuri, saranno fornite indicazioni su come abilitare la decrittografia SSL per Azure Stack.

## <a name="edge-firewall-scenario"></a>Scenario di firewall perimetrale
In una distribuzione edge, Azure Stack viene distribuito direttamente dietro il firewall o il router perimetrale. In questi scenari, è supportata per il firewall deve essere sopra il bordo (Scenario 1) in cui supporta sia le configurazioni di firewall attivo-attivo e attivo-passivo o che agisce come dispositivo di bordo (Scenario 2) in cui supporta solo firewall attivo-attivo configurazione di basarsi su uguale Cost Multi Path ECMP () con BGP o con routing statico per il failover.

Gli indirizzi IP instradabili pubblici vengono specificati per il pool di indirizzi VIP pubblico dalla rete esterna in fase di distribuzione. In uno scenario di edge, è consigliabile non usare indirizzi IP instradabili pubblici su qualsiasi altro tipo di rete per motivi di sicurezza. Questo scenario consente a un utente provare l'esperienza del cloud controllato in autonomia completo come in un cloud pubblico come Azure.  

![Esempio di Azure Stack edge firewall](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Enterprise intranet o perimetrale scenario firewall di rete
In una distribuzione di intranet o perimetrale aziendale, Azure Stack viene distribuito su un firewall multi-suddividere in zone o tra il firewall perimetrali e il firewall di rete aziendale interna. Il traffico viene quindi distribuito tra la rete perimetrale, protetta (o rete Perimetrale) e le zone non protette come descritto di seguito:

- **Area protetta**: Si tratta della rete interna che usa indirizzi IP instradabili aziendali o interni. La rete protetta può essere suddiviso, hanno accesso in uscita a internet tramite NAT sul Firewall e viene in genere accessibile da un punto qualsiasi all'interno del Data Center attraverso la rete interna. Tutte le reti di Azure Stack devono trovarsi in un'area protetta, ad eccezione di pool di indirizzi VIP pubblici della rete esterna.
- **Area perimetrale**. Rete perimetrale è dove esterni o per le applicazioni, ad esempio server Web sono in genere distribuiti internet. In genere viene monitorato da un firewall per evitare gli attacchi DDoS e intrusioni (pirateria) consentendo comunque specificato il traffico in ingresso da internet. Solo il rete esterna pubblico pool VIP di Azure Stack deve trovarsi nell'area di rete Perimetrale.
- **Zona non sicuro**. Si tratta della rete esterna, internet. Si **non è** consigliabile distribuire Azure Stack nella zona senza protezione.

![Esempio di rete di Azure Stack perimetrale](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>Altre informazioni
Altre informazioni sulle [porte e protocolli usati dagli endpoint di Azure Stack](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Passaggi successivi
[Requisiti di infrastruttura a chiave pubblica dello Stack di Azure](azure-stack-pki-certs.md)

