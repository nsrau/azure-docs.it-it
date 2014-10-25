<properties title="SharePoint Server Farm Configuration Details" pageTitle="SharePoint Server Farm Configuration Details" description="Describes the default configuration of SharePoint farms" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="josephd"></tags>

# Dettagli di configurazione della farm di SharePoint Server

Farm di SharePoint Server è una funzionalità del portale Microsoft Azure Preview che crea automaticamente una farm di SharePoint Server 2013 preconfigurata. Ci sono due configurazioni della farm:

-   Basic
-   Disponibilità elevata

Le sezioni seguenti illustrano i dettagli di configurazione per ogni farm.

Per altre informazioni, vedere l'articolo relativo alla [farm di SharePoint Server][farm di SharePoint Server].

## Farm di SharePoint di base

La farm di SharePoint di base è costituita da tre macchine virtuali nella configurazione seguente:

![sharepointfarm][sharepointfarm]

Ecco i dettagli di configurazione:

-   Sottoscrizione di Azure: specificata durante la configurazione iniziale.
-   Nomi di dominio di Azure (anche noti come servizi cloud): nomi di dominio distinti vengono creati automaticamente per ogni macchina virtuale.
-   Account di archiviazione: specificato durante la configurazione iniziale.
-   Rete virtuale

    -   Digitare: solo cloud
    -   Spazio di indirizzi: 192.168.16.0/26
-   Macchine virtuali

    -   HostNamePrefix-DC (controller di dominio Servizi di dominio Active Directory)
    -   HostNamePrefix-SQL (server SQL Server 2014)
    -   HostNamePrefix-SQL (server SharePoint 2013)
-   Controller di dominio

    -   Prefisso nome host: specificato durante la configurazione iniziale.
    -   Dimensione: A1 (impostazione predefinita)
    -   Nome di dominio: contoso.com (impostazione predefinita)
    -   Nome account amministratore di dominio: specificato durante la configurazione iniziale.
    -   Password account amministratore di dominio: specificata durante la configurazione iniziale.
-   SQL Server

    -   Prefisso nome host: specificato durante la configurazione iniziale.
    -   Dimensione: A5 (impostazione predefinita)
    -   Nome account di accesso al database: specificato durante la configurazione iniziale.
    -   Password account di accesso al database: specificata durante la configurazione iniziale.
    -   Nome account del servizio SQL Server: specificato durante la configurazione iniziale.
    -   Password account del servizio SQL Server: specificata durante la configurazione iniziale.
-   SharePoint Server

    -   Prefisso nome host: specificato durante la configurazione iniziale.
    -   Dimensione: A2 (impostazione predefinita)
    -   Nome account farm di SharePoint: specificato durante la configurazione iniziale.
    -   Password account farm di SharePoint: specificata durante la configurazione iniziale.
    -   Passphrase farm di SharePoint: specificata durante la configurazione iniziale.

## Disponibilità elevata

La farm di SharePoint a disponibilità elevata è costituita da nove macchine virtuali nella configurazione seguente:

![sharepointfarm][1]

Ecco i dettagli di configurazione:

-   Sottoscrizione di Azure: specificata durante la configurazione iniziale.
-   Nomi di dominio di Azure (anche noti come servizi cloud): nomi di dominio distinti vengono creati automaticamente per ogni macchina virtuale.
-   Account di archiviazione: specificato durante la configurazione iniziale.
-   Rete virtuale

    -   Digitare: solo cloud
    -   Spazio di indirizzi: 192.168.16.0/26
-   Macchine virtuali

    -   HostNamePrefix-DC1 (controller di dominio Servizi di dominio Active Directory)
    -   HostNamePrefix-DC2 (controller di dominio Servizi di dominio Active Directory)
    -   HostNamePrefix-SQL1 (server SQL Server 2014)
    -   HostNamePrefix-SQL2 (server SQL Server 2014)
    -   HostNamePrefix-SQL0 (server SQL Server 2014)
    -   HostNamePrefix-WEB1 (server SharePoint 2013)
    -   HostNamePrefix-WEB2 (server SharePoint 2013)
    -   HostNamePrefix-APP1 (server SharePoint 2013)
    -   HostNamePrefix-APP2 (server SharePoint 2013)
-   Controller di dominio

    -   Prefisso nome host: specificato durante la configurazione iniziale.
    -   Dimensione: A1 (impostazione predefinita)
    -   Nome di dominio: contoso.com (impostazione predefinita)
    -   Nome account amministratore di dominio: specificato durante la configurazione iniziale.
    -   Password account amministratore di dominio: specificata durante la configurazione iniziale.
-   Server di SQL

    -   Prefisso nome host: specificato durante la configurazione iniziale.
    -   Dimensione: A5 (impostazione predefinita)
    -   Nome account di accesso al database: specificato durante la configurazione iniziale.
    -   Password account di accesso al database: specificata durante la configurazione iniziale.
    -   Nome account del servizio SQL Server: specificato durante la configurazione iniziale.
    -   Password account del servizio SQL Server: specificata durante la configurazione iniziale.
-   SharePoint Server

    -   Prefisso nome host: specificato durante la configurazione iniziale.
    -   Dimensione: A2 (impostazione predefinita)
    -   Nome account farm di SharePoint: specificato durante la configurazione iniziale.
    -   Password account farm di SharePoint: specificata durante la configurazione iniziale.
    -   Passphrase farm di SharePoint: specificata durante la configurazione iniziale.

  [farm di SharePoint Server]: ../virtual-machines-sharepoint-farm-azure-preview/
  [sharepointfarm]: ./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png
  [1]: ./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png
