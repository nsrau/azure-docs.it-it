---
title: Configurazione dell'infrastruttura del gateway applicazione Azure
description: Questo articolo descrive come configurare l'infrastruttura del gateway applicazione Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: cd1dc953c35233010250bf7f959c94d1de50fe4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319793"
---
# <a name="application-gateway-infrastructure-configuration"></a>Configurazione dell'infrastruttura del gateway applicazione

L'infrastruttura del gateway applicazione include la rete virtuale, le subnet, i gruppi di sicurezza di rete e le route definite dall'utente.

## <a name="virtual-network-and-dedicated-subnet"></a>Rete virtuale e subnet dedicata

Un gateway applicazione è una distribuzione dedicata nella rete virtuale. All'interno della rete virtuale è necessaria una subnet dedicata per il gateway applicazione. È possibile avere più istanze di una determinata distribuzione del gateway applicazione in una subnet. È anche possibile distribuire altri gateway applicazione nella subnet. Tuttavia, non è possibile distribuire altre risorse nella subnet del gateway applicazione. Non è possibile combinare Standard_v2 e il gateway applicazione Azure standard nella stessa subnet.

> [!NOTE]
> I [criteri degli endpoint servizio di rete virtuale](../virtual-network/virtual-network-service-endpoint-policies-overview.md) non sono attualmente supportati in una subnet del gateway applicazione.

### <a name="size-of-the-subnet"></a>Dimensioni della subnet

Il gateway applicazione usa un indirizzo IP privato per istanza, oltre a un altro indirizzo IP privato se è configurato un IP front-end privato.

Azure riserva inoltre cinque indirizzi IP in ogni subnet per uso interno: i primi quattro e gli ultimi indirizzi IP. Si considerino, ad esempio, 15 istanze del gateway applicazione senza IP front-end privato. Sono necessari almeno 20 indirizzi IP per questa subnet: cinque per uso interno e 15 per le istanze del gateway applicazione.

Si consideri una subnet con 27 istanze del gateway applicazione e un indirizzo IP per un indirizzo IP front-end privato. In questo caso sono necessari 33 indirizzi IP: 27 per le istanze del gateway applicazione, uno per il front-end privato e cinque per uso interno.

Lo SKU del gateway applicazione (standard o WAF) può supportare fino a 32 istanze (32 indirizzi IP dell'istanza + 1 IP front-end privato + 5 Azure riservato): è consigliabile usare una dimensione minima della subnet pari a/26

Il gateway applicazione (SKU Standard_v2 o WAF_v2) può supportare fino a 125 istanze (125 indirizzi IP dell'istanza + 1 IP front-end privato + 5 Azure riservato): è consigliabile usare una dimensione minima della subnet pari a/24

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete

I gruppi di sicurezza di rete (gruppi) sono supportati nel gateway applicazione. Esistono tuttavia alcune restrizioni:

- È necessario consentire il traffico Internet in ingresso sulle porte TCP 65503-65534 per lo SKU del gateway applicazione v1 e sulle porte TCP 65200-65535 per lo SKU v2 con la subnet di destinazione impostata su **Qualsiasi** e l'origine impostata sul tag di servizio **GatewayManager**. Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Queste porte sono protette (bloccate) dai certificati di Azure. Le entità esterne, inclusi i clienti di tali gateway, non possono comunicare su questi endpoint.

- La connettività Internet in uscita non può essere bloccata. Le regole in uscita predefinite in NSG consentono la connettività Internet. È consigliabile:

  - Non rimuovere le regole in uscita predefinite.
  - Non creare altre regole in uscita che negano la connettività in uscita.

- È necessario consentire il traffico dal tag **AzureLoadBalancer** con la **subnet di destinazione** .

### <a name="allow-access-to-a-few-source-ips"></a>Consentire l'accesso a un numero limitato di indirizzi IP di origine

Per questo scenario, usare gruppi nella subnet del gateway applicazione. Inserire le restrizioni seguenti sulla subnet in questo ordine di priorità:

1. Consentire il traffico in ingresso da un IP di origine o un intervallo IP con la destinazione come l'intero intervallo di indirizzi della subnet del gateway applicazione e la porta di destinazione come porta di accesso in ingresso, ad esempio la porta 80 per l'accesso HTTP.
2. Consente le richieste in ingresso dall'origine come tag del servizio **GatewayManager** e la destinazione come **qualsiasi** porta di destinazione e come 65503-65534 per lo SKU del gateway applicazione v1 e le porte 65200-65535 per lo SKU V2 per la [comunicazione dello stato di integrità back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Queste porte sono protette (bloccate) dai certificati di Azure. Senza i certificati appropriati, le entità esterne non possono avviare le modifiche in tali endpoint.
3. Consentire i probe di Azure Load Balancer in ingresso (tag*AzureLoadBalancer* ) e il traffico di rete virtuale in ingresso (tag*virtualnetwork* ) nel [gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Bloccare tutto il traffico in ingresso usando una regola Deny-all.
5. Consentire il traffico in uscita verso Internet per tutte le destinazioni.

## <a name="supported-user-defined-routes"></a>Route definite dall'utente supportate 

> [!IMPORTANT]
> L'uso di UdR nella subnet del gateway applicazione può causare lo stato di integrità nella [visualizzazione integrità back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) in modo che venga visualizzato come **sconosciuto**. Potrebbe anche causare un errore di generazione dei log e delle metriche del gateway applicazione. Si consiglia di non usare UdR nella subnet del gateway applicazione per poter visualizzare l'integrità, i log e le metriche del back-end.

- **v1**

   Per lo SKU V1, le route definite dall'utente (UDR) sono supportate nella subnet del gateway applicazione, purché non modifichino la comunicazione di richiesta/risposta end-to-end. Ad esempio, è possibile configurare un UDR nella subnet del gateway applicazione in modo che punti a un'appliance firewall per l'ispezione dei pacchetti. È tuttavia necessario assicurarsi che il pacchetto possa raggiungere la destinazione prevista dopo l'ispezione. In caso contrario, potrebbe verificarsi un comportamento non corretto del probe di integrità o del routing del traffico. Sono incluse le route apprese o le route 0.0.0.0/0 predefinite propagate dai gateway VPN o ExpressRoute di Azure nella rete virtuale. Qualsiasi scenario in cui 0.0.0.0/0 deve essere reindirizzato in locale (tunneling forzato) non è supportato per V1.

- **v2**

   Per lo SKU V2 sono disponibili scenari supportati e non supportati:

   **V2 scenari supportati**
   > [!WARNING]
   > Una configurazione non corretta della tabella di route può causare il routing asimmetrico nel gateway applicazione V2. Assicurarsi che tutto il traffico del piano di gestione/controllo venga inviato direttamente a Internet e non tramite un appliance virtuale. Potrebbero essere interessate anche la registrazione e le metriche.


  **Scenario 1**: UdR per disabilitare la propagazione della route BGP (Border Gateway Protocol) alla subnet del gateway applicazione

   In alcuni casi la route del gateway predefinita (0.0.0.0/0) viene annunciata tramite il ExpressRoute o i gateway VPN associati alla rete virtuale del gateway applicazione. Questo problema interrompe il traffico del piano di gestione, che richiede un percorso diretto a Internet. In questi scenari, è possibile usare un UDR per disabilitare la propagazione di route BGP. 

   Per disabilitare la propagazione della route BGP, attenersi alla procedura seguente:

   1. Creare una risorsa della tabella di route in Azure.
   2. Disabilitare il parametro di **propagazione della route del gateway di rete virtuale** . 
   3. Associare la tabella di route alla subnet appropriata. 

   L'abilitazione di UDR per questo scenario non dovrebbe interrompere le configurazioni esistenti.

  **Scenario 2**: UdR per indirizzare 0.0.0.0/0 a Internet

   È possibile creare un UDR per inviare il traffico 0.0.0.0/0 direttamente a Internet. 

  **Scenario 3**: UdR per il servizio Kubernetes di Azure con kubenet

  Se si usa kubenet con Azure Kubernetes Service (AKS) e il controller di ingresso del gateway applicazione (AGIC), è necessaria una tabella di route per consentire il routing del traffico ai pod dal gateway applicazione al nodo corretto. Questa operazione non è necessaria se si usa Azure CNI. 

  Per usare la tabella di route per consentire il funzionamento di kubenet, attenersi alla procedura seguente:

  1. Passare al gruppo di risorse creato da AKS (il nome del gruppo di risorse deve iniziare con "MC_")
  2. Trovare la tabella di route creata da AKS in tale gruppo di risorse. La tabella di route deve essere popolata con le seguenti informazioni:
     - Il prefisso dell'indirizzo deve essere l'intervallo IP dei pod che si vuole raggiungere in AKS. 
     - Il tipo di hop successivo deve essere appliance virtuale. 
     - L'indirizzo hop successivo deve essere l'indirizzo IP del nodo che ospita i pod.
  3. Associare la tabella di route alla subnet del gateway applicazione. 
    
  **V2 scenari non supportati**

  **Scenario 1**: UdR per appliance virtuali

  Qualsiasi scenario in cui 0.0.0.0/0 deve essere reindirizzato tramite qualsiasi appliance virtuale, rete virtuale hub/spoke o locale (tunneling forzato) non è supportato per V2.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [sulla configurazione degli indirizzi IP front-end](configuration-front-end-ip.md).