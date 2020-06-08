---
title: Come creare una richiesta di supporto di Azure | Documentazione Microsoft
description: I clienti che necessitano di assistenza possono usare il portale di Azure per trovare soluzioni self-service e per creare e gestire richieste di supporto.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 03/31/2020
ms.author: kfollis
ms.openlocfilehash: 0bd1191c0b92203b100b1713971119ec828352ea
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835547"
---
# <a name="how-to-create-an-azure-support-request"></a>Come creare una richiesta di supporto in Azure

## <a name="overview"></a>Panoramica

Azure consente di creare e gestire richieste di supporto, note anche come ticket di supporto. È possibile creare e gestire le richieste nel [portale di Azure](https://portal.azure.com), come illustrato in questo articolo. È anche possibile creare e gestire le richieste a livello programmatico, mediante l'[API REST per i ticket di supporto tecnico di Azure](/rest/api/support).

> [!NOTE]
> L'URL del portale di Azure è specifico per il cloud di Azure in cui viene distribuita l'organizzazione.
>
>* Il portale di Azure per uso commerciale è: [https://portal.azure.com](https://portal.azure.com)
>* Il portale di Azure per la Germania è [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Il portale di Azure per il governo degli Stati Uniti è [https://portal.azure.us](https://portal.azure.us)
>
>

In base ai suggerimenti dei clienti, abbiamo aggiornato l'esperienza relativa alla richiesta di supporto per concentrarsi su tre obiettivi principali:

* **Semplificazione**: rendere supporto e risoluzione dei problemi facili da trovare e semplificare la procedura di invio di una richiesta di supporto.
* **Integrazione**: è semplice aprire una richiesta di supporto quando si risolve un problema relativo a una risorsa, senza cambiare contesto.
* **Efficienza**: raccogliere le informazioni chiave necessarie per l'agente di supporto per risolvere il problema in modo efficiente.

## <a name="getting-started"></a>Introduzione

Selezionare **Guida e supporto** nel portale di Azure. È disponibile dal menu del portale di Azure, dall'intestazione globale o dal menu delle risorse per un servizio. Prima di poter archiviare una richiesta di supporto, è necessario disporre delle autorizzazioni appropriate.

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Per creare una richiesta di supporto, è necessario essere [Proprietario](../../role-based-access-control/built-in-roles.md#owner) o [Collaboratore](../../role-based-access-control/built-in-roles.md#contributor) o essere assegnato al ruolo di [Collaboratore richieste di supporto](../../role-based-access-control/built-in-roles.md#support-request-contributor) a livello di sottoscrizione. Per creare una richiesta di supporto senza una sottoscrizione, ad esempio in uno scenario Azure Active Directory (AAD), è necessario essere un [Amministratore](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="go-to-help--support-from-the-global-header"></a>Passare a Guida e supporto dall'intestazione globale

Per avviare una richiesta di supporto da qualsiasi punto del portale di Azure:

1. Selezionare il simbolo **?** nell'intestazione globale. Selezionare quindi **Guida e supporto**.

   ![Guida e supporto](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Selezionare **Nuova richiesta di supporto**. Seguire le istruzioni a video per fornire informazioni sul problema. Verranno suggerite alcune possibili soluzioni, verranno fornite informazioni dettagliate sul problema e su come inviare e tenere traccia della richiesta di supporto.

   ![Nuova richiesta di supporto](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Passare a Guida e supporto da un menu delle risorse

Per avviare una richiesta di supporto nel contesto della risorsa, attualmente è necessario procedere come segue:

1. Dal menu delle risorse, nella sezione **Supporto e risoluzione dei problemi**, selezionare **Nuova richiesta di supporto**.

   ![Nel contesto](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Seguire le istruzioni a video per fornire informazioni sul problema riscontrato. Quando si avvia il processo di richiesta di supporto dalla risorsa, alcune opzioni sono pre-selezionate.

## <a name="create-a-support-request"></a>Creare una richiesta di supporto

Verranno illustrati alcuni passaggi per raccogliere informazioni sul problema e risolverlo. Ogni passaggio è descritto nelle sezioni seguenti.

### <a name="basics"></a>Nozioni di base

Il primo passaggio del processo di richiesta di supporto consiste nella raccolta di informazioni di base sul problema e sul piano di supporto.

Nella scheda **Nozioni di base** in **Nuova richiesta di supporto**, usare i selettori per iniziare a indicare il problema. Per prima cosa, è necessario identificare alcune categorie generali per il tipo di problema e scegliere la sottoscrizione correlata. Selezionare il servizio, ad esempio **Macchina virtuale che esegue Windows**. Selezionare la risorsa, ad esempio il nome della macchina virtuale. Descrivere il problema con parole proprie, quindi **Selezionare un tipo di problema** per ottenere informazioni più specifiche.

![Pannello Nozioni di base](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure offre un supporto illimitato per la gestione delle sottoscrizioni, che include fatturazione, modifiche delle quote e trasferimenti di account. Per rivolgersi al supporto tecnico è necessario un piano di supporto. [Altre informazioni sui piani di supporto](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Soluzioni

Dopo avere raccolto le informazioni di base, verranno mostrate alcune soluzioni da provare autonomamente. In alcuni casi, è anche possibile eseguire una diagnostica rapida. Le soluzioni sono scritte dai tecnici di Azure e consentono di risolvere i problemi più comuni.

### <a name="details"></a>Dettagli

Successivamente, vengono raccolti ulteriori dettagli sul problema. Fornire informazioni approfondite e dettagliate in questo passaggio consente di instradare la richiesta di supporto all'agente corretto.

Se possibile, indicare quando il problema è iniziato ed eventuali passaggi per riprodurlo. È possibile caricare un file, ad esempio un file di log o un output di diagnostica.

Una volta fornite tutte le informazioni sul problema, scegliere come ottenere supporto. Nella sezione relativa al **Metodo di supporto tecnico** dei **dettagli**, selezionare la gravità dell'impatto. Fornire il metodo di contatto preferito, un momento opportuno per essere contattati e la lingua di supporto.

Successivamente, completare la sezione **Informazioni di contatto** per indicare come essere contattati.

### <a name="review--create"></a>Rivedi e crea

Completare tutte le informazioni necessarie in ogni scheda, quindi selezionare **Rivedi e crea**. Verificare i dettagli da inviare al supporto. Tornare a eventuali schede da modificare, se necessario. Una volta terminato, selezionare **Crea**.

Un agente di supporto contatterà l'utente tramite il metodo indicato. Per informazioni sui tempi di risposta iniziale, vedere [Ambito e tempi di risposta del supporto](https://azure.microsoft.com/support/plans/response/).

## <a name="all-support-requests"></a>Tutte le richieste di supporto

È possibile visualizzare i dettagli e lo stato delle richieste di supporto passando a **Guida e supporto** >  **Tutte le richieste di supporto**.

![Tutte le richieste di supporto](./media/how-to-create-azure-support-request/allrequestslower.png)

In questa pagina è possibile filtrare le richieste di supporto per **Sottoscrizione**, data di **Creazione** (UTC) e **Stato**. Inoltre, in questa pagina si possono ordinare e cercare le richieste di supporto.

Selezionare una richiesta di supporto per visualizzarne i dettagli, tra cui gravità e tempi di risposta previsti da parte di un agente del supporto.

Per modificare la gravità della richiesta, selezionare **Impatto aziendale**. Scegliere da un elenco di livelli di gravità da assegnare.

> [!NOTE]
> Il livello di gravità massimo dipende dal piano di supporto. [Altre informazioni sui piani di supporto](https://azure.microsoft.com/support/plans).
>
>
Per altre informazioni sulle opzioni di supporto self-help in Azure, guardare questo video:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Passaggi successivi

* [Inviare feedback e suggerimenti](https://feedback.azure.com/forums/266794-support-feedback)
* Interagire con Microsoft su [Twitter](https://twitter.com/azuresupport)
* Ottenere assistenza dai peer sulla [pagina delle domande di Domande e risposte Microsoft](https://docs.microsoft.com/answers/products/azure)
* Altre informazioni sono disponibili in [Domande frequenti relative al supporto di Azure](https://azure.microsoft.com/support/faq)
