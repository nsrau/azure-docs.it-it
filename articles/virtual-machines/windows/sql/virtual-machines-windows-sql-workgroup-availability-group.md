---
title: Configurare un gruppo di disponibilità del gruppo di lavoro indipendente dal dominioConfigure a domain-independent workgroup availability group availability group
description: Informazioni su come configurare un gruppo di disponibilità AlwaysOn di un gruppo di lavoro alwayson in una macchina virtuale di SQL Server in Azure.Learn how to configure an Active Directory Domain-independent workgroup Always On availability group on a SQL Server virtual machine in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 72c04cf5e3e5fbdeac2d267dfc7b2703bd37a1c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122675"
---
# <a name="configure-a-workgroup-availability-group"></a>Configurare un gruppo di disponibilità del gruppo di lavoroConfigure a workgroup availability group 

In questo articolo vengono illustrati i passaggi necessari per creare un cluster indipendente dal dominio di Active Directory con un gruppo di disponibilità AlwaysOnAlways On availability group. questo è noto anche come un cluster di gruppi di lavoro. Questo articolo è incentrato sui passaggi rilevanti per la preparazione e la configurazione del gruppo di lavoro e del gruppo di disponibilità e sui pulsanti di glossing sui passaggi descritti in altri articoli, ad esempio su come creare il cluster o distribuire il gruppo di disponibilità. 


## <a name="prerequisites"></a>Prerequisiti

Per configurare un gruppo di disponibilità del gruppo di lavoro, è necessario quanto segue:To configure a workgroup availability group, you need the following:
- Almeno due macchine virtuali Windows Server 2016 (o versioni successive) che eseguono SQL Server 2016 (o versione successiva), distribuite nello stesso set di disponibilità o zone di disponibilità diverse usando indirizzi IP statici. 
- Una rete locale con un minimo di 4 indirizzi IP liberi nella subnet. 
- Un account in ogni computer nel gruppo di amministratori che dispone anche di diritti sysadmin all'interno di SQL Server. 
- Porte aperte: TCP 1433, TCP 5022, TCP 59999. 

Per riferimento, i seguenti parametri vengono utilizzati in questo articolo, ma possono essere modificati in base alle esigenze:For reference, the following parameters are used in this article, but can be modified as is necessary: 

| **Nome** | **Parametro** |
| :------ | :---------------------------------- |
| **Nodo 1**   | AGNode1 (10.0.0.4) |
| **Nodo 2**   | AGNode2 (10.0.0.5) |
| **Nome cluster** | AGWGAG (10.0.0.6) |
| **Listener** | AGListener (10.0.0.7) | 
| **Suffisso DNS** | ag.wgcluster.example.com | 
| **Nome del gruppo di lavoro** | Gruppo AGWork | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>Imposta suffisso DNS 

In questo passaggio configurare il suffisso DNS per entrambi i server. Ad esempio: `ag.wgcluster.example.com`. In questo modo è possibile utilizzare il nome dell'oggetto a cui si `AGNode1.ag.wgcluster.example.com`desidera connettersi come indirizzo completo all'interno della rete, ad esempio . 

Per configurare il suffisso DNS, attenersi alla seguente procedura:

1. RDP nel primo nodo e aprire Server Manager. 
1. Selezionare **Server locale** e quindi selezionare il nome della macchina virtuale in **Nome computer**. 
1. Selezionare **Cambia...** in **Per rinominare il computer...**. 
1. Modificare il nome del gruppo di lavoro in `AGWORKGROUP`modo che sia significativo, ad esempio: 

   ![Modificare il nome del gruppo di lavoro](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. Selezionare **Altro...** per aprire la finestra di **dialogo Suffisso DNS e nome NetBIOS del computer.** 
1. Digitare il nome del suffisso DNS in **Suffisso DNS primario del computer**, ad `ag.wgcluster.example.com` esempio , quindi selezionare **OK:** 

   ![Aggiungi suffisso DNS](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. Verificare che il **nome completo** del computer sia ora visualizzato il suffisso DNS e quindi selezionare **OK** per salvare le modifiche: 

   ![Aggiungi suffisso DNS](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. Riavviare il server quando richiesto. 
1. Ripetere questi passaggi su tutti gli altri nodi da utilizzare per il gruppo di disponibilità. 

## <a name="edit-host-file"></a>Modifica file host

Poiché non è presente alcuna directory attiva, non è possibile autenticare le connessioni di Windows. Di conseguenza, assegnare l'attendibilità modificando il file host con un editor di testo. 

Per modificare il file host, attenersi alla seguente procedura:

1. RDP nella macchina virtuale. 
1. Utilizzare **Esplora file** `c:\windows\system32\drivers\etc`per passare a . 
1. Fare clic con il pulsante destro del mouse sul file **hosts** e aprire il file con **Blocco note** (o qualsiasi altro editor di testo).
1. Alla fine del file, aggiungere una voce per ogni nodo, il gruppo `IP Address, DNS Suffix #comment` di disponibilità e il listener sotto forma di simile:At the end of the file, add an entry for each node, the availability group, and the listener in the form of like: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Aggiungere voci per l'indirizzo IP, il cluster e il listener al file host](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>Impostare autorizzazioni

Poiché non è disponibile alcunactive Directory per gestire le autorizzazioni, è necessario consentire manualmente a un account amministratore locale non incorporato di creare il cluster. 

A tale scopo, eseguire il seguente cmdlet PowerShell in una sessione di PowerShell amministrativa su ogni nodo: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Creare il cluster di failover

In questo passaggio verrà creato il cluster di failover. Se non si ha familiarità con questi passaggi, è possibile seguirli [dall'esercitazione](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct)sul cluster di failover.

Notevoli differenze tra l'esercitazione e le operazioni da eseguire per un cluster di gruppi di lavoro:Notevol differences between the tutorial and what should be done for a workgroup cluster:
- Deselezionare **Archiviazione**e **Spazi di archiviazione diretta** durante l'esecuzione della convalida del cluster. 
- Quando si aggiungono i nodi al cluster, aggiungere il nome completo, ad esempio:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Deselezionare **Aggiungi tutte le risorse di archiviazione idonee al cluster**. 

Dopo aver creato il cluster, assegnare un indirizzo IP del cluster statico. A questo scopo, attenersi alla procedura seguente:

1. In uno dei nodi aprire **Gestione cluster**di failover , selezionare il cluster, fare clic con il pulsante destro del mouse sulla **>Nome: \<ClusterNam** in **Risorse principali cluster** e quindi scegliere **Proprietà**. 

   ![Proprietà di avvio per il nome del cluster](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. Selezionare l'indirizzo IP in **Indirizzi IP** e selezionare **Modifica**. 
1. Selezionare **Usa statico**, specificare l'indirizzo IP del cluster e quindi scegliere **OK**: 

   ![Fornire un indirizzo IP statico per il clusterProvide a static IP address for the cluster](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. Verificare che le impostazioni siano corrette e quindi selezionare **OK** per salvarle:

   ![Verificare le proprietà del clusterVerify cluster properties](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Creare un cloud di controllo 

In questo passaggio configurare un controllo di condivisione cloud. Se non si ha familiarità con la procedura, vedere [l'esercitazione](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness)sul cluster di failover . 

## <a name="enable-availability-group-feature"></a>Abilitare la funzionalità del gruppo di disponibilitàEnable availability group feature 

In questo passaggio abilitare la funzionalità del gruppo di disponibilità. Se non si ha familiarità con la procedura, vedere [l'esercitazione](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups)sul gruppo di disponibilità . 

## <a name="create-keys-and-certificate"></a>Creare chiavi e certificati

In questo passaggio creare i certificati utilizzati da un account di accesso SQL nell'endpoint crittografato. Creare una cartella in ogni nodo per contenere i backup del certificato, ad `c:\certs`esempio . 

Per configurare il primo nodo, attenersi alla seguente procedura: 

1. Aprire **SQL Server Management Studio** e connettersi `AGNode1`al primo nodo, ad esempio . 
1. Aprire una finestra **Nuova query** ed eseguire la seguente istruzione Transact-SQL (T-SQL) dopo l'aggiornamento a una password complessa e sicura:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Successivamente, creare l'endpoint HADR e utilizzare il certificato per l'autenticazione eseguendo questa istruzione Transact-SQL (T-SQL):Next, create the HADR endpoint, and use the certificate for authentication by running this Transact-SQL (T-SQL) statement:

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Utilizzare **Esplora file** per passare al percorso del `c:\certs`file in cui si trova il certificato, ad esempio . 
1. Creare manualmente una copia del `AGNode1Cert.crt`certificato, ad esempio , dal primo nodo e trasferirla nella stessa posizione sul secondo nodo. 

Per configurare il secondo nodo, attenersi alla seguente procedura: 

1. Connettersi al secondo nodo con **SQL Server Management StudioSQL Server Management Studio**, ad `AGNode2`esempio . 
1. In una finestra **Nuova query** eseguire la seguente istruzione Transact-SQL (T-SQL) dopo l'aggiornamento a una password complessa e sicura: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Successivamente, creare l'endpoint HADR e utilizzare il certificato per l'autenticazione eseguendo questa istruzione Transact-SQL (T-SQL):Next, create the HADR endpoint, and use the certificate for authentication by running this Transact-SQL (T-SQL) statement:

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Utilizzare **Esplora file** per passare al percorso del `c:\certs`file in cui si trova il certificato, ad esempio . 
1. Creare manualmente una copia del `AGNode2Cert.crt`certificato, ad esempio , dal secondo nodo e trasferirla nella stessa posizione nel primo nodo. 

Se nel cluster sono presenti altri nodi, ripetere questi passaggi, modificando anche i rispettivi nomi di certificato. 

## <a name="create-logins"></a>Creare account di accesso

L'autenticazione del certificato viene utilizzata per sincronizzare i dati tra i nodi. A tale scopo, creare un account di accesso per l'altro nodo, creare un utente per l'account di accesso, creare un certificato per l'account di accesso per utilizzare il certificato di cui è stato eseguito il backup e quindi concedere la connessione nell'endpoint del mirroring. 

A tale scopo, eseguire innanzitutto la query Transact-SQL (T-SQL) seguente nel primo nodo, ad `AGNode1`esempio: 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Eseguire quindi la query Transact-SQL (T-SQL) seguente nel `AGNode2`secondo nodo, ad esempio: 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Se nel cluster sono presenti altri nodi, ripetere questi passaggi, modificando anche il rispettivo certificato e i nomi utente. 

## <a name="configure-availability-group"></a>Configurare un gruppo di disponibilità

In questo passaggio configurare il gruppo di disponibilità e aggiungervi i database. Non creare un listener in questo momento. Se non si ha familiarità con la procedura, vedere [l'esercitazione](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group)sul gruppo di disponibilità . Assicurarsi di avviare un failover e un failback per verificare che tutto funzioni come dovrebbe. 

   > [!NOTE]
   > Se si verifica un errore durante il processo `NT AUTHORITY\SYSTEM` di sincronizzazione, potrebbe essere necessario concedere `AGNode1` i diritti sysadmin per creare le risorse cluster nel primo nodo, ad esempio temporaneamente. 

## <a name="configure-load-balancer"></a>Configurare il servizio di bilanciamento del caricoConfigure load

In questo passaggio finale configurare il servizio di bilanciamento del carico usando il portale di Azure o [PowerShellIn](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) this final step, configure the load balancer using either the [Azure portal](virtual-machines-windows-portal-sql-alwayson-int-listener.md) or PowerShell


## <a name="next-steps"></a>Passaggi successivi

È anche possibile usare [Az SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) per configurare un gruppo di disponibilità. 


