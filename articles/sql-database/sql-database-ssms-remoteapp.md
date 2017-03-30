---
title: Connettersi al database SQL usando SQL Server Management Studio in Azure RemoteApp | Documentazione Microsoft
description: Questa esercitazione spiega come imparare a usare SQL Server Management Studio in Azure RemoteApp per la sicurezza e le prestazioni durante la connessione al database SQL
services: sql-database
documentationcenter: 
author: adhurwit
manager: jhubbard
ms.assetid: 1052c83c-e7f5-4736-922f-216194d8874b
ms.service: sql-database
ms.custom: overview
ms.workload: data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: adhurwit
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9916eb7deaae6acb784cac1b7b64d93a03f3c590
ms.lasthandoff: 03/25/2017


---
# <a name="use-sql-server-management-studio-in-azure-remoteapp-to-connect-to-sql-database"></a>Usare SQL Server Management Studio in Azure RemoteApp per connettersi al database SQL

> [!IMPORTANT]
> Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
>

## <a name="introduction"></a>Introduzione
Questa esercitazione illustra come usare SQL Server Management Studio (SSMS) in Azure RemoteApp per connettersi al database SQL. Descrive il processo di installazione di SQL Server Management Studio in Azure RemoteApp, ne spiega i vantaggi e illustra le funzionalità di sicurezza che è possibile usare in Azure Active Directory.

**Tempo previsto per il completamento:** 45 minuti

## <a name="ssms-in-azure-remoteapp"></a>SSMS in Azure RemoteApp
Azure RemoteApp è un servizio di Servizi desktop remoto di Azure che fornisce applicazioni. Per altre informazioni, vedere: [Informazioni su Azure RemoteApp](../remoteapp/remoteapp-whatis.md)

SSMS eseguito in Azure RemoteApp offre la stessa esperienza dell'esecuzione in locale.

![Schermata di SSMS in esecuzione in Azure RemoteApp][1]

## <a name="benefits"></a>Vantaggi
Ecco alcuni dei molti vantaggi che derivano dall'uso di SSMS in Azure RemoteApp:

* La porta 1433 sul server SQL di Azure non deve essere esposta esternamente (all'esterno di Azure).
* Non è necessario continuare ad aggiungere e rimuovere indirizzi IP nel firewall del server SQL di Azure.
* Tutte le connessioni di Azure RemoteApp avvengono tramite HTTPS sulla porta 443 con Remote Desktop Protocol crittografato.
* È multiutente e scalabile.
* Offre un miglioramento delle prestazioni grazie all'esecuzione di SSMS nella stessa area del database SQL.
* È possibile controllare l'uso di Azure RemoteApp con la Premium Edition di Azure Active Directory che include report sull'attività utente.
* È possibile abilitare Multi-Factor Authentication (MFA).
* Possibilità di accesso a SSMS ovunque, usando uno dei client di Azure RemoteApp supportati che include iOS, Android, Mac, Windows Phone e PC Windows.

## <a name="create-the-azure-remoteapp-collection"></a>Creare la raccolta di Azure RemoteApp
Ecco i passaggi per creare la raccolta Azure RemoteApp con SSMS:

### <a name="1-create-a-new-windows-vm-from-image"></a>1. Creare una nuova VM Windows da immagine
Per creare la nuova VM, usare l'immagine "Host sessione Desktop remoto di Windows Server 2012 R2" dalla raccolta.

### <a name="2-install-ssms-from-sql-express"></a>2. Installare SSMS da SQL Express
Nella nuova VM passare alla pagina di download: [Microsoft® SQL Server® 2014 Express](https://www.microsoft.com/en-us/download/details.aspx?id=42299)

È disponibile un'opzione per scaricare solo SSMS. Dopo il download, passare alla directory di installazione ed eseguire il programma di installazione di SSMS.

È necessario installare anche SQL Server 2014 Service Pack 1. Scaricarlo qui: [Microsoft SQL Server 2014 Service Pack 1 (SP1)](https://www.microsoft.com/en-us/download/details.aspx?id=46694)

SQL Server 2014 Service Pack 1 include le funzionalità essenziali per l'utilizzo del database SQL di Azure.

### <a name="3-run-validate-script-and-sysprep"></a>3. Eseguire lo script di convalida e Sysprep
Sul desktop della VM è disponibile uno script di PowerShell denominato Validate. Fare doppio clic sullo script per eseguirlo. Verificherà che la VM è pronta all'uso per l'hosting remoto di applicazioni. Una volta completata la verifica, verrà richiesto di eseguire sysprep. Scegliere di eseguirlo.

Al termine dell'esecuzione di sysprep, la VM viene chiusa.

Per altre informazioni sulla creazione di un'immagine di Azure RemoteApp, vedere l'articolo relativo alla [creazione di un'immagine modello di RemoteApp in Azure](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx)

### <a name="4-capture-image"></a>4. Acquisire l'immagine
Una volta interrotta l'esecuzione della VM, trovarla nel portale corrente e acquisirla.

Per altre informazioni sull'acquisizione di un'immagine, vedere [Acquisire un'immagine di una macchina virtuale Windows di Azure creata con il modello di distribuzione classica](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="5-add-to-azure-remoteapp-template-images"></a>5. Aggiungere immagini modello di Azure RemoteApp
Nella sezione Azure RemoteApp del portale corrente passare alla scheda Immagini modello e fare clic su Aggiungi. Nella finestra popup selezionare "Importare un'immagine dalla raccolta di macchine virtuali" e quindi scegliere l'immagine appena creata.

### <a name="6-create-cloud-collection"></a>6. Creare una raccolta nel cloud
Nel portale corrente creare una nuova raccolta nel cloud di Azure RemoteApp. Scegliere l'immagine modello appena importata con installato SSMS.

![Creare una nuova raccolta nel cloud][2]

### <a name="7-publish-ssms"></a>7. Pubblicare SSMS
Nella scheda Pubblicazione della nuova raccolta nel cloud selezionare l'opzione per pubblicare un'applicazione dal menu Start e quindi scegliere SSMS dall'elenco.

![Pubblicare l'app][5]

### <a name="8-add-users"></a>8. Aggiungere utenti
Nella scheda Accesso utente è possibile selezionare gli utenti che avranno accesso a questa raccolta di Azure RemoteApp che include solo SSMS.

![Aggiunta di un utente][6]

### <a name="9-install-the-azure-remoteapp-client-application"></a>9. Installare l'applicazione client Azure RemoteApp
Scaricare e installare un client di Azure RemoteApp qui: [Scaricare | Azure RemoteApp](https://www.remoteapp.windowsazure.com/en/clients.aspx)

## <a name="configure-azure-sql-server"></a>Configurare il server SQL di Azure
La sola configurazione necessaria consiste nel verificare che Servizi di Azure sia abilitato per il firewall. Se si usa questa soluzione, non è necessario aggiungere alcun indirizzo IP per aprire il firewall. Il traffico di rete per SQL Server è consentito solo da altri servizi di Azure.

![Consentire Azure][4]

## <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)
MFA può essere abilitata appositamente per questa applicazione. Passare alla scheda Applicazioni di Azure Active Directory. È disponibile una voce per Microsoft Azure RemoteApp. Se si fa clic su questa applicazione e quindi la si configura, verrà visualizzata la pagina seguente in cui è possibile abilitare MFA per l'applicazione.

![Abilitare MFA][3]

## <a name="audit-user-activity-with-azure-active-directory-premium"></a>Controllare l'attività utente con Azure Active Directory Premium
Se non è disponibile Azure AD Premium, è necessario attivarlo nella sezione Licenze della directory. Con la versione Premium abilitata, è possibile assegnare utenti al livello Premium.

Passando a un utente in Azure Active Directory, si potrà quindi passare alla scheda Attività per visualizzare informazioni di accesso ad Azure RemoteApp.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato i passaggi precedenti, si sarà in grado di eseguire il client di Azure RemoteApp e l'accesso con un account utente assegnato. SSMS verrà visualizzato tra le applicazioni disponibili e si potrà eseguirlo come se fosse installato nel computer locale con accesso al server SQL di Azure.

Per altre informazioni su come stabilire la connessione al database SQL, vedere [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md).

È tutto per ora. Buon lavoro.

<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png
