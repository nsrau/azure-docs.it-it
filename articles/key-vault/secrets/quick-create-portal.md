---
title: Guida introduttiva di Azure - Impostare e recuperare un segreto da Key Vault tramite il portale di Azure | Microsoft Docs
description: Guida introduttiva che illustra come impostare e recuperare un segreto da Azure Key Vault usando il portale di Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 212e5fb62043c2ffe2b8876249a6aad1d224411d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685852"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault usando il portale di Azure

Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. È possibile creare e gestire istanze di Azure Key Vault tramite il portale di Azure. In questo avvio rapido viene creato un insieme di credenziali delle chiavi che viene poi usato per archiviare un segreto. 

Per altre informazioni, vedere 
- [Panoramica di Key Vault](../general/overview.md)
- [Panoramica dei segreti](about-secrets.md).

## <a name="prerequisites"></a>Prerequisiti

Per accedere ad Azure Key Vault, è necessaria una sottoscrizione di Azure. Se non si ha già una sottoscrizione, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

L'accesso ai segreti avviene interamente tramite Azure Key Vault. Per questa guida di avvio rapido, creare un insieme di credenziali delle chiavi usando il [portale di Azure](../general/quick-create-portal.md), l'[interfaccia della riga di comando di Azure](../general/quick-create-cli.md) o [Azure PowerShell](../general/quick-create-powershell.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="add-a-secret-to-key-vault"></a>Aggiungere un segreto all'istanza di Key Vault

Per aggiungere un segreto all'insieme di credenziali, seguire questa procedura:

1. Nel portale di Azure passare al nuovo insieme di credenziali delle chiavi
1. Nella pagina di impostazioni di Key Vault selezionare **Segreti**.
1. Fare clic su **Genera/Importa**.
1. Nella schermata **Crea un segreto** selezionare i seguenti valori:
    - **Opzioni di caricamento**: manuale.
    - **Nome**: digitare un nome per il segreto. Il nome del segreto deve essere univoco all'interno di un'istanza di Key Vault. Il nome deve essere costituito da una stringa di lunghezza compresa tra 1 e 127 caratteri, che inizia con una lettera e contiene solo i numeri 0-9, i caratteri a-z e A-Z e il trattino -. Per altre informazioni sulla denominazione, vedere [Oggetti, identificatori e controllo delle versioni di Key Vault](https://docs.microsoft.com/azure/key-vault/general/about-keys-secrets-certificates#objects-identifiers-and-versioning)
    - **Value**: digitare un valore per il segreto. Le API di Key Vault accettano e restituiscono i valori dei segreti sotto forma di stringhe. 
    - Lasciare invariati gli altri valori predefiniti. Fare clic su **Crea**.

Dopo avere ricevuto il messaggio che indica che il segreto è stato creato, è possibile fare clic sul segreto nell'elenco. 

Per altre informazioni sugli attributi dei segreti, vedere [Informazioni sui segreti di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/about-secrets)

## <a name="retrieve-a-secret-from-key-vault"></a>Recuperare un segreto dall'insieme di credenziali delle chiavi

Se si fa clic sulla versione corrente è possibile visualizzare il valore specificato nel passaggio precedente.

![Proprietà del segreto](../media/quick-create-portal/current-version-hidden.png)

Facendo clic sul pulsante "Mostra il valore segreto" nel riquadro di destra, è possibile visualizzare il valore nascosto. 

![Valore segreto visualizzato](../media/quick-create-portal/current-version-shown.png)

Per recuperare il segreto creato in precedenza, è possibile usare l'[interfaccia della riga di comando di Azure]() oppure [Azure PowerShell]().

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide di avvio rapido ed esercitazioni relative a Key Vault si basano su questa guida di avvio rapido. Se si prevede di usare le guide di avvio rapido e le esercitazioni successive, è consigliabile non cancellare le risorse create.
Quando non è più necessario, eliminare il gruppo di risorse per eliminare l'istanza di Key Vault e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questo avvio rapido, selezionarlo.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE:** digitare il nome del gruppo di risorse e selezionare **Elimina**.

> [!NOTE]
> È importante tenere presente che, dopo l'eliminazione, un segreto, una chiave, un certificato o un insieme di credenziali delle chiavi rimarrà recuperabile per un periodo configurabile di 7-90 giorni di calendario. Se non viene specificata una configurazione, il periodo di recupero predefinito verrà impostato su 90 giorni. In questo modo gli utenti hanno il tempo sufficiente per notare l'eliminazione accidentale di un segreto e intervenire. Per altre informazioni sull'eliminazione e il recupero di insiemi di credenziali delle chiavi e dei relativi oggetti, vedere [Panoramica dell'eliminazione temporanea di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/soft-delete-overview)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione dell'avvio rapido è stata creata un'istanza di Key Vault in cui è stato archiviato un segreto. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere [Proteggere l'accesso a un'istanza di Key Vault](../general/secure-your-key-vault.md)
- Vedere [Usare Key Vault con l'app Web del servizio app](../general/tutorial-net-create-vault-azure-web-app.md)
- Vedere [Usare Key Vault con l'applicazione distribuita nella macchina virtuale](../general/tutorial-net-virtual-machine.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)
