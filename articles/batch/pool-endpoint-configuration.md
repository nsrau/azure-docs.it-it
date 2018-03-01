---
title: Configurare gli endpoint del nodo nel pool di Azure Batch | Microsoft Docs
description: Come configurare o disabilitare l'accesso alle porte SSH o RDP nei nodi di calcolo in un pool di Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: danlep
ms.openlocfilehash: fdc68744406c3e995a2764f93d4474b807337ff5
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Configurare o disabilitare l'accesso remoto ai nodi di calcolo in un pool di Azure Batch

Per impostazione predefinita, Batch consente a un [utente del nodo](/rest/api/batchservice/computenode/adduser) con connettività di rete di connettersi esternamente a un nodo di calcolo in un pool di Batch. Ad esempio, un utente può connettersi tramite Desktop remoto, ovvero RDP, sulla porta 3389 a un nodo di calcolo in un pool di Windows. Analogamente, per impostazione predefinita, un utente può connettersi tramite Secure Shell, ovvero SSH, sulla porta 22 a un nodo di calcolo in un pool di Linux. 

Nell'ambiente in uso potrebbe essere necessario limitare o disabilitare queste impostazioni di accesso esterno predefinite. È possibile modificarle usando le API di Batch per impostare la proprietà [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration). 

## <a name="about-the-pool-endpoint-configuration"></a>Informazioni sulla configurazione dell'endpoint del pool
La configurazione dell'endpoint è costituita da uno o più [pool Network Address Translation](/rest/api/batchservice/pool/add#inboundnatpool) delle porte front-end. Non confondere un pool NAT con il pool di Batch dei nodi di calcolo. Impostare ciascun pool NAT affinché sostituisca le impostazioni di connessione predefinite nei nodi di calcolo del pool. 

Ogni configurazione del pool NAT include una o più [regole del gruppo di sicurezza di rete](/rest/api/batchservice/pool/add#networksecuritygrouprule). Ogni regola del gruppo di sicurezza di rete consente o rifiuta un determinato traffico di rete all'endpoint. È possibile scegliere di consentire o rifiutare tutto il traffico, il traffico identificato da un [tag predefinito](../virtual-network/virtual-networks-nsg.md#default-tags), ad esempio "Internet", o il traffico proveniente da specifici indirizzi IP o subnet.

### <a name="considerations"></a>Considerazioni
* La configurazione dell'endpoint del pool fa parte della [configurazione di rete](/rest/api/batchservice/pool/add#NetworkConfiguration) del pool. La configurazione di rete può includere facoltativamente impostazioni per aggiungere il pool a una [rete virtuale di Azure](batch-virtual-network.md). Se si configura il pool in una rete virtuale, è possibile creare le regole del gruppo di sicurezza di rete che usano le impostazioni dell'indirizzo nella rete virtuale.
* Quando si configura un pool NAT, è possibile configurare più regole del gruppo di sicurezza di rete. Le regole vengono controllate in ordine di priorità. Dopo che è stata applicata una regola, non viene verificata la corrispondenza di altre regole.


## <a name="example-deny-all-rdp-traffic"></a>Esempio: rifiutare tutto il traffico RDP

Il frammento di codice C# seguente mostra come configurare l'endpoint RDP sui nodi di calcolo in un pool di Windows per rifiutare tutto il traffico di rete. L'endpoint usa un pool front-end di porte compreso nell'intervallo *60000 - 60099*. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Esempio: rifiutare il traffico SSH da Internet

Il frammento di codice Python seguente mostra come configurare l'endpoint SSH sui nodi di calcolo in un pool di Linux per rifiutare tutto il traffico di rete. L'endpoint usa un pool front-end di porte compreso nell'intervallo *4000 - 4100*. 

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                source_address_prefix='Internet'
                )
            ]
        )
        ]
    ) 
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Esempio: consentire il traffico RDP da un indirizzo IP specifico

Il frammento di codice C# seguente mostra come configurare l'endpoint RDP sui nodi di calcolo in un pool di Windows per consentire l'accesso RDP solo dall'indirizzo IP *198.51.100.7*. La seconda regola del gruppo di sicurezza di rete rifiuta il traffico che non corrisponde all'indirizzo IP.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Esempio: consentire il traffico SSH da una subnet specifica

Il frammento di codice Python seguente mostra come configurare l'endpoint SSH sui nodi di calcolo in un pool di Linux per consentire l'accesso solo dalla subnet *192.168.1.0/24*. La seconda regola del gruppo di sicurezza di rete rifiuta il traffico che non corrisponde alla subnet.

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access='allow',
                source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                priority=175,
                access='deny',
                source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle regole del gruppo di sicurezza di rete, vedere [Filtrare il traffico di rete con gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

- Per una panoramica dettagliata di Batch, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).

