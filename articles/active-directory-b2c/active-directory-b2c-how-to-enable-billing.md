---
title: Modello di fatturazione per Azure Active Directory B2C
description: Informazioni sul modello di fatturazione di Azure AD B2C mensile per utenti attivi (MAU) e su come abilitare la fatturazione per una sottoscrizione di Azure specifica.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 844b62f9575249c7b99672e9e67c94cea7ec9f99
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931492"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Modello di fatturazione per Azure Active Directory B2C

L'utilizzo di Azure Active Directory B2C (Azure AD B2C) viene addebitato a una sottoscrizione di Azure collegata e usa un modello di fatturazione per utenti attivi mensili (MAU). Informazioni su come collegare una risorsa Azure AD B2C a una sottoscrizione e come funziona il modello di fatturazione di MAU nelle sezioni seguenti.

> [!IMPORTANT]
> Questo articolo non contiene informazioni sui prezzi. Per informazioni aggiornate sulla fatturazione e sui prezzi di utilizzo, vedere [Azure Active Directory B2C prezzi](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="monthly-active-users-mau-billing"></a>Fatturazione mensile per utenti attivi (MAU)

Azure AD B2C la fatturazione viene calcolata sul numero di utenti univoci con attività di autenticazione in un mese di calendario, noto come fatturazione di utenti attivi mensili (MAU).

A partire dal **01 novembre 2019**, tutti i nuovi tenant di Azure ad B2C creati vengono fatturati per utenti attivi mensili (Mau). I tenant esistenti [collegati a una sottoscrizione](#link-an-azure-ad-b2c-tenant-to-a-subscription) in o dopo il 01 novembre 2019 verranno fatturati per utenti attivi mensili (Mau).

Se si dispone di un tenant di Azure AD B2C esistente collegato a una sottoscrizione precedente alla 01 novembre 2019, è possibile scegliere di effettuare una delle seguenti operazioni:

* Eseguire l'aggiornamento al modello di fatturazione Monthly Active users (MAU) o
* Mantenere il modello di fatturazione per autenticazione

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Eseguire l'aggiornamento al modello di fatturazione utenti attivi mensili

I proprietari delle sottoscrizioni di Azure che hanno accesso amministrativo alla risorsa Azure AD B2C possono passare al modello di fatturazione MAU. Le opzioni di fatturazione sono configurate nella risorsa Azure AD B2C.

Il passaggio alla fatturazione mensile di utenti attivi (MAU) è **irreversibile**. Dopo la conversione di una risorsa Azure AD B2C nel modello di fatturazione basato su MAU, non è possibile ripristinare tale risorsa al modello di fatturazione per autenticazione.

Di seguito viene illustrato come passare alla fatturazione di MAU per una risorsa Azure AD B2C esistente:

1. Accedere al [portale di Azure](https://portal.azure.com) come proprietario della sottoscrizione.
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory Azure ad B2C di cui si vuole eseguire l'aggiornamento a Mau Billing.<br/>
    ![il filtro di directory e sottoscrizioni in portale di Azure](media/active-directory-b2c-how-to-enable-billing/portal-mau-01-select-b2c-directory.png)
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina **Panoramica** del tenant di Azure ad B2C selezionare il collegamento in **nome risorsa**. Si viene indirizzati alla risorsa Azure AD B2C nel tenant di Azure AD.<br/>
    ![Azure AD B2C collegamento alla risorsa evidenziato in portale di Azure](media/active-directory-b2c-how-to-enable-billing/portal-mau-02-b2c-resource-link.png)
1. Nella pagina **Panoramica** della risorsa Azure ad B2C, in **unità fatturabili**, selezionare il collegamento **per autenticazione (modifica a Mau)** .<br/>
    ![modificare il collegamento MAU evidenziato in portale di Azure](media/active-directory-b2c-how-to-enable-billing/portal-mau-03-change-to-mau-link.png)
1. Selezionare **conferma** per completare l'aggiornamento alla fatturazione di Mau.<br/>
    ![finestra di dialogo di conferma della fatturazione basata su MAU in portale di Azure](media/active-directory-b2c-how-to-enable-billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Cosa aspettarsi quando si esegue la transizione alla fatturazione di MAU dalla fatturazione per autenticazione

La misurazione basata su MAU è abilitata non appena il proprietario della sottoscrizione o della risorsa conferma la modifica. La fattura mensile rifletterà le unità di autenticazione fatturate fino alla modifica e le nuove unità di MAU che iniziano con la modifica.

Gli utenti non sono conteggiati a doppio durante il mese di transizione. Per gli utenti attivi univoci che eseguono l'autenticazione prima della modifica viene addebitata una tariffa per autenticazione in un mese di calendario. Gli stessi utenti non sono inclusi nel calcolo MAU per il resto del ciclo di fatturazione della sottoscrizione. ad esempio:

* Il tenant di Contoso B2C ha 1.000 utenti. 250 gli utenti sono attivi in un determinato mese. L'amministratore della sottoscrizione passa da per autenticazione a utenti attivi mensili (MAU) il 10 del mese.
* La fatturazione per il 1 ° decimo viene fatturata usando il modello di autenticazione.
  * Se 100 utenti eseguono l'accesso durante questo periodo (1 ° decimo), gli utenti vengono contrassegnati come *pagati per il mese*.
* La fatturazione dal decimo (il tempo effettivo di transizione) viene fatturata alla tariffa di MAU.
  * Se un altro utente di 150 accede durante questo periodo (10a-30), vengono fatturate solo le 150 aggiuntive.
  * L'attività continua dei primi 100 utenti non influisca sulla fatturazione per il resto del mese di calendario.

Durante il periodo di fatturazione della transizione, il proprietario della sottoscrizione visualizzerà probabilmente le voci per entrambi i metodi (per autenticazione e per ogni MAU) presenti nell'istruzione di fatturazione della sottoscrizione di Azure:

* Una voce per l'utilizzo fino alla data/ora di modifica che riflette per autenticazione.
* Una voce per l'utilizzo dopo la modifica che riflette gli utenti attivi mensili (MAU).

Per informazioni aggiornate sulla fatturazione e sui prezzi di utilizzo per Azure AD B2C, vedere [prezzi Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Collegare un tenant di Azure AD B2C a una sottoscrizione

Gli addebiti per l'utilizzo per Azure Active Directory B2C (Azure AD B2C) vengono fatturati a una sottoscrizione di Azure. Quando viene creato un tenant di Azure AD B2C, l'amministratore del tenant deve collegarlo in modo esplicito a una sottoscrizione di Azure.

Il collegamento alla sottoscrizione viene effettuato creando una *risorsa* Azure ad B2C all'interno della sottoscrizione di Azure di destinazione. È possibile creare diverse Azure AD B2C risorse in una singola sottoscrizione di Azure, insieme ad altre risorse di Azure, ad esempio macchine virtuali, account di archiviazione e app per la logica. È possibile visualizzare tutte le risorse all'interno di una sottoscrizione passando al tenant Azure Active Directory (Azure AD) a cui è associata la sottoscrizione.

Una sottoscrizione collegata a un tenant di Azure AD B2C può essere usata per la fatturazione dell'utilizzo Azure AD B2C o di altre risorse di Azure, incluse risorse Azure AD B2C aggiuntive. La sottoscrizione non può essere usata per aggiungere altri servizi basati su licenza di Azure o licenze di Office 365 nel tenant di Azure AD B2C.

### <a name="prerequisites"></a>Prerequisiti

* [Sottoscrizione di Azure](https://azure.microsoft.com/free/)
* [Azure ad B2C tenant](active-directory-b2c-get-started.md) che si vuole collegare a una sottoscrizione
  * È necessario essere un amministratore tenant
  * Il tenant non deve essere già collegato a una sottoscrizione

### <a name="create-the-link"></a>Creare il collegamento

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene la sottoscrizione di Azure che si vuole usare (*non* la directory che contiene il tenant di Azure ad B2C).
1. Selezionare **Crea una risorsa**, immettere `Active Directory B2C` nel campo **Cerca nel Marketplace** e quindi selezionare **Azure Active Directory B2C**.
1. Selezionare **Crea**
1. Selezionare **collega un tenant di Azure ad B2C esistente alla sottoscrizione di Azure**.
1. Selezionare un **Tenant Azure ad B2C** dall'elenco a discesa. Vengono visualizzati solo i tenant di cui si è un amministratore globale e che non sono già collegati a una sottoscrizione. Il campo **nome risorsa Azure ad B2C** viene popolato con il nome di dominio del tenant di Azure ad B2C selezionato.
1. Selezionare una **sottoscrizione** di Azure attiva di cui si è amministratore.
1. In **gruppo di risorse**selezionare **Crea nuovo**e quindi specificare il **percorso del gruppo di risorse**. Le impostazioni del gruppo di risorse non hanno alcun effetto sulla posizione, sulle prestazioni o sullo stato di fatturazione del tenant Azure AD B2C.
1. Selezionare **Create** (Crea).
    ![pagina di creazione della risorsa di Azure AD B2C in portale di Azure](./media/active-directory-b2c-how-to-enable-billing/portal-01-create-b2c-resource-page.png)

Dopo aver completato questi passaggi per un tenant di Azure AD B2C, la sottoscrizione di Azure viene fatturata in base ai dettagli di Azure Direct o Enterprise Agreement, se applicabile.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gestire le risorse del tenant di Azure AD B2C

Dopo aver creato la risorsa Azure AD B2C in una sottoscrizione di Azure, verrà visualizzata una nuova risorsa di tipo "tenant B2C" con le altre risorse di Azure.

È possibile usare questa risorsa per:

* Passare alla sottoscrizione per esaminare le informazioni di fatturazione
* Ottenere l'ID tenant del tenant Azure AD B2C in formato GUID
* Passare al tenant di Azure AD B2C.
* Invia una richiesta di supporto
* Spostare la risorsa del tenant di Azure AD B2C in un'altra sottoscrizione o gruppo di risorse di Azure

![Pagina Impostazioni risorse B2C nella portale di Azure](./media/active-directory-b2c-how-to-enable-billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Restrizioni a livello di area

Se sono state stabilite restrizioni a livello di area per la creazione di risorse di Azure nella sottoscrizione, tale restrizione potrebbe impedire la creazione della risorsa Azure AD B2C.

Per attenuare questo problema, è possibile ridurre le restrizioni a livello di area.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Sottoscrizioni di Azure Cloud Solution Provider (CSP)

Le sottoscrizioni di Azure Cloud Solution Provider (CSP) sono supportate in Azure AD B2C. La funzionalità è disponibile tramite le API o il portale di Azure per Azure AD B2C e per tutte le risorse di Azure. Gli amministratori della sottoscrizione CSP possono collegare, spostare ed eliminare relazioni con Azure AD B2C come fatto con altre risorse di Azure.

La gestione di Azure AD B2C tramite il controllo degli accessi in base al ruolo non è influenzata dall'associazione tra il tenant di Azure AD B2C e una sottoscrizione di Azure CSP. Il controllo degli accessi in base al ruolo è ottenibile usando ruoli basati su tenant, non ruoli basati su sottoscrizioni.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Modificare la sottoscrizione per la fatturazione di Azure AD B2C tenant

Azure AD B2C tenant possono essere spostati in un'altra sottoscrizione se le sottoscrizioni di origine e di destinazione sono presenti nello stesso tenant di Azure Active Directory.

Per informazioni su come spostare risorse di Azure come il tenant di Azure AD B2C in un'altra sottoscrizione, vedere [spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md).

Prima di iniziare lo spostamento, assicurarsi di leggere l'intero articolo per comprendere completamente le limitazioni e i requisiti per tale spostamento. Oltre alle istruzioni per lo spostamento delle risorse, include informazioni critiche come un elenco di controllo di pre-spostamento e come convalidare l'operazione di spostamento.

## <a name="next-steps"></a>Passaggi successivi

Oltre a esaminare i dettagli relativi all'utilizzo e alla fatturazione in una sottoscrizione di Azure selezionata, è possibile esaminare i report dettagliati sull'utilizzo giornaliero usando l'API per la [creazione di report sull'utilizzo](active-directory-b2c-reference-usage-reporting-api.md).
