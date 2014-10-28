<properties linkid="manage-services-integration-hybrid-connection" urlDisplayName="Create and Manage Hybrid Connections - BizTalk Services" pageTitle="Create and Manage Hybrid Connections | Azure" metaKeywords="BizTalk Services, BizTalk, web sites, website, hybrid connections, Azure" description="Learn how to create a hybrid connection, manage the connection, and install the Hybrid Connection Manager." metaCanonical="" services="integration-services" documentationCenter="" title="Create and Manage Hybrid Connections" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="mandia"></tags>

# Creare e gestire connessioni ibride

Questo argomento elenca i passaggi per creare e gestire le connessioni ibride di Azure.

La procedura per connettersi a una risorsa locale prevede:

1.  [Creare una a connessione ibrida][Creare una a connessione ibrida] specificando il nome host o l'indirizzo IP della risorsa locale nella rete privata.

2.  [Collegare il sito Web o il servizio mobile di Azure][Collegare il sito Web o il servizio mobile di Azure] alla connessione ibrida.

3.  [Installare Gestione connessioni ibride][Installare Gestione connessioni ibride] nella risorsa locale e connettersi a una specifica connessione ibrida. Il portale di Azure consente di eseguire l'installazione e la connessione con un solo clic.

4.  [Gestire le connessioni ibride][Gestire le connessioni ibride] e le relative chiavi di connessione.

## <a name="CreateHybridConnection"></a>Creare una connessione ibrida

Una connessione ibrida può essere creata nel portale di gestione di Azure usando i siti Web **oppure** i servizi BizTalk.

**Per creare connessioni ibride con i siti Web**, vedere [Connettere un sito Web di Azure a una risorsa locale][Connettere un sito Web di Azure a una risorsa locale].

**Per creare connessioni ibride nei servizi BizTalk**:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Nel pannello di navigazione sinistro selezionare **Servizi BizTalk**, quindi scegliere il servizio BizTalk.
    Se non si ha un servizio BizTalk, è possibile [Creare un servizio BizTalk][Creare un servizio BizTalk].
3.  Selezionare la scheda Connessioni ibride:
    ![Hybrid Connections Tab][Hybrid Connections Tab]

4.  Selezionare **Creare una connessione ibrida** o il pulsante **AGGIUNGI** nella barra delle applicazioni. Immettere le informazioni seguenti:

    |---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nome**      | Il nome della connessione ibrida deve essere univoco e non può essere uguale al nome del servizio BizTalk. È possibile inserire qualsiasi nome, ma è consigliabile sceglierne uno che descriva lo scopo specifico. Tra gli esempi sono inclusi: 
                      Paghe*SQLServer*                                                                                                                                                                                                                                
                      ElencoFornitori*SharepointServer*                                                                                                                                                                                                               
                      Clienti*OracleServer*                                                                                                                                                                                                                           |
    | **Nome host** | Immettere il nome host completo, solo il nome host o l'indirizzo IPv4 della risorsa locale. Tra gli esempi sono inclusi:                                                                                                                        
                      *SQLServerUtente*                                                                                                                                                                                                                               
                      *SQLServerUtente*.*Dominio*.corp.*SocietàUtente*.com                                                                                                                                                                                            
                      *ServerSharePointServerHTTPUtente*                                                                                                                                                                                                              
                      *ServerSharePointServerHTTPUtente*.*SocietàUtente*.com                                                                                                                                                                                          
                      10.100.10.10                                                                                                                                                                                                                                    |
    | **Porta**     | Immettere il numero di porta della risorsa locale. Ad esempio, se si usa un sito Web, immettere la porta 80 o 443. Se si usa SQL Server, immettere la porta 1433.                                                                               |

5.  Fare clic sul segno di spunta.

#### Informazioni aggiuntive

-   È possibile creare più connessioni ibride. Vedere il [Servizi BizTalk: Tabella delle edizioni Developer, Basic, Standard e Premium][Servizi BizTalk: Tabella delle edizioni Developer, Basic, Standard e Premium] per il numero di connessioni consentito.
-   Ogni connessione ibrida viene creata con una coppia di stringhe di connessione: chiavi dell'applicazione per SEND e chiavi locali per LISTEN. Ogni coppia ha una chiave primaria e una chiave secondaria.

## <a name="LinkWebSite"></a>Collegare il sito Web o il servizio mobile di Azure

Per collegare il sito Web di Azure a una connessione ibrida esistente, selezionare **usa una connessione ibrida esistente** nel pannello delle connessioni ibride. Vedere [Connettere un sito Web di Azure a una risorsa locale][Connettere un sito Web di Azure a una risorsa locale].

Per collegare il servizio mobile di Azure a una connessione ibrida esistente, selezionare **aggiungi connessione ibrida** quando si modifica o si crea un servizio mobile. Vedere [Servizi mobili di Azure e Connessioni ibride][Servizi mobili di Azure e Connessioni ibride].

## <a name="InstallHCM"></a>Installare Gestione connessioni ibride nella risorsa locale

Dopo aver creato una connessione ibrida, installare Gestione connessioni ibride nella risorsa locale, disponibile per il download dal sito Web di Azure o dal servizio BizTalk. Passaggi dei servizi BizTalk:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Nel pannello di navigazione sinistro selezionare **Servizi BizTalk**, quindi scegliere il servizio BizTalk.
3.  Selezionare la scheda **Connessioni ibride**:
    ![Hybrid Connections Tab][Hybrid Connections Tab]
4.  Nella barra delle applicazioni selezionare **Installazione locale**:
    ![On-Premises Setup][On-Premises Setup]
5.  Selezionare **Installa e configura** per eseguire o scaricare Gestione connessioni ibride nel sistema locale.
6.  Selezionare il segno di spunta per avviare l'installazione.

<!-- You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:  1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps. 2. Download the Hybrid Connection Manager MSI file.  3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file.  4. Using Windows PowerShell, type:  > Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*”  -->

#### Informazioni aggiuntive

-   Le connessioni ibride supportano le risorse locali installate nei seguenti sistemi operativi:

    -   Windows Server 2008 R2
    -   Windows Server 2012
    -   Windows Server 2012 R2
-   Dopo aver installato Gestione connessioni ibride, si verifica quanto segue:

    -   La connessione ibrida ospitata in Azure viene configurata automaticamente per usare la stringa di connessione dell'applicazione primaria.
    -   La risorsa locale viene configurata automaticamente per usare la stringa di connessione locale primaria.
-   Gestione connessioni ibride deve usare una stringa di connessione locale valida per le autorizzazioni. Il sito Web o il servizio mobile di Azure deve usare una stringa di connessione dell'applicazione valida per le autorizzazioni.

## <a name="ManageHybridConnection"></a>Gestire le connessioni ibride

Per gestire le connessioni ibride è possibile:

-   Usare il portale di Azure e andare al servizio BizTalk.
-   Usare le [API REST][API REST].
    <!-- - Use Windows PowerShell cmdlets  **INSERT LINK**. -->

#### Copiare/rigenerare le stringhe di connessione ibrida

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Nel pannello di navigazione sinistro selezionare **Servizi BizTalk**, quindi scegliere il servizio BizTalk.
3.  Selezionare la scheda **Connessioni ibride**:
    ![Hybrid Connections Tab][Hybrid Connections Tab]
4.  Selezionare la connessione ibrida. Nella barra delle applicazioni selezionare **Gestisci connessione**:
    ![Manage Options][Manage Options]
    **Gestisci connessione** elenca le stringhe di connessione dell'applicazione e locali. È possibile copiare le stringhe di connessione o rigenerare la chiave di accesso usata nella stringa di connessione.
    **Se si seleziona Rigenera**, la chiave di accesso condivisa usata nella stringa di connessione viene cambiata. Eseguire le operazioni seguenti:

-   Nel portale di gestione di Azure, selezionare **Chiavi di sincronizzazione** nell'applicazione Azure.
-   Eseguire di nuovo **Installazione locale**. Quando si esegue di nuovo l'installazione locale, la risorsa locale viene configurata automaticamente per usare la stringa di connessione primaria aggiornata.

#### Usare i Criteri di gruppo per controllare le risorse locali usate da una connessione ibrida

1.  Scaricare i modelli amministrativi di Gestione connessioni ibride.
2.  Estrarre i file.
3.  Nel computer che modifica i Criteri di gruppo eseguire le operazioni seguenti:

-   Copiare i file .ADMX nella cartella *%WINROOT%\\PolicyDefinitions*.
-   Copiare i file .ADML nella cartella *%WINROOT%\\PolicyDefinitions\\it-it*.

Dopo aver copiato i file, usare l'Editor Criteri di gruppo per cambiare i criteri.

## Avanti

-   [Connettere un sito Web di Azure a una risorsa locale][Connettere un sito Web di Azure a una risorsa locale]
-   [Procedura dettagliata relativa a Connessioni ibride: Connettersi a un'istanza di SQL Server locale da un sito Web di Azure][Procedura dettagliata relativa a Connessioni ibride: Connettersi a un'istanza di SQL Server locale da un sito Web di Azure]
-   [Servizi mobili di Azure e Connessioni ibride][Servizi mobili di Azure e Connessioni ibride]
-   [Panoramica delle connessioni ibride][Panoramica delle connessioni ibride]

## Vedere anche

-   [API REST per la gestione dei servizi BizTalk in Microsoft Azure][API REST]
-   [Servizi BizTalk: Tabella delle edizioni Developer, Basic, Standard e Premium][Servizi BizTalk: Tabella delle edizioni Developer, Basic, Standard e Premium]
-   [Creare un servizio BizTalk mediante il portale di gestione di Azure][Creare un servizio BizTalk mediante il portale di gestione di Azure]
-   [Servizi BizTalk: schede Dashboard, Monitor e Scale][Servizi BizTalk: schede Dashboard, Monitor e Scale]

  [Creare una a connessione ibrida]: #CreateHybridConnection
  [Collegare il sito Web o il servizio mobile di Azure]: #LinkWebSite
  [Installare Gestione connessioni ibride]: #InstallHCM
  [Gestire le connessioni ibride]: #ManageHybridConnection
  [Connettere un sito Web di Azure a una risorsa locale]: http://go.microsoft.com/fwlink/p/?LinkId=397538
  [portale di gestione di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=213885
  [Creare un servizio BizTalk]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Hybrid Connections Tab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
  [Servizi BizTalk: Tabella delle edizioni Developer, Basic, Standard e Premium]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Servizi mobili di Azure e Connessioni ibride]: http://azure.microsoft.com/it-it/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started
  [On-Premises Setup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
  [API REST]: http://msdn.microsoft.com/library/azure/dn232347.aspx
  [Manage Options]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
  [Procedura dettagliata relativa a Connessioni ibride: Connettersi a un'istanza di SQL Server locale da un sito Web di Azure]: http://go.microsoft.com/fwlink/?LinkID=397979
  [Panoramica delle connessioni ibride]: http://azure.microsoft.com/it-it/documentation/articles/integration-hybrid-connection-overview
  [Creare un servizio BizTalk mediante il portale di gestione di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Servizi BizTalk: schede Dashboard, Monitor e Scale]: http://go.microsoft.com/fwlink/p/?LinkID=302281
