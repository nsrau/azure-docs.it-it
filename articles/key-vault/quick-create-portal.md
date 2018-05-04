---
title: Guida introduttiva di Azure - Creare un'istanza di Key Vault nel portale | Microsoft Docs
description: Guida introduttiva che illustra come creare un'istanza di Azure Key Vault usando il portale
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: ''
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/16/2018
ms.author: barclayn
ms.openlocfilehash: 391985f3ad0e07c4559545e9d0c5fffc45fe27d2
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="quickstart-create-a-key-vault-using-the-azure-portal"></a>Guida introduttiva: Creare un'istanza di Key Vault usando il portale di Azure

Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. Per altre informazioni su Key Vault, vedere la [panoramica](key-vault-overview.md). È possibile creare e gestire istanze di Azure Key Vault tramite il portale di Azure. In questa guida introduttiva viene creata un'istanza di Key Vault. Successivamente verrà archiviato un segreto.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-into-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-a-vault"></a>Creare un insieme di credenziali

1. Selezionare l'opzione **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure

    ![Output dopo la creazione dell'istanza di Key Vault](./media/quick-create-portal/search-services.png)
2. Nella casella di ricerca immettere **Key Vault**.
3. Nell'elenco dei risultati scegliere **Key Vault**.
4. Nella sezione Key Vault scegliere **Crea**.
5. Nella pagina **Crea insieme di credenziali delle chiavi** specificare le informazioni seguenti:
    - **Nome**: per questa guida introduttiva si userà **Contoso-vault2**. È necessario specificare un nome univoco nel test.
    - **Sottoscrizione**: scegliere la sottoscrizione che verrà usata per questa guida introduttiva.
    - In **Gruppo di risorse** scegliere **Crea nuovo** e immettere il nome del gruppo di risorse.
    - Scegliere un percorso nel menu a discesa **Percorso**.
    - Selezionare la casella di controllo **Aggiungi al dashboard**.
    - Lasciare invariati i valori predefiniti delle altre opzioni.
6. Dopo avere specificato le informazioni, selezionare **Crea**.

Prendere nota delle due proprietà elencate di seguito:

* **Nome dell'insieme di credenziali**: nell'esempio corrisponde a **Contoso-Vault2**. Questo nome verrà usato per altri passaggi.
* **Vault URI** (URI dell'insieme di credenziali): in questo esempio corrisponde a https://contoso-vault2.vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

![Output dopo la creazione dell'istanza di Key Vault](./media/quick-create-portal/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Aggiungere un segreto all'istanza di Key Vault

Per aggiungere un segreto all'insieme di credenziali, sono sufficienti un paio di passaggi aggiuntivi. In questo caso si aggiunge una password che può essere usata da un'applicazione. La password è denominata **ExamplePassword** e al suo interno viene archiviato il valore di **Pa$$w0rd**.

1. Nella pagina delle proprietà dell'istanza di Key Vault selezionare **Segreti**.
2. Fare clic su **Genera/Importa**.
3. Nella schermata **Crea un segreto** eseguire queste operazioni:
    - **Opzioni di caricamento**: manuali.
    - **Nome**: ExamplePassword.
    - **Valore**: Pa$$w0rd.
    - Lasciare invariati gli altri valori predefiniti. Fare clic su **Crea**.

Dopo avere ricevuto il messaggio che indica che il segreto è stato creato, è possibile fare clic sul segreto nell'elenco. Sarà quindi possibile vedere alcune delle proprietà. Se si fa clic sulla versione corrente è possibile visualizzare il valore specificato nel passaggio precedente.

![Proprietà del segreto](./media/quick-create-portal/version.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide introduttive ed esercitazioni relative a Key Vault si basano su questa guida introduttiva. Se si prevede di usare le guide introduttive e le esercitazioni successive, è consigliabile non cancellare le risorse create.
Quando non è più necessario, eliminare il gruppo di risorse per eliminare l'istanza di Key Vault e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questa guida introduttiva, selezionarlo.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE:** digitare il nome del gruppo di risorse e selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata un'istanza di Key Vault ed è stato archiviato un segreto. Per altre informazioni su Key Vault e su come usarlo con le applicazioni, passare all'esercitazione per le applicazioni Web che usano Key Vault.

> [!div class="nextstepaction"]
> [Usare Azure Key Vault da un'applicazione Web](key-vault-use-from-web-application.md) Per informazioni su come leggere un segreto da Key Vault usando un'applicazione Web con l'[identità del servizio gestito](/active-directory/managed-service-identity/overview.md), proseguire con l'esercitazione [Configurare un'applicazione Web di Azure per la lettura di un segreto da Key Vault](tutorial-web-application-keyvault.md)
