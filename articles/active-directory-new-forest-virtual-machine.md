<properties linkid="manage-services-networking-active-directory-forest" urlDisplayName="Active Directory forest" pageTitle="Install an Active Directory forest on an Azure virtual network" metaKeywords="" description="A tutorial that explains how to create a new Active Directory forest on a virtual machine (VM) on an Azure Virtual Network." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha"></tags>

# Installazione di una nuova foresta Active Directory in una rete virtuale di Azure

In questo argomento viene illustrato come creare un nuovo ambiente Windows Server Active Directory su una macchina virtuale (VM) in una [rete virtuale di Azure][]. In questo caso, la rete virtuale di Azure non è connessa a una rete locale.

Altri argomenti di interesse:

-   Facoltativamente, è possibile [configurare una VPN da sito a sito utilizzando la procedura guidata del portale di gestione][] e quindi installare una nuova foresta, oppure estendere una foresta locale a una rete virtuale di Azure. Per la procedura, vedere [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure][].
-   Per le linee guida concettuali sull'installazione di Servizi di dominio Active Directory in una rete virtuale di Azure, vedere [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure][].
-   Per istruzioni dettagliate sulla creazione di un ambiente di laboratorio di test in Azure che include Servizi di dominio Active Directory, vedere [Guida del laboratorio di testing: Configurazione di base di Windows Server 2012 R2 in Azure][].

## Sommario

-   [Differenze rispetto all'installazione locale][]
-   [Passaggio 1: Creare una rete virtuale di Azure][]
-   [Passaggio 2: Creare una VM per l'esecuzione del controller di dominio e dei ruoli del server DNS][]
-   [Passaggio 3: Installare Windows Server Active Directory][]
-   [Passaggio 4: Impostare il server DNS per la rete virtuale di Azure][]
-   [Passaggio 5: Creare VM per i membri del dominio e unire il dominio][]

## <span id="differ"></span></a>Differenze rispetto all'installazione locale

Non esistono molte differenze tra l'installazione locale di un controller di dominio e l'installazione in Azure. Le differenze principali sono elencate nella tabella seguente.

| Configurazione                                     | Locale                                                                                                                          | Rete virtuale di Azure                                                                                                 |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| **Indirizzo IP del controller di dominio**         | Assegnare un indirizzo IP statico nelle proprietà della scheda di rete                                                          | Ottenere l'indirizzo IP tramite DHCP ed eseguire facoltativamente il cmdlet Set-AzureStaticVNetIP per renderlo statico |
| **Resolver del client DNS**                        | Impostare l'indirizzo del server DNS preferito e quello alternativo nelle proprietà della scheda di rete dei membri del dominio | Impostare l'indirizzo del server DNS nelle proprietà della rete virtuale                                               |
| **Archiviazione del database di Active Directory** | Modificare facoltativamente il percorso di archiviazione predefinito da C:\\                                                    | È necessario modificare il percorso di archiviazione predefinito da C:\\                                               |

## <span id="createvnet"></span></a>Passaggio 1: Creare una rete virtuale di Azure

1.  Accedere al [portale di gestione di Azure][].
2.  Creare una rete virtuale. Fare clic su **Networks** \> **Create a virtual network**. Utilizzare i valori nella tabella seguente per completare la procedura guidata.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Pagina della procedura guidata</th>
    <th align="left">Valori da specificare</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Dettagli della rete virtuale</strong></td>
    <td align="left"><p>Name: immettere un nome per la rete virtuale</p>
    <p>Region: scegliere l'area più vicina</p>
    <p>Gruppo di affinità: <strong>Crea un gruppo di affinità</strong></p>
    <p>Nome gruppo di affinità: immettere un nome per il gruppo di affinità</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>DNS e VPN</strong></td>
    <td align="left"><p>Lasciare il campo relativo al server DNS vuoto</p>
    <p>Non selezionare alcuna opzione VPN</p></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>Spazi di indirizzi della rete virtuale</strong></td>
    <td align="left"><p>Nome subnet: immettere un nome per la subnet</p>
    <p>Starting IP: <strong>10.0.0.0</strong></p>
    <p>CIDR: <strong>/24 (256)</strong></p></td>
    </tr>
    </tbody>
    </table>

## <span id="createvm"></span></a>Passaggio 2: Creare una VM per l'esecuzione del controller di dominio e dei ruoli del server DNS

1.  Fare clic su **New** \> **Compute** \> **Virtual Machine** \> **From Gallery**.
2.  Utilizzare i valori nella tabella seguente per completare la procedura guidata.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Pagina della procedura guidata</th>
    <th align="left">Valori da specificare</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Sistema operativo</strong></td>
    <td align="left">Selezionare <strong>Windows Server 2012 R2 Datacenter</strong></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Configurazione macchina virtuale</strong></td>
    <td align="left"><p>Data di rilascio: data odierna</p>
    <p>Nome computer: specificare un valore univoco</p>
    <p>Livello: Standard</p>
    <p>Dimensione: selezionare una dimensione</p>
    <p>Nome utente: immettere un nome. Questo account utente sarà membro del gruppo Administrators predefinito.</p>
    <p>Password: deve contenere almeno 8 caratteri e includere 3 dei seguenti tipi di caratteri:</p>
    <ul>
    <li>una lettera maiuscola</li>
    <li>una lettera minuscola</li>
    <li>un numero</li>
    <li>un carattere speciale</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>Servizio cloud</strong></td>
    <td align="left"><p>Servizio cloud: <strong>Crea un nuovo servizio cloud</strong></p>
    <p>Nome servizio cloud: accettare il valore predefinito</p>
    <p>Regione/Gruppo di affinità/Rete virtuale: selezionare la rete virtuale creata</p>
    <p>Subnet della rete virtuale: Selezionare la subnet creata.</p>
    <p>Account di archiviazione: <strong>Usa un account di archiviazione generato automaticamente</strong></p>
    <p>Set di disponibilità: <strong>None</strong></p>
    <p>Endpoints: accettare i valori predefiniti</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Agente di macchine virtuali</strong></td>
    <td align="left">Selezionare <strong>Installa l'agente di macchine virtuali</strong></td>
    </tr>
    </tbody>
    </table>

3.  L'indirizzo IP dinamico assegnato alla VM per impostazione predefinita è valido per la durata del servizio cloud, ma se la VM viene arrestata verrà modificato. È possibile assegnare un IP statico [eseguendo il cmdlet Set-AzureStaticVNetIP Azure PowerShell][], per fare in modo che l'indirizzo IP resti invariato anche se la VM viene arrestata.
4.  Collegare un disco aggiuntivo alla VM per l'archiviazione del database di Active Directory, dei log e di SYSVOL.
5.  Fare clic sulla **VM** \> **Attach** \> **Attach empty disk**.
6.  Specificare una dimensione, ad esempio 10 GB e accettare tutti gli altri valori predefiniti.
7.  Accedere alla VM e formattare il disco aggiuntivo.
8.  Fare clic su **Connect** per accedere alla VM, fare clic su **Open** per creare una sessione RDP, quindi fare di nuovo clic su **Connect**.
9.  Modificare le credenziali inserendo il nuovo nome utente e la password specificati.
10. In Server Manager fare clic su **Tools** \> **Computer Management**.
11. Fare clic su **Disk Management**, quindi su **OK** per inizializzare il nuovo disco.
12. Fare clic con il pulsante destro del mouse sul nome del disco e scegliere **New Simple Volume**. Completare la procedura guidata per formattare il nuovo disco.

## <span id="installad"></span></a>Passaggio 3: Installare Windows Server Active Directory

[Installare Servizi di dominio Active Directory][] utilizzando la procedura per l'installazione locale, ovvero utilizzando l'interfaccia utente, un file di risposte o Windows PowerShell. Per installare una nuova foresta è necessario fornire le credenziali di amministratore. Per specificare il percorso del database di Active Directory, dei log e di SYSVOL, modificare il percorso di archiviazione predefinito dall'unità del sistema operativo al disco dati aggiuntivo precedentemente collegato alla macchina virtuale.

Al termine dell'installazione del controller di dominio, connettersi nuovamente alla VM e accedere al controller di dominio. Ricordare di specificare le credenziali del dominio.

</p>
## <span id="dns"></span></a>Passaggio 4: Impostare il server DNS per la rete virtuale di Azure

1.  Fare clic su **Virtual Networks**, fare doppio clic sulla rete virtuale creata, quindi fare clic su **Configure**.
2.  In **DNS servers** digitare il nome e il DIP del controller di dominio e fare clic su **Save**.
3.  Selezionare la VM e fare clic su **Restart** per fare in modo che la VM configuri le impostazioni del resolver DNS con l'indirizzo IP del nuovo server DNS.

## <span id="domainmembers"></span></a>Passaggio 5: Creare VM per i membri del dominio e unire il dominio

Creare altre macchine virtuali per il provisioning dei computer membri del dominio. È possibile utilizzare l'interfaccia utente o Azure PowerShell. Se si utilizza l'interfaccia utente, seguire la stessa procedura utilizzata per creare la prima VM. Unire quindi le VM al dominio come si farebbe per un'installazione locale. Se si usa Azure PowerShell, è possibile eseguire il provisioning delle macchine virtuali e aggiungerle al dominio al primo avvio.

In questo esempio verrà creata una macchina virtuale aggiunta al dominio e denominata DC2 che esegue Windows Server 2012 R2 Datacenter. Dopo il provisioning, eseguire l'accesso a DC2 come amministratore di dominio e alzare di livello la macchina in modo che diventi un controller di dominio di replica.

È possibile eseguire Get-AzureVMImage per ottenere nomi di immagine. Ad esempio, per visualizzare un elenco di immagini per Windows Server 2012 R2, eseguire Get-AzureVMImage | where-object {$\_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}.

    '

    cls

    Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
    Select-AzureSubscription -SubscriptionName "Free Trial"

    #Deploy a new VM and join it to the domain
    #-------------------------------------------
    #Specify my DC's DNS IP (10.0.0.4)
    $myDNS = New-AzureDNS -Name 'DC1' -IPAddress '10.0.0.4'

    # OS Image to Use
    $image = 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd'
    $service = 'DC2'
    $AG = 'YourAffinityGroup'
    $vnet = 'YourVirtualNetwork'
    $pwd = 'P@ssw0rd'
    $size = 'Small'

    #VM Configuration
    $vmname = 'DC2'
    $MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

    New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Se si esegue nuovamente lo script sarà necessario fornire un valore univoco per $service. È possibile eseguire Test-AzureName -Service *nome servizio*, il cui risultato indica se il nome è già in uso.

## Vedere anche

-   [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure][1]

-   [Configurare una rete virtuale solo cloud nel portale di gestione][]

-   [Configurare una VPN da sito a sito nel portale di gestione][]

-   [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure.][]

-   [Windows Azure IT Pro IaaS: (01) Dati fondamentali delle macchine virtuali][]

-   [Windows Azure IT Pro IaaS: (05) Creazione di reti virtuali e connettività cross-premise][]

-   [Rete virtuale di Azure][rete virtuale di Azure]

-   [Come installare e configurare Azure PowerShell][]

-   [Azure PowerShell][]

-   [Cmdlet di gestione di Azure][]

-   [Impostazione dell'indirizzo IP statico per una VM di Azure][]

-   [Come assegnare un IP statico a una macchina virtuale di Azure][]

-   [Installazione di una nuova foresta Active Directory][Installare Servizi di dominio Active Directory]

-   [Introduzione alla virtualizzazione di Servizi di dominio Active Directory (livello 100)][]

-   [Guida del laboratorio di testing: Configurazione di base di Windows Server 2012 R2 in Azure][]

  [rete virtuale di Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/jj156007.aspx
  [configurare una VPN da sito a sito utilizzando la procedura guidata del portale di gestione]: http://msdn.microsoft.com/it-it/library/windowsazure/dn133795.aspx
  [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure]: http://www.windowsazure.com/it-it/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/jj156090.aspx
  [Guida del laboratorio di testing: Configurazione di base di Windows Server 2012 R2 in Azure]: http://www.microsoft.com/it-it/download/details.aspx?id=41684
  [Differenze rispetto all'installazione locale]: #differ
  [Passaggio 1: Creare una rete virtuale di Azure]: #createvnet
  [Passaggio 2: Creare una VM per l'esecuzione del controller di dominio e dei ruoli del server DNS]: #createvm
  [Passaggio 3: Installare Windows Server Active Directory]: #installad
  [Passaggio 4: Impostare il server DNS per la rete virtuale di Azure]: #dns
  [Passaggio 5: Creare VM per i membri del dominio e unire il dominio]: #domainmembers
  [portale di gestione di Azure]: https://manage.windowsazure.com
  [eseguendo il cmdlet Set-AzureStaticVNetIP Azure PowerShell]: http://msdn.microsoft.com/it-it/library/windowsazure/dn630228.aspx
  [Installare Servizi di dominio Active Directory]: http://technet.microsoft.com/library/jj574166.aspx
  [1]: http://msdn.microsoft.com/library/azure/jj156090.aspx
  [Configurare una rete virtuale solo cloud nel portale di gestione]: http://msdn.microsoft.com/it-it/library/dn631643.aspx
  [Configurare una VPN da sito a sito nel portale di gestione]: http://msdn.microsoft.com/it-it/library/dn133795.aspx
  [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure.]: http://azure.microsoft.com/it-it/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [Windows Azure IT Pro IaaS: (01) Dati fondamentali delle macchine virtuali]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01
  [Windows Azure IT Pro IaaS: (05) Creazione di reti virtuali e connettività cross-premise]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05
  [Come installare e configurare Azure PowerShell]: http://www.windowsazure.com/it-it/documentation/articles/install-configure-powershell/
  [Azure PowerShell]: http://msdn.microsoft.com/it-it/library/windowsazure/jj156055.aspx
  [Cmdlet di gestione di Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/jj152841
  [Impostazione dell'indirizzo IP statico per una VM di Azure]: http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address
  [Come assegnare un IP statico a una macchina virtuale di Azure]: http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/
  [Introduzione alla virtualizzazione di Servizi di dominio Active Directory (livello 100)]: http://technet.microsoft.com/it-it/library/hh831734.aspx
