---
title: Visualizzare e gestire clienti e risorse delegate
description: Come provider di servizi che usa Azure Lighthouse, è possibile visualizzare tutte le risorse e le sottoscrizioni dei clienti delegate visitando i clienti nella portale di Azure.
ms.date: 07/06/2020
ms.topic: how-to
ms.openlocfilehash: f509a91d71caa3ab1ec8282f4151556f7c10d52c
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86133272"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Visualizzare e gestire clienti e risorse delegate

I provider di servizi che usano [Azure Lighthouse](../overview.md) possono usare la pagina **clienti** nella [portale di Azure](https://portal.azure.com) per visualizzare le risorse e le sottoscrizioni delegate del cliente. Nonostante qui si faccia riferimento ai provider di servizi e ai clienti, le aziende che gestiscono più tenant possono usare lo stesso processo per consolidare l'esperienza di gestione.

Per accedere alla pagina **Clienti personali** nel portale di Azure, selezionare **Tutti i servizi**, quindi cercare **Clienti personali** e selezionarla. È possibile trovarla anche immettendo "Clienti personali" nella casella di ricerca nella parte superiore del portale di Azure.

Tenere presente che la sezione **clienti** principali della pagina **clienti personali** Mostra solo le informazioni sui clienti che hanno sottoscrizioni delegate o gruppi di risorse. Se si lavora con altri clienti (ad esempio tramite il [programma Cloud Solution Provider](/partner-center/csp-overview)), non verranno visualizzate informazioni sui clienti nella sezione **Customers** , a meno che non siano state caricate le [risorse in Azure Lighthouse](onboard-customer.md).

Nella parte inferiore della pagina, una sezione separata denominata **Cloud Solution Provider (anteprima)** Mostra le informazioni e le risorse di fatturazione per i clienti CSP che hanno [firmato il contratto](/partner-center/confirm-customer-agreement) per i clienti Microsoft e si trovano nel [piano Azure](/partner-center/azure-plan-get-started). Per altre informazioni, vedere [Introduzione all'account di fatturazione del Contratto Microsoft Partner](../../cost-management-billing/understand/mpa-overview.md). Si noti che questi clienti CSP vengono visualizzati in questa sezione indipendentemente dal fatto che siano stati caricati anche in Azure Lighthouse. Analogamente, non è necessario che un cliente CSP venga visualizzato nella sezione del **provider di soluzioni cloud (anteprima)** dei **clienti** per caricarli nel Faro di Azure.

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

## <a name="view-and-manage-delegations"></a>Visualizzazione e gestione delle deleghe

Le deleghe mostrano la sottoscrizione o il gruppo di risorse che è stato delegato, insieme agli utenti e alle autorizzazioni che possono accedervi. Per visualizzare queste informazioni, selezionare **Deleghe** sul lato sinistro della pagina **Clienti personali**.

I filtri nella parte superiore della pagina consentono di ordinare e raggruppare le informazioni sulle assegnazioni di accesso o filtrare in base a clienti, offerte o parole chiave specifiche.

### <a name="view-role-assignments"></a>Visualizzare le assegnazioni di ruolo

Gli utenti e le autorizzazioni associati a ogni delega vengono visualizzati nella colonna **Assegnazioni di ruoli**. È possibile selezionare ogni voce per visualizzare l'elenco completo di utenti, gruppi ed entità servizio a cui è stato concesso l'accesso alla sottoscrizione o al gruppo di risorse. Da qui è possibile selezionare un utente, un gruppo o un nome dell'entità servizio specifico per ottenere altri dettagli.

### <a name="remove-delegations"></a>Rimuovi delega

Se sono stati inclusi utenti con il [ruolo di eliminazione della registrazione dei servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) durante l'onboarding di un cliente in Azure Lighthouse, gli utenti possono rimuovere una delega selezionando l'icona del cestino che viene visualizzata nella riga relativa alla delega. In tal caso, nessun utente nel tenant del provider di servizi sarà in grado di accedere alle risorse precedentemente Delegate.

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
