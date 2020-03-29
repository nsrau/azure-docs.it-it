---
title: InvalidNetworkConfigurationErrorCode error - Azure HDInsight
description: Vari motivi per le creazioni di cluster non riuscite con InvalidNetworkConfigurationErrorCode in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720385"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Creazione del cluster ha esito negativo con InvalidNetworkConfigurationErrorCode in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

Se viene visualizzato `InvalidNetworkConfigurationErrorCode` il codice di errore con la descrizione "La configurazione della rete virtuale non è compatibile con HDInsight Requirement", indica in genere un problema con la configurazione della [rete virtuale](../hdinsight-plan-virtual-network-deployment.md) per il cluster. In base al resto della descrizione dell'errore, seguire le sezioni seguenti per risolvere il problema.

## <a name="hostname-resolution-failed"></a>"Risoluzione del nome host non riuscita"

### <a name="issue"></a>Problema

La descrizione dell'errore contiene "Risoluzione del nome host non riuscita".

### <a name="cause"></a>Causa

Questo errore indica un problema con la configurazione DNS personalizzata. I server DNS all'interno di una rete virtuale possono inoltrare le query DNS ai resolver ricorsivi di Azure per risolvere i nomi host all'interno di tale rete virtuale (per informazioni dettagliate, vedere Risoluzione dei nomi [nelle reti virtuali).](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) L'accesso ai resolver ricorsivi di Azure viene fornito tramite l'indirizzo IP virtuale 168.63.129.16. Questo indirizzo IP è accessibile solo dalle macchine virtuali di Azure.This IP is only accessible from the Azure VMs. Pertanto, non funzionerà se si usa un server DNS OnPrem o se il server DNS è una macchina virtuale di Azure, che non fa parte della rete virtuale del cluster.

### <a name="resolution"></a>Risoluzione

1. Ssh nella macchina virtuale che fa parte del `hostname -f`cluster ed eseguire il comando . Verrà restituito il nome di dominio completo dell'host (indicato come `<host_fqdn>` nelle istruzioni riportate di seguito).

1. Quindi, eseguire `nslookup <host_fqdn>` il comando `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`(ad esempio, ). Se questo comando risolve il nome in un indirizzo IP, significa che il server DNS funziona correttamente. In questo caso, generare un caso di supporto con HDInsight e verrà analizzato il problema. Nel caso del supporto, includere i passaggi per la risoluzione dei problemi eseguiti. Questo ci aiuterà a risolvere il problema più velocemente.

1. Se il comando precedente non restituisce un `nslookup <host_fqdn> 168.63.129.16` indirizzo IP, eseguire (ad esempio, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`). Se questo comando è in grado di risolvere l'IP, significa che il server DNS non inoltra la query al DNS di Azure oppure non è una macchina virtuale che fa parte della stessa rete virtuale del cluster.

1. Se non si dispone di una macchina virtuale di Azure che può fungere da server DNS personalizzato nella rete virtuale del cluster, è necessario aggiungerlo prima. Creare una macchina virtuale nella rete virtuale, che verrà configurata come server d'inoltro DNS.

1. Dopo aver distribuito una macchina virtuale nella rete virtuale, configurare le regole di inoltro DNS in questa macchina virtuale. Inoltrare tutte le richieste di risoluzione dei nomi iDNS a 168.63.129.16 e il resto al server DNS. Di seguito è [riportato](../hdinsight-plan-virtual-network-deployment.md) un esempio di questa configurazione per un server DNS personalizzato.

1. Aggiungere l'indirizzo IP di questa macchina virtuale come prima voce DNS per la configurazione DNS della rete virtuale.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Impossibile connettersi all'account di archiviazione di Azure"

### <a name="issue"></a>Problema

Descrizione dell'errore contiene "Impossibile connettersi all'account di archiviazione di Azure" o "Impossibile connettersi a SQL di Azure".

### <a name="cause"></a>Causa

Archiviazione di Azure e SQL non dispongono di indirizzi IP fissi, pertanto è necessario consentire le connessioni in uscita a tutti gli indirizzi IP per consentire l'accesso a questi servizi. I passaggi di risoluzione esatti dipendono dalla configurazione di un gruppo di sicurezza di rete (NSG) o di regole definite dall'utente (UDR). Per informazioni dettagliate su queste configurazioni, fare riferimento alla sezione sul controllo del traffico di rete con HDInsight con gruppi di sicurezza di [rete e route definite dall'utente.](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)

### <a name="resolution"></a>Risoluzione

* Se il cluster utilizza un gruppo di sicurezza di [rete (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Passare al portale di Azure e identificare il gruppo di sicurezza di rete associato alla subnet in cui viene distribuito il cluster. Nella sezione Regole di **sicurezza in uscita** consentire l'accesso in uscita a Internet senza limitazioni (si noti che un numero di **priorità** inferiore indica una priorità più alta). Inoltre, nella sezione **subnet** verificare se questo gruppo di sicurezza di rete viene applicato alla subnet del cluster.

* Se il cluster utilizza un [UDR (User-defined Routes)](../../virtual-network/virtual-networks-udr-overview.md).

    Passare al portale di Azure e identificare la tabella di route associata alla subnet in cui viene distribuito il cluster. Dopo aver trovato la tabella di route per la subnet, esaminare la sezione **relativa alle route** in essa contenuti.

    Se sono definite route, assicurarsi che siano presenti route per gli indirizzi IP per l'area in cui è stato distribuito il cluster e che **NextHopType** per ogni route sia **Internet**. Deve essere definita una route per ogni indirizzo IP richiesto documentato nell'articolo di cui sopra.

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"La configurazione della rete virtuale non è compatibile con il requisito HDInsight"

### <a name="issue"></a>Problema

Le descrizioni degli errori contengono messaggi simili a quelli seguenti:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Causa

Probabilmente un problema con l'installazione DNS personalizzata.

### <a name="resolution"></a>Risoluzione

Verificare che 168.63.129.16 si trova nella catena DNS personalizzata. I server DNS all'interno di una rete virtuale possono inoltrare query DNS ai resolver ricorsivi di Azure per risolvere i nomi host all'interno di quella rete virtuale. Per ulteriori informazioni, vedere [Risoluzione dei](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)nomi nelle reti virtuali . L'accesso ai resolver ricorsivi di Azure viene fornito tramite l'indirizzo IP virtuale 168.63.129.16.

1. Usare [il comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster, quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Eseguire il comando seguente:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Verrà visualizzata una schermata analoga alla seguente:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    In base al risultato- scegliere uno dei seguenti passaggi da seguire:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 non è in questo elenco

**Opzione 1**  
Aggiungere 168.63.129.16 come primo DNS personalizzato per la rete virtuale seguendo la procedura descritta in [Plan a virtual network for Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md). Questi passaggi sono applicabili solo se il server DNS personalizzato viene eseguito su Linux.These steps are applicable only if your custom DNS server runs on Linux.

**Opzione 2**  
Distribuire una macchina virtuale del server DNS per la rete virtuale. Questo include i passaggi seguenti:

* Creare una macchina virtuale nella rete virtuale, che verrà configurata come server d'inoltro DNS (può essere una macchina virtuale Linux o Windows).
* Configurare le regole di inoltro DNS in questa macchina virtuale (inoltrare tutte le richieste di risoluzione dei nomi iDNS a 168.63.129.16 e il resto al server DNS).
* Aggiungere l'indirizzo IP di questa macchina virtuale come prima voce DNS per la configurazione DNS della rete virtuale.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 è nell'elenco

In questo caso, creare un caso di supporto con HDInsight e verrà analizzato il problema. Includere il risultato dei comandi seguenti nel caso di supporto. Questo ci aiuterà a indagare e risolvere il problema più velocemente.

Da una sessione ssh sul nodo head, modificare ed eseguire quanto segue:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
