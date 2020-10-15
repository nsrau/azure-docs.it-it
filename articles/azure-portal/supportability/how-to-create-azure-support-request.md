---
title: Come creare una richiesta di supporto di Azure | Documentazione Microsoft
description: I clienti che necessitano di assistenza possono usare il portale di Azure per trovare soluzioni self-service e per creare e gestire richieste di supporto.
services: Azure Supportability
author: mgblythe
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: how-to
ms.date: 06/25/2020
ms.author: mblythe
ms.openlocfilehash: b44b38e52b50dd7994326f09bebbb775c93c0e2e
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073700"
---
# <a name="create-an-azure-support-request"></a>Creare una richiesta di supporto in Azure

Azure consente di creare e gestire richieste di supporto, note anche come ticket di supporto. È possibile creare e gestire le richieste nel [portale di Azure](https://portal.azure.com), come illustrato in questo articolo. È anche possibile creare e gestire le richieste a livello programmatico, mediante l'[API REST per i ticket di supporto tecnico di Azure](/rest/api/support).

> [!NOTE]
> L'URL del portale di Azure è specifico per il cloud di Azure in cui viene distribuita l'organizzazione.
>
>* Il portale di Azure per uso commerciale è: [https://portal.azure.com](https://portal.azure.com)
>* Il portale di Azure per la Germania è [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Il portale di Azure per il governo degli Stati Uniti è [https://portal.azure.us](https://portal.azure.us)

L'esperienza di richiesta di supporto è incentrata su tre obiettivi principali:

* **Semplificazione**: rendere supporto e risoluzione dei problemi facili da trovare e semplificare la procedura di invio di una richiesta di supporto.
* **Integrazione**: è semplice aprire una richiesta di supporto quando si risolve un problema relativo a una risorsa, senza cambiare contesto.
* **Efficienza**: raccolta delle informazioni chiave necessarie per il tecnico di supporto per risolvere il problema in modo efficiente.

Azure offre un supporto illimitato per la gestione delle sottoscrizioni, che include fatturazione, modifiche delle quote e trasferimenti di account. Per rivolgersi al supporto tecnico è necessario un piano di supporto. Per altre informazioni, vedere [Confronto dei piani di supporto](https://azure.microsoft.com/support/plans).

## <a name="getting-started"></a>Introduzione

Selezionare **Guida e supporto** nel portale di Azure. È disponibile dal menu del portale di Azure, dall'intestazione globale o dal menu delle risorse per un servizio. Prima di poter archiviare una richiesta di supporto, è necessario disporre delle autorizzazioni appropriate.

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Per creare una richiesta di supporto, è necessario essere [Proprietario](../../role-based-access-control/built-in-roles.md#owner) o [Collaboratore](../../role-based-access-control/built-in-roles.md#contributor) o essere assegnato al ruolo di [Collaboratore richieste di supporto](../../role-based-access-control/built-in-roles.md#support-request-contributor) a livello di sottoscrizione. Per creare una richiesta di supporto senza una sottoscrizione, ad esempio uno scenario di Azure Active Directory, è necessario essere un [amministratore](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="go-to-help--support-from-the-global-header"></a>Passare a Guida e supporto dall'intestazione globale

Per avviare una richiesta di supporto da qualsiasi punto del portale di Azure:

1. Selezionare il simbolo **?** nell'intestazione globale. Selezionare quindi **Guida e supporto**.

   ![Guida e supporto](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

1. Selezionare **Nuova richiesta di supporto**. Seguire le istruzioni per fornire informazioni sul problema. Verranno suggerite alcune possibili soluzioni, verranno fornite informazioni dettagliate sul problema e su come inviare e tenere traccia della richiesta di supporto.

   ![Nuova richiesta di supporto](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Passare a Guida e supporto da un menu delle risorse

Per avviare una richiesta di supporto nel contesto della risorsa, attualmente è necessario procedere come segue:

1. Dal menu delle risorse, nella sezione **Supporto e risoluzione dei problemi**, selezionare **Nuova richiesta di supporto**.

   ![Nel contesto](./media/how-to-create-azure-support-request/incontext2lower.png)

1. Seguire le istruzioni a video per fornire informazioni sul problema riscontrato. Quando si avvia il processo di richiesta di supporto dalla risorsa, alcune opzioni sono pre-selezionate.

## <a name="create-a-support-request"></a>Creare una richiesta di supporto

Verranno illustrati alcuni passaggi per raccogliere informazioni sul problema e risolverlo. Ogni passaggio è descritto nelle sezioni seguenti.

### <a name="basics"></a>Nozioni di base

Il primo passaggio del processo di richiesta di supporto consiste nella raccolta di informazioni di base sul problema e sul piano di supporto.

Nella scheda **Nozioni di base** in **Nuova richiesta di supporto**, usare i selettori per iniziare a indicare il problema. Per prima cosa, è necessario identificare alcune categorie generali per il tipo di problema e scegliere la sottoscrizione correlata. Selezionare il servizio, ad esempio **macchina virtuale che esegue Windows**. Selezionare la risorsa, ad esempio il nome della macchina virtuale. Descrivere il problema con le proprie parole, quindi selezionare **tipo di problema** e **sottotipo di problema** per ottenere informazioni più specifiche.

![Pannello Nozioni di base](./media/how-to-create-azure-support-request/basics2lower.png)

### <a name="solutions"></a>Soluzioni

Dopo avere raccolto le informazioni di base, verranno mostrate alcune soluzioni da provare autonomamente. In alcuni casi, è anche possibile eseguire una diagnostica rapida. Le soluzioni sono scritte dai tecnici di Azure e consentono di risolvere i problemi più comuni.

### <a name="details"></a>Dettagli

Successivamente, vengono raccolti ulteriori dettagli sul problema. Fornire informazioni approfondite e dettagliate in questo passaggio consente di instradare la richiesta di supporto al tecnico appropriato.

1. Se possibile, indicare quando il problema è iniziato ed eventuali passaggi per riprodurlo. È possibile caricare un file, ad esempio un file di log o un output di diagnostica. Per ulteriori informazioni sul caricamento di file, vedere [linee guida](how-to-manage-azure-support-request.md#file-upload-guidelines)per il caricamento di file.

1. Una volta fornite tutte le informazioni sul problema, scegliere come ottenere supporto. Nella sezione relativa al **Metodo di supporto tecnico** dei **dettagli**, selezionare la gravità dell'impatto. Il livello di gravità massimo dipende dal [piano di supporto](https://azure.microsoft.com/support/plans).

    Per impostazione predefinita, è selezionata l'opzione **Condividi informazioni di diagnostica** . Questo consente al supporto di Azure di raccogliere [informazioni di diagnostica](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) dalle risorse di Azure. In alcuni casi, è presente una seconda domanda che non viene selezionata per impostazione predefinita, ad esempio la richiesta di accesso alla memoria di una macchina virtuale.

1. Fornire il metodo di contatto preferito, un momento opportuno per essere contattati e la lingua di supporto.

1. Successivamente, completare la sezione **Informazioni di contatto** per indicare come essere contattati.

### <a name="review--create"></a>Rivedi e crea

Completare tutte le informazioni necessarie in ogni scheda, quindi selezionare **Rivedi e crea**. Verificare i dettagli da inviare per il supporto. Tornare a eventuali schede da modificare, se necessario. Una volta terminato, selezionare **Crea**.

Un tecnico del supporto contatterà l'utente usando il metodo indicato. Per informazioni sui tempi di risposta iniziali, vedere [ambito del supporto tecnico e](https://azure.microsoft.com/support/plans/response/)velocità di risposta.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle opzioni di supporto self-help in Azure, guardare questo video:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

Per ulteriori informazioni, seguire i collegamenti seguenti:

* [Come gestire una richiesta di supporto di Azure](how-to-manage-azure-support-request.md)
* [API REST dei ticket di supporto di Azure](/rest/api/support)
* [Inviare feedback e suggerimenti](https://feedback.azure.com/forums/266794-support-feedback)
* Interagire con Microsoft su [Twitter](https://twitter.com/azuresupport)
* Ottenere assistenza dai peer sulla [pagina delle domande di Domande e risposte Microsoft](/answers/products/azure)
* Altre informazioni sono disponibili in [Domande frequenti relative al supporto di Azure](https://azure.microsoft.com/support/faq)