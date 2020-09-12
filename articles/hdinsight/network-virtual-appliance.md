---
title: Configurare l'appliance virtuale di rete in Azure HDInsight
description: Informazioni su come configurare una serie di funzionalità aggiuntive per l'appliance virtuale di rete in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 1dcc345260c6a0844344bb33dec94c40d1dd9a54
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650872"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Configurare l'appliance virtuale di rete in Azure HDInsight

> [!Important]
> Le informazioni seguenti sono necessarie **solo** se si vuole configurare un'appliance virtuale di rete diversa dal firewall di Azure.

Il firewall di Azure viene configurato automaticamente per consentire il traffico per molti degli scenari importanti comuni. Se si usa un'altra appliance virtuale di rete, sarà necessario configurare una serie di funzionalità aggiuntive. Quando si configura l'appliance virtuale di rete, tenere presenti i seguenti fattori:

* Gli endpoint di servizio che supportano i servizi possono essere configurati con gli endpoint di servizio, causando il bypass dell'appliance virtuale di dispositivo, in genere per considerazioni su costi o prestazioni
* Le dipendenze degli indirizzi IP sono per il traffico non HTTP/S (traffico TCP e UDP).
* Gli endpoint HTTP/HTTPS FQDN possono essere inclusi nell'elenco elementi consentiti nel dispositivo NVA.
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
| Indirizzi IP pubblicati [qui](hdinsight-management-ip-addresses.md) | Questi indirizzi IP sono per il punto di controllo di HDInsight e devono essere inclusi nella UDR per evitare il routing asimmetrico |
| Indirizzi IP privati di AAD-DS | Necessaria solo per i cluster ESP|


### <a name="fqdn-httphttps-dependencies"></a>Dipendenze HTTP/HTTPS con nome di dominio completo

> [!Important]
> L'elenco seguente fornisce solo alcuni FQDN che potrebbero essere necessari per l'applicazione di patch per il sistema operativo e per la sicurezza o le convalide dei certificati dopo la creazione del cluster e durante il ciclo di vita delle operazioni del cluster. È possibile ottenere l'elenco delle dipendenze FQDN (principalmente archiviazione di Azure e il bus di servizio di Azure) per la configurazione dell'appliance virtuale [di sistema in questo file](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json). Queste dipendenze vengono usate da HDInsight Resource Provider (RP) per creare e monitorare/gestire correttamente i cluster. Sono inclusi i dati di telemetria/diagnostica, i metadati di provisioning, le configurazioni correlate ai cluster, gli script, i modelli ARM e così via. L'elenco di dipendenze FQDN potrebbe cambiare con il rilascio di aggiornamenti HDIngisht futuri.

| **Endpoint**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com:80                                                      |

## <a name="next-steps"></a>Passaggi successivi

* [Usare il firewall per limitare il traffico in uscita](./hdinsight-restrict-outbound-traffic.md)
* [Architettura della rete virtuale di HDInsight](hdinsight-virtual-network-architecture.md)
