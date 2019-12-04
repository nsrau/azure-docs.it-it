---
title: Creare e crittografare una macchina virtuale Linux con il portale di Azure
description: Questo argomento di avvio rapido illustra come usare il portale di Azure per creare e crittografare una macchina virtuale Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: e777387437b572eb11ebb7999d87a172b54738bb
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151247"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Guida introduttiva: Creare e crittografare una macchina virtuale con il portale di Azure

È possibile creare macchine virtuali di Azure tramite il portale di Azure. Il portale di Azure è un'interfaccia utente basata su browser per creare le macchine virtuali e le risorse associate. Questo argomento di avvio rapido illustra come usare il portale di Azure per distribuire una macchina virtuale (VM) Linux che esegue Ubuntu 18.04 LTS, creare un insieme di credenziali delle chiavi per l'archiviazione delle chiavi di crittografia e crittografare la VM.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Scegliere **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
1. Nella pagina Nuovo, in Più comuni, selezionare **Ubuntu Server 18.04 LTS**.
1. Nella scheda **Informazioni di base**, in **Dettagli progetto**, verificare che sia selezionata la sottoscrizione corretta.
1. Per **Gruppo di risorse** selezionare il gruppo di risorse creato in precedenza durante la creazione dell'insieme di credenziali delle chiavi, ad esempio **myResourceGroup**.
1. Per **Nome macchina virtuale** immettere *MyVM*.
1. Per **Area** selezionare la stessa area usata in precedenza durante la creazione dell'insieme di credenziali delle chiavi, ad esempio **Stati Uniti orientali**.
1. Assicurarsi che l'opzione **Dimensioni** sia impostata su *D2s Standard v3*.
1. In **Account amministratore** selezionare **Password**. Immettere un nome utente e una password.
    ![Schermata di creazione del gruppo di risorse](./media/disk-encryption/portal-qs-vm-creation.png)
1. Selezionare la scheda "Gestione" e verificare di avere un account di archiviazione di diagnostica. Se non si ha tale account, selezionare "Crea nuovo", specificare il nome del nuovo account e fare clic su "OK" ![Schermata di creazione del gruppo di risorse](./media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Fare clic su "Rivedi e crea".
1. Nella pagina **Crea macchina virtuale** è possibile visualizzare i dettagli sulla macchina virtuale che si sta creando. Quando si è pronti, selezionare **Crea**.

La distribuzione della macchina virtuale richiederà alcuni minuti. Al termine della distribuzione, passare alla sezione successiva.

## <a name="encrypt-the-virtual-machine"></a>Crittografare la macchina virtuale

1. Una volta completata la distribuzione della VM, selezionare **Vai alla risorsa**.
1. Sulla barra laterale sinistra selezionare **Dischi**.
1. Nella schermata Dischi selezionare **Crittografia**. 

    ![Selezione di dischi e crittografia](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Nella schermata Crittografia, in **Disks to encrypt** (Dischi da crittografare) scegliere **Dischi dati e sistema operativo**.
1. In **Impostazioni di crittografia** scegliere **Selezionare l'insieme di credenziali delle chiavi e la chiave per la crittografia**.
1. Nella schermata **Selezionare chiave da Azure Key Vault** selezionare **Crea nuovo**.

    ![Selezione di dischi e crittografia](../media/disk-encryption/portal-qs-keyvault-create.png)

1. Nella schermata **Crea nuovo Key Vault** assicurarsi che il gruppo di risorse corrisponda a quello usato per creare la VM.
1. Assegnare un nome all'insieme di credenziali delle chiavi.  Ogni insieme di credenziali delle chiavi in Azure deve avere un nome univoco.
1. Nella scheda **Criteri di accesso** selezionare la casella **Crittografa dischi di Azure per la crittografia dei volumi**.

    ![Selezione di dischi e crittografia](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. Selezionare **Rivedi e crea**.  
1. Dopo che l'insieme di credenziali delle chiavi ha superato la convalida, selezionare **Crea**. Si tornerà nella schermata **Selezionare chiave da Azure Key Vault**.
1. Lasciare vuoto il campo **Chiave** e scegliere **Seleziona**.
1. Nella parte superiore della schermata Crittografia fare clic su **Salva**. Viene visualizzato un avviso popup che informa che la VM verrà riavviata. Fare clic su **Sì**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non serve più, è possibile eliminare il gruppo di risorse, la macchina virtuale e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la macchina virtuale, selezionare Elimina e quindi confermare il nome del gruppo di risorse da eliminare.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un insieme di credenziali delle chiavi abilitato per le chiavi di crittografia, quindi è stata creata una macchina virtuale che è stata abilitata per la crittografia.  

> [!div class="nextstepaction"]
> [Informazioni su Crittografia dischi di Azure](disk-encryption-overview.md)
