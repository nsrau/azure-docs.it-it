---
title: Visualizzare e gestire i provider di servizi
description: I clienti possono usare la pagina Provider di servizi nel portale di Azure per visualizzare informazioni sui provider di servizi, sulle offerte di provider di servizi e sulle risorse delegate.
ms.date: 10/12/2020
ms.topic: how-to
ms.openlocfilehash: e94a9fd6562a9fa8bc0f2d84cce477e7907a1e48
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371104"
---
# <a name="view-and-manage-service-providers"></a>Visualizzare e gestire i provider di servizi

I clienti possono usare la pagina dei **provider di servizi** nella [portale di Azure](https://portal.azure.com) per visualizzare i dettagli relativi a provider di servizi e offerte di provider di servizi, delegare risorse specifiche al [Faro di Azure](../overview.md)e acquistare nuove offerte di provider di servizi.

> [!TIP]
> Sebbene si faccia riferimento ai provider di servizi e ai clienti, le [aziende che gestiscono più tenant](../concepts/enterprise.md) possono usare lo stesso processo per consolidare l'esperienza di gestione.

Per accedere alla pagina **Provider di servizi** nel portale di Azure, il cliente può selezionare **Tutti i servizi**, quindi cercare **Provider di servizi** e selezionarlo. Possono anche trovarlo immettendo "Service Providers" o "Azure Lighthouse" nella casella di ricerca nella parte superiore della portale di Azure.

> [!NOTE]
> Per visualizzare la pagina dei **provider di servizi** , un utente nel tenant del cliente deve avere il [ruolo predefinito Reader](../../role-based-access-control/built-in-roles.md#reader) (o un altro ruolo predefinito che include l'accesso in lettura).
>
> Per aggiungere o aggiornare offerte, delegare risorse e rimuovere offerte, l'utente deve disporre del [ruolo predefinito proprietario](../../role-based-access-control/built-in-roles.md#owner) per la sottoscrizione.

Tenere presente che la pagina **provider di servizi** Mostra solo le informazioni sui provider di servizi che hanno accesso alle sottoscrizioni o ai gruppi di risorse del cliente tramite Azure Lighthouse. Se un cliente lavora con provider di servizi aggiuntivi che non usano Azure Lighthouse per accedere alle risorse del cliente, le informazioni su tali provider di servizi non vengono visualizzate qui.

> [!TIP]
> I provider di servizi possono visualizzare le informazioni sui clienti passando ai **clienti** nella portale di Azure. Per altre informazioni, vedere [Visualizzare e gestire clienti e risorse delegate](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Visualizzare i dettagli dei provider di servizi

Per visualizzare i dettagli sui provider di servizi, il cliente può selezionare le **offerte del provider di servizi** sul lato sinistro della pagina provider di **Servizi** .

Per ogni offerta di un provider di servizi, il cliente vedrà il nome del provider di servizi e l'offerta associata, con il nome immesso dal cliente durante il processo di onboarding.

Nella colonna **Deleghe** il cliente visualizza quante sottoscrizioni e/o gruppi di risorse sono stati delegati al provider di servizi per tale offerta. Il provider di servizi potrà accedere a queste sottoscrizioni e/o gruppi di risorse e gestirli a seconda dei livelli di accesso specificati nell'offerta.

## <a name="add-or-remove-service-provider-offers"></a>Aggiungere o rimuovere le offerte di provider di servizi

Un cliente può aggiungere una nuova offerta del provider di servizi dalla pagina delle **offerte del provider di servizi** selezionando **Aggiungi offerta**. Il provider di servizi deve avere pubblicato un'offerta per questo cliente. Il cliente può quindi selezionare tale offerta dalla schermata **Offerte private** e infine selezionare **Crea**.

Se il cliente vuole rimuovere un'offerta di provider di servizi, può selezionare l'icona del cestino nella riga relativa all'offerta. Dopo la conferma dell'eliminazione, il provider di servizi non avrà più accesso alle risorse del cliente precedentemente delegate per l'offerta.

## <a name="delegate-resources"></a>Delegare le risorse

Prima che un provider di servizi possa accedere alle risorse di un cliente e gestirle, le risorse devono essere delegate. Se un cliente ha accettato un'offerta ma non ha ancora delegato alcuna risorsa, vedrà una nota nella parte superiore della sezione delle offerte del **provider di servizi** . In questo modo, il cliente saprà che è necessario intervenire prima che il provider di servizi possa accedere a qualsiasi risorsa del cliente.

Per delegare sottoscrizioni o gruppi di risorse:

1. Selezionare la casella relativa alla riga contenente il provider di servizi, l'offerta e il nome. Selezionare quindi **Delega risorse** nella parte superiore della schermata.
1. Nella sezione **Dettagli offerta** della pagina **Delega risorse** esaminare i dettagli del provider di servizi e dell'offerta. Per esaminare le assegnazioni di ruolo per l'offerta, selezionare **Fare clic qui per visualizzare i dettagli dell'offerta selezionata**.
1. Nella sezione **Delega** selezionare **Delega sottoscrizioni** o **Delega gruppi di risorse**.
1. Scegliere le sottoscrizioni e/o i gruppi di risorse che si vuole delegare per questa offerta, quindi selezionare **Aggiungi**.
1. Selezionare la casella di controllo nella parte inferiore della pagina per confermare che si vuole concedere a questo provider di servizi l'accesso alle risorse selezionate, quindi selezionare **Delega**.

## <a name="update-service-provider-offers"></a>Aggiornare le offerte dei provider di servizi

Dopo che un cliente ha aggiunto un'offerta, un provider di servizi può pubblicare una versione aggiornata della stessa offerta in Azure Marketplace. Ad esempio, potrebbe voler aggiungere una nuova definizione di ruolo. Se è stata pubblicata una nuova versione dell'offerta, nella pagina **offerte del provider di servizi** viene visualizzata un'icona "Aggiorna" nella riga relativa all'offerta. Il cliente può selezionare questa icona per visualizzare le differenze tra la versione corrente e la nuova versione dell'offerta.

 ![Icona dell'offerta di aggiornamento](../media/update-offer.jpg)

Una volta controllate le modifiche, il cliente può scegliere di eseguire l'aggiornamento alla nuova versione. Al termine dell'operazione, le autorizzazioni e le altre impostazioni specificate nella nuova versione verranno applicate a tutte le sottoscrizioni e/o ai gruppi di risorse delegati per l'offerta.

## <a name="view-delegations"></a>Visualizzare le deleghe

Le deleghe rappresentano le assegnazioni di ruolo che concedono al provider di servizi le autorizzazioni per le risorse delegate da un cliente. Per visualizzare queste informazioni, selezionare **Deleghe** sul lato sinistro della pagina **Provider di servizi**.

I filtri nella parte superiore della pagina consentono di ordinare e raggruppare le informazioni di delega. È anche possibile filtrare in base a clienti, offerte o parole chiave specifiche.

> [!NOTE]
> I clienti non visualizzeranno queste assegnazioni di ruolo o tutti gli utenti del tenant del provider di servizi a cui sono stati concessi questi ruoli, quando [visualizzano le assegnazioni di ruolo per l'ambito delegato nel portale di Azure](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) o tramite le API.

## <a name="audit-delegations-in-your-environment"></a>Delega di controllo nell'ambiente in uso

I clienti potrebbero voler ottenere visibilità nelle sottoscrizioni e/o nei gruppi di risorse delegati al Faro di Azure. Questa operazione è particolarmente utile per i clienti con un numero elevato di sottoscrizioni o con molti utenti che eseguono attività di gestione.

Viene fornita una [definizione dei criteri incorporata in criteri di Azure](../../governance/policy/samples/built-in-policies.md#lighthouse) per [controllare la delega degli ambiti a un tenant di gestione](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). È possibile assegnare questo criterio a un gruppo di gestione che include tutte le sottoscrizioni che si desidera controllare. Quando si verifica la conformità con questi criteri, tutte le sottoscrizioni delegate e/o i gruppi di risorse (all'interno del gruppo di gestione a cui viene assegnato il criterio) verranno visualizzati in uno stato non conforme. È quindi possibile esaminare i risultati e verificare che non esistano deleghe impreviste.

Un'altra [definizione di criteri predefinita](../../governance/policy/samples/built-in-policies.md#lighthouse) consente [di limitare le delega a specifici tenant di gestione](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json). Questo criterio può essere applicato in modo analogo a un gruppo di gestione che include tutte le sottoscrizioni per le quali si desidera limitare le deleghe. Dopo la distribuzione del criterio, qualsiasi tentativo di delegare una sottoscrizione a un tenant esterno a quelli specificati verrà negato.

Per altre informazioni su come assegnare un criterio e visualizzare i risultati dello stato di conformità, vedere [Guida introduttiva: creare un'assegnazione di criteri](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Azure Lighthouse](../overview.md).
- Informazioni su come i provider di servizi possono [visualizzare e gestire i clienti](view-manage-customers.md) nella pagina **clienti** del portale di Azure.
