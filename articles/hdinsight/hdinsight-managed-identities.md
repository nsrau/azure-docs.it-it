---
title: Identità gestite in Azure HDInsightManaged identities in Azure HDInsight
description: Viene fornita una panoramica dell'implementazione delle identità gestite in Azure HDInsight.Provides an overview of the implementation of managed identities in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 1081865a2e138af38ba171197719f08dedf6ffdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408929"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Identità gestite in Azure HDInsightManaged identities in Azure HDInsight

Un'identità gestita è un'identità registrata in Azure Active Directory (Azure AD) le cui credenziali sono gestite da Azure.A managed identity is an identity registered in Azure Active Directory (Azure AD) whose credentials are managed by Azure. Con le identità gestite non è necessario registrare le entità servizio in Azure AD. In alternativa, è possibile mantenere le credenziali, ad esempio i certificati.

Le identità gestite vengono usate in Azure HDInsight per accedere ai servizi di dominio di Azure AD o accedere ai file in Archiviazione data lake di Azure Gen2 quando necessario.

Esistono due tipi di identità gestite: assegnate dall'utente e dal sistema. Azure HDInsight supporta solo identità gestite assegnate dall'utente. HDInsight non supporta le identità gestite assegnate dal sistema. Un'identità gestita assegnata dall'utente viene creata come risorsa autonoma di Azure, che è quindi possibile assegnare a una o più istanze del servizio di Azure.A user-assigned managed identity is created as a standalone Azure resource, which you can then assign to one or more Azure service instances. Al contrario, un'identità gestita assegnata dal sistema viene creata in Azure AD e quindi abilitata direttamente in una particolare istanza del servizio di Azure automaticamente. La durata dell'identità gestita assegnata dal sistema viene quindi legata alla durata dell'istanza del servizio in cui è abilitata.

## <a name="hdinsight-managed-identity-implementation"></a>Implementazione dell'identità gestita HDInsightHDInsight managed identity implementation

In Azure HDInsight viene eseguito il provisioning delle identità gestite in ogni nodo del cluster. Questi componenti di identità, tuttavia, sono utilizzabili solo dal servizio HDInsight.These identity components, however, are only usable by the HDInsight service. Attualmente non è disponibile alcun metodo supportato per generare token di accesso usando le identità gestite installate nei nodi del cluster HDInsight.There's currently no supported method to generate access tokens using the managed identities installed on HDInsight cluster nodes. Per alcuni servizi di Azure, le identità gestite vengono implementate con un endpoint che è possibile usare per acquisire token di accesso. Usare i token per interagire con altri servizi di Azure da soli.

## <a name="create-a-managed-identity"></a>Creare un'identità gestitaCreate a managed identity

Le identità gestite possono essere create con uno dei metodi seguenti:Managed identities can be created with any of the following methods:

* [Azure portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

I passaggi rimanenti per la configurazione dell'identità gestita dipendono dallo scenario in cui verrà utilizzata.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scenari di identità gestite in Azure HDInsightManaged identity scenarios in Azure HDInsight

Le identità gestite vengono usate in Azure HDInsight in più scenari. Vedere i documenti correlati per istruzioni dettagliate sull'impostazione e sulla configurazione:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Pacchetto di sicurezza aziendale](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Crittografia del disco con chiavi gestite dal cliente](disk-encryption.md)

## <a name="faq"></a>Domande frequenti

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Cosa succede se si elimina l'identità gestita dopo la creazione del cluster?

I problemi del cluster si verificano quando è necessaria l'identità gestita. Attualmente non è possibile aggiornare o modificare un'identità gestita dopo la creazione del cluster. È quindi consigliabile assicurarsi che l'identità gestita non venga eliminata durante il runtime del cluster. In alternativa, è possibile ricreare il cluster e assegnare una nuova identità gestita.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md)
