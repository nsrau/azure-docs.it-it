---
title: Connettere storage explorer per un account di archiviazione o una sottoscrizione di Azure Stack | Microsoft Docs
description: Informazioni su come connettere storage explorer a una sottoscrizione di Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 1c59f092957704c44b5cda012aa7c471fdaa3275
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763368"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Connettere storage explorer per una sottoscrizione di Azure Stack o un account di archiviazione

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

In questo articolo verrà illustrato come connettersi a sottoscrizioni di Azure Stack e gli account di archiviazione usando storage explorer. Azure storage explorer è un'app autonoma che consente di usare facilmente con i dati di archiviazione di Azure Stack in Windows, macOS e Linux.

> [!NOTE]  
> Sono disponibili diversi strumenti disponibili per spostare i dati da e verso archiviazione di Azure Stack. Per altre informazioni, vedere [trasferimento dei dati degli strumenti per l'archiviazione di Azure Stack](azure-stack-storage-transfer.md).

Se non è stato installato, Esplora archivi [download di storage explorer](https://www.storageexplorer.com/) e installarlo.

Dopo la connessione a una sottoscrizione di Azure Stack o un account di archiviazione, è possibile usare la [articoli Esplora archiviazione di Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) per lavorare con i dati di Azure Stack. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Preparare per la connessione ad Azure Stack

È necessario l'accesso diretto a una connessione VPN di storage explorer accedere alla sottoscrizione di Azure Stack o Azure Stack. Per informazioni su come configurare una connessione VPN ad Azure Stack, vedere [Connettersi ad Azure Stack con VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

Per il Azure Stack Development Kit (ASDK), è necessario esportare il certificato radice dell'autorità di Azure Stack.

> [!Note]  
> Per il ASDK, se ci si connette a di ASDK tramite VPN, non usare il certificato radice (CA.cer) che è stato creato durante il processo di configurazione VPN.  Questo è un certificato con codifica DER e non consentirà di Storage Explorer recuperare le sottoscrizioni di Azure Stack. Attenersi alla procedura seguente per esportare un certificato con codificata Base 64 da usare con Storage Explorer.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Esportare e quindi importare il certificato di Azure Stack

Esportare e quindi importare il certificato di Azure Stack per il ASDK. Per un sistema integrato, il certificato è firmato pubblicamente. Pertanto, questo passaggio non è necessario durante l'impostazione di connessione di Storage Explorer per Azure Stack integrato system.

1. Apri `mmc.exe` in un computer host Azure Stack, o un computer locale con una connessione VPN ad Azure Stack. 

2. Nel **File**, selezionare **Aggiungi/Rimuovi Snap-in**, quindi aggiungere **certificati** gestire **account dell'utente**.

3.  Sotto **Console \Trusted Root\Certificated (Computer locale) di certificazione radice attendibili\Certificati**. Trovare **AzureStackSelfSignedRootCert**.

    ![Caricare il certificato radice di Azure Stack tramite mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. Fare clic su certificato, selezionare **tutte le attività** > **Esporta**, quindi seguire le istruzioni per esportare il certificato con **con codifica Base 64 X.509 (. Area a esecuzione vincolata)**.

    Il certificato esportato verrà usato nel passaggio successivo.

5. Avvio di storage explorer, e se viene visualizzato il **Connetti ad archiviazione di Azure** dialogo casella, annullare l'operazione.

6. Nel **Edit** dal menu **i certificati SSL**e quindi selezionare **Importa certificati**. Usare la finestra di dialogo selezione file per trovare e aprire il certificato esportato nel passaggio precedente.

    Dopo aver importato il certificato, viene chiesto di riavviare storage explorer.

    ![Importare il certificato in storage explorer](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. Dopo il riavvio di Esplora archivi, selezionare la **Edit** menu di scelta e controllo per verificare se **Target Azure Stack** sia selezionata. In caso contrario, selezionare **Target Azure Stack**, quindi riavviare storage explorer per rendere effettiva la modifica. Questa configurazione è obbligatoria per la compatibilità con l'ambiente di Azure Stack.

    ![Verificare che Target Azure Stack (Azure Stack di destinazione) sia selezionato](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription-with-azure-ad"></a>Connettersi a una sottoscrizione di Azure Stack con Azure AD

Usare la procedura seguente per connettere storage explorer a una sottoscrizione di Azure Stack, che appartiene a un account Azure Active Directory (Azure AD).

1. Nel riquadro sinistro di storage explorer, selezionare **Manage Accounts**. 
    Vengono visualizzati tutti la sottoscrizione di Microsoft che hai effettuato l'accesso.

2. Per connettersi alla sottoscrizione di Azure Stack, selezionare **aggiungere un account**.

    ![Aggiungere un account Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. Nella finestra Connetti al finestra di dialogo archiviazione di Azure, sotto **ambiente di Azure**, selezionare **Azure** oppure **Azure Cina**, quale dipende l'account di Azure Stack che è in uso, selezionare **Accedi** accedere con l'account di Azure Stack associato ad almeno una sottoscrizione di Azure Stack attiva.

    ![Connessione ad Archiviazione di Azure](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Dopo avere effettuato l'acceso con un account Azure Stack, il riquadro sinistro verrà popolato con le sottoscrizioni di Azure Stack associate a quell'account. Selezionare le sottoscrizioni di Azure Stack da usare e quindi selezionare **Applica**. Selezionando o deselezionando la casella di controllo **Tutte le sottoscrizioni**, sarà possibile selezionare o deselezionare tutte le sottoscrizioni di Azure Stack elencate.

    ![Selezionare le sottoscrizioni di Azure Stack dopo avere compilato la finestra di dialogo Custom Cloud Environment (Ambiente cloud personalizzato)](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    Il riquadro sinistro mostra gli account di archiviazione associati alle sottoscrizioni di Azure Stack selezionate.

    ![Elenco di account di archiviazione, inclusi gli account delle sottoscrizioni di Azure Stack](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-subscription-with-ad-fs-account"></a>Connettersi a una sottoscrizione di Azure Stack con account AD FS

> [!Note]  
> L'esperienza di accesso servizio federato di Azure (AD FS) supporta Storage Explorer 1.2.0 o versioni più recenti con Azure Stack 1804 o aggiornamento più recente.
Usare la procedura seguente per connettere storage explorer a una sottoscrizione di Azure Stack che appartiene a un account di AD FS.

1. Selezionare **gestire gli account**. Finestra di esplorazione Elenca le sottoscrizioni di Microsoft che è eseguito l'accesso.
2. Selezionare **aggiungere un account** per connettersi alla sottoscrizione di Azure Stack.

    ![Aggiungi un account](media/azure-stack-storage-connect-se/add-an-account.png)

3. Selezionare **Avanti**. Nella finestra Connetti al finestra di dialogo archiviazione di Azure, sotto **ambiente di Azure**, selezionare **ambiente personalizzato di uso**, quindi fare clic su **Avanti**.

    ![Connettersi ad archiviazione di Azure](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Immettere le informazioni necessarie dell'ambiente personalizzato di Azure Stack. 

    | Campo | Note |
    | ---   | ---   |
    | Nome ambiente | Il campo può essere personalizzato dall'utente. |
    | Endpoint di Azure Resource Manager | Gli esempi di endpoint di risorse di Azure Resource Manager di Azure Stack Development Kit.<br>Per gli operatori: https://adminmanagement.local.azurestack.external <br> Per gli utenti: https://management.local.azurestack.external |

    Se si lavora in Azure Stack integrato system e non conosce l'endpoint di gestione, contattare l'operatore.

    ![Aggiungi un account](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Selezionare **Accedi**, per connettersi all'account di Azure Stack associato ad almeno una sottoscrizione di Azure Stack attiva.



6. Selezionare le sottoscrizioni di Azure Stack che si desidera utilizzare. Selezionare **Applica**.

    ![Gestione account](./media/azure-stack-storage-connect-se/account-management.png)

    Il riquadro sinistro mostra gli account di archiviazione associati alle sottoscrizioni di Azure Stack selezionate.

    ![Elenco di sottoscrizioni associate](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Connettersi a un account di archiviazione di Azure Stack

È anche possibile connettersi a un account di archiviazione di Azure Stack usando nome e una coppia di chiavi.

1. Nel riquadro sinistro di storage explorer, selezionare Gestisci account. Vengono visualizzati tutti gli account Microsoft che hai effettuato l'accesso.

    ![Aggiungi un account](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Per connettersi alla sottoscrizione di Azure Stack, selezionare **aggiungere un account**.

    ![Aggiungi un account](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. Nella finestra Connetti alla finestra di dialogo archiviazione di Azure, selezionare **usare un nome account di archiviazione e la chiave**.

4. Immettere il nome dell'account nel **nome dell'Account**, incollare la chiave account nella **chiave dell'Account** casella di testo, seleziona **altri (specificare sotto)** in **gli endpoint di archiviazione dominio** e immettere l'endpoint di Azure Stack.

    Un endpoint di Azure Stack include due parti: il nome di un'area e il dominio di Azure Stack. In Azure Stack Development Kit, è l'endpoint predefinito **local.azurestack.external**. Se non si è certi sull'endpoint della, contattare l'amministratore del cloud.

    ![Collegare nome e la chiave](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Selezionare **Connessione**.
6. Dopo essere stato collegato correttamente l'account di archiviazione, l'account di archiviazione viene visualizzato con (**esterne, altri**) aggiunto al nome.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva a storage explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Archiviazione di Azure Stack: differenze e considerazioni](azure-stack-acs-differences.md)
* Per altre informazioni sull'archiviazione di Azure, vedere [Introduzione ad archiviazione di Microsoft Azure](../../storage/common/storage-introduction.md)
