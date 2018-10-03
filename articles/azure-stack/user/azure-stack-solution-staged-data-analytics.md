---
title: Creare una soluzione analitica di dati di staging con Azure e Azure Stack | Microsoft Docs
description: Informazioni su come creare una soluzione analitica di dati di staging con Azure e Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/02/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: b4b81546a267e6fd082f83db8b23010f0742771f
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237906"
---
# <a name="tutorial-create-a-staged-data-analytics-solution-with-azure-and-azure-stack"></a>Esercitazione: Creare una soluzione analitica di dati di staging con Azure e Azure Stack 

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Informazioni su come usare in locale e ambienti cloud pubblici per soddisfare le esigenze delle aziende più funzionalità. Azure Stack offre una soluzione flessibile, sicura e rapido per la raccolta, elaborazione, l'archiviazione e la distribuzione di dati locali e remoti, in particolare quando sicurezza, riservatezza, criteri aziendali e ai requisiti normativi possono differire tra percorsi e gli utenti.

In questo modello, i clienti raccolgono i dati che richiede l'analisi al momento della raccolta in modo che sia possano prendere decisioni rapide. Questa raccolta di dati si verifica spesso senza accesso a Internet. Quando viene stabilita la connettività, è necessario eseguire un'analisi a elevato utilizzo di risorse dei dati per ottenere informazioni aggiuntive. È comunque possibile analizzare i dati quando un cloud pubblico è troppo tardi o non disponibile.

In questa esercitazione, creare un ambiente di esempio:

> [!div class="checklist"]
> - Creare il blob di archiviazione di dati non elaborati.
> - Creare una nuova funzione dello Stack di Azure per spostare pulire i dati da Azure Stack in Azure.
> - Creare una funzione attivata dall'archiviazione Blob.
> - Creare un account di archiviazione di Azure Stack che contiene un blob e una coda.
> - Creare una funzione attivata dalla coda.
> - Funzione attivata da coda di test.

> [!Tip]  
> ![ibrido-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack è un'estensione di Azure. Azure Stack offre l'agilità e innovazione del cloud computing al tuo ambiente locale e abilitare l'unico cloud ibrido che consente di compilare e distribuire le app ibride ovunque.  
> 
> Il white paper [considerazioni sulla progettazione per applicazioni ibride](https://aka.ms/hybrid-cloud-applications-pillars) esamina i concetti fondamentali della qualità del software (selezione host, scalabilità, disponibilità, resilienza, gestibilità e sicurezza) per la progettazione, distribuzione e gestione applicazioni ibride. Le considerazioni di progettazione assistere nell'ottimizzazione della progettazione di applicazioni ibride, riducendo al minimo le sfide negli ambienti di produzione.

## <a name="prerequisites"></a>Prerequisiti

Per compilare questa soluzione sono necessarie alcune attività di preparazione:

-   Azure Stack installato e funzionante (ulteriori informazioni sono disponibili qui: [Panoramica di Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-storage-overview))

-   Una sottoscrizione di Azure. (Creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

-   Scaricare e installare [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

-   È necessario fornire i propri dati da elaborare tramite le funzioni. Dati devono essere generati e sono disponibili per caricare il contenitore blob di archiviazione di Azure Stack.

## <a name="issues-and-considerations"></a>Considerazioni e problemi

### <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Funzioni di Azure e soluzioni di archiviazione di scalabilità per soddisfare il volume di dati e le richieste di elaborazione. Per informazioni sulla scalabilità di Azure e le destinazioni, vedere [documentazione di scalabilità di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

### <a name="availability-considerations"></a>Considerazioni sulla disponibilità

L'archiviazione è la considerazione di disponibilità primaria per questo modello. Connessione tramite collegamenti veloci è obbligatorio per la distribuzione e l'elaborazione di volumi di dati di grandi dimensioni.

### <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Considerare come il controllo del codice sorgente e strumenti di sviluppo consentirà di gestire la soluzione.

## <a name="create-the-raw-data-storage-blob"></a>Crea il blob di archiviazione di dati non elaborati

Il contenitore blob e l'account di archiviazione conterrà tutti i dati originali generati dalle attività in locale, tra cui attività macchina e dipendenti, i dati di struttura, le metriche di produzione e altri report.

1.  Accedi per il [ *portale di Azure Stack*](https://portal.local.azurestack.external/).

2.  Nel portale di Azure Stack, espandere il menu a sinistra per aprire il menu dei servizi e scegliere **tutti i servizi**. Scorrere verso il basso **memorizzazione** e scegliere **account di archiviazione**. Nella finestra di account di archiviazione, scegliere **Add**.

3.  Usare le informazioni seguenti per l'account:

    a.  Nome: **preferito**

    b.  Modello di distribuzione: **Resource Manager**

    c.  Tipologia account: **archiviazione (per utilizzo generico V1)**

    d.  Percorso: **Stati Uniti occidentali**

    e.  Replica: **archiviazione con ridondanza locale (LRS)**

    f.  Prestazioni: **Standard**

    g.  Trasferimento sicuro obbligatorio: **disabilitato**

    h.  Sottoscrizione: Scegliere uno

    i.  Gruppo di risorse: specificare un nuovo gruppo di risorse o selezionarne un esistente

    j.  Configurare le reti virtuali: **disabilitato**

4.  Selezionare **per creare l'account di archiviazione, crearne**.

    ![Alt text](media\azure-stack-solution-staged-data-analytics\image1.png)

5.  Una volta creato, selezionare il nome dell'account di archiviazione.

6.  Nel pannello dell'account, sotto il **servizio BLOB** titolo, selezionare **contenitori**.

7.  Nella parte superiore del pannello, selezionare **+ contenitore.** e selezionare **contenitore**.

    ![Alt text](media\azure-stack-solution-staged-data-analytics\image2.png)

8.  Nome: **preferito**

9.  Livello di accesso pubblico: **contenitore** (accesso in lettura anonimo per contenitori e BLOB)

10.  Selezionare **OK**.

## <a name="create-an-azure-stack-function"></a>Creare una funzione di Azure Stack

Creare una nuova funzione di Azure Stack per spostare pulire i dati da Azure Stack in Azure.

### <a name="create-the-azure-stack-function-app"></a>Creare app per le funzioni di Azure Stack

1. Accedi il [portale di Azure Stack](https://portal.local.azurestack.external).
2. Selezionare **Tutti i servizi**.
3. Selezionare **App per le funzioni** nel **Web e dispositivi mobili** gruppo.

4.  Creare app per le funzioni usando le impostazioni specificate nella tabella sotto l'immagine.

    | Impostazione | Valore consigliato | DESCRIZIONE |
    | ---- | ---- | ---- |
    | Nome app | Nome globalmente univoco | Nome che identifica la nuova app per le funzioni. I caratteri validi sono `a` - `z`, `0``-9`, e `-`. |
    | Sottoscrizione | Sottoscrizione in uso | Sottoscrizione in cui viene creata questa nuova app per le funzioni. |
    | **Gruppo di risorse** |  |  |
    | myResourceGroup | Nome del nuovo gruppo di risorse in cui creare l'app per le funzioni. |  |
    | OS | Windows | L'hosting senza server attualmente è disponibile solo in un ambiente di esecuzione su Windows. |
    | **Piano di hosting** |  |  |
    | Piano a consumo | Piano di hosting che definisce come vengono allocate le risorse all'app per le funzioni. Nel piano a consumo predefinito, le risorse vengono aggiunte in modo dinamico come richiesto dalle funzioni. In questo hosting senza server, si paga solo per l'esecuzione delle funzioni. |  |
    | Località | Area più vicina | Scegliere un'area nelle vicinanze o vicino ad altri servizi di accesso di funzioni. |
    | **Account di archiviazione** |  |  |
    | \<account di archiviazione creato in precedenza > | Nome del nuovo account di archiviazione usato dall'app per le funzioni. I nomi degli account di archiviazione deve essere di lunghezza compresa tra 3 e 24 caratteri. Il nome può usare solo lettere minuscole e numeri. È possibile anche usare un account esistente. |  |

    **Esempio:**

    ![Definire nuove impostazioni dell'app per le funzioni](media\azure-stack-solution-staged-data-analytics\image6.png)

5.  Selezionare **Crea** per effettuare il provisioning dell'app per le funzioni e distribuirla.

6.  Selezionare l'icona di notifica nell'angolo superiore destro del portale e attendere la visualizzazione del messaggio **La distribuzione è riuscita**.

    ![Definire nuove impostazioni dell'app per le funzioni](media\azure-stack-solution-staged-data-analytics\image7.png)

7.  Selezionare **Vai alla risorsa** per visualizzare una nuova app per le funzioni.

![App per le funzioni creata correttamente.](media\azure-stack-solution-staged-data-analytics\image8.png)

### <a name="add-a-function-to-the-azure-stack-function-app"></a>Aggiungere una funzione per l'app funzioni di Azure Stack

1.  Creare una nuova funzione facendo clic su **funzioni**, il **+ nuova funzione** pulsante.

    ![Alt text](media\azure-stack-solution-staged-data-analytics\image3.png)

2.  Selezionare **Trigger Timer**.

    ![Alt text](media\azure-stack-solution-staged-data-analytics\image4.png)

3.  Selezionare **C\#**  come linguaggio e il nome di funzione: `upload-to-azure` impostare la pianificazione su `0 0 * * * *`, che in CRON, in notazione è una volta ogni ora.

    ![Alt text](media\azure-stack-solution-staged-data-analytics\image5.png)

## <a name="create-a-blob-storage-triggered-function"></a>Creare una funzione attivata dall'archiviazione BLOB

1.  Espandere l'app per le funzioni e selezionare il **+** accanto alla **funzioni**.

2.  Nel campo di ricerca, digitare `blob` e quindi scegliere la lingua da usare per il **trigger di Blob** modello.

  ![Scegliere il modello di attivazione dell'archiviazione BLOB.](media\azure-stack-solution-staged-data-analytics\image10.png)

3.  Usare le impostazioni specificate nella tabella seguente:

    | Impostazione | Valore consigliato | DESCRIZIONE |
    | ------- | ------- | ------- |
    | NOME | Univoco nell'app per le funzioni | Nome della funzione attivata dal BLOB. |
    | path | \<percorso dalla posizione di archiviazione precedente > | Percorso da monitorare nell'archiviazione BLOB. Il nome del file del blob viene passato nel binding come parametro del nome. |
    | Connessione dell'account di archiviazione | Connessione dell'App (funzione) | È possibile usare la connessione dell'account di archiviazione già usata da app per le funzioni o crearne uno nuovo. |

    **Esempio:**

    ![Creare una funzione attivata dall'archiviazione BLOB.](media\azure-stack-solution-staged-data-analytics\image11.png)

4.  Selezionare **Crea** per creare la funzione.

### <a name="test-the-function"></a>Testare la funzione

1.  Nel portale di Azure, passare alla funzione. Espandere la **registri** nella parte inferiore della pagina e assicurarsi che lo streaming dei log non viene sospesa.

2.  Aprire Storage Explorer e connettersi all'account di archiviazione creato all'inizio di questa sezione.

3.  Espandere l'account di archiviazione **contenitori Blob**, e il blob creato in precedenza. Selezionare **caricare** e quindi **caricare file**.

    ![Caricare un file nel contenitore BLOB.](media\azure-stack-solution-staged-data-analytics\image12.png)

4.  Nella finestra di dialogo Caricamento file, selezionare il campo di file. Passare a un file in un computer locale, ad esempio un file di immagine, selezionarlo e selezionare **aperto** e quindi **caricare**.

5.  Tornare ai log di funzione e verificare che il blob è stato letto.

    **Esempio:**

    ![Visualizzare il messaggio nei log.](media\azure-stack-solution-staged-data-analytics\image13.png)

## <a name="create-an-azure-stack-storage-account"></a>Creare un account di archiviazione di Azure Stack

Creare un account di archiviazione di Azure Stack che contiene un blob e una coda.

### <a name="storage-blob--data-archiving"></a>Archiviazione di dati di archiviazione Blob

Questo account di archiviazione conterrà due contenitori. Questi contenitori sono un blob usato per contenere dati di archiviazione e una coda usata per l'elaborazione di dati assegnati per la distribuzione sede centrale.

Usare i passaggi e le impostazioni indicate sopra per creare un altro contenitore di archiviazione blob e l'account come nostro spazio di archiviazione.

### <a name="storage-queue--filtered-data-holding"></a>Che contiene i dati filtrati della coda di archiviazione

1.  Usare i passaggi e le impostazioni descritte in precedenza per accedere all'account di archiviazione nuovo.

2.  Nella sezione Panoramica Account di archiviazione, selezionare **coda.**

3.  Selezionare il **+ coda** e il riempimento **nome** campo e digitare un nome per la nuova coda.

4.  Selezionare **OK.**

    ![Alt text](media\azure-stack-solution-staged-data-analytics\image14.png)

    ![Alt text](media\azure-stack-solution-staged-data-analytics\image15.png)

## <a name="create-a-queue-triggered-function"></a>Creare una funzione attivata da una coda

1.  Usare i passaggi nella sezione Creazione della funzione precedente per creare una funzione di Azure Stack aggiuntiva con un trigger della coda invece di un trigger di blob.

2.  Usare le impostazioni specificate nella tabella seguente:

    | Impostazione | Valore consigliato | DESCRIZIONE |
    | ------- | ------- | ------- |
    | NOME | Univoco nell'app per le funzioni | Nome della funzione attivata dalla coda. |
    | path | \<percorso dalla posizione di archiviazione precedente > | Percorso di archiviazione monitorato. Il nome del file della coda viene passato nel binding come parametro del nome. |
    | Connessione dell'account di archiviazione | Connessione dell'App (funzione) | È possibile usare la connessione dell'account di archiviazione già usata da app per le funzioni o crearne uno nuovo. |

3.  Selezionare **Crea** per creare la funzione.

## <a name="test-the-queue-triggered-function"></a>Funzione attivata da un test della coda

1.  Nel portale di Azure, passare alla funzione. Espandere la **registri** nella parte inferiore della pagina e assicurarsi che lo streaming dei log non viene sospesa.

2.  Aprire Storage Explorer e connettersi all'account di archiviazione creato all'inizio di questa sezione.

3.  Espandere l'account di archiviazione **contenitori Blob**, e il blob creato in precedenza. Selezionare **caricare** e quindi **caricare i file.**

    ![Caricare un file nel contenitore BLOB.](media\azure-stack-solution-staged-data-analytics\image12.png)

4.  Nella finestra di dialogo Caricamento file, selezionare il campo di file. Passare a un file in un computer locale, ad esempio un file di immagine, selezionarlo e selezionare **aperto** e quindi **caricare**.

5.  Tornare ai log di funzione e verificare che il blob è stato letto.

  **Esempio:**

    ![Visualizzare il messaggio nei log.](media\azure-stack-solution-staged-data-analytics\image13.png)

## <a name="securely-stored-and-accessed-compliant-data"></a>Dati conformi a cui si accede e archiviati in modo sicuro

In modo sicuro i dati aziendali globali sono archiviati, elaborati, distribuiti e accessibili tramite Azure e Azure Stack di gestione temporanea dei dati Analitica e direttive endpoint personalizzato. Attività dipendente e la macchina uffici remoti, i dati di struttura e le metriche di business vengono continuamente compilate, archiviate, verificare la conformità e distribuite in base ai criteri aziendali e delle normative internazionali.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui modelli Cloud di Azure, vedere [Cloud Design Patterns](https://docs.microsoft.com/azure/architecture/patterns).