---
title: Risolvere i problemi relativi ai gruppi di sicurezza di rete - PowerShell | Documentazione Microsoft
description: Informazioni su come risolvere i problemi dei gruppi di sicurezza di rete nel modello di distribuzione Azure Resource Manager con Azure PowerShell.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 4c732bb7-5cb1-40af-9e6d-a2a307c2a9c4
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 5edaf7197576ac1c0bd1fc6bed21fd65ed135106
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Risolvere i problemi relativi ai gruppi di sicurezza di rete tramite Azure PowerShell
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

## <a name="detailed-troubleshooting-steps"></a>Procedura di risoluzione dei problemi dettagliata
Completare i passaggi seguenti per risolvere i problemi dei gruppi di sicurezza di rete per una VM:

1. Avviare una sessione di Azure PowerShell e accedere ad Azure. Se non si ha dimestichezza con Azure PowerShell, leggere l'articolo [Come installare e configurare Azure PowerShell](/powershell/azure/overview) .
2. Immettere il comando seguente per restituire tutte le regole dei gruppi di sicurezza di rete applicate a un'interfaccia di rete denominata *VM1 NIC1* nel gruppo di risorse *RG1*:
   
        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Se non si conosce il nome di un'interfaccia di rete, immettere il comando seguente per recuperare i nomi di tutte le interfacce di rete in un gruppo di risorse: 
   > 
   > `Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`
   > 
   > 
   
    Il testo riportato di seguito è un esempio delle regole valide restituite per l'interfaccia di rete *VM1 NIC1* :
   
        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
   
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]
   
    Tenere presenti le seguenti informazioni nell'output:
   
   * Esistono due sezioni in **NetworkSecurityGroup**: una è associata a una subnet (*Subnet1*), l'altra a un'interfaccia di rete (*VM1-NIC1*). In questo esempio, è stato applicato un gruppo di sicurezza di rete a ciascuna.
   * **Associazione** mostra la risorsa (subnet o interfaccia di rete) a cui è associato un determinato gruppo di sicurezza di rete. Se la risorsa del gruppo di sicurezza di rete viene spostata oppure se viene eliminata la sua associazione appena prima di eseguire il comando, potrebbe essere necessario attendere qualche secondo prima che la modifica sia effettiva nell'output del comando. 
   * I nomi delle regole precedute da *defaultSecurityRules*: quando viene creato un gruppo di risorse di rete, vengono create varie regole predefinite al suo interno. Le regole predefinite non possono essere rimosse, tuttavia possono essere sostituite con regole dalla priorità più elevata.
     Per ulteriori informazioni sulle regole di sicurezza predefinite dei gruppi di sicurezza di rete, leggere l'articolo [Panoramica dei gruppi di sicurezza di rete](virtual-networks-nsg.md#default-rules) .
   * **ExpandedAddressPrefix** espande i prefissi degli indirizzi per i tag predefiniti dei gruppi di sicurezza di rete. I tag rappresentano più prefissi di indirizzi. L'espansione dei tag può essere utile per la risoluzione dei problemi di connettività delle VM da e verso prefissi di indirizzi specifici. Ad esempio, con il peering reti virtuali, il tag VIRTUAL_NETWORK viene espanso per visualizzare i prefissi delle reti virtuali con peering nell'output precedente.
     
     > [!NOTE]
     > Il comando mostra regole efficaci solo se un gruppo di sicurezza di rete è associato a una subnet, a un'interfaccia di rete o a entrambe. Una VM può avere diverse interfacce di rete a cui sono applicati vari gruppi di sicurezza di rete. Per risolvere il problema, eseguire il comando per ogni interfaccia di rete.
     > 
     > 
3. Per semplificare l'applicazione di filtri a grandi quantità di regole per i gruppi di sicurezza di rete, immettere i seguenti comandi per risolvere il problema: 
   
        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView
   
    Viene applicato un filtro per il traffico RDP (porta TCP 3389) alla visualizzazione griglia, come illustrato nell'immagine seguente:
   
    ![Elenco di regole](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
4. Come si può vedere, sono presenti regole sia di tipo "allow" che di tipo "deny" per RDP. L'output del passaggio 2 mostra che la regola *DenyRDP* si trova nel gruppo di sicurezza di rete applicato alla subnet. Per le regole in entrata, vengono elaborati per primi i gruppi di sicurezza di rete applicati alla subnet. Se viene trovata una corrispondenza, il gruppo di sicurezza di rete applicato all'interfaccia di rete non viene elaborato. In questo caso, la regola *DenyRDP* della subnet blocca RDP verso la VM (**VM1**).
   
   > [!NOTE]
   > A una VM potrebbero essere collegate più interfacce di rete. Ognuna può essere collegata a una subnet diversa. Dal momento che i comandi nei passaggi precedenti vengono eseguiti su un'interfaccia di rete, è importante assicurarsi di specificare l'interfaccia di rete su cui si verifica l'errore di connettività. Se non si è certi, è sempre possibile eseguire i comandi su ogni interfaccia di rete collegata alla VM.
   > 
   > 
5. Per effettuare una connessione RDP a VM1, modificare la regola *Deny RDP (3389)* in *Allow RDP(3389)* nel gruppo di sicurezza di rete **Subnet1-NSG**. Verificare che la porta TCP 3389 sia aperta aprendo una connessione RDP alla VM o usando lo strumento PsPing. Per trovare ulteriori informazioni su PsPing, consultare la [pagina di download di PsPing](https://technet.microsoft.com/sysinternals/psping.aspx)
   
    È possibile rimuovere le regole da un gruppo di sicurezza di rete usando le informazioni nell'output dal seguente comando:
   
        Get-Help *-AzureRmNetworkSecurityRuleConfig

## <a name="considerations"></a>Considerazioni
Durante la risoluzione dei problemi di connettività, tenere presente quanto segue:

* Le regole predefinite dei gruppi di sicurezza di rete bloccano l'accesso in ingresso da Internet e consentono solo il traffico di rete in ingresso nella rete virtuale. È necessario aggiungere regole in modo esplicito per consentire l'accesso in ingresso da Internet, come richiesto.
* Se non sono presenti regole dei gruppi di sicurezza di rete che causano un errore di connettività della VM, il problema potrebbe essere dovuto a:
  * Software del firewall in esecuzione all'interno del sistema operativo della VM
  * Route configurate per appliance virtuali o traffico locale. Il traffico Internet può essere reindirizzato al traffico locale tramite tunneling forzato. Una connessione RDP o SSH da Internet alla VM potrebbe non funzionare con questa impostazione, a seconda di come l'hardware di rete locale gestisce questo traffico. Per informazioni su come diagnosticare problemi di route che potrebbero impedire il flusso del traffico in ingresso e in uscita dalla VM, vedere l'articolo [Troubleshooting Routes](virtual-network-routes-troubleshoot-powershell.md) (Risoluzione dei problemi di route). 
* Se si hanno reti virtuali con peering, il tag VIRTUAL_NETWORK si espanderà automaticamente per impostazione predefinita in modo da includere i prefissi delle reti virtuali con peering. È possibile vedere questi prefissi nell'elenco **ExpandedAddressPrefix** per risolvere eventuali problemi legati alla connettività di peering della rete virtuale. 
* Le regole di sicurezza effettive vengono visualizzate solo se c'è un gruppo di sicurezza di rete associato all'interfaccia di rete o alla subnet della VM. 
* Se non esistono gruppi di sicurezza di rete associati all'interfaccia di rete o alla subnet e si dispone di un indirizzo IP pubblico assegnato alla VM, tutte le porte saranno aperte per l'accesso in ingresso e in uscita. Se la VM ha un indirizzo IP pubblico, è consigliabile applicare gruppi di sicurezza di rete all'interfaccia di rete o alla subnet.  

