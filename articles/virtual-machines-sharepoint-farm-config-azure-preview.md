<properties 
	pageTitle="Dettagli di configurazione della farm di SharePoint Server" 
	description="Descrive la configurazione predefinita delle farm di SharePoint." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-sharepoint" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="josephd"/>


# Dettagli di configurazione della farm di SharePoint Server

Farm di SharePoint Server è una funzionalità del portale Microsoft Azure Preview che crea automaticamente una farm di SharePoint Server 2013 preconfigurata. Ci sono due configurazioni della farm:

- Basic
- Disponibilità elevata

Le sezioni seguenti illustrano i dettagli di configurazione per ogni farm.

Per altre informazioni, vedere l'articolo relativo alla [farm di SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md).

## Farm di SharePoint di base

La farm di SharePoint di base è costituita da tre macchine virtuali in questa configurazione:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png)

Ecco i dettagli di configurazione:

-	Sottoscrizione di Azure: specificata durante la configurazione iniziale.
-	Nomi di dominio di Azure (noti anche come servizi cloud): nomi di dominio distinti vengono creati automaticamente per ogni macchina virtuale.
-	Account di archiviazione: specificato durante la configurazione iniziale.
-	Rete virtuale 	
	-   Tipo: solo cloud	
    -	Spazio degli indirizzi: 192.168.16.0/26    

- Macchine virtuali
	-	*HostNamePrefix*-DC (controller di dominio Servizi di dominio di Active Directory)
	-	*HostNamePrefix*-SQL (server SQL Server 2014)
	-	*HostNamePrefix*-SP (server SharePoint 2013)

- Controller di dominio
	-	Immagine di macchina virtuale: Windows Server 2012 R2.
	-	Prefisso nome host: specificato durante la configurazione iniziale.
	-	Dimensione: A1 (impostazione predefinita)
	-	Nome di dominio: contoso.com (impostazione predefinita)
	-	Nome account amministratore di dominio: specificato durante la configurazione iniziale.
	-	Password account amministratore di dominio: specificata durante la configurazione iniziale.

- SQL Server
	-	Immagine di macchina virtuale: SQL Server 2014 RTM Enterprise in Windows Server 2012 R2
	-	Prefisso nome host: specificato durante la configurazione iniziale.
	-	Dimensione: A5 (impostazione predefinita)
	-	Nome account di accesso al database: specificato durante la configurazione iniziale.
	-	Password account di accesso al database: specificata durante la configurazione iniziale.
	-	Nome account del servizio SQL Server: specificato durante la configurazione iniziale.
	-	Password account del servizio SQL Server: specificata durante la configurazione iniziale.

- SharePoint Server
	-	Immagine di macchina virtuale: versione di valutazione di SharePoint Server 2013.
	-	Prefisso nome host: specificato durante la configurazione iniziale.
	-	Dimensione: A2 (impostazione predefinita)
	-	Nome account farm di SharePoint: specificato durante la configurazione iniziale.
	-	Password account farm di SharePoint: specificata durante la configurazione iniziale.
	-	Passphrase farm di SharePoint: specificata durante la configurazione iniziale.


## Farm di SharePoint a disponibilità elevata

La farm di SharePoint a disponibilità elevata è costituita da nove macchine virtuali in questa configurazione.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)
 
Ecco i dettagli di configurazione:

-	Sottoscrizione di Azure: specificata durante la configurazione iniziale.
-	Nomi di dominio di Azure (noti anche come servizi cloud): nomi di dominio distinti vengono creati in base alla figura riportata sopra.
-	Account di archiviazione: specificato durante la configurazione iniziale.
-	Rete virtuale	
	-	Tipo: solo cloud
	-	Spazio degli indirizzi: 192.168.16.0/26	

-	Macchine virtuali
	-	*HostNamePrefix*-DC1 (controller di dominio Servizi di dominio di Active Directory)
	-	*HostNamePrefix*-DC2 (controller di dominio Servizi di dominio di Active Directory)
	-	*HostNamePrefix*-SQL1 (server SQL Server 2014)
	-	*HostNamePrefix*-SQL2 (server SQL Server 2014)
	-	*HostNamePrefix*-SQL0 (server Windows Server 2012 R2)
	-	*HostNamePrefix*-WEB1 (server SharePoint 2013)
	-	*HostNamePrefix*-WEB2 (server SharePoint 2013)
	-	*HostNamePrefix*-APP1 (server SharePoint 2013)
	-	*HostNamePrefix*-APP2 (server SharePoint 2013)

-	Controller di dominio
	-	Immagine di macchina virtuale: Windows Server 2012 R2.
	-	Prefisso nome host: specificato durante la configurazione iniziale.
	-	Dimensione: A1 (impostazione predefinita)
	-	Nome di dominio: contoso.com (impostazione predefinita)
	-	Nome account amministratore di dominio: specificato durante la configurazione iniziale.
	-	Password account amministratore di dominio: specificata durante la configurazione iniziale.

-	Server di SQL
	-	Immagine di macchina virtuale: SQL Server 2014 RTM Enterprise in Windows Server 2012 R2
	-	Prefisso nome host: specificato durante la configurazione iniziale.
	-	Dimensione: A5 (impostazione predefinita)
	-	Nome account di accesso al database: specificato durante la configurazione iniziale.
	-	Password account di accesso al database: specificata durante la configurazione iniziale.
	-	Nome account del servizio SQL Server: specificato durante la configurazione iniziale.
	-	Password account del servizio SQL Server: specificata durante la configurazione iniziale.

-	SharePoint Server
	-	Immagine di macchina virtuale: versione di valutazione di SharePoint Server 2013.
	-	Prefisso nome host: specificato durante la configurazione iniziale.
	-	Dimensione: A2 (impostazione predefinita)
	-	Nome account farm di SharePoint: specificato durante la configurazione iniziale.
	-	Password account farm di SharePoint: specificata durante la configurazione iniziale.		
	-	Passphrase farm di SharePoint: specificata durante la configurazione iniziale.

> [AZURE.NOTE]I server di SharePoint vengono creati dall'immagine di traccia di SharePoint Server 2013. Per continuare a utilizzare la macchina virtuale dopo la scadenza della versione di valutazione, è necessario convertire l'installazione per l'utilizzo di una chiave Retail o Volume License per le edizioni Standard o Enterprise di SharePoint Server 2013.

## Risorse aggiuntive

[Farm di SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md)

[SharePoint nei servizi infrastruttura di Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[Configurazione di una farm Intranet di SharePoint in un cloud ibrido per l'esecuzione di test](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

<!---HONumber=58-->