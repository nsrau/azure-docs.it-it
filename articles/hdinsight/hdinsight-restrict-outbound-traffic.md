---
title: Configurare la restrizione del traffico di rete in uscita-Azure HDInsight
description: Informazioni su come configurare la restrizione del traffico di rete in uscita per i cluster HDInsight di Azure.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: df691102b565824d6cb6a86f19e6fce3822d8ba8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498133"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>Configurare il traffico di rete in uscita per i cluster HDInsight di Azure usando il firewall (anteprima)

Questo articolo illustra la procedura per proteggere il traffico in uscita dal cluster HDInsight usando il firewall di Azure. I passaggi seguenti presuppongono che si stia configurando un firewall di Azure per un cluster esistente. Se si distribuisce un nuovo cluster e si è protetti da un firewall, creare innanzitutto il cluster HDInsight e la subnet, quindi seguire i passaggi descritti in questa guida.

## <a name="background"></a>Contesto

I cluster HDInsight di Azure vengono in genere distribuiti nella propria rete virtuale. Il cluster ha dipendenze da servizi esterni alla rete virtuale che richiedono l'accesso alla rete per funzionare correttamente.

Esistono diverse dipendenze che richiedono il traffico in ingresso. Il traffico di gestione in ingresso non può essere inviato attraverso un dispositivo firewall. Gli indirizzi di origine per questo traffico sono noti e sono pubblicati [qui](hdinsight-management-ip-addresses.md). È anche possibile creare regole del gruppo di sicurezza di rete (NSG) con queste informazioni per proteggere il traffico in ingresso verso i cluster.

Le dipendenze del traffico in uscita HDInsight sono quasi completamente definite con FQDN, che non dispongono di indirizzi IP statici. La mancanza di indirizzi statici indica che i gruppi di sicurezza di rete (gruppi) non possono essere usati per bloccare il traffico in uscita da un cluster. Gli indirizzi cambiano abbastanza spesso che non è possibile configurare le regole in base alla risoluzione dei nomi corrente e usarle per configurare le regole NSG.

La soluzione per la protezione degli indirizzi in uscita consiste nell'usare un dispositivo firewall in grado di controllare il traffico in uscita in base ai nomi di dominio. Il firewall di Azure può limitare il traffico HTTP e HTTPS in uscita in base al nome di dominio completo dei tag di destinazione o [FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Configurazione del firewall di Azure con HDInsight

Un riepilogo dei passaggi per bloccare l'uscita dal HDInsight esistente con il firewall di Azure è:
1. Creazione di un firewall.
1. Aggiungere regole dell'applicazione al firewall
1. Aggiungere regole di rete al firewall.
1. Creare una tabella di routing.

### <a name="create-a-new-firewall-for-your-cluster"></a>Creare un nuovo firewall per il cluster

1. Creare una subnet denominata **AzureFirewallSubnet** nella rete virtuale in cui è presente il cluster. 
1. Creare un nuovo firewall **test-FW01** usando la procedura descritta in [esercitazione: distribuire e configurare il firewall di Azure usando il portale di Azure](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Configurare il firewall con le regole dell'applicazione

Creare una raccolta di regole dell'applicazione che consenta al cluster di inviare e ricevere comunicazioni importanti.

Selezionare il nuovo firewall **test-FW01** dalla portale di Azure. Fare clic su **regole** in **Impostazioni** > **raccolta regole applicazione** > **Aggiungi raccolta regole applicazione**.

![Title: aggiungere una raccolta di regole dell'applicazione](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

Nella schermata **Aggiungi raccolta regole applicazione** completare i passaggi seguenti:

1. Immettere un **nome**, una **priorità**e fare clic su **Consenti** dal menu a discesa **Azione** e immettere le regole seguenti nella **sezione Tag FQDN** :

   | **Nome** | **Indirizzo di origine** | **Tag FQDN** | **Note** |
   | --- | --- | --- | --- |
   | Rule_1 | * | HDInsight e WindowsUpdate | Obbligatorio per i servizi HDI |

1. Aggiungere le regole seguenti alla **sezione FQDN di destinazione** :

   | **Nome** | **Indirizzo di origine** | **Protocollo: porta** | **FQDN di destinazione** | **Note** |
   | --- | --- | --- | --- | --- |
   | Rule_2 | * | https: 443 | login.windows.net | Consente l'attività di accesso di Windows |
   | Rule_3 | * | https: 443 | login.microsoftonline.com | Consente l'attività di accesso di Windows |
   | Rule_4 | * | https: 443, http: 80 | < storage_account_name. blob. Core. Windows. net > | Se il cluster è supportato da WASB, aggiungere una regola per WASB. Per usare solo le connessioni HTTPS, verificare che l'account di archiviazione sia abilitato per il [trasferimento sicuro obbligatorio](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) . |

1. Fare clic su **Aggiungi**.

   ![Titolo: immettere i dettagli della raccolta regole dell'applicazione](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Configurare il firewall con le regole di rete

Creare le regole di rete per configurare correttamente il cluster HDInsight.

1. Selezionare il nuovo firewall **test-FW01** dalla portale di Azure.
1. Fare clic su **regole** in **Impostazioni** > **raccolta regole di rete** > **Aggiungi raccolta regole di rete**.
1. Nella schermata **Aggiungi raccolta regole di rete** immettere un **nome**e una **priorità**, quindi fare clic su **Consenti** dal menu a discesa **azione** .
1. Creare le regole seguenti nella sezione **indirizzi IP** :

   | **Nome** | **Protocollo** | **Indirizzo di origine** | **Indirizzo di destinazione** | **Porta di destinazione** | **Note** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_1 | UDP | * | * | `123` | Servizio ora |
   | Rule_2 | Qualsiasi | * | DC_IP_Address_1, DC_IP_Address_2 | `*` | Se si usa Enterprise Security Package (ESP), aggiungere una regola di rete nella sezione indirizzi IP che consenta la comunicazione con AAD-DS per i cluster ESP. È possibile trovare gli indirizzi IP dei controller di dominio nella sezione AAD-DS nel portale | 
   | Rule_3 | TCP | * | Indirizzo IP dell'account di Data Lake Storage | `*` | Se si usa Azure Data Lake Storage, è possibile aggiungere una regola di rete nella sezione indirizzi IP per risolvere un problema di SNI con ADLS Gen1 e Gen2. Questa opzione consente di instradare il traffico al firewall che può comportare costi più elevati per carichi di dati di grandi dimensioni, ma il traffico verrà registrato e controllabile nei log del firewall. Determinare l'indirizzo IP per l'account Data Lake Storage. È possibile usare un comando di PowerShell, ad esempio `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` per risolvere l'FQDN in un indirizzo IP.|
   | Rule_4 | TCP | * | * | `12000` | Opzionale Se si usa Log Analytics, creare una regola di rete nella sezione indirizzi IP per abilitare la comunicazione con l'area di lavoro di Log Analytics. |

1. Nella sezione **tag servizio** creare le regole seguenti:

   | **Nome** | **Protocollo** | **Indirizzo di origine** | **Tag di servizio** | **Porta di destinazione** | **Note** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_7 | TCP | * | SQL | `1433` | Configurare una regola di rete nella sezione Tag del servizio per SQL che consentirà di registrare e controllare il traffico SQL, a meno che non siano stati configurati endpoint di servizio per SQL Server nella subnet HDInsight che ignorerà il firewall. |

1. Fare clic su **Aggiungi** per completare la creazione della raccolta regole di rete.

   ![Titolo: immettere la raccolta regole dell'applicazione](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Creare e configurare una tabella di route

Creare una tabella di route con le voci seguenti:

1. Sei indirizzi da [questo elenco di indirizzi IP di gestione HDInsight obbligatori](../hdinsight/hdinsight-management-ip-addresses.md) con un hop successivo di **Internet**:
    1. Quattro indirizzi IP per tutti i cluster in tutte le aree
    1. Due indirizzi IP specifici per l'area in cui viene creato il cluster
1. Una route dell'appliance virtuale per l'indirizzo IP 0.0.0.0/0 con l'hop successivo che è l'indirizzo IP privato del firewall di Azure.

Ad esempio, per configurare la tabella di route per un cluster creato nell'area Stati Uniti di "Stati Uniti centrali", seguire questa procedura:

1. Accedere al portale di Azure.
1. Selezionare il firewall **di Azure test-FW01**. Copiare l' **indirizzo IP privato** elencato nella pagina **Overview** . Per questo esempio verrà usato un **indirizzo di esempio di 10.1.1.4**
1. Creare una nuova tabella di route.
1. Fare clic su **Route** in **Impostazioni**.
1. Fare clic su **Aggiungi** per creare route per gli indirizzi IP nella tabella seguente.

| Nome route | Prefisso indirizzo | Tipo hop successivo | Indirizzo hop successivo |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | ND |
| 23.99.5.239 | 23.99.5.239/32 | Internet | ND |
| 168.61.48.131 | 168.61.48.131/32 | Internet | ND |
| 138.91.141.162 | 138.91.141.162/32 | Internet | ND |
| 13.67.223.215 | 13.67.223.215/32 | Internet | ND |
| 40.86.83.253 | 40.86.83.253/32 | Internet | ND |
| 0.0.0.0 | 0.0.0.0/0 | Appliance virtuale | 10.1.1.4 |

Completare la configurazione della tabella di route:

1. Assegnare la tabella di route creata alla subnet HDInsight facendo clic su **subnet** in **Settings (impostazioni** ) e quindi su **associa**.
1. Nella schermata **associa subnet** selezionare la rete virtuale in cui è stato creato il cluster e la **subnet HDInsight** usata per il cluster HDInsight.
1. Fare clic su **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Traffico di applicazioni di nodi perimetrali o personalizzate

I passaggi precedenti consentiranno al cluster di funzionare senza problemi. È comunque necessario configurare le dipendenze per supportare le applicazioni personalizzate in esecuzione nei nodi perimetrali, se applicabile.

Le dipendenze dell'applicazione devono essere identificate e aggiunte al firewall di Azure o alla tabella di route.

Per evitare problemi di routing asimmetrico, è necessario creare le route per il traffico dell'applicazione.

Se le applicazioni hanno altre dipendenze, è necessario aggiungerle al firewall di Azure. Creare regole per le applicazioni per consentire il traffico HTTP/HTTPS e regole di rete per tutto il resto.

## <a name="logging-and-scale"></a>Registrazione e scalabilità

Il firewall di Azure può inviare i log a diversi sistemi di archiviazione. Per istruzioni sulla configurazione della registrazione per il firewall, seguire la procedura descritta in [esercitazione: monitorare i log e le metriche del firewall di Azure](../firewall/tutorial-diagnostics.md).

Una volta completata la configurazione della registrazione, se si registrano dati in Log Analytics, è possibile visualizzare il traffico bloccato con una query come la seguente:

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

L'integrazione del firewall di Azure con i log di monitoraggio di Azure è utile quando si esegue per la prima volta un'applicazione quando non si è a conoscenza di tutte le dipendenze dell'applicazione. Per altre informazioni sui log di Monitoraggio di Azure, vedere [Analizzare i dati di log in Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md)

Per informazioni sui limiti di scalabilità del firewall di Azure e sugli aumenti delle richieste, vedere [questo](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#azure-firewall-limits) documento o consultare le [domande frequenti](https://docs.microsoft.com/en-us/azure/firewall/firewall-faq). 

## <a name="access-to-the-cluster"></a>Accesso al cluster
Dopo aver configurato correttamente il firewall, è possibile usare l'endpoint interno (`https://<clustername>-int.azurehdinsight.net`) per accedere a Ambari dall'interno di VNET. 

Per usare l'endpoint pubblico (`https://<clustername>.azurehdinsight.net`) o l'endpoint SSH (`<clustername>-ssh.azurehdinsight.net`), assicurarsi di avere le route corrette nella tabella di route e le regole NSG per evitare il problema di routing asimmetrica illustrato [qui](https://docs.microsoft.com/azure/firewall/integrate-lb). In particolare, in questo caso, è necessario consentire l'indirizzo IP del client nelle regole del NSG in ingresso e aggiungerlo anche alla tabella di route definita dall'utente con il set di hop successivo come `internet`. Se la configurazione non viene eseguita correttamente, verrà visualizzato un errore di timeout.

## <a name="configure-another-network-virtual-appliance"></a>Configurare un'altra appliance virtuale di rete

>[!Important]
> Le informazioni seguenti sono necessarie **solo** se si vuole configurare un'appliance virtuale di rete diversa dal firewall di Azure.

Le istruzioni precedenti consentono di configurare il firewall di Azure per la limitazione del traffico in uscita dal cluster HDInsight. Il firewall di Azure viene configurato automaticamente per consentire il traffico per molti degli scenari importanti comuni. Se si vuole usare un'altra appliance virtuale di rete, sarà necessario configurare manualmente una serie di funzionalità aggiuntive. Quando si configura l'appliance virtuale di rete, tenere presente quanto segue:

* Con gli endpoint di servizio devono essere configurati servizi che supportano endpoint di servizio.
* Le dipendenze degli indirizzi IP sono per il traffico non HTTP/S (traffico TCP e UDP).
* Gli endpoint HTTP/HTTPS FQDN possono essere inseriti nel dispositivo appliance virtuale di dispositivi.
* Gli endpoint HTTP/HTTPS con caratteri jolly sono dipendenze che possono variare in base a un numero di qualificatori.
* Assegnare la tabella di route creata alla subnet HDInsight.

### <a name="service-endpoint-capable-dependencies"></a>Dipendenze che supportano endpoint di servizio

| **Endpoint** |
|---|
| SQL di Azure |
| Archiviazione di Azure |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>Dipendenze di indirizzi IP

| **Endpoint** | **Dettagli** |
|---|---|
| \*:123 | Controllo dell'orologio NTP. Il traffico viene verificato in più endpoint sulla porta 123. |
| Indirizzi IP pubblicati [qui](hdinsight-management-ip-addresses.md) | Si tratta del servizio HDInsight |
| Indirizzi IP privati AAD-DS per i cluster ESP |
| \*: 16800 per l'attivazione di Windows KMS |
| \*12000 per Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>Dipendenze HTTP/HTTPS con nome di dominio completo

>[!Important]
> L'elenco seguente fornisce solo alcuni dei nomi di dominio completi più importanti. È possibile ottenere l'elenco completo dei nomi di dominio completi per la configurazione dell'appliance virtuale di sistema [in questo file](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

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
| azure.archive.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                |
| ocsp.digicert.com:80                                                |

## <a name="next-steps"></a>Passaggi successivi

* [Architettura della rete virtuale di Azure HDInsight](hdinsight-virtual-network-architecture.md)
