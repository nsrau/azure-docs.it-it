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
ms.openlocfilehash: daae9c16797ad9c1b85635f5aec7d0cf884e003f
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206011"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Identità gestite in Azure HDInsight

Un'identità gestita è un'identità registrata in Azure Active Directory (Azure AD) le cui credenziali sono gestite da Azure. Con le identità gestite, non è necessario registrare le entità servizio in Azure AD o gestire le credenziali, ad esempio i certificati.

Le identità gestite vengono usate in Azure HDInsight per accedere ai servizi di Azure AD dominio o ai file di accesso in Azure Data Lake Storage Gen2 quando necessario.

Esistono due tipi di identità gestite: assegnato dall'utente e assegnato dal sistema. Azure HDInsight supporta solo le identità gestite assegnate dall'utente. HDInsight non supporta identità gestite assegnate dal sistema. Un'identità gestita assegnata dall'utente viene creata come risorsa di Azure autonoma, che può quindi essere assegnata a una o più istanze del servizio di Azure. Al contrario, viene creata un'identità gestita assegnata dal sistema in Azure AD e quindi abilitata direttamente in una specifica istanza del servizio di Azure. Il ciclo di vita dell'identità gestita assegnata dal sistema viene quindi associato alla durata dell'istanza del servizio su cui è abilitata.

## <a name="hdinsight-managed-identity-implementation"></a>Implementazione dell'identità gestita di HDInsight

In Azure HDInsight, viene eseguito il provisioning delle identità gestite in ogni nodo del cluster. Questi componenti Identity, tuttavia, sono utilizzabili solo dal servizio HDInsight. Attualmente non è disponibile alcun metodo supportato per generare token di accesso usando le identità gestite installate nei nodi del cluster HDInsight. Per alcuni servizi di Azure, le identità gestite sono implementate con un endpoint che è possibile usare per acquisire i token di accesso per interagire con altri servizi di Azure.

## <a name="create-a-managed-identity"></a>Creare un'identità gestita

È possibile creare identità gestite con uno dei metodi seguenti:

* [Azure portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

I passaggi rimanenti per la configurazione dell'identità gestita dipendono dallo scenario in cui verrà usato.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scenari di identità gestite in Azure HDInsight

Le identità gestite vengono usate in HDInsight di Azure in più scenari. Per istruzioni dettagliate sull'installazione e sulla configurazione, vedere i documenti correlati:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Crittografia del disco della chiave gestita dal cliente](disk-encryption.md)

## <a name="faq"></a>Domande frequenti
### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Cosa accade se si elimina l'identità gestita dopo la creazione del cluster?
Si verificano problemi nel cluster quando l'identità gestita è necessaria. Non è attualmente possibile aggiornare o modificare l'identità di gestione dopo la creazione del cluster. Si consiglia pertanto di verificare che l'identità gestita non venga eliminata durante il runtime del cluster. In alternativa, è possibile ricreare il cluster e assegnare una nuova identità gestita.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md)
