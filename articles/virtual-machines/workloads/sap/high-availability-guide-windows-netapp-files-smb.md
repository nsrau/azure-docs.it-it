---
title: HA macchine virtuali di Azure per SAP NW in Windows con Azure NetApp Files (SMB) Documenti Microsoft
description: Disponibilità elevata per SAP NetWeaver nelle macchine virtuali di Azure in Windows con Azure NetApp Files (SMB) per le applicazioni SAPHigh availability for SAP NetWeaver on Azure VMs on Windows with Azure NetApp Files (SMB) for SAP applications
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: radeltch
ms.openlocfilehash: b41db629c5308348f632b3dc51c75822ba361c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591354"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Disponibilità elevata per SAP NetWeaver nelle macchine virtuali di Azure in Windows con Azure NetApp Files (SMB) per le applicazioni SAPHigh availability for SAP NetWeaver on Azure VMs on Windows with Azure NetApp Files(SMB) for SAP applications

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

In questo articolo viene descritto come distribuire, configurare le macchine virtuali, installare il framework del cluster e installare un sistema SAP NetWeaver 7.50 a disponibilità elevata nelle macchine virtuali Windows, utilizzando [SMB](https://docs.microsoft.com/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) nei [file NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)di Azure.  

Il livello di database non viene trattato in dettaglio in questo articolo. Si presuppone che la [rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) di Azure sia già stata creata.  

Leggere prima di tutto i documenti e le note SAP seguenti:

* [Documentazione sui file NetApp di AzureAzure NetApp Files documentation][anf-azure-doc] 
* Nota SAP [1928533][1928533], che contiene:  
  * Un elenco delle dimensioni delle macchine virtuali di Azure supportate per la distribuzione del software SAP
  * Importanti informazioni sulla capacità per le dimensioni delle VM di Azure
  * Software SAP e combinazioni di sistemi operativi e database supportati
  * Versione del kernel SAP richiesta per Windows in Microsoft Azure
* La nota SAP [2015553][2015553] elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
* La nota SAP [2178632][2178632] contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
* La nota SAP [1999351][1999351] contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
* Sap Nota [2287140](https://launchpad.support.sap.com/#/notes/2287140) elenca i prerequisiti per la funzionalità CA supportata da SAP del protocollo SMB 3.x.
* SAP Nota [2802770](https://launchpad.support.sap.com/#/notes/2802770) dispone di informazioni sulla risoluzione dei problemi per la transazione SAP a esecuzione lenta AL11 in Windows 2012 e 2016.
* SAP Nota [1911507](https://launchpad.support.sap.com/#/notes/1911507) contiene informazioni sulla funzionalità di failover trasparente per una condivisione file in Windows Server con il protocollo SMB 3.0.
* SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452) ha raccomandazione (disattivando la generazione di nomi 8.3) per risolvere le prestazioni del file system scarse/errori durante l'accesso ai dati.
* [Installare la disponibilità elevata di SAP NetWeaver in un cluster di failover Windows e condivisione file per le istanze di SAP ASCS/SCS in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share) 
* [Scenari e architettura di disponibilità elevata in Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
* [Aggiungere la porta probe nella configurazione del cluster ASCSAdd probe port in ASCS cluster configuration](sap-high-availability-installation-wsfc-file-share.md)
* [Installazione di un'istanza (A)SCS in un cluster di failover](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Creare un volume SMB per Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)
* [Applicazioni SAP NetApp in Microsoft Azure con i file NetApp di Azure][anf-sap-applications-azure]

## <a name="overview"></a>Panoramica

SAP ha sviluppato un nuovo approccio e un'alternativa per i dischi condivisi del cluster per il clustering di un'istanza ASCS/SCS di SAP in un cluster di failover di Windows. Anziché utilizzare dischi condivisi del cluster, è possibile utilizzare una condivisione file SMB per distribuire i file host globali SAP. File NetApp di Azure supporta SMBv3 (insieme a NFS) con ACL NTFS tramite Active Directory. File NetApp di Azure è automaticamente a disponibilità elevata (in quanto si tratta di un servizio PaaS). Queste funzionalità rendono i file NetApp di Azure un'ottima opzione per ospitare la condivisione file SMB per il globale SAP.  
Sono supportati sia Servizi di [dominio Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/overview) che Servizi di dominio Active [Directory.](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) È possibile usare i controller di dominio di Active Directory esistenti con File di Azure NetApp.You can use existing Active Directory domain controllers with Azure NetApp Files. I controller di dominio possono trovarsi in Azure come macchine virtuali o in locale tramite ExpressRoute o VPN S2S.Domain controllers can be in Azure as virtual machines, or on premises via ExpressRoute or S2S VPN. In this article, we will use Domain controller in an Azure VM.  
La disponibilità elevata (HA) per i servizi centrali SAP Netweaver richiede l'archiviazione condivisa. Per raggiungere questo obiettivo in Windows, finora era necessario creare un cluster SOFS o utilizzare il cluster condiviso disk s/w come SIOS. Ora è possibile ottenere SAP Netweaver HA usando l'archiviazione condivisa, distribuita in File NetApp di Azure.Now it is possible to achieve SAP Netweaver HA by using shared storage, deployed on Azure NetApp Files. L'uso dei file NetApp di Azure per l'archiviazione condivisa elimina la necessità di SOFS o SIOS.  

> [!NOTE]
> Il clustering di istanze ASCS/SCS di SAP tramite con condivisione file è supportato per SAP NetWeaver 7.40 (e versioni successive) con kernel SAP versione 7.49 o versione successiva.  

![Architettura SAP ASCS/SCS HA con condivisione SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

I prerequisiti per una condivisione file SMB sono:
* Protocollo SMB 3.0 (o versione successiva).
* Possibilità di impostare gli elenchi di controllo di accesso (ACL) di Active Directory per i gruppi di utenti di Active Directory e l'oggetto computer.
* La condivisione file deve essere abilitata per la funzionalità di controllo della rete.

The share for the SAP Central services in this reference architecture is offered by Azure NetApp Files:

![Architettura SAP ASCS/SCS HA con condivisione SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Creare e montare il volume SMB per i file NetApp di AzureCreate and mount SMB volume for Azure NetApp Files

Eseguire i passaggi seguenti come preparazione per l'utilizzo dei file NetApp di Azure.Perform the following steps, as preparation for using Azure NetApp Files.  

1. Seguire i passaggi per registrarsi per i file NetApp di AzureFollow the steps to [Register for Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  
2. Creare l'account NetApp di Azure seguendo la procedura descritta in [Creare un account NetAppCreate Azure NetApp account,](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account) following the steps described in Create a NetApp account  
3. Impostare il pool di capacità seguendo le istruzioni in [Impostare un pool](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool) di capacità
4. Le risorse dei file netApp di Azure devono risiedere nella subnet delegata. Seguire le istruzioni in [Delegare una subnet ai](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) file NetApp di Azure per creare una subnet delegata.  

> [!IMPORTANT]
> È necessario creare connessioni active Directory prima di creare un volume SMB. Esaminare i requisiti per le [connessioni di Active Directory](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections).  

5. Creare una connessione Active Directory, come descritto in [Creare una connessione active DirectoryCreate Active Directory connection,](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#create-an-active-directory-connection) as described in Create an Active Directory connection  
6. Creare il volume SMB di File NetApp di Azure SMB, seguendo le istruzioni in [Aggiungere un volume SMBCreate SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#add-an-smb-volume) Azure NetApp Files SMB volume, following the instructions in Add an SMB volume  
7. Montare il volume SMB nella macchina virtuale Windows.

> [!TIP]
> È possibile trovare le istruzioni su come montare il volume File NetApp di Azure, se si passa al portale di [Azure](https://portal.azure.com/#home) per l'oggetto File di Rete di Azure, fare clic sul pannello **Volumi,** quindi su Istruzioni di **montaggio**.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Preparare l'infrastruttura per la configurazione ha SAP usando un cluster di failover di WindowsPrepare the infrastructure for SAP HA by using a Windows failover cluster 

1. [Impostare gli indirizzi IP DNS necessari](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#b22d7b3b-4343-40ff-a319-097e13f62f9e)  
2. [Impostare indirizzi IP statici per le macchine virtuali SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#84c019fe-8c58-4dac-9e54-173efd4b2c30)
3. [Impostare un indirizzo IP statico per il servizio](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#7a8f3e9b-0624-4051-9e41-b73fff816a9e)di bilanciamento del carico interno di Azure.
4. [Impostare delle regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#f19bd997-154d-4583-a46e-7f5a69d0153c).
5. Modificare le regole di [bilanciamento del carico predefinite di ASCS/SCS per il servizio](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#fe0bd8b5-2b43-45e3-8295-80bee5415716)di bilanciamento del carico interno di Azure.
6. [Aggiungere macchine virtuali Windows al dominio](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
7. [Aggiungere voci del Registro di sistema su entrambi i nodi del cluster dell'istanza SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#661035b2-4d0f-4d31-86f8-dc0a50d78158)
8. [Configurare un cluster di failover di Windows Server per un'istanza SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#0d67f090-7928-43e0-8772-5ccbf8f59aab)
9. Se si usa Windows Server 2016, è consigliabile configurare [Azure Cloud Witness.](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Installare l'istanza SAP ASCS in entrambi i nodi

È necessario il seguente software da SAP:
   * Sap Software Provisioning Manager (SWPM) versione dello strumento di installazione SPS25 o versione successiva.
   * SAP Kernel 7.49 o versione successiva
   * Creare un nome host virtuale (nome di rete del cluster) per l'istanza SAP ASCS/SCS in cluster, come descritto in [Creare un nome host virtuale per l'istanza SAP ASCS/SCS in cluster.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#a97ad604-9094-44fe-a364-f89cb39bf097)

> [!NOTE]
> Il clustering di istanze ASCS/SCS di SAP tramite con condivisione file è supportato per SAP NetWeaver 7.40 (e versioni successive) con kernel SAP versione 7.49 o versione successiva.  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Installare un'istanza ASCS/SCS nel primo nodo del cluster ASCS/SCS

1. Installare un'istanza SAP ASCS/SCS nel primo nodo del cluster. Avviare lo strumento di installazione SAP SWPM, quindi passare a: **Product** > **DBMS** > Installation > Application Server ABAP (o Java) > High-Availability System >'istanza ASCS/SCS > Primo nodo cluster.  

2. Selezionare **Cluster di condivisione file** come configurazione della condivisione Cluster in SWPM.  
3. Quando viene richiesto al passaggio **Parametri cluster**di sistema SAP , immettere il nome host per la condivisione SMB File NetApp di Azure già creata come Nome **host condivisione file**.  In questo esempio, il nome host della condivisione SMB è **anfsmb-9562**. 

> [!IMPORTANT]
> Se Lo stato di verifica obbligatorio Risultati in SWPM mostra la condizione della funzionalità Disponibilità continua non soddisfatta, è possibile risolverla seguendo le istruzioni nel [messaggio di errore Ritardato quando si tenta](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l)di accedere a una cartella condivisa che non esiste più in Windows .  

> [!TIP]
> Se Lo strumento Verifica prerequisito in SWPM mostra la condizione Dimensione swap non soddisfatta, è possibile regolare la dimensione SWAP passando a Risorse del computer>proprietà del sistema>Impostazioni prestazioni> Avanzate> memoria virtuale> Modifica.  

4. Configurare una risorsa `SAP-SID-IP` cluster SAP, la porta probe, tramite PowerShell.Configure an SAP cluster resource, the probe port, by using PowerShell. Eseguire questa configurazione su uno dei nodi del cluster SAP ASCS/SCS, come descritto in [Configure probe port](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#10822f4f-32e7-4871-b63a-9b86c76ce761).

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Installare un'istanza ASCS/SCS nel secondo nodo del cluster ASCS/SCS

1. Installare un'istanza SAP ASCS/SCS nel secondo nodo del cluster. Avviare lo strumento di installazione SAP SWPM, quindi passare a **Product** > **DBMS** > Installation > Application Server ABAP (o Java) >'istanza ASCS/SCS ad alta disponibilità > ASCS/SCS > nodo cluster aggiuntivo.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Installare un'istanza di DBMS e i server applicazioni SAP

Completare l'installazione SAP installando:

   * Un'istanza DBMS  
   * Un server applicazioni SAP primario  
   * Un server applicazioni SAP aggiuntivo  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testare il failover dell'istanza SAP ASCS/SCS 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Eseguire il failover dal nodo del cluster A al nodo del cluster B e viceversa
In questo scenario di test si farà riferimento al nodo del cluster sapascs1 come nodo A e al nodo del cluster sapascs2 come nodo B.

1. Verificare che le risorse del ![cluster siano in esecuzione sul nodo A. Figura 1: risorse cluster di failover di Windows Server in esecuzione sul nodo A prima del test di failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Riavviare il nodo del cluster A. Le risorse cluster SAP verranno ![spostate nel nodo del cluster B. Figura 2: risorse cluster di failover di Windows Server in esecuzione sul nodo B dopo il test di failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Test di immissione di blocco

1.Verificare che il server di replica SAP Enqueue (ERS) sia attivo  
2. Accedere al sistema SAP, eseguire la transazione SU01 e aprire un ID utente in modalità di modifica. Verrà generata la voce di blocco SAP.  
3. Quando si è connessi al sistema SAP, visualizzare la voce di blocco, passando alla transazione ST12.  
4. Eseguire il failover delle risorse ASCS dal nodo del cluster A al nodo del cluster B.  
5. Verificare che la voce di blocco, generata prima del failover delle risorse cluster SAP ASCS/SCS, venga mantenuta.  

![Figura 3: La voce di blocco viene mantenuta dopo il test di failoverFigure 3: Lock entry is retained after failover test](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Per altre informazioni, vedere [Risoluzione dei problemi per il failover di accodamento in ASCS con ERSFor](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS) more information, see Troubleshooting for Enqueue Failover in ASCS with ERS
## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* To learn how to establish high availability and plan for disaster recovery of SAP 
* HANA in Azure (istanze di grandi dimensioni), vedere [SAP HANA (istanze di grandi dimensioni) disponibilità elevata e ripristino di emergenza in Azure.HANA on Azure (large instances), see SAP HANA (large instances) high availability and disaster recovery on Azure.](hana-overview-high-availability-disaster-recovery.md)
* Per informazioni su come ottenere la disponibilità elevata e un piano di ripristino di emergenza di SAP HANA nelle macchine virtuali di Azure, vedere [Disponibilità elevata di SAP HANA nelle macchine virtuali di Azure (VM)][sap-hana-ha].
