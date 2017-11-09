---
title: Esplora archivi di connettersi a una sottoscrizione di Azure Stack
description: Informazioni su come connettersi Exporer archiviazione a una sottoscrizione di Azure Stack
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: c7e6d70148d39fd74f6409a0a239833f8e9f7614
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2017
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription"></a>Esplora archivi di connettersi a una sottoscrizione di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Esplora archivi Azure (anteprima) è un'applicazione autonoma che consente di lavorare con i dati dello Stack di archiviazione di Azure in Windows, macOS e Linux. Sono disponibili diversi strumenti disponibili per spostare i dati da e verso Azure Stack di archiviazione. Per ulteriori informazioni, vedere [strumenti per l'archiviazione di Azure Stack di trasferimento dei dati](azure-stack-storage-transfer.md).

In questo articolo informazioni su come connettersi all'account di archiviazione di Azure Stack tramite Esplora archivi. 

Se non è stato installato, Esplora archivi [scaricare](http://www.storageexplorer.com/) ed e installarlo.

Dopo la connessione alla sottoscrizione di Azure Stack, è possibile utilizzare il [articoli Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) per lavorare con i dati dello Stack di Azure. 

## <a name="prepare-an-azure-stack-subscription"></a>Preparare una sottoscrizione di Azure Stack

È necessario accedere al desktop del computer host Azure Stack o una connessione VPN per accedere alla sottoscrizione di Azure Stack Esplora risorse di archiviazione. Per informazioni su come configurare una connessione VPN ad Azure Stack, vedere [Connettersi ad Azure Stack con VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

Per il Kit di sviluppo dello Stack di Azure, è necessario esportare il certificato radice dell'autorità dello Stack di Azure.

### <a name="to-export-and-then-import-the-azure-stack-certificate"></a>Per esportare e quindi importare il certificato di Azure Stack

1. Aprire `mmc.exe` su un computer host di Azure Stack o un computer locale con una connessione VPN allo Stack di Azure. 

2. In **File**selezionare **Aggiungi/Rimuovi Snap-in**, quindi aggiungere **certificati** per gestire **account dell'utente**.



3. In **Root\Certificated Console (Computer locale) \Trusted Root Certification Authorities\Certificates** trovare **AzureStackSelfSignedRootCert**.

    ![Caricare il certificato radice di Azure Stack tramite mmc.exe][25]

4. Il pulsante destro del certificato, selezionare **tutte le attività** > **esportare**e quindi seguire le istruzioni per esportare il certificato con **codificato Base 64 x. 509 (. CER)**.  

    Il certificato esportato verrà usato nel passaggio successivo.
5. Avviare Esplora archivi (anteprima), e se viene visualizzato il **Connetti ad archiviazione di Azure** finestra di dialogo casella, annullare l'operazione.

6. Nel **modifica** dal menu **i certificati SSL**, quindi fare clic su **importazione certificati**. Usare la finestra di dialogo selezione file per trovare e aprire il certificato esportato nel passaggio precedente.

    Dopo l'importazione verrà chiesto di riavviare Storage Explorer.

    ![Importare il certificato in Storage Explorer (anteprima)][27]

A questo punto si è pronti per la connessione Esplora archivi a una sottoscrizione di Azure Stack.

### <a name="to-connect-an-azure-stack-subscription"></a>Per connettersi a una sottoscrizione di Azure Stack


1. Dopo il riavvio di Storage Explorer (anteprima), selezionare il menu **Modifica** e verificare che **Target Azure Stack** (Azure Stack di destinazione) sia selezionato. In caso contrario, selezionarlo e riavviare Storage Explorer per applicare la modifica. Questa configurazione è obbligatoria per la compatibilità con l'ambiente di Azure Stack.

    ![Verificare che Target Azure Stack (Azure Stack di destinazione) sia selezionato][28]

7. Nel riquadro sinistro selezionare **Manage Accounts** (Gestisci account).  
    Verranno visualizzati tutti gli account Microsoft a cui si è connessi.

8. Per connettersi all'account Azure Stack, selezionare **Aggiungi un account**.

    ![Aggiungere un account Azure Stack][29]

9. Nel **Connetti ad archiviazione di Azure** nella finestra di dialogo **ambiente Azure**selezionare **ambiente dello Stack di utilizzare Azure**e quindi fare clic su **Avanti**.

10. Per accedere con l'account di Azure Stack associata almeno una sottoscrizione di Azure Stack active, inserire il **accedere all'ambiente di Azure Stack** la finestra di dialogo.  

    I dati per ogni campo sono i seguenti:

    * **Environment name** (Nome ambiente): il campo può essere personalizzato dall'utente.
    * **ARM resource endpoint** (Endpoint risorse di Azure Resource Manager): esempi di endpoint di risorse di Azure Resource Manager:

        * Per l'operatore cloud:<br> https://adminmanagement.Local.azurestack.External   
        * Per tenant:<br> https://Management.Local.azurestack.External
 
    * **Id tenant**: facoltativo. Il valore viene indicato solo quando deve essere specificata la directory.

12. Dopo avere effettuato l'acceso con un account Azure Stack, il riquadro sinistro verrà popolato con le sottoscrizioni di Azure Stack associate a quell'account. Selezionare le sottoscrizioni di Azure Stack da usare e quindi selezionare **Applica**. Selezionando o deselezionando la casella di controllo **Tutte le sottoscrizioni**, sarà possibile selezionare o deselezionare tutte le sottoscrizioni di Azure Stack elencate.

    ![Selezionare le sottoscrizioni di Azure Stack dopo avere compilato la finestra di dialogo Custom Cloud Environment (Ambiente cloud personalizzato)][30]  
    Il riquadro sinistro mostra gli account di archiviazione associati alle sottoscrizioni di Azure Stack selezionate.

    ![Elenco di account di archiviazione, inclusi gli account delle sottoscrizioni di Azure Stack][31]

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione a soluzioni di archiviazione (anteprima)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Stack di archiviazione Azure: considerazioni e le differenze](azure-stack-acs-differences.md)


* Per ulteriori informazioni sull'archiviazione di Azure, vedere [Introduzione all'archiviazione di Microsoft Azure](../../storage/common/storage-introduction.md)

[25]: ./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png
[26]: ./media/azure-stack-storage-connect-se/export-root-cert-azure-stack.png
[27]: ./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png
[28]: ./media/azure-stack-storage-connect-se/select-target-azure-stack.png
[29]: ./media/azure-stack-storage-connect-se/add-azure-stack-account.png
[30]: ./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png
[31]: ./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png
