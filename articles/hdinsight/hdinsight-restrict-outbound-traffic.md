---
title: Configura limitazione del traffico di rete in uscita per i cluster HDInsight di Azure
description: Informazioni su come configurare restrizione di traffico di rete in uscita per i cluster HDInsight di Azure.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/13/2019
ms.openlocfilehash: f244a67abab5c7f8cd14277f87f055ac6d48b8d2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762423"
---
# <a name="configure-outbound-network-traffic-restriction-for-azure-hdinsight-clusters"></a>Configura limitazione del traffico di rete in uscita per i cluster HDInsight di Azure

Questo articolo fornisce i passaggi per poter proteggere il traffico in uscita dal cluster HDInsight tramite il Firewall di Azure. I passaggi seguenti presuppongono che si sta configurando un Firewall di Azure per un cluster esistente. Se si distribuisce un nuovo cluster e un firewall, creare innanzitutto il cluster HDInsight e la subnet e quindi seguire i passaggi descritti in questa Guida.

## <a name="background"></a>Sfondo

I cluster HDInsight di Azure vengono in genere distribuiti nella propria rete virtuale. Il cluster presenta dipendenze da servizi di fuori di tale rete virtuale che richiedono l'accesso alla rete per funzionare correttamente.

Esistono varie dipendenze che richiedono il traffico in ingresso. Il traffico di gestione in ingresso non può essere inviato attraverso un dispositivo firewall. Gli indirizzi di origine per questo tipo di traffico sono note e vengono pubblicati [qui](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip). È anche possibile creare regole di gruppo di sicurezza di rete (NSG) con queste informazioni per proteggere il traffico in ingresso per i cluster.

Le dipendenze di traffico in uscita di HDInsight sono quasi interamente definite con nomi di dominio completo, che non hanno indirizzi IP statici su cui si basano. La mancanza di indirizzi statici significa i gruppi di sicurezza di rete (Nsg) non può essere utilizzati per bloccare il traffico in uscita da un cluster. Gli indirizzi cambiano con una frequenza tale che uno non è possibile impostare le regole di base la risoluzione dei nomi corrente e usarlo per impostare le regole di sicurezza di rete.

La soluzione per proteggere gli indirizzi in uscita è usare un dispositivo firewall che può controllare il traffico in uscita in base ai nomi di dominio. Firewall di Azure può limitare il traffico HTTP e HTTPS in uscita in base all'FQDN della destinazione.

## <a name="configuring-azure-firewall-with-hdinsight"></a>Configurazione di Firewall di Azure con HDInsight

Un riepilogo dei passaggi per bloccare traffico in uscita da di HDInsight esistente con il Firewall di Azure sono:
1. Abilitare gli endpoint servizio.
1. Creare una regola del firewall.
1. Aggiungere le regole di applicazione al firewall
1. Aggiungere le regole di rete verso il firewall.
1. Creare una tabella di routing.

### <a name="enable-service-endpoints"></a>Abilitare gli endpoint servizio

Se si desidera ignorare il firewall (ad esempio, per risparmiare sui costi nel trasferimento dei dati) è quindi possibile abilitare gli endpoint di servizio per SQL e archiviazione nella subnet di HDInsight. Quando si dispone di endpoint servizio abilitato per SQL di Azure, eventuali dipendenze SQL di Azure con il cluster devono essere configurate con anche gli endpoint di servizio.

Per abilitare gli endpoint di servizio corretto, completare i passaggi seguenti:

1. Accedere al portale di Azure e selezionare la rete virtuale che viene distribuito il cluster HDInsight in.
1. Selezionare **subnet** sotto **impostazioni**.
1. Selezionare la subnet in cui viene distribuito il cluster.
1. Nella schermata per modificare le impostazioni di subnet, fare clic su **Microsoft. SQL** e/o **Microsoft. Storage** dal **endpoint di servizio**  >   **Servizi** casella a discesa.
1. Se si usa un cluster ESP, quindi è necessario selezionare anche il **Microsoft. azureactivedirectory** endpoint del servizio.
1. Fare clic su **Save**.

### <a name="create-a-new-firewall-for-your-cluster"></a>Creare un nuovo firewall per il cluster

1. Creare una subnet denominata **AzureFirewallSubnet** nella rete virtuale in cui è presente il cluster. 
1. Creare una nuova regola del firewall **Test-FW01** usando la procedura in [esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).
1. Selezionare il nuovo firewall dal portale di Azure. Fare clic su **regole** sotto **impostazioni** > **raccolta regole applicazione** > **Aggiungi insieme di regole di applicazione**.

    ![Titolo: Aggiungi raccolta regole dell'applicazione](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

### <a name="configure-the-firewall-with-application-rules"></a>Configurare il firewall con le regole di applicazione

Creare una raccolta di regole di applicazione che consente al cluster inviare e ricevere comunicazioni importanti.

Selezionare il nuovo firewall **Test-FW01** dal portale di Azure. Fare clic su **regole** sotto **impostazioni** > **raccolta regole applicazione** > **Aggiungi insieme di regole di applicazione**.

Nel **aggiungere la raccolta di regole dell'applicazione** schermata, seguire questa procedura:

1. Immettere un **Name**, **priorità**, fare clic su **Consenti** dal **azione** menu a discesa.
1. Aggiungere le regole seguenti:
    1. Una regola per consentire il traffico di HDInsight e l'aggiornamento di Windows:
        1. Nel **tag FQDN** sezione, fornire un **nome**e impostare **indirizzi di origine** a `*`.
        1. Selezionare **HDInsight** e il **WindowsUpdate** dal **tag FQDN** menu a discesa.
    1. Una regola per consentire attività di accesso di Windows:
        1. Nel **destinazione FQDN** sezione, fornire un **nome**e impostare **indirizzi di origine** a `*`.
        1. Immettere `https:443` sotto **protocollo: Port** e `login.windows.net` sotto **destinazione FQDN**.
    1. Una regola per consentire i dati di telemetria SQM:
        1. Nel **destinazione FQDN** sezione, fornire un **nome**e impostare **indirizzi di origine** a `*`.
        1. Immettere `https:443` sotto **protocollo: Port** e `sqm.telemetry.microsoft.com` sotto **destinazione FQDN**.
    1. Se il cluster è supportato da WASB e non si usano gli endpoint del servizio precedenti, aggiungere una regola per WASB:
        1. Nel **destinazione FQDN** sezione, fornire un **nome**e impostare **indirizzi di origine** a `*`.
        1. Immettere `wasb` sotto **protocollo: Port** e `*` sotto **destinazione FQDN**.
1. Fare clic su **Aggiungi**.

![Titolo: Immettere i dettagli sulla raccolta di regole di applicazione](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Configurare il firewall con le regole di rete

Creare le regole di rete per configurare correttamente il cluster HDInsight.

> [!Important]
> È possibile scegliere tra l'uso di tag di servizio SQL nel firewall usando le regole di rete, come descritto in questa sezione, oppure un linguaggio SQL endpoint del servizio come descritto [la sezione relativa agli endpoint di servizio](#enable-service-endpoints). Se si sceglie di usare i tag SQL nelle regole di rete, è possibile accedere e controllare il traffico SQL. Uso di un endpoint del servizio avrà il traffico SQL ignorare il firewall.

1. Selezionare il nuovo firewall **Test-FW01** dal portale di Azure.
1. Fare clic su **regole** sotto **impostazioni** > **raccolta regole di rete** > **Aggiungi insieme di regole di rete**.
1. Nel **aggiungere una raccolta di regole di rete** schermata, immettere una **nome**, **priorità**e fare clic su **Consenti** dal **azione** dal menu a discesa.
1. Creare le regole seguenti:
    1. Una regola di rete che consente al cluster eseguire la sincronizzazione dell'orologio tramite NTP.
        1. Nel **regole** sezione, fornire un **Name** e selezionare **qualsiasi** dal **protocollo** elenco a discesa.
        1. Impostare **gli indirizzi di origine** e **gli indirizzi di destinazione** a `*`.
        1. Impostare **le porte di destinazione** alla 123.
    1. Se si usa Enterprise Security Package (ESP), quindi aggiungere una regola di rete che consente la comunicazione con AAD-DS per i cluster ESP.
        1. Determinare i due indirizzi IP per i controller di dominio.
        1. Nella riga successiva nella **regole** sezione, fornire un **nome** e selezionare **qualsiasi** dal **protocollo** elenco a discesa.
        1. Impostare **indirizzi di origine** `*`.
        1. Immettere tutti gli indirizzi IP per il controller di dominio **gli indirizzi di destinazione** separati da virgole.
        1. Impostare **le porte di destinazione** a `*`.
    1. Se si Usa archiviazione di Azure Data Lake, è possibile aggiungere una regola di rete per risolvere un problema SNI con Azure Data Lake Store Gen1 e Gen2. Questa opzione instraderà il traffico al firewall che potrebbe comportare costi più elevati per i caricamenti di dati di grandi dimensioni, ma il traffico sarà controllabile e registrati.
        1. Determinare l'indirizzo IP per l'account Data Lake Store. È possibile usare un comando di powershell, ad esempio `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` risolvere il FQDN per un indirizzo IP.
        1. Nella riga successiva nella **regole** sezione, fornire un **nome** e selezionare **qualsiasi** dal **protocollo** elenco a discesa.
        1. Impostare **indirizzi di origine** `*`.
        1. Immettere l'indirizzo IP per l'account di archiviazione nel **gli indirizzi di destinazione**.
        1. Impostare **le porte di destinazione** a `*`.
    1. Una regola di rete per consentire la comunicazione con la chiave di gestione per Windows attivazione del servizio.
        1. Nella riga successiva nella **regole** sezione, fornire un **nome** e selezionare **qualsiasi** dal **protocollo** elenco a discesa.
        1. Impostare **indirizzi di origine** `*`.
        1. Impostare **gli indirizzi di destinazione** a `*`.
        1. Impostare **le porte di destinazione** a `1688`.
    1. Se si usa Log Analitica, quindi creare una regola di rete per consentire la comunicazione con l'area di lavoro di Log Analitica.
        1. Nella riga successiva nella **regole** sezione, fornire un **nome** e selezionare **qualsiasi** dal **protocollo** elenco a discesa.
        1. Impostare **indirizzi di origine** `*`.
        1. Impostare **gli indirizzi di destinazione** a `*`.
        1. Impostare **le porte di destinazione** a `12000`.
    1. Configurare un tag di servizio per SQL che ti permetterà di accedere e controllare il traffico SQL.
        1. Nella riga successiva nella **regole** sezione, fornire un **nome** e selezionare **qualsiasi** dal **protocollo** elenco a discesa.
        1. Impostare **indirizzi di origine** `*`.
        1. Impostare **gli indirizzi di destinazione** a `*`.
        1. Selezionare **Sql** dalle **tag di servizio** elenco a discesa.
        1. Impostare **le porte di destinazione** a `1433,11000-11999,14000-14999`.
1. Fare clic su **Add** per completare la creazione della raccolta di regole di rete.

![Titolo: Immettere i dettagli sulla raccolta di regole di applicazione](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Creare e configurare una tabella di route

Creare una tabella di route con le voci seguenti:

1. Sette indirizzi compreso [questo elenco di indirizzi IP richiesti gestione di HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) con un hop successivo **Internet**:
    1. Quattro indirizzi IP per tutti i cluster in tutte le aree
    1. Due indirizzi IP che sono specifici per l'area in cui la creazione del cluster
    1. Un indirizzo IP per resolver ricorsivo di Azure
1. Una route di Appliance virtuale per l'IP indirizzo 0.0.0.0/0 con hop successivo in corso l'indirizzo IP privato Firewall di Azure.

Ad esempio, per configurare la tabella di route per un cluster creato nell'area Stati Uniti "Stati Uniti centrali", utilizzare i seguenti passaggi:

1. Accedere al portale di Azure.
1. Selezionare il firewall di Azure **Test-FW01**. Copia il **indirizzo IP privato** elencati nel **Panoramica** pagina. In questo esempio si userà un **adresa 10.1.1.4 di esempio**
1. Creare una nuova tabella di route.
1. Fare clic su **route** sotto **impostazioni**.
1. Fare clic su **Add** creare route per gli indirizzi IP nella tabella seguente.

| Nome route | Prefisso indirizzo | Tipo hop successivo | Indirizzo hop successivo |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | NA |
| 23.99.5.239 | 23.99.5.239/32 | Internet | NA |
| 168.61.48.131 | 168.61.48.131/32 | Internet | NA |
| 138.91.141.162 | 138.91.141.162/32 | Internet | NA |
| 13.67.223.215 | 13.67.223.215/32 | Internet | NA |
| 40.86.83.253 | 40.86.83.253/32 | Internet | NA |
| 168.63.129.16 | 168.63.129.16/32 | Internet | NA |
| 0.0.0.0 | 0.0.0.0/0 | Appliance virtuale | 10.1.1.4 |

![Titolo: Creazione di una tabella di route](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-route-table.png)

Completare la configurazione della tabella di route:

1. Assegnare la tabella di route alla subnet di HDInsight creata facendo **subnet** sotto **impostazioni** e quindi **associare**.
1. Nel **associa subnet** schermata, selezionare la rete virtuale che è stato creato il cluster in e il **AzureFirewallSubnet** creato per l'uso con il firewall.
1. Fare clic su **OK**.

![Titolo: Creazione di una tabella di route](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-route-table-associate-subnet.png)

## <a name="edge-node-application-traffic"></a>Traffico dell'applicazione nodo perimetrale

I passaggi precedenti consentirà il cluster per il funzionamento senza problemi. È comunque necessario configurare le dipendenze per gestire le applicazioni personalizzate in esecuzione nei nodi del bordo, se applicabile.

Le dipendenze dell'applicazione devono essere identificate e aggiunte per il Firewall di Azure o la tabella di route.

Le route devono essere create per il traffico dell'applicazione evitare problemi di routing asimmetrici.

Se l'applicazione presenta altre dipendenze, devono essere aggiunti al Firewall di Azure. Creare regole per le applicazioni per consentire il traffico HTTP/HTTPS e regole di rete per tutto il resto.

## <a name="logging"></a>Registrazione

Firewall di Azure possono inviare i log per alcuni sistemi di archiviazione diverso. Per istruzioni sulla configurazione di registrazione per il firewall, seguire i passaggi descritti in [esercitazione: Monitorare i log del Firewall di Azure e le metriche](../firewall/tutorial-diagnostics.md).

Dopo aver completato la configurazione di registrazione, se si è la registrazione dati per Log Analitica, è possibile visualizzare il traffico bloccato con una query simile al seguente:

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

L'integrazione del Firewall di Azure con i log di monitoraggio di Azure è utile durante il recupero prima di tutto eseguita un'applicazione quando non si è consapevoli di tutte le dipendenze dell'applicazione. Per altre informazioni sui log di Monitoraggio di Azure, vedere [Analizzare i dati di log in Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md)

## <a name="configure-another-network-virtual-appliance"></a>Configurare un'altra appliance virtuale di rete

>[!Important]
> Le informazioni seguenti sono **solo** necessari se si vuole configurare l'appliance virtuale di rete (NVA) diverso da Firewall di Azure.

Le istruzioni precedenti consentono di configurare il Firewall di Azure per limitare il traffico in uscita dal cluster HDInsight. Firewall di Azure viene configurato automaticamente per consentire il traffico per molti degli scenari comuni di importante. Se si desidera utilizzare un'altra appliance virtuale di rete, è necessario configurare manualmente un numero di funzionalità aggiuntive. Tenere presente come nell'esempio di configurare l'appliance virtuale di rete:

* Con gli endpoint di servizio devono essere configurati servizi che supportano endpoint di servizio.
* Dipendenze dell'indirizzo IP sono per il traffico HTTP/S (traffico TCP e UDP).
* Endpoint HTTP/HTTPS di nome di dominio completo può essere inserito nel proprio dispositivo Appliance virtuale di rete.
* Endpoint HTTP/HTTPS con caratteri jolly sono le dipendenze che possono variare basate su un numero di qualificatori.
* Assegnare la tabella di route alla subnet di HDInsight creato.

### <a name="service-endpoint-capable-dependencies"></a>Dipendenze che supportano endpoint di servizio

| **Endpoint** |
|---|
| Azure SQL |
| Archiviazione di Azure |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>Dipendenze di indirizzi IP

| **Endpoint** | **Dettagli** |
|---|---|
| \*:123 | Controllo dell'orologio NTP. Il traffico viene verificato in più endpoint sulla porta 123. |
| Gli indirizzi IP pubblicato [qui](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) | Questi sono il servizio HDInsight |
| AAD-DS indirizzi IP privati per ESP cluster |
| \*: 16800 for KMS Activation di Windows |
| \*12000 per Log Analitica |

#### <a name="fqdn-httphttps-dependencies"></a>Dipendenze HTTP/HTTPS con nome di dominio completo

>[!Important]
> L'elenco seguente offre solo alcune delle più importanti FQDN. È possibile ottenere l'elenco completo dei nomi di dominio completi per la configurazione di Appliance di rete virtuale [in questo file](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

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

* [Architettura di rete virtuale di Azure HDInsight](hdinsight-virtual-network-architecture.md)
