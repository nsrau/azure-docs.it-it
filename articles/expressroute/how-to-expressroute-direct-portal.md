---
title: 'Azure ExpressRoute: configurare ExpressRoute Direct: portale'
description: Questa pagina consente di configurare ExpressRoute Direct usando il portale.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 06/12/2020
ms.author: cherylmc
ms.openlocfilehash: f22f81d69d746764af43d9167874aabedbd56f51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84765324"
---
# <a name="create-expressroute-direct-using-the-azure-portal"></a>Creare ExpressRoute direttamente usando il portale di Azure

Questo articolo illustra come creare ExpressRoute Direct usando il portale di Azure.
ExpressRoute Direct ti permette di connetterti direttamente alla rete globale di Microsoft nelle località di peering distribuite in modo strategico in tutto il mondo. Per altre informazioni, vedere [Informazioni su ExpressRoute Direct](expressroute-erdirect-about.md).

## <a name="1-create-expressroute-direct"></a><a name="create-erdir"></a>1. creare ExpressRoute direttamente

1. Nel menu [portale di Azure](https://portal.azure.com) o nella **Home** page selezionare **Crea una risorsa**.

1. Nella pagina **nuovo** , nel campo ***Cerca nel Marketplace*** digitare **ExpressRoute Direct**, quindi premere **invio** per ottenere i risultati della ricerca.

1. Nei risultati selezionare **ExpressRoute Direct**.

1. Nella pagina **ExpressRoute Direct** selezionare **Crea** per aprire la pagina **Crea ExpressRoute diretto** .

1. Per iniziare, completare i campi della pagina **nozioni di base** .

    :::image type="content" source="./media/how-to-expressroute-direct-portal/basics.png" alt-text="Pagina Informazioni di base":::

    * **Sottoscrizione**: la sottoscrizione di Azure che si vuole usare per creare un nuovo ExpressRoute Direct. La risorsa di ExpressRoute Direct e circuiti di ExpressRoute devono essere nella stessa sottoscrizione.
    * **Gruppo di risorse**: il gruppo di risorse di Azure in cui verrà creata la nuova risorsa ExpressRoute Direct. Se non si dispone di un gruppo di risorse esistente, è possibile crearne uno nuovo.
    * **Region**: area pubblica di Azure in cui verrà creata la risorsa.
    * **Nome**: il nome della nuova risorsa ExpressRoute Direct.

1. Completare quindi i campi nella pagina di **configurazione** .

    :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration.png" alt-text="Pagina Configurazione":::

    * **Località di peering**: la località di peering a cui ci si connetterà alla risorsa ExpressRoute Direct. Per ulteriori informazioni sulle località di peering, vedere [ExpressRoute locations](expressroute-locations-providers.md).
   * **Larghezza**di banda: la larghezza di banda della coppia di porte che si vuole riservare. ExpressRoute Direct supporta entrambe le opzioni di larghezza di banda di 10 GB e 100 GB. Se la larghezza di banda desiderata non è disponibile nella località di peering specificata, [aprire una richiesta di supporto nella portale di Azure](https://aka.ms/azsupt).
   * **Incapsulamento**: ExpressRoute Direct supporta sia l'incapsulamento QinQ che Dot1Q.
     * Se si seleziona QinQ, a ogni circuito di ExpressRoute viene assegnato dinamicamente un S-Tag e il circuito è univoco per tutta la risorsa di ExpressRoute Direct.
     *  Ogni C-Tag nel circuito deve essere univoco nel circuito, ma non in tutto ExpressRoute Direct.
     * Se si seleziona l'incapsulamento Dot1Q, è necessario gestire l'univocità del C-Tag (VLAN) nell'intera risorsa di ExpressRoute Direct.
     >[!IMPORTANT]
     >ExpressRoute Direct può essere un solo tipo di incapsulamento. L'incapsulamento non può essere modificato dopo la creazione di ExpressRoute Direct.
     >

1. Specificare i tag delle risorse, quindi selezionare **Verifica + crea** per convalidare le impostazioni delle risorse dirette di ExpressRoute.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/validate.png" alt-text="Rivedi e crea":::

1. Selezionare **Crea**. Verrà visualizzato un messaggio che informa che la distribuzione è in corso. Lo stato verrà visualizzato in questa pagina durante la creazione delle risorse. 

## <a name="2-change-admin-state-of-links"></a><a name="state"></a>2. modificare lo stato di amministrazione dei collegamenti

Utilizzare questo processo per condurre un test di livello 1, verificando che ogni Cross Connection sia correttamente trasferito a ciascun router per le porte primaria e secondaria.

1. Nella sezione **collegamenti** della pagina **Panoramica** delle risorse di ExpressRoute Direct selezionare **Link1**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/link.png" alt-text="Collegamento 1" lightbox="./media/how-to-expressroute-direct-portal/link-expand.png":::

1. Attivare o disabilitare l'impostazione **stato amministratore** su **abilitato**, quindi selezionare **Salva**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/state.png" alt-text="Stato amministratore":::

    >[!IMPORTANT]
    >La fatturazione verrà avviata quando lo stato di amministrazione è abilitato su uno o più collegamenti.
    >

1. Ripetere lo stesso processo per **Link2**.

## <a name="3-create-a-circuit"></a><a name="circuit"></a>3. creare un circuito

Per impostazione predefinita, è possibile creare dieci circuiti nella sottoscrizione in cui si trova la risorsa di ExpressRoute Direct. Questo numero può essere aumentato dal supporto. Si è responsabili del monitoraggio della larghezza di banda con provisioning e di quella utilizzata. La larghezza di banda con provisioning è la somma della larghezza di banda di tutti i circuiti sulla risorsa diretta ExpressRoute. La larghezza di banda utilizzata è l'utilizzo fisico delle interfacce fisiche sottostanti.

* Esistono altre larghezze di banda del circuito che è possibile utilizzare in ExpressRoute Direct solo per supportare gli scenari descritti in precedenza. Sono: 40 Gbps e 100 Gbps.

* SkuTier può essere locale, standard o Premium.

* SkuFamily deve essere MeteredData. Illimitata non è supportata in ExpressRoute Direct.

La procedura seguente consente di creare un circuito ExpressRoute dal flusso di lavoro diretto di ExpressRoute. Se invece si preferisce, è anche possibile creare un circuito usando il flusso di lavoro del circuito normale, anche se non vi è alcun vantaggio nell'uso dei passaggi del flusso di lavoro del circuito regolare per questa configurazione. Vedere [creare e modificare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md).

1. Nella sezione **Impostazioni** dirette di ExpressRoute selezionare **circuiti**, quindi selezionare **+ Aggiungi**. 

    :::image type="content" source="./media/how-to-expressroute-direct-portal/add.png" alt-text="Aggiungere" lightbox="./media/how-to-expressroute-direct-portal/add-expand.png":::

1. Configurare le impostazioni nella pagina di **configurazione** .

   :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration2.png" alt-text="Pagina Configurazione":::

1. Specificare i tag delle risorse. Selezionare **Verifica + crea** per convalidare i valori prima di creare la risorsa.

   :::image type="content" source="./media/how-to-expressroute-direct-portal/review.png" alt-text="Rivedi e crea":::

1. Selezionare **Crea**. Verrà visualizzato un messaggio che informa che la distribuzione è in corso. Lo stato verrà visualizzato in questa pagina durante la creazione delle risorse. 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su ExpressRoute Direct, vedere la [Panoramica](expressroute-erdirect-about.md).
