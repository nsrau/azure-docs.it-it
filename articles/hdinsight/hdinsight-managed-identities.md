---
title: Identità gestite in Azure HDInsight
description: Viene fornita una panoramica dell'implementazione di identità gestite in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: e7be8fbf5f6c2c59e93d48729785dd34bae5955e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327378"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Identità gestite in Azure HDInsight

Un'identità gestita è un'identità registrata in Azure Active Directory (Azure AD) le cui credenziali sono gestite da Azure. Con le identità gestite, non è necessario registrare le entità servizio in Azure AD o gestire le credenziali, ad esempio i certificati.

È possibile usare le identità gestite in Azure HDInsight per consentire ai cluster di accedere ai file di Azure AD Domain Services, di accedere Azure Key Vault o di accedere ai file in Azure Data Lake Storage Gen2.

Esistono due tipi di identità gestite: assegnato dall'utente e assegnato dal sistema. Azure HDInsight Usa identità gestite assegnate dall'utente. Un'identità gestita assegnata dall'utente viene creata come risorsa di Azure autonoma, che può quindi essere assegnata a una o più istanze del servizio di Azure. Al contrario, viene creata un'identità gestita assegnata dal sistema in Azure AD e quindi abilitata direttamente in una specifica istanza del servizio di Azure. Il ciclo di vita dell'identità gestita assegnata dal sistema viene quindi associato alla durata dell'istanza del servizio su cui è abilitata.

## <a name="hdinsight-managed-identity-implementation"></a>Implementazione dell'identità gestita di HDInsight

In Azure HDInsight, viene eseguito il provisioning delle identità gestite in ogni nodo del cluster. Questi componenti Identity, tuttavia, sono utilizzabili solo dal servizio HDInsight. Attualmente non è disponibile alcun metodo supportato per generare token di accesso usando le identità gestite installate nei nodi del cluster HDInsight. Per alcuni servizi di Azure, le identità gestite sono implementate con un endpoint che è possibile usare per acquisire i token di accesso per interagire con altri servizi di Azure.

## <a name="create-a-managed-identity"></a>Creare un'identità gestita

È possibile creare identità gestite con uno dei metodi seguenti:

* [Portale di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Gestione risorse di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

I passaggi rimanenti per la configurazione dell'identità gestita dipendono dallo scenario in cui verrà usato.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scenari di identità gestite in Azure HDInsight

Le identità gestite vengono usate in HDInsight di Azure in più scenari. Per istruzioni dettagliate sull'installazione e sulla configurazione, vedere i documenti correlati:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Bring Your Own Key Kafka (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md)
