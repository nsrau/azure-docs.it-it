<properties
 pageTitle="Creare una distribuzione di più macchine virtuali utilizzando l'interfaccia della riga di comando multipiattaforma di Azure | Azure"
 description="Informazioni su come creare una distribuzione di più macchine virtuali usando l'interfaccia della riga di comando multipiattaforma di Azure"
 services="virtual-machines"
 documentationCenter="nodejs"
 authors="AlanSt"
 manager="timlt"
 editor=""/>

 <tags
 ms.service="virtual-machines"
 ms.devlang="nodejs"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="02/20/2015"
 ms.author="alanst;kasing"/>

# Creare una distribuzione di più macchine virtuali utilizzando l'interfaccia della riga di comando multipiattaforma di Azure

Lo script seguente descrive come configurare una distribuzione di servizio di più cloud, con più macchine virtuali, in una rete virtuale, usando l'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli).

Nell'immagine seguente viene illustrato l'aspetto della distribuzione al completamento dello script:

![](./media/virtual-machines-create-multi-vm-deployment-xplat-cli/multi-vm-xplat-cli.png)

Lo script crea una macchina virtuale (**servervm**) nel servizio cloud **servercs** con due dischi dati collegati e due macchine virtuali (**clientvm1, clientvm2**) nel servizio cloud **workercs**. Entrambi i servizi cloud vengono posizionati nella rete virtuale **samplevnet**. Il servizio cloud **servercs** dispone anche di un endpoint configurato per la connettività esterna.

## Script CLI per l'esecuzione dell'operazione
Il codice per impostare questa funzionalità è relativamente semplice:

>[AZURE.NOTE] Sarà probabilmente necessario modificare i nomi dei servizi cloud servercs e workercs per fare in modo che siano univoci

 azure network vnet create samplevnet -l "West US"
 azure vm create -l "West US" -w samplevnet -e 10000 -z Small -n servervm servercs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-it-it-30GB azureuser Password@1
 azure vm create -l "West US" -w samplevnet -e 10001 -z Small -n clientvm1 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-it-it-30GB azureuser Password@1
 azure vm create -l "West US" -w samplevnet -e 10002 -c -z Small -n clientvm2 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-it-it-30GB azureuser Password@1
 azure vm disk attach-new servervm 100
 azure vm disk attach-new servervm 500
 azure vm endpoint create servervm 443 443 -n https -o tcp

Il codice per l'eliminazione viene riportato di seguito:

 azure vm delete -b -q servervm
 azure vm delete -b -q clientvm1
 azure vm delete -b -q clientvm2
 azure network vnet delete -q samplevnet

*L'opzione -q elimina la conferma interattiva per l'eliminazione di oggetti, -b cancella i dischi e i BLOB associati alla macchina virtuale.*

## Formati generici dei comandi usati

Nonostante sia possibile trovare altre informazioni usando l'opzione -help in qualsiasi comando CLI di Azure, il formato generico di ciascun comando utilizzato sopra è il seguente:

 azure network vnet create -l <Region> <VNet_name>
 azure network vnet delete -q <VNet_name>

 azure vm create -l <Region> -w <Vnet_name> -e <SSH_port> -z <VM_size> -n <VM_name> <Cloud_service_name> <VM_image> <Username> <Password>
 azure vm delete -b -q <VM_name>
 azure vm disk attach-new <VM_name>
 azure vm endpoint create <VM_name> <External_port> <Internal_port> -n <Endpoint_name> -o <TCP/UDP>

## Passaggi successivi

 
* [Computing Linux e open source in Azure](virtual-machines-linux-opensource.md)
* [Come accedere a una macchina virtuale che esegue Linux](virtual-machines-linux-how-to-log-on.md)

<!--HONumber=47-->
