---
title: Configurare l'appliance virtuale di rete in Azure HDInsight
description: Informazioni su come configurare una serie di funzionalità aggiuntive per l'appliance virtuale di rete in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: cbc2104ae3c55ae3670867b7a253d812f3a4be0e
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864708"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Configurare l'appliance virtuale di rete in Azure HDInsight

> [!Important]
> Le informazioni seguenti sono necessarie **solo** se si vuole configurare un'appliance virtuale di rete diversa dal firewall di Azure.

Il firewall di Azure viene configurato automaticamente per consentire il traffico per molti degli scenari importanti comuni. Se si usa un'altra appliance virtuale di rete, sarà necessario configurare una serie di funzionalità aggiuntive. Quando si configura l'appliance virtuale di rete, tenere presenti i seguenti fattori:

* Con gli endpoint di servizio devono essere configurati servizi che supportano endpoint di servizio.
* Le dipendenze degli indirizzi IP sono per il traffico non HTTP/S (traffico TCP e UDP).
* Gli endpoint HTTP/HTTPS FQDN possono essere inseriti nel dispositivo appliance virtuale di dispositivi.
* Gli endpoint HTTP/HTTPS con caratteri jolly sono dipendenze che possono variare in base a un numero di qualificatori.
* Assegnare la tabella di route creata alla subnet HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Dipendenze con supporto per endpoint di servizio

| **Endpoint** |
|---|
| SQL di Azure |
| Archiviazione di Azure |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Dipendenze di indirizzi IP

| **Endpoint** | **Dettagli** |
|---|---|
| \*:123 | Controllo dell'orologio NTP. Il traffico viene verificato in più endpoint sulla porta 123. |
| Indirizzi IP pubblicati [qui](hdinsight-management-ip-addresses.md) | Questi indirizzi IP sono servizio HDInsight |
| Indirizzi IP privati AAD-DS per i cluster ESP |
| \*: 16800 per l'attivazione di Windows KMS |
| \*12000 per Log Analytics |

### <a name="fqdn-httphttps-dependencies"></a>Dipendenze HTTP/HTTPS con nome di dominio completo

> [!Important]
> L'elenco seguente fornisce solo alcuni dei nomi di dominio completi più importanti. È possibile ottenere altri FQDN (principalmente archiviazione di Azure e il bus di servizio di Azure) per la configurazione dell'appliance virtuale [di sistema in questo file](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Endpoint**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Passaggi successivi

* [Usare il firewall per limitare il traffico in uscita](./hdinsight-restrict-outbound-traffic.md)
* [Architettura della rete virtuale di Azure HDInsight](hdinsight-virtual-network-architecture.md)
