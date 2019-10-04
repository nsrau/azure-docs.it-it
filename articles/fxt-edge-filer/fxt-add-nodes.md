---
title: Configurazione del cluster Microsoft Azure FXT Edge Filer - Aggiungere nodi
description: Come aggiungere nodi alla cache di archiviazione di Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: d84b98b4ab936bbb6978144eb2e89b5e19df7069
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543241"
---
# <a name="tutorial-add-cluster-nodes"></a>Esercitazione: Aggiungere nodi del cluster 

Un nuovo cluster Azure FXT Edge Filer viene creato con un solo nodo. È possibile aggiungere almeno altri due nodi e abilitare la disponibilità elevata prima di eseguire altre operazioni di configurazione. 

Questa esercitazione descrive come aggiungere nodi del cluster e abilitare la funzionalità di disponibilità elevata. 

In questa esercitazione si apprenderà: 

> [!div class="checklist"]
> * Come aggiungere nodi al cluster FXT
> * Come abilitare la disponibilità elevata

Il completamento dei passaggi di questa esercitazione richiede 45 minuti.

Prima di iniziare questa esercitazione, accendere i nodi che si vuole aggiungere e [impostarne le password iniziali](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. Caricare la pagina Cluster Nodes (Nodi del cluster)

Aprire il pannello di controllo del cluster in un Web browser e accedere come amministratore. Le istruzioni dettagliate sono disponibili nell'articolo introduttivo, in [Aprire le pagine delle impostazioni](fxt-cluster-create.md#open-the-settings-pages).

Il pannello di controllo mostra la scheda **Dashboard** quando viene aperto. 

![Scheda Dashboard nel pannello di controllo (prima scheda)](media/fxt-cluster-config/dashboard-1-node.png)

Questa immagine mostra il dashboard di un cluster appena creato, con un unico nodo.

## <a name="2-locate-the-node-to-add"></a>2. Individuare il nodo da aggiungere

Per aggiungere nodi, fare clic sulla scheda **Settings** (Impostazioni) e scegliere la pagina **FXT Nodes** (Nodi FXT) nella sezione **Cluster**.

![Scheda Settings (Impostazioni) del pannello di controllo (seconda scheda) con la pagina Cluster > FXT Nodes (Nodi FXT) caricata](media/fxt-cluster-config/settings-fxt-nodes.png)

L'elenco **FXT Nodes - Unjoined** (Nodi FXT - Non aggiunti) mostra tutti i nodi FXT non assegnati. La maggior parte dei data center ne ha pochi. Individuare i nodi FXT che si vuole aggiungere al cluster.

> [!Tip] 
> Se non è possibile trovare il nodo desiderato nell'elenco **Unjoined** (Non aggiunti), verificare che soddisfi questi requisiti:
> 
> * Il nodo è acceso e associato a una [password radice impostata](fxt-node-password.md).
> * Il nodo è connesso a una rete cui è possibile accedere. Se si usano VLAN, il nodo deve trovarsi nella stessa VLAN del cluster.
> * Il nodo può essere rilevato con il protocollo Bonjour. 
>
>   Alcune impostazioni del firewall bloccano le porte TCP/UDP usate da Bonjour, impedendo al sistema operativo di FXT di rilevare automaticamente i nodi.
> 
> Se il nodo che si vuole aggiungere non è presente nell'elenco, provare queste soluzioni: 
> 
> * Fare clic sul pulsante **Manual Discover** (Individuazione manuale) per trovarlo in base all'indirizzo IP.
> 
> * Assegnare manualmente indirizzi IP temporanei. Benché usata raramente, questa operazione può essere necessaria se si usano VLAN con tag e i nodi non si trovano nella rete corretta oppure se la rete non permette l'assegnazione automatica degli indirizzi IP. Seguire le istruzioni nella versione precedente di questo documento per [impostare manualmente un indirizzo IP statico](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

Nell'elenco vengono visualizzati nome del nodo, indirizzo IP, versione del software e stato di idoneità. In genere, la colonna **Status** (Stato) indica "Wants to join" (Pronto per l'aggiunta) o descrive un problema hardware o di sistema che rende il nodo non idoneo per l'aggiunta al cluster.

La colonna **Actions** (Azioni) contiene pulsanti che permettono di aggiungere il nodo al cluster o di aggiornarne il software. Il pulsante di aggiornamento installa automaticamente la versione del software corrispondente ai nodi già presenti nel cluster.

Tutti i nodi in un cluster devono usare la stessa versione del sistema operativo, ma non è necessario aggiornare il software prima di aggiungere un nodo. Dopo aver fatto clic sul pulsante **Allow to join** (Consenti aggiunta), il processo di aggiunta al cluster controlla e installa automaticamente il software del sistema operativo corrispondente alla versione nel cluster.

Per altre informazioni sulle opzioni di questa pagina, vedere [ **Cluster** > **FXT Nodes (Nodi FXT)** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) nella guida alla configurazione del cluster.

## <a name="3-click-the-allow-to-join-button"></a>3. Fare clic sul pulsante "Allow to join" (Consenti aggiunta) 

Fare clic sul pulsante **Allow to join** (Consenti aggiunta) nella colonna **Actions** (Azioni) per il nodo che si vuole aggiungere.

Dopo aver fatto clic sul pulsante, lo stato del nodo può cambiare a causa dell'aggiornamento del software in preparazione all'aggiunta al cluster. 

L'immagine seguente mostra un nodo in fase di aggiunta al cluster. Probabilmente sta ricevendo un aggiornamento del sistema operativo prima di venire aggiunto. Per i nodi in fase di aggiunta al cluster non viene visualizzato alcun pulsante nella colonna **Actions** (Azioni).

![Riga della tabella dei nodi, che mostra il nome del nodo, l'indirizzo IP, la versione del software, il messaggio "Allowed to join" (Aggiunta consentita) e un'ultima colonna vuota](media/fxt-cluster-config/node-join-in-process.png)

Dopo qualche istante, il nuovo nodo dovrebbe essere visualizzato nell'elenco dei nodi del cluster nella parte superiore alla pagina delle impostazioni **FXT Nodes** (Nodi FXT). 

Ripetere questo processo per aggiungere altri nodi al cluster. Non è necessario attendere il completamento dell'aggiunta di un nodo al cluster prima di iniziare ad aggiungerne un altro.

## <a name="enable-high-availability"></a>Abilitare la disponibilità elevata

Dopo aver aggiunto un secondo nodo al cluster, è possibile che venga visualizzato un messaggio di avviso nella scheda Dashboard del pannello di controllo che indica che la funzionalità di disponibilità elevata non è configurata. 

La disponibilità elevata permette ai nodi del cluster di compensare gli uni per gli altri se uno non è più attivo. La disponibilità elevata non è abilitata per impostazione predefinita.

![Scheda Dashboard con il messaggio "The cluster has more than one node, but HA is not enabled" (Il cluster contiene più di un nodo, ma la disponibilità elevata non è abilitata) nella tabella Conditions (Condizioni)](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Non abilitare la disponibilità elevata finché il cluster non contiene almeno tre nodi.

Seguire questa procedura per attivare la disponibilità elevata: 

1. Caricare la pagina **High Availability** (Disponibilità elevata) nella sezione **Cluster** della scheda **Settings** (Impostazioni).

   ![Pagina di configurazione della disponibilità elevata (Cluster > High Availability, Disponibilità elevata) La casella "Enable HA" (Abilita disponibilità elevata) si trova nella parte superiore, mentre il pulsante di invio è nella parte inferiore.](media/fxt-cluster-config/enable-ha.png)

2. Fare clic sulla casella **Enable HA** (Abilita disponibilità elevata) e quindi sul pulsante **Submit** (Invia). 

Viene visualizzato un avviso in **Dashboard** che conferma l'abilitazione della disponibilità elevata.

![Scheda Dashboard che mostra il messaggio "HA is now fully configured" (La disponibilità elevata è completamente configurata)](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Passaggi successivi

Dopo aver aggiunto tutti i nodi nel cluster, continuare configurando l'archiviazione a lungo termine del cluster.

> [!div class="nextstepaction"]
> [Aggiungere archiviazione back-end e configurare lo spazio dei nomi virtuale](fxt-add-storage.md)