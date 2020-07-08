---
title: Configurare la restrizione del traffico di rete in uscita - Azure HDInsight
description: Di seguito viene descritto come configurare il traffico di rete in uscita per i cluster di Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 37e6b2986f76529b5f3b2edc69f50259485df0b4
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087011"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Configurare il traffico di rete in uscita per i cluster di Azure HDInsight tramite il firewall

Il presente articolo illustra la procedura per proteggere il traffico in uscita dal cluster HDInsight tramite Firewall di Azure. I passaggi seguenti presuppongono che si stia configurando un firewall di Azure per un cluster esistente. Se si sta distribuendo un nuovo cluster dietro a un firewall, è necessario creare prima il cluster HDInsight e la subnet, quindi seguire i passaggi descritti in questa guida.

## <a name="background"></a>Background

I cluster HDInsight vengono in genere distribuiti in una rete virtuale. Il cluster è associato a dipendenze da servizi esterni a tale rete virtuale.

Esistono diverse dipendenze che richiedono il traffico in ingresso. Il traffico di gestione in ingresso non può essere inviato attraverso un dispositivo firewall. Gli indirizzi di origine per questo traffico sono noti e sono pubblicati [qui](hdinsight-management-ip-addresses.md). È inoltre possibile creare regole del gruppo di sicurezza di rete per proteggere il traffico in ingresso verso i cluster.

Le dipendenze del traffico in uscita dal cluster HDInsight sono quasi completamente definite con FQDN, i quali non dispongono di indirizzi IP statici. La mancanza di indirizzi statici significa che i gruppi di sicurezza di rete non possono bloccare il traffico in uscita da un cluster. Gli indirizzi cambiano con una frequenza tale che non è possibile configurare le regole in base alla risoluzione corrente dei nomi e usarla.

Proteggere gli indirizzi in uscita con un firewall in grado di controllare il traffico in uscita in base ai nomi di dominio. Firewall di Azure limita il traffico in uscita in base all'FQDN della destinazione o ai [tag FQDN](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Configurazione di Firewall di Azure con HDInsight

Di seguito, un riepilogo dei passaggi da seguire per bloccare i dati in uscita dal cluster HDInsight esistente con Firewall di Azure:

1. Creare una subnet.
1. Creare un firewall.
1. Aggiungere le regole dell'applicazione al firewall.
1. Aggiungere le regole di rete al firewall.
1. Creare una tabella di routing.

### <a name="create-new-subnet"></a>Creare una nuova subnet

Creare una subnet denominata **AzureFirewallSubnet** nella rete virtuale in cui si trova il cluster.

### <a name="create-a-new-firewall-for-your-cluster"></a>Creare un nuovo firewall per il cluster

Creare un firewall denominato **Test-FW01** seguendo i passaggi descritti nella sezione **Distribuire il firewall** in [Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Configurare il firewall con le regole dell'applicazione

Creare una raccolta di regole dell'applicazione che consenta al cluster di inviare e ricevere comunicazioni importanti.

1. Selezionare il nuovo firewall **Test-FW01** dal portale di Azure.

1. Passare a **Impostazioni** > **Regole** > **Raccolta regole dell'applicazione** >  **+ Aggiungi raccolta regole dell'applicazione**.

    ![Titolo: Aggiungi raccolta regole dell'applicazione](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Nella schermata **Aggiungi raccolta regole dell'applicazione**, specificare le informazioni seguenti:

    **Sezione superiore**

    | Proprietà|  valore|
    |---|---|
    |Nome| FwAppRule|
    |Priorità|200|
    |Azione|Allow|

    **Sezione Tag FQDN**

    | Nome | Indirizzo di origine | Tag FQDN | Note |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate e HDInsight | Obbligatorio per i servizi HDI |

    **Sezione FQDN di destinazione**

    | Nome | Indirizzi di origine | `Protocol:Port` | FQDN di destinazione | Note |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Consente l'attività di accesso di Windows |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Consente l'attività di accesso di Windows |
    | Rule_4 | * | https:443,http:80 | storage_account_name.blob.core.windows.net | Sostituire `storage_account_name` con il nome dell'account di archiviazione. Se il cluster è supportato da WASB, aggiungere una regola per WASB. Per usare SOLO le connessioni HTTPS, assicurarsi che nell'account di archiviazione sia abilitato ["trasferimento sicuro obbligatorio"](../storage/common/storage-require-secure-transfer.md). |

   ![Titolo: Immetti i dettagli della raccolta regole dell'applicazione](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Selezionare **Aggiungi**.

### <a name="configure-the-firewall-with-network-rules"></a>Configurare il firewall con le regole di rete

Creare le regole di rete per configurare correttamente il cluster HDInsight.

1. Proseguendo dal passaggio precedente, passare a **Raccolta regole di rete** >  **+ Aggiungi raccolta regole di rete**.

1. Nella schermata **Aggiungi raccolta regole dell'applicazione**, specificare le informazioni seguenti:

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
    | Rule_2 | Qualsiasi | * | DC_IP_Address_1, DC_IP_Address_2 | * | Se si usa Enterprise Security Package (ESP), aggiungere una regola di rete nella sezione Indirizzi IP che consenta la comunicazione con AAD-DS per i cluster ESP. È possibile trovare gli indirizzi IP dei controller di dominio nella sezione AAD-DS del portale |
    | Rule_3 | TCP | * | Indirizzo IP dell'account Azure Data Lake Storage | * | Se si usa Azure Data Lake Storage, è possibile aggiungere una regola di rete nella sezione Indirizzi IP per risolvere un problema SNI con ADLS Gen1 e Gen2. Questa opzione consente di indirizzare il traffico al firewall. Ciò potrebbe comportare costi più elevati per grandi carichi di dati, ma il traffico verrà registrato e sarà verificabile nei log del firewall. Determinare l'indirizzo IP dell'account Azure Data Lake Storage. È possibile usare un comando di PowerShell, ad esempio `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` per risolvere l'FQDN in un indirizzo IP.|
    | Rule_4 | TCP | * | * | 12000 | (Facoltativo) Se si usa Log Analytics, creare una regola di rete nella sezione Indirizzi IP per abilitare la comunicazione con l'area di lavoro di Log Analytics. |

    **Sezione Tag di servizio**

    | Nome | Protocollo | Indirizzi di origine | Tag di servizio | Porte di destinazione | Note |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Configurare una regola di rete nella sezione Tag servizio per SQL che consentirà di registrare e controllare il traffico SQL, a meno che non siano stati configurati endpoint di servizio per SQL Server nella subnet HDInsight, che ignoreranno il firewall. |
    | Rule_8 | TCP | * | Monitoraggio di Azure | * | (Facoltativo) Si consiglia ai clienti che prevedono di usare la funzionalità di scalabilità automatica di aggiungere questa regola. |
    
   ![Titolo: Immetti la raccolta regole dell'applicazione](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Selezionare **Aggiungi**.

### <a name="create-and-configure-a-route-table"></a>Creare e configurare tabelle di route

Creare una tabella di route con le voci seguenti:

* Tutti gli indirizzi IP da [Servizi di integrità e gestione: tutte le aree](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) con un tipo di hop successivo di **Internet**.

* Due indirizzi IP per l'area in cui il cluster viene creato da [Servizi di integrità e gestione: Aree specifiche](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) con un tipo di hop successivo di **Internet**.

* Una route di appliance virtuale per l'indirizzo IP 0.0.0.0/0 con hop successivo corrispondente all'indirizzo IP privato di Firewall di Azure.

Ad esempio, per configurare la tabella di route per un cluster creato nell'area Stati Uniti di "Stati Uniti orientali", seguire questi passaggi:

1. Selezionare il firewall di Azure **Test-FW01**. Copiare l'**Indirizzo IP privato** elencato nella pagina **Panoramica**. In questo esempio viene usato l'**indirizzo campione 10.0.2.4**.

1. Passare quindi a **Tutti i servizi** > **Rete** > **Tabelle route** e **Crea tabella di route**.

1. Dalla nuova route, passare a **Impostazioni** > **Route** >  **+ Aggiungi**. Aggiungere le route seguenti:

| Nome route | Prefisso indirizzo | Tipo hop successivo | Indirizzo hop successivo |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | ND |
| 23.99.5.239 | 23.99.5.239/32 | Internet | ND |
| 168.61.48.131 | 168.61.48.131/32 | Internet | ND |
| 138.91.141.162 | 138.91.141.162/32 | Internet | ND |
| 13.82.225.233 | 13.82.225.233/32 | Internet | ND |
| 40.71.175.99 | 40,71,175,99/32...20, 24, 32 | Internet | ND |
| 0.0.0.0 | 0.0.0.0/0 | Appliance virtuale | 10.0.2.4 |

Completare la configurazione della tabella di route:

1. Assegnare la tabella di route creata alla subnet HDInsight selezionando **Subnet** in **Impostazioni**.

1. Selezionare **+ Associa**.

1. Nella schermata **Associa subnet**, selezionare la rete virtuale in cui è stato creato il cluster e la **subnet** usata per il cluster HDInsight.

1. Selezionare **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Traffico dell'applicazione del nodo perimetrale o personalizzata

I passaggi precedenti consentiranno al cluster di operare senza problemi. È comunque necessario configurare le dipendenze per supportare le applicazioni personalizzate in esecuzione nei nodi perimetrali, ove applicabile.

Le dipendenze delle applicazioni devono essere identificate e aggiunte a Firewall di Azure o alla tabella di route.

È necessario creare route per il traffico delle applicazioni per evitare problemi di routing asimmetrico.

Se le applicazioni hanno altre dipendenze, è necessario aggiungerle a Firewall di Azure. Creare regole per le applicazioni per consentire il traffico HTTP/HTTPS e regole di rete per tutto il resto.

## <a name="logging-and-scale"></a>Registrazione e scalabilità

Firewall di Azure può inviare i log a diversi sistemi di archiviazione. Per le istruzioni sulla configurazione della registrazione per il firewall, seguire i passaggi descritti in [Esercitazione: monitorare i log e le metriche di Firewall di Azure](../firewall/tutorial-diagnostics.md).

Dopo aver completato la configurazione della registrazione, se si usa Log Analytics è possibile visualizzare il traffico bloccato con una query, ad esempio:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

L'integrazione di Firewall di Azure con i log di Monitoraggio di Azure è utile quando si avvia un'applicazione per la prima volta. In particolare, quando non si è a conoscenza di tutte le dipendenze dell'applicazione. Per altre informazioni sui log di Monitoraggio di Azure, vedere [Analizzare i dati di log in Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md)

Per informazioni sui limiti di scalabilità di Firewall di Azure e sugli aumenti delle richieste, vedere [questo](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) documento o consultare le [domande frequenti ](../firewall/firewall-faq.md).

## <a name="access-to-the-cluster"></a>Accesso al cluster

Dopo aver configurato correttamente il firewall, è possibile usare l'endpoint interno (`https://CLUSTERNAME-int.azurehdinsight.net`) per accedere ad Ambari dall'interno della rete virtuale.

Per usare l'endpoint pubblico (`https://CLUSTERNAME.azurehdinsight.net`) o l'endpoint SSH (`CLUSTERNAME-ssh.azurehdinsight.net`), assicurarsi di avere le route corrette nella tabella di route e le regole del gruppo di sicurezza di rete per evitare il problema di routing asimmetrico illustrato [qui](../firewall/integrate-lb.md). In particolare, in questo caso, è necessario consentire l'indirizzo IP del client nelle regole del gruppo di sicurezza di rete in ingresso e aggiungerlo alla tabella di route definita dall'utente con l'hop successivo come `internet`. Se il routing non è configurato correttamente, verrà visualizzato un errore di timeout.

## <a name="next-steps"></a>Passaggi successivi

* [Architettura della rete virtuale di HDInsight](hdinsight-virtual-network-architecture.md)
* [Configurare un'appliance virtuale di rete](./network-virtual-appliance.md)
