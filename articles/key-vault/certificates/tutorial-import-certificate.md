---
title: 'Esercitazione: Importare un certificato in Key Vault usando il portale di Azure | Microsoft Docs'
description: Esercitazione che illustra come importare un certificato in Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/03/2020
ms.author: sebansal
ms.openlocfilehash: 754f30f7931f9fad6a95328cbf8ab34f70cb75a0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81426110"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Esercitazione: Importare un certificato in Azure Key Vault

Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. È possibile creare e gestire istanze di Azure Key Vault tramite il portale di Azure. In questa esercitazione si creerà un insieme di credenziali delle chiavi, che verrà quindi usato per importare un certificato. Per altre informazioni su Key Vault, vedere la relativa [panoramica](../general/overview.md).

L'esercitazione illustra come:

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi.
> * Importare un certificato nell'insieme di credenziali delle chiavi usando il portale.
> * Importare un certificato nell'insieme di credenziali delle chiavi usando l'interfaccia della riga di comando.


Prima di iniziare, leggere i [concetti di base di Key Vault](../general/basic-concepts.md). 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-vault"></a>Creare un insieme di credenziali

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.
2. Nella casella di ricerca immettere **Key Vault**.
3. Nell'elenco dei risultati scegliere **Key Vault**.
4. Nella sezione Key Vault scegliere **Crea**.
5. Nella pagina **Crea insieme di credenziali delle chiavi** specificare le informazioni seguenti:
    - **Name**: è necessario un nome univoco. Per questa guida di avvio rapido si userà **Example-Vault**. 
    - **Sottoscrizione** Scegliere una sottoscrizione.
    - In **Gruppo di risorse** scegliere **Crea nuovo** e immettere il nome del gruppo di risorse.
    - Scegliere un percorso nel menu a discesa **Percorso**.
    - Lasciare invariati i valori predefiniti delle altre opzioni.
6. Dopo avere specificato le informazioni, selezionare **Crea**.

Prendere nota delle due proprietà elencate di seguito:

* **Vault Name**: in questo esempio corrisponde a **Example-Vault**. Questo nome verrà usato per altri passaggi.
* **Vault URI** (URI dell'insieme di credenziali): in questo esempio corrisponde a https://example-vault.vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

A questo punto, l'account Azure è l'unico autorizzato a eseguire operazioni su questo nuovo insieme di credenziali.

![Output dopo la creazione dell'istanza di Key Vault](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Importare un certificato in Key Vault

Per importare un certificato nell'insieme di credenziali, è necessario che un file di certificato PEM o PFX sia disponibile nel disco. In questo caso, verrà importato un certificato denominato **ExampleCertificate**.

> [!IMPORTANT]
> In Azure Key Vault i formati di certificato supportati sono PFX e PEM. 
> - Il formato di file PEM contiene uno o più file di certificato X509.
> - Il formato di file PFX è un formato di file di archivio per l'archiviazione di diversi oggetti di crittografia in un unico file, ad esempio il certificato del server (rilasciato per il dominio), una chiave privata corrispondente e può includere facoltativamente una CA intermedia.  

1. Nella pagina delle proprietà di Key Vault selezionare **Certificati**.
2. Fare clic su **Genera/Importa**.
3. Nella schermata **Crea un certificato** scegliere i valori seguenti:
    - **Metodo di creazione del certificato**: Importazione.
    - **Nome certificato**: ExampleCertificate.
    - **Carica il file di certificato**: selezionare il file di certificato dal disco
    - Lasciare invariati gli altri valori predefiniti. Fare clic su **Crea**.

![Proprietà del certificato](../media/certificates/tutorial-import-cert/cert-import.png)

Dopo avere ricevuto il messaggio che indica che il certificato è stato importato, è possibile fare clic sul di esso nell'elenco per visualizzare alcune delle proprietà. 

![Proprietà del certificato](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Importare un certificato usando l'interfaccia della riga di comando di Azure

Importa un certificato in un insieme di credenziali delle chiavi specificato. Per importare un certificato esistente valido, contenente una chiave privata, in Azure Key Vault, il certificato da importare può essere in formato PFX o PEM. Se il certificato è in formato PEM, il file PEM deve contenere la chiave e i certificati X509. Questa operazione richiede l'autorizzazione di importazione certificati.

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```
Per altre informazioni sui parametri, vedere [qui](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import)

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide introduttive ed esercitazioni relative a Key Vault si basano su questa guida introduttiva. Se si prevede di usare le guide introduttive e le esercitazioni successive, è consigliabile non cancellare le risorse create.
Quando non è più necessario, eliminare il gruppo di risorse per eliminare l'istanza di Key Vault e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questo avvio rapido, selezionarlo.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE:** digitare il nome del gruppo di risorse e selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'istanza di Key Vault ed è stato importato un certificato al suo interno. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Altre informazioni sulla [Gestione dei certificati in Azure Key Vault](/archive/blogs/kv/manage-certificates-via-azure-key-vault)
- Vedere esempi di [Importazione di certificati con le API REST](/rest/api/keyvault/importcertificate/importcertificate)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)