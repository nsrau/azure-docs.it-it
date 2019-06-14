---
title: Come eliminare ed esportare dati personali da Azure DevTest Labs | Microsoft Docs
description: Informazioni su come eliminare ed esportare dati personali dal servizio Azure DevLast Labs per adempiere agli obblighi derivanti dall'applicazione del Regolamento generale sulla protezione dei dati (GDPR).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: e681652c13e521bd33524e247db65088f47a794c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60394991"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Esportare o eliminare dati personali da Azure DevTest Labs
Questo articolo illustra la procedura da eseguire per eliminare ed esportare dati personali dal servizio Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Quali dati personali vengono raccolti da DevTest Labs?
DevTest Labs raccoglie due principali tipi di dati personali dell'utente: l'indirizzo di posta elettronica utente e l'ID oggetto utente. Queste informazioni sono fondamentali per il servizio per fornire funzionalità interne ad amministratori e utenti dei lab.

### <a name="user-email-address"></a>Indirizzo di posta elettronica utente
DevTest Labs usa l'indirizzo di posta elettronica utente per inviare notifiche di arresto automatico tramite posta elettronica agli utenti dei lab. Il messaggio di posta elettronica notifica agli utenti che il loro computer sta per essere arrestato. Gli utenti, se lo desiderano, possono posporre o ignorare l'arresto. L'indirizzo di posta elettronica viene configurato a livello di lab o a livello di macchina virtuale.

**Impostazione dell'indirizzo di posta elettronica a livello di lab:**

![Impostazione dell'indirizzo di posta elettronica a livello di lab](./media/personal-data-delete-export/lab-user-email.png)

**Impostazione dell'indirizzo di posta elettronica a livello di macchina virtuale:**

![Impostazione dell'indirizzo di posta elettronica a livello di macchina virtuale](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ID oggetto utente
DevTest Labs usa l'ID oggetto utente per mostrare agli amministratori dei lab gli andamenti mensili dei costi e le informazioni sui costi per risorsa. Consente di tenere traccia dei costi e gestire i valori di soglia per i lab. 

**Andamento dei costi stimato per il mese di calendario corrente:** 
![Andamento dei costi stimato per il mese di calendario corrente](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Costi stimati per risorsa da inizio mese:** 
![Costi stimati per risorsa da inizio mese](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Perché sono necessari i dati personali?
Il servizio DevTest Labs usa i dati personali per scopi operativi. Questi dati sono fondamentali per consentire al servizio di fornire funzionalità chiave. Se si imposta un criterio di conservazione per l'indirizzo di posta elettronica utente, gli utenti del lab, dopo l'eliminazione del loro indirizzo di posta elettronica dal sistema, non ricevono notifiche tempestive di arresto automatico tramite posta elettronica. Analogamente, se gli ID oggetto utente vengono eliminati in base a un criterio di conservazione, gli amministratori dei lab non sono in grado di visualizzare gli andamenti dei costi mensili e i costi per risorsa per i computer dei lab. Di conseguenza, questi dati devono essere mantenuti fino a quando la risorsa dell'utente è attiva nel lab.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Come è possibile fare in modo che il sistema dimentichi i dati personali?
Se un utente del lab vuole che i dati personali vengano eliminati, può provvedere a eliminare la risorsa corrispondente nel lab. Il servizio DevTest Labs rende anonimi i dati personali eliminati 30 giorni dopo l'eliminazione da parte dell'utente.

Se ad esempio si elimina la macchina virtuale o si rimuove l'indirizzo di posta elettronica, il servizio DevTest Labs rende anonimi questi dati dopo 30 giorni dall'eliminazione della risorsa. Il criterio di conservazione dei 30 giorni dopo l'eliminazione serve ad assicurare all'amministratore del lab che le previsioni dei costi mensili sono accurate.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Come si può richiedere un'esportazione dei dati personali?
Un utente del lab può richiedere un'esportazione dei dati personali archiviati nel servizio DevTest Labs. Per richiedere un'esportazione, passare all'opzione **Dati personali** nella pagina **Panoramica** del lab. Selezionare il pulsante **Richiedi esportazione** per avviare la creazione di un file Excel scaricabile nell'account di archiviazione dell'amministratore del lab. È quindi possibile contattare l'amministratore di lab per visualizzare i dati.

1. Selezionare **Dati personali** nel menu a sinistra. 

    ![Pagina Dati personali](./media/personal-data-delete-export/personal-data-page.png)
2. Selezionare il **gruppo di risorse** che contiene il lab.

    ![Selezionare il gruppo di risorse](./media/personal-data-delete-export/select-resource-group.png)
3. Selezionare l'**account di archiviazione** nel gruppo di risorse.
4. Nella pagina **Account di archiviazione** selezionare **BLOB**.

    ![Selezionare il riquadro BLOB](./media/personal-data-delete-export/select-blobs-tile.png)
5. Selezionare il contenitore denominato **labresourceusage** nell'elenco dei contenitori.

    ![Selezionare il contenitore BLOB](./media/personal-data-delete-export/select-blob-container.png)
6. Selezionare la **cartella** denominata in base al lab. In questa cartella sono presenti file con estensione **csv** per i **dischi** e le **macchine virtuali** del lab. È possibile scaricare i file con estensione csv, filtrare il contenuto relativo all'utente del lab che richiede un accesso e condividere il contenuto con tale utente.

    ![Scaricare il file con estensione csv](./media/personal-data-delete-export/download-csv-file.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Impostare i criteri per un lab](devtest-lab-get-started-with-lab-policies.md)
- [Domande frequenti](devtest-lab-faq.md)
