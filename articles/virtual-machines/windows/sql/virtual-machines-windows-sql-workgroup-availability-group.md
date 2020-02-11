---
title: Configurare un gruppo di disponibilità del gruppo di lavoro indipendente dal dominio
description: Informazioni su come configurare un gruppo di disponibilità di gruppi di lavoro indipendente da Dominio di Active Directory Always On in una macchina virtuale SQL Server in Azure.
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
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122675"
---
# <a name="configure-a-workgroup-availability-group"></a>Configurare un gruppo di disponibilità del gruppo di lavoro 

Questo articolo illustra i passaggi necessari per creare un Active Directory cluster indipendente dal dominio con un gruppo di disponibilità Always On; Questa operazione è nota anche come cluster del gruppo di lavoro. Questo articolo è incentrato sui passaggi relativi alla preparazione e alla configurazione del gruppo di lavoro e del gruppo di disponibilità e illustra i passaggi trattati in altri articoli, ad esempio la modalità di creazione del cluster o la distribuzione del gruppo di disponibilità. 


## <a name="prerequisites"></a>Prerequisiti

Per configurare un gruppo di disponibilità del gruppo di lavoro, è necessario quanto segue:
- Almeno due macchine virtuali Windows Server 2016 (o versioni successive) che eseguono SQL Server 2016 (o versione successiva), distribuite nello stesso set di disponibilità o in diverse zone di disponibilità, usando indirizzi IP statici. 
- Una rete locale con almeno 4 indirizzi IP liberi nella subnet. 
- Un account in ogni computer del gruppo Administrator che dispone anche dei diritti di amministratore di sistema all'interno SQL Server. 
- Porte aperte: TCP 1433, TCP 5022, TCP 59999. 

Per riferimento, in questo articolo vengono usati i parametri seguenti, ma è possibile modificarli quando necessario: 

| **Nome** | **Parametro** |
| :------ | :---------------------------------- |
| **Node1**   | AGNode1 (10.0.0.4) |
| **Node2**   | AGNode2 (10.0.0.5) |
| **Nome cluster** | AGWGAG (10.0.0.6) |
| **Listener** | AGListener (10.0.0.7) | 
| **Suffisso DNS** | ag.wgcluster.example.com | 
| **Nome gruppo di lavoro** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>Imposta suffisso DNS 

In questo passaggio, configurare il suffisso DNS per entrambi i server. Ad esempio: `ag.wgcluster.example.com`. Questo consente di utilizzare il nome dell'oggetto a cui si desidera connettersi come indirizzo completo all'interno della rete, ad esempio `AGNode1.ag.wgcluster.example.com`. 

Per configurare il suffisso DNS, attenersi alla procedura seguente:

1. RDP nel primo nodo e aprire Server Manager. 
1. Selezionare **server locale** e quindi selezionare il nome della macchina virtuale in **nome computer**. 
1. Selezionare **Cambia..** . in **per rinominare il computer..** .. 
1. Modificare il nome del gruppo di lavoro in modo che sia significativo, ad esempio `AGWORKGROUP`: 

   ![Modificare il nome del gruppo di lavoro](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. Selezionare **altro...** per aprire la finestra di dialogo **suffisso DNS e nome computer NetBIOS** . 
1. Digitare il nome del suffisso DNS in **suffisso DNS primario del computer**, ad esempio `ag.wgcluster.example.com` e quindi selezionare **OK**: 

   ![Aggiungi suffisso DNS](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. Verificare che il **nome del computer completo** mostri ora il suffisso DNS, quindi selezionare **OK** per salvare le modifiche: 

   ![Aggiungi suffisso DNS](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. Riavviare il server quando richiesto. 
1. Ripetere questi passaggi in tutti gli altri nodi da usare per il gruppo di disponibilità. 

## <a name="edit-host-file"></a>Modifica file host

Poiché non esiste alcun Active Directory, non è possibile autenticare le connessioni di Windows. Di conseguenza, assegnare l'attendibilità modificando il file host con un editor di testo. 

Per modificare il file host, attenersi alla seguente procedura:

1. RDP nella macchina virtuale. 
1. Usare **Esplora file** per passare a `c:\windows\system32\drivers\etc`. 
1. Fare clic con il pulsante destro del mouse sul file **hosts** e aprire il file con il **blocco note** o qualsiasi altro editor di testo.
1. Alla fine del file, aggiungere una voce per ogni nodo, il gruppo di disponibilità e il listener nel formato `IP Address, DNS Suffix #comment`, ad esempio: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Aggiungere voci per l'indirizzo IP, il cluster e il listener al file host](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>Impostare autorizzazioni

Poiché non esistono Active Directory per gestire le autorizzazioni, è necessario consentire manualmente a un account amministratore locale non incorporato di creare il cluster. 

A tale scopo, eseguire il cmdlet di PowerShell seguente in una sessione di PowerShell amministrativa in ogni nodo: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Creare il cluster di failover

In questo passaggio verrà creato il cluster di failover di. Se non si ha familiarità con questi passaggi, è possibile seguirli dall' [esercitazione sul cluster di failover](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct).

Differenze rilevanti tra l'esercitazione e operazioni da eseguire per un cluster di gruppi di lavoro:
- Deselezionare **archiviazione**e **spazi di archiviazione diretta** quando si esegue la convalida del cluster. 
- Quando si aggiungono i nodi al cluster, aggiungere il nome completo, ad esempio:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Deselezionare **la casella di controllo Aggiungi tutte le archiviazioni idonee al cluster**. 

Una volta creato il cluster, assegnare un indirizzo IP del cluster statico. A tale scopo, eseguire la procedura seguente:

1. In uno dei nodi aprire **Gestione cluster di failover**, selezionare il cluster, fare clic con il pulsante destro del mouse sul **Nome: \<ClusterNam >** in **risorse principali del cluster** , quindi selezionare **Proprietà**. 

   ![Proprietà di avvio per il nome del cluster](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. Selezionare l'indirizzo IP in **indirizzi IP** e fare clic su **modifica**. 
1. Selezionare **USA statico**, specificare l'indirizzo IP del cluster e quindi fare clic su **OK**: 

   ![Fornire un indirizzo IP statico per il cluster](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. Verificare che le impostazioni siano corrette e quindi fare clic su **OK** per salvarle:

   ![Verificare le proprietà del cluster](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Creare un cloud di controllo 

In questo passaggio, configurare un server di controllo della condivisione cloud. Se non si ha familiarità con i passaggi, vedere l' [esercitazione sul cluster di failover](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness). 

## <a name="enable-availability-group-feature"></a>Abilitare la funzionalità gruppo di disponibilità 

In questo passaggio abilitare la funzionalità gruppo di disponibilità. Se non si ha familiarità con i passaggi, vedere l'esercitazione relativa al [gruppo di disponibilità](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificate"></a>Creazione di chiavi e certificati

In questo passaggio, creare i certificati utilizzati da un account di accesso SQL sull'endpoint crittografato. Creare una cartella in ogni nodo per conservare i backup dei certificati, ad esempio `c:\certs`. 

Per configurare il primo nodo, attenersi alla procedura seguente: 

1. Aprire **SQL Server Management Studio** e connettersi al primo nodo, ad esempio `AGNode1`. 
1. Aprire una **nuova** finestra di query ed eseguire l'istruzione Transact-SQL (T-SQL) seguente dopo l'aggiornamento a una password complessa e sicura:

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

1. Successivamente, creare l'endpoint HADR e usare il certificato per l'autenticazione eseguendo l'istruzione Transact-SQL (T-SQL):

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

1. Usare **Esplora file** per passare al percorso del file in cui si trova il certificato, ad esempio `c:\certs`. 
1. Eseguire manualmente una copia del certificato, ad esempio `AGNode1Cert.crt`, dal primo nodo e trasferirlo nello stesso percorso nel secondo nodo. 

Per configurare il secondo nodo, attenersi alla procedura seguente: 

1. Connettersi al secondo nodo con **SQL Server Management Studio**, ad esempio `AGNode2`. 
1. In una **nuova** finestra di query eseguire l'istruzione Transact-SQL (T-SQL) seguente dopo l'aggiornamento a una password complessa e sicura: 

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

1. Successivamente, creare l'endpoint HADR e usare il certificato per l'autenticazione eseguendo l'istruzione Transact-SQL (T-SQL):

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

1. Usare **Esplora file** per passare al percorso del file in cui si trova il certificato, ad esempio `c:\certs`. 
1. Eseguire manualmente una copia del certificato, ad esempio `AGNode2Cert.crt`, dal secondo nodo e trasferirlo nello stesso percorso del primo nodo. 

Se sono presenti altri nodi nel cluster, ripetere questi passaggi anche in questo caso, modificando i rispettivi nomi di certificato. 

## <a name="create-logins"></a>Crea account di accesso

L'autenticazione del certificato viene usata per sincronizzare i dati tra i nodi. Per consentire questa operazione, creare un account di accesso per l'altro nodo, creare un utente per l'account di accesso, creare un certificato per l'account di accesso per l'utilizzo del certificato di cui è stato eseguito il backup, quindi concedere la connessione nell'endpoint del mirroring. 

A tale scopo, eseguire prima la query Transact-SQL (T-SQL) seguente sul primo nodo, ad esempio `AGNode1`: 

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

Eseguire quindi la query Transact-SQL (T-SQL) seguente sul secondo nodo, ad esempio `AGNode2`: 

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

Se sono presenti altri nodi nel cluster, ripetere questi passaggi anche in questo caso, modificando il rispettivo certificato e i rispettivi nomi utente. 

## <a name="configure-availability-group"></a>Configurare un gruppo di disponibilità

In questo passaggio, configurare il gruppo di disponibilità e aggiungervi i database. Non creare un listener in questo momento. Se non si ha familiarità con i passaggi, vedere l'esercitazione relativa al [gruppo di disponibilità](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group). Assicurarsi di avviare un failover e un failback per verificare che tutto funzioni come dovrebbe essere. 

   > [!NOTE]
   > Se si verifica un errore durante il processo di sincronizzazione, potrebbe essere necessario concedere `NT AUTHORITY\SYSTEM` diritti sysadmin per creare risorse cluster sul primo nodo, ad esempio `AGNode1` temporaneamente. 

## <a name="configure-load-balancer"></a>Configurare il servizio di bilanciamento del carico

In questo passaggio finale configurare il servizio di bilanciamento del carico usando il [portale di Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md) o [PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)


## <a name="next-steps"></a>Passaggi successivi

È anche possibile usare [AZ SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) per configurare un gruppo di disponibilità. 


