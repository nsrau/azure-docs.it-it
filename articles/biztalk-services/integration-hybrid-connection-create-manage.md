<properties 
	pageTitle="Creare e gestire connessioni ibride | Azure" 
	description="Informazioni su come creare una connessione ibrida, gestire la connessione e installare Hybrid Connection Manager. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="mandia"/>


# Creare e gestire connessioni ibride


## Panoramica dei passaggi
1. Creare una a connessione ibrida immettendo il nome host o l'indirizzo IP della risorsa locale nella rete privata.
2. Collegare App Web di Azure o App per dispositivi mobili di Azure alla connessione ibrida.
3. Installare Gestione connessioni ibride nella risorsa locale e connettersi a una specifica connessione ibrida. Il portale di Azure consente di eseguire l'installazione e la connessione con un solo clic.
4. Gestire le connessioni ibride e le relative chiavi di connessione.

In questo argomento vengono riportati i passaggi seguenti.


## <a name="CreateHybridConnection"></a>Creare una connessione ibrida

Una connessione ibrida può essere creata nel portale di gestione di Azure usando le app Web **oppure** i servizi BizTalk.

**Per creare connessioni ibride con le app Web**, vedere [Connettere App Web di Azure a una risorsa locale](../web-sites-hybrid-connection-get-started.md).

**Per creare connessioni ibride nei servizi BizTalk**:

1. Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel pannello di navigazione sinistro selezionare **Servizi BizTalk**, quindi scegliere il servizio BizTalk. <br/>Se non ne esiste già uno, è possibile [creare un servizio BizTalk](biztalk-provision-services.md).
3. Selezionare la scheda Connessioni ibride: <br/> ![scheda per le connessioni ibride][HybridConnectionTab]

4. Selezionare **Creare una connessione ibrida** o il pulsante **AGGIUNGI** nella barra delle applicazioni. Immettere le informazioni seguenti:

	Proprietà | Descrizione
--- | ---
Nome | Il nome della connessione ibrida deve essere univoco e non può essere uguale al nome del servizio BizTalk. È possibile inserire qualsiasi nome, ma è consigliabile sceglierne uno che descriva lo scopo specifico. Tra gli esempi sono inclusi:<br/><br/>Payroll*SQLServer*<br/>SupplyList*SharepointServer*<br/>Customers*OracleServer*
Nome host | Immettere il nome host completo, solo il nome host o l'indirizzo IPv4 della risorsa locale. Tra gli esempi sono inclusi:<br/><br/>mySQLServer<br/>*mySQLServer*.*Domain*.corp.*yourCompany*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*.*yourCompany*.com<br/>10.100.10.10
Porta | Immettere il numero di porta della risorsa locale. Ad esempio, se si usa App Web, immettere la porta 80 o 443. Se si usa SQL Server, immettere la porta 1433.

5. Per completare la configurazione, fare clic sul segno di spunta.

#### Informazioni aggiuntive

- È possibile creare più connessioni ibride. Vedere [Servizi BizTalk: tabella delle edizioni](biztalk-editions-feature-chart.md) per il numero di connessioni consentito. 
- Ogni connessione ibrida viene creata con una coppia di stringhe di connessione: chiavi dell’applicazione per SEND e chiavi locali per LISTEN. Ogni coppia ha una chiave primaria e una chiave secondaria. 


## <a name="LinkWebSite"></a>Collegare App Web di Azure o App per dispositivi mobili di Azure

Per collegare App Web di Azure a una connessione ibrida esistente, selezionare **usa una connessione ibrida esistente** nel pannello delle connessioni ibride. Vedere [Connettere le app Web di Azure a una risorsa locale](../web-sites-hybrid-connection-get-started.md).

Per collegare le app per dispositivi mobili di Azure a una connessione ibrida esistente, selezionare **aggiungi connessione ibrida** quando si modifica o si crea un servizio mobile. Vedere [Servizi mobili di Azure e Connessioni ibride](../mobile-services-dotnet-backend-hybrid-connections-get-started.md).


## <a name="InstallHCM"></a>Installare Gestione connessioni ibride nella risorsa locale

Dopo aver creato una connessione ibrida, installare Gestione connessioni ibride nella risorsa locale, disponibile per il download da App Web di Azure o dal servizio BizTalk. Passaggi dei servizi BizTalk:

1. Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel pannello di navigazione sinistro selezionare **Servizi BizTalk**, quindi scegliere il servizio BizTalk. 
3. Selezionare la scheda **Connessioni ibride**: <br/> ![scheda per le connessioni ibride][HybridConnectionTab]
4. Nella barra delle applicazioni selezionare **Installazione locale:**: <br/> ![On-Premises Setup][HCOnPremSetup]
5. Selezionare **Installa e configura** per eseguire o scaricare Gestione connessioni ibride nel sistema locale. 
6. Selezionare il segno di spunta per avviare l'installazione. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
-->

#### Informazioni aggiuntive
- Le connessioni ibride supportano le risorse locali installate nei seguenti sistemi operativi:

	- Windows Server 2008 R2
	- Windows Server 2012
	- Windows Server 2012 R2


- Dopo aver installato Gestione connessioni ibride, si verifica quanto segue:

	- La connessione ibrida ospitata in Azure viene configurata automaticamente per usare la stringa di connessione dell'applicazione primaria. 
	- La risorsa locale viene configurata automaticamente per usare la stringa di connessione locale primaria.

- Gestione connessioni ibride deve usare una stringa di connessione locale valida per le autorizzazioni. In App Web o in App per dispositivi mobili di Azure deve essere usata una stringa di connessione dell'applicazione valida per le autorizzazioni.
- È possibile ridimensionare le connessioni ibride installando un'altra istanza di Hybrid Connection Manager in un altro server. Configurare il listener locale per utilizzare lo stesso indirizzo come il primo listener locale. In questo caso, il traffico è (round robin) distribuito in modo casuale tra i listener locale attivo. 


## <a name="ManageHybridConnection"></a>Gestire le connessioni ibride
Per gestire le connessioni ibride è possibile:

- Usare il portale di Azure e andare al servizio BizTalk. 
- Usare le [API REST](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### Copiare/rigenerare le stringhe di connessione ibrida

1. Accedere al [portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel pannello di navigazione sinistro selezionare **Servizi BizTalk**, quindi scegliere il servizio BizTalk. 
3. Selezionare la scheda **Connessioni ibride**: <br/> ![scheda per le connessioni ibride][HybridConnectionTab]
4. Selezionare la connessione ibrida. Nella barra delle applicazioni selezionare **Gestisci connessione**: <br/> ![Gestione delle opzioni][HCManageConnection] <br/> **Gestisci connessione** elenca le stringhe di connessione dell'applicazione e locali. È possibile copiare le stringhe di connessione o rigenerare la chiave di accesso usata nella stringa di connessione. <br/> <br/> **Se si seleziona Rigenera**, la chiave di accesso condivisa usata nella stringa di connessione viene modificata. Eseguire le operazioni seguenti:
- Nel portale di gestione di Azure selezionare **Chiavi di sincronizzazione** nell'applicazione Azure.
- Eseguire di nuovo **Installazione locale**. Quando si esegue di nuovo l'installazione locale, la risorsa locale viene configurata automaticamente per usare la stringa di connessione primaria aggiornata.


#### Usare i Criteri di gruppo per controllare le risorse locali usate da una connessione ibrida

1. Scaricare i [modelli amministrativi di Gestione connessioni ibride](http://www.microsoft.com/download/details.aspx?id=42963).
2. Estrarre i file.
3. Nel computer che modifica i Criteri di gruppo eseguire le operazioni seguenti: 

	- Copiare i file .ADMX nella cartella *%WINROOT%\PolicyDefinitions*.
	- Copiare i file .ADML nella cartella *%WINROOT%\PolicyDefinitions\it-it*.

Dopo aver copiato i file, usare l'Editor Criteri di gruppo per cambiare i criteri.




## Avanti

[Connettere le app Web di Azure a una risorsa locale](../web-sites-hybrid-connection-get-started.md)<br/> [Connettersi a un’istanza di SQL Server locale dalle app Web di Azure](../web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/> [Servizi mobili di Azure e connessioni ibride](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)<br/> [Panoramica delle connessioni ibride](integration-hybrid-connection-overview.md)


## Vedere anche

[API REST per la gestione di servizi BizTalk in Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)<br/> [Servizi BizTalk: tabella delle edizioni](biztalk-editions-feature-chart.md)<br/> [Creare un servizio BizTalk tramite il portale di gestione di Azure](biztalk-provision-services.md)<br/> [Servizi BizTalk: schede Dashboard, Monitor e Scale](biztalk-dashboard-monitor-scale-tabs.md)<br/>


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png

<!---HONumber=July15_HO4-->