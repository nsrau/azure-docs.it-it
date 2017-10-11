---
title: Risolvere i problemi relativi ai gruppi di sicurezza di rete - Portale | Microsoft Docs
description: Informazioni su come risolvere i problemi dei gruppi di sicurezza di rete nel modello di distribuzione Azure Resource Manager tramite il portale di Azure.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: f01d3b43a7953697a6b03e176dace33448d95cd9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Risolvere i problemi relativi ai gruppi di sicurezza di rete tramite il portale di Azure
> [!div class="op_single_selector"]
> * [Portale di Azure](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Se sono stati configurati gruppi di sicurezza di rete nella macchina virtuale (VM) e si verificano problemi di connettività alla VM, questo articolo offre una panoramica delle funzionalità di diagnostica per i gruppi di sicurezza di rete per risolvere il problema.

I gruppi di sicurezza di rete consentono di controllare i tipi di traffico che scorrono dentro e fuori le macchine virtuali (VM). I gruppi di sicurezza di rete possono essere applicati alle subnet in una rete virtuale di Azure, nelle interfacce di rete o in entrambe. Le regole effettive applicate a un'interfaccia di rete (NIC) sono un'aggregazione delle regole esistenti nei gruppi di sicurezza di rete applicati a un'interfaccia di rete e alla subnet a cui è connessa. Talvolta le regole nei gruppi di sicurezza di rete possono essere in conflitto tra loro e influire sulla connettività di rete della VM.  

È possibile visualizzare tutte le regole di sicurezza effettive dai gruppi di sicurezza di rete, così come vengono applicate nelle interfacce di rete della VM. Questo articolo illustra come risolvere i problemi di connettività delle VM usando queste regole nel modello di distribuzione Azure Resource Manager. Se si ha scarsa dimestichezza con i concetti di rete virtuale e gruppo di sicurezza di rete, vedere gli articoli generali sulle [reti virtuali](virtual-networks-overview.md) e sui [gruppi di sicurezza di rete](virtual-networks-nsg.md).

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Uso di regole di sicurezza effettive per risolvere i problemi di flusso del traffico delle VM
Lo scenario seguente è un esempio di un problema di connessione comune:

Una macchina virtuale denominata *VM1* fa parte di una subnet denominata *Subnet1* in una rete virtuale denominata *WestUS-VNet1*. Un tentativo di connettersi alla VM con RDP su porta TCP 3389 ha esito negativo. I gruppi di sicurezza di rete vengono applicati sia all'interfaccia di rete *VM1-NIC1* che alla subnet *Subnet1*. Il traffico verso la porta TCP 3389 è consentito nel gruppo di sicurezza di rete associato all'interfaccia di rete *VM1 NIC1*, tuttavia il comando ping TCP per VM1 della porta 3389 ha esito negativo.

Sebbene in questo esempio si usi la porta TCP 3389, è possibile attenersi alla procedura seguente per determinare gli errori di connessione in ingresso e in uscita su qualsiasi porta.

### <a name="vm"></a>Visualizzare le regole di sicurezza effettive per una macchina virtuale
Completare i passaggi seguenti per risolvere i problemi dei gruppi di sicurezza di rete per una VM:

È possibile visualizzare l'elenco completo delle regole di sicurezza effettive in un'interfaccia di rete dalla VM stessa. È anche possibile aggiungere, modificare ed eliminare le regole dei gruppi di sicurezza di rete per subnet e interfacce di rete usando il pannello delle regole effettive, se si hanno le autorizzazioni per eseguire queste operazioni.

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Fare clic su **Altri servizi** e quindi su **Macchine virtuali** nell'elenco visualizzato.
3. Selezionare una VM in cui risolvere i problemi nell'elenco visualizzato. Verrà visualizzato un pannello della VM contenente opzioni.
4. Fare clic su **Diagnostica e risoluzione dei problemi** e quindi selezionare un problema comune. Per questo esempio si seleziona **Non è possibile connettersi alla macchina virtuale Windows** . 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)
5. Sotto il problema vengono visualizzati i passaggi, come illustrato nell'immagine seguente: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)
   
    Fare clic su *effective security group rules* (regole effettive del gruppo di sicurezza) nell'elenco dei passaggi consigliati.
6. Verrà visualizzato il pannello **Ottieni regole di sicurezza valide** , come illustrato nell'immagine seguente:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)
   
    Si notino le sezioni seguenti dell'immagine:
   
   * **Ambito:** impostato su *VM1*, ovvero la VM selezionata nel passaggio 3.
   * **Interfaccia di rete:** *VM1-NIC1* . Una VM può avere più interfacce di rete (NIC). Ogni interfaccia di rete può avere regole di sicurezza effettive univoche. Per risolvere il problema può essere necessario visualizzare le regole di sicurezza effettive per ogni interfaccia di rete.
   * **Gruppi di sicurezza di rete associati:** i gruppi di sicurezza di rete possono essere applicati sia all'interfaccia di rete che alla subnet cui è connessa l'interfaccia di rete. Nell'immagine, il gruppo di sicurezza di rete è stato applicato sia all'interfaccia di rete che alla subnet cui è connessa. È possibile fare clic sui nomi dei gruppi di sicurezza di rete per modificare le regole direttamente nei gruppi stessi.
   * **Scheda VM1-nsg:** l'elenco delle regole visualizzate nell'immagine si riferisce al gruppo di sicurezza di rete applicato all'interfaccia di rete. Azure crea diverse regole predefinite quando viene creato un gruppo di sicurezza di rete. Non è possibile rimuovere le regole predefinite, ma è possibile eseguirne l'override con regole di priorità più alta. Per altre informazioni sulle regole predefinite, vedere l'articolo generale sui [gruppi di sicurezza di rete](virtual-networks-nsg.md#default-rules) .
   * **Colonna DESTINAZIONE:** alcune regole hanno testo nella colonna, mentre altre hanno prefissi di indirizzo. Il testo è il nome dei tag predefiniti applicati alla regola di sicurezza al momento della creazione. I tag sono identificatori forniti dal sistema che rappresentano più prefissi. Selezionando una regola con un tag, ad esempio *AllowInternetOutBound*, sarà possibile visualizzare i prefissi nel pannello **Prefissi degli indirizzi** .
   * **Scarica:** l'elenco di regole può essere lungo. È possibile fare clic su **Scarica** e salvare un file CSV delle regole per eseguire l'analisi offline.
   * **AllowRDP** : questa regola consente le connessioni RDP alla VM.
7. Fare clic sulla scheda **Subnet1-NSG** per visualizzare le regole effettive del gruppo di sicurezza di rete applicato alla subnet, come illustrato nell'immagine seguente: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)
   
    Si noti la regola *in ingresso* **denyRDP** . Le regole in ingresso applicate alla subnet vengono valutate prima delle regole applicate all'interfaccia di rete. La regola di accesso negato viene applicata alla subnet, quindi la richiesta di connessione alla porta TCP 3389 ha esito negativo perché la regola di accesso consentito applicata all'interfaccia di rete non viene mai valutata. 
   
    La regola *denyRDP* è il motivo per cui la connessione RDP ha esito negativo. Rimuovere la regola per risolvere il problema.
   
   > [!NOTE]
   > Se la VM associata all'interfaccia di rete non è in esecuzione oppure non sono stati applicati gruppi di sicurezza di rete all'interfaccia di rete o alla subnet, non verranno visualizzate regole.
   > 
   > 
8. Per modificare le regole del gruppo di sicurezza di rete, fare clic su *Subnet1-NSG* nella sezione **Gruppi di sicurezza di rete associati** .
   Verrà visualizzato il pannello **Subnet1-NSG** . È possibile modificare le regole direttamente facendo clic su **Regole di sicurezza in ingresso**.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)
9. Dopo aver rimosso la regola in ingresso *denyRDP* dal gruppo di sicurezza di rete **Subnet1-NSG** e aver aggiunto una regola *allowRDP*, l'elenco delle regole effettive sarà simile all'immagine seguente:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)
   
    Verificare che la porta TCP 3389 sia aperta aprendo una connessione RDP alla VM o usando lo strumento PsPing. Per altre informazioni su PsPing, vedere la [pagina di download di PsPing](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="nic"></a>Visualizzare le regole di sicurezza effettive per un'interfaccia di rete
In caso di problemi con il flusso del traffico della VM in un'interfaccia di rete specifica, è possibile visualizzare un elenco completo delle regole effettive nel contesto dell'interfaccia di rete seguendo questa procedura:

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Fare clic su **Altri servizi** e quindi su **Interfacce di rete** nell'elenco visualizzato.
3. Selezionare un'interfaccia di rete. Nell'immagine seguente è stata selezionata un'interfaccia di rete denominata *VM1-NIC1* .
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)
   
    Si noti che il valore di **Ambito** corrisponde all'interfaccia di rete selezionata. Per informazioni sugli altri dati visualizzati, vedere il passaggio 6 della sezione relativa alla **risoluzione dei problemi dei gruppi di sicurezza di rete per una VM** di questo articolo.
   
   > [!NOTE]
   > Se un gruppo di sicurezza di rete viene rimosso da un'interfaccia di rete, il gruppo di sicurezza di rete della subnet è ancora valido per l'interfaccia di rete specificata. In questo caso, l'output visualizzerà solo le regole del gruppo di sicurezza di rete della subnet. Le regole vengono visualizzate solo se l'interfaccia di rete è collegata a una VM.
   > 
   > 
4. È possibile modificare direttamente le regole per i gruppi di sicurezza di rete associati a un'interfaccia di rete e a una subnet. Per informazioni su questa procedura, vedere il passaggio 8 della sezione **Visualizzare le regole di sicurezza effettive per una macchina virtuale** di questo articolo.

## <a name="nsg"></a>Visualizzare le regole di sicurezza effettive per un gruppo di sicurezza di rete
Quando si modificano le regole del gruppo di sicurezza di rete, può essere opportuno esaminare l'impatto delle regole aggiunte in una determinata VM. È possibile visualizzare l'elenco completo delle regole di sicurezza effettive per tutte le interfacce di rete a cui è applicato un determinato gruppo di sicurezza di rete, restando nel contesto del pannello dello specifico gruppo di sicurezza di rete. Per risolvere i problemi delle regole effettive in un gruppo di sicurezza di rete, seguire questa procedura:

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Fare clic su **Altri servizi** e quindi su **Gruppi di sicurezza di rete** nell'elenco visualizzato.
3. Selezionare un gruppo di sicurezza di rete. Nell'immagine seguente è stato selezionato un gruppo di sicurezza di rete denominato VM1-nsg.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)
   
    Si notino le sezioni seguenti dell'immagine precedente:
   
   * **Ambito:** impostato sul gruppo di sicurezza di rete selezionato.
   * **Macchina virtuale:** quando un gruppo di sicurezza di rete è applicato a una subnet, viene applicato a tutte le interfacce di rete collegate a tutte le VM connesse alla subnet. Questo elenco indica tutte le VM alle quali è applicato questo gruppo di sicurezza di rete. È possibile selezionare una VM qualsiasi dall'elenco.
     
     > [!NOTE]
     > Se un gruppo di sicurezza di rete viene applicato solo a una subnet vuota, le VM non verranno elencate. Se un gruppo di sicurezza di rete viene applicato a un'interfaccia di rete che non è associata a una VM, non verrà elencata neanche quella interfaccia di rete. 
     > 
     > 
   * **Interfaccia di rete:** una macchina virtuale può avere più interfacce di rete. È possibile selezionare un'interfaccia di rete collegata alla VM selezionata.
   * **Gruppi di sicurezza di rete associati:** un'interfaccia di rete può avere sempre fino a due gruppi di sicurezza di rete effettivi, uno applicato all'interfaccia di rete e l'altro alla subnet. Anche se per l'ambito viene selezionato VM1-nsg, l'output visualizzerà entrambi i gruppi di sicurezza di rete se l'interfaccia di rete ha un gruppo di sicurezza di rete effettivo per la subnet.
4. È possibile modificare direttamente le regole per i gruppi di sicurezza di rete associati a un'interfaccia di rete o a una subnet. Per informazioni su questa procedura, vedere il passaggio 8 della sezione **Visualizzare le regole di sicurezza effettive per una macchina virtuale** di questo articolo.

Per informazioni sugli altri dati visualizzati, vedere il passaggio 6 della sezione **Visualizzare le regole di sicurezza effettive per una macchina virtuale** di questo articolo.

> [!NOTE]
> Anche se è possibile applicare un solo gruppo di sicurezza di rete a una singola subnet o interfaccia di rete, un gruppo di sicurezza di rete può essere associato a più interfacce di rete e più subnet.
> 
> 

## <a name="considerations"></a>Considerazioni
Durante la risoluzione dei problemi di connettività, tenere presente quanto segue:

* Le regole predefinite dei gruppi di sicurezza di rete bloccano l'accesso in ingresso da Internet e consentono solo il traffico di rete in ingresso nella rete virtuale. È necessario aggiungere regole in modo esplicito per consentire l'accesso in ingresso da Internet, come richiesto.
* Se non sono presenti regole dei gruppi di sicurezza di rete che causano un errore di connettività della VM, il problema potrebbe essere dovuto a:
  * Software del firewall in esecuzione all'interno del sistema operativo della VM
  * Route configurate per appliance virtuali o traffico locale. Il traffico Internet può essere reindirizzato al traffico locale tramite tunneling forzato. Una connessione RDP o SSH da Internet alla VM potrebbe non funzionare con questa impostazione, a seconda di come l'hardware di rete locale gestisce questo traffico. Per informazioni su come diagnosticare problemi di route che potrebbero impedire il flusso del traffico in ingresso e in uscita dalla VM, vedere l'articolo [Troubleshooting Routes](virtual-network-routes-troubleshoot-powershell.md) (Risoluzione dei problemi di route). 
* Se si hanno reti virtuali con peering, il tag VIRTUAL_NETWORK si espanderà automaticamente per impostazione predefinita in modo da includere i prefissi delle reti virtuali con peering. È possibile vedere questi prefissi nell'elenco **ExpandedAddressPrefix** per risolvere eventuali problemi legati alla connettività di peering della rete virtuale. 
* Le regole di sicurezza effettive vengono visualizzate solo se c'è un gruppo di sicurezza di rete associato all'interfaccia di rete o alla subnet della VM. 
* Se non esistono gruppi di sicurezza di rete associati all'interfaccia di rete o alla subnet e si dispone di un indirizzo IP pubblico assegnato alla VM, tutte le porte saranno aperte per l'accesso in ingresso e in uscita. Se la VM ha un indirizzo IP pubblico, è consigliabile applicare gruppi di sicurezza di rete all'interfaccia di rete o alla subnet.

