---
title: Esercitazione - Abilitare l'integrazione di reti virtuali e firewall in Hub eventi di Azure | Microsoft Docs
description: Questa esercitazione illustra come integrare Hub eventi di Azure con reti virtuali e firewall per abilitare l'accesso sicuro.
services: event-hubs-messaging
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 11/28/2018
ms.topic: tutorial
ms.service: event-hubs-messaging
ms.custom: mvc
ms.openlocfilehash: e47c2af353a7f365534ba4cf9ce574c16a0ea4ca
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233140"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>Esercitazione: abilitare l'integrazione di reti virtuali e firewall nello spazio dei nomi di Hub eventi di Azure

Gli [endpoint del servizio Rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) estendono lo spazio di indirizzi privato della rete virtuale e l'identità della rete virtuale ai servizi di Azure tramite una connessione diretta. Gli endpoint consentono di associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali. Il traffico che transita dalla rete virtuale al servizio di Azure rimane sempre nella rete backbone di Microsoft Azure.

I firewall consentono di limitare l'accesso allo spazio dei nomi di Hub eventi di Azure da specifici indirizzi IP (o intervalli di indirizzi IP)

Questa esercitazione illustra come integrare gli endpoint del servizio Rete virtuale e configurare i firewall (applicazione di filtri IP) con lo spazio dei nomi di Hub eventi di Azure esistente, tramite il portale.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Integrare gli endpoint del servizio Rete virtuale con lo spazio dei nomi di Hub eventi di Azure.
> * Configurare i firewall (applicazione di filtri IP) con lo spazio dei nomi di Hub eventi di Azure.

>[!WARNING]
> L'implementazione dell'integrazione delle reti virtuali può impedire l'interazione da parte di altri servizi Azure con l'Hub eventi di Azure.
>
> Le integrazioni dirette non sono supportate quando le reti virtuali sono abilitate.
> Scenari comuni di Azure che non funzionano con reti virtuali:
> * Diagnostica e registrazione di Azure
> * Analisi di flusso di Azure
> * Integrazione della Griglia di eventi
> * Le funzioni e app web sono necessarie in una rete virtuale.
> * Route dell'hub IoT
> * IoT Device Explorer


> [!IMPORTANT]
> Le reti virtuali sono supportate nei livelli **standard** e **dedicato** di Hub eventi. Non sono supportate nel livello Basic.

Se non si dispone di un abbonamento di Azure, creare un [account gratuito][] prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Si sfrutterà uno spazio dei nomi di Hub eventi esistente. Sarà quindi necessario assicurarsi di disporre di uno spazio dei nomi di Hub eventi disponibile. In caso contrario, consultare [questa esercitazione](./event-hubs-create.md)

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Aprire e accedere prima di tutto al [portale di Azure][Azure portal] con la propria sottoscrizione di Azure.

## <a name="select-event-hubs-namespace"></a>Selezionare lo spazio dei nomi di Hub eventi di Azure

Ai fini di questa esercitazione, è stato creato uno spazio dei nomi di Hub eventi apposito da selezionare.

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>Selezionare l'opzione Firewall e reti virtuali

Usare il menu di spostamento nel riquadro sinistro del portale per scegliere l'opzione **Firewall e reti virtuali**.

  ![Passare al menu](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  La prima volta che si visita questa pagina, il pulsante di opzione **Tutte le reti** deve essere selezionato. Questo significa che lo spazio dei nomi di Hub eventi di Azure consente tutte le connessioni in ingresso.

## <a name="add-virtual-network-service-endpoint"></a>Aggiungere endpoint del servizio di rete virtuale

Per limitare l'accesso, è necessario integrare l'endpoint del servizio di Rete virtuale per questo spazio dei nomi di Hub eventi.

1. Fare clic sul pulsante di opzione **Reti selezionate** nella parte superiore della pagina per abilitare il resto della pagina con le opzioni del menu.
  ![reti selezionate](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Nella sezione Rete virtuale della pagina, selezionare l'opzione ***+Aggiungi rete virtuale esistente***. Verrà visualizzato il riquadro che consentirà di selezionare una rete virtuale già creata.
  ![aggiungi rete virtuale esistente](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. Selezionare la rete virtuale dall'elenco e scegliere una subnet.
   ![seleziona una subnet](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. È necessario abilitare l'endpoint di servizio prima di aggiungere la rete virtuale all'elenco. Se l'endpoint di servizio non è abilitato, il portale richiederà di abilitarlo.
  ![seleziona subnet e abilita endpoint](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > Se non si è in grado di attivare l'endpoint di servizio, è possibile ignorare quello mancante usando il modello ARM. Questa funzionalità non è disponibile sul portale.

5. Dopo aver abilitato l'endpoint di servizio nella subnet selezionata, si può procedere ad aggiungerla all'elenco delle reti virtuali consentite.
  ![aggiunta della subnet dopo l'abilitazione dell'endpoint](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. A questo punto, premere il pulsante **Salva** sulla barra multifunzione in alto per salvare la configurazione della rete virtuale nel servizio. Attendere alcuni minuti prima di visualizzare la conferma nelle notifiche del portale.

## <a name="add-firewall-for-specified-ip"></a>Aggiungere un firewall per l'IP specificato

È possibile limitare l'accesso allo spazio dei nomi di Hub eventi per un limitato intervallo di indirizzi IP o per un indirizzo IP specifico usando le regole del firewall.

1. Fare clic sul pulsante di opzione **Reti selezionate** nella parte superiore della pagina per abilitare il resto della pagina con le opzioni del menu.
  ![reti selezionate](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Nella sezione **Firewall**, nella griglia ***Intervallo di indirizzi***, è possibile aggiungere uno o più indirizzi IP o intervalli di indirizzi IP specifici.
  ![aggiungi indirizzi IP](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. Dopo aver aggiunto più indirizzi IP (o intervalli di indirizzi IP), premere **Salva** sulla barra multifunzione in alto per assicurarsi di salvare la configurazione sul lato servizio. Attendere alcuni minuti prima di visualizzare la conferma nelle notifiche del portale.
  ![aggiungi indirizzi IP e premi Salva](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>Aggiungere l'indirizzo IP corrente alle regole del firewall

1. È anche possibile aggiungere il proprio indirizzo IP corrente in pochi istanti controllando la casella di spunta ***Aggiungi indirizzo IP del client (IL TUO INDIRIZZO IP CORRENTE)*** sopra la griglia ***Intervallo di indirizzi***.
  ![aggiunta dell'indirizzo IP corrente](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. Dopo aver aggiunto il proprio indirizzo IP alle regole del firewall, premere **Salva** sulla barra multifunzione in alto per assicurarsi di salvare la configurazione sul lato servizio. Attendere alcuni minuti prima di visualizzare la conferma nelle notifiche del portale.
  ![aggiungi indirizzo IP corrente e premi Salva](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>Conclusioni

In questa esercitazione, gli endpoint della rete virtuale e le regole del firewall sono stati integrati con uno spazio dei nomi di Hub eventi di Azure esistente. L'esercitazione ha illustrato come:
> [!div class="checklist"]
> * Integrare gli endpoint del servizio Rete virtuale con lo spazio dei nomi di Hub eventi di Azure.
> * Configurare i firewall (applicazione di filtri IP) con lo spazio dei nomi di Hub eventi di Azure.


[Azure portal]: https://portal.azure.com/
