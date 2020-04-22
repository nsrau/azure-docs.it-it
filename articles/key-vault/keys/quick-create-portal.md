---
title: Avvio rapido di Azure - Impostare e recuperare una chiave da Key Vault con il portale di Azure | Microsoft Docs
description: Avvio rapido che illustra come impostare e recuperare una chiave da Azure Key Vault con il portale di Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: 41f3d60d91b7418d6e9733b8351d4830b31dbace
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420285"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-the-azure-portal"></a>Avvio rapido: Impostare e recuperare una chiave da Azure Key Vault con il portale di Azure

Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. È possibile creare e gestire istanze di Azure Key Vault tramite il portale di Azure. In questo avvio rapido viene creato un insieme di credenziali delle chiavi che viene poi usato per archiviare una chiave. Per altre informazioni su Key Vault, vedere la relativa [panoramica](../general/overview.md).

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
    - **Sottoscrizione**: scegliere una sottoscrizione.
    - In **Gruppo di risorse** scegliere **Crea nuovo** e immettere il nome del gruppo di risorse.
    - Scegliere un percorso nel menu a discesa **Percorso**.
    - Lasciare invariati i valori predefiniti delle altre opzioni.
6. Dopo avere specificato le informazioni, selezionare **Crea**.

Prendere nota delle due proprietà elencate di seguito:

* **Vault Name**: in questo esempio corrisponde a **Example-Vault**. Questo nome verrà usato per altri passaggi.
* **Vault URI** (URI dell'insieme di credenziali): in questo esempio corrisponde a https://example-vault.vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

A questo punto, l'account Azure è l'unico autorizzato a eseguire operazioni su questo nuovo insieme di credenziali.

![Output dopo la creazione dell'istanza di Key Vault](../media/keys/quick-create-portal/vault-properties.png)

## <a name="add-a-key-to-key-vault"></a>Aggiungere una chiave a Key Vault

Per aggiungere una chiave a Key Vault, sono sufficienti un paio di passaggi aggiuntivi. In questo caso si aggiunge una chiave che può essere usata da un'applicazione. Il nome della chiave è **ExampleKey**.

1. Nella pagina delle proprietà di Key Vault selezionare **Chiavi**.
2. Fare clic su **Genera/Importa**.
3. Nella schermata **Creare una chiave** selezionare i seguenti valori:
    - **Opzioni**: Genera.
    - **Nome**: ExampleKey.
    - Lasciare invariati gli altri valori predefiniti. Fare clic su **Crea**.

Dopo avere ricevuto il messaggio che indica che la chiave è stata creata, è possibile fare clic sulla chiave nell'elenco. Sarà quindi possibile vedere alcune delle proprietà. Se si fa clic sulla versione corrente è possibile visualizzare il valore specificato nel passaggio precedente.

![Proprietà della chiave](../media/keys/quick-create-portal/current-version-hidden.png)


## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide di avvio rapido ed esercitazioni relative a Key Vault si basano su questa guida di avvio rapido. Se si prevede di usare le guide di avvio rapido e le esercitazioni successive, è consigliabile non cancellare le risorse create.
Quando non è più necessario, eliminare il gruppo di risorse per eliminare l'istanza di Key Vault e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questo avvio rapido, selezionarlo.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE:** digitare il nome del gruppo di risorse e selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata creata un'istanza di Key Vault in cui è stata archiviata una chiave. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)