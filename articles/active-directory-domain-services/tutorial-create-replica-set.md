---
title: 'Esercitazione: Creare un set di repliche in Azure AD Domain Services | Microsoft Docs'
description: Informazioni su come creare e usare set di repliche nel portale di Azure ai fini della resilienza del servizio con Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: iainfou
ms.openlocfilehash: 69bb61012082404dfd6488b5e0606e5966c2fcef
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504652"
---
# <a name="tutorial-create-and-use-replica-sets-for-resiliency-or-geolocation-in-azure-active-directory-domain-services-preview"></a>Esercitazione: Creare e usare set di repliche per la resilienza o la georilevazione in Azure Active Directory Domain Services (anteprima)

Per migliorare la resilienza di un dominio gestito di Azure Active Directory Domain Services (Azure AD DS) o eseguire la distribuzione in località geografiche aggiuntive vicine alle applicazioni, è possibile usare *set di repliche*. Ogni spazio dei nomi di dominio gestito di Azure AD DS, ad esempio *aaddscontoso.com*, contiene un set di repliche iniziale. La possibilità di creare set di repliche aggiuntivi in altre aree di Azure supporta la resilienza geografica di un dominio gestito.

È possibile aggiungere un set di repliche a qualsiasi rete virtuale con peering in qualsiasi area di Azure che supporti Azure AD DS.

I set di repliche sono una funzionalità in anteprima pubblica di Azure AD Domain Services. Tenere presenti le differenze esistenti a livello di supporto per le funzionalità ancora in anteprima. Per altre informazioni sulle anteprime, vedere il [contratto di servizio per le anteprime di Azure Active Directory](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Comprendere i requisiti delle reti virtuali
> * Creare un set di repliche
> * Eliminare un set di repliche

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services creato con set di repliche e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un dominio gestito di Azure Active Directory Domain Services][tutorial-create-instance].

    > [!IMPORTANT]
    > Assicurarsi di creare un dominio gestito che usa set di repliche. Un dominio gestito esistente creato prima di questa anteprima non supporta i set di repliche. Per il dominio gestito sarà inoltre necessario usare almeno uno SKU *Enterprise*. Se necessario, [cambiare lo SKU di un dominio gestito][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

In questa esercitazione si creano e si configurano set di repliche usando il portale di Azure. Per iniziare, accedere prima di tutto al [portale di Azure](https://portal.azure.com).

## <a name="networking-considerations"></a>Considerazioni sulla rete

Le reti virtuali che ospitano set di repliche devono poter comunicare tra loro. La connettività di rete alle reti virtuali che ospitano i set di repliche è necessaria anche per le applicazioni e i servizi che dipendono da Azure AD DS. Per creare una rete di peer completa, sarà necessario configurare il peering di reti virtuali di Azure tra tutte le reti virtuali. I peering consentono una replica intrasito efficace tra i set di repliche.

Per poter usare i set di repliche in Azure AD DS, esaminare prima di tutto i requisiti delle reti virtuali di Azure riportati di seguito:

* Evitare la sovrapposizione degli spazi indirizzi IP per consentire il routing e il peering di reti virtuali.
* Creare subnet con un numero di indirizzi IP sufficiente per supportare lo scenario.
* Assicurarsi che Azure AD DS abbia una propria subnet. Non condividere la subnet della rete virtuale con i servizi e le VM delle applicazioni.
* Le reti virtuali con peering NON sono transitive.

> [!TIP]
> Quando si crea un set di repliche nel portale di Azure, vengono creati automaticamente i peering di rete tra le reti virtuali.
>
> Se necessario, è possibile creare una rete virtuale e una subnet quando si aggiunge un set di repliche nel portale di Azure. In alternativa, è possibile scegliere risorse rete virtuale esistenti nell'area di destinazione per un set di repliche e consentire la creazione automatica dei peering, se non sono già presenti.

## <a name="create-a-replica-set"></a>Creare un set di repliche

Quando si crea un dominio gestito, ad esempio *aaddscontoso.com*, viene creato un set di repliche iniziale. I set di repliche aggiuntivi condividono lo stesso spazio dei nomi e la stessa configurazione. Le modifiche apportate ad Azure AD DS, ad esempio alla configurazione, a identità utente e credenziali, ai gruppi, agli oggetti Criteri di gruppo e agli oggetti computer e le modifiche di altro tipo, vengono applicate a tutti i set di repliche nel dominio gestito con la replica di AD DS.

In questa esercitazione si crea un set di repliche aggiuntivo in un'area di Azure diversa rispetto al set di repliche iniziale di Azure AD DS.

Per creare un set di repliche aggiuntivo, seguire questa procedura:

1. Nel portale di Azure cercare e selezionare **Azure AD Domain Services**.
1. Scegliere il dominio gestito, ad esempio *aaddscontoso.com*.
1. Sul lato sinistro selezionare **Replica sets (preview)** (Set di repliche - anteprima). Ogni dominio gestito include un set di repliche iniziale nell'area selezionata, come illustrato nello screenshot di esempio seguente:

    ![Screenshot di esempio per la visualizzazione e l'aggiunta di un set di repliche nel portale di Azure](./media/tutorial-create-replica-set/replica-set-list.png)

    Per creare un set di repliche aggiuntivo, selezionare **+ Aggiungi**.

1. Nella finestra *Add a replica set* (Aggiungi set di repliche) selezionare l'area di destinazione, ad esempio *Stati Uniti orientali*.

    Selezionare una rete virtuale nell'area di destinazione, ad esempio *vnet-eastus*, e quindi scegliere una subnet, ad esempio *aadds-subnet*. Se necessario, scegliere **Crea nuovo** per aggiungere una rete virtuale nell'area di destinazione e quindi **Gestisci** per creare una subnet per Azure AD DS.

    Se non sono già presenti, vengono creati automaticamente i peering di reti virtuali di Azure tra la rete virtuale del dominio gestito esistente e la rete virtuale di destinazione.

    Lo screenshot di esempio seguente illustra il processo di creazione di un nuovo set di repliche in *Stati Uniti orientali*:

    ![Screenshot di esempio per la creazione di un set di repliche nel portale di Azure](./media/tutorial-create-replica-set/create-replica-set.png)

1. Al termine, selezionare **Salva**.

Il processo di creazione del set di repliche richiede tempo, perché devono essere create le risorse nell'area di destinazione. Il dominio gestito viene quindi replicato con la replica di AD DS.

Mentre è in corso la distribuzione, il set di repliche viene visualizzato con lo stato *Provisioning*, come illustrato nello screenshot di esempio seguente. Al termine, il set di repliche è riportato come *ln esecuzione*.

![Screenshot di esempio dello stato di distribuzione del set di repliche nel portale di Azure](./media/tutorial-create-replica-set/replica-set-provisioning.png)

## <a name="delete-a-replica-set"></a>Eliminare un set di repliche

Un dominio gestito è attualmente limitato a quattro repliche: il set di repliche iniziale e tre set di repliche aggiuntivi. Se un set di repliche non è più necessario o se ne vuole creare uno in un'altra area, si possono eliminare i set di repliche superflui.

> [!IMPORTANT]
> Non è possibile eliminare l'ultimo set di repliche in un dominio gestito.

Per eliminare un set di repliche, seguire questa procedura:

1. Nel portale di Azure cercare e selezionare **Azure AD Domain Services**.
1. Scegliere il dominio gestito, ad esempio *aaddscontoso.com*.
1. Sul lato sinistro selezionare **Replica sets (preview)** (Set di repliche - anteprima). Nell'elenco dei set di repliche selezionare il menu di scelta rapida **...** accanto al set di repliche da eliminare.
1. Scegliere **Elimina** dal menu di scelta rapida e quindi confermare che si vuole eliminare il set di repliche.

> [!NOTE]
> L'eliminazione del set di repliche può richiedere molto tempo.

Se la rete virtuale o il peering usato dal set di repliche non è più necessario, è anche possibile eliminare tali risorse. Prima di eliminare le connessioni di rete, verificare che non siano necessarie per altre risorse delle applicazioni nell'altra area.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Configurare il peering reti virtuali
> * Creare un set di repliche in un'altra area geografica
> * Eliminare un set di repliche

Per altre informazioni concettuali, vedere il funzionamento dei set di repliche in Azure AD DS.

> [!div class="nextstepaction"]
> [Concetti e funzionalità dei set di repliche][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
