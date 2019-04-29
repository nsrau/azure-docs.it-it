---
title: Certificare l'immagine di macchina virtuale per Azure Marketplace | Microsoft Docs
description: L'articolo illustra come testare e inviare un'immagine di macchina virtuale per la certificazione di Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/26/2018
ms.author: pbutlerm
ms.openlocfilehash: 24430b1b785a24da06a8ea51594147040e6d5bd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638350"
---
# <a name="certify-your-vm-image"></a>Certificare l'immagine di macchina virtuale

Dopo aver creato e distribuire la macchina virtuale, è necessario testarla e inviarla per la certificazione di Azure Marketplace. Questo articolo illustra come ottenere lo *strumento di test di certificazione per Azure Certified*, come usarlo per certificare l'immagine di macchina virtuale e come caricare i risultati della verifica nel contenitore di Azure in cui è presente il disco rigido virtuale in uso. 


## <a name="download-and-run-the-certification-test-tool"></a>Scaricare ed eseguire lo strumento di test di certificazione

Lo strumento di test di certificazione per Azure Certified viene eseguito in un computer Windows locale, ma esegue il test di una macchina virtuale Linux o Windows basata su Azure.  Lo strumento verifica che l'immagine di macchina virtuale dell'utente sia compatibile con Microsoft Azure, ovvero che siano stati soddisfatti i requisiti e le linee guida per la preparazione del disco rigido virtuale. L'output dello strumento è un report di compatibilità, che verrà caricato nel [portale Cloud Partner](https://cloudpartner.azure.com) per richiedere la certificazione della macchina virtuale.

1. Scaricare e installare lo [strumento di test di certificazione per Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Aprire lo strumento di certificazione e quindi fare clic su **Avvia nuovo test**.
3. Nella schermata **Test Information** (Informazioni test) immettere un valore in **Test Name** (Nome test) per l'esecuzione del test.
4. Selezionare un valore per il campo **Platform** (Platform) per macchina virtuale, `Windows Server` o `Linux`. La piattaforma scelta determina le opzioni rimanenti.
5. Se la macchina virtuale usa questo servizio di database, selezionare la casella di controllo **Test for Azure SQL Database**(Test per il database SQL di Azure).

   ![Pagina iniziale dello strumento di test di certificazione](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Connettere lo strumento di certificazione a un'immagine di macchina virtuale

  Lo strumento si connette alle macchine virtuali basate su Windows con [PowerShell](https://docs.microsoft.com/powershell/) e alle macchine virtuali Linux tramite [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Connettere lo strumento di certificazione a un'immagine di macchina virtuale Linux

1. Selezionare la modalità **SSH Authentication** (Autenticazione SSH), ovvero `Password Authentication` o `key File Authentication`.
2. Se si usa l'autenticazione basata su password, immettere i valori nei campi **VM DNS Name** (Nome DNS macchina virtuale), **User name** (Nome utente) e **Password**.  Facoltativamente, è possibile modificare il valore predefinito di **SSH port** (Porta SSH).

     ![Autenticazione della password dell'immagine VM Linux](./media/publishvm_026.png)

3. Se si usa l'autenticazione basata su file di chiavi, immettere i valori nei campi **VM DNS Name** (Nome DNS macchina virtuale), **User name** (Nome utente) e **Private key** (Chiave privata).  Facoltativamente, è possibile specificare un valore per **Passphrase** o modificare l'impostazione predefinita per il campo **SSH port** (Porta SSH).

     ![Autenticazione file dell'immagine di macchina virtuale Linux](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Connettere lo strumento di certificazione a un'immagine di macchina virtuale basata su Windows**
1. Immettere il nome completo **VM DNS name** (Nome DNS macchina virtuale), ad esempio `MyVMName.Cloudapp.net`.
2. Immettere i valori per **User Name** (Nome utente) e **Password**.

   ![Autenticazione della password dell'immagine VM Windows](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Eseguire un test di certificazione

Dopo aver specificato i valori dei parametri per l'immagine di macchina virtuale nello strumento di certificazione, selezionare **Test Connection** (Connessione test) per garantire una connessione valida per la macchina virtuale. Dopo che la connessone è verificata, selezionare **Next** (Avanti) per avviare il test.  Al termine del test, viene visualizzata una tabella con i risultati del test, ovvero test superato, non riuscito o con avviso.  L'esempio seguente mostra i risultati del test per un test per una macchina virtuale Linux. 

![Risultati dei test di certificazione per l'immagine di una macchina virtuale Linux](./media/publishvm_029.png)

Se uno dei test non riesce, l'immagine *non viene* certificata. In questo caso esaminare i requisiti e i messaggi di errore, apportare le modifiche indicate ed eseguire nuovamente il test. 

Dopo il test automatizzato, è necessario specificare informazioni aggiuntive sull'immagine di macchina virtuale nella schermata **Questionnaire** (Questionario).  Sono presenti due schede che è necessario completare.  La scheda **General Assessment** (Valutazione generale) contiene domande con risposta **True/False** (Vero/Falso), mentre la scheda **Kernel Customization** (Personalizzazione kernel) contiene domande a più risposte e con risposte libere.  Rispondere alle domande in entrambe le schede e quindi selezionare **Next** (Avanti).

![Questionario dello strumento di certificazione](./media/publishvm_030.png)

L'ultima schermata consente di indicare informazioni aggiuntive, ad esempio informazioni di accesso SSH per un'immagine di macchina virtuale Linux e una spiegazione per eventuali valutazioni non superate se si cercano le eccezioni. 

Fare clic infine su **Generate Report** (Genera report) per scaricare i risultati del test e i file di log per i casi di test eseguiti, oltre alle risposte al questionario. Salvare i risultati nello stesso contenitore dei dischi rigidi virtuali.

![Salvare i risultati del test di certificazione](./media/publishvm_031.png)


## <a name="next-steps"></a>Passaggi successivi

È necessario successivamente [generare un URI (Uniform Resource Identifier) per ogni disco rigido virtuale](./cpp-get-sas-uri.md) inviato al marketplace. 
