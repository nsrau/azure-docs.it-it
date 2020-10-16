---
title: Configurare l'appliance virtuale di rete in Azure HDInsight
description: Informazioni su come configurare una serie di funzionalità aggiuntive per l'appliance virtuale di rete in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: c0f5d8cdc7dda72f21fc1cf372e3796b26a3054a
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92127421"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Configurare l'appliance virtuale di rete in Azure HDInsight

> [!Important]
> Le informazioni seguenti sono necessarie **solo** se si vuole configurare un'appliance virtuale di rete diversa dal [firewall di Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic).

Il tag FQDN del firewall di Azure viene configurato automaticamente per consentire il traffico per molti degli FQDN importanti comuni. Se si usa un'altra appliance virtuale di rete, sarà necessario configurare una serie di funzionalità aggiuntive. Quando si configura l'appliance virtuale di rete, tenere presenti i seguenti fattori:

* Gli endpoint di servizio che supportano i servizi possono essere configurati con gli endpoint di servizio, causando il bypass dell'appliance virtuale di dispositivo, in genere per considerazioni su costi o prestazioni
* Se ResourceProviderConnection è impostato su in *uscita*, è possibile usare endpoint privati per l'archiviazione e i server SQL per i Metastore e non è necessario aggiungerli all'appliance virtuale di sistema.
* Le dipendenze degli indirizzi IP sono per il traffico non HTTP/S (traffico TCP e UDP).
* Gli endpoint HTTP/HTTPS FQDN possono essere approvati nel dispositivo NVA.
* Assegnare la tabella di route creata alla subnet HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Dipendenze con supporto per endpoint di servizio

Facoltativamente, è possibile abilitare uno o più degli endpoint di servizio seguenti che comporteranno il bypass dell'appliance virtuale di dispositivo. Questa opzione può essere utile per grandi quantità di trasferimenti di dati per risparmiare sui costi e anche per le ottimizzazioni delle prestazioni. 

| **Endpoint** |
|---|
| SQL di Azure |
| Archiviazione di Azure |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Dipendenze di indirizzi IP

| **Endpoint** | **Dettagli** |
|---|---|
| Indirizzi IP pubblicati [qui](hdinsight-management-ip-addresses.md) | Questi indirizzi IP sono per il provider di risorse HDInsight e devono essere inclusi nella UDR per evitare il routing asimmetrico. Questa regola è necessaria solo se ResourceProviderConnection è impostato su in *ingresso*. Se ResourceProviderConnection è impostato su in *uscita* , questi IP non sono necessari in UdR.  |
| Indirizzi IP privati di AAD-DS | Necessaria solo per i cluster ESP, se non è stato reti virtuali il peering.|


### <a name="fqdn-httphttps-dependencies"></a>Dipendenze HTTP/HTTPS con nome di dominio completo

È possibile ottenere l'elenco delle dipendenze FQDN (principalmente archiviazione di Azure e il bus di servizio di Azure) per la configurazione dell'appliance virtuale [di sistema in questo repository](https://github.com/Azure-Samples/hdinsight-fqdn-lists/). Per l'elenco delle aree, vedere [qui](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/master/Regional). Queste dipendenze vengono usate da HDInsight Resource Provider (RP) per creare e monitorare/gestire correttamente i cluster. Sono inclusi i dati di telemetria/diagnostica, i metadati di provisioning, le configurazioni correlate ai cluster, gli script e così via. Questo elenco di dipendenze FQDN può cambiare con il rilascio di aggiornamenti HDInsight futuri.

L'elenco seguente fornisce solo alcuni FQDN che potrebbero essere necessari per l'applicazione di patch per il sistema operativo e per la sicurezza o le convalide dei certificati *dopo* la creazione del cluster e durante il ciclo di vita delle operazioni del cluster:

| **FQDN dipendenze di runtime**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>Passaggi successivi

* [Usare il firewall per limitare il traffico in uscita](./hdinsight-restrict-outbound-traffic.md)
* [Architettura della rete virtuale di HDInsight](hdinsight-virtual-network-architecture.md)
