---
title: Come usare la funzionalità IP pubblico nella soluzione VMware di Azure
description: Questo articolo illustra come usare la funzionalità IP pubblico nella rete WAN virtuale di Azure.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 63475b478a951632c068b168353acf2e0bb7061c
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490390"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-vmware-solution"></a>Come usare la funzionalità IP pubblico nella soluzione VMware di Azure

IP pubblico è una nuova funzionalità di connettività della soluzione VMware di Azure. Consente di rendere accessibili tramite una rete pubblica risorse quali server Web, macchine virtuali (VM) e host. 

L'accesso a Internet pubblico viene abilitato in due modi. 

- Le applicazioni possono essere ospitate e pubblicate sotto il servizio di bilanciamento del carico del gateway applicazione per il traffico HTTP/HTTPS.
- Pubblicato tramite funzionalità IP pubbliche nella rete WAN virtuale di Azure.

Come parte della distribuzione del cloud privato della soluzione VMware di Azure, quando si Abilita la funzionalità IP pubblico vengono creati e abilitati i componenti necessari con l'automazione:

-  Rete WAN virtuale

-  Hub WAN virtuale con connettività ExpressRoute

-  Servizi firewall di Azure con IP pubblico

Questo articolo illustra in dettaglio come usare la funzionalità IP pubblico nella rete WAN virtuale.

## <a name="prerequisites"></a>Prerequisiti

- Ambiente della soluzione VMware di Azure
- Un server Web in esecuzione nell'ambiente della soluzione VMware di Azure.
- Un nuovo intervallo IP non sovrapposto per la distribuzione dell'hub WAN virtuale, in genere un `/24` .

## <a name="reference-architecture"></a>Architettura di riferimento

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="Diagramma dell'architettura IP pubblico" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

Il diagramma dell'architettura Mostra un server Web del cliente ospitato nell'ambiente della soluzione VMware di Azure e configurato con indirizzi IP privati RFC1918.  Questo servizio Web viene reso disponibile a Internet tramite la funzionalità IP pubblico WAN virtuale.  L'IP pubblico è in genere un NAT di destinazione convertito nel firewall di Azure. Con le regole di DNAT, il criterio firewall converte le richieste di indirizzi IP pubblici in un indirizzo privato (server Web) con una porta.

Le richieste utente hanno raggiunto il firewall su un IP pubblico che, a sua volta, viene convertito in un indirizzo IP privato usando le regole DNAT nel firewall di Azure. Il firewall controlla la tabella NAT e, se la richiesta corrisponde a una voce, trasmette il traffico all'indirizzo e alla porta convertiti nell'ambiente della soluzione VMware di Azure.

Il server Web riceve la richiesta e risponde con le informazioni o la pagina richiesta al firewall, quindi il firewall inoltra le informazioni all'utente nell'indirizzo IP pubblico.

## <a name="test-case"></a>Test case
In questo scenario, è necessario pubblicare il server Web IIS su Internet. Usare la funzionalità IP pubblico della soluzione VMware di Azure per pubblicare il sito Web in un indirizzo IP pubblico.  Si configureranno le regole NAT nel firewall e si accederà alla risorsa della soluzione VMware di Azure (VM con webserver) con IP pubblico.

## <a name="deploy-virtual-wan"></a>Distribuire una rete WAN virtuale

1. Accedere al portale di Azure, quindi cercare e selezionare **soluzione VMware di Azure**.

1. Selezionare il cloud privato della soluzione VMware di Azure.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Screenshot del cloud privato della soluzione VMware di Azure." border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. In **Gestisci** selezionare **connettività**.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="Screenshot della sezione connettività." border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. Selezionare la scheda **IP pubblico** , quindi selezionare **Configura**.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="Screenshot che mostra dove iniziare a configurare l'IP pubblico" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. Accettare i valori predefiniti o modificarli, quindi selezionare **Crea**.

   - Gruppo di risorse Wide Area Network virtuale

   - Nome Wide Area Network virtuale

   - Blocco di indirizzi dell'hub virtuale (usando un nuovo intervallo IP non sovrapposto)

   - Numero di indirizzi IP pubblici (1-100)

Il completamento della distribuzione di tutti i componenti richiede circa un'ora. Questa distribuzione deve essere eseguita una sola volta per supportare tutti gli indirizzi IP pubblici futuri per questo ambiente della soluzione VMware di Azure.  

>[!TIP]
>È possibile monitorare lo stato dall'area di **notifica** . 

## <a name="view-and-add-public-ip-addresses"></a>Visualizzare e aggiungere indirizzi IP pubblici

È possibile controllare e aggiungere altri indirizzi IP pubblici attenendosi alla procedura riportata di seguito.

1. Nella portale di Azure cercare e selezionare **Firewall**.

1. Selezionare un firewall distribuito e quindi selezionare **visitare gestione firewall di Azure per configurare e gestire il firewall**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Screenshot che mostra l'opzione per la configurazione e la gestione del firewall" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Selezionare **hub virtuale protetto** e selezionare un hub virtuale nell'elenco.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="Screenshot di gestione firewall" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. Nella pagina hub virtuale selezionare **configurazione IP pubblico** e aggiungere un altro indirizzo IP pubblico, quindi selezionare **Aggiungi**. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Screenshot dell'aggiunta di una configurazione IP pubblico in Firewall Manager" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. Specificare il numero di indirizzi IP necessari e selezionare **Aggiungi**.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="Screenshot per aggiungere un numero specificato di configurazioni IP pubbliche" border="true":::


## <a name="create-firewall-policies"></a>Creare criteri firewall

Una volta distribuiti tutti i componenti, è possibile visualizzarli nel gruppo di risorse aggiunto. Il passaggio successivo consiste nell'aggiungere un criterio del firewall.

1. Nella portale di Azure cercare e selezionare **Firewall**.

1. Selezionare un firewall distribuito e quindi selezionare **visitare gestione firewall di Azure per configurare e gestire il firewall**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Screenshot che mostra l'opzione per la configurazione e la gestione del firewall" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Selezionare **criteri del firewall di Azure** e quindi selezionare **Crea criterio del firewall di Azure**.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="Screenshot della creazione di un criterio firewall in Firewall Manager" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. Nella scheda informazioni di **base** specificare i dettagli richiesti e selezionare **Avanti: impostazioni DNS**. 

1. Nella scheda **DNS** selezionare **Disabilita** e quindi selezionare **Avanti: regole**.

1. Selezionare **Aggiungi raccolta regole** , fornire i dettagli seguenti e selezionare **Aggiungi** e quindi selezionare **Avanti: Intelligence** per le minacce.

   -  Nome
   -  Tipo di raccolta Rules-DNAT
   -  Priorità
   -  Azione raccolta regole-Consenti
   -  Nome della regola
   -  Tipo di origine- **IPAddress**
   -  Origine-* *\** _
   -  Protocollo-_ *TCP**
   -  Porta di destinazione- **80**
   -  Tipo di destinazione- **indirizzo IP**
   -  Destinazione- **indirizzo IP pubblico**
   -  Indirizzo tradotto: **indirizzo IP privato del server Web della soluzione VMware di Azure**
   -  Porta tradotta- **porta del server Web della soluzione VMware di Azure**

1. Lasciare il valore predefinito e quindi fare clic su **Next (hub** ).

1. Selezionare **associa hub virtuale**.

1. Selezionare un Hub nell'elenco e selezionare **Aggiungi**.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="Screenshot che Mostra gli hub selezionati che verranno convertiti in hub virtuali Scecured." border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. Selezionare **Avanti: Tag**. 

1. Opzionale Creare coppie di nome e valore per categorizzare le risorse. 

1. Selezionare **Avanti: verifica + crea** e quindi selezionare **Crea**.

## <a name="limitations"></a>Limitazioni

È possibile avere 100 indirizzi IP pubblici per ogni SDDCs.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'uso di indirizzi IP pubblici con la [rete WAN virtuale di Azure](../virtual-wan/virtual-wan-about.md).

