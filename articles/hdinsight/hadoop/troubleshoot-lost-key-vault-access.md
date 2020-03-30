---
title: I cluster di Azure HDInsight con crittografia del disco perdono l'accesso all'insieme di credenziali delle chiaviAzure HDInsight clusters with disk encryption lose Key Vault access
description: Passaggi per la risoluzione dei problemi e possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.Troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: 2ae389be25cd8633a53a49cf000796c1510733a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965164"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Scenario: i cluster di Azure HDInsight con crittografia del disco perdono l'accesso all'insieme di credenziali delle chiaviScenario: Azure HDInsight clusters with disk encryption lose Key Vault access

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

L'avviso `The HDInsight cluster is unable to access the key for BYOK encryption at rest`RHC (Resource Health Center), è visualizzato per i cluster BYOK (Bring Your Own Key) in cui i nodi del cluster hanno perso l'accesso ai client Key Vault (KV). Avvisi simili possono essere visualizzati anche su Apache Ambari UI.

## <a name="cause"></a>Causa

L'avviso garantisce che KV sia accessibile dai nodi del cluster, garantendo in tal modo la connessione di rete, l'integrità KV e i criteri di accesso per l'utente assegnato all'identità gestita. Questo avviso è solo un avviso di arresto imminente del broker nei riavvii successivi del nodo, il cluster continua a funzionare fino al riavvio dei nodi.

Passare a Apache Ambari UI per trovare ulteriori informazioni sull'avviso da **Disk Encryption Key Vault Status**. Questo avviso conterrà dettagli sul motivo dell'errore di verifica.

## <a name="resolution"></a>Risoluzione

### <a name="kvaad-outage"></a>Interruzione KV/AA

Esaminare [la disponibilità e la ridondanza](../../key-vault/key-vault-disaster-recovery-guidance.md) di Archiviazione delle chiavi di Azure e la pagina dello stato di Azure per altri dettaglihttps://status.azure.com/

### <a name="kv-accidental-deletion"></a>Cancellazione accidentale KV

* Ripristinare la chiave eliminata su KV per il recupero automatico. Per ulteriori informazioni, consultate Recuperare la [chiave eliminata.](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey)
* Contatta il team KV per recuperare da cancellazioni accidentali.

### <a name="kv-access-policy-changed"></a>Criteri di accesso KV modificati

Ripristinare i criteri di accesso per l'utente assegnato all'identità gestita assegnato al cluster HDI per l'accesso al KV.

### <a name="key-permitted-operations"></a>Operazioni chiave consentite

Per ogni tasto in KV, è possibile scegliere l'insieme di operazioni consentite. Assicurarsi di aver abilitato le operazioni di wrapping e unwrap per il tasto BYOK

### <a name="expired-key"></a>Chiave scaduta

Se la scadenza è trascorsa e la chiave non viene ruotata, ripristinare la chiave da HSM di backup o contattare il team KV per cancellare la data di scadenza.

### <a name="kv-firewall-blocking-access"></a>Il firewall KV blocca l'accesso

Correggere le impostazioni del firewall KV per consentire ai nodi del cluster BYOK di accedere al KV.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>Regole del gruppo di sicurezza di rete sull'accesso al blocco della rete virtuale

Controllare le regole del gruppo di sicurezza di rete associate alla rete virtuale collegata al cluster.

## <a name="mitigation-and-prevention-steps"></a>Misure di mitigazione e prevenzione

### <a name="kv-accidental-deletion"></a>Cancellazione accidentale KV

* Configurare l'insieme di credenziali delle chiavi con il [set di blocco delle risorse](../../azure-resource-manager/management/lock-resources.md).
* Eseguire il backup delle chiavi nel relativo modulo di sicurezza hardware.

### <a name="key-deletion"></a>Cancellazione della chiave

Il cluster deve essere eliminato prima dell'eliminazione della chiave.

### <a name="kv-access-policy-changed"></a>Criteri di accesso KV modificati

Controllare e testare regolarmente i criteri di accesso.

### <a name="expired-key"></a>Chiave scaduta

* Eseguire il backup delle chiavi nell'HSM.
* Utilizzare una chiave senza alcun set di scadenza.
* Se è necessario impostare la scadenza, ruotare le chiavi prima della data di scadenza.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
