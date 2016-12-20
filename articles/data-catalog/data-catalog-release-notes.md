---
title: Note sulla versione di Azure Data Catalog | Documentazione Microsoft
description: Note sulla versione del Catalogo dati di Azure.
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 3aca9c49-45a4-4352-92e6-bd25ee3eacf7
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 09/21/2016
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 60abdc490cefc7362ca6ed0dec7dbfd53984201f


---
# <a name="azure-data-catalog-release-notes"></a>Note sulla versione del Catalogo dati di Azure
## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Note per la versione del 20 novembre 2015 del Catalogo dati di Azure
### <a name="opening-data-sources-in-power-bi-desktop"></a>Apertura di origini di dati in Power BI Desktop
Quando si utilizza l'opzione "Apri in Power BI Desktop" dal portale **Catalogo dati di Azure** , gli utenti potrebbero rilevare uno dei due problemi dell'applicazione Power BI Desktop:

* Viene visualizzata una finestra di dialogo con il titolo "Impossibile aprire il documento"
* L'applicazione Power BI Desktop si apre, ma il file sembra vuoto

Per ogni situazione, il problema può essere risolto scaricando e installando la versione più recente di Power BI Desktop da [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Note per la versione del 13 novembre 2015 del Catalogo dati di Azure
### <a name="registering-and-connecting-to-teradata"></a>Registrazione e connessione a Teradata
Per la connessione alle origini dati di Teradata, gli utenti devono installare i driver Teradata ODBC corretti che corrispondono al numero di bit (32 bit o 64 bit) del software in uso.

A questa data di rilascio di ADC, il più recente [driver Teradata ODBC per windows (versione 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) è compatibile con Office 2013, ma non con Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Note per la versione del 13 luglio 2015 del Catalogo dati di Azure
### <a name="registering-and-connecting-to-oracle-database"></a>Registrazione e connessione al database Oracle
Per la connessione alle origini dati del Database Oracle, gli utenti devono installare i driver Oracle corretti che corrispondono al numero di bit (32 bit o 64 bit) del software in uso.

* Per la registrazione delle origini dati di Oracle in un computer che esegue Windows a 32 bit, verranno usati i driver di Oracle a 32 bit
* Per la registrazione delle origini dati di Oracle in un computer che esegue Windows a 64 bit, verranno usati i driver di Oracle a 64 bit
* Per la connessione alle origini dati di Oracle tramite Excel in un computer che esegue la versione a 32 bit di Microsoft Office, incluso in Windows a 64 bit, verranno usati i driver di Oracle a 32 bit
* Per la connessione alle origini dati di Oracle tramite Excel in un computer che esegue la versione a 64 bit di Microsoft Office, verranno usati i driver di Oracle a 64 bit

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Registrazione e la connessione a SQL Server Reporting Services
Attualmente il supporto per le origini dati di SQL Server Reporting Services (SSRS) è limitato al server in modalità nativa. Il supporto per SSRS in modalità SharePoint verrà aggiunto in una versione successiva.

### <a name="opening-data-assets-in-excel"></a>Apertura degli asset di dati in Excel
Quando si aprono asset di dati in Microsoft Excel dal portale di **Azure Data Catalog**, potrebbe essere visualizzata agli utenti una finestra di dialogo con un **avviso di sicurezza di Microsoft Excel**. È un comportamento standard e previsto e gli utenti possono selezionare **Abilita** per continuare.

Per altre informazioni, vedere [Attivazione o disattivazione degli avvisi di protezione relativi ai collegamenti a siti Web sospetti e a file scaricati da tali siti](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Configurazione di proxy e criteri e registrazione dell'origine dati
È possibile che si verifichi una situazione in cui gli utenti possono accedere al portale del Catalogo dati di Azure, ma, quando tentano di accedere allo strumento di registrazione dell'origine dati, viene restituito un messaggio di errore che impedisce l'accesso.

Le possibili cause di questo comportamento problematico sono due:

**Causa 1: la configurazione di Active Directory Federation Services** Lo strumento di registrazione dell'origine dati usa l'autenticazione basata su form per convalidare gli accessi degli utenti in Active Directory. Per accedere, l'autenticazione basata su form deve essere abilitata nei criteri di autenticazione globali da un amministratore di Active Directory.

In alcune situazioni, questo errore si verifica solo quando l'utente è nella rete aziendale o si connette dall'esterno della rete aziendale. I criteri di autenticazione globali consentono di abilitare i metodi di autenticazione separatamente per le connessioni Extranet e Intranet. Se l'autenticazione basata su form non è abilitata per la rete da cui l'utente si connette, è possibile che si verifichino errori di accesso.

Per altre informazioni, vedere [Configurare i criteri di autenticazione](https://technet.microsoft.com/library/dn486781.aspx).

**Causa 2: la configurazione del proxy di rete** Se la rete aziendale usa un server proxy, lo strumento di registrazione potrebbe non essere in grado di connettersi ad Azure Active Directory tramite il proxy. Gli utenti possono abilitare lo strumento di registrazione modificando il file di configurazione dello strumento e aggiungendo questa sezione al file:

      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Per individuare il file RegistrationTool.exe.config, avviare lo strumento di registrazione e quindi aprire l'utilità Gestione attività Windows. Nella scheda Dettagli di Gestione attività, fare clic con il pulsante destro del mouse su RegistrationTool.exe e scegliere Apri percorso file dal menu a comparsa.




<!--HONumber=Nov16_HO3-->


