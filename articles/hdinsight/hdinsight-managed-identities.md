---
title: Identità gestite in Azure HDInsight
description: Viene fornita una panoramica dell'implementazione dell'identità gestite in Azure HDInsight.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: hrasheed
ms.openlocfilehash: 5012b669b7460a44cb2732d7db7bf76fd1f567cf
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766950"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Identità gestite in Azure HDInsight

Un'identità gestita è un'identità registrata in Azure Active Directory (Azure AD) le cui credenziali vengono gestite da Azure. Con identità gestite, non è necessario registrare le entità servizio in Azure AD o gestire le credenziali, ad esempio i certificati.

Identità gestite è utilizzabile in Azure HDInsight per consentire i cluster accedere ai servizi di dominio Azure AD, accedere ad Azure Key Vault o accedere ai file in Azure Data Lake Storage Gen2.

Esistono due tipi di identità gestite: assegnata dall'utente e assegnato dal sistema. Azure HDInsight Usa identità gestite assegnata dall'utente. Un'identità gestita assegnata dall'utente viene creata come risorsa di Azure che è quindi possibile assegnare a uno o più istanze di servizio di Azure autonoma. Al contrario, un'identità gestita assegnato dal sistema viene creata in Azure AD e quindi abilitata automaticamente direttamente in un'istanza di servizio di Azure specifico. Il ciclo di vita dell'identità assegnata dal sistema gestito viene quindi collegata al ciclo di vita dell'istanza del servizio a cui è abilitata.

## <a name="hdinsight-managed-identity-implementation"></a>Implementazione di identity HDInsight gestiti

In Azure HDInsight, vengono effettuato il provisioning di identità gestite in ogni nodo del cluster. Questi componenti di identità, tuttavia, sono solo utilizzabili dal servizio HDInsight. Non è attualmente supportato alcun metodo per generare i token di accesso usando l'identità gestite installata nei nodi del cluster HDInsight. Per alcuni servizi di Azure, le identità gestito sono implementate con un endpoint che è possibile usare per acquisire token di accesso per l'interazione con altri servizi di Azure per conto proprio.

## <a name="create-a-managed-identity"></a>Creare un'identità gestita

È possibile creare identità gestite con uno qualsiasi dei metodi seguenti:

* [Portale di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

I passaggi rimanenti per configurare l'identità gestita variano a seconda lo scenario in cui verrà usato.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scenari di identità gestita in Azure HDInsight

Identità gestite vengono usate in Azure HDInsight in più scenari. Vedere i documenti correlati per il programma di installazione dettagliata e istruzioni di configurazione:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md)
