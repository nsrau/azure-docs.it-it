---
title: Visualizzare e gestire i provider di servizi
description: I clienti possono usare la pagina Provider di servizi nel portale di Azure per visualizzare informazioni sui provider di servizi, sulle offerte di provider di servizi e sulle risorse delegate.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 0a7d39d95e35e30a16ce11db5b942024b6890438
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286604"
---
# <a name="view-and-manage-service-providers"></a>Visualizzare e gestire i provider di servizi

I clienti possono usare la pagina **Provider di servizi** nel [portale di Azure](https://portal.azure.com) per visualizzare informazioni sui provider di servizi e sulle offerte di provider di servizi, delegare risorse specifiche tramite la [gestione risorse delegate di Azure](../concepts/azure-delegated-resource-management.md) e acquistare offerte aggiuntive dei provider di servizi. Nonostante qui si faccia riferimento ai provider di servizi e ai clienti, le aziende che gestiscono più tenant possono usare lo stesso processo per consolidare l'esperienza di gestione.

Per accedere alla pagina **Provider di servizi** nel portale di Azure, il cliente può selezionare **Tutti i servizi**, quindi cercare **Provider di servizi** e selezionarlo. Può trovarlo anche immettendo "Provider di servizi" nella casella di ricerca nella parte superiore del portale di Azure.

Tenere presente che la pagina **Provider di servizi** mostra solo le informazioni sui provider di servizi che hanno accesso alle sottoscrizioni o ai gruppi di risorse del cliente tramite la gestione risorse delegate di Azure. Se un cliente lavora con provider di servizi aggiuntivi che non usano la gestione risorse delegate di Azure per accedere alle risorse del cliente, le informazioni su tali provider di servizi non vengono visualizzate qui.

> [!NOTE]
> I provider di servizi possono visualizzare le informazioni sui clienti passando a **Clienti personali** nel portale di Azure. Per altre informazioni, vedere [Visualizzare e gestire clienti e risorse delegate](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Visualizzare i dettagli dei provider di servizi

Un cliente, per visualizzare le informazioni sui provider di servizi con cui collabora, può selezionare **Provider offers** (Offerte di provider) sul lato sinistro della pagina **Provider di servizi**.

Per ogni offerta di un provider di servizi, il cliente vedrà il nome del provider di servizi e l'offerta associata, con il nome immesso dal cliente durante il processo di onboarding.

Nella colonna **Deleghe** il cliente visualizza quante sottoscrizioni e/o gruppi di risorse sono stati delegati al provider di servizi per tale offerta. Il provider di servizi potrà accedere a queste sottoscrizioni e/o gruppi di risorse e gestirli a seconda dei livelli di accesso specificati nell'offerta.

## <a name="delegate-resources"></a>Risorse delegate

Prima che un provider di servizi possa accedere alle risorse di un cliente e gestirle, le risorse devono essere delegate. Se un cliente ha accettato un'offerta, ma non ha ancora delegato alcuna risorsa, vedrà una nota nella parte superiore della sezione **Provider offers** (Offerte di provider). In questo modo, il cliente saprà che è necessario intervenire prima che il provider di servizi possa accedere a qualsiasi risorsa del cliente.

Per delegare sottoscrizioni o gruppi di risorse:

1. Selezionare la casella relativa alla riga contenente il provider di servizi, l'offerta e il nome. Selezionare quindi **Delega risorse** nella parte superiore della schermata.
1. Nella sezione **Dettagli offerta** della pagina **Delega risorse** esaminare i dettagli del provider di servizi e dell'offerta. Per esaminare le assegnazioni di ruolo per l'offerta, selezionare **Fare clic qui per visualizzare i dettagli dell'offerta selezionata**.
1. Nella sezione **Delega** selezionare **Delega sottoscrizioni** o **Delega gruppi di risorse**.
1. Scegliere le sottoscrizioni e/o i gruppi di risorse che si vuole delegare per questa offerta, quindi selezionare **Aggiungi**.
1. Selezionare la casella di controllo nella parte inferiore della pagina per confermare che si vuole concedere a questo provider di servizi l'accesso alle risorse selezionate, quindi selezionare **Delega**.

## <a name="add-or-remove-service-provider-offers"></a>Aggiungere o rimuovere le offerte di provider di servizi

Un cliente può aggiungere una nuova offerta di provider di servizi dalla pagina **Provider offers** (Offerte di provider) selezionando **Aggiungi offerta**. Il provider di servizi deve avere pubblicato un'offerta per questo cliente. Il cliente può quindi selezionare tale offerta dalla schermata **Offerte private** e infine selezionare **Crea**.

Se il cliente vuole rimuovere un'offerta di provider di servizi, può selezionare l'icona del cestino nella riga relativa all'offerta. Dopo la conferma dell'eliminazione, il provider di servizi non avrà più accesso alle risorse del cliente precedentemente delegate per l'offerta.

## <a name="view-delegations"></a>Visualizzare le deleghe

Le deleghe rappresentano le assegnazioni di ruolo che concedono al provider di servizi le autorizzazioni per le risorse delegate da un cliente. Per visualizzare queste informazioni, selezionare **Deleghe** sul lato sinistro della pagina **Provider di servizi**.

I filtri nella parte superiore della pagina consentono di ordinare e raggruppare le informazioni sulle deleghe o filtrare in base a clienti, offerte o parole chiave specifiche.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Azure Lighthouse](../overview.md).
- Per informazioni su come i provider di servizi possono [visualizzare e gestire i clienti](view-manage-customers.md), passare a **Clienti personali** nel portale di Azure.