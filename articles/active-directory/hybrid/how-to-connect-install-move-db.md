---
title: Spostare il database di Azure AD Connect da SQL Server Express a SQL Server. | Microsoft Docs
description: Questo documento descrive come spostare il database di Azure AD Connect dal server locale di SQL Server Express in un server SQL remoto.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 03/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 8f9d530531d5f72e92bdb7362879199ce6a4853c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474847"
---
# <a name="move-azure-ad-connect-database-from-sql-server-express-to-sql-server"></a>Spostare il database di Azure AD Connect da SQL Server Express a SQL Server 

Questo documento descrive come spostare il database di Azure AD Connect dal server locale di SQL Server Express in un server SQL remoto.  È possibile usare le procedure seguenti per completare questa attività.

## <a name="about-this-scenario"></a>Informazioni sullo scenario
Di seguito sono riportate alcune brevi informazioni sullo scenario.  In questo scenario, Azure AD Connect versione 1.1.819.0 è installato in un singolo controller di dominio Windows Server 2016.  Usa la versione integrata SQL Server 2012 Express Edition per il database.  Il database verrà spostato in un server SQL Server 2017.

![](media/how-to-connect-install-move-db/move1.png)

## <a name="move-the-azure-ad-connect-database"></a>Spostare il database di Azure AD Connect
Seguire questa procedura per spostare il database di Azure AD Connect in un'istanza remota di SQL Server.

1.  Nel server di Azure AD Connect passare a **Servizi** e arrestare il servizio **Microsoft Azure AD Sync**.
2. Individuare la cartella **%Program Files%\Microsoft Azure AD Sync/Data/** e copiare i file **ADSync.mdf** e **ADSync_log.ldf** nel server SQL remoto.
3. Riavviare il servizio **Microsoft Azure AD Sync** nel server di Azure AD Connect.
4. Disinstallare Azure AD Connect passando a Pannello di controllo, Programmi, Programmi e funzionalità.  Selezionare Microsoft Azure AD Connect e fare clic su Disinstalla nella parte superiore.
5. Nel server SQL remoto aprire SQL Server Management Studio.
6. In Database fare clic con il pulsante destro del mouse e scegliere Connetti.
7. Nella schermata **Collega database** fare clic su **Aggiungi** e selezionare il file ADSync.mdf.  Fare clic su **OK**.
![](media/how-to-connect-install-move-db/move2.png)

8. Una volta collegato il database, tornare al server di Azure AD Connect e installare Azure AD Connect.
9. Al termine dell'installazione del file con estensione msi, viene avviata la procedura guidata per l'installazione di Azure AD Connect in modalità rapida. Chiudere la schermata facendo clic sull'icona di chiusura.
![Schermata iniziale](./media/how-to-connect-install-move-db/db1.png)
10. Avviare un nuovo prompt dei comandi o una nuova sessione di PowerShell. Passare alla cartella <drive>\Programmi\Microsoft Azure AD Connect. Eseguire il comando .\AzureADConnect.exe /useexistingdatabase per avviare la procedura guidata per l'installazione di Azure AD Connect con un database esistente.
![PowerShell](./media/how-to-connect-install-move-db/db2.png)
11. Viene visualizzata la schermata iniziale di Azure AD Connect. Accettare le condizioni di licenza e l'informativa sulla privacy e quindi fare clic su **Continua**.
![Schermata iniziale](./media/how-to-connect-install-move-db/db3.png)
12. Nella schermata **Installazione dei componenti necessari** l'opzione **Usa un'istanza di SQL Server esistente** è abilitata. Specificare il nome dell'istanza di SQL Server che ospita il database ADSync. Se l'istanza del motore SQL usata per ospitare il database ADSync non è quella predefinita in SQL Server, è necessario specificarne il nome. Inoltre, se l'esplorazione di SQL non è abilitata, è necessario specificare il numero di porta dell'istanza del motore SQL, Ad esempio:          
![Schermata iniziale](./media/how-to-connect-install-move-db/db4.png)           

13. Nella schermata **Connessione ad Azure AD** è necessario specificare le credenziali di amministratore globale della directory di Azure AD. È consigliabile usare un account nel dominio onmicrosoft.com predefinito. Questo account viene usato solo per creare un account di servizio in Azure AD e al termine della procedura guidata non viene più usato.
![Connettere](./media/how-to-connect-install-move-db/db5.png)
 
14. Nella schermata **Connessione delle directory** la foresta di Active Directory esistente, configurata per la sincronizzazione delle directory, è visualizzata con accanto una croce rossa. Per sincronizzare le modifiche da una foresta locale di Active Directory è necessario un account di Active Directory Domain Services. La procedura guidata di Azure AD Connect non è in grado di recuperare le credenziali dell'account di Active Directory Domain Services archiviate nel database ADSync perché sono crittografate e possono essere decrittografate solo dal server di Azure AD Connect precedente. Fare clic su **Cambia credenziali** per specificare l'account di Active Directory Domain Services per la foresta di Active Directory.
![Directories](./media/how-to-connect-install-move-db/db6.png)
 
 
15. Nella finestra di dialogo popup è possibile (i) specificare le credenziali di amministratore dell'organizzazione e consentire ad Azure AD Connect di creare automaticamente l'account di Active Directory Domain Services oppure (ii) creare manualmente l'account di Active Directory Domain Services e specificarne le credenziali per Azure AD Connect. Dopo aver selezionato un'opzione e specificato le credenziali necessarie, fare clic su **OK** per chiudere la finestra di dialogo popup.
![Schermata iniziale](./media/how-to-connect-install-move-db/db7.png)
 
 
16. Una volta specificate le credenziali, la croce rossa viene sostituita con un segno di spunta verde. Fare clic su **Avanti**.
![Schermata iniziale](./media/how-to-connect-install-move-db/db8.png)
 
 
17. Nella schermata **Pronto per la configurazione** fare clic su **Installa**.
![Schermata iniziale](./media/how-to-connect-install-move-db/db9.png)
 
 
18. Al termine dell'installazione, sul server di Azure AD Connect viene abilitata automaticamente la modalità di gestione temporanea. Prima di disabilitare questa modalità, è consigliabile esaminare la configurazione del server e le operazioni di esportazione in sospeso per eventuali modifiche impreviste. 

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
- [Installare Azure AD Connect usando un database ADSync esistente](how-to-connect-install-existing-database.md)
- [Installare Azure AD Connect usando le autorizzazioni di amministratore con delega SQL](how-to-connect-install-sql-delegation.md)

