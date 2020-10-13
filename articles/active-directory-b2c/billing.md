---
title: Modello di fatturazione per Azure Active Directory B2C
description: Informazioni sul modello di fatturazione di Azure AD B2C mensile per utenti attivi (MAU), su come collegare un tenant Azure AD B2C a una sottoscrizione di Azure e su come selezionare i prezzi per il livello Premium appropriati.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 416e2c767b5afd40fea38e6f75fcd3f01440b49a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89255346"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Modello di fatturazione per Azure Active Directory B2C

I prezzi Azure Active Directory B2C (Azure AD B2C) si basano sugli utenti attivi mensili (MAU), ovvero sul numero di utenti univoci con attività di autenticazione in un mese di calendario. Questo modello di fatturazione si applica sia ai tenant di Azure AD B2C sia alla [collaborazione Azure ad utenti Guest (B2B)](https://docs.microsoft.com/azure/active-directory/external-identities/external-identities-pricing). MAU Billing ti aiuta a ridurre i costi offrendo un livello gratuito e prezzi flessibili e prevedibili. In questo articolo vengono fornite informazioni sulla fatturazione di MAU, il collegamento dei tenant Azure AD B2C a una sottoscrizione e la modifica del piano tariffario.

> [!IMPORTANT]
> Questo articolo non contiene i dettagli relativi ai prezzi. Per informazioni aggiornate sulla fatturazione e sui prezzi di utilizzo, vedere [Azure Active Directory B2C prezzi](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="what-do-i-need-to-do"></a>Come devo procedere?

Per sfruttare i vantaggi della fatturazione di MAU, il tenant di Azure AD B2C deve essere collegato a una sottoscrizione di Azure. Potrebbe anche essere necessario passare il tenant di Azure AD B2C a un altro piano tariffario se si vuole usare Azure AD B2C funzionalità Premium P2, ad esempio l'accesso condizionale basato sul rischio.

|Se il tenant è:  |È necessario:  |
|---------|---------|
| Un tenant di Azure AD B2C già fatturato per ogni singolo MAU     | Non eseguire alcuna operazione. Quando gli utenti eseguono l'autenticazione al tenant di Azure AD B2C, l'addebito verrà addebitato automaticamente usando il modello di fatturazione basato su MAU.        |
| Un tenant Azure AD B2C non ancora collegato a una sottoscrizione     |  [Collegare il tenant di Azure ad B2C a una sottoscrizione](#link-an-azure-ad-b2c-tenant-to-a-subscription) per attivare la fatturazione di Mau.     |
| Un tenant Azure AD B2C collegato a una sottoscrizione prima del 1 ° novembre 2019    | [Passa alla fatturazione di Mau (scelta consigliata)](#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)o Mantieni il modello di fatturazione per autenticazione.     |
| Un tenant Azure AD B2C e si vogliono usare le funzionalità Premium, ad esempio l'accesso condizionale basato sul rischio    | [Passare a un piano tariffario Azure ad](#change-your-azure-ad-pricing-tier) che supporti le funzionalità che si desidera utilizzare.        |
|  |  |

## <a name="about-the-monthly-active-users-mau-billing-model"></a>Informazioni sul modello di fatturazione mensile per utenti attivi (MAU)

La fatturazione di MAU è stata applicata per Azure AD B2C tenant il **1 ° novembre 2019**. Tutti i tenant di Azure AD B2C creati e collegati a una sottoscrizione in data o dopo tale data sono stati fatturati in base ai singoli MAU. Se si dispone di un tenant Azure AD B2C che non è stato collegato a una sottoscrizione, è necessario farlo ora. Se si ha già un tenant di Azure AD B2C collegato a una sottoscrizione prima del 1 ° novembre 2019, è consigliabile eseguire l'aggiornamento al modello di fatturazione per utenti attivi mensili (MAU) oppure è possibile rimanere nel modello di fatturazione per autenticazione.
  
Il tenant di Azure AD B2C deve essere collegato anche al piano tariffario di Azure appropriato in base alle funzionalità che si vuole usare. Le funzionalità Premium richiedono Azure AD B2C [prezzi Premium P1 o P2](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Potrebbe essere necessario aggiornare il piano tariffario quando si utilizzano le nuove funzionalità. Ad esempio, l'accesso condizionale è necessario selezionare il piano tariffario Azure AD B2C Premium P2 per il tenant.

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Collegare un tenant di Azure AD B2C a una sottoscrizione

Gli addebiti per l'utilizzo per Azure Active Directory B2C (Azure AD B2C) vengono fatturati a una sottoscrizione di Azure. È necessario collegare in modo esplicito un tenant di Azure AD B2C a una sottoscrizione di Azure creando una *risorsa* Azure ad B2C all'interno della sottoscrizione di Azure di destinazione. È possibile creare diverse Azure AD B2C risorse in una singola sottoscrizione di Azure, insieme ad altre risorse di Azure, ad esempio macchine virtuali, account di archiviazione e app per la logica. È possibile visualizzare tutte le risorse all'interno di una sottoscrizione passando al tenant Azure Active Directory (Azure AD) a cui è associata la sottoscrizione.

Una sottoscrizione collegata a un tenant di Azure AD B2C può essere usata per la fatturazione dell'utilizzo Azure AD B2C o di altre risorse di Azure, incluse risorse Azure AD B2C aggiuntive. Non può essere usato per aggiungere altri servizi basati su licenze di Azure o licenze Office 365 all'interno del tenant Azure AD B2C.

### <a name="prerequisites"></a>Prerequisiti

* [Sottoscrizione di Azure](https://azure.microsoft.com/free/)
* [Azure ad B2C tenant](tutorial-create-tenant.md) che si vuole collegare a una sottoscrizione
  * È necessario essere un amministratore tenant
  * Il tenant non deve essere già collegato a una sottoscrizione

### <a name="create-the-link"></a>Creare il collegamento

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene la sottoscrizione di Azure che si vuole usare (*non* la directory che contiene il tenant di Azure ad B2C).
3. Selezionare **Crea una risorsa**, immettere `Active Directory B2C` nel campo **Cerca nel Marketplace** e quindi selezionare **Azure Active Directory B2C**.
4. Selezionare **Crea**.
5. Selezionare **collega un tenant di Azure ad B2C esistente alla sottoscrizione di Azure**.
6. Selezionare un **Tenant Azure ad B2C** dall'elenco a discesa. Vengono visualizzati solo i tenant per i quali si è un amministratore globale e che non sono già collegati a una sottoscrizione. Il campo **nome risorsa Azure ad B2C** viene popolato con il nome di dominio del tenant di Azure ad B2C selezionato.
7. Selezionare una **sottoscrizione** di Azure attiva di cui si è amministratori.
8. In **gruppo di risorse**selezionare **Crea nuovo**e quindi specificare il **percorso del gruppo di risorse**. Le impostazioni del gruppo di risorse non hanno alcun effetto sulla posizione, sulle prestazioni o sullo stato di fatturazione del tenant Azure AD B2C.
9. Selezionare **Crea**.

    ![Pagina di creazione della risorsa Azure AD B2C in portale di Azure](./media/billing/portal-01-create-b2c-resource-page.png)

Dopo aver completato questi passaggi per un tenant di Azure AD B2C, la sottoscrizione di Azure viene fatturata in base ai dettagli di Azure Direct o Enterprise Agreement, se applicabile.

## <a name="change-your-azure-ad-pricing-tier"></a>Modificare il piano tariffario Azure AD

Un tenant deve essere collegato al piano tariffario di Azure appropriato in base alle funzionalità che si vuole usare con il tenant del Azure AD B2C. Per le funzionalità Premium è necessario Azure AD B2C P1 o P2 Premium, come descritto nei [prezzi Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/). In alcuni casi, è necessario aggiornare il piano tariffario quando si usano le nuove funzionalità. Se ad esempio si vuole usare la protezione delle identità, l'accesso condizionale basato sul rischio e tutte le future funzionalità Premium P2 con Azure AD B2C, è necessario selezionare il piano tariffario Azure AD B2C Premium P2 per il tenant.

Per modificare il piano tariffario, attenersi alla seguente procedura.

1. Accedere al portale di Azure.

2. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene la sottoscrizione di Azure a cui è collegato il tenant di Azure B2C (*non* Selezionare il tenant Azure ad B2C).

3. Nella casella di ricerca nella parte superiore del portale immettere il nome del tenant Azure AD B2C. Quindi selezionare il tenant nei risultati della ricerca in **risorse**.

4. Nella pagina **Panoramica** risorse, in piano **tariffario**, selezionare **modifica**.

   ![Modificare il piano tariffario](media/billing/change-pricing-tier.png)
 
5. Selezionare il piano tariffario che include le funzionalità che si desidera abilitare.

   ![Selezionare il piano tariffario](media/billing/select-tier.png)

## <a name="switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants"></a>Passa alla fatturazione di MAU (prima di novembre 2019 Azure AD B2C tenant)

Se il tenant di Azure AD B2C è stato collegato a una sottoscrizione prima del **1 ° novembre 2019**, viene usato il modello di fatturazione per autenticazione precedente. Si consiglia di eseguire l'aggiornamento al modello di fatturazione Monthly Active users (MAU). Le opzioni di fatturazione sono configurate nella risorsa Azure AD B2C.

Il passaggio alla fatturazione mensile di utenti attivi (MAU) è **irreversibile**. Dopo la conversione di una risorsa Azure AD B2C nel modello di fatturazione basato su MAU, non è possibile ripristinare tale risorsa al modello di fatturazione per autenticazione.

Di seguito viene illustrato come passare alla fatturazione di MAU per una risorsa Azure AD B2C esistente:

1. Accedere al [portale di Azure](https://portal.azure.com) come proprietario della sottoscrizione con accesso amministrativo alla risorsa Azure ad B2C.

2. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory Azure ad B2C di cui si vuole eseguire l'aggiornamento a Mau Billing.<br/>

    ![Filtro di directory e sottoscrizione in portale di Azure](./media/billing/portal-mau-01-select-b2c-directory.png)

3. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.

4. Nella pagina **Panoramica** del tenant di Azure ad B2C selezionare il collegamento in **nome risorsa**. Si viene indirizzati alla risorsa Azure AD B2C nel tenant di Azure AD.<br/>

    ![Collegamento Azure AD B2C risorsa evidenziato in portale di Azure](./media/billing/portal-mau-02-b2c-resource-link.png)

5. Nella pagina **Panoramica** della risorsa Azure ad B2C, in **unità fatturabili**, selezionare il collegamento **per autenticazione (modifica a Mau)** .<br/>

    ![Passa al collegamento MAU evidenziato in portale di Azure](./media/billing/portal-mau-03-change-to-mau-link.png)

6. Selezionare **conferma** per completare l'aggiornamento alla fatturazione di Mau.<br/>

    ![Finestra di dialogo di conferma della fatturazione basata su MAU in portale di Azure](./media/billing/portal-mau-04-confirm-change-to-mau.png)


### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Cosa aspettarsi quando si esegue la transizione alla fatturazione di MAU dalla fatturazione per autenticazione

La misurazione basata su MAU è abilitata non appena il proprietario della sottoscrizione o della risorsa conferma la modifica. La fattura mensile rifletterà le unità di autenticazione fatturate fino alla modifica e le nuove unità di MAU che iniziano con la modifica.

Gli utenti non sono conteggiati a doppio durante il mese di transizione. Per gli utenti attivi univoci che eseguono l'autenticazione prima della modifica viene addebitata una tariffa per autenticazione in un mese di calendario. Gli stessi utenti non sono inclusi nel calcolo MAU per il resto del ciclo di fatturazione della sottoscrizione. Ad esempio:

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

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gestire le risorse del tenant di Azure AD B2C

Dopo aver creato la risorsa Azure AD B2C in una sottoscrizione di Azure, verrà visualizzata una nuova risorsa di tipo "tenant B2C" con le altre risorse di Azure.

È possibile usare questa risorsa per:

* Passare alla sottoscrizione per esaminare le informazioni di fatturazione
* Ottenere l'ID tenant del tenant Azure AD B2C in formato GUID
* Passare al tenant di Azure AD B2C.
* Inviare una richiesta di supporto.
* Spostare la risorsa del tenant di Azure AD B2C in un'altra sottoscrizione o gruppo di risorse di Azure

### <a name="regional-restrictions"></a>Restrizioni a livello di area

Se sono state stabilite restrizioni a livello di area per la creazione di risorse di Azure nella sottoscrizione, tale restrizione potrebbe impedire la creazione della risorsa Azure AD B2C.

Per attenuare questo problema, è possibile ridurre le restrizioni a livello di area.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Sottoscrizioni di Azure Cloud Solution Provider (CSP)

Le sottoscrizioni di Azure Cloud Solution Provider (CSP) sono supportate in Azure AD B2C. La funzionalità è disponibile tramite le API o il portale di Azure per Azure AD B2C e per tutte le risorse di Azure. Gli amministratori della sottoscrizione CSP possono collegare, spostare ed eliminare relazioni con Azure AD B2C come fatto con altre risorse di Azure.

La gestione di Azure AD B2C tramite il controllo degli accessi in base al ruolo non è influenzata dall'associazione tra il tenant di Azure AD B2C e una sottoscrizione di Azure CSP. Il controllo degli accessi in base al ruolo è ottenibile usando ruoli basati su tenant, non ruoli basati su sottoscrizioni.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Modificare la sottoscrizione per la fatturazione di Azure AD B2C tenant

### <a name="move-using-azure-resource-manager"></a>Sposta usando Azure Resource Manager

Azure AD B2C tenant possono essere spostati in un'altra sottoscrizione usando Azure Resource Manager se le sottoscrizioni di origine e di destinazione sono presenti nello stesso tenant Azure Active Directory.

Per informazioni su come spostare risorse di Azure come il tenant di Azure AD B2C in un'altra sottoscrizione, vedere [spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Prima di iniziare lo spostamento, assicurarsi di leggere l'intero articolo per comprendere completamente le limitazioni e i requisiti per tale spostamento. Oltre alle istruzioni per lo spostamento delle risorse, include informazioni critiche come un elenco di controllo di pre-spostamento e come convalidare l'operazione di spostamento.

### <a name="move-by-unlinking-and-relinking"></a>Spostamento mediante lo scollegamento e il ricollegamento

Se le sottoscrizioni di origine e di destinazione sono associate a tenant Azure Active Directory diversi, non è possibile eseguire lo spostamento tramite Azure Resource Manager come spiegato in precedenza. Tuttavia, è comunque possibile ottenere lo stesso risultato scollegando il tenant Azure AD B2C dalla sottoscrizione di origine e ricollegarlo alla sottoscrizione di destinazione. Questo metodo è sicuro perché l'unico oggetto che si elimina è il *collegamento di fatturazione*, non il tenant Azure ad B2C stesso. Nessuno degli utenti, delle app, dei flussi utente e così via sarà interessato.

1. Nella directory Azure AD B2C, [invitare un utente Guest](user-overview.md#guest-user) dal tenant Azure ad di destinazione (quello a cui è collegata la sottoscrizione di Azure di destinazione) e assicurarsi che l'utente disponga del ruolo di **amministratore globale** in Azure ad B2C.
1. Passare alla *risorsa di Azure* che rappresenta Azure ad B2C nella sottoscrizione di Azure di origine, come illustrato nella sezione gestire le risorse del [tenant del Azure ad B2C](#manage-your-azure-ad-b2c-tenant-resources) . Non passare al tenant Azure AD B2C effettivo.
1. Selezionare il pulsante **Elimina** nella pagina **Panoramica** . Questa operazione *non* comporta l'eliminazione degli utenti o delle applicazioni del tenant di Azure ad B2C correlati. Rimuove semplicemente il collegamento di fatturazione dalla sottoscrizione di origine.
1. Accedere al portale di Azure con l'account utente che è stato aggiunto come amministratore in Azure AD B2C nel passaggio 1. Passare quindi alla sottoscrizione di Azure di destinazione collegata al tenant di Azure Active Directory di destinazione. 
1. Ristabilire il collegamento di fatturazione nella sottoscrizione di destinazione attenendosi alla procedura [creare il collegamento](#create-the-link) precedente.
1. La risorsa Azure AD B2C ora è stata spostata nella sottoscrizione di Azure di destinazione (collegata al Azure Active Directory di destinazione) e verrà fatturata tramite questa sottoscrizione da ora in poi.

## <a name="next-steps"></a>Passaggi successivi

Per le informazioni più aggiornate sui prezzi, vedere [Azure Active Directory B2C prezzi](https://azure.microsoft.com/pricing/details/active-directory-b2c/).