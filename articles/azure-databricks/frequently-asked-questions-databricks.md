---
title: 'Azure Databricks: Domande frequenti e guida | Microsoft Docs'
description: "Ottenere risposte alle domande più comuni e informazioni sulla risoluzione dei problemi di Azure Databricks."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: nitinme
ms.openlocfilehash: b6b001087cba5f8550d4fea3e4a2f7c1c865beae
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="azure-databricks-preview-common-questions-and-help"></a>Anteprima di Azure Databricks: Domande frequenti e guida

Questo articolo elenca le domande più importanti degli utenti su Azure Databricks. Elenca anche alcuni problemi comuni che si possono incontrare nell'uso di Azure Databricks. Per altre informazioni su Azure Databricks, vedere [Cos'è Azure Databricks?](what-is-azure-databricks.md) 

## <a name="common-questions"></a>Domande frequenti

Questa sezione elenca le domande più comuni relative ad Azure Databricks.

### <a name="can-i-use-my-own-keys-for-local-encryption"></a>È possibile usare le proprie chiavi per crittografia locale? 
Nella versione corrente l'uso delle proprie chiavi da Azure Keyvault non è supportato. 

### <a name="can-i-use-azure-vnets-with-azure-databricks"></a>È possibile usare le reti virtuali di Azure con Azure Databricks?
Come parte del provisioning di Azure Databricks, viene creata una nuova rete virtuale. Come parte di questa versione, è possibile usare la propria rete virtuale di Azure.

### <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>Come si accede ad Azure Data Lake Store da un notebook? 

1. In Azure Active Directory eseguire il provisioning di un'entità servizio e registrare la relativa chiave.
2. Assegnare le autorizzazioni necessarie all'entità servizio in Azure Data Lake Store.
3. Per accedere a un file in Azure Data Lake Store, usare le credenziali dell'entità servizio in notebook.

Per altre informazioni, vedere [Use Data Lake Store with Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store) (Usare Data Lake Store con Azure Databricks).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Questa sezione descrive come risolvere i problemi comuni con Azure Databricks.

### <a name="issue-your-account-email-does-not-have-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problema: L'account {indirizzo di posta elettronica} non dispone del ruolo di proprietario o collaboratore sulla risorsa dell'area di lavoro Databricks nel portale di Azure.

**Messaggio di errore**

L'account {indirizzo di posta elettronica} non dispone del ruolo di proprietario o collaboratore sulla risorsa dell'area di lavoro Databricks nel portale di Azure. Questo errore può verificarsi anche nel caso di un utente guest nel tenant. Chiedere all'amministratore di concedere l'accesso all'utente o di aggiungerlo come utente direttamente nell'area di lavoro Databricks. 

**Soluzione**

Per inizializzare il tenant è necessario accedere come normale utente del tenant, non come utente guest. È inoltre necessario un ruolo di collaboratore sulla risorsa dell'area di lavoro Databricks. È possibile concedere un accesso utente dalla scheda **Controllo di accesso (IAM)** all'interno dell'area di lavoro Azure Databricks nel portale di Azure.

### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problema: L'account {indirizzo di posta elettronica} non è stato registrato in Databricks 

**Soluzione**

Se l'utente non ha creato l'area di lavoro e viene aggiunto come utente dell'area di lavoro, contattare la persona che ha creato l'area di lavoro chiedendole di aggiungere l'utente usando la console di amministrazione di Azure Databricks. Per istruzioni, vedere [Adding and managing users](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html) (Aggiunta e gestione degli utenti). Se l'utente ha creato l'area di lavoro e riceve comunque questo errore, provare a fare clic di nuovo su "Inizializza area di lavoro" nel portale di Azure.

### <a name="issue-cloud-provider-launch-failure-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Problema: Errore di avvio del provider di servizi cloud: si è verificato un errore del provider di servizi cloud durante la configurazione del cluster.

**Messaggio di errore**

Errore di avvio del provider di servizi cloud: si è verificato un errore del provider di servizi cloud durante la configurazione del cluster. Per altre informazioni, vedere la guida di Databricks. Codice di errore di Azure: PublicIPCountLimitReached. Messaggio di errore di Azure: Non è possibile creare più di 60 indirizzi IP pubblici per questa sottoscrizione in questa area.

**Soluzione**

Il cluster Azure Databricks usa un indirizzo IP pubblico per ogni nodo. Se la sottoscrizione ha già usato tutti i suoi indirizzi IP pubblici, è necessario [richiedere di aumentare la quota](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request). Scegliere **Quota** come **Tipo di problema**, **Rete ARM** come **Tipo di quota** e richiedere un aumento della quota di indirizzi IP pubblici in **Dettagli** (ad esempio, se il limite è 60 e si desidera creare un cluster da 100 nodi, richiedere un aumento del limite a 160).

## <a name="next-steps"></a>Passaggi successivi
Per istruzioni dettagliate per la creazione di una data factory della versione 2, vedere le esercitazioni seguenti:

- [Guida introduttiva: Introduzione ad Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Informazioni su Azure Databricks](what-is-azure-databricks.md)

