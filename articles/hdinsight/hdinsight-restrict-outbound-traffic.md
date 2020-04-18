---
title: Configurare la restrizione del traffico di rete in uscita - Azure HDInsightConfigure outbound network traffic restriction - Azure HDInsight
description: Informazioni su come configurare la restrizione del traffico di rete in uscita per i cluster Azure HDInsight.Learn how to configure outbound network traffic restriction for Azure HDInsight clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: d4bf2d1d4beeb00325d54e091a00438073509eef
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641304"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Configurare il traffico di rete in uscita per i cluster Azure HDInsight tramite FirewallConfigure outbound network traffic for Azure HDInsight clusters using Firewall

Questo articolo illustra i passaggi per proteggere il traffico in uscita dal cluster HDInsight usando Firewall di Azure.This article provides the steps for you to secure outbound traffic from your HDInsight cluster using Azure Firewall. I passaggi seguenti presuppongono che si stia configurando un firewall di Azure per un cluster esistente. Se si distribuisce un nuovo cluster dietro un firewall, creare prima il cluster e la subnet HDInsight.If you're deploying a new cluster behind a firewall, create your HDInsight cluster and subnet first. Quindi seguire i passaggi in questa guida.

## <a name="background"></a>Background

I cluster HDInsight vengono in genere distribuiti in una rete virtuale. Il cluster ha dipendenze da servizi esterni a tale rete virtuale.

Esistono diverse dipendenze che richiedono traffico in ingresso. Il traffico di gestione in ingresso non può essere inviato tramite un dispositivo firewall. Gli indirizzi di origine per questo traffico sono noti e sono pubblicati [qui](hdinsight-management-ip-addresses.md). È inoltre possibile creare regole del gruppo di sicurezza di rete (NSG) con queste informazioni per proteggere il traffico in ingresso verso i cluster.

Le dipendenze del traffico in uscita di HDInsight sono quasi interamente definite con FQDN. Che non hanno indirizzi IP statici dietro di loro. La mancanza di indirizzi statici significa che i gruppi di sicurezza di rete (NSG) non possono bloccare il traffico in uscita da un cluster. Gli indirizzi cambiano abbastanza spesso non è possibile impostare regole in base alla risoluzione dei nomi e all'uso correnti.

Proteggere gli indirizzi in uscita con un firewall in grado di controllare il traffico in uscita in base ai nomi di dominio. Firewall di Azure limita il traffico in uscita in base al nome di dominio completo dei [tag FQDN](../firewall/fqdn-tags.md)o di destinazione.

## <a name="configuring-azure-firewall-with-hdinsight"></a>Configurazione di Firewall di Azure con HDInsightConfiguring Azure Firewall with HDInsight

Un riepilogo dei passaggi per bloccare l'uscita dall'HDInsight con Il firewall di Azure esistente sono:A summary of the steps to lock down gress from your existing HDInsight with Azure Firewall are:

1. Creare una subnet.
1. Creare un firewall.
1. Aggiungere regole dell'applicazione al firewall
1. Aggiungere regole di rete al firewall.
1. Creare una tabella di routing.

### <a name="create-new-subnet"></a>Creare una nuova subnetCreate new subnet

Creare una subnet denominata **AzureFirewallSubnet** nella rete virtuale in cui è presente il cluster.

### <a name="create-a-new-firewall-for-your-cluster"></a>Creare un nuovo firewall per il clusterCreate a new firewall for your cluster

Creare un firewall denominato **Test-FW01** seguendo la procedura descritta in **Distribuire il firewall** da [Esercitazione: Distribuire e configurare Firewall](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)di Azure tramite il portale di Azure.

### <a name="configure-the-firewall-with-application-rules"></a>Configurare il firewall con le regole dell'applicazione

Creare una raccolta di regole dell'applicazione che consenta al cluster di inviare e ricevere comunicazioni importanti.

1. Selezionare il nuovo firewall Test-FW01 dal portale di Azure.Select the new firewall **Test-FW01** from the Azure portal.

1. Passare **Settings** > alla > raccolta di**regole** > **dell'applicazione**Impostazioni : Aggiungi raccolta regole**applicazione**.

    ![Titolo: Aggiungere la raccolta di regole dell'applicazione](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Nella schermata Aggiungi raccolta regole applicazione fornire le informazioni seguenti:On the **Add application rule collection** screen, provide the following information:

    **Sezione superiore**

    | Proprietà|  valore|
    |---|---|
    |Nome| FwAppRule (regola di un'applicazione di dati)|
    |Priorità|200|
    |Azione|Allow|

    **Sezione Tag FQDN**

    | Nome | Indirizzo di origine | Tag FQDN | Note |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate e HDInsight | Obbligatorio per i servizi HDI |

    **Sezione FQDN di destinazione**

    | Nome | Indirizzi di origine | `Protocol:Port` | FQDNS di destinazione | Note |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | all'indirizzo https : 443 | login.windows.net | Consente l'attività di accesso di Windows |
    | Rule_3 | * | all'indirizzo https : 443 | login.microsoftonline.com | Consente l'attività di accesso di Windows |
    | Rule_4 | * | https:443,http:80 | storage_account_name.blob.core.windows.net | Sostituire `storage_account_name` con il nome effettivo dell'account di archiviazione. Se il cluster è supportato da WASB, aggiungere una regola per WASB. Per usare SOLO le connessioni https, assicurarsi che [l'opzione "Trasferimento sicuro richiesto"](../storage/common/storage-require-secure-transfer.md) sia abilitata nell'account di archiviazione. |

   ![Titolo: immettere i dettagli della raccolta di regole di applicazione](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Selezionare **Aggiungi**.

### <a name="configure-the-firewall-with-network-rules"></a>Configurare il firewall con le regole di rete

Creare le regole di rete per configurare correttamente il cluster HDInsight.Create the network rules to correctly configure your HDInsight cluster.

1. Continuando dal passaggio precedente, passare a **Raccolta** > regole di rete **- Aggiungi raccolta regole**di rete .

1. Nella schermata Aggiungi raccolta regole di rete fornire le informazioni seguenti:On the **Add network rule collection** screen, provide the following information:

    **Sezione superiore**

    | Proprietà|  valore|
    |---|---|
    |Nome| FwNetRule|
    |Priorità|200|
    |Azione|Allow|

    **Sezione Indirizzi IP**

    | Nome | Protocollo | Indirizzi di origine | Indirizzi di destinazione | Porte di destinazione | Note |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | Servizio Ora |
    | Rule_2 | Qualsiasi | * | DC_IP_Address_1, DC_IP_Address_2 | * | Se si utilizza Enterprise Security Package (ESP), aggiungere una regola di rete nella sezione Indirizzi IP che consenta la comunicazione con AAD-DS per i cluster ESP. È possibile trovare gli indirizzi IP dei controller di dominio nella sezione AAD-DS nel portale |
    | Rule_3 | TCP | * | Indirizzo IP dell'account di archiviazione di Data Lake | * | Se si usa Archiviazione di Azure Data Lake, è possibile aggiungere una regola di rete nella sezione Indirizzi IP per risolvere un problema SNI con ADLS Gen1 e Gen2. Questa opzione instrada il traffico al firewall. Ciò potrebbe comportare costi più elevati per carichi di dati di grandi dimensioni, ma il traffico verrà registrato e controllabile nei registri del firewall. Determinare l'indirizzo IP per l'account di archiviazione data lake. È possibile usare un comando `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` di PowerShell, ad esempio per risolvere il nome di dominio completo in un indirizzo IP.|
    | Rule_4 | TCP | * | * | 12000 | (Facoltativo) Se si usa Log Analytics, creare una regola di rete nella sezione Indirizzi IP per abilitare la comunicazione con l'area di lavoro di Log Analytics.If you're using Log Analytics, then create a network rule in the IP Addresses section to enable communication with your Log Analytics workspace. |

    **Sezione Tag servizio**

    | Nome | Protocollo | Indirizzi di origine | Tag di servizio | Porte di destinazione | Note |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Configurare una regola di rete nella sezione Tag servizio per SQL che consenta di registrare e controllare il traffico SQL. A meno che non siano stati configurati gli endpoint di servizio per SQL Server nella subnet HDInsight, il che ignorerà il firewall. |

   ![Titolo: Immettere la raccolta di regole dell'applicazione](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Selezionare **Aggiungi**.

### <a name="create-and-configure-a-route-table"></a>Creare e configurare una tabella di route

Creare una tabella del ciclo di lavorazione con le voci seguenti:

* Tutti gli indirizzi IP dei [servizi di integrità e gestione: tutte le aree](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) con un tipo di **Internet**dell'hop successivo .

* Due indirizzi IP per l'area in cui il cluster viene creato da Servizi di [integrità e gestione: aree specifiche](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) con un tipo di hop successivo di **Internet**.

* Una route dell'appliance virtuale per l'indirizzo IP 0.0.0.0/0 con l'hop successivo come indirizzo IP privato del firewall di Azure.One Virtual Appliance route for IP address 0.0.0.0/0 with the next hop as your Azure Firewall private IP address.

Ad esempio, per configurare la tabella di route per un cluster creato nell'area Stati Uniti di "Stati Uniti orientali", attenersi alla seguente procedura:

1. Selezionare il firewall di Azure **Test-FW01**. Copiare **l'indirizzo IP privato** elencato nella pagina **Panoramica.** Per questo esempio useremo un **indirizzo di esempio di 10.0.2.4**.

1. Passare quindi a **Tutti i servizi** > **Tabelle route** di**rete** > e **Crea tabella di route**.

1. Dal nuovo percorso, accedere a **Impostazioni** > **Percorsi** > **e Aggiungi**. Aggiungere i seguenti percorsi:

| Nome route | Prefisso indirizzo | Tipo hop successivo | Indirizzo hop successivo |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | ND |
| 23.99.5.239 | 23.99.5.239/32 | Internet | ND |
| 168.61.48.131 | 168.61.48.131/32 | Internet | ND |
| 138.91.141.162 | 138.91.141.162/32 | Internet | ND |
| 13.82.225.233 | 13.82.225.233/32 | Internet | ND |
| 40.71.175.99 | 40.71.175.99/32 | Internet | ND |
| 0.0.0.0 | 0.0.0.0/0 | Appliance virtuale | 10.0.2.4 |

Completare la configurazione della tabella di route:Complete the route table configuration:

1. Assegnare la tabella di route creata alla subnet HDInsight selezionando **Subnet** in **Impostazioni**.

1. Selezionare **: Associa**.

1. Nella schermata **Associa subnet** selezionare la rete virtuale in cui è stato creato il cluster. E la **subnet** utilizzata per il cluster HDInsight.

1. Selezionare **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Traffico del nodo perimetrale o dell'applicazione personalizzata

I passaggi precedenti consentiranno al cluster di funzionare senza problemi. È comunque necessario configurare le dipendenze per supportare le applicazioni personalizzate in esecuzione nei nodi perimetrali, se applicabile.

Le dipendenze dell'applicazione devono essere identificate e aggiunte al firewall di Azure o alla tabella di route.

È necessario creare route affinché il traffico dell'applicazione eviti problemi di routing asimmetrico.

Se le applicazioni hanno altre dipendenze, devono essere aggiunte al firewall di Azure.If your applications have other dependencies, they need to be added to your Azure Firewall. Creare regole per le applicazioni per consentire il traffico HTTP/HTTPS e regole di rete per tutto il resto.

## <a name="logging-and-scale"></a>Registrazione e scalabilità

Firewall di Azure può inviare log ad alcuni sistemi di archiviazione diversi. Per istruzioni sulla configurazione della registrazione per il firewall, seguire i passaggi descritti in [Esercitazione: Monitorare i log e](../firewall/tutorial-diagnostics.md)le metriche di Firewall di Azure.

Dopo aver completato la configurazione della registrazione, se si usa Log Analytics, è possibile visualizzare il traffico bloccato con una query come:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

L'integrazione di Firewall di Azure con i log di Monitoraggio di Azure è utile quando si ottiene un'applicazione funzionante per la prima volta. Soprattutto quando non si è a conoscenza di tutte le dipendenze dell'applicazione. Per altre informazioni sui log di Monitoraggio di Azure, vedere [Analizzare i dati di log in Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md)

Per informazioni sui limiti di scalabilità di Firewall di Azure e gli aumenti delle richieste, vedere [questo](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) documento o fare riferimento alle [domande frequenti](../firewall/firewall-faq.md).

## <a name="access-to-the-cluster"></a>Accesso al cluster

Dopo aver configurato correttamente il firewall, è`https://CLUSTERNAME-int.azurehdinsight.net`possibile utilizzare l'endpoint interno ( ) per accedere all'Ambari dall'interno della rete virtuale.

Per utilizzare l'endpoint pubblico (`https://CLUSTERNAME.azurehdinsight.net``CLUSTERNAME-ssh.azurehdinsight.net`) o l'endpoint ssh ( ), assicurarsi di disporre delle route corrette nella tabella di route e delle regole del gruppo di sicurezza di rete per evitare il problema di routing asimmetrico illustrato [di seguito.](../firewall/integrate-lb.md) In questo caso, è necessario consentire l'indirizzo IP del client nelle regole del gruppo di sicurezza di `internet`rete in ingresso e aggiungerlo anche alla tabella di route definita dall'utente con l'hop successivo impostato su . Se il routing non è configurato correttamente, verrà visualizzato un errore di timeout.

## <a name="configure-another-network-virtual-appliance"></a>Configurare un'altra appliance virtuale di reteConfigure another network virtual appliance

> [!Important]
> Le informazioni seguenti sono necessarie solo se si vuole configurare un'appliance virtuale di rete (NVA) diversa da Firewall di Azure.The following information is **only** required if you wish to configure a network virtual appliance (NVA) other than Azure Firewall.

Le istruzioni precedenti consentono di configurare Firewall di Azure per limitare il traffico in uscita dal cluster HDInsight.The previous instructions help you configure Azure Firewall for restricting outbound traffic from your HDInsight cluster. Firewall di Azure viene configurato automaticamente per consentire il traffico per molti degli scenari importanti comuni. L'utilizzo di un'altra appliance virtuale di rete richiederà la configurazione di una serie di funzionalità aggiuntive. Tenere presenti i fattori seguenti durante la configurazione dell'appliance virtuale di rete:Keep the following factors in mind as you configure your network virtual appliance:

* Con gli endpoint di servizio devono essere configurati servizi che supportano endpoint di servizio.
* Le dipendenze degli indirizzi IP sono per il traffico non HTTP/S (traffico TCP e UDP).
* Gli endpoint HTTP/HTTPS FQDN possono essere inseriti nel dispositivo NVA.
* Gli endpoint HTTP/HTTPS con caratteri jolly sono dipendenze che possono variare in base a un numero di qualificatori.
* Assegnare la tabella di route creata alla subnet HDInsight.Assign the route table that you create to your HDInsight subnet.

### <a name="service-endpoint-capable-dependencies"></a>Dipendenze che supportano l'endpoint del servizioService endpoint capable dependencies

| **Endpoint** |
|---|
| SQL di Azure |
| Archiviazione di Azure |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>Dipendenze di indirizzi IP

| **Endpoint** | **Dettagli** |
|---|---|
| \*:123 | Controllo dell'orologio NTP. Il traffico viene verificato in più endpoint sulla porta 123. |
| IP pubblicati [qui](hdinsight-management-ip-addresses.md) | Questi indirizzi IP sono servizi HDInsightThese IPs are HDInsight service |
| INDIRIZZI IP privati AAD-DS per cluster ESP |
| \*:16800 per l'attivazione di Windows del Servizio di gestione delle chiavi |
| \*12000 per Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>Dipendenze HTTP/HTTPS con nome di dominio completo

> [!Important]
> L'elenco seguente fornisce solo alcuni degli FQDN più importanti. È possibile ottenere FQDN aggiuntivi (per lo più Archiviazione di Azure e Bus di servizio di Azure) per la configurazione dell'account utente [in questo file.](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)

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

* [Architettura della rete virtuale di Azure HDInsightAzure HDInsight virtual network architecture](hdinsight-virtual-network-architecture.md)
