---
title: Guida introduttiva di Azure - Impostare e recuperare un segreto da Key Vault tramite il portale di Azure | Microsoft Docs
description: Guida introduttiva che illustra come impostare e recuperare un segreto da Azure Key Vault usando il portale di Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 2530fb0bd27cd98b702d804b0cb5a1dc60419f2f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490416"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault usando il portale di Azure

Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. È possibile creare e gestire istanze di Azure Key Vault tramite il portale di Azure. In questo avvio rapido viene creato un insieme di credenziali delle chiavi che viene poi usato per archiviare un segreto. Per altre informazioni su Key Vault, vedere la relativa [panoramica](key-vault-overview.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-vault"></a>Creare un insieme di credenziali

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.
2. Nella casella di ricerca immettere **Key Vault**.
3. Nell'elenco dei risultati scegliere **Key Vault**.
4. Nella sezione Key Vault scegliere **Crea**.
5. Nella pagina **Crea insieme di credenziali delle chiavi** specificare le informazioni seguenti:
    - **Nome**: è necessario un nome univoco. Per questo argomento di avvio rapido si usa **Contoso-vault2**. 
    - **Sottoscrizione** Scegliere una sottoscrizione.
    - In **Gruppo di risorse** scegliere **Crea nuovo** e immettere il nome del gruppo di risorse.
    - Scegliere un percorso nel menu a discesa **Percorso**.
    - Lasciare invariati i valori predefiniti delle altre opzioni.
6. Dopo avere specificato le informazioni, selezionare **Crea**.

Prendere nota delle due proprietà elencate di seguito:

* **Vault Name**: nell'esempio corrisponde a **Contoso-Vault2**. Questo nome verrà usato per altri passaggi.
* **Vault URI** (URI dell'insieme di credenziali): in questo esempio corrisponde a https://contoso-vault2.vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

A questo punto, l'account Azure è l'unico autorizzato a eseguire operazioni su questo nuovo insieme di credenziali.

![Output dopo la creazione dell'istanza di Key Vault](./media/quick-create-portal/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Aggiungere un segreto all'istanza di Key Vault

Per aggiungere un segreto all'insieme di credenziali, sono sufficienti un paio di passaggi aggiuntivi. In questo caso si aggiunge una password che può essere usata da un'applicazione. La password è denominata **ExamplePassword** e al suo interno viene archiviato il valore di **hVFkk965BuUv**.

1. Nella pagina delle proprietà di Key Vault selezionare **Segreti**.
2. Fare clic su **Genera/Importa**.
3. Nella schermata **Crea un segreto** selezionare i seguenti valori:
    - **Opzioni di caricamento**: manuale.
    - **Nome**: ExamplePassword.
    - **Valore**: hVFkk965BuUv
    - Lasciare invariati gli altri valori predefiniti. Fare clic su **Create**(Crea).

Dopo avere ricevuto il messaggio che indica che il segreto è stato creato, è possibile fare clic sul segreto nell'elenco. Sarà quindi possibile vedere alcune delle proprietà. Se si fa clic sulla versione corrente è possibile visualizzare il valore specificato nel passaggio precedente.

![Proprietà del segreto](./media/quick-create-portal/current-version-hidden.png)

Facendo clic sul pulsante "Mostra il valore segreto" nel riquadro di destra, è possibile visualizzare il valore nascosto. 

![Valore segreto visualizzato](./media/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide introduttive ed esercitazioni relative a Key Vault si basano su questa guida introduttiva. Se si prevede di usare le guide introduttive e le esercitazioni successive, è consigliabile non cancellare le risorse create.
Quando non è più necessario, eliminare il gruppo di risorse per eliminare l'istanza di Key Vault e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questo avvio rapido, selezionarlo.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE:** digitare il nome del gruppo di risorse e selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione dell'avvio rapido è stata creata un'istanza di Key Vault in cui è stato archiviato un segreto. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](key-vault-overview.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](key-vault-developers-guide.md)
- Vedere le informazioni su [chiavi, segreti e certificati](about-keys-secrets-and-certificates.md)
- Esaminare le [procedure consigliate per Azure Key Vault](key-vault-best-practices.md)