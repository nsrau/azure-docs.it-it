---
title: Modello di fatturazione per Azure Active Directory B2C
description: Informazioni sul modello di fatturazione degli utenti attivi mensili (MAU) di Azure AD B2C e su come abilitare la fatturazione per una sottoscrizione di Azure specifica.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30ecb1e6faa29482a8d69dd1d08e4f127f515596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190008"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Modello di fatturazione per Azure Active Directory B2C

L'utilizzo di Azure Active Directory B2C (Azure AD B2C) viene fatturato a una sottoscrizione di Azure collegata e usa un modello di fatturazione degli utenti attivi (MAU) mensile. Informazioni su come collegare una risorsa B2C di Azure AD a una sottoscrizione e sul funzionamento del modello di fatturazione MAU nelle sezioni seguenti.

> [!IMPORTANT]
> Questo articolo non contiene informazioni sui prezzi. Per informazioni aggiornate sulla fatturazione e sui prezzi dell'utilizzo, vedere [Prezzi B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)di Azure Active Directory.

## <a name="monthly-active-users-mau-billing"></a>Fatturazione mensile degli utenti attivi (MAU)

La fatturazione B2C di Azure AD viene misurata in base al conteggio di utenti univoci con attività di autenticazione all'interno di un mese di calendario, noto come fatturazione mensile degli utenti attivi (MAU).

A partire dal **01 novembre 2019,** a tutti i tenant di Azure AD B2C appena creati vengono fatturati per ogni utente attivo mensile (MAU). Ai tenant esistenti [collegati a una sottoscrizione](#link-an-azure-ad-b2c-tenant-to-a-subscription) in data o dopo il 01 novembre 2019 verranno fatturati gli utenti attivi al mese (MAU).

Se si dispone di un tenant B2C di Azure AD esistente collegato a una sottoscrizione precedente al 01 novembre 2019, è possibile scegliere di eseguire una delle operazioni seguenti:If you have an existing Azure AD B2C tenant that was linked to a subscription prior to 01 November 2019, you can choose to do one of the following:

* Eseguire l'aggiornamento al modello di fatturazione mensile degli utenti attivi (MAU), oppure
* Resta il modello di fatturazione per autenticazione

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Aggiornamento al modello di fatturazione degli utenti attivi mensili

I proprietari delle sottoscrizioni di Azure che dispongono dell'accesso amministrativo alla risorsa B2C di Azure AD possono passare al modello di fatturazione MAU. Le opzioni di fatturazione sono configurate nella risorsa B2C di Azure AD.

Il passaggio alla fatturazione mensile degli utenti attivi (MAU) è **irreversibile.** Dopo aver convertito una risorsa B2C di Azure AD nel modello di fatturazione basato su MAU, non è possibile ripristinare tale risorsa al modello di fatturazione per autenticazione.

Ecco come passare alla fatturazione MAU per una risorsa B2C di Azure AD esistente:Here's how to make the switch to MAU billing for an existing Azure AD B2C resource:

1. Accedere al [portale](https://portal.azure.com) di Azure come proprietario della sottoscrizione.
1. Nel menu superiore, selezionare il filtro **Directory e sottoscrizione** e quindi selezionare la directory B2C di Azure AD che si desidera aggiornare alla fatturazione MAU.<br/>
    ![Filtro di directory e sottoscrizione nel portale di AzureDirectory and subscription filter in Azure portal](./media/billing/portal-mau-01-select-b2c-directory.png)
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Nella pagina **Panoramica** del tenant B2C di Azure AD selezionare il collegamento in **Nome risorsa.** Si viene indirizzati alla risorsa B2C di Azure AD nel tenant di Azure AD.<br/>
    ![Collegamento alla risorsa B2C di Azure AD evidenziato nel portale di AzureAzure AD B2C resource link highlighted in Azure portal](./media/billing/portal-mau-02-b2c-resource-link.png)
1. Nella pagina **Panoramica** della risorsa B2C di Azure AD, in **Unità fatturabili,** selezionare il collegamento **Per autenticazione (Cambia in MAU).**<br/>
    ![Collegamento Cambia a MAU evidenziato nel portale di AzureChange to MAU link highlighted in Azure portal](./media/billing/portal-mau-03-change-to-mau-link.png)
1. Selezionare **Conferma** per completare l'aggiornamento alla fatturazione MAU.<br/>
    ![Finestra di dialogo di conferma della fatturazione basata su MAU nel portale di AzureMA-based billing confirmation dialog in Azure portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Cosa aspettarsi quando si passa alla fatturazione MAU dalla fatturazione per autenticazione

La misurazione basata su MAU viene abilitata non appena l'utente, il proprietario della sottoscrizione/risorsa, conferma la modifica. La fattura mensile rifletterà le unità di autenticazione fatturate fino alla modifica e le nuove unità di MAU a partire dalla modifica.

Gli utenti non vengono conteggiati due volte durante il mese di transizione. Agli utenti attivi univoci che eseguono l'autenticazione prima della modifica viene addebitata una tariffa per autenticazione in un mese di calendario. Gli stessi utenti non sono inclusi nel calcolo MAU per il resto del ciclo di fatturazione della sottoscrizione. Ad esempio:

* Il tenant B2C di Contoso ha 1.000 utenti. 250 utenti sono attivi in un dato mese. L'amministratore della sottoscrizione passa da per autenticazione a utenti attivi mensili (MAU) il 10 del mese.
* La fatturazione per il 1st-10th viene fatturata utilizzando il modello per autenticazione.
  * Se 100 utenti accedono durante questo periodo (1st-10th), tali utenti vengono contrassegnati come *pagati per il mese.*
* La fatturazione dal 10 (il momento effettivo della transizione) viene fatturata alla tariffa MAU.
  * Se altri 150 utenti accedono durante questo periodo (dal 10 al 30), vengono fatturati solo i 150 aggiuntivi.
  * L'attività continua dei primi 100 utenti non influisce sulla fatturazione per il resto del mese di calendario.

Durante il periodo di fatturazione della transizione, è probabile che il proprietario della sottoscrizione visualizzi le voci per entrambi i metodi (per autenticazione e per MAU) nell'istruzione di fatturazione della sottoscrizione di Azure:During the billing period of the transition, the subscription owner will likely see entries for both methods (per-authentication and per-MAU) appear in their Azure subscription billing statement:

* Voce per l'utilizzo fino alla data/ora della modifica che riflette la per autenticazione.
* Voce per l'utilizzo dopo la modifica che riflette gli utenti attivi mensili (MAU).

Per le informazioni più recenti sulla fatturazione all'utilizzo e sui prezzi per Azure AD B2C, vedere [Prezzi B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)di Azure Active Directory.

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Collegare un tenant B2C di Azure AD a una sottoscrizioneLink an Azure AD B2C tenant to a subscription

I costi di utilizzo per Azure Active Directory B2C (Azure AD B2C) vengono fatturati a una sottoscrizione di Azure.Usage charges for Azure Active Directory B2C (Azure AD B2C) are billed to an Azure subscription. Quando viene creato un tenant di Azure AD B2C, l'amministratore del tenant deve collegarlo in modo esplicito a una sottoscrizione di Azure.

Il collegamento di sottoscrizione viene ottenuto creando una *risorsa* B2C di Azure AD all'interno della sottoscrizione di Azure di destinazione. È possibile creare diverse risorse B2C di Azure AD in una singola sottoscrizione di Azure, insieme ad altre risorse di Azure, ad esempio macchine virtuali, account di archiviazione e app per la logica. È possibile visualizzare tutte le risorse all'interno di una sottoscrizione passando al tenant di Azure Active Directory (Azure AD) a cui è associata la sottoscrizione.

Una sottoscrizione collegata a un tenant B2C di Azure AD può essere usata per la fatturazione dell'utilizzo di Azure AD B2C o di altre risorse di Azure, incluse risorse B2C di Azure AD aggiuntive. La sottoscrizione non può essere usata per aggiungere altri servizi basati su licenza di Azure o licenze di Office 365 nel tenant di Azure AD B2C.

### <a name="prerequisites"></a>Prerequisiti

* [Sottoscrizione di AzureAzure subscription](https://azure.microsoft.com/free/)
* [Tenant B2C](tutorial-create-tenant.md) di Azure AD che si vuole collegare a una sottoscrizioneAzure AD B2C tenant that you want to link to a subscription
  * È necessario essere un amministratore tenant
  * Il tenant non deve essere già collegato a una sottoscrizione

### <a name="create-the-link"></a>Creare il collegamento

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare il filtro **Directory e sottoscrizione** nel menu superiore e quindi selezionare la directory che contiene la sottoscrizione di Azure che si vuole usare *(non* la directory contenente il tenant B2C di Azure AD).
1. Selezionare Crea una `Active Directory B2C` **risorsa**, immettere nel campo Cerca **marketplace** e quindi selezionare Azure Active **Directory B2C**.
1. Selezionare **Crea**
1. Selezionare **Collega un tenant di Azure AD B2C esistente alla sottoscrizione**di Azure.
1. Selezionare un **tenant B2C di Azure AD** dall'elenco a discesa. Vengono visualizzati solo i tenant per i quali si è un amministratore globale e che non sono già collegati a una sottoscrizione. Il campo **Nome risorsa B2C** di Azure AD viene popolato con il nome di dominio del tenant B2C di Azure AD selezionato.
1. Selezionare **una sottoscrizione** di Azure attiva di cui si è amministratori.
1. In **Gruppo di**risorse selezionare Crea **nuovo**e quindi specificare il percorso del gruppo **di risorse**. Le impostazioni del gruppo di risorse qui non hanno alcun impatto sulla posizione del tenant di Azure AD B2C, sulle prestazioni o sullo stato di fatturazione.
1. Selezionare **Crea**.
    ![Pagina di creazione delle risorse B2C di Azure AD nel portale di AzureThe Azure AD B2C Resource creation page in Azure portal](./media/billing/portal-01-create-b2c-resource-page.png)

Dopo aver completato questi passaggi per un tenant di Azure AD B2C, la sottoscrizione di Azure viene fatturata in base ai dettagli del contratto Enterprise ODirect o Azure, se applicabile.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gestire le risorse del tenant di Azure AD B2C

Dopo aver creato la risorsa B2C di Azure AD in una sottoscrizione di Azure, verrà visualizzata una nuova risorsa di tipo "Tenant B2C" con le altre risorse di Azure.After you create the Azure AD B2C resource in an Azure subscription, you should see a new resource of the type "B2C tenant" appear with your other Azure resources.

È possibile usare questa risorsa per:

* Passare all'abbonamento per esaminare le informazioni di fatturazione
* Ottenere l'ID tenant del tenant B2C di Azure AD nel formato GUIDGet the Azure AD B2C tenant's tenant ID in GUID format
* Passare al tenant di Azure AD B2C.
* Inviare una richiesta di supporto.
* Spostare la risorsa tenant di Azure AD B2C in un'altra sottoscrizione o gruppo di risorse di AzureMove your Azure AD B2C tenant resource to another Azure subscription or resource group

![Pagina Impostazioni risorsa B2C nel portale di AzureB2C Resource settings page in the Azure portal](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Restrizioni regionali

Se sono stati stabilite restrizioni regionali per la creazione di risorse di Azure nella sottoscrizione, tale restrizione potrebbe impedire la creazione della risorsa B2C di Azure AD.

Per risolvere questo problema, ridurre le restrizioni regionali.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Sottoscrizioni di Azure Cloud Solution Provider (CSP)Azure Cloud Solution Providers (CSP) subscriptions

Le sottoscrizioni di Azure Cloud Solution Provider (CSP) sono supportate in Azure AD B2C. La funzionalità è disponibile tramite le API o il portale di Azure per Azure AD B2C e per tutte le risorse di Azure. Gli amministratori della sottoscrizione CSP possono collegare, spostare ed eliminare relazioni con Azure AD B2C come fatto con altre risorse di Azure.CSP subscription administrators can link, move, and delete relationships with Azure AD B2C as done with other Azure resources.

La gestione di Azure AD B2C tramite il controllo degli accessi in base al ruolo non è influenzata dall'associazione tra il tenant di Azure AD B2C e una sottoscrizione di Azure CSP. Il controllo degli accessi in base al ruolo viene ottenuto utilizzando ruoli basati su tenant, non ruoli basati su sottoscrizione.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Modificare la sottoscrizione di fatturazione tenant di Azure AD B2CChange the Azure AD B2C tenant billing subscription

I tenant di Azure AD B2C possono essere spostati in un'altra sottoscrizione se le sottoscrizioni di origine e di destinazione esistono all'interno dello stesso tenant di Azure Active Directory.Azure AD B2C tenants can be moved to another subscription if the source and destination subscriptions exist within the same Azure Active Directory tenant.

Per informazioni su come spostare le risorse di Azure, ad esempio il tenant di Azure AD B2C, in un'altra sottoscrizione, vedere Spostare le risorse in un nuovo gruppo di [risorse o sottoscrizione.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

Prima di iniziare lo spostamento, leggere l'intero articolo per comprendere appieno le limitazioni e i requisiti per tale spostamento. Oltre alle istruzioni per lo spostamento delle risorse, include informazioni critiche come un elenco di controllo pre-spostamento e come convalidare l'operazione di spostamento.

## <a name="next-steps"></a>Passaggi successivi

Per le informazioni più recenti sui prezzi, vedere [Prezzi B2C di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
