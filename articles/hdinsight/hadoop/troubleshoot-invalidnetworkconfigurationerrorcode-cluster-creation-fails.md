---
title: InvalidNetworkConfigurationErrorCode dalla creazione del cluster in Azure HDInsight
description: Diversi motivi per le creazioni di cluster non riuscite con InvalidNetworkConfigurationErrorCode in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/05/2019
ms.openlocfilehash: 1f60ef5e267b0ee3233f9adb9dc9e8ccd3ac1c65
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734784"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>La creazione del cluster non riesce con InvalidNetworkConfigurationErrorCode in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

Se viene visualizzato il codice `InvalidNetworkConfigurationErrorCode` di errore con la descrizione "la configurazione della rete virtuale non è compatibile con il requisito di HDInsight", in genere indica un problema con la [configurazione della rete virtuale](../hdinsight-plan-virtual-network-deployment.md) per il cluster. In base al resto della descrizione dell'errore, seguire le sezioni seguenti per risolvere il problema.

## <a name="hostname-resolution-failed"></a>"Risoluzione nome host non riuscita"

### <a name="issue"></a>Problema

La descrizione dell'errore contiene "risoluzione nome host non riuscita".

### <a name="cause"></a>Causa

Questo errore indica un problema con la configurazione DNS personalizzata. I server DNS all'interno di una rete virtuale possono inviare query DNS ai resolver ricorsivi di Azure per risolvere i nomi host all'interno della rete virtuale. per informazioni dettagliate, vedere [risoluzione dei nomi nelle reti virtuali](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) . L'accesso ai resolver ricorsivi di Azure viene fornito tramite l'indirizzo IP virtuale 168.63.129.16. Questo indirizzo IP è accessibile solo dalle macchine virtuali di Azure. Quindi non funzionerà se si usa un server DNS locale o se il server DNS è una macchina virtuale di Azure, che non fa parte del vNet del cluster.

### <a name="resolution"></a>Risoluzione

1. Eseguire ssh nella macchina virtuale che fa parte del cluster ed eseguire il comando `hostname -f`. Verrà restituito il nome di dominio completo dell'host (indicato `<host_fqdn>` nelle istruzioni seguenti).

1. Eseguire quindi il comando `nslookup <host_fqdn>` (ad esempio, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`). Se questo comando risolve il nome in un indirizzo IP, significa che il server DNS funziona correttamente. In questo caso, viene generato un caso di supporto con HDInsight e si analizzerà il problema. Nel caso di supporto, includere i passaggi per la risoluzione dei problemi eseguiti. Questo consentirà di risolvere il problema più velocemente.

1. Se il comando precedente non restituisce un indirizzo IP, eseguire `nslookup <host_fqdn> 168.63.129.16` , ad esempio,. `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16` Se questo comando è in grado di risolvere l'indirizzo IP, significa che il server DNS non sta inviando la query al DNS di Azure o non è una macchina virtuale che fa parte dello stesso vNet del cluster.

1. Se non si dispone di una macchina virtuale di Azure che può fungere da server DNS personalizzato nel vNet del cluster, è necessario prima aggiungere questa. Creare una macchina virtuale in vNet, che verrà configurata come server di trasmissione DNS.

1. Dopo aver distribuito una macchina virtuale nella vNet, configurare le regole di invio DNS in questa macchina virtuale. Inviare tutte le richieste di risoluzione dei nomi di IDN a 168.63.129.16 e il resto al server DNS. Di [seguito](../hdinsight-plan-virtual-network-deployment.md) è riportato un esempio di questa configurazione per un server DNS personalizzato.

1. Aggiungere l'indirizzo IP della macchina virtuale come prima voce DNS per la configurazione DNS della rete virtuale.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Non è stato possibile connettersi all'account di archiviazione di Azure"

### <a name="issue"></a>Problema

La descrizione dell'errore contiene "Impossibile connettersi all'account di archiviazione di Azure" o "Impossibile connettersi a SQL Azure".

### <a name="cause"></a>Causa

Archiviazione di Azure e SQL non hanno indirizzi IP fissi, quindi è necessario consentire le connessioni in uscita a tutti gli IP per consentire l'accesso a questi servizi. La procedura di risoluzione esatta varia a seconda che sia stato configurato un gruppo di sicurezza di rete (NSG) o regole definite dall'utente (UDR). Per informazioni dettagliate su queste configurazioni, vedere la sezione [controllo del traffico di rete con HDInsight con gruppi di sicurezza di rete e route definite dall'utente](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) .

### <a name="resolution"></a>Risoluzione

* Se il cluster usa un [gruppo di sicurezza di rete (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Passare alla portale di Azure e identificare il NSG associato alla subnet in cui viene distribuito il cluster. Nella sezione **regole di sicurezza in uscita** , consentire l'accesso in uscita a Internet senza limitazioni (si noti che un numero di **priorità** più basso qui indica una priorità più elevata). Inoltre, nella sezione **subnet** verificare se questo NSG viene applicato alla subnet del cluster.

* Se il cluster USA [route definite dall'utente (UDR)](../../virtual-network/virtual-networks-udr-overview.md).

    Passare alla portale di Azure e identificare la tabella di route associata alla subnet in cui viene distribuito il cluster. Una volta trovata la tabella di route per la subnet, controllare la sezione **Route** .

    Se sono presenti route definite, assicurarsi che siano presenti route per gli indirizzi IP per l'area in cui è stato distribuito il cluster e che il **NextHopType** per ogni route sia **Internet**. Deve essere definita una route per ogni indirizzo IP richiesto documentato nell'articolo citato sopra.

---

### <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
