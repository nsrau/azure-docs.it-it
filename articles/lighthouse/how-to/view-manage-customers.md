---
title: Visualizzare e gestire clienti e risorse delegate
description: I provider di servizi che usano la gestione risorse delegate di Azure possono visualizzare tutte le sottoscrizioni e le risorse dei clienti delegate passando a Clienti personali nel portale di Azure.
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: 0d4b3187066754e8a549f029623762df539b30b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543427"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Visualizzare e gestire clienti e risorse delegate

I provider di servizi che usano la [gestione risorse delegate di Azure](../concepts/azure-delegated-resource-management.md) possono usare la pagina **Clienti personali** nel [portale di Azure](https://portal.azure.com) per visualizzare le sottoscrizioni e le risorse dei clienti delegate. Nonostante qui si faccia riferimento ai provider di servizi e ai clienti, le aziende che gestiscono più tenant possono usare lo stesso processo per consolidare l'esperienza di gestione.

Per accedere alla pagina **Clienti personali** nel portale di Azure, selezionare **Tutti i servizi**, quindi cercare **Clienti personali** e selezionarla. È possibile trovarla anche immettendo "Clienti personali" nella casella di ricerca nella parte superiore del portale di Azure.

Tieni presente che la sezione **Clienti** superiore della pagina **I miei clienti** mostra solo le informazioni sui clienti che hanno delegato sottoscrizioni o gruppi di risorse. Se si lavora con altri clienti (ad esempio tramite il programma Cloud Solution Provider , non verranno visualizzate informazioni su tali clienti nella sezione Clienti a meno che non siano state create operazioni di onboarding delle risorse per la gestione delle risorse delegate di Azure.If you work with other customers (such as through the [Cloud Solution Provider program](https://docs.microsoft.com/partner-center/csp-overview), you won not see details about those customers in the **Customers** section unless you have onboarded their resources onboarded resources for Azure delegated resource management.

Più in basso nella pagina, una sezione separata **denominata Cloud Solution Provider (anteprima)** mostra le informazioni di fatturazione e le risorse per i clienti CSP che hanno [firmato il Contratto clienti Microsoft (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) e sono [sotto il piano di Azure](https://docs.microsoft.com/partner-center/azure-plan-get-started). Per altre informazioni, vedere [Introduzione all'account di fatturazione del Contratto Microsoft Partner](../../billing/mpa-overview.md). Si noti che tali clienti CSP vengono visualizzati in questa sezione indipendentemente dal fatto che siano stati caricati o meno anche per la gestione delle risorse delegate di Azure.Note that such CSP customers appear in this section whether or not you have also onboarded them for Azure delegated resource management. Analogamente, un cliente CSP non deve essere visualizzato nella sezione Cloud Solution Provider (Preview) di Clienti personali per poterli eseguire l'onboarding per la gestione delle risorse delegate di Azure.Similarly, a CSP customer does not have to appear in the **Cloud Solution Provider (Preview)** section of My **customers** in order to you to onboard them for Azure delegated resource management.

> [!NOTE]
> I clienti possono visualizzare le informazioni sui provider di servizi passando a **Provider di servizi** nel portale di Azure. Per altre informazioni, vedere [Visualizzare e gestire i provider di servizi](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Visualizzare e gestire i dettagli dei clienti

Per visualizzare i dettagli dei clienti, selezionare **Clienti** sul lato sinistro della pagina **Clienti personali**.

Per ogni cliente, verranno visualizzati il nome del cliente, l'ID del cliente (ID del tenant) e l'offerta associata all'engagement. Nella colonna **Deleghe** saranno visualizzati il numero di sottoscrizioni delegate e/o il numero di gruppi di risorse delegati.

> [!IMPORTANT]
> Per visualizzare una delega, è necessario che agli utenti sia stato concesso il ruolo [Lettore](../../role-based-access-control/built-in-roles.md#reader) (o un altro ruolo predefinito che include l'accesso in lettura) nel processo di onboarding.

I filtri nella parte superiore della pagina consentono di ordinare e raggruppare le informazioni sui clienti o filtrare in base a clienti, offerte o parole chiave specifiche.

Da questa pagina è possibile visualizzare le informazioni seguenti:

- Per visualizzare tutte le sottoscrizioni, le offerte e le deleghe associate a un cliente, selezionare il nome del cliente.
- Per visualizzare altri dettagli su un'offerta e le relative deleghe, selezionare il nome dell'offerta.
- Per visualizzare altri dettagli sulle assegnazioni di ruolo per le sottoscrizioni o i gruppi di risorse delegati, selezionare la voce nella colonna **Deleghe**.

## <a name="view-and-manage-delegations"></a>Visualizzare e gestire le deleghe

Le deleghe mostrano la sottoscrizione o il gruppo di risorse che è stato delegato, insieme agli utenti e alle autorizzazioni che possono accedervi. Per visualizzare queste informazioni, selezionare **Deleghe** sul lato sinistro della pagina **Clienti personali**.

I filtri nella parte superiore della pagina consentono di ordinare e raggruppare le informazioni sulle assegnazioni di accesso o filtrare in base a clienti, offerte o parole chiave specifiche.

### <a name="view-role-assignments"></a>Visualizzare le assegnazioni di ruolo

Gli utenti e le autorizzazioni associati a ogni delega vengono visualizzati nella colonna **Assegnazioni di ruoli**. È possibile selezionare ogni voce per visualizzare l'elenco completo di utenti, gruppi ed entità servizio a cui è stato concesso l'accesso alla sottoscrizione o al gruppo di risorse. Da qui è possibile selezionare un utente, un gruppo o un nome dell'entità servizio specifico per ottenere altri dettagli.

### <a name="remove-delegations"></a>Rimuovere le deleghe

Se sono stati inclusi utenti con il ruolo di eliminazione [assegnazione registrazione servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) durante l'onboarding di un cliente per la gestione delle risorse delegate di Azure, tali utenti possono rimuovere una delega selezionando l'icona del cestino visualizzata nella riga della delega. In questo caso, nessun utente nel tenant del provider di servizi sarà in grado di accedere alle risorse delegate in precedenza.


## <a name="work-in-the-context-of-a-delegated-subscription"></a>Lavorare nel contesto di una sottoscrizione delegata

È possibile lavorare direttamente nel contesto di una sottoscrizione delegata all'interno del portale di Azure, senza cambiare la directory che si sta usando. A tale scopo, procedere come indicato di seguito:

1. Selezionare l'icona **Directory e sottoscrizione** nella parte superiore del portale di Azure.
2. Nel filtro **sottoscrizioni globali** verificare che sia selezionata solo la casella di tale sottoscrizione delegata. È possibile usare la casella a discesa **Directory corrente e delegate** per mostrare solo le sottoscrizioni in una directory specifica. Non usare l'opzione **Cambia directory** perché cambierebbe la directory a cui è stato eseguito l'accesso.

Se quindi si accede a un servizio che supporta le [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md), il servizio userà per impostazione predefinita il contesto della sottoscrizione delegata selezionata. Per modificare questa impostazione, seguire i passaggi precedenti e selezionare la casella **Seleziona tutto** (o scegliere una o più sottoscrizioni da usare in alternativa).

> [!NOTE]
> Se si è ottenuto l'accesso a uno o più gruppi di risorse, invece dell'accesso a un'intera sottoscrizione, è possibile selezionare la sottoscrizione a cui appartiene il gruppo di risorse. Sarà quindi possibile lavorare nel contesto di tale sottoscrizione, ma si potrà accedere solo ai gruppi di risorse designati.

È anche possibile accedere alle funzionalità correlate a sottoscrizioni o gruppi di risorse delegati dall'interno di servizi che supportano esperienze di gestione tra tenant selezionando la sottoscrizione o il gruppo di risorse da tale servizio.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md).
- Informazioni su come i clienti possono [visualizzare e gestire i provider di servizi](view-manage-service-providers.md) passando a **Provider di servizi** nel portale di Azure.
