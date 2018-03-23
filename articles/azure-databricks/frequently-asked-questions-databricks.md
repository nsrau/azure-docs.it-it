---
title: 'Azure Databricks: Domande frequenti e guida | Microsoft Docs'
description: Ottenere risposte alle domande più comuni e informazioni sulla risoluzione dei problemi di Azure Databricks.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: nitinme
ms.openlocfilehash: 5da6ffc346cc0e7f0f83bf4a4c33600b668a17ca
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2018
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Domande frequenti su Azure Databricks

Questo articolo elenca le domande più importanti degli utenti su Azure Databricks. Vengono inoltre elencati alcuni problemi comuni che si possono verificare durante l'uso di Databricks. Per altre informazioni, vedere [Informazioni su Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-my-own-keys-for-local-encryption"></a>È possibile usare le proprie chiavi per crittografia locale? 
Nella versione corrente l'uso delle proprie chiavi da Azure Key Vault non è supportato. 

## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>È possibile usare reti virtuali di Azure con Databricks?
Come parte del provisioning di Databricks, viene creata una nuova rete virtuale. In questa versione, è possibile usare la propria rete virtuale di Azure.

## <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>Come si accede ad Azure Data Lake Store da un notebook? 

A tale scopo, seguire questa procedura:
1. In Azure Active Directory (Azure AD) eseguire il provisioning di un'entità servizio e registrare la relativa chiave.
2. Assegnare le autorizzazioni necessarie all'entità servizio in Data Lake Store.
3. Per accedere a un file in Data Lake Store, usare le credenziali dell'entità servizio in Notebook.

Per altre informazioni, vedere [Use Data Lake Store with Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html) (Usare Data Lake Store con Azure Databricks).

## <a name="fix-common-problems"></a>Risolvere i problemi frequenti

Ecco alcuni problemi che possono verificarsi con Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problema: la sottoscrizione non è registrata per usare lo spazio dei nomi 'Microsoft.Databricks'

#### <a name="error-message"></a>Messaggio di errore

"La sottoscrizione non è registrata per usare lo spazio dei nomi 'Microsoft.Databricks'. Vedere https://aka.ms/rps-not-found per informazioni su come registrare le sottoscrizioni. (codice: MissingSubscriptionRegistration)"

#### <a name="solution"></a>Soluzione

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Sottoscrizioni**, la sottoscrizione in uso e quindi **Provider di risorse**. 
3. Nell'elenco di provider di risorse selezionare **Registra** per **Microsoft.Databricks**. Per registrare il provider di risorse, è necessario il ruolo di proprietario o collaboratore della sottoscrizione.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problema: l'account {indirizzo di posta elettronica} non dispone del ruolo di proprietario o collaboratore per la risorsa dell'area di lavoro Databricks nel portale di Azure

#### <a name="error-message"></a>Messaggio di errore

"L'account {indirizzo di posta elettronica} non dispone del ruolo di proprietario o collaboratore sulla risorsa dell'area di lavoro Databricks nel portale di Azure. Questo errore può verificarsi anche nel caso di un utente guest nel tenant. Chiedere all'amministratore di concedere l'accesso all'utente o di aggiungerlo come utente direttamente nell'area di lavoro Databricks". 

#### <a name="solution"></a>Soluzione

Di seguito sono riportate due soluzioni a questo problema:

* Per inizializzare il tenant è necessario effettuare l'accesso come normale utente del tenant, non come utente guest. È inoltre necessario un ruolo di collaboratore sulla risorsa dell'area di lavoro Databricks. È possibile concedere un accesso utente dalla scheda **Controllo di accesso (IAM)** all'interno dell'area di lavoro Databricks nel portale di Azure.

* Questo errore può verificarsi anche se il nome di dominio di posta elettronica viene assegnato a più directory in Azure AD. Come soluzione alternativa di questo problema, creare un nuovo utente nella directory che contiene la sottoscrizione con l'area di lavoro Databricks.

    a. Nel portale di Azure passare ad Azure AD. Selezionare **Utenti e gruppi** > **Aggiungi un utente**.

    b. Aggiungere un utente con indirizzo di posta elettronica `@<tenant_name>.onmicrosoft.com` invece di `@<your_domain>`. È possibile trovare questa opzione in **Domini personalizzati** in Azure AD nel portale di Azure.
    
    c. Concedere a questo nuovo utente il ruolo **Collaboratore** per la risorsa dell'area di lavoro Databricks.
    
    d. Accedere al portale di Azure con il nuovo utente e trovare l'area di lavoro Databricks.
    
    e. Avviare l'area di lavoro Databricks con questo account utente.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problema: L'account {indirizzo di posta elettronica} non è stato registrato in Databricks 

#### <a name="solution"></a>Soluzione

Se l'utente non ha creato l'area di lavoro e viene aggiunto come utente, contattare la persona che ha creato l'area di lavoro. Chiedere di aggiungere l'utente usando la console di amministrazione di Azure Databricks. Per istruzioni, vedere [Adding and managing users](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html) (Aggiunta e gestione degli utenti). Se l'utente ha creato l'area di lavoro e riceve comunque questo errore, provare a selezionare di nuovo **Inizializza l'area di lavoro** nel portale di Azure.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problema: errore di avvio del provider dei servizi cloud durante la configurazione del cluster (PublicIPCountLimitReached)

#### <a name="error-message"></a>Messaggio di errore

"Errore di avvio del provider di servizi cloud: si è verificato un errore del provider di servizi cloud durante la configurazione del cluster. Per altre informazioni, vedere la guida di Databricks. Codice di errore di Azure: PublicIPCountLimitReached. Messaggio di errore di Azure: Non è possibile creare più di 60 indirizzi IP pubblici per questa sottoscrizione in questa area".

#### <a name="solution"></a>Soluzione

I cluster Databricks usano un indirizzo IP pubblico per ogni nodo. Se la sottoscrizione ha già usato tutti i suoi indirizzi IP pubblici, è necessario [richiedere di aumentare la quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). Scegliere **Quota** come **Tipo di problema** e **Rete: ARM** come **Tipo di quota**. In **Dettagli**, richiedere un aumento della quota dell'indirizzo IP pubblico. Se ad esempio il limite corrente è 60 e si vuole creare un cluster a 100 nodi, richiedere l'aumento del limite a 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problema: un secondo tipo di errore di avvio del provider dei servizi cloud durante la configurazione del cluster (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Messaggio di errore

"Errore di avvio del provider di servizi cloud: si è verificato un errore del provider di servizi cloud durante la configurazione del cluster. Per altre informazioni, vedere la guida di Databricks.
Codice di errore di Azure: messaggio di errore di MissingSubscriptionRegistration Azure: la sottoscrizione non è registrata per l'uso dello spazio dei nomi 'Microsoft.Compute'. Vedere https://aka.ms/rps-not-found per informazioni su come registrare le sottoscrizioni."

#### <a name="solution"></a>Soluzione

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Sottoscrizioni**, la sottoscrizione in uso e quindi **Provider di risorse**. 
3. Nell'elenco di provider di risorse selezionare **Registra** per **Microsoft.Compute**. Per registrare il provider di risorse, è necessario il ruolo di proprietario o collaboratore della sottoscrizione.

Per istruzioni più dettagliate, vedere [Provider e tipi di risorse](../azure-resource-manager/resource-manager-supported-services.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Problema: per accedere alle risorse dell'organizzazione, Azure Databricks necessita di autorizzazioni che solo un amministratore può concedere.

#### <a name="background"></a>Background

Azure Databricks è integrato con Azure AD. Ciò consente di impostare le autorizzazioni all'interno di Azure Databricks (ad esempio, per notebook o cluster) specificando gli utenti da Azure AD. Per elencare i nomi degli utenti da Azure AD, Azure Databricks richiede l'autorizzazione di lettura per tali informazioni, per la quale è necessario il consenso. Se il consenso non è ancora disponibile, viene visualizzato l'errore.

#### <a name="solution"></a>Soluzione

Accedere come amministratore globale al portale di Azure. Per Azure Active Directory, passare alla scheda **Impostazioni utente** e assicurarsi che l'opzione **Gli utenti possono fornire il consenso alle app che accedono ai dati aziendali per loro conto** sia impostata su **Sì**.

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva: Introduzione ad Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Informazioni su Azure Databricks](what-is-azure-databricks.md)

