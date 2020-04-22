---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottieni suggerimenti e dettagli sullo sviluppo per Hadoop, Spark, R Server, Hive e altro ancora.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: cdb31f1a46d7f46b69e9e0ad47a77ba6b32a50a0
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770824"
---
# <a name="release-notes"></a>Note sulla versione

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Summary

Azure HDInsight is one of the most popular services among enterprise customers for open-source analytics on Azure.

## <a name="release-date-01092020"></a>Data di rilascio: 01/09/2020

Questa versione si applica sia per HDInsight 3.6 e 4.0.This release applies both for HDInsight 3.6 and 4.0. La versione HDInsight viene resa disponibile per tutte le aree in diversi giorni. La data di rilascio qui indica la prima data di rilascio dell'area. Se non vedi le modifiche di seguito, attendi che il rilascio sia attivo nella tua regione in diversi giorni.

> [!IMPORTANT]  
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere l'[articolo sul controllo delle versioni di HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Nuove funzionalità
### <a name="tls-12-enforcement"></a>Imposizione di TLS 1.2
Transport Layer Security (TLS) e Secure Sockets Layer (SSL) sono protocolli di crittografia che consentono di proteggere le comunicazioni su una rete di computer. Ulteriori informazioni su [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). HDInsight usa TLS 1.2 negli endpoint HTTP pubblici, ma TLS 1.1 è ancora supportato per la compatibilità con le versioni precedenti. 

Con questa versione, i clienti possono scegliere TLS 1.2 solo per tutte le connessioni tramite l'endpoint del cluster pubblico. A tale scopo, viene introdotta la nuova proprietà **minSupportedTlsVersion** che può essere specificata durante la creazione del cluster. Se la proprietà non è impostata, il cluster supporta ancora TLS 1.0, 1.1 e 1.2, che è lo stesso del comportamento di oggi. I clienti possono impostare il valore di questa proprietà su "1.2", il che significa che il cluster supporta solo TLS 1.2 e versioni successive. Per ulteriori informazioni, vedere [Transport Layer Security](./transport-layer-security.md).

### <a name="bring-your-own-key-for-disk-encryption"></a>Porta la tua chiave per la crittografia del disco
Tutti i dischi gestiti in HDInsight sono protetti con Crittografia del servizio di archiviazione di Azure. I dati su tali dischi vengono crittografati dalle chiavi gestite da Microsoft per impostazione predefinita. A partire da questa versione, è possibile portare la propria chiave (BYOK) per la crittografia del disco e gestirla usando l'insieme di credenziali delle chiavi di Azure.Starting from this release, you can Bring Your Own Key (BYOK) for disk encryption and manage it using Azure Key Vault. La crittografia BYOK è una configurazione in un solo passaggio durante la creazione del cluster senza costi aggiuntivi. È sufficiente registrare HDInsight come identità gestita con L'insieme di credenziali delle chiavi di Azure e aggiungere la chiave di crittografia quando si crea il cluster. Per ulteriori informazioni, vedere Crittografia del [disco di chiave gestita dal cliente](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Deprecazione
Nessuna deprecazione per questa versione. Per prepararsi alle prossime deprecazioni, vedere [Modifiche imminenti](#upcoming-changes).

## <a name="behavior-changes"></a>Modifiche funzionali
Nessuna modifica del comportamento per questa versione. Per prepararsi alle modifiche imminenti, vedere [Modifiche imminenti](#upcoming-changes).

## <a name="upcoming-changes"></a>Modifiche imminenti
Le seguenti modifiche verranno apportate nelle versioni future. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>È necessaria una macchina virtuale minima di 4 core per il nodo Head 
Per garantire la disponibilità elevata e l'affidabilità dei cluster HDInsight è necessaria almeno una macchina virtuale a 4 core. A partire dal 6 aprile 2020, i clienti possono scegliere solo 4 core o versione superiore della VM come nodo Head per i nuovi cluster HDInsight. I cluster esistenti continueranno a essere eseguiti come previsto. 

### <a name="esp-spark-cluster-node-size-change"></a>Modifica delle dimensioni del nodo del cluster Spark Di ESP 
Nella prossima versione, la dimensione minima consentita del nodo per il cluster ESP Spark verrà modificata in Standard_D13_V2. Le macchine virtuali della serie A potrebbero causare problemi del cluster ESP a causa della capacità di CPU e memoria relativamente bassa. Le macchine virtuali serie A saranno deprecate per la creazione di nuovi cluster ESP.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passaggio ai set di scalabilità di macchine virtuali di Azure
HDInsight ora usa le macchine virtuali di Azure per eseguire il provisioning del cluster. Nella prossima versione, HDInsight userà invece i set di scalabilità delle macchine virtuali di Azure.In the upcoming release, HDInsight will use Azure virtual machine scale sets instead. Altre informazioni sui set di scalabilità delle macchine virtuali di Azure.See more about Azure virtual machine scale sets.

### <a name="hbase-20-to-21"></a>HBase 2.0 a 2.1
Nella prossima versione HDInsight 4.0, la versione di HBase verrà aggiornata dalla versione 2.0 alla versione 2.1.

## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni dei cluster. 

## <a name="component-version-change"></a>Modifica della versione del componente
Nessuna modifica della versione del componente per questa versione. È possibile trovare le versioni dei componenti correnti per HDInsight 4.0 ad HDInsight 3.6 qui.

## <a name="known-issues"></a>Problemi noti

A partire dal 18 marzo 2020 alcuni clienti di Azure HDInsight in Europa occidentale o Nord Europa hanno ricevuto notifiche di errore durante la creazione o la scalabilità di cluster HDInsight in queste aree. Gli errori relativi a questo problema includono:

- Si è verificato un errore interno del server durante l'elaborazione della richiesta. Riprovare la richiesta o contattare il supporto tecnico.
- Almeno un'operazione di distribuzione delle risorse non è riuscita. Elencare le operazioni di distribuzione per i dettagli. Vedere https://aka.ms/DeployOperations per i dettagli sull'utilizzo
- User SubscriptionId\<'\>ID sottoscrizione ' non dispone\<di core per creare la risorsa ' nome cluster>'. Obbligatorio: \<\>X , Disponibile: 0.

Gli ingegneri sono a conoscenza di questo problema e stanno indagando attivamente.

Per ulteriore assistenza, creare una richiesta di [supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
