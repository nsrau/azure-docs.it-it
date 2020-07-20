---
title: Backup di Azure Key Vault | Microsoft Docs
description: Usare questo documento per eseguire il backup di un segreto, una chiave o un certificato archiviato in Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: 8a152e2771f0b207e81f42c6ecae2e4d14605051
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156354"
---
# <a name="azure-key-vault-backup"></a>Backup di Azure Key Vault

Questo documento illustra come eseguire un backup dei singoli segreti, chiavi e certificati archiviati nell'insieme di credenziali delle chiavi. Questo backup è destinato a fornire una copia offline di tutti i segreti nel caso improbabile in cui si perda l'accesso all'insieme di credenziali delle chiavi.

## <a name="overview"></a>Panoramica

Key Vault fornisce automaticamente diverse funzionalità per mantenere la disponibilità ed evitare la perdita di dati. Questo backup deve essere eseguito solo se è presente una motivazione aziendale critica per mantenere un backup dei segreti. Il backup dei segreti nell'insieme di credenziali delle chiavi può introdurre ulteriori problemi operativi come la gestione di più set di log, autorizzazioni e backup quando i segreti scadono o ruotano.

Key Vault mantiene la disponibilità in scenari di emergenza ed esegue automaticamente il failover delle richieste a un'area associata senza che sia necessario alcun intervento da parte di un utente. Per altre informazioni, vedere il collegamento seguente. [Ripristino di emergenza di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)

Key Vault protegge da eliminazioni accidentali e dannose dei segreti tramite l'eliminazione temporanea e la protezione dall'eliminazione. Se si vuole una protezione da eliminazioni accidentali o dannose dei segreti, configurare l'eliminazione temporanea e la protezione dall'eliminazione per l'insieme di credenziali delle chiavi. Per altre informazioni, vedere il documento seguente. [Ripristino di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)

## <a name="limitations"></a>Limitazioni

Azure Key Vault attualmente non supporta un modo per eseguire il backup di un intero insieme di credenziali delle chiavi in un'unica operazione. Qualsiasi tentativo di usare i comandi elencati in questo documento per eseguire un backup automatico di un insieme di credenziali delle chiavi non sarà supportato da Microsoft o dal team di Azure Key Vault.

Il tentativo di usare i comandi mostrati nel documento seguente per creare un'automazione personalizzata può causare errori.

* Il backup di segreti con più versioni può causare errori di timeout.
* Il backup crea uno snapshot temporizzato. I segreti possono essere rinnovati durante un backup causando una mancata corrispondenza delle chiavi di crittografia.
* Il superamento dei limiti del servizio dell'insieme di credenziali delle chiavi per le richieste al secondo causerà la limitazione dell'insieme di credenziali delle chiavi e causerà l'esito negativo del backup.

## <a name="design-considerations"></a>Considerazioni sulla progettazione

Quando si esegue il backup di un oggetto archiviato nell'insieme di credenziali delle chiavi (segreto, chiave o certificato), l'operazione di backup scarica l'oggetto come BLOB crittografato. Questo BLOB non può essere decrittografato all'esterno di Azure. Per ottenere dati utilizzabili da questo BLOB, è necessario ripristinare il BLOB in un insieme di credenziali delle chiavi all'interno della stessa sottoscrizione di Azure e della stessa area geografica di Azure.
[Aree geografiche di Azure](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>Prerequisiti

* Autorizzazioni a livello di collaboratore o superiori per una sottoscrizione di Azure
* Insieme di credenziali delle chiavi primario contenente i segreti di cui si vuole eseguire il backup
* Insieme di credenziali delle chiavi secondario in cui verranno ripristinati i segreti.

## <a name="back-up-and-restore-with-azure-portal"></a>Eseguire il backup e il ripristino con il portale di Azure

### <a name="back-up"></a>Eseguire il backup

1. Passare al portale di Azure.
2. Selezionare l'insieme di credenziali delle chiavi.
3. Passare all'oggetto (segreto, chiave o certificato) di cui si vuole eseguire il backup.

    ![Immagine](../media/backup-1.png)

4. Selezionare l'oggetto.
5. Selezionare 'Scarica il backup'.

    ![Immagine](../media/backup-2.png)
    
6. Fare clic sul pulsante 'Download'.

    ![Immagine](../media/backup-3.png)
    
7. Archiviare il BLOB crittografato in una posizione sicura.

### <a name="restore"></a>Restore

1. Passare al portale di Azure.
2. Selezionare l'insieme di credenziali delle chiavi.
3. Passare al tipo di oggetto (segreto, chiave o certificato) di cui si vuole eseguire il ripristino.
4. Selezionare 'Ripristina il backup'

    ![Immagine](../media/backup-4.png)
    
5. Passare al percorso in cui è stato archiviato il BLOB crittografato.
6. Selezionare "Ok".

## <a name="back-up-and-restore-with-the-azure-cli"></a>Eseguire il backup e il ripristino con l'interfaccia della riga di comando di Azure

```azurecli
## Login To Azure
az login

## Set your Subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a Provider
az provider register -n Microsoft.KeyVault

## Backup a Certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>Passaggi successivi

Attivare la registrazione e il monitoraggio per Azure Key Vault. [Registrazione dell'insieme di credenziali delle chiavi di Azure](https://docs.microsoft.com/azure/key-vault/general/logging)
