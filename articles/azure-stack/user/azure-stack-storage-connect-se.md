---
title: Connettere Esplora archivi per una sottoscrizione di Azure Stack o un account di archiviazione | Documenti Microsoft
description: Informazioni su come Esplora archivi di connettersi a una sottoscrizione di Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/20/2018
ms.author: mattbriggs
ms.reviewer: xiaofmao
ms.openlocfilehash: 8b670ec7040aab7eca26d411c9e31a934052be19
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Connettere Esplora archivi per una sottoscrizione di Azure Stack o un account di archiviazione

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Azure Storage Explorer è un'app autonoma che consente di lavorare con i dati dello Stack di archiviazione di Azure in Windows, macOS e Linux. Sono disponibili diversi strumenti disponibili per lo spostamento dei dati da e verso Azure Stack di archiviazione. Per ulteriori informazioni, vedere [strumenti per l'archiviazione di Azure Stack di trasferimento dei dati](azure-stack-storage-transfer.md).

In questo articolo, è illustrato come connettersi a Azure Stack sottoscrizioni e account di archiviazione tramite Esplora archivi. 

Se non è stato installato, Esplora archivi [scaricare](http://www.storageexplorer.com/) e installarlo.

Dopo la connessione a una sottoscrizione di Azure Stack o un account di archiviazione, è possibile usare il [articoli Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) per funzionare con i dati dello Stack di Azure. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Preparare per la connessione a Azure Stack

È necessario l'accesso diretto per lo Stack di Azure o una connessione VPN per Esplora risorse di archiviazione per accedere alla sottoscrizione di Azure Stack. Per informazioni su come configurare una connessione VPN ad Azure Stack, vedere [Connettersi ad Azure Stack con VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

Per il Kit di sviluppo dello Stack di Azure, è necessario esportare il certificato radice dell'autorità dello Stack di Azure.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Esportare e quindi importare il certificato di Azure Stack

1. Aprire `mmc.exe` su un computer host di Azure Stack o un computer locale con una connessione VPN allo Stack di Azure. 

2. In **File**selezionare **Aggiungi/Rimuovi Snap-in**, quindi aggiungere **certificati** per gestire **account dell'utente**.

3. In **Root\Certificated Console (Computer locale) \Trusted Root Certification Authorities\Certificates** trovare **AzureStackSelfSignedRootCert**.

    ![Caricare il certificato radice di Azure Stack tramite mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. Il pulsante destro del certificato, selezionare **tutte le attività** > **esportare**e quindi seguire le istruzioni per esportare il certificato con **codificato Base 64 x. 509 (. CER)**.  

    Il certificato esportato verrà usato nel passaggio successivo.

5. Avviare Esplora archivi, e se viene visualizzato il **Connetti ad archiviazione di Azure** dialogo casella, annullare l'operazione.

6. Nel **modificare** dal menu **i certificati SSL**, quindi selezionare **importazione certificati**. Usare la finestra di dialogo selezione file per trovare e aprire il certificato esportato nel passaggio precedente.

    Dopo l'importazione verrà chiesto di riavviare Storage Explorer.

    ![Importare il certificato in Esplora archivi](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. Dopo il riavvio di Esplora archivi, selezionare il **modificare** menu, quindi assicurarsi che **dello Stack di Azure di destinazione** sia selezionata. In caso contrario, selezionarlo e riavviare Storage Explorer per applicare la modifica. Questa configurazione è obbligatoria per la compatibilità con l'ambiente di Azure Stack.

    ![Verificare che Target Azure Stack (Azure Stack di destinazione) sia selezionato](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription"></a>Connettersi a una sottoscrizione di Azure Stack

A questo punto si è pronti per la connessione Esplora archivi a una sottoscrizione di Azure Stack.

1. Nel riquadro sinistro di Esplora archivi, selezionare **Gestisci account**.  
    Vengono visualizzati tutti la sottoscrizione di Microsoft che l'utente connesso.

2. Per connettersi alla sottoscrizione Azure Stack, selezionare **aggiungere un account**.

    ![Aggiungere un account Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. In Connetti alla finestra di dialogo di archiviazione di Azure, sotto **ambiente Azure**, selezionare **Azure** o **Azure China**, quale dipende l'account di Azure Stack che viene usato, e quindi selezionare **Accedi**. Per accedere con l'account di Azure Stack associata almeno una sottoscrizione di Azure Stack attiva.

    ![Connetti ad archiviazione di Azure](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Dopo avere effettuato l'acceso con un account Azure Stack, il riquadro sinistro verrà popolato con le sottoscrizioni di Azure Stack associate a quell'account. Selezionare le sottoscrizioni di Azure Stack che si desidera utilizzare e quindi selezionare Applica. (Selezionando o deselezionando il tutte le sottoscrizioni casella di controllo Attiva o disattiva la selezione di tutte o nessuna delle sottoscrizioni Azure Stack elencate).

    ![Selezionare le sottoscrizioni di Azure Stack dopo avere compilato la finestra di dialogo Custom Cloud Environment (Ambiente cloud personalizzato)](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)  

    Il riquadro sinistro mostra gli account di archiviazione associati alle sottoscrizioni di Azure Stack selezionate.

    ![Elenco di account di archiviazione, inclusi gli account delle sottoscrizioni di Azure Stack](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Connettersi a un account di archiviazione Azure Stack

È inoltre possibile connettersi a un account di archiviazione di Azure Stack mediante nome account di archiviazione e coppia di chiavi.

1.  Nel riquadro sinistro di Esplora archivi, selezionare Gestisci account. Vengono visualizzati tutti gli account di Microsoft che l'utente connesso.

    ![Aggiungi un account](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2.  Per connettersi alla sottoscrizione Azure Stack, selezionare **aggiungere un account**.
 
    ![Aggiungi un account](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3.  In Connetti alla finestra di dialogo di archiviazione di Azure, selezionare **utilizzare un nome account di archiviazione e una chiave**.

4. Immettere il nome dell'account nel **nome dell'Account**, incollare chiave dell'account nel **chiave dell'Account** casella di testo, seleziona **altri (immettere di seguito)** in **gli endpoint di archiviazione dominio** e immettere l'endpoint dello Stack di Azure.  

    Un endpoint di Azure Stack comprende due parti: il nome di un'area e il dominio di Azure Stack. Nel Kit di sviluppo dello Stack di Azure, l'endpoint predefinito è **local.azurestack.external**. Contattare l'amministratore del cloud se non si è sicuri sull'endpoint.

    ![Collegare nome e la chiave](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5.  Selezionare **Connessione**.
6.  Dopo che l'account di archiviazione è stato collegato correttamente, l'account di archiviazione viene visualizzato con (**esterne, anche altri**) aggiunto al nome.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione a Esplora archivi](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Stack di archiviazione Azure: considerazioni e le differenze](azure-stack-acs-differences.md)
* Per ulteriori informazioni sull'archiviazione di Azure, vedere [Introduzione all'archiviazione di Microsoft Azure](../../storage/common/storage-introduction.md)
