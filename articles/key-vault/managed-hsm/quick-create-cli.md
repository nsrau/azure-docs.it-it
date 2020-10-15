---
title: Avvio rapido - Effettuare il provisioning di un modulo di protezione hardware gestito e attivarlo
description: Questo argomento di avvio rapido illustra come effettuare il provisioning di un modulo di protezione hardware gestito e attivarlo tramite l'interfaccia della riga di comando di Azure
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 86d0a336a7d3f5d12ed8e53de802616f839f9eba
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91756818"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>Avvio rapido: Effettuare il provisioning di un modulo di protezione hardware gestito e attivarlo tramite l'interfaccia della riga di comando di Azure

Il modulo di protezione hardware gestito di Azure Key Vault è un servizio cloud completamente gestito, a disponibilità elevata, a tenant singolo e conforme agli standard che consente di proteggere le chiavi crittografiche per le applicazioni cloud tramite moduli di protezione hardware convalidati in base agli standard **FIPS 140-2 livello 3**. Per altre informazioni sul modulo di protezione hardware gestito, vedere la [panoramica](overview.md). 

In questo argomento di avvio rapido viene creato e attivato un modulo di protezione hardware gestito con l'interfaccia della riga di comando di Azure. Successivamente verrà archiviato un segreto.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo sono necessari gli elementi seguenti:

* Una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interfaccia della riga di comando di Azure versione 2.12.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).
* Un modulo di protezione hardware gestito nella sottoscrizione. Vedere [Avvio rapido: Effettuare il provisioning di un modulo di protezione hardware gestito e attivarlo tramite l'interfaccia della riga di comando di Azure](quick-create-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure usando l'interfaccia della riga di comando è possibile digitare:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *ContosoResourceGroup* nella località *eastus2*.

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location eastus2
```

## <a name="create-a-managed-hsm"></a>Creare un modulo di protezione hardware gestito

La creazione di un modulo di protezione hardware gestito è un processo in due passaggi:
1. Provisioning di una risorsa modulo di protezione hardware gestito.
1. Attivazione del modulo di protezione hardware gestito scaricando il dominio di sicurezza.

### <a name="provision-a-managed-hsm"></a>Effettuare il provisioning di un modulo di protezione hardware gestito

Usare il comando `az keyvault create` per creare un modulo di protezione hardware gestito. Questo script ha tre parametri obbligatori: nome del gruppo di risorse, nome del modulo di protezione hardware e località geografica.

Per creare una risorsa modulo di protezione hardware gestito, è necessario fornire gli input seguenti:
- Gruppo di risorse in cui verrà inserito il modulo nella sottoscrizione.
- località di Azure.
- Elenco di amministratori iniziali.

L'esempio seguente crea un modulo di protezione hardware denominato **ContosoMHSM** nel gruppo di risorse **ContosoResourceGroup**, nella località **Stati Uniti orientali 2** e con l'**utente connesso corrente** come unico amministratore.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> L'esecuzione del comando di creazione può richiedere alcuni minuti. Al termine, è possibile attivare il modulo di protezione hardware.

L'output di questo comando mostra le proprietà del modulo di protezione hardware gestito creato. Le due proprietà più importanti sono:

* **name**: nell'esempio il nome è ContosoMHSM. Questo nome verrà usato per altri comandi di Key Vault.
* **hsmUri**: nell'esempio l'URI è "https://contosohsm.managedhsm.azure.net". Le applicazioni che usano il modulo di protezione hardware tramite l'API REST devono usare questo URI.

L'account Azure ora è autorizzato a eseguire qualsiasi operazione su questo modulo di protezione hardware gestito. Per ora, nessun altro è autorizzato.

### <a name="activate-your-managed-hsm"></a>Attivare il modulo di protezione hardware gestito

Tutti i comandi relativi al piano dati sono disabilitati finché il modulo di protezione hardware non viene attivato. Non sarà possibile creare chiavi o assegnare ruoli. Solo gli amministratori designati assegnati durante il comando di creazione possono attivare il modulo di protezione hardware. Per attivare il modulo di protezione hardware, è necessario scaricare il [dominio di sicurezza](security-domain.md).

Per attivare il modulo di protezione hardware occorre:
- Almeno 3 coppie di chiavi RSA (al massimo 10)
- Specificare il numero minimo di chiavi necessarie per decrittografare il dominio di sicurezza (quorum)

Per attivare il modulo di protezione hardware è necessario inviare almeno 3 (al massimo 10) chiavi pubbliche RSA al modulo stesso. Il modulo di protezione hardware crittografa il dominio di sicurezza con queste chiavi e lo restituisce. Al termine del download del dominio di sicurezza, il modulo di protezione hardware è pronto per l'uso. Occorre anche specificare il quorum, ossia il numero minimo di chiavi private necessarie per decrittografare il dominio di sicurezza.

L'esempio seguente illustra come usare `openssl` per generare 3 certificati autofirmati.

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Creare e archiviare in una posizione sicura le coppie di chiavi RSA e il file del dominio di sicurezza generati in questo passaggio.

Usare il comando `az keyvault security-domain download` per scaricare il dominio di sicurezza e attivare il modulo di protezione hardware gestito. L'esempio seguente usa 3 coppie di chiavi RSA (per questo comando sono necessarie solo le chiavi pubbliche) e imposta il quorum su 2.

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

Archiviare il file del dominio di sicurezza e le coppie di chiavi RSA in una posizione sicura. Serviranno per il ripristino di emergenza o per la creazione di un altro modulo di protezione hardware gestito che condivide lo stesso dominio di sicurezza, in modo che possano condividere le chiavi.

Dopo il download del dominio di sicurezza, il modulo di protezione hardware sarà attivo e pronto per l'uso.

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide introduttive ed esercitazioni della raccolta si basano su questa. Se si prevede di usare le guide introduttive e le esercitazioni successive, è consigliabile non cancellare le risorse create.

Quando non sono più necessari, è possibile rimuovere il gruppo di risorse e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group). È possibile eliminare le risorse in questo modo:

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata un'istanza di Key Vault in cui è stato archiviato un segreto. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica del modulo di protezione hardware gestito](overview.md)
- Informazioni sulla [gestione delle chiavi in un modulo di protezione hardware gestito](key-management.md)
- Vedere le [procedure consigliate per il modulo di protezione hardware gestito](best-practices.md)
