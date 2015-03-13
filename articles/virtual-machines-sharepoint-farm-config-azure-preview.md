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
	ms.date="1/26/2015" 
	ms.author="josephd"/>


#Dettagli di configurazione della farm di SharePoint Server#

Farm di SharePoint Server è una funzionalità del portale Microsoft Azure Preview che crea automaticamente una farm di SharePoint Server 2013 preconfigurata. Ci sono due configurazioni della farm:

- Basic
- Disponibilità elevata

Le sezioni seguenti illustrano i dettagli di configurazione per ogni farm.

Per altre informazioni, vedere [Farm di SharePoint Server](../virtual-machines-sharepoint-farm-azure-preview/).

##Farm di SharePoint di base##

La farm di SharePoint di base è costituita da tre macchine virtuali in questa configurazione:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png) 

Ecco i dettagli di configurazione:

-	Sottoscrizione di Azure: specificata durante la configurazione iniziale.
-	Nomi di dominio di Azure (anche noti come servizi cloud): nomi di dominio distinti vengono creati automaticamente per ogni macchina virtuale.
-	Account di archiviazione: specificata durante la configurazione iniziale.
-	Rete virtuale 	
	-   Tipo: solo cloud	
    -	Spazio di indirizzi: 192.168.16.0/26    

- Macchine virtuali
	-	*HostNamePrefix*-DC (controller di dominio Servizi di dominio Active Directory)
	-	*HostNamePrefix*-SQL (server SQL Server 2014)
	-	*HostNamePrefix*-SP (server SharePoint 2013)

- Controller di dominio
	-	Prefisso nome host: specificata durante la configurazione iniziale.
	-	Dimensione: A1 (impostazione predefinita)
	-	Nome di dominio: contoso.com (impostazione predefinita)
	-	Nome account amministratore di dominio: specificata durante la configurazione iniziale.
	-	Password account amministratore di dominio: specificata durante la configurazione iniziale.

- SQL Server
	-	Prefisso nome host: specificata durante la configurazione iniziale.
	-	Dimensione: A5 (impostazione predefinita)
	-	Nome account di accesso al database: specificata durante la configurazione iniziale.
	-	Password account di accesso al database: specificata durante la configurazione iniziale.
	-	Nome account del servizio SQL Server: specificata durante la configurazione iniziale.
	-	Password account del servizio SQL Server: specificata durante la configurazione iniziale.

- SharePoint Server
	-	Prefisso nome host: specificata durante la configurazione iniziale.
	-	Dimensione: A2 (impostazione predefinita)
	-	Nome account farm di SharePoint: specificata durante la configurazione iniziale.
	-	Password account farm di SharePoint: specificata durante la configurazione iniziale.
	-	Passphrase farm di SharePoint: specificata durante la configurazione iniziale.


##Farm di SharePoint a disponibilità elevata##

La farm di SharePoint a disponibilità elevata è costituita da nove macchine virtuali in questa configurazione.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)
 
Ecco i dettagli di configurazione:

-	Sottoscrizione di Azure: specificata durante la configurazione iniziale.
-	Nomi di dominio di Azure (anche noti come servizi cloud): nomi di dominio distinti vengono creati come raffigurato nella figura riportata sopra.
-	Account di archiviazione: specificata durante la configurazione iniziale.
-	Rete virtuale	
	-	Tipo: solo cloud
	-	Spazio di indirizzi: 192.168.16.0/26	

-	Macchine virtuali
	-	*HostNamePrefix*-DC1 (controller di dominio Servizi di dominio Active Directory)
	-	*HostNamePrefix*-DC2 (controller di dominio Servizi di dominio Active Directory)
	-	*HostNamePrefix*-SQL1 (server SQL Server 2014)
	-	*HostNamePrefix*-SQL2 (server SQL Server 2014)
	-	*HostNamePrefix*-SQL0 (server Windows Server 2012 R2)
	-	*HostNamePrefix*-WEB1 (server SharePoint 2013)
	-	*HostNamePrefix*-WEB2 (server SharePoint 2013)
	-	*HostNamePrefix*-APP1 (server SharePoint 2013)
	-	*HostNamePrefix*-APP2 (server SharePoint 2013)

-	Controller di dominio
	-	Prefisso nome host: specificata durante la configurazione iniziale.
	-	Dimensione: A1 (impostazione predefinita)
	-	Nome di dominio: contoso.com (impostazione predefinita)
	-	Nome account amministratore di dominio: specificata durante la configurazione iniziale.
	-	Password account amministratore di dominio: specificata durante la configurazione iniziale.

-	Server di SQL
	-	Prefisso nome host: specificata durante la configurazione iniziale.
	-	Dimensione: A5 (impostazione predefinita)
	-	Nome account di accesso al database: specificata durante la configurazione iniziale.
	-	Password account di accesso al database: specificata durante la configurazione iniziale.
	-	Nome account del servizio SQL Server: specificata durante la configurazione iniziale.
	-	Password account del servizio SQL Server: specificata durante la configurazione iniziale.

-	SharePoint Server
	-	Prefisso nome host: specificata durante la configurazione iniziale.
	-	Dimensione: A2 (impostazione predefinita)
	-	Nome account farm di SharePoint: specificata durante la configurazione iniziale.
	-	Password account farm di SharePoint: specificata durante la configurazione iniziale.		
	-	Passphrase farm di SharePoint: specificata durante la configurazione iniziale.

##Risorse aggiuntive##

[Farm di SharePoint Server](../virtual-machines-sharepoint-farm-azure-preview/)

[SharePoint nei servizi infrastruttura di Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)


<!--HONumber=42-->
