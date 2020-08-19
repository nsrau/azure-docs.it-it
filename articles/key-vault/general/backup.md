---
title: Eseguire il backup di un segreto, una chiave o un certificato archiviato in Azure Key Vault | Microsoft Docs
description: Usare questo documento per eseguire il backup di un segreto, una chiave o un certificato archiviato in Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: a1c07432dcf90759662e8f4aaedc760abd18157c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585934"
---
# <a name="azure-key-vault-backup"></a>Backup di Azure Key Vault

Questo documento spiega come eseguire il backup di segreti, chiavi e certificati archiviati nell'insieme di credenziali delle chiavi. Un backup ha lo scopo di fornire una copia offline di tutti i segreti nel caso improbabile in cui si perda l'accesso all'insieme di credenziali delle chiavi.

## <a name="overview"></a>Panoramica

Azure Key Vault fornisce automaticamente diverse funzionalità che aiutano a mantenere la disponibilità ed evitare la perdita di dati. Il backup dei segreti deve essere eseguito solo in presenza di una motivazione aziendale critica. Il backup dei segreti nell'insieme di credenziali delle chiavi può infatti comportare una serie di problemi operativi, come la gestione di più set di log, autorizzazioni e backup quando i segreti scadono o ruotano.

Key Vault mantiene la disponibilità in scenari di emergenza ed esegue automaticamente il failover delle richieste a un'area associata senza che sia necessario l'intervento di un utente. Per altre informazioni, vedere [Disponibilità e ridondanza in Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).

Per proteggere i segreti da eliminazioni accidentali o dannose, configurare l'eliminazione temporanea e la protezione dall'eliminazione nell'insieme di credenziali delle chiavi. Per altre informazioni, vedere [Panoramica dell'eliminazione temporanea di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/soft-delete-overview).

## <a name="limitations"></a>Limitazioni

Azure Key Vault attualmente non offre un modo per eseguire il backup di un intero insieme di credenziali delle chiavi in un'unica operazione. Qualsiasi tentativo di usare i comandi elencati in questo documento per eseguire un backup automatico di un insieme di credenziali delle chiavi può generare errori per i quali non si riceverà assistenza da Microsoft o dal team di Azure Key Vault. 

Considerare anche le conseguenze seguenti:

* Il backup di segreti con più versioni potrebbe causare errori di timeout.
* Un backup crea uno snapshot temporizzato. I segreti potrebbero essere rinnovati durante un backup causando una mancata corrispondenza delle chiavi di crittografia.
* Se si superano i limiti del servizio Key Vault per le richieste al secondo, l'insieme di credenziali delle chiavi verrà limitato e il backup non riuscirà.

## <a name="design-considerations"></a>Considerazioni sulla progettazione

Quando si esegue il backup di un oggetto dell'insieme di credenziali delle chiavi, come un segreto, una chiave o un certificato, l'operazione di backup scarica l'oggetto come BLOB crittografato. Questo BLOB non può essere decrittografato all'esterno di Azure. Per ottenere dati utilizzabili da questo BLOB, è necessario ripristinare il BLOB in un insieme di credenziali delle chiavi all'interno della stessa sottoscrizione di Azure e della stessa [area geografica di Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="prerequisites"></a>Prerequisiti

Per eseguire il backup di un oggetto dell'insieme di credenziali delle chiavi, è necessario avere: 

* Autorizzazioni a livello di collaboratore o superiori per una sottoscrizione di Azure.
* Insieme di credenziali delle chiavi primario che contiene i segreti di cui si vuole eseguire il backup.
* Insieme di credenziali delle chiavi secondario in cui verranno ripristinati i segreti.

## <a name="back-up-and-restore-from-the-azure-portal"></a>Eseguire il backup e il ripristino dal portale di Azure

Per eseguire il backup e il ripristino di oggetti usando il portale di Azure, seguire le procedure illustrate in questa sezione.

### <a name="back-up"></a>Eseguire il backup

1. Accedere al portale di Azure.
2. Selezionare l'insieme di credenziali delle chiavi.
3. Passare all'oggetto (segreto, chiave o certificato) di cui si vuole eseguire il backup.

    ![Screenshot che mostra dove selezionare l'impostazione Chiavi e un oggetto in un insieme di credenziali delle chiavi.](../media/backup-1.png)

4. Selezionare l'oggetto.
5. Selezionare **Scarica il backup**.

    ![Screenshot che mostra dove selezionare il pulsante Scarica il backup in un insieme di credenziali delle chiavi.](../media/backup-2.png)
    
6. Selezionare **Download**.

    ![Screenshot che mostra dove selezionare il pulsante Scarica in un insieme di credenziali delle chiavi.](../media/backup-3.png)
    
7. Archiviare il BLOB crittografato in una posizione sicura.

### <a name="restore"></a>Restore

1. Accedere al portale di Azure.
2. Selezionare l'insieme di credenziali delle chiavi.
3. Passare al tipo di oggetto (segreto, chiave o certificato) di cui si vuole eseguire il ripristino.
4. Selezionare **Ripristina il backup**.

    ![Screenshot che mostra dove selezionare il pulsante Ripristina il backup in un insieme di credenziali delle chiavi.](../media/backup-4.png)
    
5. Passare al percorso in cui è stato archiviato il BLOB crittografato.
6. Selezionare **OK**.

## <a name="back-up-and-restore-from-the-azure-cli"></a>Eseguire il backup e il ripristino dall'interfaccia della riga di comando di Azure

```azurecli
## Log in to Azure
az login

## Set your subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a provider
az provider register -n Microsoft.KeyVault

## Back up a certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>Passaggi successivi

Attivare la [registrazione e il monitoraggio](https://docs.microsoft.com/azure/key-vault/general/logging) per Azure Key Vault.
