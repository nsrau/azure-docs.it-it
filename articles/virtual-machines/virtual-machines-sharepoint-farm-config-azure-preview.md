<properties
	pageTitle="Configurazione di Server Farm di SharePoint | Microsoft Azure"
	description="In questo articolo viene descritta la configurazione predefinita delle farm di SharePoint quando si utilizza la funzionalità della farm di SharePoint Server del portale di anteprima di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="josephd"/>


# Dettagli di configurazione della farm di SharePoint Server

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.
 

Farm di SharePoint Server è una funzionalità del portale di anteprima di Azure che crea automaticamente una farm di SharePoint Server 2013 preconfigurata. Ci sono due configurazioni della farm:

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
    -	Spazio degli indirizzi: 10.0.0.0/26

- Macchine virtuali:
	-	*HostNamePrefix*-DC (controller di dominio Servizi di dominio di Active Directory)
	-	*HostNamePrefix*-SQL (server SQL Server 2014)
	-	*HostNamePrefix*-SP (server SharePoint 2013)

- Controller di dominio:
	-	Immagine di macchina virtuale: Windows Server 2012 R2.
	-	Prefisso nome host: specificato durante la configurazione iniziale.
	-	Dimensione: A1 (impostazione predefinita).
	-	Nome di dominio: contoso.com (impostazione predefinita).
	-	Nome account amministratore di dominio: specificato durante la configurazione iniziale.
	-	Password account amministratore di dominio: specificata durante la configurazione iniziale.

- Server di database
	-	Immagine di macchina virtuale: SQL Server 2014 RTM Enterprise in Windows Server 2012 R2
	-	Prefisso nome host: specificato durante la configurazione iniziale.
	-	Dimensione: A5 (impostazione predefinita).
	-	Nome account di accesso al database: specificato durante la configurazione iniziale.
	-	Password account di accesso al database: specificata durante la configurazione iniziale.
	-	Nome account del servizio SQL Server: sqlservice (impostazione predefinita).
	-	Password account del servizio SQL Server: specificata durante la configurazione iniziale.

- SharePoint Server
	-	Immagine di macchina virtuale: versione di valutazione di SharePoint Server 2013.
	-	Prefisso nome host: specificato durante la configurazione iniziale.
	-	Dimensione: A2 (impostazione predefinita).
	-	Nome account della farm di SharePoint: sp\_farm (impostazione predefinita).
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
	-	Spazio degli indirizzi: 10.0.0.0/26

-	Macchine virtuali:
	-	*HostNamePrefix*-DC1 (controller di dominio Servizi di dominio di Active Directory)
	-	*HostNamePrefix*-DC2 (controller di dominio Servizi di dominio di Active Directory)
	-	*HostNamePrefix*-SQL1 (server SQL Server 2014)
	-	*HostNamePrefix*-SQL2 (server SQL Server 2014)
	-	*HostNamePrefix*-SQL0 (server Windows Server 2012 R2)
	-	*HostNamePrefix*-WEB1 (server SharePoint 2013)
	-	*HostNamePrefix*-WEB2 (server SharePoint 2013)
	-	*HostNamePrefix*-APP1 (server SharePoint 2013)
	-	*HostNamePrefix*-APP2 (server SharePoint 2013)

-	I controller di dominio:
	-	Immagine di macchina virtuale: Windows Server 2012 R2.
	-	Prefisso nome host: specificato durante la configurazione iniziale.
	-	Dimensione: A1 (impostazione predefinita).
	-	Nome di dominio: contoso.com (impostazione predefinita).
	-	Nome account amministratore di dominio: specificato durante la configurazione iniziale.
	-	Password account amministratore di dominio: specificata durante la configurazione iniziale.

-	Server di database:
	-	Immagine di macchina virtuale: SQL Server 2014 RTM Enterprise in Windows Server 2012 R2
	-	Prefisso nome host: specificato durante la configurazione iniziale.
	-	Dimensioni: A5 (impostazione predefinita) per server di database, A0 (impostazione predefinita) per il controllo della condivisione dei file (SQL0).
	-	Nome account di accesso al database: specificato durante la configurazione iniziale.
	-	Password account di accesso al database: specificata durante la configurazione iniziale.
	-	Nome account del servizio SQL Server: sqlservice (impostazione predefinita).
	-	Password account del servizio SQL Server: specificata durante la configurazione iniziale.

-	SharePoint Server:
	-	Immagine di macchina virtuale: versione di valutazione di SharePoint Server 2013.
	-	Prefisso nome host: specificato durante la configurazione iniziale.
	-	Dimensione: A2 (impostazione predefinita).
	-	Nome account della farm di SharePoint: sp\_farm (impostazione predefinita).
	-	Password account farm di SharePoint: specificata durante la configurazione iniziale.
	-	Passphrase farm di SharePoint: specificata durante la configurazione iniziale.

> [AZURE.NOTE]I server di SharePoint vengono creati dall'immagine di traccia di SharePoint Server 2013 versione di prova. Per continuare a utilizzare la macchina virtuale dopo la scadenza della versione di valutazione, è necessario convertire l'installazione per l'utilizzo di una chiave Retail o Volume License per le edizioni Standard o Enterprise di SharePoint Server 2013.

## Gestione risorse di Azure

La funzionalità di Farm di SharePoint Server del Portale di anteprima di Azure consente di creare macchine virtuali nella gestione dei servizi. Per creare farm di SharePoint Server 2013 in Gestione risorse, vedere [Distribuzione di Farm di SharePoint con i modelli di Gestione risorse di Azure](virtual-machines-workload-template-sharepoint.md).

## Risorse aggiuntive

[Farm di SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md)

[Farm di SharePoint ospitati nei servizi di infrastruttura di Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Configurazione di una farm Intranet di SharePoint in un cloud ibrido per l'esecuzione di test](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

<!---HONumber=Oct15_HO3-->