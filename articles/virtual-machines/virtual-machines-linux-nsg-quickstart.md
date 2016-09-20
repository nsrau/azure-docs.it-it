<properties
   pageTitle="Aprire porte per una VM Linux | Microsoft Azure"
   description="Informazioni su come aprire una porta o creare un endpoint per la VM Linux tramite il modello di distribuzione Azure Resource Manager e l'interfaccia della riga di comando di Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="iainfou"/>  

# Apertura di porte per una VM Linux in Azure
Aprire una porta o creare un endpoint in una macchina virtuale (VM) di Azure tramite la creazione di un filtro di rete su una subnet o un'interfaccia di rete di VM. Questi filtri, che consentono di controllare il traffico in ingresso e in uscita, vengono inseriti in un gruppo di sicurezza di rete e collegati alla risorsa che riceve il traffico. Si userà un esempio comune di traffico Web sulla porta 80.

## Comandi rapidi
Per creare un gruppo di sicurezza di rete e le regole, è necessaria l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) in modalità Resource Manager (`azure config mode arm`).

Creare il gruppo di sicurezza di rete immettendo nomi e percorso in modo appropriato:

```
azure network nsg create --resource-group TestRG --name TestNSG --location westus
```

Aggiungere una regola per consentire il traffico HTTP al server Web (o in base allo specifico scenario, come l'accesso SSH o la connettività al database):

```
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow --resource-group TestRG --nsg-name TestNSG --name AllowHTTP
```

Associare il gruppo di sicurezza di rete con l'interfaccia di rete della VM:

```
azure network nic set --resource-group TestRG --name TestNIC --network-security-group-name TestNSG
```

In alternativa, è possibile associare il gruppo di sicurezza di rete a una subnet di rete virtuale invece della sola interfaccia di rete in una singola VM:

```
azure network vnet subnet set --resource-group TestRG --name TestSubnet --network-security-group-name TestNSG
```

## Altre informazioni sui gruppi di sicurezza di rete
I comandi rapidi seguenti consentono di rendere operativo il traffico verso la VM. I gruppi di sicurezza di rete offrono numerose funzionalità efficienti e la necessaria granularità per controllare l'accesso alle risorse. Per altre informazioni, leggere l'articolo sulla [creazione di un gruppo di sicurezza di rete e di regole dell'elenco di controllo di accesso qui](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

È possibile definire le regole dell'elenco di controllo di accesso e i gruppi di sicurezza di rete come parte dei modelli di Azure Resource Manager. Per altre informazioni, leggere l'articolo [Come creare NSG utilizzando un modello](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se si deve usare il port forwarding per eseguire il mapping di una sola porta esterna verso una porta interna della VM, usare un servizio di bilanciamento del carico e le regole Network Address Translation (NAT). Ad esempio, si desidera esporre la porta TCP 8080 esternamente e che il traffico venga indirizzato sulla porta TCP 80 in una VM. Per altre informazioni, leggere l'articolo relativo alla [creazione di un servizio di bilanciamento del carico per Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## Passaggi successivi
In questo esempio viene creata una regola semplice per consentire il traffico HTTP. È possibile trovare informazioni sulla creazione di ambienti più dettagliati negli articoli seguenti:

- [Panoramica di Gestione risorse di Azure](../resource-group-overview.md)
- [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)
- [Panoramica di Azure Resource Manager per i servizi di bilanciamento del carico](../load-balancer2 /load-balancer-arm.md)

<!---HONumber=AcomDC_0907_2016-->