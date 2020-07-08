---
title: Configurare un gruppo di disponibilità del gruppo di lavoro indipendente dal dominio
description: Informazioni su come configurare un gruppo di disponibilità Active Directory gruppo di lavoro indipendente dal dominio Always On in una macchina virtuale SQL Server in Azure.
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
ms.openlocfilehash: 93819332def05022272eabc130e0f2240938f244
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955506"
---
# <a name="configure-a-workgroup-availability-group"></a>Configurare un gruppo di disponibilità del gruppo di lavoro 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo illustra i passaggi necessari per creare un cluster indipendente dal dominio di Active Directory con un gruppo di disponibilità Always On; questa operazione è nota anche come cluster del gruppo di lavoro. Questo articolo è incentrato sui passaggi relativi alla preparazione e alla configurazione del gruppo di lavoro e del gruppo di disponibilità e sorvola sui passaggi trattati in altri articoli, ad esempio la modalità di creazione del cluster o la distribuzione del gruppo di disponibilità. 


## <a name="prerequisites"></a>Prerequisiti

Per configurare un gruppo di disponibilità del gruppo di lavoro, è necessario quanto segue:
- Almeno due macchine virtuali Windows Server 2016 (o versioni successive) che eseguono SQL Server 2016 (o versione successiva), distribuite nello stesso set di disponibilità o in diverse zone di disponibilità, usando indirizzi IP statici. 
- Una rete locale con almeno 4 indirizzi IP liberi nella subnet. 
- Un account in ogni computer del gruppo di amministratori che dispone anche dei diritti di amministratore di sistema all'interno di SQL Server. 
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

## <a name="set-a-dns-suffix"></a>Impostare un suffisso DNS 

In questo passaggio, configurare il suffisso DNS per entrambi i server. Ad esempio: `ag.wgcluster.example.com`. Questo consente di usare il nome dell'oggetto a cui si desidera connettersi come indirizzo completo all'interno della rete, ad esempio `AGNode1.ag.wgcluster.example.com`. 

Seguire questa procedura per configurare il suffisso DNS:

1. Connettersi tramite RDP al primo nodo e aprire Server Manager. 
1. Selezionare **Server locale** quindi selezionare il nome della macchina virtuale in **Nome computer**. 
1. Selezionare **Modifica...** in **Per rinominare il computer...** . 
1. Modificare il nome del gruppo di lavoro in modo che sia significativo, ad esempio `AGWORKGROUP`: 

   ![Modificare il nome del gruppo di lavoro](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. Selezionare **Altro...** per aprire la finestra di dialogo **Suffisso DNS e nome NetBIOS del computer**. 
1. Digitare il nome del suffisso DNS in **Suffisso DNS primario del computer**, ad esempio `ag.wgcluster.example.com`, quindi selezionare **OK**: 

   ![Aggiungere il suffisso DNS](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. Verificare che il **Nome completo del computer** ora mostri il suffisso DNS, quindi selezionare **OK** per salvare le modifiche: 

   ![Aggiungere il suffisso DNS](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. Riavviare il server quando richiesto. 
1. Ripetere questi passaggi in tutti gli altri nodi da usare per il gruppo di disponibilità. 

## <a name="edit-a-host-file"></a>Modificare un file host

Poiché non esiste alcuna Active Directory, non è possibile autenticare le connessioni di Windows. Di conseguenza, assegnare l'attendibilità modificando il file host con un editor di testo. 

Per modificare il file host, attenersi alla procedura seguente:

1. Connettersi tramite RDP alla macchina virtuale. 
1. Usare **Esplora file** per andare a `c:\windows\system32\drivers\etc`. 
1. Fare clic con il pulsante destro del mouse sul file **host** e aprire il file con **Blocco note** (o qualsiasi altro editor di testo).
1. Alla fine del file, aggiungere una voce per ogni nodo, il gruppo di disponibilità e il listener nel formato `IP Address, DNS Suffix #comment`, ad esempio: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Aggiungere voci per l'indirizzo IP, il cluster e il listener nel file host](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>Impostare autorizzazioni

Poiché non esistono Active Directory per gestire le autorizzazioni, è necessario consentire manualmente a un account amministratore locale non incorporato di creare il cluster. 

A tale scopo, eseguire il cmdlet di PowerShell seguente in una sessione di PowerShell amministrativa in ogni nodo: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Creare il cluster di failover

In questo passaggio si creerà il cluster di failover. Se non si ha familiarità con questi passaggi, è possibile seguirli dall'[esercitazione sul cluster di failover](failover-cluster-instance-storage-spaces-direct-manually-configure.md).

Differenze rilevanti tra l'esercitazione e le operazioni da eseguire per un cluster di gruppi di lavoro:
- Deselezionare **Archiviazione** e **Spazi di archiviazione diretta** quando si esegue la convalida del cluster. 
- Quando si aggiungono i nodi al cluster, aggiungere il nome completo, ad esempio:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Deselezionare **Aggiungi tutte le risorse di archiviazione idonee al cluster**. 

Una volta creato il cluster, assegnare un indirizzo IP del cluster statico. A questo scopo, attenersi alla procedura seguente:

1. Su uno dei nodi, aprire **Gestione cluster di failover**, selezionare il cluster, fare clic con il pulsante destro del mouse su **Nome: \<ClusterNam>** in **Risorse principali del cluster** e quindi selezionare **Proprietà**. 

   ![Avviare le proprietà per il nome del cluster](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. Selezionare l'indirizzo IP in **Indirizzi IP** e selezionare **Modifica**. 
1. Selezionare **Usa statico**, specificare l'indirizzo IP del cluster e quindi selezionare **OK**: 

   ![Fornire un indirizzo IP statico per il cluster](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. Verificare che le impostazioni siano corrette e quindi selezionare **OK** per salvarle:

   ![Verificare le proprietà del cluster](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Creare un cloud di controllo 

In questo passaggio, configurare un controllo della condivisione cloud. Se non si ha familiarità con i passaggi, vedere [distribuire un cloud di controllo per un cluster di failover](/windows-server/failover-clustering/deploy-cloud-witness). 

## <a name="enable-the-availability-group-feature"></a>Abilitare la funzionalità gruppo di disponibilità 

In questo passaggio, abilitare la funzionalità del gruppo di disponibilità. Se non si ha familiarità con i passaggi, vedere l'[esercitazione sul cluster di failover](availability-group-manually-configure-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificates"></a>Creazione di chiavi e certificati

In questo passaggio, creare i certificati usati da un account di accesso SQL sull'endpoint crittografato. Creare una cartella in ogni nodo per conservare i backup dei certificati, ad esempio `c:\certs`. 

Per configurare il primo nodo, effettuare le operazioni seguenti: 

1. Aprire **SQL Server Management Studio** e connettersi al primo nodo, ad esempio `AGNode1`. 
1. Aprire la finestra **Nuova query** ed eseguire l'istruzione Transact-SQL (T-SQL) seguente dopo l'aggiornamento a una password complessa e sicura:

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

1. Usare **Esplora file** per andare al percorso del file in cui si trova il certificato, ad esempio `c:\certs`. 
1. Eseguire manualmente una copia del certificato, ad esempio `AGNode1Cert.crt`, dal primo nodo e trasferirlo nello stesso percorso nel secondo nodo. 

Per configurare il secondo nodo, effettuare le operazioni seguenti: 

1. Connettersi al secondo nodo con **SQL Server Management Studio**, ad esempio `AGNode2`. 
1. In una finestra **Nuova query** eseguire l'istruzione Transact-SQL (T-SQL) seguente dopo l'aggiornamento a una password complessa e sicura: 

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

1. Usare **Esplora file** per andare al percorso del file in cui si trova il certificato, ad esempio `c:\certs`. 
1. Eseguire manualmente una copia del certificato, ad esempio `AGNode2Cert.crt`, dal secondo nodo e trasferirlo nello stesso percorso nel primo nodo. 

Se sono presenti altri nodi nel cluster, ripetere questi passaggi anche in questo caso, modificando i rispettivi nomi di certificato. 

## <a name="create-logins"></a>Creare account di accesso

L'autenticazione del certificato viene usata per sincronizzare i dati tra i nodi. Per consentire questa operazione, creare un account di accesso per l'altro nodo, creare un utente per l'account di accesso, creare un certificato per l'account di accesso per usare il certificato di cui è stato eseguito il backup, quindi concedere la connessione nell'endpoint del mirroring. 

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

Successivamente, eseguire la query Transact-SQL (T-SQL) seguente sul secondo nodo, ad esempio `AGNode2`: 

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

Se sono presenti altri nodi nel cluster, ripetere questi passaggi anche in questo caso, modificando i rispettivi nomi di certificato e utente. 

## <a name="configure-an-availability-group"></a>Configurare un gruppo di disponibilità

In questo passaggio, configurare il gruppo di disponibilità e aggiungervi i database. Non creare un listener in questo momento. Se non si ha familiarità con i passaggi, vedere l'[esercitazione sul gruppo di disponibilità](availability-group-manually-configure-tutorial.md#create-the-availability-group). Assicurarsi di avviare un failover e un failback per verificare che tutto funzioni come dovrebbe. 

   > [!NOTE]
   > Se si verifica un errore durante il processo di sincronizzazione, potrebbe essere necessario concedere diritti di amministratore di sistema `NT AUTHORITY\SYSTEM` per creare risorse cluster sul primo nodo, ad esempio `AGNode1` temporaneamente. 

## <a name="configure-a-load-balancer"></a>Configurare un servizio di bilanciamento del carico

In questo passaggio finale configurare il servizio di bilanciamento del carico usando il [portale di Azure](availability-group-load-balancer-portal-configure.md) o [PowerShell](availability-group-listener-powershell-configure.md).


## <a name="next-steps"></a>Passaggi successivi

È anche possibile usare [AZ SQL VM CLI](availability-group-az-cli-configure.md) per configurare un gruppo di disponibilità. 


