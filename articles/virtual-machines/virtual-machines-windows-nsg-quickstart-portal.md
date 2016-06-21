<properties
   pageTitle="Consentire l'accesso esterno a una VM mediante il portale | Microsoft Azure"
   description="Informazioni su come aprire una porta o creare un endpoint che consenta l'accesso alla VM tramite il modello di distribuzione di Resource Manager nel portale di Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="05/24/2016"
   ms.author="iainfou"/>

# Consentire l'accesso esterno alla VM mediante il portale di Azure
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## Comandi rapidi
È possibile [eseguire questi passaggi anche tramite Azure PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

Come prima operazione, creare il gruppo di sicurezza di rete. Selezionare un gruppo di risorse nel portale, fare clic su "Aggiungi", quindi cercare e selezionare "Gruppo di sicurezza di rete":

![Aggiungere un gruppo di sicurezza di rete](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

Immettere un nome per il gruppo di sicurezza di rete e selezionare un percorso:

![Creare un gruppo di sicurezza di rete](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

Selezionare il nuovo gruppo di sicurezza di rete. È ora possibile creare una regola in entrata:

![Aggiungere una regola in entrata](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

Fornire un nome per la regola. Si noti che la porta 80 è già inserita per impostazione predefinita. Qui è possibile modificare l'origine, il protocollo e la destinazione durante l'aggiunta di regole ulteriori al gruppo di sicurezza di rete:

![Creare una regola in entrata](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

Il passaggio finale consiste nell'associare il gruppo di sicurezza di rete con una subnet o un'interfaccia di rete specifica. Per associare il gruppo di sicurezza di rete a una subnet:

![Associare un gruppo di sicurezza di rete con una subnet](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

Selezionare la rete virtuale, quindi selezionare la subnet appropriata:

![Associare un gruppo di sicurezza di rete con una rete virtuale](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

È stato creato un gruppo di sicurezza di rete, è stata creata una regola in entrata che consente il traffico sulla porta 80 ed è stata associata a una subnet. Tutte le macchine virtuali che si connettono a tale subnet saranno raggiungibili sulla porta 80.


## Altre informazioni sui gruppi di sicurezza di rete
I comandi rapidi seguenti consentono di rendere operativo il traffico verso la VM. I gruppi di sicurezza di rete offrono numerose funzionalità efficienti e la necessaria granularità per controllare l'accesso alle risorse. Per altre informazioni, leggere l'articolo sulla [creazione di un gruppo di sicurezza di rete e di regole dell'elenco di controllo di accesso qui](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Le regole dell'elenco di controllo di accesso e i gruppi di sicurezza di rete possono anche essere definiti come parte dei modelli di distribuzione Azure Resource Manager. Per altre informazioni, leggere l'articolo [Come creare NSG utilizzando un modello](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se si deve usare il port forwarding per eseguire il mapping di una sola porta esterna a una porta interna nella VM, è necessario usare un servizio di bilanciamento del carico e le regole Network Address Translation (NAT). Ad esempio, si desidera esporre la porta TCP 8080 esternamente e che il traffico venga indirizzato sulla porta TCP 80 in una VM. Per altre informazioni, leggere l'articolo relativo alla [creazione di un servizio di bilanciamento del carico per Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## Passaggi successivi
In questo esempio viene creata una regola semplice per consentire il traffico HTTP. È possibile trovare informazioni sulla creazione di ambienti più dettagliati negli articoli seguenti:

- [Panoramica di Gestione risorse di Azure](../resource-group-overview.md)
- [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)
- [Panoramica di Azure Resource Manager per i servizi di bilanciamento del carico](../load-balancer/load-balancer-arm.md)

<!---HONumber=AcomDC_0608_2016-->